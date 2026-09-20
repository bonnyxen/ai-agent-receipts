![AI Agent Receipts](assets/repo-banner.png)

# AI Agent Receipts

A tiny, testable inbox agent that turns a customer message into an evidence-backed draft for human review.

Most agent demos show the answer. This one also shows where the answer came from, what is still unknown, and what should stop the draft from moving forward.

## What it produces

Every inquiry becomes a structured case file with:

- the customer's stated need
- 1–3 exact quotes used as evidence
- missing information
- risk flags
- a draft reply
- a `review` or `hold` decision

The agent never sends the message. A person keeps the final button.

```text
inquiry → case file → deterministic checks → human review
```

## Try it in 30 seconds

The demo is offline, requires no API key, and validates the included example:

```bash
git clone https://github.com/bonnyxen/ai-agent-receipts.git
cd ai-agent-receipts
python agent.py --demo
python -m unittest -v test_agent
```

You should see `"checks": ["passed"]` and six passing tests.

## Run it with an OpenAI model

Install the SDK:

```bash
python -m pip install -r requirements.txt
```

Set your API key and a model available to your API account:

**macOS / Linux**

```bash
export OPENAI_API_KEY="your-key"
export OPENAI_MODEL="your-model"
python agent.py --live examples/inquiry.txt
```

**Windows PowerShell**

```powershell
$env:OPENAI_API_KEY="your-key"
$env:OPENAI_MODEL="your-model"
python agent.py --live examples/inquiry.txt
```

Replace `examples/inquiry.txt` with your own text file when you are ready.

The live route uses the Responses API with a strict JSON schema, then runs local checks over the result. Structured output makes the shape predictable; the checks test claims the schema cannot prove by itself.

## The checks

`verify_case()` rejects a case when:

- an evidence quote does not appear in the original message
- the draft contains an unapproved price, promise, or numeric deadline
- instruction-like text in the customer message is not held for review
- the draft is empty or longer than 1,200 characters
- required fields are missing or have the wrong type
- missing information is listed but the draft asks no question

These are deliberately plain Python checks. They are visible, easy to change, and cheap to run.

## Make it yours

Edit `SERVICE_RULES` before using the agent for a real business. Replace the fictional studio rules with your real services, boundaries, review policy, and claims you never want the model to make.

Then add tests for your own failure cases. If an incorrect price, promise, medical claim, or legal statement would matter to your workflow, turn it into a test before you trust the agent with real inputs.

## Files

```text
agent.py                    prompt, schema, model call, and checks
test_agent.py               six offline tests
examples/inquiry.txt        fictional customer message
examples/case-file.json     expected structured result
.github/workflows/tests.yml runs the tests on every push
```

## Limits

This is an educational starter, not a complete production inbox. It does not authenticate users, store customer data, send messages, or replace human review. The regex checks are examples to extend, not a universal safety layer.

## License

MIT
