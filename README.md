# Ulisses-too Trading Bot MVP

A lightweight, single-exchange cryptocurrency trading bot designed for algorithmic trading with non-ML strategies. Built with modularity and cloud deployment in mind, featuring integrated backtesting capabilities.

## 🚀 Quick Start

```bash
# Clone the repository
git clone https://github.com/stgmf/ulisses-too.git
cd ulisses-too

# Setup Python environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Configure the bot
cp config/config.example.yaml config/config.yaml
# Edit config/config.yaml with your settings

# Run backtest
python -m app.main --mode backtest

# Run live trading
python -m app.main --mode trade
```

## 📋 Features

### Current MVP Features
- **Single Exchange Trading**: Binance support via CCXT
- **Simple Strategy**: SMA (Simple Moving Average) crossover strategy
- **Backtesting Engine**: Test strategies on historical data with fee consideration
- **Risk Management**: Position limits and stop-loss protection
- **Paper Trading**: Testnet support for safe strategy testing
- **Performance Analytics**: Comprehensive metrics and visualizations
- **Docker Support**: Easy deployment with Docker and docker-compose
- **Cloud Ready**: Prepared for Google Cloud Run deployment

### What This Bot Does
1. Connects to a single exchange (Binance)
2. Monitors a single trading pair (e.g., BTC/USDT)
3. Backtests strategies on historical data before live deployment
4. Executes trades based on SMA crossover signals
5. Manages position size based on risk parameters
6. Implements stop-loss for downside protection
7. Tracks performance with detailed metrics and visualizations

### What This Bot Doesn't Do (Yet)
- No machine learning models
- No multi-exchange arbitrage
- No complex portfolio management
- No web interface
- No real-time optimization
- No social sentiment analysis

## 🏗️ Architecture

```
crypto-trading-mvp/
├── app/
│   ├── core/                   # Core infrastructure
│   │   ├── param_manager.py    # Configuration management
│   │   ├── data_context.py     # Data container with metadata & lineage
│   │   └── exceptions.py       # Custom exceptions
│   ├── trading/                # Trading logic
│   │   ├── exchange_client.py  # Exchange connectivity
│   │   ├── data_collector.py   # OHLCV data collection
│   │   ├── risk_manager.py     # Risk management
│   │   ├── engine.py           # Main trading engine
│   │   ├── backtester.py       # Backtesting engine
│   │   ├── results_logger.py   # Results saving and visualization
│   │   └── strategies/         # Strategy implementations
│   │       ├── base.py         # Base strategy class
│   │       └── sma_cross.py    # SMA crossover strategy
│   └── main.py                 # Application entry point
├── config/
│   ├── config.yaml             # Main configuration
│   └── config.example.yaml     # Example configuration
├── backtests/                  # Backtest results (auto-created)
├── tests/                      # Unit tests
├── docker-compose.yml          # Docker composition
├── Dockerfile                  # Container definition
├── requirements.txt            # Python dependencies
└── README.md                   # This file
```

## 🔧 Configuration

### Basic Configuration (config/config.yaml)
```yaml
exchange:
  name: binance
  api_key: ${BINANCE_API_KEY}     # From environment variable
  api_secret: ${BINANCE_API_SECRET}
  testnet: true                    # Use testnet for paper trading
  fee_rate: 0.001                  # 0.1% taker fee

trading:
  symbol: BTC/USDT
  timeframe: 15m
  cycle_interval: 60               # Check every 60 seconds

strategy:
  type: sma_cross
  sma_fast: 10                     # 10-period fast SMA
  sma_slow: 30                     # 30-period slow SMA

risk:
  max_position_value: 1000         # Max $1000 per position
  max_position_pct: 0.1            # Max 10% of balance
  stop_loss: 0.02                  # 2% stop loss

backtest:
  initial_balance: 10000           # Starting balance for backtest
  start_date: "2024-01-01"         # Backtest start date
  end_date: "2024-12-31"           # Backtest end date
```

### Environment Variables
```bash
# .env file (don't commit this!)
BINANCE_API_KEY=your_api_key_here
BINANCE_API_SECRET=your_api_secret_here
```

## 📊 Backtesting

The integrated backtesting engine allows you to test strategies before risking real funds.

