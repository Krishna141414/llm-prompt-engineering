# Task 5: LLM & Prompt Engineering Notebook (VoidMain Internship)

## Goal
Brief: "Develop a useful LLM-based application while exploring tokens, context, temperature/model parameters, system instructions, zero-shot/few-shot prompting, contextual prompting and structured prompting. Compare prompt strategies and document observations."
**Keep it SIMPLE: ONE Jupyter notebook + README. No web app, no API keys, no deployment, no tests.**

## Setup
- Windows, Python 3.14, project in OneDrive. Keep `.venv` and model cache out of git. Empty `screenshots/.gitkeep`.
- Run a small open-source instruct LLM **locally** with Hugging Face `transformers` (no login, no API key) - pick a small model (~0.5-1.5B params, e.g. Qwen2.5-0.5B-Instruct or a newer equivalent) that runs on my laptop. Install the **CPU-only PyTorch build** (`pip install torch --index-url https://download.pytorch.org/whl/cpu`, ~200 MB) - do NOT install the CUDA build (~3 GB). The small model runs fine on CPU. Pin `requirements.txt`.

## Notebook `prompt_engineering.ipynb` (run top to bottom, outputs saved)
Use one simple useful task throughout: **hotel review analyzer** (sentiment + main topic + one-line summary). Use ~10 short hotel reviews written in the notebook with true labels.
1. **Intro:** what the "app" does, which model is used.
2. **Tokens & context:** tokenize a sample sentence (show tokens + IDs + count), show the model's context length, show how a long prompt costs more tokens.
3. **Temperature & parameters:** same prompt at temperature 0, 0.7, 1.2 (and top_p / max_new_tokens) - show outputs side by side.
4. **System instructions:** same request with and without a system message - compare.
5. **Prompt strategies:** zero-shot, few-shot (3 examples), contextual (adds hotel context), structured (ask for JSON) - run each on the 10 reviews.
6. **Comparison table:** per strategy: sentiment accuracy, valid-JSON rate (for structured), avg tokens, avg time. One simple bar chart.
7. **Observations & limitations:** 5-8 bullet points (what worked, what didn't, temperature effect, small-model limits).
8. **Final "app" cell:** a `analyze_review(text)` function using the best strategy, demoed on 2 new reviews.
- Charts in colors navy `#0B1020`, blue `#4F7CFF`, pink `#FF3D81`, marigold `#FFB020`, mint `#22D3B6`.

## README
What it is, model used, each concept in 1-2 lines, the comparison table, observations, how to run.

## Rules
- Do everything in one go without asking. Keep code short and commented.
- Run the notebook fully once (e.g. `jupyter nbconvert --to notebook --execute --inplace prompt_engineering.ipynb`) so outputs are saved, then commit and push.
- Finish with a SHORT summary of the comparison results. Then stop.
