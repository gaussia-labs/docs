---
title: Introduction
description: Gaussia Python SDK for AI evaluation
---

# pygaussia

The Python implementation of the Gaussia AI evaluation framework. It provides metrics for measuring fairness, toxicity, bias, conversational quality, and more for AI models and assistants.

## Installation

```bash
pip install gaussia
```

Or with specific metric extras:

```bash
pip install "gaussia[toxicity]"
pip install "gaussia[metrics]"    # all metrics
pip install "gaussia[all]"        # everything
```

## Quick Start

```python
from pygaussia.core.retriever import Retriever
from pygaussia.schemas.common import Dataset, Batch
from pygaussia.metrics.context import Context
from langchain_groq import ChatGroq

class MyRetriever(Retriever):
    def load_dataset(self) -> list[Dataset]:
        return [
            Dataset(
                session_id="session_001",
                assistant_id="my_assistant",
                language="english",
                context="Your knowledge base context here.",
                conversation=[
                    Batch(
                        qa_id="qa_001",
                        query="What is Gaussia?",
                        assistant="Gaussia is an AI evaluation framework.",
                        ground_truth_assistant="Gaussia is an AI evaluation framework for measuring fairness and quality.",
                    )
                ],
            )
        ]

model = ChatGroq(model="llama-3.3-70b-versatile", temperature=0)
results = Context.run(MyRetriever, model=model)
```

## Available Metrics

| Metric | Type | Description |
|--------|------|-------------|
| Toxicity | Data-based | Detects toxic language with fairness analysis |
| Bias | Guardian-based | Measures bias across protected attributes |
| Context | LLM-judge | Evaluates context alignment |
| Conversational | LLM-judge | Dialogue quality via Grice's maxims |
| Humanity | Data-based | Human-likeness via emotion distribution |
| BestOf | LLM-judge | Tournament-style response comparison |
| Agentic | LLM-judge | Agent behavior with Pass@K/Pass^K |
| Vision | Embedding-based | VLM similarity and hallucination detection |
| Regulatory | Hybrid | Compliance against regulatory documents |
