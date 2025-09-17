# Manager-summarization-task1
# Manager — Conversation History & Summarization (Task 1)

[![Python](https://img.shields.io/badge/Python-3.10-blue?logo=python)]()
[![Run on Colab](https://img.shields.io/badge/Run%20on-Colab-F9AB00?logo=googlecolab\&logoColor=white)]()
[![Groq API](https://img.shields.io/badge/Powered%20by-Groq%20API-ff69b4)]()

---

## Project Summary

This repository contains a clean, well-documented Google Colab / Jupyter Notebook implementation for **Conversation Management with Summarization**. The solution maintains a running conversation history between a user and an assistant, provides flexible truncation strategies (by turns or by character/word length), and performs **periodic summarization** (after every *k* runs) to keep the transcript concise.

Designed and built for an assignment using the Groq OpenAI‑compatible API, the notebook demonstrates all required behaviors, shows sample runs, and exposes configuration variables so evaluators and recruiters can reproduce and test the logic easily.

---

## Table of Contents

1. [Key Features](#key-features)
2. [Repository Structure](#repository-structure)
3. [Quick Demo (What you will see)](#quick-demo)
4. [Prerequisites](#prerequisites)
5. [Installation & Setup](#installation--setup)
6. [Configuration (Colab & Local)](#configuration)
7. [How to Run — Step by Step](#how-to-run)
8. [Core Concepts & Implementation Notes](#core-concepts)
9. [Examples & Expected Output](#examples)
10. [Evaluation Checklist](#evaluation-checklist)
11. [Security & Best Practices](#security--best-practices)
12. [Troubleshooting](#troubleshooting)
13. [Contributing & License](#contributing--license)

---

## Key Features

* ✅ Running conversation buffer storing alternating user/assistant messages.
* ✅ Truncation options:

  * Keep last **N turns** (messages)
  * Keep up to **X characters** or **Y words**
* ✅ **Periodic summarization** after every **k-th** run (configurable). When triggered, the history is summarized and the summary replaces (or is stored alongside) the full history to prevent unbounded growth.
* ✅ Demonstrations with multiple sample conversations and outputs for different truncation settings.
* ✅ Clear configuration variables to reproduce different behaviors quickly.

---

## Repository Structure

```
Manager-summarization-task1/
├─ Manager_Task1.ipynb       # Primary Colab notebook with step-by-step cells
├─ README.md                 # This file
├─ assets/                   # (optional) screenshots / sample outputs
└─ requirements.txt          # Minimal dependencies (if used locally)
```

---

## Quick Demo (What you will see)

* Feed a sequence of dialogues into the notebook (sample conversations provided).
* Observe how the conversation history grows.
* Change truncation variables (e.g., keep last `5` turns or `500` characters).
* Set `SUMMARIZE_EVERY_K = 3` and watch: after the 3rd run, the notebook calls the Groq/OpenAI API to summarize the history, and the summary replaces the older detailed buffer.

---

## Prerequisites

* Google account (to run on Colab) or Python 3.8+ locally.
* `openai` (or Groq’s OpenAI-compatible client) installed in the environment.
* API key with permission to call the Groq/OpenAI compatible API.

---

## Installation & Setup

**Option A — Run in Google Colab (recommended for graders):**

1. Open `Manager_Task1.ipynb` in Colab.
2. In the first code cell, set your API key using an input cell or Colab secret manager (recommended). Example:

```python
import os
os.environ['OPENAI_API_KEY'] = 'YOUR_API_KEY_HERE'  # replace at runtime (avoid committing key)
```

3. Run all cells.

**Option B — Run locally:**

1. Clone the repository:

```bash
git clone https://github.com/Bhoomika685/Manager-summarization-task1.git
cd Manager-summarization-task1
```

2. (Optional) Create a virtual environment and activate it.
3. Install dependencies (example):

```bash
pip install -r requirements.txt
# or
pip install openai
```

4. Export your API key:

```bash
export OPENAI_API_KEY="your_api_key_here"   # Linux / macOS
setx OPENAI_API_KEY "your_api_key_here"     # Windows (restart shell)
```

5. Open `Manager_Task1.ipynb` in Jupyter and run cells.

---

## Configuration (important variables)

In the notebook you will find an early cell with configuration parameters. Typical variables and expected usage:

```python
# Truncation controls (choose one or combine logic)
TRUNCATE_BY_TURNS = 5        # keep the most recent 5 messages (user+assistant count as turns)
TRUNCATE_BY_CHARS = None     # alternatively, set an integer to limit characters
TRUNCATE_BY_WORDS = None     # or limit by words

# Periodic summarization
SUMMARIZE_EVERY_K = 3        # perform summarization after every k-th run

# Summaries storage options
REPLACE_HISTORY_WITH_SUMMARY = True  # True: replace the buffer with summary
STORE_SUMMARIES = False              # False: don't store separate historical summaries
```

Adjust these to see different behaviors in the demo cells.

---

## How to Run — Step by Step

1. **Open Colab**: Click the Colab badge or open the notebook file in Colab.
2. **Set API Key**: Run the first cell that asks for `OPENAI_API_KEY`. Use the runtime input or Colab secrets to avoid committing keys.
3. **Run Initialization**: Run the setup cell that imports modules and defines helper functions (history buffer, truncation, summary helper, API wrapper).
4. **Run Sample Cells**: There are demo cells that simulate multiple conversation turns. Run them in order to see how truncation and summarization behave.
5. **Adjust Config**: Change `TRUNCATE_BY_TURNS` / `TRUNCATE_BY_CHARS` / `SUMMARIZE_EVERY_K` and re-run the demo cells to observe differences.
6. **Inspect Outputs**: The notebook prints the full buffer, truncated buffer, and any summary produced. Screenshots are available in `assets/` (if present).

---

## Core Concepts & Implementation Notes

**1. Conversation Buffer**

* Stored as a list of message dicts: `{"role": "user"/"assistant", "text": "..."}`.

**2. Truncation**

* Two main strategies:

  * Keep the N most recent turns (fast, deterministic).
  * Keep up to X characters/words (smoother control over length).
* Truncation is applied before sending data to the summarization pipeline to bound token usage and cost.

**3. Periodic Summarization (k-th run)**

* A run counter increments each time the system processes a new exchange.
* When `run_count % SUMMARIZE_EVERY_K == 0`, trigger the summarization routine.
* The summarization routine sends the (truncated) buffer to the Groq/OpenAI completions endpoint with an instruction to produce a concise summary.
* The resulting summary replaces the buffer or is stored in a separate `summaries` list depending on configuration.

**4. API Usage & Cost Considerations**

* The notebook demonstrates batching and minimization of token usage by truncating prior to API calls.
* Example prompt templates are included in the notebook to ensure deterministic summarization.

---

## Examples & Expected Output

There are multiple demo cells in the notebook. Example behaviors you can expect:

* **Truncation by turns**: With `TRUNCATE_BY_TURNS = 3`, the printed buffer will show only the last three messages.
* **Truncation by characters**: With `TRUNCATE_BY_CHARS = 250`, the buffer is trimmed to approximately 250 characters.
* **Periodic summarization**: With `SUMMARIZE_EVERY_K = 3`, after the third demo conversation run you will see a printed summary like:

```text
[Summary after run 3]: User seeks help with resume & interview prep; assistant provided templates and resources. Key constraints: fresh grad, India, interested in software roles.
```

The notebook contains multiple such examples and prints both the raw and summarized buffers so evaluators can compare.

---

## Evaluation Checklist (Mapping to Assignment Requirements)

* [x] Maintain running conversation history ✅
* [x] Implement truncation by turns and by length ✅
* [x] Implement periodic summarization after every k-th run ✅
* [x] Demonstrate with multiple conversation samples and show outputs at different truncation settings ✅
* [x] Notebook is runnable in Colab; outputs visible ✅

---

## Security & Best Practices

* **Never commit API keys** to public repositories. Use environment variables or Colab secrets.
* If the assignment requires a test API key inside the notebook for graders, add a clear comment at the top asking graders to insert the key at runtime and **remove** before publishing.
* Prefer short-lived keys or use GitHub Actions secrets for CI.

---

## Troubleshooting

* **API errors (rate limit / invalid key)**: Check the `OPENAI_API_KEY` value and quota for your Groq/OpenAI-compatible account.
* **Notebook stalls**: Restart the Colab runtime and re-run cells from top.
* **Unexpected summarization results**: Inspect prompt templates; ensure truncation didn't remove essential context.

---

## Contributing

This repo is a demonstration/assignment submission. If you want to propose improvements, please open an issue or create a pull request. Suggested enhancements:

* Add unit tests for summarization logic.
* Add richer prompt templates for domain-aware summarization.
* Add optional support for hierarchical summaries (long-term -> medium -> short).

---

## License & Author

**Author:** Bhoomika Hemkar (GitHub: @Bhoomika685)



