# LangChain Tools 🛠️

A hands-on Jupyter Notebook exploring built-in and custom tools in LangChain, including input schemas, validation, and tool execution.

## Overview

Tools allow AI applications to interact with external systems, retrieve information, perform calculations, and execute actions beyond an LLM's native capabilities.

This project demonstrates direct tool invocation and three approaches to creating custom tools.

## 1. Built-in Tools

| Tool | Purpose | Advantages | Limitations |
|---|---|---|---|
| `DuckDuckGoSearchRun` | Retrieve web search results | Access to recent information | Requires network access; results may be unreliable |
| `ShellTool` | Execute shell commands | Useful for local automation and file operations | Security risks; requires restricted permissions |

The notebook demonstrates web search for geopolitical news and a shell command to list directory contents.

## 2. Custom Tools

The notebook implements a division calculator using three approaches:

### Tool Decorator (`@tool`)

Converts a Python function into a LangChain tool. Type hints define the input types, while the docstring describes the tool's purpose.

```python
from langchain_core.tools import tool

@tool
def division(numerator: int, denominator: int) -> float:
    """Divide two integers."""
    return numerator / denominator

print(division.invoke({"numerator": 25, "denominator": 5}))
# 5.0
```

**Advantages:** Simple syntax, minimal boilerplate, automatic schema generation.

**Limitations:** Less control over advanced execution behavior than a custom class.

### StructuredTool

Uses `StructuredTool.from_function()` to wrap an existing function with an explicit Pydantic input schema (`DivisionInputs`). Field descriptions explain the meaning of each argument.

**Advantages:** Explicit input validation, reusable functions, customizable argument descriptions.

**Limitations:** Requires additional schema definitions and configuration.

### BaseTool

Creates a custom class by inheriting from `BaseTool` and defining the tool name, description, argument schema, and `_run()` method.

**Advantages:** Greater control over execution, class-based organization, and extensibility. Async behavior can also be implemented when needed.

**Limitations:** More boilerplate and maintenance; better suited to complex tools.

## 3. Comparison

| Approach | Complexity | Best use case |
|---|---|---|
| `@tool` | Low | Simple Python functions |
| `StructuredTool` | Medium | Existing functions with explicit schemas |
| `BaseTool` | Higher | Complex, reusable tools with custom behavior |

## 4. Tool Metadata and Input Schema

- `name`: Identifies the tool.
- `description`: Helps an LLM understand when to use it.
- `args`: Describes the expected inputs.
- `args_schema.model_json_schema()`: Exposes the input schema in JSON Schema format.
- `invoke()`: Executes the tool with the supplied input.

## 5. How LLM Tool Calling Works

```text
User Query → LLM → Tool Selection + Arguments
                 → Application Executes Tool
                 → Tool Result → LLM → Final Response
```

A tool-capable model can be configured with `bind_tools()`. The application or agent executes the requested tool and returns its result to the model. This is a conceptual extension of the notebook, which currently demonstrates direct invocation rather than an LLM-driven agent workflow.

## Getting Started

```bash
git clone https://github.com/priyankvora8274/langchain-tools.git
cd langchain-tools
pip install langchain langchain-community pydantic langchain-core duckduckgo-search ddgs langchain-experimental
```

Open `langchain_tools.ipynb` in Jupyter Notebook or Google Colab and execute the cells in order. Web search requires internet access, and shell execution depends on the operating environment.

## Notes and Best Practices

- Use descriptive tool names, docstrings, and typed inputs.
- Python division (`/`) returns a float for integer inputs; use `-> float` rather than `-> int`.
- Validate the denominator to prevent division by zero.
- In Pydantic v2, use `Field(description="...")` without `required=True` for fields with no default.
- Restrict shell commands and require approval for sensitive actions. Avoid giving an LLM unrestricted shell access.

## Learning Objectives

Understand built-in tools, custom tool creation, Pydantic schemas, tool execution, and the foundations of tool calling and AI agents.
