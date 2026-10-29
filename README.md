# LLM & Prompt Engineering: Hotel Review Analyzer

A single Jupyter notebook ([prompt_engineering.ipynb](prompt_engineering.ipynb)) that builds a small LLM "app": give it a hotel review and it returns the **sentiment** (positive / negative / neutral), the **main topic** and a **one-line summary**. Along the way it explores the core prompt-engineering concepts and compares four prompt strategies.

**Model:** [`Qwen/Qwen2.5-0.5B-Instruct`](https://huggingface.co/Qwen/Qwen2.5-0.5B-Instruct), a 494M-parameter open-source instruct model. It runs **locally on CPU** with Hugging Face `transformers`, with no API key and no login.

## Concepts covered

| Concept | What the notebook shows |
|---|---|
| **Tokens** | LLMs read sub-word tokens, not words. *"The hotel staff were incredibly friendly!"* = 7 tokens (shown with their IDs). |
| **Context** | The model can attend to 32,768 tokens. Longer prompts cost more tokens (37 vs 76 for a short vs long prompt). |
| **Temperature / top_p / max_new_tokens** | Same prompt at temperature 0, 0.7 and 1.2: 0 is deterministic and 1.2 turns into gibberish. `top_p` limits sampling to likely tokens, and `max_new_tokens` caps the answer length. |
| **System instructions** | The same request with and without a system message: the system message sets the role, rules and length. |
| **Zero-shot** | Just the instruction, no examples. |
| **Few-shot** | 3 labelled example reviews before the real one. |
| **Contextual** | Adds background: the hotel, the topics it tracks, and what "neutral" means. |
| **Structured** | Asks for strict JSON (`sentiment`, `topic`, `summary`). |

## Comparison (6 labelled reviews, greedy decoding)

| Strategy | Sentiment accuracy | Valid JSON rate | Avg prompt tokens | Avg time (s) |
|---|---|---|---|---|
| Zero-shot | 0.83 | – | 74 | 3.60 |
| **Few-shot** | **1.00** | – | 178 | **2.57** |
| Contextual | 0.83 | – | 133 | 3.93 |
| Structured | 0.83 | 1.00 | 100 | 3.05 |

Times were measured on a laptop CPU. JSON was only requested in the structured strategy. The final `analyze_review(text)` function uses the best strategy (few-shot).

## Observations

- **Few-shot won (6/6).** The examples taught both the "neutral" label and the output format. It costs about 2.4× the prompt tokens of zero-shot, yet it was the fastest because its answers were short.
- **All misses were on mixed/neutral reviews.** Clear positive and negative reviews were always right. Zero-shot and structured called a mixed review negative, and contextual over-applied its "mixed = neutral" rule.
- **Structured output was 100% valid JSON**, which makes it the easiest to use from code, but topic labels were free-form.
- **Temperature:** 0 is identical every run, 0.7 rephrases while staying faithful, and 1.2 hallucinates and emits random multilingual tokens. `top_p=0.3` brings it back under control.
- **System message:** without one the answer rambles ("As an AI language model..."). With one it is short and on-task.
- **Small-model limits:** it occasionally leaks Chinese words, drifts on topic names, and takes about 3 s per review on CPU. With 6 test reviews, one mistake is worth 17 points, so treat the numbers as indicative.

## How to run (Windows)

```bash
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt          # installs the CPU-only PyTorch build
jupyter notebook prompt_engineering.ipynb
# or run it headless and save the outputs:
jupyter nbconvert --to notebook --execute --inplace prompt_engineering.ipynb
```

The model (~1 GB) downloads automatically on the first run into the Hugging Face cache (`~/.cache/huggingface`), outside the repo.
