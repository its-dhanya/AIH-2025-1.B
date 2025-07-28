# Document Analysis Pipeline

This project provides an intelligent pipeline for extracting, analyzing, and summarizing information from PDF documents based on a given task. It leverages advanced NLP models, including Sentence Transformers for semantic search and Ollama for intelligent text formatting and keyword generation.

## Features

- **Smart PDF Text Extraction**: Utilizes PyMuPDF with enhanced title detection and structural analysis.
- **Domain-Aware Processing**: Identifies document domains (e.g., academic, technical, legal, recipe) to refine extraction and formatting.
- **Intelligent Information Retrieval**: Uses Sentence Transformers for semantic search and a large language model (Ollama) for dynamic keyword generation to find the most relevant document sections.
- **LLM-Powered Formatting**: Formats extracted text into clean, structured prose tailored to the document's detected domain.
- **Structured Output**: Generates a JSON report with extracted sections, refined text, and domain analysis.

## Prerequisites

Before you begin, ensure you have the following installed on your system:

### Docker Desktop
- Download Docker Desktop for your operating system (Windows, macOS).
- Ensure Docker Desktop is running and configured with sufficient resources (e.g., at least 4GB RAM, and enough disk space for images and containers).

### Ollama
- This project uses an external Ollama server for its Large Language Model (LLM) capabilities. The Ollama server and its models are NOT bundled within this Docker image due to size constraints.

**Installation**: Download and install Ollama on your host machine: https://ollama.com/download

**Model Download**: After installing Ollama, pull the stablelm2 model:
```bash
ollama pull stablelm2
```

**Verify Ollama**: Ensure the Ollama server is running (it usually starts automatically after installation) and the stablelm2 model is available. By default, Ollama runs on `http://localhost:11434`.

## Project Structure

Your project directory should be structured as follows:

```
.
├── app/
│   ├── input/
│   │   ├── Collection 1/
│   │   │   ├── collection_1_metadata.json
│   │   │   └── pdfs/
│   │   │       └── document_a.pdf
│   │   ├── Collection 2/
│   │   │   ├── collection_2_metadata.json
│   │   │   └── pdfs/
│   │   │       └── challenge1b_input.pdf
│   │   └── ... (other collections)
│   └── output/
│       └── ... (generated analysis files will appear here)
├── Dockerfile
├── main.py
├── requirements.txt
└── (other local development files like venv, model_cache etc. can exist but are ignored by Docker)
```

- `app/input/`: Contains subdirectories for each "collection". Each collection subdirectory should contain:
  - A metadata JSON file (e.g., `collection_1_metadata.json`) defining the `job_to_be_done` and documents.
  - A `pdfs/` subdirectory containing the PDF files referenced in the metadata.

- `app/output/`: This directory will be created by the pipeline, and the generated JSON analysis reports will be saved here.

- `Dockerfile`: Defines the Docker image build process.
- `main.py`: The main Python script containing the document analysis pipeline logic.
- `requirements.txt`: Lists all Python dependencies.

## Setup and Execution

### 1) Build Docker Image
```bash
sudo docker build -t my-python-app .
```

### 2) Create Docker Network
```bash
sudo docker network create ollama-net
```

### 3) Run the Docker Container
```bash
sudo docker run -it --rm --network ollama-net \
  -e OLLAMA_HOST=http://ollama:11434 \
  -v ~/Desktop/Adobe/AIH-2025-1.B/app/input:/app/input \
  -v ~/Desktop/Adobe/AIH-2025-1.B/app/output:/app/output \
  my-python-app
```

### 4) View Results
After execution, the generated JSON analysis reports (e.g., `Collection_1_analysis.json`) will be in your local `app/output/` directory.

## Troubleshooting

### Input/output or disk space issues during build
- Ensure sufficient disk space (recommended: 15–20 GB).
- Run `docker builder prune` to clean up unused data.
- If persistent, consider resetting Docker Desktop.

### 404 Not Found for Debian packages
- Make sure you're using a current base image (e.g., `python:3.9-slim-bullseye`).

### Dockerfile parse errors or unknown instructions
- Check for typos, incorrect line continuations (`\`), or misused inline comments.

### Ollama connection issues
- Ensure the Ollama server is running.
- Verify `stablelm2` is pulled (`ollama list`).
- Check that `localhost:11434` is reachable and not blocked by a firewall.
