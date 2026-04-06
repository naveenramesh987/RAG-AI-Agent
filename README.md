# RAG PDF Assistant

A robust Retrieval-Augmented Generation (RAG) application that handles PDF ingestion and conversational querying using a distributed task queue architecture.

## 📖 Overview

This project implements a reliable RAG pipeline designed to handle long-running document processing tasks. Unlike standard API-based RAG setups, this system uses Inngest to manage background jobs, ensuring that large PDF processing and AI generation are resilient to timeouts and failures.

Users can upload PDFs via a Streamlit interface, which triggers an asynchronous ingestion workflow. Once processed, users can ask natural language questions, and the system retrieves relevant context from Qdrant to generate precise answers via OpenAI's GPT-4o-mini.

## ✨ Key Features

- **Asynchronous Ingestion** — Uses Inngest to handle PDF processing, chunking, and embedding in the background
- **Reliable Workflows** — Implements rate limiting and throttling on ingestion tasks to manage API costs and system load
- **Semantic Search** — Leverages Qdrant and `text-embedding-3-large` for high-dimensional vector retrieval
- **AI Orchestration** — Uses Inngest's experimental AI step orchestration for resilient LLM calls
- **Real-time UI** — A Streamlit dashboard that tracks background job progress and displays grounded AI answers

## 🛠️ Tech Stack

| Layer | Tool |
| --- | --- |
| Backend | FastAPI + Inngest |
| Frontend | Streamlit |
| Vector DB | Qdrant |
| Embeddings & LLM | OpenAI |
| PDF Parsing | LlamaIndex |
| Language | Python |

## 🚀 Installation

1. **Clone the repository**

   ```bash
   git clone <your-repository-url>
   cd rag-inngest-app
   ```

2. **Install dependencies**

   ```bash
   pip install fastapi inngest streamlit qdrant-client openai llama-index-readers-file llama-index-core python-dotenv pydantic
   ```

3. **Set up environment variables**

   Create a `.env` file in the root directory:

   ```env
   OPENAI_API_KEY=your_openai_api_key_here
   INNGEST_API_BASE=http://127.0.0.1:8288/v1
   ```

4. **Start infrastructure**

   ```bash
   # Start Qdrant
   docker run -d --name qdrant -p 6333:6333 -v "$(pwd)/qdrant_storage:/qdrant/storage" qdrant/qdrant

   # Start Inngest Dev Server
   npx inngest-cli@latest dev -u http://localhost:8000/api/inngest
   ```

## 📂 Project Structure

```text
├── main.py           # FastAPI server defining the Inngest functions and workflow logic
├── streamlit_app.py  # Web interface for interacting with the RAG system
├── data_loader.py    # Logic for reading PDFs and generating OpenAI embeddings
├── vector_db.py      # Qdrant client wrapper for upserting and searching vectors
└── custom_types.py   # Pydantic models for structured data passing between steps
```

## 🖥️ Usage

1. **Launch the backend**

   ```bash
   uvicorn main:app --reload --port 8000
   ```

2. **Launch the frontend**

   ```bash
   streamlit run streamlit_app.py
   ```

3. **Process and query**

   - Upload a PDF in the UI — the "Ingest PDF" event is sent to Inngest
   - Once the success message appears, type a question in the query form
   - The app polls the Inngest event run until the LLM generates a response based on your document
