# ✍️ Next-Word Prediction System — Intelligent NLP Web Application

---

## 🚀 Live Application

🔗 [Next-Word Prediction Live Demo](https://www.google.com/search?q=https://your-live-demo-url.onrender.com)

---

## 📖 Overview

**Next-Word Prediction System** is an advanced natural language processing (NLP) application designed to predict subsequent words and generate fluent text continuations based on deep learning sequence models.

The system incorporates three distinct recurrent neural network architectures trained on an identical text corpus with rigorous preprocessing:

* 🧠 **LSTM (Long Short-Term Memory)** — Default architecture optimized for long-range dependencies
* 🔄 **GRU (Gated Recurrent Unit)** — Balanced architecture offering fast training and high efficiency
* ⚡ **SimpleRNN** — Baseline recurrent architecture for comparative performance

Instead of retraining on the fly, the application loads pre-trained `.keras` model artifacts and pickled tokenizers, ensuring instantaneous response times and faithful reproduction of notebook preprocessing, top-$k$ prediction, and temperature-sampling logic.

---

## ✨ Features

* **Next-Word Prediction:** Input any sentence or phrase to predict the top-$k$ most probable next words with associated confidence probabilities.
* **Dynamic Model Switching:** Switch seamlessly between LSTM, GRU, and SimpleRNN architectures per request via the UI dropdown or API field.
* **Multi-Word Text Generation:** Generate extended token continuations from an arbitrary text seed.
* **Adjustable Temperature Sampling:** Control output creativity ($T = 0$ for greedy deterministic decoding, higher values for diverse sampling).
* **Interactive REST API:** Automatic OpenAPI documentation and interactive testing via `/docs` (Swagger UI).
* **Modern Bootstrap UI:** Clean, responsive single-page interface for seamless user interaction.
* **Containerised Deployment:** Fully configured with Docker, Render, and Railway configuration files.
* **Robust Test Suite:** Comprehensive `pytest` coverage running with real-artifact readiness and mocked test support.

---

## ⚙️ How It Works

```text
                  USER INPUT
                      │
                      ▼
         ┌─────────────────────────┐
         │  Web UI / FastAPI REST  │
         └────────────┬────────────┘
                      │
                      ▼
         ┌─────────────────────────┐
         │    Text Normalisation   │
         │   & Tokenizer Lookup    │
         └────────────┬────────────┘
                      │
                      ▼
         ┌─────────────────────────┐
         │   Sequence Padding      │
         │   (Context Length = N)  │
         └────────────┬────────────┘
                      │
                      ▼
         ┌─────────────────────────┐
         │  Selected Model Artifact│
         │ (LSTM / GRU / SimpleRNN)│
         └────────────┬────────────┘
                      │
                      ▼
         ┌─────────────────────────┐
         │ Softmax Output & Top-K  │
         │ & Temperature Sampling  │
         └────────────┬────────────┘
                      │
                      ▼
         ┌─────────────────────────┐
         │    JSON API Response    │
         └─────────────────────────┘

```

---

## 🏗️ Project Architecture & Layout

```text
next_word_predictor/
├── app/
│   ├── main.py              # FastAPI application entrypoint
│   ├── routes/              # API route handlers (/predict, /generate, /health, /models)
│   ├── services/            # Predictor logic (predict_next_word, generate_text)
│   ├── models/              # Model loaders, tokenizer loader, and Pydantic schemas
│   ├── utils/               # Text cleaning and normalisation utilities
│   └── config/              # Application settings and environment variables
├── static/                  # Frontend assets (CSS stylesheets, client-side JS)
├── templates/               # HTML single-page interface (index.html)
├── notebooks/               # Training scripts and artifact export utilities
├── saved_models/            # Directory for trained .keras models, tokenizer.pkl, config.json
├── data/                    # Corpus data files (e.g., Articles.csv sample)
├── tests/                   # Pytest automated test suite
├── requirements.txt         # Pinned python dependencies
├── Dockerfile               # Container build instructions
├── Procfile                 # Railway / Heroku process configuration
├── render.yaml              # Render cloud deployment blueprint
└── README.md                # Project documentation

```

---

## 🚀 Installation & Setup

### 1. Clone the Repository

```bash
git clone https://github.com/Abdulla-B-Official/Next-Word-Prediction-System.git
cd Next-Word-Prediction-System

```

### 2. Create and Activate Virtual Environment

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

```

### 3. Install Dependencies

> ⚠️ **Important Note on Keras Version:** The `.keras` artifacts were trained and exported using **Keras 3.13.2**. `requirements.txt` pins `tensorflow==2.16.1` and `keras==3.13.2`. Installing TensorFlow alone pulls newer Keras versions that may fail to read older checkpoints. Always install via requirements:

```bash
pip install -r requirements.txt

```

---

## 🏃 Running Locally

Start the development server using Uvicorn:

```bash
uvicorn app.main:app --reload

```

* **Web Interface:** [http://localhost:8000/](http://localhost:8000/)
* **Interactive API Docs:** [http://localhost:8000/docs](http://localhost:8000/docs)
* **Health Check:** [http://localhost:8000/health](http://localhost:8000/health)

---

## 🔌 API Endpoints & Usage

### `POST /predict`

Predict the top-$k$ next words for a given text snippet.

**Request Body:**

```json
{
  "text": "the stock market",
  "top_k": 5,
  "model": "lstm"
}

```

**Response:**

```json
{
  "input": "the stock market",
  "model_used": "lstm",
  "predictions": [
    { "word": "rose", "probability": 0.41 },
    { "word": "fell", "probability": 0.22 },
    { "word": "opened", "probability": 0.12 }
  ]
}

```

### `POST /generate`

Generate multi-word text continuations from a seed phrase.

**Request Body:**

```json
{
  "text": "the government has decided to",
  "num_words": 10,
  "temperature": 0.7,
  "model": "lstm"
}

```

**Response:**

```json
{
  "input": "the government has decided to",
  "generated_text": "the government has decided to implement new economic reforms across sectors...",
  "num_words": 10,
  "temperature": 0.7
}

```

### `GET /health`

Returns system status and model readiness.

```json
{
  "status": "ok",
  "model_loaded": true,
  "active_model": "lstm",
  "context_length": 15,
  "vocab_size": 5001
}

```

### `GET /models`

Lists all available trained model architectures loaded in memory.

**cURL Example:**

```bash
curl -X POST http://localhost:8000/predict \
  -H "Content-Type: application/json" \
  -d '{"text":"artificial intelligence is","top_k":3}'

```

---

## 📦 Model Training & Artifacts

To train the models from scratch using your own corpus:

```bash
python notebooks/train_models.py --data data/Articles.csv --out saved_models

```

### Artifact Specifications

* **Model Checkpoints:** Saved as `next_word_lstm_best.keras`, `next_word_gru_best.keras`, and `next_word_rnn_best.keras`.
* **Tokenizer:** Serialised using `pickle` as `tokenizer.pkl`, persisting the complete vocabulary mapping (`word_index` / `index_word`).
* **Configuration:** `config.json` preserves context length and hyperparameter settings to guarantee preprocessing parity.

---

## 🧪 Testing

Run the automated test suite using `pytest`:

```bash
pytest

```

The tests validate tokenisation normalisation, sequence padding, predictor logic (top-$k$ ranking, OOV handling, temperature sampling), and FastAPI endpoint validation schemas.

---

## 🚢 Deployment

### Docker Containerisation

```bash
docker build -t next-word-predictor .
docker run -p 8000:8000 next-word-predictor

```

*(The container automatically detects the `$PORT` environment variable).*

### Render & Railway

* **Render:** Connect your GitHub repository and configure using `render.yaml`.
* **Railway:** Deploy directly using the provided `Procfile`:
```text
web: uvicorn app.main:app --host 0.0.0.0 --port $PORT

```



---

## 🛠️ Technology Stack

| Technology | Purpose |
| --- | --- |
| **Python** | Core Programming Language |
| **FastAPI** | High-Performance REST API & OpenAPI Docs |
| **TensorFlow / Keras** | Deep Learning Framework & Recurrent Networks |
| **Uvicorn** | ASGI Web Server |
| **Bootstrap 5** | Responsive Frontend UI Framework |
| **Docker** | Containerisation & Environment Isolation |
| **Pytest** | Automated Testing Framework |
| **Git / GitHub** | Version Control & Collaboration |

---

## 🎯 Project Outcomes

* ✅ Successfully trained and compared **LSTM, GRU, and SimpleRNN** architectures on text corpora.
* ✅ Developed a robust inference pipeline with exact preprocessing parity.
* ✅ Built an intuitive, responsive single-page web interface.
* ✅ Exposed clean, validated endpoints via **FastAPI** with automatic documentation.
* ✅ Successfully packaged and containerised the application for cloud deployment.

---

### 🌟 Predict Smarter. Code Faster. Automate Natural Language.

⭐ If you found this project useful, consider giving the repository a star!
