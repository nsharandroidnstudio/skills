---
name: new-llm-project
description: Scaffold a new LLM project with FastAPI backend, Streamlit frontend, Docker, AWS Bedrock integration, Pydantic JSON outputs, exponential backoff retry, and Langfuse prompt management. Use when creating a new AI/LLM project or when user mentions "new project", "project setup", or "scaffold".
allowed-tools: Read, Write, Edit, Bash, Glob
---

# New LLM Project Scaffolding

## Overview
This skill creates a standardized project structure for LLM-based applications using AWS Bedrock, Pydantic, and Langfuse.

## Project Structure

```
project-name/
├── backend/
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── main.py
│   ├── prompts/
│   │   └── __init__.py
│   ├── models/
│   │   └── __init__.py
│   └── utils/
│       ├── __init__.py
│       └── utility.py          # Contains call_llm function
├── frontend/
│   ├── Dockerfile
│   ├── requirements.txt
│   └── app.py                  # Streamlit application
├── evaluation/
│   └── __init__.py
├── .env.example
└── README.md
```

## Core Components

### 1. utility.py - LLM Calling Function

The `call_llm` function includes:
- AWS Bedrock client initialization
- Pydantic model validation for structured JSON output
- Exponential backoff retry logic
- Langfuse `@observe()` decorator for tracing

```python
import boto3
import json
import time
from typing import TypeVar, Type
from pydantic import BaseModel
from langfuse import get_client
from langfuse.decorators import observe

T = TypeVar('T', bound=BaseModel)

# Initialize Langfuse client
langfuse = get_client()

# Initialize Bedrock client
bedrock_client = boto3.client("bedrock-runtime", region_name="us-east-1")


@observe(as_type="generation")
def call_llm(
    prompt: str,
    response_model: Type[T],
    model_id: str = "anthropic.claude-3-sonnet-20240229-v1:0",
    max_retries: int = 3,
    base_delay: float = 1.0,
    temperature: float = 0.0,
    max_tokens: int = 4096
) -> T:
    """
    Call LLM via AWS Bedrock with JSON output, exponential backoff retry, and Langfuse tracing.

    Args:
        prompt: The prompt to send to the LLM
        response_model: Pydantic model class for structured output
        model_id: AWS Bedrock model ID
        max_retries: Maximum retry attempts with exponential backoff
        base_delay: Base delay in seconds for exponential backoff
        temperature: Model temperature (0.0 for deterministic JSON)
        max_tokens: Maximum tokens to generate

    Returns:
        Parsed Pydantic model instance
    """
    # Build JSON schema from Pydantic model
    json_schema = response_model.model_json_schema()

    system_prompt = f"""You are a helpful assistant. You MUST respond with valid JSON only.
Your response must conform to this JSON schema:
{json.dumps(json_schema, indent=2)}

Do not include any text before or after the JSON. Only output the JSON object."""

    for attempt in range(max_retries):
        try:
            # Format the request payload using Claude's native structure
            native_request = {
                "anthropic_version": "bedrock-2023-05-31",
                "max_tokens": max_tokens,
                "temperature": temperature,
                "system": system_prompt,
                "messages": [
                    {
                        "role": "user",
                        "content": [{"type": "text", "text": prompt}]
                    }
                ]
            }

            # Convert to JSON and invoke model
            request = json.dumps(native_request)
            response = bedrock_client.invoke_model(modelId=model_id, body=request)

            # Decode the response body
            model_response = json.loads(response["body"].read())
            content = model_response["content"][0]["text"]

            # Parse and validate with Pydantic
            parsed = response_model.model_validate_json(content)
            return parsed

        except Exception as e:
            if attempt < max_retries - 1:
                delay = base_delay * (2 ** attempt)  # Exponential backoff
                print(f"Attempt {attempt + 1} failed: {e}. Retrying in {delay}s...")
                time.sleep(delay)
            else:
                raise RuntimeError(f"LLM call failed after {max_retries} attempts: {e}")
```

### 2. Prompts Module - Langfuse Integration

Prompts are managed via Langfuse and fetched at runtime:

```python
# backend/prompts/__init__.py
from langfuse import get_client

langfuse = get_client()


def get_prompt(prompt_name: str, **variables) -> str:
    """
    Fetch prompt from Langfuse and compile with variables.

    Args:
        prompt_name: Name of the prompt in Langfuse
        **variables: Variables to compile into the prompt template

    Returns:
        Compiled prompt string
    """
    prompt = langfuse.get_prompt(prompt_name)
    return prompt.compile(**variables)


def get_chat_prompt(prompt_name: str, **variables) -> list:
    """
    Fetch chat prompt from Langfuse and compile with variables.

    Args:
        prompt_name: Name of the chat prompt in Langfuse
        **variables: Variables to compile into the prompt template

    Returns:
        List of chat messages
    """
    prompt = langfuse.get_prompt(prompt_name, type="chat")
    return prompt.compile(**variables)


def get_prompt_with_version(prompt_name: str, version: int = None, label: str = None, **variables) -> str:
    """
    Fetch specific version or labeled prompt from Langfuse.

    Args:
        prompt_name: Name of the prompt in Langfuse
        version: Specific version number (optional)
        label: Label like "staging" or "production" (optional)
        **variables: Variables to compile into the prompt template

    Returns:
        Compiled prompt string
    """
    if version:
        prompt = langfuse.get_prompt(prompt_name, version=version)
    elif label:
        prompt = langfuse.get_prompt(prompt_name, label=label)
    else:
        prompt = langfuse.get_prompt(prompt_name)

    return prompt.compile(**variables)
```

