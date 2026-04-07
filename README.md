# Hybrid Retriever (BM25 + Embeddings)

## Overview
This project implements a hybrid retrieval system that combines:
- BM25 (keyword-based retrieval)
- Dense embeddings (semantic retrieval)

The system is evaluated on both **golden** and **adversarial** datasets to analyze performance across direct and complex query types.

---

## Data Preparation

### Data Processing
- Loaded source documents
- Performed text cleaning and normalization
- Prepared data for retrieval

### Chunking Strategy
Documents were split using `RecursiveCharacterTextSplitter` with the following configuration:
- Chunk size: 350 characters
- Chunk overlap: 50 characters
- Separators: ["\n\n", "\n", ".", "?", "!", " ", ""]

This recursive strategy prioritizes preserving larger semantic units (such as paragraphs or dialogue blocks) before splitting into smaller chunks.

Since the dataset is dialogue-heavy, additional separators were included to better align chunks with conversational structure.  
The overlap helps retain context across adjacent chunks, which is important when information spans multiple dialogue turns.

---

## Approach
- BM25 is used for exact keyword matching
- Dense embeddings are used for semantic similarity
- Final retrieval score is a weighted combination of both (controlled by `alpha`)
- A reranker is applied to refine the retrieved results

---

## Evaluation Setup
The system is evaluated using:
- Precision@k
- Recall@k

Experiments were conducted by varying:
- `alpha` (balance between BM25 and embeddings)
- `candidate_k` (initial retrieval size)
- `top_k` (final number of retrieved results)

---

## Key Observations

1. The golden dataset allows higher recall due to direct and well-aligned queries, whereas adversarial queries are more challenging due to paraphrasing, ambiguity, reduced lexical overlap, and embedding mismatch.

2. Increasing `candidate_k` improves recall but introduces noise, which reduces precision.

3. Smaller `candidate_k` and `top_k` values are sufficient for direct queries and help reduce computational overhead.

4. The reranker may reduce recall but improves precision by selecting more relevant documents from a smaller set. This trade-off is beneficial compared to relying on a large number of retrieved documents.

5. As the number of retrieved documents increases, large language models are more susceptible to the “needle in the haystack” problem, which negatively impacts response quality.

6. Conversational data requires balancing semantic and keyword-based retrieval due to its structure (events, dialogues, and character references).

---

## Dataset
- **Golden set**: Direct queries with clear relevance
- **Adversarial set**: Complex and paraphrased queries
- **Base dataset**: Source documents used for retrieval

---

## Dependencies

 Install dependencies:
```bash
pip install -r requirements.txt
