
```markdown
# RAG Evaluation & Retrieval Optimization

> 🚧 **Status: In Progress — Core Architecture Defined, Implementation Under Active Development**

A production-oriented Retrieval-Augmented Generation (RAG) system focused on **retrieval quality, hybrid search, reranking, grounding, and quantitative evaluation** rather than building a simple chatbot.

The project is designed around an important principle:

> **RAG quality should be measured, not assumed.**

---

## 🚀 Project Overview

Most basic RAG applications follow:

```text
Documents → Embeddings → Vector Search → Top-K → LLM → Answer
```

This project goes beyond the basic pipeline by investigating **how retrieval quality can be improved and measured systematically**.

The planned system compares multiple retrieval strategies:

```text
Dense Retrieval
      │
      ├──────────────┐
      │              │
      ▼              ▼
Semantic Search   Sparse Search
                     │
                     ▼
              Hybrid Retrieval
                     │
                     ▼
             Reciprocal Rank Fusion
                     │
                     ▼
                 Reranking
                     │
                     ▼
             Context Construction
                     │
                     ▼
                    LLM
                     │
                     ▼
             Grounding Verification
                     │
                     ▼
                 Evaluation
```

The project is being implemented incrementally, with each stage tested before integrating it into the complete pipeline.

---

# 🎯 Objectives

The primary objectives are:

- Build a complete RAG pipeline from the ground up.
- Understand document ingestion and chunking strategies.
- Generate and manage semantic embeddings.
- Implement dense vector retrieval.
- Implement sparse / keyword retrieval.
- Combine dense and sparse retrieval.
- Implement Reciprocal Rank Fusion (RRF).
- Introduce cross-encoder reranking.
- Build a controlled evaluation dataset.
- Quantitatively evaluate retrieval quality.
- Compare different retrieval configurations.
- Analyze retrieval failure cases.
- Verify whether generated answers are grounded in retrieved context.
- Build a reproducible evaluation workflow.
- Expose the final pipeline through an API.

---

# 🏗️ System Architecture

The target architecture is:

```text
                         ┌─────────────────┐
                         │    Documents    │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │ Document Loader │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │    Chunking     │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │   Embeddings    │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │  Vector Index   │
                         └────────┬────────┘
                                  │
                 ┌────────────────┴────────────────┐
                 │                                 │
                 ▼                                 ▼
        ┌─────────────────┐               ┌─────────────────┐
        │ Dense Retrieval │               │ Sparse Retrieval│
        └────────┬────────┘               └────────┬────────┘
                 │                                 │
                 └────────────────┬────────────────┘
                                  ▼
                         ┌─────────────────┐
                         │ Hybrid Retrieval│
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │ RRF / Rank      │
                         │ Fusion           │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │   Reranking     │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │ Context Builder │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │      LLM        │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │ Answer + Sources│
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │ Grounding /     │
                         │ Verification    │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │ Evaluation      │
                         │ Harness         │
                         └─────────────────┘
```

---

# 🧠 Core Engineering Areas

## 1. Document Ingestion

Documents are transformed into structured internal representations before entering the retrieval system.

Planned responsibilities:

- Document loading
- Metadata extraction
- Document normalization
- Chunk generation
- Chunk metadata management

---

## 2. Chunking

Chunking is treated as an important retrieval component rather than simply splitting text into fixed-size pieces.

The system will support experimentation with different chunking strategies and their effect on retrieval quality.

```text
Document
   ↓
Chunking Strategy
   ↓
Chunks + Metadata
   ↓
Embedding / Indexing
```

---

## 3. Dense Retrieval

Dense retrieval represents documents and queries as vectors.

```text
Query
  ↓
Embedding Model
  ↓
Vector
  ↓
Vector Database
  ↓
Top-K Similar Documents
```

This allows semantically related information to be retrieved even when the exact keywords differ.

---

## 4. Sparse Retrieval

Sparse retrieval provides lexical matching and is particularly useful for:

- Exact terminology
- Names
- Technical keywords
- Identifiers
- Domain-specific phrases

The planned implementation uses BM25-style retrieval.

---

## 5. Hybrid Retrieval

Dense and sparse retrieval have different strengths.

The system therefore combines:

```text
Semantic Retrieval
        +
Lexical Retrieval
        ↓
