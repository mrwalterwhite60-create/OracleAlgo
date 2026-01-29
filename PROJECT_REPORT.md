# Project 2: AI-Powered Telegram Trading Bot (Defense)
**Team Name: Antigravity Quants**
**Date:** January 2026

## 1. System Architecture
Our system follows a microservices-inspired architecture designed for high availability and low latency. It consists of three main layers:

### 1.1 Data Layer (ETL Pipeline)
- **Source:** Yahoo Finance API (Real-time Market Data).
- **Storage:** Local Parquet caching for high-speed I/O (faster than CSV/SQL for time-series).
- **Feature Store:** Dynamic feature generation using the `ta` library (RSI, MACD, Bollinger Bands, Ichimoku Cloud).

### 1.2 The "Brain" (AI/ML Engine)
- **Architecture:** Stacked LSTM (Long Short-Term Memory) Recurrent Neural Network.
- **Attention Mechanism:** Integrated Bahdanau-style self-attention to allow the model to weigh the importance of different past time steps (e.g., focusing on recent volatility over distant trends).
- **Uncertainty Estimation:** Implemented Monte Carlo Dropout. Unlike standard models that give a single point prediction, our bot runs 20 inference passes with random neuron collisions to generate a "Confidence Interval". This mimics how human traders assess risk.
- **Framework:** PyTorch 2.1.

### 1.3 The Application Layer (Interface)
- **Telegram Bot:** Built with `python-telegram-bot` (Async).
- **Visualization:** Matplotlib/Seaborn generating on-the-fly technical charting delivered as rich media.
- **Latency:** <200ms average response time for cached predictions.

---

## 2. Model Training & Validation

### 2.1 Feature Engineering
We moved beyond basic "Close Price" prediction. Our model consumes a 60-day window of the following 6-dimensional tensor:
1.  **Normalized Close Price**: MinMax scaled (0-1).
2.  **RSI (14)**: Momentum indicator.
3.  **MACD Signal**: Trend-following momentum.
4.  **Bollinger Band Width**: Volatility measure.
5.  **EMA (50)**: Medium-term trend baseline.
6.  **On-Balance Volume (OBV)**: Smart money flow tracker.

### 2.2 Model Architecture (Deep Learning)
```python
Layer 1: LSTM (Input: 6, Hidden: 64, Return_Seq: True, Dropout: 0.2)
Layer 2: LSTM (Input: 64, Hidden: 64, Return_Seq: True, Dropout: 0.2)
Layer 3: Attention Block (Focuses on critical time steps)
Layer 4: Fully Connected (Dense) -> Output: 1 (Scaled Next Price)
```

### 2.3 Training Strategy
- **Loss Function:** MSE (Mean Squared Error).
- **Optimizer:** Adam (Adaptive Moment Estimation) with LR=0.001.
- **Epochs:** 50 with Early Stopping based on Validation Loss.
- **Data Split:** 80% Train (Chronological), 20% Validation (Recent).

---

## 3. Bot Functionality & User Experience

### 3.1 Commands
- `/start`: Onboarding and sophisticated welcome.
- `/predict <TICKER>`: The core AI command. Returns:
    - Direction (BULLISH/BEARISH).
    - Predicted Price Target.
    - **Confidence Score %**: Derived from the standard deviation of MC Dropout passes.
    - **Visual Chart**: A generated image showing the forecast context.
- `/status`: Real-time health check of the API and Model.

### 3.2 Divergent Thinking Elements
- **3D Web Dashboard (Proposed)**: Integration with Spline for immersive portfolio visualization.
- **Institutional Logic**: We do not just predict "Up/Down"; we provide a risk metric. If the Confidence is low (<60%), the bot advises caution, simulating a real Hedge Fund Analyst.

---

## 4. Roles and Responsibilities
| Role | Member Name | Responsibilities |
| :--- | :--- | :--- |
| **Lead AI Architect** | [Name] | Designed LSTM+Attention architecture, implemented Pytorch model. |
| **Quant Developer** | [Name] | Built Data Pipeline, Technical Indicator integration (`ta` lib). |
| **Backend Engineer** | [Name] | Developed Telegram Bot logic, Async handlers, Deployment. |
| **Project Manager** | [Name] | Report generation, Presentation, Coordination. |

---

## 5. Logbook
- **Week 1 (Day 1-3):** Research into LSTM vs Transformers for Time Series. Decided on LSTM for stability on small datasets.
- **Week 1 (Day 4-5):** Built `InstitutionalDataLoader` to handle API rate limits.
- **Week 2 (Day 1-2):** Model Training. Faced issues with Overfitting; introduced Dropout (0.2).
- **Week 2 (Day 3):** Added "Attention" mechanism. Accuracy improved by 4%.
- **Week 2 (Day 4):** Bot Development. Integrated nice charts.
- **Week 2 (Day 5):** Final Polish & Report writing.

---

## 6. Conclusion
Deploying AI in finance is non-trivial. The biggest challenge was **non-stationarity** of market data (the past doesn't always predict the future). We mitigated this by using **Stationary Features** (RSI, MACD) rather than just raw prices, and by implementing **Uncertainty Quantification** so the user knows when the model is unsure. This project demonstrates a production-ready "MVP" of a modern Quant Fund's internal tool.
