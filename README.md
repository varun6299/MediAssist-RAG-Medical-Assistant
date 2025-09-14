# 🩺 MediAssist — RAG-based Medical Assistant  
*A Retrieval-Augmented Generation (RAG) system that leverages trusted medical manuals to provide accurate, reliable, and context-grounded clinical information.*

---

## 📦 Packages Used
[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/)  
[![LangChain](https://img.shields.io/badge/LangChain-RAG-green.svg)](https://www.langchain.com/)  
[![Llama.cpp](https://img.shields.io/badge/Llama.cpp-LLM-blue.svg)](https://github.com/ggerganov/llama.cpp)  
[![PyMuPDF](https://img.shields.io/badge/PyMuPDF-PDF-lightgrey.svg)](https://pymupdf.readthedocs.io/)  
[![Pandas](https://img.shields.io/badge/Pandas-EDA-teal.svg)](https://pandas.pydata.org/)  
[![NumPy](https://img.shields.io/badge/NumPy-Scientific-orange.svg)](https://numpy.org/)  

---

## 🏥 Business Context
Healthcare professionals face **information overload** when navigating large volumes of clinical references, diagnostic guidelines, and treatment protocols. Searching through **4,000+ pages of medical manuals** during critical patient care is inefficient and risky.  

MediAssist was developed as an **AI-powered assistant** that uses RAG to:  
- Provide **fact-grounded clinical answers**.  
- Reduce **time to retrieve medical knowledge**.  
- Improve **consistency of diagnoses and treatments**.  
- Empower doctors and nurses with **decision support at scale**.  

---

## 🎯 Objectives
The goals of MediAssist are to:  
- **Retrieve** relevant sections from authoritative sources (Merck Manual, ~4,000 pages).  
- **Generate** answers that are concise, medically correct, and context-rich.  
- **Experiment** with prompt engineering strategies to optimize clarity and accuracy.  
- **Fine-tune** RAG configurations to balance completeness vs efficiency.  
- **Demonstrate** feasibility of deploying a prototype for real-world decision support.  

---

## 📊 Dataset
- **Source:** Merck Manual PDF (>4,000 pages, 23 sections).  
- **Content:** Disorders, diagnostic tests, treatments, drug information.  
- **Loader:** `PyMuPDFLoader` for ingestion.  
- **Preprocessing:** Text split into retrievable chunks with metadata (section, page).  
- **Use Cases:** Queries like sepsis management, appendicitis treatment, alopecia areata, brain injuries, and fracture care.  

---

## 🛠 RAG Building Approach

The system was developed in **nine structured steps** (MediAssist Framework):

### 1. Define Problem & Objectives  
- Established key clinical use cases: diagnostics, treatments, emergency protocols.  
- Identified the need to minimize **hallucinations** while ensuring **completeness** of answers.  

### 2. Baseline LLM Testing  
- Evaluated pre-trained LLaMA models without retrieval.  
- Observed issues: **hallucinations, missing sub-answers, oversimplification**.  

### 3. Prompt Engineering  
- Tested **Basic, Role-Based, Instruction, Few-Shot, and [INST] tag prompts**.  
- Best performance came from **Instruction-based and [INST]** prompts when paired with RAG.  

### 4. Data Preparation  
- Loaded Merck Manual PDF.  
- Split into **semantic chunks (200–1000 tokens)**.  
- Preserved metadata (section, page) for **traceability and citation**.  

### 5. Embedding & Vectorization  
- Converted chunks into dense **embeddings** using domain-appropriate embedding models.  
- Each chunk embedded into a high-dimensional vector space.  
- Captured **semantic meaning**, allowing similarity search on medical concepts (not just keywords).  

### 6. Vector Store Setup  
- Stored embeddings in a **vector database** with efficient Approximate Nearest Neighbor (ANN) indexing.  
- Indexed metadata alongside vectors for citation retrieval.  
- Configured **similarity search (top-k retrieval)** to pull the most relevant sections for each query.  

### 7. Retriever Configuration  
- Implemented a **retrieval pipeline**:  
  1. Embed user query.  
  2. Retrieve **top-k (k=3–5)** most relevant chunks from the vector DB.  
  3. Insert retrieved context into the LLM prompt.  
- Fine-tuned parameters: retrieval depth, score threshold, and token window size.  

### 8. RAG Implementation  
- Integrated retriever with **LLaMA LLM (via llama.cpp)** using LangChain.  
- Prompt assembly combined: **retrieved chunks + engineered prompt template**.  
- Guardrails:  
  - “No-answer” fallback if no relevant context retrieved.  
  - Forced citation of source sections.  

### 9. Fine-Tuning & Evaluation  
- Iteratively adjusted configs (k, temperature, top_p).  
- Benchmarked on dimensions: **factual correctness, completeness, readability**.  
- Instruction-based prompts with **k=5, temp=0.3** yielded the **best clinical answers**.  

---

## 📏 Evaluation Metrics
MediAssist responses were scored on:  
- **Groundedness** – factual alignment with retrieved manual text.  
- **Completeness** – coverage of all sub-parts of the query.  
- **Relevance** – clinical appropriateness of the information.  
- **Clarity** – concise and usable by healthcare providers.  

### Why RAG > LLM-only?  
- **LLM-only:** Fluent but often hallucinated or incomplete.  
- **RAG:** Reliable, evidence-backed answers with clear source citations.  

---

## 📊 Results & Comparison

| Setup                         | Strengths | Weaknesses | Best Use Case |
|-------------------------------|-----------|------------|---------------|
| **Baseline LLM**              | Fluent answers | Frequent hallucinations | Not recommended |
| **RAG – Default Setup**       | Reliable grounding | Partial answers | Prototyping |
| **Fine-Tuned RAG Config 2**   | High completeness, accurate, structured | Slightly slower | Clinical support |
| **Fine-Tuned RAG Config 3**   | Balanced, concise, efficient | Some oversimplification | General-purpose assistants |

**Best Performing Setup:**  
- **RAG + Instruction-based prompts** with `k=5`, `temperature=0.3`.  
- Produced the **most complete and clinically accurate responses**, with clear structure and citations.  

---

## 💡 Insights & Business Impact
- **Accuracy & Trust:** Anchoring responses in Merck Manual eliminates hallucinations.  
- **Efficiency:** Saves **minutes per query**, critical in emergencies.  
- **Consistency:** Standardizes clinical guidance across practitioners.  
- **Scalability:** Approach can extend to other domains like oncology, drug safety, or treatment protocols.  
- **Prototype Feasibility:** Successfully queries a **4,000+ page manual** in seconds.  

📌 **Bottom Line:**  
MediAssist demonstrates that GenAI + RAG can transform clinical decision-making by delivering **fast, reliable, and fact-grounded answers at scale**.  

---
