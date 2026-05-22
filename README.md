# 🔬 ResearchMind — Multi-Agent AI Research System

---

## ✨ What It Does

ResearchMind is an autonomous multi-agent research pipeline. Four specialized AI agents work in sequence — searching the web, scraping the best sources, writing a structured report, and then critically reviewing it with a score out of 10.

| Agent | Role | Tools |
|---|---|---|
| 🔍 **Search Agent** | Finds 5 recent, reliable web results on the topic | Tavily Search API |
| 📄 **Reader Agent** | Picks the best URL and deep-scrapes its content | BeautifulSoup + Requests |
| ✍️ **Writer Chain** | Combines research and drafts a structured report | Llama 3.3 70B via Groq |
| 🧠 **Critic Chain** | Reviews the report, scores it 1–10, gives feedback | Llama 3.3 70B via Groq |

---

## 🏗️ How It Works

```
  User Input: Research Topic
          │
          ▼
  ┌───────────────────────┐
  │   Step 1: Search Agent │
  │                       │
  │  Tavily API → 5 web   │
  │  results with titles, │
  │  URLs & snippets      │
  └──────────┬────────────┘
             │  search_results
             ▼
  ┌───────────────────────┐
  │   Step 2: Reader Agent │
  │                       │
  │  Picks best URL →     │
  │  Scrapes full content │
  │  (up to 3000 chars)   │
  └──────────┬────────────┘
             │  scraped_content
             ▼
  ┌───────────────────────┐
  │   Step 3: Writer Chain │
  │                       │
  │  Combines search +    │
  │  scraped data →       │
  │  Structured report    │
  │  (Intro · Findings ·  │
  │   Conclusion · URLs)  │
  └──────────┬────────────┘
             │  report
             ▼
  ┌───────────────────────┐
  │   Step 4: Critic Chain │
  │                       │
  │  Reviews report →     │
  │  Score: X/10          │
  │  Strengths            │
  │  Areas to Improve     │
  │  One-line verdict     │
  └──────────┬────────────┘
             │
             ▼
  ┌───────────────────────┐
  │   Streamlit UI        │
  │   + Download (.md)    │
  └───────────────────────┘
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **LLM** | Llama 3.3 70B via Groq (ultra-fast inference) |
| **Agents** | LangChain `create_react_agent` |
| **Web Search** | Tavily Search API (5 results per query) |
| **Web Scraping** | BeautifulSoup4 + Requests |
| **Chains** | LangChain LCEL (writer + critic) |
| **UI** | Streamlit |
| **Deployment** | Streamlit Community Cloud |

---

## 📁 Project Structure

```
multi-agent-system/
│
├── app.py              # Streamlit UI (ResearchMind frontend)
├── agents.py           # Search agent, Reader agent, Writer chain, Critic chain
├── tools.py            # web_search (Tavily) + scrape_url (BeautifulSoup) tools
├── pipeline.py         # Full pipeline orchestrator (CLI entry point)
├── main.py             # Alternative CLI runner
│
├── requirements.txt    # Python dependencies
└── .env                # API keys (never commit this)
```

---

## ⚡ Quick Start

### 1. Clone the repository

```bash
git clone https://github.com/imVikash-ai/multi-agent-system.git
cd multi-agent-system
```

### 2. Create a virtual environment

```bash
python -m venv venv

# Windows
venv\Scripts\activate

# Mac / Linux
source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Set up environment variables

Create a `.env` file in the project root:

```env
GROQ_API_KEY=your_groq_api_key_here
TAVILY_API_KEY=your_tavily_api_key_here
```

### 5. Run the Streamlit app

```bash
streamlit run app.py
```

Or run the CLI pipeline directly:

```bash
python pipeline.py
# Enter a research topic: LLM agents 2025
```

---

## 🔑 Getting API Keys

| Service | Get Key | Free Tier |
|---|---|---|
| **Groq** | [console.groq.com](https://console.groq.com) | ✅ Free — very fast inference |
| **Tavily** | [app.tavily.com](https://app.tavily.com) | ✅ Free — 1000 searches/month |

---

## 🎯 Example Output

**Input:** `"LLM agents 2025"`

**Writer Output:**
```
## Introduction
Large Language Model (LLM) agents have emerged as...

## Key Findings
1. Tool-use capabilities have expanded significantly...
2. Multi-agent collaboration frameworks like LangGraph...
3. Memory architectures now support long-horizon tasks...

## Conclusion
The trajectory of LLM agents in 2025 suggests...

## Sources
- https://arxiv.org/...
- https://blog.langchain.dev/...
```

**Critic Output:**
```
Score: 8/10

Strengths:
- Well-structured with clear sections
- Backed by real URLs from reliable sources

Areas to Improve:
- Could include more quantitative benchmarks
- Missing comparison with 2024 baselines

One line verdict:
A solid, well-researched report with room for deeper analysis.
```

---

## 🚀 Deploying to Streamlit Cloud

### 1. Push to GitHub (without `.env`)

```bash
git add .
git commit -m "deploy"
git push origin main
```

> ⚠️ Make sure `uv.lock` is deleted or in `.gitignore` — Streamlit Cloud will fail if it detects it.

### 2. Deploy on [share.streamlit.io](https://share.streamlit.io)

- Repository: `imVikash-ai/multi-agent-system`
- Branch: `main`
- Main file: `app.py`

### 3. Add secrets

Go to **App Settings → Secrets** and add:

```toml
GROQ_API_KEY = "your_groq_key_here"
TAVILY_API_KEY = "your_tavily_key_here"
```

---

## 🐛 Troubleshooting

### `No pyproject.toml found` on Streamlit Cloud
Remove `uv.lock` from the repo — Streamlit Cloud sees it and tries to use `uv` package manager instead of `requirements.txt`:
```bash
rm uv.lock
echo "uv.lock" >> .gitignore
git add . && git commit -m "remove uv.lock" && git push
```

### `ImportError: cannot import name 'create_agent'`
The correct function name is `create_react_agent`. Fix in `agents.py`:
```python
# Wrong
from langchain.agents import create_agent

# Correct
from langchain.agents import create_react_agent
```

### `AuthenticationError: Invalid API Key`
Double-check your `.env` file locally or Streamlit Secrets in production. Keys must have no extra spaces or quotes.

### Tavily returns no results
Some topics may return limited results on the free tier. Try rephrasing the topic to be more specific.

---

## 🗺️ Roadmap

- [ ] Add LangGraph for stateful multi-agent orchestration
- [ ] LangSmith integration for agent tracing & observability
- [ ] PDF export of final research report
- [ ] Add memory so agents learn from previous research sessions
- [ ] Support for multiple search engines (Brave, Serper)
- [ ] Async pipeline for faster parallel agent execution

---

## 👨‍💻 Author

**Vikash Kumar** — Gen AI Engineer

[![LinkedIn](https://img.shields.io/badge/LinkedIn-vikashkumar--ai-0A66C2?style=flat&logo=linkedin)](https://linkedin.com/in/vikashkumar-ai)
[![GitHub](https://img.shields.io/badge/GitHub-imVikash--ai-181717?style=flat&logo=github)](https://github.com/imVikash-ai)

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---
