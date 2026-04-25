# 🏥 Insurance Claim Agent

> An AI-powered insurance claim processing system built with **LangGraph** — automating claim validation, reducing operational costs, and eliminating human error through intelligent agentic workflows.

[![Python](https://img.shields.io/badge/Python-3.12%2B-blue?logo=python)](https://www.python.org/)
[![LangGraph](https://img.shields.io/badge/LangGraph-1.1.9%2B-green)](https://langchain-ai.github.io/langgraph/)
[![Chainlit](https://img.shields.io/badge/Chainlit-2.11.1%2B-orange)](https://docs.chainlit.io/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.136%2B-teal?logo=fastapi)](https://fastapi.tiangolo.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Why LangGraph for Insurance Claims?](#-why-langgraph-for-insurance-claims)
- [Architecture](#️-architecture)
- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [Prerequisites](#-prerequisites)
- [Installation](#-installation)
- [Configuration](#️-configuration)
- [Database Setup](#️-database-setup)
- [Running the Application](#-running-the-application)
- [API Reference](#-api-reference)
- [Project Structure](#-project-structure)
- [Contributing](#-contributing)
- [License](#-license)

---

## 📖 Overview

The **Insurance Claim Agent** is an end-to-end agentic AI system that automates the insurance claim validation lifecycle. It integrates with FHIR-compliant healthcare APIs to retrieve real patient and insurance data, applies RAG-based policy document retrieval, and leverages GPT-powered validation — all orchestrated via a **LangGraph** state machine.

The system exposes two interfaces:
- 💬 **Chainlit Chat UI** — a conversational interface for claim officers
- 🔌 **FastAPI REST API** — for programmatic integration with existing hospital or insurance systems

---

## 🚀 Why LangGraph for Insurance Claims?

Traditional insurance claim processing is slow, expensive, and error-prone. Manual review bottlenecks cause delays that frustrate patients and inflate operational costs. This project leverages **LangGraph** to fundamentally transform that process:

### ⚡ Process Automation & Speed
LangGraph orchestrates a multi-step agentic workflow — from patient data retrieval to AI validation to final decision — in a single, automated pipeline. What previously took hours of manual review can now be completed in seconds.

### 💰 Cost Reduction
By automating routine claim validations (e.g., straightforward approvals or clear-cut rejections), the system dramatically reduces the volume of claims requiring human involvement. Claim officers are freed up to focus exclusively on edge cases and complex disputes.

### 🎯 Eliminating Human Error
Rule-based routing and AI-driven validation ensure consistent decision-making across every claim. The workflow enforces the same policy checks every single time, eliminating inconsistencies caused by fatigue, oversight, or varying interpretations between reviewers.

### 📋 Human-in-the-Loop Review
For ambiguous cases, LangGraph's built-in `interrupt` mechanism pauses execution and routes the claim to a human reviewer — ensuring oversight is applied exactly where it matters, without slowing down clear-cut cases.

### 🔍 Full Auditability
Every claim decision is persisted to a PostgreSQL database with a timestamped record including the patient ID, decision status, and detailed AI reasoning. This creates a complete audit trail for regulatory compliance, dispute resolution, and retrospective analysis — answering *who decided what, and why* at any point in time.

---

## 🏗️ Architecture

```
User / API Client
       │
       ▼
┌─────────────────┐        ┌──────────────────┐
│   Chainlit UI   │        │   FastAPI REST   │
│    (app.py)     │        │  (claim_api.py)  │
└────────┬────────┘        └────────┬─────────┘
         │                          │
         └──────────┬───────────────┘
                    ▼
         ┌──────────────────────┐
         │   LangGraph Agent    │
         │  (StateGraph Engine) │
         └──────────┬───────────┘
                    │
    ┌───────────────┼───────────────────────┐
    ▼               ▼                       ▼
┌────────┐   ┌───────────┐          ┌──────────────┐
│  FHIR  │   │  ChromaDB │          │  PostgreSQL  │
│  API   │   │  (RAG)    │          │  (Audit DB)  │
└────────┘   └───────────┘          └──────────────┘
```

### LangGraph Workflow

```
[START]
   │
   ▼
fetch_patient_data  ──▶  fetch_patient_insurance  ──▶  retrieve_policy_docs
                                                               │
                                                               ▼
                                                        validate_claim (LLM)
                                                               │
                                                               ▼
                                                        claim_decision
                                                        ┌──────┴──────┐
                                                        ▼             ▼
                                                  store_claim   human_review
                                                        ▲             │
                                                        └─────────────┘
                                                               │
                                                            [END]
```

---

## ✨ Features

- 🤖 **AI-Powered Validation** — GPT-4 analyzes patient data, insurance coverage, and policy documents to recommend claim outcomes
- 🏥 **FHIR Integration** — Fetches live patient and coverage data from FHIR R4-compliant APIs
- 📚 **RAG Policy Retrieval** — ChromaDB vector search retrieves relevant insurance policy clauses for each claim
- 👤 **Human-in-the-Loop** — Ambiguous claims are escalated to human reviewers via a conversational interface before being finalized
- 💾 **Persistent Audit Log** — All decisions are stored in PostgreSQL with full reasoning traces
- 🔌 **Dual Interface** — Chat UI for claim officers + REST API for system integrations
- 🔍 **LangSmith Tracing** — Full observability into every LLM call and agent step

---

## 🛠 Tech Stack

| Layer | Technology |
|-------|-----------|
| Agent Orchestration | [LangGraph](https://langchain-ai.github.io/langgraph/) |
| LLM | [OpenAI GPT-4](https://openai.com/) via LangChain |
| Vector Store | [ChromaDB](https://www.trychroma.com/) |
| Chat UI | [Chainlit](https://docs.chainlit.io/) |
| REST API | [FastAPI](https://fastapi.tiangolo.com/) + [Uvicorn](https://www.uvicorn.org/) |
| Database | [PostgreSQL](https://www.postgresql.org/) via [psycopg3](https://www.psycopg.org/) |
| Observability | [LangSmith](https://docs.smith.langchain.com/) |
| Package Manager | [uv](https://docs.astral.sh/uv/) |
| Runtime | Python 3.12+ |

---

## 📦 Prerequisites

- **Python 3.12+**
- **[uv](https://docs.astral.sh/uv/)** — fast Python package manager
- **PostgreSQL** — running locally or via Docker
- **OpenAI API Key**
- *(Optional)* LangSmith API Key for tracing

Install `uv` if you haven't already:
```bash
# Windows (PowerShell)
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

# macOS / Linux
curl -LsSf https://astral.sh/uv/install.sh | sh
```

---

## 🚀 Installation

```bash
# 1. Clone the repository
git clone https://github.com/your-username/Insurance_claim_Agent.git
cd Insurance_claim_Agent

# 2. Install dependencies using uv
uv sync
```

---

## ⚙️ Configuration

Create a `.env` file in the root directory:

```env
# Required
OPENAI_API_KEY=sk-...

# Optional — LangSmith tracing
LANGSMITH_API_KEY=lsv2_...
LANGSMITH_TRACING=true
LANGSMITH_PROJECT=insurance-claim-agent
```

> ⚠️ **Never commit `.env` to version control.** It is already included in `.gitignore`.

---

## 🗄️ Database Setup

Start PostgreSQL and run the schema setup:

```bash
# Using Docker (recommended)
docker run --name claim-db \
  -e POSTGRES_PASSWORD=1234 \
  -e POSTGRES_DB=claim_db \
  -p 5432:5432 \
  -d postgres:16

# Apply schema
psql -h localhost -U postgres -d claim_db -f claims.sql
```

Or apply manually in your PostgreSQL client:

```sql
-- From claims.sql
CREATE EXTENSION IF NOT EXISTS "pgcrypto";

CREATE TABLE claims (
    claim_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    patient_id INT NOT NULL,
    status VARCHAR(50) NOT NULL,
    decision_details TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## ▶️ Running the Application

### Chat UI (Chainlit)

```bash
uv run chainlit run app.py -w
```

Open your browser at **http://localhost:8000**

### REST API (FastAPI)

```bash
uv run uvicorn claim_processing_api:app --reload
```

API docs available at **http://localhost:8000/docs**

---

## 🔌 API Reference

### `POST /process-claim`

Submit a new insurance claim for automated processing.

**Request Body:**
```json
{
  "patient_id": "12345",
  "treatment_code": "Z12.31",
  "claim_details": "Patient requests coverage for third Hemoglobin A1c test this year."
}
```

**Response:**
```json
{
  "final_decision": "Approved",
  "ai_feedback": "The claim is valid based on the patient's diagnostic history and policy coverage terms..."
}
```

| Status | Meaning |
|--------|---------|
| `Approved` | Claim automatically approved by AI validation |
| `Rejected` | Claim rejected based on policy assessment |
| `Request More Info` | Escalated for human review |

---

## 📁 Project Structure

```
insurance-claim-agent/
├── app.py                     # Chainlit chat UI entry point
├── claim_processing_agent.py  # LangGraph workflow definition
├── claim_processing_api.py    # FastAPI REST API
├── main.py                    # CLI entry point
├── claims.sql                 # PostgreSQL schema
├── insurance_data.txt         # Policy documents for RAG
├── pyproject.toml             # Project metadata & dependencies
├── uv.lock                    # Locked dependency versions
├── .chainlit/                 # Chainlit configuration
├── .env                       # Environment variables (not committed)
└── .gitignore
```

---

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. **Fork** the repository
2. Create a feature branch: `git checkout -b feature/your-feature-name`
3. **Commit** your changes: `git commit -m 'feat: add some feature'`
4. **Push** to the branch: `git push origin feature/your-feature-name`
5. Open a **Pull Request**

Please follow [Conventional Commits](https://www.conventionalcommits.org/) for commit messages.

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

<p align="center">Built with ❤️ using LangGraph, Chainlit, and FastAPI</p>