### Running a Backtest
```bash
# Basic backtest with default config
python -m app.main --mode backtest

# Backtest with custom config
python -m app.main --mode backtest --config config/backtest_config.yaml
```

### Backtest Output
The backtester generates:
- **Performance Metrics**: Total return, Sharpe ratio, max drawdown, win rate
- **Trade Log**: Detailed record of all trades with fees
- **Visualizations**: Equity curve, drawdown chart, signal markers
- **Metadata**: Complete processing history for reproducibility

Results are saved in the `backtests/` directory with timestamps.

### Example Backtest Results
```
Backtest Results:
Total Return: 24.56%
Max Drawdown: -8.32%
Sharpe Ratio: 1.45
Win Rate: 58.33%
Total Trades: 48
Fee Impact: -0.96%

Results saved to: backtests/sma_cross_20240115_143022/
```

### Analyzing Results
Each backtest creates:
- `results.csv`: Complete price and signal data
- `trades.csv`: Individual trade records
- `metadata.json`: Metrics and processing history
- `backtest_plot.png`: Visual performance summary

## 🐳 Docker Deployment

### Local Deployment
```bash
# Build and run with docker-compose
docker-compose up -d

# View logs
docker-compose logs -f trading-bot

# Stop the bot
docker-compose down
```

### Google Cloud Run Deployment
```bash
# Setup GCP project
gcloud projects create your-project-id
gcloud config set project your-project-id

# Enable required services
gcloud services enable run.googleapis.com
gcloud services enable secretmanager.googleapis.com
gcloud services enable cloudbuild.googleapis.com

# Store secrets
echo -n "your_api_key" | gcloud secrets create binance-api-key --data-file=-
echo -n "your_api_secret" | gcloud secrets create binance-api-secret --data-file=-

# Deploy to Cloud Run
gcloud run deploy trading-bot \
  --source . \
  --region us-central1 \
  --set-secrets BINANCE_API_KEY=binance-api-key:latest,BINANCE_API_SECRET=binance-api-secret:latest
```

## 🧪 Testing

```bash
# Run all tests
pytest

# Run with coverage
pytest --cov=app tests/

# Run specific test file
pytest tests/test_strategy.py

# Run backtesting tests
pytest tests/test_backtester.py
```

## 📈 Strategy Details

### SMA Crossover Strategy
The bot implements a simple but effective SMA crossover strategy:

1. **Fast SMA** (default: 10 periods): Responds quickly to price changes
2. **Slow SMA** (default: 30 periods): Provides trend direction
3. **Buy Signal**: When fast SMA crosses above slow SMA
4. **Sell Signal**: When fast SMA crosses below slow SMA

### Risk Management
- **Position Sizing**: Never risk more than 10% of account balance
- **Stop Loss**: Automatic 2% stop loss on all positions
- **Maximum Position Value**: Capped at $1000 per trade
- **Fee Consideration**: All calculations include exchange fees

## 🎯 Development Workflow

### 1. Strategy Development
```python
# Create new strategy in app/trading/strategies/
class MyStrategy(BaseStrategy):
    def calculate_signal(self, data_context):
        # Your strategy logic here
        return signal  # -1, 0, or 1
    
    def get_lookback_period(self):
        return 50  # Bars needed for calculation
```

### 2. Backtest Strategy
```bash
# Test on historical data
python -m app.main --mode backtest --config config/my_strategy.yaml

# Analyze results in backtests/ directory
```

### 3. Paper Trade
```bash
# Test with paper money (testnet)
python -m app.main --mode trade --config config/my_strategy_paper.yaml
```

### 4. Go Live
```bash
# Trade with real funds (use with caution!)
# Set testnet: false in config
python -m app.main --mode trade --config config/my_strategy_live.yaml
```

## 🚧 Development Roadmap

### Phase 1: MVP (Current) ✅
- [x] Basic exchange connectivity
- [x] Simple SMA strategy
- [x] Risk management framework
- [x] Backtesting engine with fee tracking
- [x] Performance visualization
- [x] Docker deployment
- [x] Cloud Run ready

### Phase 2: Enhanced Strategies
- [ ] RSI (Relative Strength Index) strategy
- [ ] Bollinger Bands strategy
- [ ] MACD strategy
- [ ] Strategy combination framework
- [ ] Walk-forward optimization