Hybrid Retrieval
```

The objective is to determine whether combining both approaches provides more robust retrieval.

---

# 🔀 Reciprocal Rank Fusion

Multiple retrieval systems produce independent rankings.

The project uses **Reciprocal Rank Fusion (RRF)** as the planned mechanism for combining those rankings.

```text
Dense Ranking
      │
      ├───────┐
      │       │
      ▼       ▼
              RRF
      ▲       ▲
      │       │
Sparse Ranking
              │
              ▼
       Combined Ranking
```

This allows retrieval strategies to contribute complementary evidence to the final ranking.

---

# 🎯 Reranking

Initial retrieval produces a candidate set.

A reranker is then used to refine the ordering of those candidates.

```text
Query
  ↓
Hybrid Retrieval
  ↓
Candidate Documents
  ↓
Cross Encoder
  ↓
Re-ranked Documents
```

This separates:

**candidate retrieval**

from:

**fine-grained relevance ranking**

---

# 📊 Retrieval Evaluation

A major focus of the project is quantitative evaluation.

Instead of evaluating a RAG system only by looking at generated answers, the retrieval layer itself will be measured.

The evaluation framework will compare:

```text
Dense
Sparse
Hybrid
Hybrid + Reranker
```

under the same evaluation dataset.

---

## Evaluation Metrics

The project will evaluate metrics including:

### Recall@K

Measures whether relevant documents are retrieved within the top K results.

### MRR

Mean Reciprocal Rank measures how high the first relevant result appears.

### nDCG@K

Normalized Discounted Cumulative Gain evaluates the quality of the ranking while giving greater importance to highly ranked relevant results.

---

# 🧪 Golden Evaluation Dataset

A controlled evaluation dataset will contain queries and expected relevant documents/chunks.

Example:

```json
{
  "query": "What is the speed limit on this road?",
  "relevant_documents": [
    "speed-limits.md"
  ]
}
```

The same queries will be executed against different retrieval configurations.

This makes comparisons reproducible and prevents evaluation from depending only on subjective examples.

---

# 🔬 Evaluation Harness

The planned evaluation harness will automate experiments:

```text
                Golden Dataset
                       │
                       ▼
              ┌─────────────────┐
              │ Evaluation      │
              │ Harness         │
              └────────┬────────┘
                       │
       ┌───────────────┼───────────────┐
       ▼               ▼               ▼
     Dense           Sparse          Hybrid
       │               │               │
       └───────────────┼───────────────┘
                       ▼
                  Reranking
                       │
                       ▼
                   Metrics
                       │
                       ▼
              Comparison Report
```

The eventual goal is to answer questions such as:

- Which retrieval strategy performs best?
- Where does dense retrieval fail?
- Where does sparse retrieval outperform semantic search?
- Does hybrid retrieval improve recall?
- Does reranking improve ranking quality?
- Which queries remain difficult?
- What trade-offs exist between retrieval quality and computational cost?

---

# 🧾 Grounding & Citation Verification

Retrieval quality and answer quality are separate problems.

The project therefore includes a planned grounding layer:

```text
Retrieved Context
       ↓
      LLM
       ↓
Generated Answer
       ↓
Citation / Grounding Verification
       ↓
Supported / Unsupported Claims
```

This allows the system to investigate whether generated claims are actually supported by retrieved evidence.

---

# 🤖 RAG Generation

Once the retrieval pipeline is established, retrieved documents will be passed to an LLM with structured context.

The generation stage will focus on:

- Context-aware prompting
- Source-aware responses
- Citation generation
- Reducing unsupported claims
- Controlled context construction

---

# 🌐 API Layer

The final system is planned to expose the RAG pipeline through a FastAPI service.

Planned flow:

```text
Client
  ↓
FastAPI
  ↓
RAG Service
  ↓
Retrieval
  ↓
Reranking
  ↓
Generation
  ↓
Verification
  ↓
Response
```

---

# 🧪 Testing Strategy

Testing will be introduced alongside implementation rather than at the end.

Planned testing levels:

```text
Unit Tests
    ↓
Component Tests
    ↓
Integration Tests
    ↓
Retrieval Evaluation
    ↓
