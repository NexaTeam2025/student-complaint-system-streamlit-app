# AI-Powered Student Complaint Assistant

Intelligent Ticket Classification, Technical Troubleshooting Agent, and MLflow Tracking

## Overview

This project delivers a unified **AI-powered student ticketing system** that automatically classifies complaints, routes them to the correct department, and provides instant AI-generated troubleshooting for technical issues. The application is built using **Streamlit** (UI), **Hugging Face BERT multilingual classification**, **OpenAI GPT models**, and **MLflow** for experiment tracking.

The system is designed for hybrid environments where institutions process high volumes of complaints and must distinguish between:

1. **Technical issues** that require debugging assistance
2. **Non-technical service requests** that must be routed to administrative departments

The unified interface supports both models, integrates them with MLflow, and exposes an end-to-end workflow through a modern Streamlit application.

---

## Key Features

### 1. Technical Ticket Detection (OpenAI GPT-based Agent)

When a student submits a complaint that appears technical, the system:

* Detects that it is a technical issue
* Uses the **OpenAI GPT-4o-mini** model with a strict JSON schema
* Generates:

  * A short summary
  * 3 to 6 actionable steps
  * Optional code or commands
  * A verification checklist
* Automatically logs each request into **MLflow** (params, metrics, artifacts, duration)

### 2. Non-Technical Ticket Routing (BERT Model)

If the complaint is non-technical, the system uses a multilingual BERT classifier to route the complaint to one of five departments:

| Label                  | Route To                |
| ---------------------- | ----------------------- |
| Certificates_Documents | Student Affairs / Docs  |
| Courses_Training       | Course & Training Unit  |
| Facilities_Logistics   | Maintenance & Logistics |
| Finance_Admin          | Finance                 |
| IT_Support             | IT Department           |

BERT supports English, Arabic, and mixed-language complaints.

### 3. MLflow Integration

All LLM processing steps are tracked with MLflow:

* Experiment: `student-complaint-ai-agent`
* Logs include:

  * Model parameters
  * Runtime duration
  * Token usage (if provided by OpenAI API)
  * Parsed JSON response
  * Student complaint text
* Errors are logged as artifacts for audit and debugging
* Supports DagsHub MLflow dashboards

### 4. Streamlit User Interface

A custom-designed, responsive interface:

* Full-screen layout with no sidebar
* Hero section with gradient theme
* Complaint entry form
* Live AI classification and routing
* Right-side contextual help panel (English/Arabic tips)
* Real-time code outputs and steps for technical issues
* Automatic routing for non-technical cases with confidence score

---

## Architecture

### High-Level Flow

```
User Complaint
       ↓
Technical Detector (GPT Agent)
       ↓ yes                     ↓ no
Technical AI Response     BERT Non-Technical Classifier
       ↓                         ↓
MLflow Logging & Steps     Department Routing
       ↓                         ↓
       Streamlit UI Rendering
```

### Components

* **Streamlit frontend**: interactive web UI
* **OpenAI Agent**: LLM-based JSON-structured technical assistant
* **BERT Model**: multilingual sequence classifier
* **MLflow/DagsHub**: experiment tracking and artifact versioning

---

## Tech Stack

### Core

* Python
* Streamlit
* PyTorch
* Hugging Face Transformers
* OpenAI API
* MLflow
* dotenv

### Optional (if local model exists)

* Local directory `BERT_BEST` is automatically detected
* Otherwise, it loads from `HF_MODEL_NAME` (defined in `config.py`)

---

## Installation

### 1. Clone the repository

```
git clone <your-repo-url>
cd <project>
```

### 2. Create and activate environment

```
python -m venv venv
source venv/bin/activate    # Linux / macOS
venv\Scripts\activate       # Windows
```

### 3. Install dependencies

Requirements file:

```
streamlit
torch
transformers
huggingface_hub
python-dotenv
openai
mlflow
```

Install them:

```
pip install -r requirements.txt
```

### 4. Configure environment variables

Create a `.env` file:

```
OPENAI_API_KEY=your-key
LLM_TIMEOUT=25
```

### 5. Run the application

```
streamlit run app.py
```

---

## Model Details

### OpenAI GPT Agent

The agent uses:

* A strict JSON schema
* Internal translation to English for mixed Arabic/English inputs
* Category classification into:

  * coding_bug
  * coding_how_to
  * dev_env_tooling
  * sys_networks
  * data_ml_dl
  * theory_concept
  * other_technical
  * non_technical

The agent outputs:

* Routing metadata
* Steps with associated terminal commands (auto-extracted)
* Verification checklist
* Optional code block

### BERT Multilingual Classifier

* Supports Arabic, English, Egyptian dialect
* Sequence classification head with 5 target labels
* Supports dynamic loading from:

  * Local folder `BERT_BEST`
  * Hugging Face Hub model specified in `config.py`

---

## MLflow Tracking

### Logged items

| Type       | Examples                                       |
| ---------- | ---------------------------------------------- |
| Params     | model, temperature, timeout, complaint length  |
| Metrics    | runtime, token usage, technical flag           |
| Artifacts  | JSON complaint + parsed LLM response           |
| Error Logs | OpenAIError, missing API key, unexpected error |

Access your MLflow UI:

```
http://127.0.0.1:5000
```

Or DagsHub URL (example from your code):

```
https://dagshub.com/<repo>/mlflow
```

---

## Project Structure

```
.
├── app.py                     # Streamlit main app
├── config.py                  # Model config (HF model name)
├── BERT_BEST/                 # Optional local checkpoint
├── requirements.txt
├── .env
└── README.md
```


---

## Future Enhancements

* Add database storage for complaint history
* Add multilingual TTS replies
* Add admin dashboard for ticket statistics
* Deploy via Docker or Hugging Face Spaces
