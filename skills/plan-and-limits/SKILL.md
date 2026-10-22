---
name: plan-and-limits
description: Use when the user asks about Nihontekini Free vs Premium, remaining AI-agent card or quiz allowance, monthly resets, or receives a quota error.
---

## Workflow
1. Call `get_plan` for the authenticated user's current plan. Interpret `agentLimits` as an object for free or `"none"` for Premium. Prefer server `resetsOn` to calculating your own date.
2. For free, explain 50 agent-added cards and 3 agent-generated quizzes per UTC month and display actual remaining counts; deletions do not restore slots. Their own cards are free to study. In-app "Test me" is once per day on free.
3. For Premium, explain unlimited agent-added cards and quizzes, predefined JLPT N4–N1 decks, unlimited in-app Test me, subscription or lifetime purchase, and verified `premiumUntil` if provided.
4. If another tool returns an allowance error, pass the concrete limitation clearly and stop; don't retry to bypass it. Suggest manual add, waiting for next reset, or optional in-app upgrade under Me > Premium, without pressure or repeated promotion.
5. ChatGPT cannot initiate, purchase or manage Premium. If a user requests "Buy me Premium", say they must open the iOS app and choose Me > Premium; do not call payment tools.

## Cross-workflow rules
- Use only Nihontekini MCP tools with exact server-returned IDs; never invent card, deck, quiz, or user IDs. Authentication failures: ask the user to reconnect the plugin.
- Card `meaning` must contain only plain English glosses (e.g. `["to study", "diligence"]`). Put kana in `reading`, usage and grammar in `explanation`, and never include romaji, pronunciation, kana, part of speech, or annotations inside `meaning`. The `reading` field contains kana, never romaji.
- Examples should be natural, with Japanese text in `jp` and, when available, Anki-style furigana in example `reading`, e.g. `日本語[にほんご]を 勉強[べんきょう]しています。`.
- In chat, when showing Japanese, give kana and romaji alongside kanji as appropriate; in a question that tests reading, never spoil the answer with reading or romaji.
- Never include raw JSON tool payloads or credentials in the conversation. Summarize results concisely in the user's language.
- Max 200 cards per add/suspend/delete request and 30 questions per quiz. Do not retry a refused quota call. Rate limit 429: respect Retry-After and avoid hammering.
- On free accounts, 50 agent-added cards and 3 AI-generated quizzes are allowed per UTC calendar month. Card deletions do not refund quota. Successful write results may include `freePlan` remaining counts; treat them as authoritative, not assumptions. If a quota refusal occurs, report it clearly, say when it resets, and explain the in-app Me > Premium option or manual card creation. Never claim the assistant can buy, start, cancel, or change Premium; do not pressure the user or repeat an upsell after it has been explained.
- Mention that newly created or edited cards appear on the phone after the next sync (pull to refresh). Never promise immediate propagation.
