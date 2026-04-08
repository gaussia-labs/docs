---
title: Architecture
description: How pygaussia is designed
---

# Architecture

## Core Pattern

All metrics inherit from the `Gaussia` base class and follow the Template Method pattern:

```python
from pygaussia.core import Gaussia, Retriever
from pygaussia.schemas import Batch

class MyMetric(Gaussia):
    def batch(self, session_id, context, assistant_id, batch, language=None):
        for interaction in batch:
            # Evaluate each interaction
            result = self._evaluate(interaction)
            self.metrics.append(result)
```

## Data Flow

```
Retriever.load_dataset() -> list[Dataset]
    ↓
Gaussia._process() iterates datasets
    ↓
Metric.batch() processes each conversation
    ↓
Results in self.metrics
```

## Key Abstractions

| Class | Purpose |
|-------|--------|
| `Gaussia` | Base class for all metrics (Template Method) |
| `Retriever` | Abstract data loader |
| `Guardian` | Abstract bias detection interface |
| `Judge` | LLM-based evaluation with structured output |
| `StatisticalMode` | Pluggable statistics (Frequentist/Bayesian) |

## Design Patterns

- **Template Method**: `Gaussia` defines `_process()`, subclasses implement `batch()`
- **Strategy**: `FrequentistMode` / `BayesianMode` for statistical analysis
- **Adapter**: Guardian implementations adapt different models behind a common interface
- **Factory Method**: `MyMetric.run(RetrieverClass)` instantiates and orchestrates the pipeline

## Custom Retriever

Users implement a `Retriever` subclass to provide their data:

```python
from pygaussia.core.retriever import Retriever
from pygaussia.schemas.common import Dataset

class MyRetriever(Retriever):
    def load_dataset(self) -> list[Dataset]:
        # Load from database, API, files, etc.
        pass
```

## Data Structures

### Dataset

A conversation session:

```python
Dataset(
    session_id="session_001",
    assistant_id="my_assistant",
    language="english",
    context="Background information...",
    conversation=[Batch(...), Batch(...)],
)
```

### Batch

A single Q&A interaction:

```python
Batch(
    qa_id="qa_001",
    query="User question",
    assistant="Assistant response",
    ground_truth_assistant="Expected response",
)
```
