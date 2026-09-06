---
name: rag-systems
group: Retrieval
description: >-
  Build retrieval that works: chunking strategy, embeddings, hybrid search, reranking and
  retrieval evals. Use when building RAG pipelines, chunking, embeddings, or vector search.
---

# rag-systems

## Core Philosophy
Retrieval-Augmented Generation (RAG) is not simply splitting a PDF into 500-character chunks, embedding them with OpenAI, and dumping them into a vector database. Naive RAG produces fragmented context, keyword mismatch, and hallucinated answers. Production-grade RAG is a precision information retrieval pipeline that combines semantic chunking, hybrid search (dense embeddings + sparse BM25), cross-encoder reranking, and rigorous automated evaluation using the RAG Triad.

---

## 4-Step Production RAG Engineering Pipeline

### Step 1: Chunking Strategy & Document Decomposition
1. **Avoid Arbitrary Fixed-Length Splitting**:
   - Fixed character counts cut mid-sentence, slicing critical context in half.
2. **High-Precision Chunking Archetypes**:
   - *Semantic / Markdown Chunking*: Split strictly along document boundaries (`#`, `##`, `###`, code blocks).
   - *Recursive Character Chunking with Overlap*: Split by paragraph (`

`), then sentence (`
`), with 15–20% token overlap to preserve transitional context.
   - *Parent-Document / Small-to-Big Retrieval*: Embed small chunks (128 tokens) for precise vector matching, but return the larger parent chunk (1,024 tokens) to the LLM context for generation.

### Step 2: Hybrid Retrieval (Dense Vector + Sparse BM25)
1. **The Vector Blindspot**:
   - Dense embeddings excel at conceptual meaning, but fail completely on exact keywords, part numbers, error codes, and acronyms (e.g. `CVE-2024-38077` or `AWS-ERR-403`).
2. **Hybrid Search Architecture**:
   - Query both a dense vector index (e.g. `text-embedding-3-large` or `bge-large-en`) and a sparse keyword index (BM25 in OpenSearch / Elasticsearch).
3. **Reciprocal Rank Fusion (RRF)**:
   - Combine dense and sparse results using RRF:
     $$RRF(d) = \sum_{m \in M} rac{1}{k + r_m(d)}$$
     - Where $k pprox 60$, and $r_m(d)$ is the rank of document $d$ in retrieval system $m$.

### Step 3: Cross-Encoder Reranking
1. **The Retrieval-to-Context Funnel**:
   - Retrieve top **50 candidates** via hybrid search.
   - Pass candidates through a specialized **Cross-Encoder Reranker** (e.g. Cohere Rerank v3 or `bge-reranker-large`).
   - Select strictly the top **3–5 highest-scoring passages** to feed into LLM prompt context.
2. **Context Window Placement**:
   - Place the most critical retrieved passages at the very beginning and very end of the context window to combat the "Lost in the Middle" LLM attention phenomenon.

### Step 4: The RAG Triad Evaluation & Continuous Benchmarking
1. **The 3 RAG Metrics (Ragas Framework)**:
   - *Context Relevance*: Are the retrieved chunks strictly relevant to the query without extraneous noise?
   - *Groundedness (Faithfulness)*: Is the LLM's answer mathematically derivable *exclusively* from the retrieved context without hallucination?
   - *Answer Relevance*: Does the generated response directly answer the user's initial question?
2. **Target Benchmark**:
   - Maintain Groundedness $\ge 95\%$ and Context Relevance $\ge 85\%$ across your production golden evaluation dataset.

---

## Deliverable Format: RAG Architecture Specification (`RAG-PIPELINE-SPEC.md`)

```markdown
# RAG System Architecture Specification: [Knowledge Base Name]

## 1. Document Ingestion & Chunking
- **Source Formats**: Markdown documentation, OpenAPI specs, PDF whitepapers
- **Chunking Strategy**: Markdown header-aware chunking (Max 512 tokens, 64 token overlap)
- **Parent-Document Retrieval**: Enabled (Parent chunk size: 2,048 tokens)

## 2. Embedding & Retrieval Topology
- **Embedding Model**: `text-embedding-3-large` (1536 dimensions)
- **Vector Database**: Qdrant / pgvector
- **Sparse Index**: BM25 enabled on text payload
- **Fusion Method**: Reciprocal Rank Fusion (RRF, k=60)

## 3. Reranking & Context Assembly
- **Reranker Model**: `cohere-rerank-v3` / `bge-reranker-large`
- **Funnel Mechanics**: Top 50 retrieved -> Reranked -> Top 5 injected into LLM
- **Attention Optimization**: Interleaved context ordering (Best passages first and last)

## 4. Evaluation Benchmarks (RAG Triad)
- **Evaluation Dataset**: 100 domain Q&A pairs
- **Current Groundedness Score**: 96.2%
- **Current Context Relevance**: 88.4%
- **Current Answer Relevance**: 94.1%
```

---

## Worked Example: Developer Documentation RAG

- **Problem**: Engineers asked for specific API endpoint parameters; standard vector search retrieved broad tutorial text instead of the parameter table.
- **Solution**: Implemented BM25 hybrid search with parent-document retrieval and Cohere Rerank.
- **Outcome**: Parameter accuracy jumped from 61% to 98%; developer support inquiries for documentation dropped 34%.

---

## Verification Checklist

- [ ] Chunking respects semantic boundaries (headers/paragraphs) rather than arbitrary character splits.
- [ ] Hybrid retrieval combines dense vector embeddings with sparse BM25 search.
- [ ] Cross-encoder reranker filters the top candidate pool down to the top 3–5 passages.
- [ ] System evaluated using the RAG Triad (Context Relevance, Groundedness, Answer Relevance).
- [ ] Prompt explicitly instructs the model to cite sources and refuse to answer if context is absent.

---

## Anti-Patterns

- **Vector-Only Naivete**: Expecting vector embeddings to match exact error codes or SKU strings without BM25.
- **Context Stuffing**: Dumping 40 raw retrieved chunks into the prompt, degrading LLM reasoning and ballooning costs.
- **Zero Hallucination Audits**: Shipping RAG into production without measuring groundedness against a golden test suite.
