# Mujeeb AI — Customer Service Assistant (RAG-based, Arabic)

A customer service chatbot built on RAG (Retrieval-Augmented Generation) technology. It answers inquiries regarding shipping, returns, payments, accounts, orders, support, and warranties—relying exclusively on a specific knowledge base—while seamlessly handing the conversation over to a human agent if a clear answer is unavailable.

## Why RAG instead of a standard LLM?

The bot never answers based on general knowledge. Every response is constructed solely from the FAQs retrieved as most relevant to the customer's query. If there is no sufficient match within the knowledge base, the bot explicitly acknowledges this and transfers the customer to a human agent rather than guessing; providing an incorrect answer regarding refunds or payments carries real business risk. ## Project Structure

```
business_faqs_arabic/
├── business_faqs_chatbot.py   # Entry point -- run this file
├── knowledge_base.json        # 15 common questions (extensible)
├── requirements.txt
├── .env                       # API key and model settings (never share this file)
└── .gitignore
```

## Setup

```bash
python -m venv venv
source venv/bin/activate        # On Windows: venv\Scripts\activate
pip install -r requirements.txt
```

Open the `.env` file and enter your key:

```
LLM_API_KEY=sk-your-key-here
LLM_MODEL=gpt-4o-mini
# LLM_BASE_URL=   # Only if using a provider other than OpenAI
```

## Running the Bot

```bash
python business_faqs_chatbot.py
```

Type your question and get an answer. Type `خروج` (Exit) to end the conversation.

Example questions to try:
- "How long does shipping take?"
- "What is your return policy?"
- "Do you sell on Mars?" (to test the human agent handover mechanism)

## Running without an API Key

If `LLM_API_KEY` is left blank, the bot continues to work—it simply returns the best
matching answer directly from the FAQ list, rather than a natural language response. Useful
for testing the retrieval mechanism without incurring API call costs.

## Using Another Provider

OpenAI is the default (no need for `LLM_BASE_URL`). To use another OpenAI-compatible provider,
configure both variables:

```
LLM_MODEL=deepseek-chat
LLM_BASE_URL=https://api.deepseek.com
```

Use only the provider's official domain. Never point `LLM_BASE_URL` to an unknown
third-party domain—doing so sends your API key to someone else's server.

## Customizing the Knowledge Base

Replace the content of `knowledge_base.json` with your company's actual questions,
while maintaining the same structure:

```json
{
"id": "faq001",
"category": "shipping",
"question": "How long does shipping take?",
"answer": "..."
}
```

No need to modify the code—the bot rebuilds the index from the file content
every time it starts.

## Configuring Retrieval Behavior

In the `business_faqs_chatbot.py` file:

- `TOP_K` — The number of FAQs retrieved for each question (default: 3).
- `SIMILARITY_THRESHOLD` — The similarity score required for the bot to answer
instead of handing off to a human agent (default: 0.45). Lower this value if the bot
is handing off too frequently, or raise it if it is answering questions it shouldn't.

## Deployment

This file runs as a standard Python script (text-based I/O), so it can easily be
integrated with:

- A web framework (FastAPI/Flask) that exposes `FAQChatbot.ask()` as an endpoint. - A messaging platform (WhatsApp Business, Telegram, Slack).
- Any server, Docker container, or platform that runs Python processes (Railway, Render, Fly.io, etc.).

In all cases, store `LLM_API_KEY` as a platform-level environment variable or secret—do not hardcode it or upload the `.env` file.

## Known Limitations

- `knowledge_base.json` is a static file; updating it requires a restart to rebuild the retrieval index.
- There is no persistent storage for conversation history; it resets with every restart.
- There is no authentication or rate-limiting system; implement these before deploying the tool publicly.
