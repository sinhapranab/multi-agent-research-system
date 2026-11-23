# Multi-Agent Research System

A Python-based multi-agent research pipeline that accepts a topic,
searches the web, reads a selected webpage, drafts a structured research
report, and evaluates the report with a critic chain.

## Features

-   **Web Search Agent:** Uses Tavily to find up to five results with
    titles, URLs, and snippets.
-   **Reader Agent:** Selects a relevant URL and extracts readable page
    text using Requests and Beautiful Soup.
-   **Research Writer:** Combines search results and scraped content
    into a structured report.
-   **Critic:** Scores the report and identifies strengths and areas for
    improvement.
-   **Command-line interface:** Run the complete workflow by entering a
    topic in the terminal.

## Tech Stack

-   Python
-   LangChain and LangChain Google GenAI
-   Google Gemini (`gemini-3.8-flash`, as configured in the code)
-   Tavily Search API
-   Requests and Beautiful Soup 4
-   python-dotenv
-   Rich

## Project Structure

``` text
multi-agent-research-system/
│
├── tools.py       # Tavily search and webpage-scraping tools
├── agents.py      # Search agent, reader agent, writer and critic chains
├── pipeline.py    # Orchestrates the research workflow
├── .env           # API keys (do not commit)
├── .gitignore
└── README.md
```

## How It Works

``` text
Research topic
     |
     v
1. Search Agent (Tavily)
     |
     v
2. Reader Agent (scrape a relevant URL)
     |
     v
3. Writer Chain (draft report)
     |
     v
4. Critic Chain (evaluate report)
     |
     v
Report and critic feedback
```

### 1. Search Agent

The `web_search` tool in `tools.py` sends the topic to Tavily and
requests up to five results. It returns each result's title, URL, and up
to 300 characters of content. `build_search_agent()` creates a LangChain
agent that can use this tool.

### 2. Reader Agent

The `scrape_url` tool downloads a URL, parses its HTML with Beautiful
Soup, removes script, style, navigation, and footer elements, and
returns up to 3,000 characters of extracted text. `build_reader_agent()`
creates an agent that can use this tool. The pipeline asks it to choose
a relevant URL from the search results and read it in more detail.

### 3. Writer Chain

The writer receives the topic, search results, and scraped content. It
generates a report containing:

-   Introduction
-   Key Findings (at least three explained points)
-   Conclusion
-   Sources (URLs found in the research)

### 4. Critic Chain

The critic evaluates the report and returns a score out of 10,
strengths, areas to improve, and a one-line verdict. The current
pipeline displays this feedback but does not automatically revise the
report.

## Installation and Setup

### 1. Clone the repository

``` bash
git clone https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
cd YOUR-REPOSITORY
```

Replace the example URL and directory with your GitHub repository
details.

### 2. Create and activate a virtual environment

**Windows PowerShell:**

``` powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

**macOS / Linux:**

``` bash
python3 -m venv venv
source venv/bin/activate
```

### 3. Install dependencies

``` bash
pip install langchain langchain-google-genai langchain-core tavily-python requests beautifulsoup4 python-dotenv rich
```

### 4. Configure API keys

Create a `.env` file in the project root:

``` env
GOOGLE_API_KEY=your_google_gemini_api_key
TAVILY_API_KEY=your_tavily_api_key
```

Get your Gemini API key from [Google AI
Studio](https://aistudio.google.com/) and your search API key from
[Tavily](https://tavily.com/).

Never commit your real API keys to GitHub.

## Run the Project

With the virtual environment activated, run this command from the
project root:

``` bash
python pipeline.py
```

Enter a topic when prompted, for example:

``` text
Enter a research topic: Generative AI applications in healthcare
```

The pipeline runs the search, reading, report-writing, and critic-review
stages and prints the results in the terminal.

## Configuration

The Gemini model is configured in `agents.py`:

``` python
llm = ChatGoogleGenerativeAI(
    model="gemini-3.8-flash"
)
```

Change the model identifier if you want to use another Gemini model
supported by your installed integration and API access.

The number of Tavily search results is configured in `tools.py`:

``` python
results = tavily.search(query=query, max_results=5)
```

The maximum amount of extracted webpage text is configured here:

``` python
return soup.get_text(separator=" ", strip=True)[:3000]
```

## Environment Variables

  Variable           Purpose
  ------------------ -----------------------------------------
  `GOOGLE_API_KEY`   Authenticates requests to Google Gemini
  `TAVILY_API_KEY`   Authenticates Tavily web searches

## Security and Responsible Use

-   Add `.env` to `.gitignore`; never publish API keys.
-   Respect website terms of service, robots policies, copyright, and
    rate limits.
-   Treat search results and scraped webpages as untrusted external
    content.
-   Only scrape content you are permitted to access.

Suggested `.gitignore`:

``` gitignore
.env
venv/
.venv/
__pycache__/
*.py[cod]
```

## Limitations

-   The reader agent is prompted to select a relevant URL, but the code
    does not independently verify source credibility.
-   Only the first 3,000 characters of extracted webpage text are passed
    back by the scraper.
-   Some websites may block automated requests or require JavaScript to
    render their content.
-   Search snippets and scraped pages can contain errors or outdated
    information. Verify important claims against original sources.
-   The critic provides feedback but does not currently initiate an
    automatic revision cycle.
-   The workflow runs sequentially.

## Future Improvements

-   Retrieve and compare multiple sources.
-   Add source validation, date checks, and claim-level citations.
-   Add retries and clearer handling for blocked or malformed webpages.
-   Save reports and critic feedback as Markdown or PDF files.
-   Add a writer-revision step based on critic feedback.
-   Build a Streamlit or FastAPI interface.
-   Add logging, automated tests, and configurable settings.

## Author

**Your Name**

-   GitHub: [Your GitHub Profile](https://github.com/YOUR-USERNAME)
-   LinkedIn: [Your LinkedIn
    Profile](https://www.linkedin.com/in/YOUR-PROFILE/)
