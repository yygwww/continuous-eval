---
title: LLM-based Context Precision
---

### Definition

Context Precision is used to measure information density.


$$
\text{LLM-Based Context Precision} =
\frac{
  \text{Number of Relevant Chunks in Retrieved Sentences}
}{
  \text{Total Number of Sentences in Retrieved Contexts}
}
$$

$$
\text{LLM-Based Context Average Precision} =
\frac{
  \text{Number of Relevant Chunks in Retrieved Sentences}
}{
  \text{Total Number of Sentences in Retrieved Contexts}
}
$$


### Example Usage

Required data items: `question`, `retrieved_context`

```python
from continuous_eval.metrics import LLMBasedContextPrecision
from continuous_eval.llm_factory import LLMFactory

datum = {
    "question": "What is the capital of France?",
    "retrieved_contexts": [
        "Paris is the capital of France and also the largest city in the country.",
        "Lyon is a major city in France.",
    ],
}

metric = LLMBasedContextPrecision(LLMFactory("gpt-4-1106-preview"), log_relevance_by_context=True)
print(metric.calculate(**datum))
```

Note: optional variable `log_relevance_by_context` outputs `LLM_based_context_relevance_by_context` - the LLM judgement of relevance to answer the question per context retrieved.


### Sample Output



```JSON
{
    'LLM_based_context_precision': 0.5, 
    'LLM_based_context_average_precision': 1.0, 
    'LLM_based_context_relevance_by_context': [True, False]
}
```