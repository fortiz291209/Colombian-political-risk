# Colombian Political Risk and Commodity/FX Market Dynamics

**Author:** Fernando Ortiz Sierra  
**Period:** June 2021 – July 2024  
**Affiliation:** Private Family Office, Bogota, Colombia

## Overview

This repository contains the full research and trading signal infrastructure developed over three years (2021–2024) to study the relationship between Colombian political events and asset price movements in crude oil (Brent, WTI), gold, and correlated FX pairs (USD/COP, USD/BRL, USD/MXN). 

The core thesis: political discourse in Colombia, particularly around the 2022 presidential election (Gustavo Petro vs. Rodolfo Hernandez) and subsequent policy announcements, generated systematic mispricing in oil and FX markets that could be captured through contrarian positioning based on NLP-derived sentiment signals.

## Key Results

- **2022 Presidential Election:** Sentiment pipeline correctly identified negative market overreaction to Petro's election victory in June 2022. Anti-consensus positioning on USD/COP and Brent crude generated positive returns during the post-election period as markets repriced from panic to fundamentals.
- **Policy Announcement Signals:** NLP pipeline tracked Petro administration energy policy announcements (drilling moratorium rhetoric, Ecopetrol restructuring, mining tax proposals) and generated contrarian signals when sentiment divergence exceeded historical thresholds.
- **Event Study Results:** Difference-in-differences estimation isolated the abnormal return component attributable to political events from broader EM commodity and FX movements.

## Repository Structure

```
colombia-political-risk/
├── src/
│   ├── __init__.py
│   ├── data_ingestion.py       # Data collection from FRED, Yahoo Finance, government APIs
│   ├── nlp_pipeline.py         # LLM-based sentiment extraction from political communications
│   ├── sentiment_scorer.py     # Sentiment scoring and aggregation at daily/weekly frequencies
│   ├── signal_generator.py     # Trading signal construction from sentiment scores
│   ├── event_study.py          # Event study and DiD estimation framework
│   ├── backtester.py           # Event-driven backtesting engine with transaction costs
│   ├── portfolio.py            # Position sizing, volatility targeting, drawdown controls
│   ├── risk_metrics.py         # Sharpe, max drawdown, VaR, CVaR, rolling statistics
│   └── visualization.py        # Publication-quality charts and regime plots
├── configs/
│   ├── market_config.yaml      # Ticker symbols, data sources, frequencies
│   └── strategy_config.yaml    # Signal thresholds, position limits, cost assumptions
├── data/
│   └── (raw data downloaded at runtime via data_ingestion.py)
├── notebooks/
│   ├── 01_data_exploration.md       # Initial EDA on oil/FX/political data
│   ├── 02_sentiment_calibration.md  # LLM prompt engineering and scoring validation
│   ├── 03_event_study_2022.md       # Petro election event study
│   ├── 04_strategy_backtest.md      # Full strategy backtest and parameter sensitivity
│   └── 05_regime_analysis.md        # Market regime classification and conditional performance
├── docs/
│   ├── METHODOLOGY.md          # Full research methodology documentation
│   ├── POLITICAL_TIMELINE.md   # Timeline of key Colombian political events (2021-2024)
│   └── SIGNAL_DEFINITIONS.md   # Formal definitions of all trading signals
├── tests/
│   ├── __init__.py
│   ├── test_sentiment.py       # Unit tests for sentiment scoring
│   ├── test_signals.py         # Unit tests for signal generation
│   └── test_backtester.py      # Backtester validation against known results
├── results/
│   └── (generated at runtime)
├── requirements.txt
└── README.md
```

## Methodology

### 1. Data Collection (`data_ingestion.py`)

- **Market data:** Daily OHLCV for Brent crude, WTI, gold, USD/COP, USD/BRL, USD/MXN, Colombian CDS spreads, and COLCAP index via Yahoo Finance and FRED.
- **Political data:** Presidential communications, ministerial press releases, congressional voting records, and social media activity from official government accounts. Collected via web scraping and API access to Colombian government portals.
- **Macro controls:** Colombian CPI, policy rate (Banco de la Republica), US 10Y yield, DXY, VIX, EM sovereign spread indices.

### 2. NLP Sentiment Pipeline (`nlp_pipeline.py`, `sentiment_scorer.py`)

The sentiment pipeline processes raw Spanish-language political communications through large language models to generate daily sentiment scores across five dimensions:

