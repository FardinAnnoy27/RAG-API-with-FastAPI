```markdown
# 🤖 RAG API with FastAPI

## 📋 Project Overview

A local **Retrieval-Augmented Generation (RAG)** API built with **FastAPI**, **ChromaDB**, and **Ollama** that answers personal questions by grounding AI responses in your own documents — running entirely on your machine with zero cloud costs.

Ever asked an AI a question about yourself and gotten a completely made-up answer? LLMs only know what they were trained on, not what's specific to you. This project solves that by implementing a full RAG pipeline that retrieves real context from a personal knowledge base before generating answers.

### What It Does
- 📄 **RAG-assisted AI** — Ask questions about yourself and get accurate, grounded answers.
- 🔍 **Semantic Search** — ChromaDB vector database retrieves the most relevant context using vector embeddings.
- 🚀 **REST API** — A FastAPI-powered `/ask` endpoint that implements the full RAG pipeline.
- 💎 **Multi-User AI Directory** — Extended API where anyone can submit their profile and be queried.

---

## 🏗️ Architecture


```

```
     ┌──────────────┐
     │  User/Client │
     └──────┬───────┘
            │  HTTP Request (GET /ask?question=...)
            ▼
    ┌───────────────┐
    │   FastAPI     │
    │   (app.py)    │
    │   REST API    │
    └───────┬───────┘
            │
  ┌─────────┴──────────┐
  │                    │
  ▼                    ▼
┌──────────────┐    ┌───────────────┐
│  ChromaDB    │    │    Ollama     │
│  (Vector DB) │    │  (Local LLM)  │
│              │    │               │
│  Stores &    │    │ qwen2.5:0.5b  │
│  retrieves   │    │ (Generation)  │
│  embeddings  │    │               │
└──────┬───────┘    └───────────────┘
│                    ▲
│  Relevant context  │  Augmented prompt
└────────────────────┘

```

### How the RAG Pipeline Works

1. **Question comes in** ➔ User sends a question via the `/ask` endpoint.
2. **Retrieve** ➔ ChromaDB performs semantic search using `nomic-embed-text` embeddings to find the most relevant context from your knowledge base.
3. **Augment** ➔ The retrieved context is injected into a prompt template alongside the user's question.
4. **Generate** ➔ Ollama's `qwen2.5:0.5b` model generates a grounded answer using the augmented prompt.
5. **Response** ➔ The API returns the AI-generated answer to the user.

---

## 🛠️ Tech Stack

| Tool | Purpose |
| :--- | :--- |
| **FastAPI** | REST API framework |
| **ChromaDB** | Vector database for semantic search |
| **Ollama** | Local LLM runtime (no cloud costs!) |
| **nomic-embed-text** | Embedding model for vector search |
| **qwen2.5:0.5b** | LLM for response generation |
| **Python 3.10+** | Programming language |
| **Uvicorn** | ASGI server to run FastAPI |

---

## 📁 Project Structure

```text
nextwork-rag-api/
├── app.py              # FastAPI application with /ask endpoint
├── embed.py            # Script to create and store vector embeddings
├── k8s.txt             # Knowledge base document (personal profile)
├── requirements.txt    # Python dependencies
├── db/                 # ChromaDB vector database (auto-generated)
├── venv/               # Python virtual environment (not committed)
└── README.md           # Project documentation

```

---

## ⚙️ Prerequisites

Before running this project, make sure you have the following installed:

### 1. Python 3.10+

Check if Python is installed:

```powershell
python --version

```

