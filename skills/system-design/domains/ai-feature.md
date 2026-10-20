# AI features: LLMs and models

## Questions
Ranked; re-rank for the prompt. The default follows each one.

1. What happens when the model is wrong? Default: the user sees a draft to review; the model never acts alone.
2. What user data goes to the model provider, and is that allowed? Default: only what the task needs, and never secrets or payment data.
3. Budget per user or per month? Default: a per-user daily cap.
4. Latency target? Default: first token under 2 s; stream the response.
5. Which provider or model is required? Default: one provider behind an interface, so it can be swapped.
6. Does it read untrusted content (web pages, emails, uploads)? Default: yes; treat that content as data, never as instructions.
7. How is quality measured? Default: a fixed eval set, run on every prompt or model change.

## Scale dimensions
- Calls per day: users × calls per user per day.
- Tokens per call: input (prompt + context) and output, counted separately.
- Cost: calls per day × tokens × price per token, with and without caching.
- Provider rate limits (requests/min and tokens/min) against peak calls.
- Latency: time to first token, plus total generation time at the expected output length.

## Usual failures
- The model returns a wrong, made-up, or malformed answer.
- The output does not match the expected schema, and parsing fails.
- The provider is slow, rate-limits you, or is down.
- Prompt injection inside untrusted content steers the model.
- Costs spike from a loop, a retry storm, or one heavy user.
- A model or prompt update silently lowers quality.
- Personal data ends up in prompts, logs, or provider training data.
