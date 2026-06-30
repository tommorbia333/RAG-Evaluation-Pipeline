# RAG System — Judgment & Decision-Making Corpus

A retrieval-augmented generation pipeline built from scratch over a corpus of ~30–50 Wikipedia articles on cognitive biases and judgment & decision-making.

No orchestration frameworks (LangChain, LlamaIndex, etc.). Every component — chunking, embedding, retrieval, generation, evaluation — is implemented explicitly so I can reason about and defend each piece.

## Why this project

I wanted a portfolio piece where I could point at any layer of the RAG stack and explain exactly what it does, why I made that design choice, and what the alternatives were. Using a framework would hide too much.

The JDM/cognitive-bias domain was chosen deliberately: lots of confusable concepts (anchoring vs. framing, availability vs. representativeness) that stress retrieval quality and make error analysis interesting.

## Architecture

```
Documents → Chunker → Embedder → Vector Store → Retriever → Generator → Answer
                                                      ↑
                                                  Eval harness
```

## Phases

| Phase | Focus | Status |
|---|---|---|
| Week 1 | Minimal end-to-end slice: ingest → embed → dense retrieve → generate → basic eval | In progress |
| Week 2 | Postgres + pgvector, hybrid retrieval (dense + BM25), full eval harness with LLM-as-judge | Upcoming |
| Week 3 | UI, cost/latency analysis, error analysis, final polish | Upcoming |

## Stack

- **Python 3.11**, managed with `uv`
- **Embeddings:** `BAAI/bge-small-en-v1.5` (local, cached)
- **Retrieval:** brute-force cosine (wk1) → Postgres + pgvector + FTS hybrid (wk2)
- **Generator:** Claude Haiku 4.5
- **Eval judge:** Claude Sonnet 4.6
- **Linting:** ruff &middot; **Testing:** pytest

## Quickstart

```bash
make install               # create venv + editable install
cp .env.example .env       # add your ANTHROPIC_API_KEY
make test                  # run the test suite
make index                 # ingest → chunk → embed → persist
make ask                   # query the pipeline end-to-end
```

## Project structure

```
src/rag/
  ingest/      # document loading + chunking
  embed/       # embedding with bge-small
  retrieve/    # dense (wk1), hybrid (wk2)
  generate/    # Claude API client + prompt building
  eval/        # retrieval metrics + LLM judge
scripts/       # CLI entry points
data/          # corpus + gold Q/A set
tests/         # pytest suite
```