### Phase 3: Data & Analytics
- [ ] Local database for trade history (PostgreSQL)
- [ ] Extended performance metrics (Sortino, Calmar ratios)
- [ ] Monte Carlo simulation for strategy robustness
- [ ] CSV export for tax reporting
- [ ] Real-time performance dashboard

### Phase 4: Web Interface
- [ ] REST API for bot control
- [ ] Simple web dashboard (Flask/FastAPI)
- [ ] Real-time position monitoring
- [ ] Strategy performance comparison
- [ ] Backtest result browser

### Phase 5: Advanced Risk Management
- [ ] Dynamic position sizing based on volatility
- [ ] Correlation-based risk limits
- [ ] Maximum drawdown controls
- [ ] Time-based stop losses
- [ ] Trailing stop implementation

### Phase 6: Multi-Asset Support
- [ ] Multiple trading pairs
- [ ] Portfolio rebalancing
- [ ] Asset correlation tracking
- [ ] Sector exposure limits
- [ ] Cross-asset hedging

### Phase 7: Machine Learning Integration
- [ ] Feature engineering pipeline
- [ ] ML model integration framework
- [ ] Probability-based position sizing
- [ ] Model performance tracking
- [ ] A/B testing framework

### Phase 8: Production Features
- [ ] Multi-exchange support
- [ ] Order book integration
- [ ] Advanced order types (iceberg, TWAP)
- [ ] Emergency shutdown mechanisms
- [ ] Alerting and monitoring (Telegram, Discord)

### Phase 9: Scaling & Performance
- [ ] Redis for real-time data caching
- [ ] Horizontal scaling support
- [ ] WebSocket price feeds
- [ ] Microservices architecture
- [ ] Event-driven architecture

### Phase 10: Advanced Features
- [ ] Arbitrage detection
- [ ] Market making capabilities
- [ ] Options strategies
- [ ] Social sentiment integration
- [ ] News-based trading signals

## 🐛 Troubleshooting

### Common Issues

**1. API Connection Errors**
```bash
# Check API credentials
echo $BINANCE_API_KEY  # Should show your key

# Test exchange connection
python -c "from app.trading.exchange_client import ExchangeClient; client = ExchangeClient('binance', 'key', 'secret'); print(client.get_ticker('BTC/USDT'))"
```

**2. Insufficient Balance**
- Ensure testnet account has balance
- Lower `max_position_value` in config
- Check if your API key has trading permissions

**3. Strategy Not Trading**
- Check if SMA periods have enough data
- Verify market is open
- Review backtest results for signal generation
- Check logs for error messages

**4. Backtest Errors**
- Ensure historical data date range is valid
- Check if symbol is supported by exchange
- Verify internet connection for data download

## 📚 Resources

- [CCXT Documentation](https://docs.ccxt.com/en/latest/)
- [Binance API Docs](https://binance-docs.github.io/apidocs/)
- [Technical Analysis Library](https://mrjbq7.github.io/ta-lib/)
- [Google Cloud Run Guide](https://cloud.google.com/run/docs)
- [Docker Best Practices](https://docs.docker.com/develop/dev-best-practices/)

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Code Style
- Follow PEP 8
- Add type hints where possible
- Include docstrings for all functions
- Write tests for new features

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## ⚠️ Disclaimer

**IMPORTANT**: This bot is for educational purposes only. Cryptocurrency trading involves substantial risk of loss. 

- Never trade with funds you cannot afford to lose
- Always test strategies thoroughly on testnet before using real funds
- Past performance does not guarantee future results
- The authors are not responsible for any financial losses

## 🆘 Support

- **Issues**: Use GitHub issues for bug reports
- **Discussions**: Use GitHub discussions for questions
- **Email**: 2.tagmf@gmail.com

## 📝 Changelog

### v0.2.0 (Current)
- Added backtesting engine with fee tracking
- Implemented data lineage tracking
- Added performance visualization
- Enhanced risk management

### v0.1.0
- Initial MVP release
- Basic SMA strategy
- Binance integration
- Docker support

---

**Remember**: Start small, test everything, and never risk more than you can afford to lose. Happy trading! 🚀