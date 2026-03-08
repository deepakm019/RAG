# RAG with Ollama, LangChain, and PDFs

A Retrieval-Augmented Generation (RAG) system that enables question-answering over PDF documents using local LLMs via Ollama, with semantic search powered by FAISS vector database.

## Features

- **Local LLM Integration**: Uses Ollama for running language models locally without cloud dependencies
- **PDF Processing**: Automatically loads and processes PDF documents
- **Semantic Search**: FAISS vector database for efficient similarity-based document retrieval
- **RAG Chain**: LangChain-powered retrieval-augmented generation for context-aware answers
- **Streaming Output**: Real-time response generation with streaming callbacks
- **Persistent Vector Store**: Save and load FAISS indices for reusability

## Prerequisites

### Required Software
- **Ollama**: Download from [ollama.ai](https://ollama.ai)
- **Python**: 3.9 or higher
- **pip**: Python package manager

### System Requirements
- **RAM**: Minimum 8 GB (16 GB recommended for larger models)
- **Disk Space**: 10-20 GB for model downloads

## Installation

### 1. Install Ollama
Download and install from [ollama.ai](https://ollama.ai)

### 2. Clone/Setup Project
```bash
cd "e:\Personal Projects\RAG"
python -m venv venv
```

### 3. Activate Virtual Environment

**On Windows (PowerShell):**
```powershell
.\.venv\Scripts\Activate.ps1
```

**On Windows (CMD):**
```cmd
.venv\Scripts\activate.bat
```

**On macOS/Linux:**
```bash
source venv/bin/activate
```

### 4. Install Python Dependencies
```bash
pip install -r requirements.txt
```

Or install the package:
```bash
pip install .
```

## Usage

### 1. Start Ollama
In a terminal, start the Ollama server:
```bash
ollama serve
```

### 2. Pull Models
In another terminal, download the required models:
```bash
# For LLM (language model)
ollama pull llama3.2:1b

# For embeddings
ollama pull all-minilm
```

### 3. Run the Notebook
1. Ensure you have a PDF file named `sample.pdf` in the project directory
2. Open `rag_with_ollama.ipynb` in Jupyter or VS Code
3. Run cells sequentially from top to bottom

## Configuration

### Model Selection

Edit the model name in the notebook's Ollama initialization cell:

```python
llm = ChatOllama(
    model="llama3.2:1b",  # Change this
    base_url="http://localhost:11434"
)
```

**Available Models:**
- `llama3.2:1b` - Lightweight option (1B parameters, ~2GB RAM)
- `mistral` - 7B model, good balance (~4GB RAM)
- `llama2` - Official Llama 2 (7B parameters, ~4GB RAM)
- `neural-chat` - Optimized for chat (7B parameters, ~4GB RAM)
- `dolphin-mixtral` - Powerful mixture of experts (~13GB RAM)

### Chunk Size and Overlap

Adjust text splitting behavior:
```python
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=400,      # Size of each chunk (increase for longer documents)
    chunk_overlap=50,    # Overlap between chunks (10-20% of chunk_size recommended)
    separators=["\n\n", "\n", " ", ""]
)
```

### Retrieval Parameters

Modify how many documents are retrieved per query:
```python
retriever = vector_store.as_retriever(
    search_type="similarity",
    search_kwargs={"k": 3}  # Number of documents to retrieve
)
```

## Project Structure

```
RAG/
├── rag_with_ollama.ipynb    # Main notebook with full pipeline
├── readme.md                 # This file
├── .gitignore               # Git ignore rules
├── pyproject.toml           # Project metadata
├── faiss_db/                # FAISS vector store (created after running)
│   └── index.faiss
└── build/                   # Build artifacts
```

## How It Works

### 1. Document Loading
PDF documents are loaded using `PyPDFLoader` and converted to text.

### 2. Text Chunking
Documents are split into overlapping chunks using `RecursiveCharacterTextSplitter` for better context preservation.

### 3. Embeddings
Each chunk is converted to embeddings using `OllamaEmbeddings` with the `all-minilm` model.

### 4. Vector Store Creation
Embeddings are stored in FAISS for efficient similarity search.

### 5. Retrieval
For each query:
- The query is embedded
- Top-K most similar document chunks are retrieved
- Retrieved context is passed to the LLM

### 6. Response Generation
The LLM generates an answer using the prompt template with retrieved context.

## Performance Tips

- **Large Documents**: Increase `chunk_size` to 800-1000 for better context
- **Fine-Grained Search**: Decrease `chunk_size` to 200-300 for more specific results
- **Memory Management**: Use lighter models (`llama3.2:1b`) if RAM is limited
- **GPU Acceleration**: Install `faiss-gpu` for faster similarity search with NVIDIA GPU
- **Persistent Indices**: Indices saved in `faiss_db/` are reloaded quickly without recomputation

## Troubleshooting

### Model Memory Error
If you get "model requires more system memory than available":
- Switch to a smaller model: `mistral` or `llama3.2:1b`
- Close other applications to free up RAM

### Ollama Connection Error
Ensure Ollama is running:
```bash
ollama serve
```

### Missing Dependencies
Reinstall all requirements:
```bash
pip install --upgrade -r requirements.txt
```

## References

- [LangChain Documentation](https://python.langchain.com/)
- [Ollama GitHub](https://github.com/ollama/ollama)
- [FAISS Wiki](https://github.com/facebookresearch/faiss/wiki)
- [LangChain + Ollama](https://python.langchain.com/docs/integrations/llms/ollama)

## License

This project is provided as-is for educational and personal use.