End-to-End RAG Tests
```

The goal is to keep individual components independently testable before connecting the complete pipeline.

---

# 🛠️ Technology Stack

| Layer | Technology |
|---|---|
| Language | Python |
| RAG | Retrieval-Augmented Generation |
| Embeddings | Sentence Transformers |
| Vector Database | Qdrant |
| Sparse Retrieval | BM25 |
| Hybrid Search | Dense + Sparse |
| Rank Fusion | Reciprocal Rank Fusion |
| Reranking | Cross-Encoder |
| API | FastAPI |
| Testing | Pytest |
| Containerization | Docker |
| CI | GitHub Actions |
| Version Control | Git / GitHub |

Technologies may evolve during implementation and experimentation.

---

# 📁 Planned Project Structure

```text
rag-evaluation-harness/
│
├── README.md
├── pyproject.toml
├── .gitignore
├── .env.example
│
├── data/
│   ├── sample/
│   └── eval/
│
├── docs/
│   ├── architecture.md
│   └── experiments/
│
├── src/
│   └── rag/
│       │
│       ├── ingestion/
│       │   ├── models.py
│       │   ├── loaders.py
│       │   └── chunking.py
│       │
│       ├── indexing/
│       │   ├── embeddings.py
│       │   └── vector_store.py
│       │
│       ├── retrieval/
│       │   ├── dense.py
│       │   ├── sparse.py
│       │   ├── hybrid.py
│       │   ├── fusion.py
│       │   └── rerank.py
│       │
│       ├── generation/
│       │   ├── llm.py
│       │   ├── prompts.py
│       │   └── generate.py
│       │
│       ├── verification/
│       │   └── citations.py
│       │
│       └── eval/
│           ├── models.py
│           ├── golden.py
│           ├── metrics.py
│           └── harness.py
│
└── tests/
```

---

# 📈 Development Roadmap

The project is being developed in incremental milestones.

### 🟢 Architecture & Design

- [x] Define RAG architecture
- [x] Define retrieval strategies
- [x] Define evaluation strategy
- [x] Define project structure
- [x] Define technology stack
- [x] Define experimental methodology

### 🟡 Core RAG Pipeline

- [ ] Document models
- [ ] Document loading
- [ ] Chunking
- [ ] Embeddings
- [ ] Vector indexing
- [ ] Dense retrieval
- [ ] Sparse retrieval
- [ ] Hybrid retrieval
- [ ] RRF
- [ ] Reranking

### 🟡 Generation & Grounding

- [ ] LLM integration
- [ ] Context construction
- [ ] Prompt engineering
- [ ] Citation generation
- [ ] Grounding verification

### 🟡 Evaluation

- [ ] Golden dataset
- [ ] Recall@K
- [ ] MRR
- [ ] nDCG
- [ ] Retrieval comparison
- [ ] Failure analysis
- [ ] Evaluation harness

### ⚪ Engineering

- [ ] FastAPI
- [ ] Automated testing
- [ ] Docker
- [ ] GitHub Actions
- [ ] Reproducible experiments
- [ ] Performance analysis
- [ ] Documentation

---

# 🧭 Current Development Status

### Overall Project Scope

```text
Architecture & Design       ████████████████████ Defined
Core RAG Pipeline           ███████░░░░░░░░░░░░░ In Progress
Evaluation Framework       ██████░░░░░░░░░░░░░░ In Progress
Production Engineering     ████░░░░░░░░░░░░░░░░ Planned
```

> **Current status:** The majority of the system architecture, component boundaries, retrieval strategies, evaluation methodology, and implementation roadmap have been defined. The remaining work is being implemented incrementally and validated component-by-component.

---

# 💡 Project Philosophy

The central idea behind this project is:

> **Don't just build a RAG application. Build a RAG system that can explain how well it retrieves information.**

A good-looking answer does not necessarily mean:

```text
Good Retrieval
      ≠
Good Generation
      ≠
Grounded Answer
```

These properties need to be evaluated independently.

---

# 📚 Learning Focus

This project is being developed to gain practical experience with:

- Retrieval-Augmented Generation
- Information Retrieval
- Semantic Search
- Dense Retrieval
- Sparse Retrieval
- BM25
- Embeddings
- Vector Databases
- Hybrid Search
- Reciprocal Rank Fusion
- Cross-Encoder Reranking
- Retrieval Evaluation
- LLM Evaluation
- Grounding
- Citation Verification
- FastAPI
- Docker
- Automated Testing
- Reproducible Experiments

---

# 🚧 Status

**In Progress**

The architecture and majority of the planned feature scope have been defined.

Implementation is proceeding incrementally, with each module being understood, implemented, tested, and integrated before moving to the next stage.

---

## 👨‍💻 Author

**Sanjay Kumar Pandit**

Computer Science & Engineering  
IIT Bhubaneswar

---

## 📄 License

MIT License
```

