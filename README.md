# Company Brochure Generator

![A laptop displaying a company website beside a printed brochure on a sunlit desk](assets/company-brochure-banner.png)

### From company websites to source-informed brochures

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=flat-square&logo=python&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?style=flat-square&logo=jupyter&logoColor=white)
![Groq API](https://img.shields.io/badge/Groq-API-F55036?style=flat-square)
![Model](https://img.shields.io/badge/Model-gpt--oss--20b-334155?style=flat-square)
![Chat Completions](https://img.shields.io/badge/AI-Chat_Completions-0F766E?style=flat-square)

[![View Notebook](https://img.shields.io/badge/View_Notebook-2563EB?style=for-the-badge)](company-brochure-generator.ipynb)
[![Read Brochure](https://img.shields.io/badge/Read_Brochure-0F766E?style=for-the-badge)](company-brochure.md)
[![Get Started](https://img.shields.io/badge/Get_Started-475569?style=for-the-badge)](#getting-started)

This project converts a company's public website into a concise, source-informed brochure for prospective customers, investors, and recruits. It collects website content, calls a large language model (LLM) to select relevant pages, and makes a second chat completion request to write a Markdown brochure.

The implementation is a step-by-step Jupyter notebook using the **Groq API** and **`openai/gpt-oss-20b`**. It demonstrates how to connect an LLM to a practical Python workflow, prepare useful context, process responses, and save a reviewable document.

## AI Engineering Focus: Chat Completions

This project covers the **chat completion component of AI engineering**: building role-based messages, sending inference requests to a hosted model, and using generated responses within an application.

| Skill demonstrated | Implementation |
| --- | --- |
| LLM API integration | Configures the Groq Python client and calls `client.chat.completions.create()` with `openai/gpt-oss-20b`. |
| Prompt design | Uses system messages to define behavior and user messages to supply company details, audience, tone, and website context. |
| Multiple LLM calls | Separates page selection from brochure writing, with each request serving a specific purpose. |
| Response processing | Parses link-selection JSON locally, checks its structure, and accepts only candidate URLs collected from the website. |
| Fallback behavior | Uses keyword-based page selection when the model returns empty or malformed JSON. |
| Context management | Limits candidate links, page count, source characters, and completion tokens to keep requests manageable. |
| Streaming chat completions | Includes an optional streaming request that updates the displayed brochure as text arrives. |
| Source-informed generation | Supplies collected page text and asks the model to support claims with source links. |

The project uses a hosted model for inference. Its scope is application development around chat completions; it does not train or fine-tune a model.

## What It Does

1. Accepts a company name, website URL, target audience, and writing tone.
2. Downloads the homepage and extracts readable text and same-domain links.
3. Calls the LLM to identify pages relevant to a company brochure.
4. Collects the selected pages and assembles a bounded set of source material.
5. Calls the LLM again to generate a brochure in Markdown.
6. Displays the result for review and saves it to a local `.md` file.
7. Optionally generates another version using streaming chat completions.

The brochure prompt requests an overview, products or services, customer benefits, and supported culture or careers information. It also asks for a call to action and source links, while instructing the model to omit unsupported claims.

## Technology Stack

| Technology | Purpose |
| --- | --- |
| Python 3.10+ | Workflow implementation |
| Jupyter Notebook | Step-by-step execution and explanation |
| Groq Python SDK | Hosted LLM chat completion requests |
| `openai/gpt-oss-20b` | Page selection and brochure generation |
| Requests | Public HTML page retrieval |
| Beautiful Soup | Text and link extraction |
| python-dotenv | Local API key configuration |
| IPython display | Markdown rendering and streaming updates |

## Project Files

```text
Company-Brochure-Generator/
├── company-brochure-generator.ipynb   # Documented project workflow
├── company-brochure.md                # Generated brochure output
└── README.md                         # Project overview and setup
```

Create a local `.env` file for credentials. Keep it and your virtual environment out of version control.

## Getting Started

### 1. Prepare the environment

Use Python 3.10 or newer and open `company-brochure-generator.ipynb` in VS Code or Jupyter with a Python kernel selected. The notebook's first code cell installs its dependencies into the current kernel:

```python
%pip install groq python-dotenv requests beautifulsoup4
```

### 2. Configure Groq credentials

Create an API key in the [Groq console](https://console.groq.com/keys), then create a `.env` file beside the notebook:

```dotenv
GROQ_API_KEY=your_groq_api_key_here
```

The notebook loads this value without printing it. An existing environment variable takes precedence over the `.env` file.

### 3. Choose a company

Update the configuration cell with the company you want to describe:

```python
COMPANY_NAME = "Your Company"
COMPANY_URL = "https://example.com"
TARGET_AUDIENCE = "prospective customers, investors, and recruits"
BROCHURE_TONE = "clear, professional, and welcoming"
```

### 4. Run the notebook manually

Run the cells in order. Inspect the homepage preview and selected links before proceeding to brochure generation. Model-selection and generation cells make Groq API requests; rerunning them makes additional requests. Collected website text is sent to Groq as model context.

### 5. Review and save

Check the generated claims and source links before sharing the brochure. The save cell writes `brochure_text` to `company-brochure.md` using UTF-8 and overwrites that file when rerun. Change the filename to retain multiple versions.

The optional streaming step stores its result separately in `streamed_brochure_text`. To save that version, explicitly use that variable in the save cell.

## Request Limits and Error Handling

The notebook currently limits collection to **two additional pages**, **40 candidate links**, and **1,500 characters per page**. Chat completion requests use a **2,048-token completion limit**. Character limits are approximate controls and do not guarantee a particular token count.

- **Token or rate limits:** reduce source limits and rebuild the source material before retrying. Wait when the account's available rate capacity has been consumed.
- **Malformed model JSON:** the link-selection function falls back to keyword matching without making an additional model request.
- **Additional page failures:** failed pages are recorded and skipped so the remaining sources can still be used.
- **Authentication or API errors:** these remain visible so the user can correct the credentials or request configuration.

## Limitations

The collector reads public, server-rendered HTML. It does not execute JavaScript, authenticate, extract PDF content, or read text from images. Collection limits may exclude useful information, and generated claims still require human verification. Use websites you are permitted to retrieve and follow their access rules.


