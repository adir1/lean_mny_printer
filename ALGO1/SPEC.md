# ETF Flow surfer

**Plain‑English Summary of the Revised “Flow‑Weighted, Small‑Cap, Options‑Trading” Algorithm**

1. **Start with the universe of ETFs**  
   * Keep only those U.S. equity‑ETFs that are at least 80 % invested in U.S. stocks.  
   * For every such ETF we know its current Net Asset Value (NAV) and the NAV from one month ago.

2. **Measure how each ETF is really moving**  
   * Compute the **dollar change** in its assets:  

     \[
     \text{Dollar‑Change}_e = \frac{(\text{NAV}_{\text{today}}-\text{NAV}_{\text{prev month}})}{\text{NAV}_{\text{prev month}}}\times \text{NAV}_{\text{prev month}}
     \]  

     – Positive = net inflow, Negative = net outflow.  
   * This number tells us exactly how many **dollars** the ETF gained or lost.

3. **Attach dollar‑flows to the stocks inside the ETFs**  
   * Each ETF publishes the **weight** of every stock it holds.  
   * For a given stock *t*, the portion of the ETF’s dollar‑change that can be credited to *t* is  
     \[
     \text{Flow}_{e,t}= \text{Weight}_{e,t}\times \text{Dollar‑Change}_e .
     \]  
   * Sum this weighted flow over **all ETFs** that hold *t*.  
     * The total **positive flow** for *t* = sum of all positive weighted flows.  
     * The total **negative flow** for *t* = sum of the absolute values of all negative weighted flows.

4. **Keep only “small” stocks**  
   * Compute the median market‑cap of every stock that appears in any of the ETFs.  
   * Retain a stock only if its market‑cap is **≤ X × median** (e.g., X = 0.5, meaning “no larger than half the median cap”).

5. **Rank the remaining stocks**  
   * **Long‑candidate score** = (total positive dollar flow) ÷ (1 + λ · (Market‑Cap / Median‑Cap)).  
   * **Short‑candidate score** = (total negative dollar flow) ÷ (1 + λ · (Market‑Cap / Median‑Cap)).  
   * λ is a scaling factor (usually 1).  
   * Sort the long list **ascending** (smaller numbers = stronger inflow relative to size) and the short list **descending** (larger numbers = stronger outflow).

6. **Pick the top‑N tickers** from each sorted list (e.g., the best 5‑10 long and the best 5‑10 short).  
   * These are the stocks you will trade the next month.

7. **Turn the tickers into an options portfolio**  
   * Use **very short‑dated, at‑the‑money (ATM) options** – preferably weekly expirations or 3‑day options – so that time‑decay (theta) is minimal.  
   * For every **long ticker** buy an ATM **call**; for every **short ticker** buy an ATM **put**.  
   * Allocate the same **dollar amount** to each option (equal‑weight).  
   * The portfolio is therefore a set of cheap, near‑expiry options that capture the directional bias implied by the flow‑driven ticker selection.

8. **Execute at the start of the next trading period** (e.g., on the first day after the selection date) and rebalance the whole process every week.

---

### Why the Final Step Uses Weekly or 3‑Day Options

* **Weekly / 3‑day expirations have the shortest theta** of any listed equity option.  
* By selecting the nearest‑expiry ATM contracts we **minimize the erosion of premium** caused by time decay, letting the option’s price move almost entirely with the underlying stock’s price.  
* The short expiry also keeps the capital commitment low and makes it easy to roll the whole portfolio each week (or every few days) as new flow signals arrive.

---

### TL;DR Flow‑to‑Options Pipeline

1. **Find ETFs that are inflowing or outflowing** → compute their dollar‑change.  
2. **Weight each dollar‑change by the ETF’s holdings** → get a dollar inflow/outflow attribution for every stock.  
3. **Filter to small‑cap stocks**, rank them by the weighted dollar flow divided by a market‑cap scaling factor.  
4. **Take the top‑N long and short tickers**, then **buy ATM weekly (or 3‑day) calls for the longs and puts for the shorts**, allocating equal dollars to each.  
5. **Trade at the start of the next period** and repeat the whole process on the next data set.

That’s the complete algorithm, now expressed in everyday language and aligned with the goal of using ultra‑short‑dated options to limit time‑decay while staying fully data‑driven.

## Python Pseudocode

**Compact Python‑style pseudocode**  
(Variable names follow the TLDR description: `phi`, `F_dollar`, `w`, `M`, `flow_pos`, `flow_neg`, etc.)

