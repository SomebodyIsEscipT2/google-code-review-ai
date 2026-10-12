# 🔍 Google Code Review AI

> AI-powered code review with the precision of a Google Staff Engineer.

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.110+-green.svg)](https://fastapi.tiangolo.com/)
[![Tests](https://img.shields.io/badge/tests-237%20passing-brightgreen.svg)](https://github.com/SomebodyIsEscipT2/google-code-review-ai/actions)
[![CI](https://github.com/SomebodyIsEscipT2/google-code-review-ai/actions/workflows/tests.yml/badge.svg)](https://github.com/SomebodyIsEscipT2/google-code-review-ai/actions)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

**Every PR reviewed like a Staff Engineer saw it — automatically.** Google Code Review AI is a self-hosted review engine that combines free static analysis with deep AI-powered review to catch security flaws, logic bugs, and architecture issues before they hit production.

## The Problem

Code reviews are inconsistent. Junior devs miss security flaws. Senior devs don't have time to review every PR. Static linters catch formatting issues but miss logic bugs, race conditions, and subtle vulnerabilities. Meanwhile, one missed SQL injection costs you months.

## The Solution

**Google Code Review AI** combines free static analysis with deep AI-powered review to catch what humans and linters miss — before it hits production. Hook it into your GitHub PRs, CI pipeline, or call it directly as an API. Static checks cost nothing; AI review runs on your own Gemini or OpenAI key.

### What It Does

- 🔍 **Static + AI Hybrid** — Static analysis catches the obvious stuff for free. AI handles nuanced logic, security, and architecture
- 🤖 **Dual AI Provider** — Choose Google Gemini or OpenAI as your review engine
- 🔒 **Security-First** — Detects OWASP Top 10 vulnerabilities, hardcoded secrets, SQL injection, unsafe deserialization
- 🌐 **Multi-Language** — Python, JavaScript, TypeScript with auto-detection and language-specific rules
- 📝 **Inline Comments** — Line-by-line review comments with severity levels and fix suggestions
- 📊 **Git Diff Analysis** — Review only changed lines in a diff, focused and efficient
- 📈 **Review History & Analytics** — Track reviews, most common issues, languages analyzed
- 🔗 **GitHub Webhook** — Auto-review PRs on open/push with signature verification
- 📊 **Quality Scoring** — 0-100 score with severity breakdown

## Quick Start

```bash
git clone https://github.com/SomebodyIsEscipT2/google-code-review-ai.git
cd google-code-review-ai
pip install -r requirements.txt
cp .env.example .env   # add GEMINI_API_KEY or OPENAI_API_KEY for AI review
python main.py
```

Server at `http://localhost:8000` — Interactive docs at `/docs`.

Static analysis works out of the box with **no API key**. Add a Gemini or OpenAI key to unlock full AI review.

### Docker

```bash
docker compose up -d
```

## API Endpoints (11 Total)

### Code Review
| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/v1/review/code` | Full AI + static code review with quality score |
| `POST` | `/api/v1/review/inline` | Line-by-line review comments with severity & fixes |
| `POST` | `/api/v1/review/diff` | AI review of code diffs (PR-style) |

### Static Analysis
| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/v1/analyze/static` | Free static analysis (no AI cost) |
| `POST` | `/api/v1/analyze/diff` | Analyze only changed lines in a unified diff |

### Languages
| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/v1/languages` | List supported languages and their rules |

### History & Analytics
| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/v1/history` | Review history with filtering (language, date range) |
| `GET` | `/api/v1/analytics` | Aggregate stats — top issues, languages, avg scores |
| `GET` | `/api/v1/stats` | System stats and configuration |

### Infrastructure
| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/v1/health` | Health check with uptime |
| `POST` | `/api/v1/webhook/github` | GitHub PR webhook with signature verification |

## Example: Review Code

```bash
curl -X POST http://localhost:8000/api/v1/analyze/static \
  -H "Content-Type: application/json" \
  -d '{
    "code": "import pickle\ndata = pickle.loads(user_input)\nquery = f\"SELECT * FROM users WHERE id = {user_id}\"",
    "language": "python"
  }'
```

Detects: unsafe deserialization, SQL injection, f-string in query.

## Example: Inline Review

```bash
curl -X POST http://localhost:8000/api/v1/review/inline \
  -H "Content-Type: application/json" \
  -d '{
    "code": "password = \"admin123\"\neval(user_input)",
    "language": "python"
  }'
```

Returns line-by-line comments with severity levels and fix suggestions.

## Example: Diff Analysis

```bash
curl -X POST http://localhost:8000/api/v1/analyze/diff \
  -H "Content-Type: application/json" \
  -d '{
    "diff": "--- a/app.py\n+++ b/app.py\n@@ -1,3 +1,4 @@\n import os\n+import pickle\n+data = pickle.loads(request.data)\n query = \"SELECT * FROM users\""
  }'
```

Reviews only the changed lines — fast, focused, efficient.

## Architecture

```
google-code-review-ai/
├── main.py                              # FastAPI server
├── config/
│   └── settings.py                      # Env-based configuration
├── src/
│   ├── api/
│   │   └── routes.py                    # 11 API endpoints
│   ├── core/
│   │   ├── static_analyzer.py           # Pattern-based vulnerability detection
│   │   ├── inline_comments.py           # Line-by-line review engine
│   │   ├── language_rules.py            # Multi-language rule system
│   │   ├── diff_analyzer.py             # Git diff parsing & analysis
│   │   └── prompts.py                   # AI review prompt templates
│   ├── models/
│   │   └── schemas.py                   # Pydantic v2 schemas
│   └── services/
│       ├── review_service.py            # Review orchestration
│       └── history_service.py           # Review history & analytics
├── tests/                               # 237 tests across 9 files
│   ├── test_static_analyzer.py
│   ├── test_inline_comments.py
│   ├── test_language_rules.py
│   ├── test_diff_analyzer.py
│   ├── test_history.py
│   ├── test_routes.py
│   ├── test_schemas.py
│   ├── test_review_service.py
│   └── test_prompts.py
├── .github/workflows/tests.yml          # CI/CD
├── Dockerfile                           # Multi-stage build
├── docker-compose.yml
├── requirements.txt
└── LICENSE
```

## Multi-Language Support

| Language | Static Analysis | AI Review | Rules |
|----------|:-:|:-:|---|
| Python | ✅ | ✅ | Security, style, imports, type hints |
| JavaScript | ✅ | ✅ | Security, async patterns, DOM issues |
| TypeScript | ✅ | ✅ | Type safety, JS rules + TS-specific |

## Running Tests

```bash
python -m pytest tests/ -v
```

All 237 tests run offline — no API keys required for static analysis.

## Use Cases

- **PR Review Automation** — Auto-review every pull request via GitHub webhook
- **Security Scanning** — Catch vulnerabilities before they ship
- **Code Quality Gates** — Enforce quality scores in CI/CD pipelines
- **Team Onboarding** — Consistent review standards for new developers
- **Diff-Focused Review** — Review only what changed, not the entire file

## License

MIT — see [LICENSE](LICENSE).

---

**Built by [VCorv](https://vcorv.com)** — a technology company building tools for the AI era.

