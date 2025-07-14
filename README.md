# RAG Question Answering System

**Author:** Manav Patel (500967756)  
**Course:** DS8008 Final Project

## Overview

A Retrieval-Augmented Generation (RAG) system that answers questions from a large document corpus using semantic search and neural question answering.

## Architecture

1. **Document Processing**: Text chunking with sentence overlap
2. **Embedding**: SentenceTransformer (all-MiniLM-L6-v2) creates 384-dim vectors  
3. **Indexing**: FAISS vector database for fast similarity search
4. **QA**: RoBERTa model extracts answers from retrieved chunks

## Key Results

- **F1 Score**: 0.4578 average token-level F1
- **Corpus Size**: 91,561 processed document chunks
- **Retrieval**: Top-50 chunks per query for comprehensive coverage
- **Performance**: Efficient batch processing with memory optimization

## Project Structure

```
├── data/
│   ├── document_corpus.txt              # Source documents
│   ├── evaluation_questions.txt         # Test questions  
│   └── Store_Sales_Price_Elasticity_Promotions_Data.parquet
├── notebooks/
│   ├── main_rag_system.ipynb           # Complete implementation
│   ├── rag_qa_system.ipynb             # Alternative implementation
│   └── tool_examples.ipynb
├── docs/
│   ├── implementation.md               # Step-by-step process
│   ├── results.md                      # Performance analysis
│   └── architecture.md                 # System design
└── src/                                # Future modular code
```

## Technical Stack

- **Python**: Core implementation
- **FAISS**: Vector similarity search
- **SentenceTransformers**: Text embeddings
- **HuggingFace Transformers**: QA models
- **NLTK**: Text preprocessing

## Quick Start

1. Install dependencies: `pip install faiss-cpu sentence-transformers transformers nltk pandas`
2. Run `notebooks/main_rag_system.ipynb` end-to-end
3. View results in generated CSV output

## Implementation Highlights

- **Chunking Strategy**: 150-word chunks with 1-sentence overlap preserves context
- **Batch Processing**: 512-chunk batches for memory efficiency  
- **Exact Search**: FAISS IndexFlatL2 for accurate retrieval
- **Evaluation**: Token-F1 scoring against retrieved context

This system demonstrates practical RAG implementation for document-based question answering with strong performance on diverse query types.
