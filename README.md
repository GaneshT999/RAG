# RAG Pipeline with PDF + ChromaDB + Groq LLM

This project builds a Retrieval-Augmented Generation (RAG) pipeline that:
- Loads PDF files
- Splits them into chunks
- Generates embeddings
- Stores/retrieves chunks from ChromaDB
- Uses a Groq-hosted LLM to answer questions from retrieved context

The main implementation and experiments are in the notebook:
- `notebook/pdf_loader.ipynb`

## Project Structure

```
main.py
pyproject.toml
requirements.txt
README.md
data/
	pdf_files/
	text_files/
	vector_store/
notebook/
	pdf_loader.ipynb
	document.ipynb
```

## Prerequisites

- Python 3.10+
- A Groq API key

## Installation

From project root:

```bash
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

If you use `pyproject.toml` workflow, install with your preferred tool (for example `pip install -e .`).

## Environment Variables

Create a `.env` file in project root:

```env
GROQ_API_KEY=your_groq_api_key_here
GROQ_MODEL=llama-3.1-8b-instant
```

`GROQ_MODEL` is optional; default is `llama-3.1-8b-instant`.

## How the Pipeline Works

### 1) PDF Ingestion
- `process_all_pdfs(...)` recursively finds PDFs and loads pages using `PyPDFLoader`.
- Metadata such as `source_file` and `file_type` is attached.

### 2) Chunking
- `split_documents(...)` uses `RecursiveCharacterTextSplitter`.
- Default settings: `chunk_size=1000`, `chunk_overlap=200`.

### 3) Embeddings
- `EmbeddingManager` loads `all-MiniLM-L6-v2` from sentence-transformers.
- Converts chunk text into vector embeddings.

### 4) Vector Store
- `VectorStore` initializes persistent ChromaDB in `data/vector_store`.
- Stores: chunk text, metadata, and embedding vectors.

### 5) Retrieval
- `RAGRetriever.retrieve(...)` embeds the query and runs vector similarity search.
- Converts Chroma distance to similarity score via: `similarity = 1 - distance`.

### 6) Answer Generation
- `rag_simple(...)` retrieves top chunks and asks Groq LLM for an answer.
- `reg_advanced(...)` adds source list + confidence score.
- `AdvancedRAGPipeline` adds history and optional summarization.

## Notebook Usage

Open and run cells in order in:
- `notebook/pdf_loader.ipynb`

Recommended flow:
1. Load PDFs
2. Split into chunks
3. Build embeddings
4. Store in ChromaDB
5. Retrieve by query
6. Run simple/advanced RAG answer generation