### 3. Pydantic Response Models

Example response models in `backend/models/`:

```python
# backend/models/__init__.py
from pydantic import BaseModel, Field
from typing import List, Optional


class AnalysisResult(BaseModel):
    """Example response model for analysis tasks."""
    summary: str = Field(description="Brief summary of the analysis")
    confidence: float = Field(ge=0, le=1, description="Confidence score 0-1")
    findings: List[str] = Field(description="List of key findings")
    recommendation: Optional[str] = Field(default=None, description="Action recommendation")


class ExtractionResult(BaseModel):
    """Example response model for data extraction tasks."""
    entities: List[str] = Field(description="Extracted entities")
    relationships: List[dict] = Field(description="Relationships between entities")
    metadata: dict = Field(default_factory=dict, description="Additional metadata")
```

### 4. Example Usage

```python
# backend/main.py
from fastapi import FastAPI
from langfuse.decorators import observe
from utils.utility import call_llm
from models import AnalysisResult
from prompts import get_prompt

app = FastAPI()


@app.post("/analyze")
@observe()
def analyze_text(text: str) -> AnalysisResult:
    """Analyze text and return structured result."""

    # Get prompt from Langfuse (optional - can also use inline prompts)
    # prompt_template = get_prompt("text-analysis", text=text)

    # Call LLM with Pydantic model for structured JSON output
    result = call_llm(
        prompt=f"Analyze the following text and provide insights:\n\n{text}",
        response_model=AnalysisResult,
        temperature=0.0  # Deterministic for JSON
    )

    return result
```

### 5. Docker Configuration

**backend/Dockerfile:**
```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**frontend/Dockerfile:**
```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8501

CMD ["streamlit", "run", "app.py", "--server.port=8501", "--server.address=0.0.0.0"]
```

**frontend/requirements.txt:**
```
streamlit>=1.32.0
requests>=2.31.0
python-dotenv>=1.0.0
```

**frontend/app.py:**
```python
import os
import streamlit as st
import requests

# Backend API URL (use environment variable or default to localhost)
API_URL = os.getenv("BACKEND_URL", "http://localhost:8000")

# Configure page
st.set_page_config(
    page_title="LLM Application",
    page_icon="🤖",
    layout="wide"
)

st.title("🤖 LLM Application")

# Input section
user_input = st.text_area("Enter your text:", height=150)

if st.button("Analyze", type="primary"):
    if user_input:
        with st.spinner("Processing..."):
            try:
                response = requests.post(
                    f"{API_URL}/analyze",
                    params={"text": user_input}
                )
                response.raise_for_status()
                result = response.json()

                # Display results
                st.subheader("Results")
                st.write(f"**Summary:** {result.get('summary', 'N/A')}")
                st.write(f"**Confidence:** {result.get('confidence', 'N/A')}")

                if result.get('findings'):
                    st.write("**Findings:**")
                    for finding in result['findings']:
                        st.write(f"- {finding}")

                if result.get('recommendation'):
                    st.info(f"**Recommendation:** {result['recommendation']}")

            except requests.exceptions.RequestException as e:
                st.error(f"Error connecting to backend: {e}")
    else:
        st.warning("Please enter some text to analyze.")
```

### 6. Required Dependencies

**backend/requirements.txt:**
```
boto3>=1.34.0
pydantic>=2.0.0
langfuse>=2.0.0
fastapi>=0.109.0
uvicorn>=0.27.0
python-dotenv>=1.0.0
```

### 7. Environment Variables

**.env.example:**
```bash
# AWS Bedrock
AWS_ACCESS_KEY_ID=your_access_key
AWS_SECRET_ACCESS_KEY=your_secret_key
AWS_REGION=us-east-1

# Langfuse
LANGFUSE_PUBLIC_KEY=your_public_key
LANGFUSE_SECRET_KEY=your_secret_key
LANGFUSE_HOST=https://cloud.langfuse.com

# Frontend (optional - defaults to localhost:8000)
BACKEND_URL=http://localhost:8000
```

## Instructions for Claude

When the user asks to create a new LLM project:

1. Ask for the project name if not provided
2. Create the full directory structure as shown above
3. Generate all files with proper implementations
4. Ensure `call_llm` function includes:
   - AWS Bedrock client with `boto3.client("bedrock-runtime")`
   - Pydantic `model_json_schema()` for JSON schema generation
   - Pydantic `model_validate_json()` for response parsing
   - Exponential backoff retry: `base_delay * (2 ** attempt)`
   - Langfuse `@observe(as_type="generation")` decorator for tracing
5. Set up prompts module with Langfuse `get_prompt()` and `compile()`
6. Create example Pydantic models for structured outputs
7. Set up Streamlit frontend with app.py connecting to backend API
8. Set up Dockerfile for both backend (FastAPI) and frontend (Streamlit)
9. Add .env.example with required environment variables
10. Remind user to:
    - Configure AWS credentials
    - Set up Langfuse account and add prompts in dashboard
    - Update model_id if using different Claude version
    - Run backend: `uvicorn main:app --reload` (port 8000)
    - Run frontend: `streamlit run app.py` (port 8501)
    - Set `BACKEND_URL` env var if backend is not on localhost:8000
