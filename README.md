Crypto Trading Bot MVP
A lightweight, single-exchange cryptocurrency trading bot designed for algorithmic trading with non-ML strategies. Built with modularity and cloud deployment in mind.
🚀 Quick Start
bash# Clone the repository
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

# Run the bot
python -m app.main
📋 Features
Current MVP Features

Single Exchange Trading: Binance support via CCXT
Simple Strategy: SMA (Simple Moving Average) crossover strategy
Risk Management: Position limits and stop-loss protection
Paper Trading: Testnet support for safe strategy testing
Docker Support: Easy deployment with Docker and docker-compose
Cloud Ready: Prepared for Google Cloud Run deployment

What This Bot Does

Connects to a single exchange (Binance)
Monitors a single trading pair (e.g., BTC/USDT)
Executes trades based on SMA crossover signals
Manages position size based on risk parameters
Implements stop-loss for downside protection

What This Bot Doesn't Do (Yet)

No machine learning models
No multi-exchange arbitrage
No complex portfolio management
No web interface
No backtesting engine
No performance analytics

🏗️ Architecture
crypto-trading-mvp/
├── app/
│   ├── core/                 # Core infrastructure
│   │   ├── param_manager.py  # Configuration management
│   │   ├── data_context.py   # Data container with metadata
│   │   └── exceptions.py     # Custom exceptions
│   ├── trading/              # Trading logic
│   │   ├── exchange_client.py # Exchange connectivity
│   │   ├── data_collector.py  # OHLCV data collection
│   │   ├── risk_manager.py    # Risk management
│   │   ├── engine.py          # Main trading engine
│   │   └── strategies/        # Strategy implementations
│   │       ├── base.py        # Base strategy class
│   │       └── sma_cross.py   # SMA crossover strategy
│   └── main.py               # Application entry point
├── config/
│   ├── config.yaml           # Main configuration
│   └── config.example.yaml   # Example configuration
├── tests/                    # Unit tests
├── docker-compose.yml        # Docker composition
├── Dockerfile               # Container definition
├── requirements.txt         # Python dependencies
└── README.md               # This file
🔧 Configuration
Basic Configuration (config/config.yaml)
yamlexchange:
  name: binance
  api_key: ${BINANCE_API_KEY}     # From environment variable
  api_secret: ${BINANCE_API_SECRET}
  testnet: true                    # Use testnet for paper trading

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
Environment Variables
bash# .env file (don't commit this!)
BINANCE_API_KEY=your_api_key_here
BINANCE_API_SECRET=your_api_secret_here
🐳 Docker Deployment
Local Deployment
bash# Build and run with docker-compose
docker-compose up -d

# View logs
docker-compose logs -f trading-bot

# Stop the bot
docker-compose down
Google Cloud Run Deployment
bash# Setup GCP project
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
🧪 Testing
bash# Run all tests
pytest

# Run with coverage
pytest --cov=app tests/

# Run specific test file
pytest tests/test_strategy.py
📊 Strategy Details
SMA Crossover Strategy
The bot implements a simple but effective SMA crossover strategy:

Fast SMA (default: 10 periods): Responds quickly to price changes
Slow SMA (default: 30 periods): Provides trend direction
Buy Signal: When fast SMA crosses above slow SMA
Sell Signal: When fast SMA crosses below slow SMA

Risk Management

Position Sizing: Never risk more than 10% of account balance
Stop Loss: Automatic 2% stop loss on all positions
Maximum Position Value: Capped at $1000 per trade

🚧 Development Roadmap
Phase 1: MVP (Current)

 Basic exchange connectivity
 Simple SMA strategy
 Risk management framework
 Docker deployment
 Cloud Run ready

Phase 2: Enhanced Strategies

 RSI (Relative Strength Index) strategy
 Bollinger Bands strategy
 MACD strategy
 Strategy combination framework

Phase 3: Data & Analytics

 Local database for trade history (PostgreSQL)
 Performance metrics calculation
 Basic backtesting engine
 CSV export for analysis

Phase 4: Web Interface

 REST API for bot control
 Simple web dashboard (Flask/FastAPI)
 Real-time position monitoring
 Performance charts

Phase 5: Advanced Risk Management

 Dynamic position sizing based on volatility
 Correlation-based risk limits
 Maximum drawdown controls
 Time-based stop losses

Phase 6: Multi-Asset Support

 Multiple trading pairs
 Portfolio rebalancing
 Asset correlation tracking
 Sector exposure limits

Phase 7: Machine Learning Integration

 Feature engineering pipeline
 ML model integration framework
 Probability-based position sizing
 Model performance tracking

Phase 8: Production Features

 Multi-exchange support
 Order book integration
 Advanced order types
 Emergency shutdown mechanisms
 Alerting and monitoring

Phase 9: Scaling & Performance

 Redis for real-time data caching
 Horizontal scaling support
 WebSocket price feeds
 Microservices architecture

Phase 10: Advanced Features

 Arbitrage detection
 Market making capabilities
 Options strategies
 Social sentiment integration

🐛 Troubleshooting
Common Issues
1. API Connection Errors
bash# Check API credentials
echo $BINANCE_API_KEY  # Should show your key

# Test exchange connection
python -c "from app.trading.exchange_client import ExchangeClient; client = ExchangeClient('binance', 'key', 'secret'); print(client.get_ticker('BTC/USDT'))"
2. Insufficient Balance

Ensure testnet account has balance
Lower max_position_value in config

3. Strategy Not Trading

Check if SMA periods have enough data
Verify market is open
Check logs for signals

📚 Resources

CCXT Documentation
Binance API Docs
Google Cloud Run Guide
Docker Best Practices

🤝 Contributing

Fork the repository
Create your feature branch (git checkout -b feature/amazing-feature)
Commit your changes (git commit -m 'Add some amazing feature')
Push to the branch (git push origin feature/amazing-feature)
Open a Pull Request

📄 License
This project is licensed under the MIT License - see the LICENSE file for details.
⚠️ Disclaimer
This bot is for educational purposes only. Cryptocurrency trading involves substantial risk of loss. Never trade with funds you cannot afford to lose. Always test strategies thoroughly on testnet before using real funds.
🆘 Support

Issues: Use GitHub issues for bug reports
Discussions: Use GitHub discussions for questions
Email: your-email@example.com


Remember: Start small, test everything, and never risk more than you can afford to lose. Happy trading! 🚀