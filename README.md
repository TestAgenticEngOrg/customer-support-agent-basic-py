# Customer Support Agent (Python)

A simple customer support agent for Acme Store. It answers questions about orders, returns and shipping, and creates support tickets when it can't help. It uses Claude through the WSO2 Agent Manager AI gateway.

The tools use mock data:

| Tool | What it does | Sample data |
|---|---|---|
| `get_order_status` | Looks up an order | `ORD-1001` (Shipped), `ORD-1002` (Processing), `ORD-1003` (Delivered) |
| `search_faq` | Answers common questions | returns, shipping, warranty, payment |
| `create_support_ticket` | Escalates to a human | Returns a ticket ID like `TCK-1A2B3C4D` |

## Deploy on Agent Manager

1. Create a **platform-hosted Python agent** from this repo: branch `main`, app path `/`, interface **Default** (`POST /chat` on port 8000). Don't add API keys as environment variables.
2. On the agent, open **Configure → + Add LLM Configuration** and attach an Anthropic LLM provider. Agent Manager then injects `ANTHROPIC_URL` and `ANTHROPIC_API_KEY`, so all model calls go through the AI gateway.
3. Build and deploy, then try it from **Try It** with the sample prompts below.

Optional: set `ANTHROPIC_MODEL` to use a model other than `claude-opus-5-5`.

## Sample prompts

Send these in order in the same session to see each tool and the conversation memory:

| Prompt | What to expect |
|---|---|
| Hi, where is my order ORD-1001? | Looks up the order: shipped, Wireless Headphones, arriving 2026-10-29 |
| And what about ORD-1002? | Uses the earlier context: processing, Mechanical Keyboard |
| Can you check ORD-9999? | Says the order wasn't found, without inventing details |
| What is your return policy? | Answers from the FAQ: 30 days, refunds in 5 to 7 business days |
| How long does express shipping take? | Answers from the FAQ: 1 to 2 business days |
| Do you accept PayPal? | Answers from the FAQ: yes |
| My headphones from ORD-1003 stopped working. I want to talk to a human. My email is test.user@example.com | Creates a support ticket and shares the ticket ID |
| Write me a poem about cats | Stays on topic and steers back to store support |

## Call it with curl

```bash
curl -X POST https://<agent-endpoint>/chat \
  -H "Content-Type: application/json" -H "X-API-Key: <key>" \
  -d '{"message": "Where is my order ORD-1001?", "session_id": "my-test-session"}'
```

`session_id` must be 8 to 128 characters. Reuse it to continue a conversation.

## Run locally

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
ANTHROPIC_API_KEY=<key> python main.py
```

Never commit API keys.
