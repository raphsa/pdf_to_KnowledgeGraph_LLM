# PDF-to-Knowledge Graph → Instruction Generation → LLM Fine-Tuning

**University:** University of Oulu, Finland

**Course:** Natural Language Processing and Text Mining

**Authors:** 
- Sali Raffaele [![GitHub - raphsa](https://img.shields.io/badge/GitHub-@raphsa-blue?logo=github)](https://github.com/raphsa)
- Vianello Martina [![GitHub - martinavianello](https://img.shields.io/badge/GitHub-@martinavianello-blue?logo=github)](https://github.com/martinavianello)
- Zani Beatrice [![GitHub - beazani](https://img.shields.io/badge/GitHub-@beazani-blue?logo=github)](https://github.com/beazani)

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
- **Source Code:**
```
main.ipynb
```  
- **Outputs:**  
```
data/raw_text.jsonl
data/tables/*.csv
```

---

### **2. Domain Schema & Ontology**
- Design an ontology defining **entities, attributes, and relations** (e.g., `Ingredient`, `Nutrient`, `Recipe`, `Technique`).
- Specify URI and type conventions.  
- **Output:**  
```
data/ontology.yaml - data/ontology.ttl
```

---

### **3. NER & Keyphrase Extraction**
- Apply **NER** (spaCy/transformer) and **keyphrase extraction** (YAKE, KeyBERT).
- Map entity mentions to ontology classes and resolve aliases.
- **Source Code:**
```
ner3.ipynb
``` 
- **Output:**  
```
entities_new.jsonl
```

---

### **4. Relation Extraction & Triple Building**
- Identify relations via rule-based or ML models.  
  Example: `Ingredient → hasNutrient → Nutrient`
- Convert relations into RDF triples with provenance metadata.
- **Source Code:**
```
main.ipynb
``` 
- **Output:**  
```
data/triples_clean.ttl
```

---

### **5. Knowledge Graph Construction**
- Load triples into a graph database (e.g., **Neo4j**, **GraphDB**, **Fuseki**).
- Run SPARQL/Cypher queries for validation.
- **Source Code:**
```
main.ipynb
```  
- **Outputs:**  
  - Graph instance  
  - Query notebook with 5–10 example queries: ```data/fuseki_queries.ipynb```   

---

### **6. Fact Extraction & Contradiction Checking**
- Generate atomic facts and natural-language paraphrases.
- Detect internal contradictions or overlaps in numerical/textual data.
- **Source Code:**
```
main.ipynb
``` 
- **Output:**  
```
data/facts.jsonl
```

---

### **7. Instruction–Response Dataset Generation**
- Create **grounded instruction–response pairs** from KG facts.  
- Types include:
  - Factoid QA  
  - List/Compare queries  
  - Reasoning and constraint-based queries  
- Each example includes **grounding evidence** (linked triples/pages).
- **Source Code:**
```
main.ipynb
``` 
- **Outputs:**  
```
data/train/test/val/train_instructions.jsonl
data/train/test/val/test_instructions.jsonl
data/train/test/val/val_instructions.jsonl
```

---

### **8. Baseline Models**
- **RAG over KG:** Retrieve facts from KG and use LLM for reasoning.  
- **Zero-shot LLM:** Directly prompt the model without KG grounding.  
- **Metrics:** latency, context length, factual coverage.
- **Source Code:**
```
baselines.ipynb
```
- **Outputs:**
```
models/baselines/rag_predictions.jsonl
models/baselines/zero_shot_predictions.jsonl
```

---

### **9. Fine-Tuning Setup**
- Fine-tune LLM (e.g., **Mistral-7B**, **LLaMA-2-7B**) using **LoRA** or adapter-based methods.
- Maintain consistent prompt templates with factual guardrails.
- **Source Code:**
```
fine_tuning.ipynb
model_test.ipynb
```  
- **Outputs:**  
  - Training logs: ```models/fine_tuned_model/train/logs/```  
  - Model checkpoints: ```models/fine_tuned_model/train/```   
  - Validation metrics: ```models/fine_tuned_model/train/```

---

### **10. Evaluation**
- **Automatic Metrics**
  - F1 for factual QA  
  - ROUGE/BLEU for generation  
  - Constraint satisfaction rate  

- **Rubric-based Metrics**
  - Relevance  
  - Completeness  
  - Citation correctness  
- **Source Code:**
```
model_evaluation.ipynb
```
- **Outputs:**
```
evaluation/plots/*.png
```

---

### **11. Hallucination Detection**
- Align generated entities and values to KG.
- Mark unsupported claims.  
- **Metric:**  
`Hallucination Rate = % of answers with unsupported claims`
- **Source Code:**
```
model_evaluation.ipynb
```
- **Output:**
```
evaluation/plots/hallucination_rate.png
```

---

### **12. Interactive Demo**
Simple **Streamlit/Gradio** app for demonstration:  
- **Demo Input:** User query  
- **Demo Output:** Generated answer with inline citations  
- **Modes:** Zero-shot | RAG-KG | Fine-tuned
- **Source Code:**
```
app/baseline_gui.ipynb
app/fine_tuning_gui.ipynb
app/gui.ipynb
``` 

---

## 🧩 Directory Structure

```
Project_24_NLP/
│
├── app/
│   ├── baseline_gui.ipynb
│   ├── fine_tuning_gui.ipynb
│   ├── gui.ipynb
│
├── data/
│   ├── tables/
│   ├── train/test/val/
│   ├── raw_text.jsonl
│   ├── ontology.yaml
│   ├── ontology.ttl
│   ├── seed_vocabularies.json
│   ├── new_seed.json
│   ├── entities_new.jsonl
│   ├── facts.jsonl
│   ├── triples_clean.ttl
│   ├── fuseki_queries.ipynb
│   └── sustainable-health-from-food_web.pdf
│
├── models/
│   ├── fine_tuned_model/train/
│   ├── baselines/
│
├── evaluation/
│   ├── plots/
│
├── report/
│   ├── main.tex
│   └── main.pdf
│
├── baselines.ipynb
├── fine_tuning.ipynb
├── main.ipynb
├── model_test.ipynb
├── ner3.ipynb
└── README.md
```

---

## 🧪 Technologies Used

- **Python 3.10**
- **spaCy**, **Transformers (Hugging Face)**
- **KeyBERT**, **YAKE**
- **Neo4j** / **GraphDB**
- **PyTorch / PEFT (LoRA)**
- **Streamlit / Gradio**

---

## 🧰 Installation

1. Python version:

    - **Python3.10** required

2. Install dependencies:

```
pip install -r requirements.txt
```

---

## Evaluation Goals

- Assess how **grounded fine-tuning** improves **manual/rubric based metrics** and **reduces hallucination**.  
- Measure trade-offs between **retrieval-based** and **end-to-end fine-tuned** methods.  
- Provide insight into building **trustworthy domain-specific LLMs**.

---