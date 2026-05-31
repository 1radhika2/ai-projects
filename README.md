# 🎯 AI-Powered Resume Screener

**Project 01** — Tier 1 (Beginner)

Screen resumes against job descriptions using OpenAI embeddings and GPT-4, with semantic similarity scoring and LLM-based evaluation. Perfect for learning parsing, embeddings, and scoring in a real-world HR use case.

---

## Features

- 📄 **Multi-Format Parsing** – Extract text from PDF, DOCX, and TXT resumes
- 🧠 **Semantic Scoring** – Compare resumes to job descriptions using embeddings
- 🤖 **LLM Evaluation** – GPT-4o-mini evaluates fit and provides reasoning
- ⚡ **Async Processing** – Fast, non-blocking API calls
- 🔍 **Detailed Results** – Similarity scores, LLM reasoning, and ranked results
- ✅ **SOLID Architecture** – SRP (parser/embedder/scorer separation), Liskov, DIP

---

## Quick Start

### 1. Prerequisites
- Python 3.11+
- OpenAI API key (with $5+ budget)
- pip

### 2. Setup

```bash
# Navigate to project
cd resume_screener

# Create virtual environment
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Copy and configure environment
cp .env.example .env
# Edit .env and add your OPENAI_API_KEY
```

### 3. Run the API

```bash
uvicorn app.main:app --reload --port 8000
```

The API is available at `http://localhost:8000`

- **Health check:** `GET /health`
- **Screen resumes:** `POST /screen`

### 4. Run the Frontend

In a new terminal:

```bash
streamlit run frontend/streamlit_app.py
```

Open `http://localhost:8501` in your browser.

### 5. Run Tests

```bash
pytest tests/ -v
```

---

## Architecture (SOLID Principles)

| Principle | Implementation |
|-----------|---|
| **SRP** (Single Responsibility) | `parser.py` (text extraction), `embedder.py` (vectors), `scorer.py` (LLM scoring) |
| **Open/Closed** | Add new parsers by subclassing `BaseParser` without modifying existing code |
| **Liskov** | All parsers implement `BaseParser.parse(file_bytes, filename) → str` interface |
| **ISP** (Interface Segregation) | `ScoringService` takes pre-computed score — no dependency on `EmbeddingService` |
| **DIP** (Dependency Inversion) | Services receive `Settings` via constructor injection, not hardcoded config |

---

## Project Structure

```
resume_screener/
├── app/
│   ├── main.py                 # FastAPI app setup
│   ├── core/
│   │   └── config.py          # Settings & environment loading
│   ├── models/
│   │   └── schemas.py         # Pydantic request/response models
│   ├── routers/
│   │   └── screening.py       # /screen endpoint logic
│   └── services/
│       ├── embedder.py        # OpenAI embeddings service
│       ├── parser.py          # PDF/DOCX/TXT parsing
│       └── scorer.py          # LLM-based scoring
├── frontend/
│   └── streamlit_app.py       # Web UI for screening
├── tests/
│   ├── test_parser.py
│   ├── test_scorer.py
│   └── __init__.py
├── requirements.txt
├── .env.example
└── README.md
```

---

## API Endpoints

### `POST /screen`

Screen resumes against a job description.

**Request:**
```json
{
  "job_description": "Senior Python engineer with 5+ years experience...",
  "resumes": [
    { "filename": "alice.pdf", "file_bytes": "<base64>" },
    { "filename": "bob.docx", "file_bytes": "<base64>" }
  ]
}
```

**Response:**
```json
{
  "results": [
    {
      "filename": "alice.pdf",
      "score": 0.87,
      "reasoning": "Strong Python background, exact match on seniority...",
      "rank": 1
    }
  ]
}
```

### `GET /health`

Health check endpoint.

**Response:**
```json
{
  "status": "ok"
}
```

---

## Environment Variables

Create a `.env` file (copy from `.env.example`):

```env
OPENAI_API_KEY=sk-proj-...your-key...
EMBEDDING_MODEL=text-embedding-3-small
CHAT_MODEL=gpt-4o-mini
MAX_UPLOAD_MB=5
```

| Variable | Description | Default |
|----------|---|---|
| `OPENAI_API_KEY` | OpenAI API key | Required |
| `EMBEDDING_MODEL` | Model for semantic search | `text-embedding-3-small` |
| `CHAT_MODEL` | Model for LLM evaluation | `gpt-4o-mini` |
| `MAX_UPLOAD_MB` | Max resume file size | `5` |

---

## Key Concepts

### Parsing
- Supports PDF (PyPDF2), DOCX (python-docx), and TXT formats
- Extensible: add new formats by creating a parser class that inherits `BaseParser`

### Embeddings
- Text-embedding-3-small provides semantic understanding
- Allows fuzzy matching (skills like "Python" ≈ "Python 3")
- Fast and cheap (~0.02¢ per 1M tokens)

### Scoring
- **Similarity Score** (0-1): How well resume matches job description semantically
- **LLM Score** (0-1): GPT-4o-mini provides detailed reasoning
- **Ranking**: Resumes sorted by combined score

---

## Testing

```bash
# Run all tests
pytest tests/ -v

# Run specific test file
pytest tests/test_parser.py -v

# Run with coverage
pytest tests/ --cov=app --cov-report=term-missing
```

---

## Common Issues

### "Incorrect API key provided"
- Check `.env` file has valid `OPENAI_API_KEY`
- Ensure file is in project root
- Restart server after updating `.env`

### "You exceeded your quota"
- Log in to https://platform.openai.com/account/billing/overview
- Add payment method and set spending limit ($5-$10 recommended)

### "Unsupported file type"
- Only PDF, DOCX, TXT supported
- Ensure file extension matches content type

---

## Next Steps

1. **Try the UI** – Upload resumes via Streamlit frontend
2. **Test the API** – Use `curl` or Postman for `/screen` endpoint
3. **Add parsers** – Extend to support RTF, ODT, or HTML
4. **Tune scoring** – Modify LLM prompts in `scorer.py` for your use case
5. **Deploy** – Use Render, Railway, or AWS Lambda

---

## Dependencies

- **FastAPI** – Web framework
- **Uvicorn** – ASGI server
- **Streamlit** – Frontend UI
- **LangChain** – LLM abstractions
- **OpenAI** – API client
- **PyPDF2** – PDF parsing
- **python-docx** – DOCX parsing
- **Pydantic** – Data validation

See [requirements.txt](requirements.txt) for full list.

---

## License

MIT License – see [LICENSE](../LICENSE) for details.

---

## Resources

- [OpenAI API Docs](https://platform.openai.com/docs)
- [FastAPI Tutorial](https://fastapi.tiangolo.com)
- [Streamlit Docs](https://docs.streamlit.io)
- [SOLID Principles](https://en.wikipedia.org/wiki/SOLID)
