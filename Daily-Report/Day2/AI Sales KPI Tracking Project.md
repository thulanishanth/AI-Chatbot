# Project Blueprint: SalesPulse AI (Automated Sales KPI & Performance Insights)

## 1. Executive Summary
**Project Title:** SalesPulse AI – Intelligent Real-Time KPI Tracker & Forecasting Engine  
**Domain:** FinTech / Sales Operations / Business Intelligence  
**Core Objective:** To replace manual, fragmented sales tracking with an automated, AI-driven system that provides real-time visibility, predictive analytics, and proactive performance alerts.

---

## 2. Business Problem & Solution
### The Problem
* **Fragmentation:** Sales data is scattered across spreadsheets, CRMs, and emails.
* **Latency:** Regional heads spend days consolidating data, leading to "post-mortem" analysis rather than proactive management.
* **Subjectivity:** Insights are dependent on human interpretation, often missing subtle trends or risks.

### The Solution
A centralized **Django-based platform** that ingests sales data in real-time, uses **Pandas** for rigorous KPI calculation, and integrates **LangChain (LLM)** to provide natural language insights (e.g., *"Why did Region North miss the target?"*) and **Prophet** for forecasting future trends.

---

## 3. High-Level System Architecture

The system follows a modern **ELT (Extract, Load, Transform)** architecture enhanced with an AI Inference layer.

### Layers
1.  **Data Ingestion Layer (The "Senses")**
    * **Sources:** CSV/Excel uploads, REST APIs (CRM integration), Email attachments.
    * **Tech:** Python `pandas`, Django File Handling, Celery (for async processing).
2.  **Storage Layer (The "Memory")**
    * **Relational DB:** PostgreSQL (Stores structured sales data, user profiles, hierarchical regions).
    * **Vector DB (Optional):** FAISS/ChromaDB (Stores embedded historical summaries for RAG-based AI queries).
3.  **Application Logic Layer (The "Brain")**
    * **KPI Engine:** Python scripts to calculate WoW (Week-over-Week), MoM (Month-over-Month) growth, and attainment %.
    * **AI Engine:** * *Forecasting:* Facebook Prophet or ARIMA for predicting next month's sales.
        * *Insights:* OpenAI/Gemini API via LangChain to generate text summaries of data.
4.  **Presentation Layer (The "Face")**
    * **Dashboard:** Streamlit (embedded) or Chart.js/D3.js rendered via Django Templates.
    * **Alerts:** Email/Slack notifications via SMTP/Webhooks.

---

## 4. Technical Stack

| Component | Technology | Reasoning |
| :--- | :--- | :--- |
| **Backend Framework** | **Django (Python)** | Robust ORM for complex data relationships (Region -> Manager -> Sales Rep); built-in Admin panel. |
| **Database** | **PostgreSQL** | Reliable ACID compliance for financial/sales data. |
| **Data Processing** | **Pandas & NumPy** | High-performance data manipulation and cleaning. |
| **Async Task Queue** | **Celery + Redis** | Essential for handling large Excel uploads without freezing the UI. |
| **AI & LLM** | **LangChain + OpenAI/Gemini** | Framework for chaining prompts to generate natural language business insights. |
| **Forecasting** | **Prophet (Meta)** | specialized for time-series forecasting with seasonality (e.g., holiday sales spikes). |
| **Frontend** | **Bootstrap 5 + Chart.js** | Responsive UI with interactive, lightweight charting. |
| **Deployment** | **Docker + Gunicorn + Nginx** | Standard production-grade containerization. |

---

## 5. MVC Architecture 

### Model (Data Structure)
* **`Organization`**: Hierarchy definitions (Region, Zone, Territory).
* **`SalesTarget`**: Assigned quotas per rep/period.
* **`Transaction`**: Granular sales events (Date, Amount, SKU, Customer).
* **`PerformanceMetric`**: Pre-calculated daily/weekly KPIs (stored to avoid re-calculation).

### View (User Interface & API)
* **`DashboardView`**: Aggregates `PerformanceMetric` data for the logged-in user's scope.
* **`UploadView`**: Handles file uploads and triggers Celery tasks.
* **`InsightAPI`**: REST endpoint receiving a question ("How is Q3 looking?") and returning AI-generated text.

### Controller (Business Logic / Services)
* **`DataIngestionService`**: Validates Excel schemas, sanitizes data.
* **`CalculationEngine`**: Computes attainment %, run rates, and gaps.
* **`ForecastService`**: Runs the ML model on historical data.

---

## 6. Control Flow Systems

### Flow A: Data Ingestion & KPI Calculation
1.  **User Action:** Regional Head uploads `Weekly_Sales_Oct.xlsx`.
2.  **System:** Django View receives file -> Saves to Temp -> Triggers Celery Task.
3.  **Async Task:** * Loads Excel into Pandas DataFrame.
    * Validates columns (detects missing data).
    * Calculates new KPIs (e.g., "75% to Target").
    * Updates `PerformanceMetric` table.
4.  **Notification:** User receives "Data Processed" alert via WebSocket/Page Refresh.

### Flow B: AI Insight Generation
1.  **Trigger:** Dashboard loads or User clicks "Generate Insights".
2.  **System:** Fetches aggregated KPIs for the current view (e.g., "Region West").
3.  **AI Engine:** * Formats data as a prompt: *"Region West is at 60% of target with 5 days left. Historical average is 85%. Analyze risk."*
    * Sends to LLM via LangChain.
4.  **Output:** LLM returns: *"Critical Risk: Region West is trending to miss target by 15%. Recommend immediate follow-up on Deals A and B."*
5.  **Display:** Insight card shown on Dashboard.

---

## 7. Advantages & Disadvantages

### Advantages (Pros)
* **Speed:** Reduces reporting time from days to seconds.
* **Proactivity:** Predictive alerts allow managers to intervene *before* a target is missed.
* **Scalability:** Can handle growing data volumes without manual staff increase.
* **Data Integrity:** Automated validation removes human error in spreadsheets.

### Disadvantages (Cons)
* **Implementation Complexity:** Setting up Async queues (Celery) and ML pipelines is complex.
* **Cost:** API costs for LLMs (OpenAI/Gemini) can scale with usage.
* **Data Dependency:** The system is only as good as the input data; "Garbage In, Garbage Out" applies strictly.

---

## 8. Reference GitHub Repositories
* **[Sales-Analytics-AI-Intelligence-Platform](https://github.com/Hassan0397/Sales-Analytics-AI-Intelligence-Platform-Data-Analyst-GenAI-Project-)**: End-to-end GenAI sales dashboard.
* **[django-pandas](https://github.com/wemake-services/django-pandas)**: Tools for working with Pandas and Django Models.
* **[prophet](https://github.com/facebook/prophet)**: The core forecasting library.
* **[langchain](https://github.com/langchain-ai/langchain)**: Framework for building the "Chat with Data" feature.