# LangChain Structured Output

This project demonstrates how to use LangChain's `with_structured_output()` method to get structured responses from Large Language Models (LLMs). It covers multiple schema definition approaches and different LLM providers.

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Project Structure](#project-structure)
- [Usage Guide](#usage-guide)
  - [1. Using Pydantic Schema](#1-using-pydantic-schema)
  - [2. Using TypedDict Schema](#2-using-typeddict-schema)
  - [3. Using JSON Schema](#3-using-json-schema)
  - [4. Using with Llama (HuggingFace)](#4-using-with-llama-huggingface)
- [Schema Definition Examples](#schema-definition-examples)
- [Environment Variables](#environment-variables)

## Overview

Structured output allows you to constrain LLM responses to match a predefined schema. This is useful for:
- Extracting specific information from text
- Getting consistent response formats
- Building reliable data pipelines with LLMs

## Prerequisites

- Python 3.9+
- API keys for the LLM providers you want to use:
  - Google AI API key (for Google Generative AI)
  - HuggingFace API token (for Llama models)

## Installation

### Step 1: Clone the Repository

```bash
git clone <repository-url>
cd LangChain-Structured-Output
```

### Step 2: Create a Virtual Environment (Recommended)

```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### Step 3: Install Dependencies

```bash
pip install langchain langchain-google-genai langchain-huggingface python-dotenv pydantic
```

### Step 4: Set Up Environment Variables

Create a `.env` file in the project root:

```env
GOOGLE_API_KEY=your_google_api_key_here
HUGGINGFACEHUB_API_TOKEN=your_huggingface_token_here
```

## Project Structure

```
LangChain-Structured-Output/
├── pydantic_demo.py              # Basic Pydantic model example
├── typeddict_demo.py             # Basic TypedDict example
├── json_schema.json              # Sample JSON schema
├── with_structured_output_pydantic.py   # Structured output using Pydantic
├── with_structured_output_typeddict.py  # Structured output using TypedDict
├── with_structured_output_json.py       # Structured output using JSON Schema
├── with_structured_output_llama.py      # Structured output with HuggingFace Llama
└── students_dataset.csv          # Sample dataset
```

## Usage Guide

### 1. Using Pydantic Schema

Pydantic provides the most feature-rich way to define schemas with validation and descriptions.

```bash
python with_structured_output_pydantic.py
```

**How it works:**
```python
from pydantic import BaseModel, Field
from typing import Optional, Literal

class Review(BaseModel):
    key_themes: list[str] = Field(description="Key themes in the review")
    summary: str = Field(description="Brief summary")
    sentiment: Literal["pos", "neg"] = Field(description="Sentiment")
    pros: Optional[list[str]] = Field(default=None, description="List of pros")
    cons: Optional[list[str]] = Field(default=None, description="List of cons")

structured_model = model.with_structured_output(Review)
result = structured_model.invoke("Your text here...")
```

### 2. Using TypedDict Schema

TypedDict offers a lightweight alternative using Python's typing module.

```bash
python with_structured_output_typeddict.py
```

**How it works:**
```python
from typing import TypedDict, Annotated, Optional, Literal

class Review(TypedDict):
    key_themes: Annotated[list[str], "Key themes description"]
    summary: Annotated[str, "Summary description"]
    sentiment: Annotated[Literal["pos", "neg"], "Sentiment description"]

structured_model = model.with_structured_output(Review)
result = structured_model.invoke("Your text here...")
```

### 3. Using JSON Schema

Define your schema as a JSON/dictionary object for maximum flexibility.

```bash
python with_structured_output_json.py
```

**How it works:**
```python
json_schema = {
    "title": "Review",
    "type": "object",
    "properties": {
        "key_themes": {
            "type": "array",
            "items": {"type": "string"},
            "description": "Key themes"
        },
        "summary": {"type": "string", "description": "Summary"},
        "sentiment": {"type": "string", "enum": ["pos", "neg"]}
    },
    "required": ["key_themes", "summary", "sentiment"]
}

structured_model = model.with_structured_output(json_schema)
result = structured_model.invoke("Your text here...")
```

### 4. Using with Llama (HuggingFace)

Use open-source models via HuggingFace integration.

```bash
python with_structured_output_llama.py
```

**How it works:**
```python
from langchain_huggingface import ChatHuggingFace, HuggingFaceEndpoint

llm = HuggingFaceEndpoint(
    repo_id="TinyLlama/TinyLlama-1.1B-Chat-v1.0",
    task="text-generation"
)

model = ChatHuggingFace(llm=llm)
structured_model = model.with_structured_output(Review)
```

## Schema Definition Examples

### Basic Pydantic Demo

Learn Pydantic basics with validation:

```bash
python pydantic_demo.py
```

### Basic TypedDict Demo

Understand TypedDict structure:

```bash
python typeddict_demo.py
```

## Environment Variables

| Variable | Description | Required For |
|----------|-------------|--------------|
| `GOOGLE_API_KEY` | Google AI Studio API key | Google Generative AI |
| `HUGGINGFACEHUB_API_TOKEN` | HuggingFace API token | Llama/HuggingFace models |

## Tips

1. **Choose the right schema type:**
   - Use **Pydantic** for complex validation and type coercion
   - Use **TypedDict** for simple, lightweight schemas
   - Use **JSON Schema** when working with external schema definitions

2. **Field descriptions matter:** Good descriptions help the LLM understand what data to extract.

3. **Use Optional fields:** Mark fields as `Optional` when the information might not always be present.

4. **Literal types:** Use `Literal` to constrain values to specific options (e.g., sentiment: "pos" or "neg").

## License

MIT License
