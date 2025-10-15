# PDF-to-Knowledge Graph → Instruction Generation → LLM Fine-Tuning

**University:** University of Oulu, Finland

**Course:** Natural Language Processing and Text Mining

**Authors:** 
- Sali Raffaele @raphsa
- Vianello Martina [@martinavianello](https://github.com/martinavianello)
- Zani Beatrice [@beazani](https://github.com/beazani)

**Year:** 2025/2026

---

## Project Overview

This project builds a complete NLP pipeline that transforms a **domain-specific PDF** into structured knowledge, generates **instruction-response pairs**, and **fine-tunes a language model** based on that knowledge.  

The end goal is to evaluate whether **LLM fine-tuning on grounded knowledge** improves factual accuracy compared to **retrieval-based** or **zero-shot** baselines.

---

## Domain & Data Source

- **Source Document:** *SUSTAINABLE HEALTH FROM FOOD* (PDF)  
- **Domain:** Nutrition and Food Science  
- **Restriction:** No external data sources — all knowledge must be derived from the provided PDF and its knowledge graph.  

---

## Project Pipeline

### **1. PDF Ingestion & Parsing**
- Extract text, headings, and metadata with page anchors.
- Parse tables into structured formats (`CSV`/`JSON`).
- Preserve figures, captions, and section hierarchy.  
**Deliverables:**  
```
raw_text.jsonl
tables/*.csv
```

---

### **2. Domain Schema & Ontology**
- Design an ontology defining **entities, attributes, and relations** (e.g., `Ingredient`, `Nutrient`, `Recipe`, `Technique`).
- Specify URI and type conventions.  
**Deliverable:**  
```
ontology.yaml  (or ontology.ttl)
```

---

### **3. NER & Keyphrase Extraction**
- Apply **NER** (spaCy/transformer) and **keyphrase extraction** (YAKE, KeyBERT).
- Map entity mentions to ontology classes and resolve aliases.  
**Deliverable:**  
```
entities.jsonl
```

---

### **4. Relation Extraction & Triple Building**
- Identify relations via rule-based or ML models.  
  Example: `Ingredient → hasNutrient → Nutrient`
- Convert relations into RDF triples with provenance metadata.  
**Deliverables:**  
```
triples.ttl  or  graph.json
```

---

### **5. Knowledge Graph Construction**
- Load triples into a graph database (e.g., **Neo4j**, **GraphDB**, **Fuseki**).
- Run SPARQL/Cypher queries for validation.  
**Deliverables:**  
- Graph instance  
- Query notebook with 5–10 example queries  

---

### **6. Fact Extraction & Contradiction Checking**
- Generate atomic facts and natural-language paraphrases.
- Detect internal contradictions or overlaps in numerical/textual data.  
**Deliverable:**  
```
facts.jsonl
```

---

### **7. Instruction–Response Dataset Generation**
- Create **grounded instruction–response pairs** from KG facts.  
- Types include:
  - Factoid QA  
  - List/Compare queries  
  - Reasoning and constraint-based queries  
- Each example includes **grounding evidence** (linked triples/pages).  
**Deliverable:**  
```
instructions.jsonl  (train/val/test split)
```

---

### **8. Baseline Models**
- **RAG over KG:** Retrieve facts from KG and use LLM for reasoning.  
- **Zero-shot LLM:** Directly prompt the model without KG grounding.  
**Metrics:** latency, context length, factual coverage.

---

### **9. Fine-Tuning Setup**
- Fine-tune LLM (e.g., **Mistral-7B**, **LLaMA-2-7B**) using **LoRA** or adapter-based methods.
- Maintain consistent prompt templates with factual guardrails.  
**Deliverables:**  
- Training logs  
- Model checkpoints  
- Validation metrics  

---

### **10. Evaluation**
**Automatic Metrics**
- Exact Match / F1 for factual QA  
- ROUGE/BLEU for generation  
- Constraint satisfaction  
- Faithfulness (string match to KG facts)

**Human / Rubric-based**
- Relevance  
- Completeness  
- Citation correctness  


---

### **11. Hallucination Detection**
- Align generated entities and values to KG.
- Mark unsupported claims.  
**Metric:**  
`Hallucination Rate = % of answers with unsupported claims`

---

### **12. (Optional) Interactive Demo**
Simple **Streamlit/Gradio** app for demonstration:  
- **Input:** User query  
- **Output:** Generated answer with inline citations  
- **Modes:** Zero-shot | RAG-KG | Fine-tuned  

---

## 🧩 Directory Structure

```
Project_24_NLP/
│
├── data/
│   ├── raw_text.jsonl
│   ├── tables/
│   ├── ontology.yaml
│   ├── entities.jsonl
│   ├── triples.ttl
│   ├── facts.jsonl
│
├── notebooks/
│   ├── parsing.ipynb
│   ├── relation_extraction.ipynb
│   ├── KG_queries.ipynb
│
├── models/
│   ├── fine_tuned_model/
│   ├── baselines/
│
├── evaluation/
│   ├── results.csv
│   ├── plots/
│
├── app/
│   └── demo_app.py
│
└── README.md
```

---

## 🧪 Technologies Used

- **Python 3.10+**
- **spaCy**, **Transformers (Hugging Face)**
- **KeyBERT**, **YAKE**
- **Neo4j** / **GraphDB**
- **PyTorch / PEFT (LoRA)**
- **Streamlit / Gradio**

---

## Evaluation Goals

- Assess how **grounded fine-tuning** improves **faithfulness** and **reduces hallucination**.  
- Measure trade-offs between **retrieval-based** and **end-to-end fine-tuned** methods.  
- Provide insight into building **trustworthy domain-specific LLMs**.

---

## Learning Outcomes

By completing this project, you will learn how to:
- Extract and represent structured knowledge from unstructured PDFs.  
- Build and query knowledge graphs.  
- Generate instruction datasets for LLM training.  
- Fine-tune and evaluate large language models on domain-specific tasks.