If not installed, download it from [python.org](https://www.python.org/downloads/).

### 2. Ollama

Ollama runs AI models locally on your machine. Download it from [ollama.com](https://ollama.com).

Verify Ollama is running:

```powershell
curl.exe http://localhost:11434

```

You should see: `Ollama is running`

> 💡 **Tip:** If Ollama isn't running, open the Ollama app from your Start Menu to kick off the background service.

---

## 🚀 Getting Started

Follow these steps to set up and run the project from scratch.

### Step 1: Clone the Repository

```powershell
git clone [https://github.com/FardinAnnoy27/nextwork-rag-api.git](https://github.com/FardinAnnoy27/nextwork-rag-api.git)
cd nextwork-rag-api

```

### Step 2: Create and Activate a Virtual Environment

A virtual environment isolates your Python packages so they don't conflict with other projects.

```powershell
# Create the virtual environment
python -m venv venv

# Activate it (Windows PowerShell)
.\venv\Scripts\Activate.ps1

# Activate it (Windows CMD)
.\venv\Scripts\activate.bat

```

> ✅ You'll know it's activated when you see `(venv)` at the start of your terminal prompt.

### Step 3: Install Python Dependencies

```powershell
pip install fastapi uvicorn chromadb ollama

```

| Package | What It Does |
| --- | --- |
| `fastapi` | Web framework for building the REST API |
| `uvicorn` | ASGI server that runs the FastAPI app |
| `chromadb` | Vector database for storing and searching embeddings |
| `ollama` | Python client for interacting with Ollama models |

### Step 4: Pull the Required Ollama Models

These models run locally — no API keys or cloud costs required.

```powershell
# Embedding model — converts text into vector embeddings for semantic search
ollama pull nomic-embed-text

# LLM model — generates natural language responses
ollama pull qwen2.5:0.5b

```

| Model | Purpose | Size |
| --- | --- | --- |
| `nomic-embed-text` | Creates vector embeddings for semantic search | ~274 MB |
| `qwen2.5:0.5b` | Generates AI responses from augmented prompts | ~395 MB |

### Step 5: Set Up Your Knowledge Base

The `k8s.txt` file is your personal knowledge base document. Edit it to include your own information (name, skills, hobbies, goals, etc.).

```powershell
# Open and edit the knowledge base file
notepad k8s.txt

```

Example content:

```text
Name: Fardin
Skills: Python, FastAPI, AWS, AI/ML
Hobbies: Coding, learning new technologies
Goals: Become an AI engineer

```

### Step 6: Build the Vector Database

This script reads your knowledge base, converts it into vector embeddings using `nomic-embed-text`, and stores them in ChromaDB.

```powershell
python embed.py

```

> 💾 This creates a local `db/` directory containing your ChromaDB vector database.

### Step 7: Start the API Server

```powershell
uvicorn app:app --reload

```

You should see output like:

```text
INFO:     Uvicorn running on [http://127.0.0.1:8000](http://127.0.0.1:8000) (Press CTRL+C to quit)
INFO:     Started reloader process

```

> 🔄 The `--reload` flag automatically restarts the server whenever you save changes to your code.

---

## 🧪 Testing the API

### Option 1: Swagger UI (Recommended)

Open your browser and navigate to:

```text
[http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)

```

This opens FastAPI's built-in interactive API documentation where you can:

1. Click on the `/ask` endpoint.
2. Click **Try it out**.
3. Enter a question (e.g., `"What are your hobbies?"`).
4. Click **Execute**.

### Option 2: Direct Browser URL

```text
[http://127.0.0.1:8000/ask?question=What](http://127.0.0.1:8000/ask?question=What) are your hobbies?

```

### Option 3: PowerShell / Terminal

```powershell
curl.exe "[http://127.0.0.1:8000/ask?question=What%20are%20your%20hobbies](http://127.0.0.1:8000/ask?question=What%20are%20your%20hobbies)?"

```

### Example Response

```json
{
  "question": "What are your hobbies?",
  "answer": "Based on the provided context, your hobbies include coding and learning new technologies.",
  "context": "Hobbies: Coding, learning new technologies"
}

```

---

## 🛑 Stopping the Server

To stop the API server, press `CTRL + C` in your terminal.

To deactivate the virtual environment:

```powershell
deactivate

```

---

## 💎 Secret Mission: Multi-User AI Directory

The API was extended into a multi-user system where:

* Anyone can **submit their profile** via a POST endpoint.
* Users can **query any submitted profile** by name.
* Each profile is stored as unique vector embeddings in ChromaDB.
* Enables a scalable, AI-powered people directory.

---

## 📚 What I Learned

* 🔍 How **RAG (Retrieval-Augmented Generation)** architectures function from ingestion to generation.
* 📄 How to manage a local semantic vector space using **ChromaDB**.
* 🚀 How to design clean, documentation-first **REST APIs** with **FastAPI**.
* 🧠 Running open-source localized models via **Ollama** (`nomic-embed-text` & `qwen2.5:0.5b`).
* 🏗️ Implementing the classic **Retrieve ➔ Augment ➔ Generate** production pattern.

---

## 🔮 What's Next — DevOps × AI Series

This project is **Part 2** of a 4-part **DevOps × AI Series**:

| # | Project | Status |
| --- | --- | --- |
| 1 | Set Up a Local AI Environment with Ollama | ✅ Complete |
| 2 | **Build a RAG API with FastAPI** | 🚀 **You are here** |
| 3 | Containerize Your RAG API with Docker | 🐳 Up Next |
| 4 | Automate Testing with GitHub Actions | ⚙️ Coming Soon |

---

## 🙏 Acknowledgments

Built as part of the [NextWork](https://learn.nextwork.org) DevOps × AI project series.

```

---

### How to apply this to your project right away:

1. Open **PowerShell** and navigate to your project:
   ```powershell
   cd path\to\nextwork-rag-api

```

2. Open the file with Notepad:
```powershell
notepad README.md

```


3. Clear out whatever is there, **paste the block above**, change `YOUR_USERNAME` to your actual GitHub handle, save, and close!
4. Save your updated package list to `requirements.txt`:
```powershell
pip freeze > requirements.txt

```


5. Commit and push everything up to your repo:
```powershell
git add README.md requirements.txt
git commit -m "docs: upgrade README with clean architecture diagram and exact setup steps"
git push

```
