# Sovereign Career Matcher (`sovereign-career-matcher`)

[![PyPI Version](https://img.shields.io/badge/pypi-v1.0.0-blue.svg)](pyproject.toml)
[![Tests](https://img.shields.io/badge/pytest-passing_100%25-brightgreen.svg)](tests/test_solution.py)
[![n8n Integration](https://img.shields.io/badge/n8n-workflow_ready-orange.svg)](n8n/workflow.json)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

> **Enterprise Standalone Package**: NLP-driven career optimization library. Scrapes and parses technical job postings, extracts skill requirements, matches them against a verified codebase knowledge vault, and renders customized ATS-optimized resumes in PDF and Markdown.

---

## 1. Overview & Architectural Blueprint

`sovereign-career-matcher` is an independently packaged, zero-dependency software library and microservice engineered as part of Russell Alan Powers' 10-year software engineering portfolio.

It delivers robust capabilities in **NLP & Resume Optimization** and provides seamless integration with n8n event workflows.

```
┌───────────────────────────┐         HTTP POST          ┌───────────────────────────────────────────┐
│        n8n Engine         │ ─────────────────────────> │        sovereign-career-matcher Adapter        │
│   (Port 5678 Webhook)     │ <───────────────────────── │             (Port 8796)                 │
└───────────────────────────┘       Idempotent JSON      └───────────────────────────────────────────┘
                                                                               │
                                                                               ▼
                                                         ┌───────────────────────────────────────────┐
                                                         │            CoreEngine Domain              │
                                                         │      (SHA-256 Idempotent Execution)       │
                                                         └───────────────────────────────────────────┘
```

---

## 2. Core Exported Classes & Features

- **Primary Module**: `from sovereign_career_matcher import JobDescriptionParser, SkillTaxonomyMatcher, ATSScoreSimulator, ResumePDFRenderer`
- **Deterministic Idempotency**: All executions generate unique SHA-256 idempotency tokens preventing duplicate runs across network retries.
- **Self-Contained Microservice**: Zero external third-party dependencies required for base execution.

---

## 3. Installation & Quickstart

```bash
# Clone the repository
git clone https://github.com/russellpowers/sovereign-career-matcher.git
cd career-matcher

# Install in editable mode
pip install -e .

# Verify health status via CLI
sovereign-career-matcher --health
```

---

## 4. CLI Usage Reference

```bash
# Check service health
sovereign-career-matcher --health

# Execute core domain action with a JSON payload
sovereign-career-matcher --exec process_data --payload '{"sample_key": "sample_value"}'
```

---

## 5. n8n Automation & Integration Contract

- **Microservice Port**: `http://localhost:8796`
- **Inbound Trigger Route**: `POST /api/v1/execute`
- **Integration Workflow**: `Job description URL received -> Parse technical requirements -> Query portfolio vault -> Output tailored ATS resume`

### How to Import into n8n:
1. Open your n8n canvas (`http://localhost:5678`).
2. Click **Workflows** > **Import from File**.
3. Select `n8n/workflow.json`.
4. Start the background webhook adapter:
   ```bash
   python3 n8n/webhook_adapter.py
   ```
5. Dispatch your test event to `http://localhost:5678/webhook/career-matcher-trigger`.

---

## 6. Verification & Automated Testing

This repository includes a 100% passing test suite runnable via `pytest` or `python3`:

```bash
# Run tests with pytest
pytest tests/test_solution.py -v

# Run tests directly (zero dependencies)
python3 tests/test_solution.py
```

---

## 7. Staff/Principal Engineer Technical Defense

> **60-Second Interview Pitch**:
> "Demonstrates natural language processing, semantic similarity matching, document synthesis, and strategic career positioning."