1. **Energy policy sentiment:** Directional stance on oil exploration, Ecopetrol, mining regulation
2. **Fiscal sentiment:** Tax reform rhetoric, spending announcements, deficit language
3. **Market confidence:** Investor-facing language, FDI commentary, trade policy signals
4. **Political stability:** Coalition dynamics, protest activity, institutional friction
5. **EM contagion:** References to regional political developments (Brazil, Mexico, Argentina)

Each dimension is scored on a [-1, +1] scale. The aggregate political risk score is a weighted average calibrated to maximize correlation with next-week USD/COP realized volatility.

### 3. Signal Construction (`signal_generator.py`)

Trading signals are generated when the NLP-derived sentiment score diverges from market-implied sentiment (proxied by USD/COP option-implied volatility and Colombian CDS spreads):

- **Contrarian long signal:** Sentiment score < -0.5 AND CDS spread > 2σ above 60-day mean AND oil implied vol below historical median. Interpretation: market is pricing too much fear relative to the actual political risk.
- **Contrarian short signal:** Sentiment score > 0.3 AND CDS spread compressing AND oil implied vol rising. Interpretation: market is underpricing emerging political risk.
- **Regime filter:** Signals are suppressed during global risk-off events (VIX > 30) to avoid trading against systemic moves.

### 4. Event Study Framework (`event_study.py`)

Formal econometric analysis of political events using:

- **Event study methodology:** Abnormal returns computed against a market model estimated on [-120, -20] pre-event window. CAR (cumulative abnormal returns) computed over [0, +5], [0, +10], and [0, +20] post-event windows.
- **Difference-in-differences:** Colombian assets (treatment) vs. comparable EM assets (control) around political events, with parallel trends validation on pre-event windows.
- **Key events analyzed:**
  - Petro's first-round victory (May 2022)
  - Petro's runoff victory vs. Hernandez (June 2022)
  - Energy ministry drilling moratorium announcement (August 2022)
  - Tax reform passage (November 2022)
  - Ecopetrol dividend policy changes (2023)
  - Mining regulation proposals (2023-2024)

### 5. Backtesting Engine (`backtester.py`)

Event-driven backtester with:

- Realistic transaction costs (bid-ask spreads calibrated to Colombian FX market microstructure)
- Slippage model based on historical USD/COP liquidity patterns
- Position sizing via inverse-volatility weighting with maximum position limits
- Drawdown controls: positions reduced by 50% when portfolio drawdown exceeds 5%
- Multiple parameter configurations tested for robustness (lookback windows, signal thresholds, holding periods)

## Key Political Events Timeline

| Date | Event | Market Impact |
|------|-------|--------------|
| May 2022 | Petro wins first round | USD/COP +3.2%, Brent -1.8% |
| Jun 2022 | Petro wins presidency | USD/COP +5.1%, COLCAP -4.3% |
| Aug 2022 | Drilling moratorium rhetoric | Ecopetrol -8.2% |
| Nov 2022 | Tax reform passed | USD/COP -2.1% (relief rally) |
| Mar 2023 | Ecopetrol dividend cut rumors | Ecopetrol -6.4% |
| Jun 2023 | Mining regulation proposal | Colombian CDS +45bps |
| Dec 2023 | Political crisis / cabinet reshuffle | USD/COP +2.8% |
| Mar 2024 | Health reform collapse | COLCAP +3.1% (relief) |

## Technical Requirements

```
python >= 3.9
numpy >= 1.21
pandas >= 1.4
scipy >= 1.8
statsmodels >= 0.13
scikit-learn >= 1.0
matplotlib >= 3.5
seaborn >= 0.12
openai >= 0.27  # for LLM sentiment extraction
yfinance >= 0.2
beautifulsoup4 >= 4.11
requests >= 2.28
pyyaml >= 6.0
```

## Usage

```bash
# Install dependencies
pip install -r requirements.txt

# Run full pipeline
python -m src.data_ingestion --start 2021-01-01 --end 2024-07-01
python -m src.nlp_pipeline --input data/political_texts/ --output data/sentiment_scores.csv
python -m src.signal_generator --config configs/strategy_config.yaml
python -m src.backtester --config configs/strategy_config.yaml --output results/

# Run event studies
python -m src.event_study --events configs/events.yaml --output results/event_studies/
```

## Disclaimer

This repository documents research conducted for a private family office. All return figures and specific trade details have been excluded. The code and methodology are shared for academic and professional portfolio purposes only.

## Contact

Fernando Ortiz Sierra  
fao2@fordham.edu | +1 347 323 8049
