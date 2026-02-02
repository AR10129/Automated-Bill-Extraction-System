# Automated Bill Extraction System

<div align="center">

**A High-Precision, Hybrid AI Pipeline for extracting structured data from complex medical bills.**

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat-square&logo=python&logoColor=white)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.109-009688?style=flat-square&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![Google Gemini](https://img.shields.io/badge/Gemini-2.0_Flash-orange?style=flat-square&logo=google&logoColor=white)](https://ai.google.dev/)
[![Docker](https://img.shields.io/badge/Docker-Enabled-2496ED?style=flat-square&logo=docker&logoColor=white)](https://www.docker.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)](LICENSE)

[Overview](#overview) • [Architecture](#system-architecture) • [Installation](#Installation & Setup) • [Project Structure](#Project-Structure) 

</div>

---

## Overview

The Automated Bill Extraction System is an enterprise-grade FastAPI service engineered to extract structured line-item data from medical bills, invoices, and financial documents. By leveraging a sophisticated hybrid architecture that combines **Google Gemini 2.0 Flash** multimodal vision AI with **Tesseract OCR** text recognition, the system delivers exceptional accuracy across diverse document formats including scanned images, multi-page PDFs, and complex layouts.

### Core Capabilities

**Hybrid Intelligence Processing**  
Utilizes dual-layer verification combining vision-based document understanding with optical character recognition to achieve superior extraction accuracy compared to single-method approaches.

**Enterprise-Scale Document Handling**  
Processes multi-page PDFs through intelligent batching algorithms that optimize API token usage while maintaining document context and structural integrity across pages.

**Resilient Error Handling**  
Features self-healing JSON parsing, automatic retry logic for transient failures, and graceful degradation to ensure consistent service availability in production environments.

**Cloud-Native Architecture**  
Fully containerized with Docker, designed for horizontal scaling, and optimized for deployment on modern cloud platforms including Render, AWS ECS, Azure Container Apps, and Google Cloud Run.


## System Architecture

```
┌──────────────────┐
│ Client Request   │  POST /extract-bill-data
│ (URL or File)    │  { "document": "https://..." }
└────────┬─────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│         FastAPI Application Layer           │
│  • URL Validation & File Download          │
│  • MIME Type Detection (Magic Bytes)       │
└────────┬────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│         Document Preprocessing              │
│  • PDF → Images (pdf2image + Poppler)      │
│  • Image Enhancement (Pillow)               │
│  • Page Batching (2 pages per batch)       │
└────────┬────────────────────────────────────┘
         │
         ▼
┌──────────────────────────────────────────────────────┐
│         Hybrid Extraction Engine                     │
│                                                      │
│  ┌─────────────────────┐   ┌──────────────────────┐│
│  │  Gemini 2.0 Flash   │   │   Tesseract OCR      ││
│  │  Vision Analysis    │   │   Text Extraction    ││
│  │  • Layout detection │   │   • Raw text         ││
│  │  • Table parsing    │   │   • Validation       ││
│  │  • Context analysis │   │   • Cross-check      ││
│  └──────────┬──────────┘   └──────────┬───────────┘│
│             │                         │            │
│             └──────────┬──────────────┘            │
│                        ▼                           │
│              ┌──────────────────┐                  │
│              │  Data Fusion     │                  │
│              │  & Validation    │                  │
│              └──────────────────┘                  │
└────────┬─────────────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│       JSON Repair & Schema Validation       │
│  • json_repair for malformed JSON           │
│  • Pydantic validation                      │
│  • Item count recalculation                 │
└────────┬────────────────────────────────────┘
         │
         ▼
┌──────────────────┐
│ Structured JSON  │  {
│ Response         │    "is_success": true,
│                  │    "data": { ... },
│                  │    "token_usage": { ... }
│                  │  }
└──────────────────┘
```

---

## 📦 Installation & Setup

### Prerequisites

- Python 3.10 or higher
- Tesseract OCR installed
- Poppler utilities installed
- Google Gemini API key

### Local Development

1. **Clone the Repository**
```bash
git clone https://github.com/AR10129/Automated-Bill-Extraction-System.git
cd Automated-Bill-Extraction-System
```

2. **Install System Dependencies**

**Ubuntu/Debian:**
```bash
sudo apt-get update
sudo apt-get install tesseract-ocr poppler-utils
```

**macOS:**
```bash
brew install tesseract poppler
```

**Windows:**
- Download Tesseract from [GitHub](https://github.com/UB-Mannheim/tesseract/wiki)
- Download Poppler from [oschwartz10612](https://github.com/oschwartz10612/poppler-windows/releases/)

3. **Install Python Dependencies**
```bash
pip install -r requirements.txt
```

4. **Configure Environment Variables**

Create a `.env` file in the root directory:
```env
GOOGLE_API_KEY=your_gemini_api_key_here
PORT=8000
HOST=0.0.0.0
```

5. **Run the Development Server**
```bash
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

The API will be available at `http://localhost:8000`

### Docker Deployment

1. **Build the Docker Image**
```bash
docker build -t bill-extraction-api .
```

2. **Run the Container**
```bash
docker run -d -p 8000:8000 \
  -e GOOGLE_API_KEY=your_api_key \
  --name bill-extractor \
  bill-extraction-api
```


## 📂 Project Structure

```
Automated-Bill-Extraction-System/
│
├── app/
│   ├── __init__.py              # Package initializer
│   ├── main.py                  # FastAPI app & API routes
│   ├── extractor.py             # Core extraction logic (Gemini + OCR)
│   ├── schemas.py               # Pydantic data models
│   └── static/
│       └── index.html           # Web UI for testing
│
├── tests/
│   ├── test_api.py              # API endpoint tests
│   └── test_models.py           # Model validation tests
│
├── .env                         # Environment variables (not in git)
├── .env.example                 # Template for environment vars
├── Dockerfile                   # Production container config
├── docker-compose.yml           # Multi-container orchestration
├── render.yaml                  # Render.com deployment config
├── requirements.txt             # Python dependencies
├── keep_alive.py                # Health check script for Render
└── README.md                    # This file
```

---

