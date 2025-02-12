---
title: Automatic Dataset Generation
---

In this example, we show how to generate an evaluation dataset from a vectorstore.

```python
import datetime
import logging
from pathlib import Path
from time import perf_counter

from dotenv import load_dotenv

from continuous_eval.data_downloader import example_data_downloader
from continuous_eval.generators import SimpleDatasetGenerator
from continuous_eval.llm_factory import LLMFactory

load_dotenv()


def main():
    logging.basicConfig(level=logging.INFO)

    generator_llm = "gpt-4-1106-preview"
    num_questions = 10
    multi_hop_precentage = 0.25
    max_try_ratio = 3

    print(f"Generating a {num_questions}-questions dataset with {generator_llm}...")
    db = example_data_downloader("graham_essays/small/chromadb", Path("temp"), force_download=False)

    tic = perf_counter()
    dataset_generator = SimpleDatasetGenerator(
        vector_store_index=db,
        generator_llm=LLMFactory(generator_llm),
    )
    dataset = dataset_generator.generate(
        embedding_vector_size=1536,
        num_questions=num_questions,
        multi_hop_percentage=multi_hop_precentage,
        max_try_ratio=max_try_ratio,
    )
    toc = perf_counter()
    print(f"Finished generating dataset in {tic-toc:.2f}sec.")

    current_datetime = datetime.datetime.now().strftime("%Y%m%d_%H%M%S")
    output_directory = Path("generated_dataset")
    output_directory.mkdir(parents=True, exist_ok=True)
    fname = (
        output_directory / f"G_{generator_llm}_Q_{num_questions}_MH%_{multi_hop_precentage}_{current_datetime}.jsonl"
    )
    print(f"Saving dataset to {fname}")
    dataset.to_jsonl(fname)
    print(f"Done.")


if __name__ == "__main__":
    main()
```