---
name: clean-code
description: Enforces clean, simple Python code with max 150 lines per file for LLM projects. Use when implementing features, writing code, or refactoring. Ensures code stays simple and maintainable.
allowed-tools: Read, Write, Edit, Bash, Glob
---

# Clean Code for Python LLM Projects

Enforces clean, simple code for FastAPI + Streamlit + Bedrock LLM applications.

## STRICT RULES

### 1. MAX 150 LINES PER FILE
- **NEVER exceed 150 lines** (excluding imports/docstrings)
- Split immediately when approaching limit
- One responsibility per file
- Count lines before finishing - if over 150, refactor

### 2. Simple OOP
- Max 5-7 methods per class
- Composition over inheritance
- Pydantic BaseModel for all data
- Small focused classes

### 3. Functions
- Max 20 lines per function (ideal: 5-10)
- Max 3 parameters (use Pydantic for more)
- Type hints required everywhere
- Early returns, no deep nesting

---

## Project Structure (Aligned with new-llm-project)

```
project-name/
├── backend/
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── main.py              # FastAPI app entry (~50 lines)
│   ├── prompts/
│   │   └── __init__.py      # Langfuse get_prompt (~60 lines)
│   ├── models/
│   │   └── __init__.py      # Pydantic models (~80 lines)
│   └── utils/
│       └── utility.py       # call_llm function (~80 lines)
├── frontend/
│   ├── Dockerfile
│   ├── requirements.txt
│   └── app.py               # Streamlit app (~80 lines)
├── evaluation/
│   └── __init__.py
├── .env.example
└── README.md
```

---

## File Size Limits

| File | Target | Max |
|------|--------|-----|
| main.py | 40-60 | 100 |
| utility.py | 60-80 | 120 |
| models/__init__.py | 40-80 | 120 |
| prompts/__init__.py | 40-60 | 100 |
| app.py (Streamlit) | 60-80 | 120 |

**If any file exceeds 150 lines, STOP and split.**

---

## Code Patterns

### Pydantic Models (models/__init__.py)
```python
from pydantic import BaseModel, Field
from typing import List, Optional

class AnalysisResult(BaseModel):
    summary: str = Field(description="Brief summary")
    confidence: float = Field(ge=0, le=1)
    findings: List[str] = Field(description="Key findings")
```

### FastAPI Routes (main.py) - THIN
```python
@app.post("/analyze")
@observe()
def analyze_text(text: str) -> AnalysisResult:
    result = call_llm(
        prompt=f"Analyze: {text}",
        response_model=AnalysisResult
    )
    return result
```

### call_llm (utils/utility.py)
- AWS Bedrock client
- Pydantic model_json_schema() for JSON schema
- Exponential backoff: base_delay * (2 ** attempt)
- Langfuse @observe() decorator

### Prompts (prompts/__init__.py)
```python
def get_prompt(prompt_name: str, **variables) -> str:
    prompt = langfuse.get_prompt(prompt_name)
    return prompt.compile(**variables)
```

### Streamlit (frontend/app.py)
```python
st.title("LLM App")
if st.button("Analyze"):
    response = requests.post(f"{API_URL}/analyze", params={"text": text})
    st.write(response.json())
```

---

## Splitting Rules

When file grows too large, split by:

| Component | Split Strategy |
|-----------|---------------|
| models/ | requests.py, responses.py, entities.py |
| main.py | routes/analyze.py, routes/extract.py |
| utility.py | bedrock.py, retry.py |
| prompts/ | analysis_prompts.py, extraction_prompts.py |
| app.py | components/sidebar.py, components/results.py |

---

## Anti-Patterns

```python
# BAD: Everything in one giant function
def handle_request(data):
    # 200 lines of validation, LLM call, parsing...

# GOOD: Split responsibilities
def handle_request(request: AnalysisRequest) -> AnalysisResult:
    prompt = build_prompt(request)
    result = call_llm(prompt=prompt, response_model=AnalysisResult)
    return result
```

```python
# BAD: Inline prompts in code
prompt = f"""You are a helpful assistant...
{100 lines of prompt}..."""

# GOOD: Prompts via Langfuse
prompt = get_prompt("analysis-prompt", text=text)
```

---

## Checklist Before Commit

- [ ] No file > 150 lines
- [ ] All Pydantic models for data
- [ ] Type hints on all functions
- [ ] Routes are thin (delegate to services)
- [ ] Prompts managed via Langfuse
- [ ] @observe() decorators for tracing
- [ ] No duplicate code
- [ ] Exponential backoff on LLM calls

---

## Instructions for Claude

When writing code for LLM projects:

1. **Check file size** after every function you write
2. **Split early** - don't wait until 150 lines
3. **Use Pydantic** for all request/response models
4. **Keep routes thin** - business logic in utils/
5. **Prompts in prompts/** - never inline long prompts
6. **Type hints everywhere** - no untyped functions

> Simple code = Maintainable LLM apps
> Each file readable in under 2 minutes
