# System Architecture

## Overview

The RAG-based Question Answering system follows a pipeline architecture with four main components:

1. **Document Processing Layer**
2. **Embedding and Indexing Layer** 
3. **Retrieval Layer**
4. **Question Answering Layer**

## Architecture Diagram

```text
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Raw Documents │───▶│  Text Processing │───▶│     Chunking    │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                                        │
                                                        ▼
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│  User Question  │    │   SentenceXform  │◀───│   Text Chunks   │
└─────────────────┘    └──────────────────┘    └─────────────────┘
        │                       │                       │
        ▼                       ▼                       ▼
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│ Question Vector │    │  Document Embeds │───▶│  FAISS Index    │
└─────────────────┘    └──────────────────┘    └─────────────────┘
        │                                               │
        └───────────────────┐                          │
                            ▼                          ▼
                    ┌──────────────────┐    ┌─────────────────┐
                    │  Similarity      │◀───│  Top-K Chunks   │
                    │  Search          │    │  Retrieval      │
                    └──────────────────┘    └─────────────────┘
                            │
                            ▼
                    ┌──────────────────┐    ┌─────────────────┐
                    │  RoBERTa QA      │───▶│  Final Answer   │
                    │  Model           │    │  + Confidence   │
                    └──────────────────┘    └─────────────────┘
```

## Component Details

### 1. Document Processing Layer

**Input:** Raw text documents
**Output:** Normalized text ready for chunking

**Process:**
- Text normalization (lowercase, whitespace cleanup)
- Sentence tokenization using NLTK
- Text validation and cleaning

### 2. Embedding and Indexing Layer

**Components:**
- **SentenceTransformer Model:** all-MiniLM-L6-v2 (384-dim vectors)
- **Chunking Strategy:** 150-word chunks with 1-sentence overlap
- **FAISS Index:** IndexFlatL2 for exact L2 distance search

**Process:**
- Split text into overlapping chunks
- Generate embeddings in batches (512 chunks/batch)
- Build and persist FAISS index

### 3. Retrieval Layer

**Input:** User question string
**Output:** Top-k relevant document chunks

**Process:**
- Encode question using same SentenceTransformer
- Perform similarity search in FAISS index
- Return ranked chunks with similarity scores

### 4. Question Answering Layer

**Input:** Question + retrieved chunks
**Output:** Extracted answer with confidence score

**Process:**
- Run RoBERTa QA model on each chunk
- Aggregate confidence scores
- Select best answer based on QA confidence

## Data Flow

1. **Preprocessing:** Documents → Chunks (91,561 chunks generated)
2. **Embedding:** Chunks → 384-dim vectors → FAISS index
3. **Query Time:** Question → Vector → Top-50 chunks → QA processing
4. **Output:** Best answer + metadata (confidence, source chunk, F1 score)

## Performance Characteristics

- **Index Size:** 91,561 vectors × 384 dimensions
- **Retrieval Speed:** Sub-second for top-50 chunks
- **Memory Usage:** Optimized with batch processing
- **Accuracy:** Average F1 score of 0.4578

## Technology Stack

- **Language:** Python
- **ML Framework:** HuggingFace Transformers
- **Vector Search:** FAISS (Facebook AI Similarity Search)
- **Embeddings:** SentenceTransformers
- **Text Processing:** NLTK
- **Data Handling:** Pandas, NumPy

## Scalability Considerations

- **Horizontal Scaling:** FAISS supports distributed indexing
- **Memory Optimization:** Batch processing prevents OOM errors
- **Storage:** Persistent index and embeddings for quick startup
- **Caching:** Pre-computed embeddings reduce inference time
