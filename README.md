# UIU Hybrid Chatbot

A Python assistant focused on **United International University (UIU)**. It combines locally cached website content with Google Gemini responses and an optional Google Search grounding fallback.

This individually developed project demonstrates website scraping, keyword-based retrieval, conversational context, and persistent local answer caching in a Jupyter notebook.

## Features

- Scrapes readable headings, paragraphs, and list items from the configured UIU webpage using Requests and Beautiful Soup.
- Saves extracted content in `uiu_data.txt` and reuses the local file on later runs.
- Retrieves local passages using keyword matching and sends them to Gemini as answer context.
- Requests user permission before enabling a web-search fallback when local information is unavailable or insufficient.
- Retains conversation history during the active session.
- Appends selected web-generated answers to the local cache for later retrieval.
- Retries rate-limit and server-error responses with exponential backoff.
- Uses a UIU-focused system instruction to guide topic scope.

## Technology stack

Python, Jupyter Notebook, Requests, Beautiful Soup, and the Gemini REST API. JSON, regular expressions, and file handling use Python's standard library.

## Files

| File | Purpose |
| --- | --- |
| `UIU_Chatbot.ipynb` | Chatbot implementation and interactive text-based chat loop |
| `uiu_data.txt` | Locally generated website content and saved answers; created during use |

## Setup

Create and activate a Python virtual environment, then install the dependencies:

```bash
python -m venv .venv
```

Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

macOS/Linux:

```bash
source .venv/bin/activate
```

```bash
pip install requests beautifulsoup4 jupyter
```

## Required configuration before running or publishing

The supplied notebook contains a hardcoded API credential and a key-specific startup comparison. Remove both before uploading it to a public repository. Revoke any previously exposed credential and use a replacement key through an environment variable.

Replace the `API_KEY` assignment with:

```python
API_KEY = os.environ.get("GEMINI_API_KEY", "")
```

Inside `main_chat_loop()`, replace the existing key-comparison condition and its error block with:

```python
if not API_KEY:
    print("Please set the GEMINI_API_KEY environment variable.")
    return
```

Set the environment variable before starting Jupyter.

Windows PowerShell:

```powershell
$env:GEMINI_API_KEY = "YOUR_API_KEY"
jupyter notebook
```

macOS/Linux:

```bash
export GEMINI_API_KEY="YOUR_API_KEY"
jupyter notebook
```

The notebook is configured with the model identifier `gemini-2.5-flash-preview-05-20`. Confirm that the configured model endpoint and search-grounding request format are supported by your account before running; update `API_URL` if necessary.

## Usage

1. Open `UIU_Chatbot.ipynb` in Jupyter.
2. Review the configuration and run the code cells.
3. On first use, the notebook fetches the configured UIU homepage and creates the local cache.
4. Enter a UIU-related question at the `You:` prompt.
5. Reply `yes` or `no` if asked whether to search the web.
6. Enter `exit` or `quit` to end the session.

Example questions:

- What admission information is available for UIU?
- What does the university website say about its programs?
- Where can I find information about tuition fees?

Answers depend on the content retrieved and on API access. The initial scraper fetches one configured webpage; it does not crawl all university pages.

## How it works

1. Load cached text or scrape the configured UIU webpage.
2. Look for local passages containing query keywords.
3. Ask Gemini to answer using the retrieved context and session history.
4. If local information is missing or detected as insufficient, request permission for a search-grounded API response.
5. Save eligible search-generated responses in the local file for reuse.

Gemini is used to formulate responses even when local context is found, so local retrieval does not provide offline chat. Conversation history is kept in memory for the current session; the text cache persists between sessions.

## Current limitations

- Retrieval uses keyword matching rather than embeddings or a vector database.
- In `search_local_data()`, the empty-string checks currently make every section enter the first search branch and prevent the intended second branch from running. Correct these conditions to distinguish saved API sections from scraped content.
- Cached model answers are not independently verified facts and may become outdated. Confirm important admissions, fees, and deadlines with official UIU sources.
- Source-citation display depends on the grounding metadata returned by the configured API; compatibility should be checked.
- The interface is an interactive text loop, with no dedicated web frontend.

## Publishing checklist

Remove all embedded credentials, clear notebook outputs, and review the notebook and cache for private information before committing. Suggested `.gitignore` entries:

```gitignore
.venv/
.env
__pycache__/
.ipynb_checkpoints/
uiu_data.txt
```

This README documents the supplied implementation and required setup changes; it does not modify the notebook code.
