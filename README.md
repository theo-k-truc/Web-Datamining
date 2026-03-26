# Web Mining & Semantics — Project

**Pipeline complet : Web Crawling → Knowledge Graph → KGE → RAG**

Domain: Artificial Intelligence

Théo Karaoulanis & Achille Martin

---

## Project Overview

This project implements a full knowledge engineering pipeline across 4 lab sessions:

1. **TD1** — Web crawling and information extraction (NER, dependency parsing)
2. **TD4** — RDF Knowledge Graph construction, Wikidata alignment and SPARQL expansion
3. **TD5** — SWRL reasoning and Knowledge Graph Embedding (TransE, ComplEx)
4. **TD6** — Retrieval-Augmented Generation (RAG) with a local LLM via SPARQL

Video link : https://drive.google.com/file/d/1x3S28HmmgNTuh9DP4Erh4GlaMIGF4ZH5/view?usp=sharing 

---

## Requirements

### Python
- Python 3.9 or higher

### Python packages
```bash
pip install trafilatura spacy pandas httpx rdflib SPARQLWrapper pykeen owlready2 requests matplotlib scikit-learn
python -m spacy download en_core_web_trf
```

### Ollama (required for TD4 — RAG)
1. Download and install from: https://ollama.com/download/windows
2. Pull the model:
```bash
ollama pull gemma:2b
```
3. Start the server (keep this terminal open):
```bash
ollama serve
```
4. Verify it is running:
```bash
curl http://localhost:11434
# Expected output: Ollama is running
```

---

## Repository Structure

```
project/
├── Projet_Web_Mining_Complet_Final.ipynb   # Main notebook (all 4 sessions)
├── expanded_knowledge_graph.ttl            # Final RDF Knowledge Graph (generated)
├── family.owl                              # SWRL ontology (generated)
├── crawler_output.jsonl                    # Crawled text and metadata (generated)
├── extracted_knowledge.csv                 # Extracted entity relations (generated)
├── train.txt                               # KGE training split (generated)
├── valid.txt                               # KGE validation split (generated)
├── test.txt                                # KGE test split (generated)
├── README.md                               # This file
└── rapport_web_mining.docx                 # Full project report
```

> **Note:** All generated files are produced by running the notebook in order.  
> They are not included in the repository due to their size.

---

## How to Run

### Option A — Full pipeline (all sessions from scratch)

Open `Projet_Web_Mining_Complet_Final.ipynb` in Jupyter and run all cells **in order** from top to bottom.

> ⚠️ The crawling and Wikidata expansion steps (cells 5 and 15) require an active internet connection and may take **30–60 minutes** to complete due to API rate limits.

### Option B — RAG only (TD4, requires generated files)

Make sure `expanded_knowledge_graph.ttl` exists in the working directory, then:

1. Start Ollama: `ollama serve`
2. Open the notebook
3. Run **only** the following cells in order:

| Cell | Description |
|------|-------------|
| Cell 1–3 | Install dependencies and imports |
| Cell 40 | RAG configuration |
| Cell 41 | Load RDF graph |
| Cell 42 | Test Ollama connection |
| Cell 43 | Build schema summary |
| Cell 44 | SPARQL generation functions |
| Cell 45 | Execution + self-repair functions |
| Cell 46 | Baseline LLM function |
| Cell 47 | 5-question evaluation |
| Cell 48 | Discussion |

---

## Key Results

| Metric | Value |
|--------|-------|
| Pages crawled | 14 |
| Relations extracted | 889 |
| Initial RDF triplets | 1,968 |
| Entities aligned to Wikidata | 296 |
| Final KB triplets | 138,659 |
| Final KB entities | 128,319 |
| Final KB relations | 2,406 |
| KGE training triplets | 124,693 |
| TransE MRR (full dataset) | 0.0540 |
| TransE Hits@10 (full dataset) | 0.1034 |
| RAG evaluation (5 questions) | 5/5 ✓ |

---

## Notes & Known Limitations

- **HermiT reasoner**: The SWRL reasoning cell attempts to use HermiT via OWLReady2, which requires a Java runtime. If Java is not installed, a Python fallback is used automatically and produces the correct results.
- **gemma:2b SPARQL quality**: The 2B parameter model sometimes generates syntactically invalid SPARQL. The self-repair loop corrects most syntax errors. A 7B+ model would produce more reliable queries.
- **Entity linking noise**: Pronouns and common nouns (e.g., "we", "they", "data") were sometimes aligned to Wikidata due to the absence of coreference resolution and entity filtering. This adds noise to the expanded graph.
- **GPU**: The KGE training was run on CPU only. Using a CUDA-enabled GPU would significantly speed up training and allow more epochs.
