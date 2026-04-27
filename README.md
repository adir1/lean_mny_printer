# LEAN Money Printer - Quantitative Trading Analysis

A Python-based quantitative trading analysis project using the **LEAN engine** to backtest and analyze trading algorithms. Experiment with market algorithms, analyze performance metrics (alpha, drawdown, risk/reward ratios), and research trading strategies using Jupyter notebooks.

## Prerequisites

- **Python 3.11+** (installed on your system)
- **uv** (Python package manager and environment tool)
- **VS Code** (recommended IDE)
- **Git** (version control)

### Install uv

If you don't have `uv` installed, install it from [astral.sh/uv](https://astral.sh/uv):

**macOS (via Homebrew):**
```bash
brew install uv
```

**Or install via pip:**
```bash
pip install uv
```

**Or download directly:**
Visit https://astral.sh/uv for other installation methods.

## Quick Start

### 1. Clone & Navigate to Project

```bash
git clone <repository-url>
cd lean_mny_printer
```

### 2. Set Up Python Environment with uv

**Create and activate a virtual environment:**

```bash
uv venv --python 3.11
source .venv/bin/activate  # On macOS/Linux
# or
.venv\Scripts\activate     # On Windows
```

### 3. Install Dependencies

```bash
uv pip install -r requirements.txt
```

This installs:
- **QuantConnect LEAN** - Backtesting engine
- **Jupyter** - Interactive notebooks
- **Data tools** - pandas, numpy, scipy, scikit-learn
- **Visualization** - matplotlib, plotly, seaborn

### 4. Open in VS Code

```bash
code .
```

**VS Code will prompt you to install recommended extensions** (Jupyter, Python, Pylance, Data Wrangler, Copilot, etc.). Click "Install All" to set up your IDE.

### 5. Start Experimenting

**Open `research.ipynb`** in VS Code:
- Click on the notebook file
- Select the Python kernel (should auto-detect your `.venv`)
- Run cells to analyze trading strategies

## Project Structure

```
lean_mny_printer/
├── main.py                    # QCAlgorithm implementation
├── config.json                # Algorithm configuration
├── research.ipynb             # Jupyter notebook for analysis
├── copilot-instructions.md    # AI assistant instructions
├── requirements.txt           # Python dependencies
├── .vscode/
│   └── extensions.json        # Recommended VS Code extensions
└── .venv/                     # Virtual environment (created by uv)
```

## Usage

### Running the Jupyter Notebook

1. **Activate environment** (if not already active):
   ```bash
   source .venv/bin/activate
   ```

2. **Start Jupyter Lab:**
   ```bash
   jupyter lab
   ```

3. **Or use VS Code's native Jupyter support:**
   - Open `research.ipynb` in VS Code
   - Select your Python kernel
   - Run cells

### Running the Algorithm

The `main.py` contains a simple `Leanmnyprinter` algorithm that backtests on SPY data.

**Using LEAN CLI (see optional section below):**
```bash
lean backtester
```

### Notebook Analysis

`research.ipynb` includes:
- **QuantBook** - Interactive analysis tool
- **Indicator Analysis** - Bollinger Bands example
- **Historical Data** - Access to market data
- **Visualization** - Charts and performance metrics

## LEAN CLI (Optional but Recommended)

Many advanced scenarios require the **LEAN CLI**:
- Downloading additional market data
- Running live paper trading
- Optimizing algorithms
- Managing multiple strategies
- Local backtesting with full customization

### Install LEAN CLI

```bash
pip install lean-cli
```

Or download from: https://www.quantconnect.com/docs/v2/lean-cli

### Verify Installation

```bash
lean --version
```

### Download Sample Data (if needed)

```bash
lean data download --dataset-file <config-file>
```

### Run Backtest Locally

```bash
lean backtester --algorithm main.py --data-dir ./data
```

## Key LEAN Concepts

### Algorithm Structure

```python
class MyAlgorithm(QCAlgorithm):
    def initialize(self):
        """Setup: Set dates, cash, subscriptions"""
        self.set_start_date(2020, 1, 1)
        self.set_end_date(2023, 12, 31)
        self.set_cash(100000)
        self.add_equity("SPY", Resolution.DAILY)
    
    def on_data(self, data: Slice):
        """Main event: Called on each data point"""
        if not self.portfolio.invested:
            self.set_holdings("SPY", 1)
```

### Performance Metrics

- **Alpha** - Excess returns vs. benchmark
- **Sharpe Ratio** - Risk-adjusted returns (target > 1.0)
- **Maximum Drawdown** - Peak-to-trough decline
- **Volatility** - Standard deviation of returns
- **Profit Factor** - Gross profit / Gross loss (target > 1.5)
- **CAGR** - Compound annual growth rate

## Resources

- **LEAN Documentation**: https://www.quantconnect.com/docs/v2/writing-algorithms/
- **QuantConnect Getting Started**: https://www.quantconnect.com/docs/v2/writing-algorithms/key-concepts/getting-started
- **LEAN CLI Docs**: https://www.quantconnect.com/docs/v2/lean-cli
- **LEAN GitHub**: https://github.com/QuantConnect/Lean
- **uv Documentation**: https://astral.sh/uv

## Common Commands

```bash
# Activate environment
source .venv/bin/activate

# Add new dependency
uv pip install <package>

# Update dependencies
uv pip install -r requirements.txt --upgrade

# Run Jupyter Lab
jupyter lab

# Run Jupyter Notebook in VS Code
# (Open .ipynb file directly in VS Code)

# List installed packages
uv pip list

# Deactivate environment
deactivate
```

## Troubleshooting

**"Python not found"**
- Ensure Python 3.11+ is installed: `python3 --version`
- Use `uv venv --python 3.11` to specify the version

**"Module not found" in Jupyter**
- Verify the kernel uses your `.venv`: Click kernel selector in VS Code
- Reinstall dependencies: `uv pip install -r requirements.txt`

**QuantConnect/LEAN import errors**
- Install LEAN CLI: `pip install lean-cli` (may be required for full functionality)
- Reinstall quantconnect: `uv pip install --force-reinstall quantconnect`

**Jupyter not opening**
- Ensure Jupyter is installed: `uv pip install jupyter`
- Check kernel: `jupyter kernelspec list`

## Next Steps

1. ✅ Install `uv` and set up environment
2. ✅ Install dependencies from `requirements.txt`
3. ✅ Open project in VS Code
4. ✅ Open `research.ipynb` and run analysis
5. 🔄 Modify algorithms in `main.py` or notebook
6. 📊 Analyze performance metrics
7. (Optional) Install LEAN CLI for advanced features

---

**Happy backtesting! 📈**