```python
# -------------------------------------------------------------
# 0.  Imports / helpers
# -------------------------------------------------------------
import numpy as np, pandas as pd

def sgn(x):                     # signum
    return np.where(x>0,1,np.where(x<0,-1,0))

# -------------------------------------------------------------
# 1.  INPUTs (already prepared)
# -------------------------------------------------------------
# holdings : DataFrame ['etf','ticker','weight']   (weights sum to 1 per ETF)
# nav      : DataFrame ['etf','nav_prev','nav_cur'] (prev‑month NAV & today NAV)
# market_cap : dict {ticker: market_cap_usd}
# -------------------------------------------------------------

# -------------------------------------------------------------
# 2.  Dollar AUM change for each ETF
# -------------------------------------------------------------
nav['phi']      = (nav['nav_cur'] - nav['nav_prev']) / nav['nav_prev']   # signed % growth
nav['F_dollar'] = nav['phi'] * nav['nav_prev']                           # signed $ inflow/outflow
# -------------------------------------------------------------

# 3.  Map ETF → ticker → weight
# -------------------------------------------------------------
etf_to_w = {e: row['weight'] for _,row in holdings.iterrows()
            for e,row in holdings.iterrows() if row['etf']==e}
# -------------------------------------------------------------

# 4.  Weighted dollar flow per ticker
# -------------------------------------------------------------
t_flow = {}                               # ticker → {'pos':0.0,'neg':0.0}
for e, wdict in etf_to_w.items():
    F_e = nav.loc[nav['etf']==e, 'F_dollar'].values[0]   # scalar $ flow for ETF e
    sign_e = sgn(F_e)                                   # +1 inflow, -1 outflow
    for t, w in wdict.items():
        if w==0: continue
        contrib = w * F_e
        t_flow.setdefault(t, {'pos':0.0,'neg':0.0})
        if sign_e>0:  t_flow[t]['pos'] += contrib
        else:         t_flow[t]['neg'] += contrib
# -------------------------------------------------------------

# 5.  Small‑cap filter
# -------------------------------------------------------------
med_cap = np.median(list(market_cap.values()))
cap_lim = 0.5 * med_cap                     # keep M ≤ 0.5·median
small_tickers = [t for t,M in market_cap.items() if M <= cap_lim]
# -------------------------------------------------------------

# 6.  Rank scores (dollar flow ÷ cap‑scale)
# -------------------------------------------------------------
rank_pos = {}
rank_neg = {}
lam = 1.0
for t in small_tickers:
    if t not in t_flow: continue
    pos_d  = t_flow[t]['pos']
    neg_d  = t_flow[t]['neg']
    M_t    = market_cap[t]
    scale  = 1 + lam * (M_t / med_cap)

    rank_pos[t] = pos_d / scale          # larger inflow → smaller rank value
    rank_neg[t] = neg_d / scale          # larger outflow → larger rank value
# -------------------------------------------------------------

# 7.  Pick top‑N candidates
# -------------------------------------------------------------
top_n_long  = 5
top_n_short = 5

long_candidates = sorted(rank_pos.items(),
                         key=lambda x: x[1])[:top_n_long]      # asc.
short_candidates= sorted(rank_neg.items(),
                         key=lambda x: x[1], reverse=True)[:top_n_short]

selected_long  = [t for t,_ in long_candidates]
selected_short = [t for t,_ in short_candidates]
# -------------------------------------------------------------

# 8.  Build equal‑weight ATM short‑term option orders
# -------------------------------------------------------------
# Helper – fetch ATM option price for a ticker & expiry (e.g., 3‑day or weekly)
def atm_price(ticker, days_to_exp):
    # placeholder – hook to market data source
    raise NotImplementedError

# Parameters
capital      = 100_000                     # total allocation
exp_days     = 3                           # 3‑day (or 5‑day weekly) expiry
weight_per   = capital / (len(selected_long)+len(selected_short))

orders = []                                 # each entry = dict
for t in selected_long:
    price = atm_price(t, exp_days)
    orders.append({'ticker':t, 'type':'call',
                   'direction':'buy', 'size_usd':weight_per,
                   'price_usd':price})
for t in selected_short:
    price = atm_price(t, exp_days)
    orders.append({'ticker':t, 'type':'put',
                   'direction':'buy', 'size_usd':weight_per,
                   'price_usd':price})
# -------------------------------------------------------------

# 9.  (Execute) – send each order to broker / execution system
# -------------------------------------------------------------
# for o in orders:
#     place_trade(o['ticker'],
#                 option_type=o['type'],
#                 side=o['direction'],
#                 notional=o['size_usd'],
#                 limit_price=o['price_usd'])
# -------------------------------------------------------------
```

**What the script does (in TLDR language)**  

1. Compute each ETF’s **dollar‑AUM change** (`F_dollar`).  
2. Multiply that flow by the **ETF‑ticker weight** → weighted flow per ticker.  
3. Sum weighted flows over all ETFs → **ticker‑level inflow** (`pos`) and **outflow** (`neg`).  
4. Throw away any ticker whose market‑cap exceeds `0.5 × median_cap`.  
5. Rank the remaining tickers with  
   `score = flow / (1 + λ·(M / median_cap))`.  
6. Take the **top‑N** of the ascending (long) and descending (short) lists.  
7. For each selected ticker, **buy an ATM weekly/3‑day option** (call for longs, put for shorts) with equal‑dollar weight.  

All variable names map directly to the algorithm description, keeping the code as short and readable as possible.