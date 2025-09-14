# 🩺 MediAssist — RAG-based Medical Assistant  
*A Retrieval-Augmented Generation (RAG) system that leverages trusted medical manuals to provide accurate, reliable, and context-grounded clinical information.*

<p align="center">
  <img src="images/MediAssistant_Image.png" width="600"/>
</p>

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

<p align="center">
  <img src="images/MediAssistant RAG.png" width="600"/>
</p>

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

<p align="center">
  <img src="images/MediAssistant RAG LLM Instance.png" width="600"/>
</p>

### 7. Retriever Configuration  
- Implemented a **retrieval pipeline**:  
  1. Embed user query.  
  2. Retrieve **top-k (k=3–5)** most relevant chunks from the vector DB.  
  3. Insert retrieved context into the LLM prompt.  
- Fine-tuned parameters: retrieval depth, score threshold, and token window size.  

### 8. RAG Implementation  
- Integrated retriever with **Mistral-7B-Instruct (quantized GGUF via `llama.cpp`)** using LangChain.  
- Guardrails:  
  - “No-answer” fallback if retrieval score too low.  
  - Forced citations for every answer.  
 
<p align="center">
  <img src="images/MediAssistant Response Function.png" width="600"/>
</p>

### 9. Fine-Tuning & Evaluation  
- Iteratively adjusted configs (k, temperature, top_p).  
- Benchmarked on dimensions: **factual correctness, completeness, readability**.  
- Instruction-based prompts with **k=5, temp=0.3** yielded the **best clinical answers**.  

---


## 🤖 Modeling Approach
- **LLM Backbone:** [Mistral-7B-Instruct v0.2 (GGUF, Q2_K quantized)](https://huggingface.co/TheBloke/Mistral-7B-Instruct-v0.2-GGUF).  
- **Inference Engine:** [`llama.cpp`](https://github.com/ggerganov/llama.cpp) for efficient local inference.  
- **RAG Orchestration:** LangChain pipeline for retrieval + generation.  
- **Prompts:** Instruction-style templates + retrieved context performed best.  

---

## 📏 Evaluation Metrics
MediAssist responses were scored on:  
- **Groundedness** – factual alignment with retrieved text.  
- **Completeness** – coverage of multi-part clinical queries.  
- **Relevance** – clinical appropriateness.  
- **Clarity** – concise, usable language for clinicians.  

👉 **Why RAG with Mistral-7B > LLM-only?**  
- LLM-only: Fluent but hallucinated and incomplete.  
- RAG + Mistral: Reliable, fact-based, with clear source citations.  

---

## 📊 Results & Comparison

| Setup                         | Strengths | Weaknesses | Best Use Case |
|-------------------------------|-----------|------------|---------------|
| **Baseline LLM (Mistral only)** | Fluent answers | Frequent hallucinations | Not recommended |
| **RAG – Default Setup**       | Reliable grounding | Partial answers | Prototyping |
| **Fine-Tuned RAG Config 2**   | High completeness, accurate, structured | Slightly slower | Clinical support |
| **Fine-Tuned RAG Config 3**   | Balanced, concise, efficient | Some oversimplification | General-purpose assistants |

**Best Performing Setup:**  
- **RAG + Instruction-based prompts with Mistral-7B-Instruct** (`k=5`, `temp=0.3`).  
- Produced the **most accurate, complete, and reliable answers** with citations.  

---

## 💡 Insights & Business Impact
- **Accuracy & Trust:** Anchoring in Merck Manual reduces hallucinations.  
- **Efficiency:** Saves **minutes per query**, critical in emergencies.  
- **Consistency:** Standardized medical guidance across providers.  
- **Scalability:** Extendable to other specialties (oncology, pharmacology).  
- **Feasibility:** Demonstrated querying **4,000+ page manual** in seconds.  

📌 **Bottom Line:**  
MediAssist proves that **RAG + Mistral-7B-Instruct** can transform medical decision-making by delivering **fast, reliable, and context-grounded answers at scale**.  
