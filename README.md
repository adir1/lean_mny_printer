# LEAN Money Printer - Quantitative Trading Analysis

A Python-based quantitative trading analysis project using the **LEAN engine** to backtest and analyze trading algorithms. Experiment with market algorithms, analyze performance metrics (alpha, drawdown, risk/reward ratios), and research trading strategies using Jupyter notebooks.

## Prerequisites

- **Miniforge** (minimal conda distribution with conda-forge)
- **VS Code** (recommended IDE)
- **Git** (version control)

### Install Miniforge

If you don't have Miniforge installed, download it from [miniforge.pydata.org](https://github.com/conda-forge/miniforge):

**macOS (via Homebrew):**

```bash
brew install miniforge
conda init zsh  # or bash, depending on your shell
```

Or download the installer directly from the repository.

## Quick Start

### 1. Clone & Navigate to Project

```bash
git clone <repository-url>
cd lean_mny_printer
```

### 2. Set Up Python Environment with Miniforge

**Create and activate the conda environment:**

```bash
conda env create -f environment.yml
conda activate lean-mny-printer
```

This creates a conda environment with:
- **LEAN** - Backtesting engine (from conda-forge)
- **Jupyter** - Interactive notebooks
- **Data tools** - pandas, numpy, scipy, scikit-learn
- **Visualization** - matplotlib, plotly, seaborn
- **Development tools** - black, flake8, pytest

### 3. Open in VS Code

```bash
code .
```

**VS Code will prompt you to install recommended extensions** (Jupyter, Python, Pylance, Data Wrangler, Copilot, etc.). Click "Install All" to set up your IDE.

After extensions are installed, **select the conda kernel**:
- Open the command palette (`Cmd+Shift+P` on macOS)
- Search for "Python: Select Interpreter"
- Choose the `lean-mny-printer` environment

### 4. Start Experimenting

**Open `research.ipynb`** in VS Code:
- Click on the notebook file
- The Python kernel should auto-detect your conda environment
- Run cells to analyze trading strategies

## Project Structure

```
lean_mny_printer/
├── main.py                    # QCAlgorithm implementation
├── config.json                # Algorithm configuration
├── research.ipynb             # Jupyter notebook for analysis
├── copilot-instructions.md    # AI assistant instructions
├── environment.yml            # Conda environment specification
├── .vscode/
│   └── extensions.json        # Recommended VS Code extensions
└── .venv/                     # (Not used - conda handles environments)
```

## Usage

### Running the Jupyter Notebook

1. **Activate environment** (if not already active):
   ```bash
   conda activate lean-mny-printer
   ```

2. **Start Jupyter Lab:**
   ```bash
   jupyter lab
   ```

3. **Or use VS Code's native Jupyter support:**
   - Open `research.ipynb` in VS Code
   - Select the `lean-mny-printer` conda kernel
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
conda activate lean-mny-printer

# Add new dependency
conda install -c conda-forge <package>

# Update environment
conda env update -f environment.yml

# Run Jupyter Lab
jupyter lab

# Run Jupyter Notebook in VS Code
# (Open .ipynb file directly in VS Code)

# List installed packages
conda list

# Deactivate environment
conda deactivate
```

## Troubleshooting

**"Python not found"**
- Ensure Miniforge is installed and initialized: `conda --version`
- Verify conda shell integration: `conda init zsh` (or your shell)

**"Module not found" in Jupyter**
- Verify the kernel uses your conda environment: Click kernel selector in VS Code, choose `lean-mny-printer`
- Reinstall dependencies: `conda env update -f environment.yml`

**LEAN import errors**
- Ensure LEAN is installed: `conda list | grep lean`
- Update environment: `conda env update -f environment.yml --prune`

**Jupyter not opening**
- Ensure environment is activated: `conda activate lean-mny-printer`
- Verify Jupyter is installed: `conda list | grep jupyter`
- Check kernel: `jupyter kernelspec list`

## Next Steps

1. ✅ Install Miniforge
2. ✅ Create conda environment from `environment.yml`
3. ✅ Open project in VS Code
4. ✅ Select conda kernel in VS Code
5. ✅ Open `research.ipynb` and run analysis
6. 🔄 Modify algorithms in `main.py` or notebook
7. 📊 Analyze performance metrics
7. (Optional) Install LEAN CLI for advanced features

---

**Happy backtesting! 📈**
