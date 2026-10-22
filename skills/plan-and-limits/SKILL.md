---
name: plan-and-limits
description: Use when the user asks which Nihontekini plan they are on, how much of their monthly AI-agent card or quiz allowance is left, when it resets, or receives a quota error.
---

## Workflow
1. Call `get_plan` for the authenticated user's current plan. Interpret `agentLimits` as an object for free or `"none"` for Premium. Prefer server `resetsOn` to calculating your own date.
2. For free, give the actual remaining agent-added cards and quizzes for this UTC month and the reset date; deletions do not restore slots. Studying their own cards is not limited.
3. For Premium, say that agent-added cards and quizzes have no monthly limit, and give a verified `premiumUntil` if provided.
4. If another tool returns an allowance error, pass the concrete limitation on clearly and stop; don't retry to bypass it. Say when it resets and that cards can still be added by hand in the app. Do not promote or recommend an upgrade.
5. ChatGPT cannot purchase, start or manage a plan. If the user asks to buy or change a plan, say you can't do that here and that plans are managed in the Nihontekini iOS app; do not describe plan pricing or call payment tools.

## Cross-workflow rules
- Use only Nihontekini MCP tools with exact server-returned IDs; never invent card, deck, quiz, or user IDs. Authentication failures: ask the user to reconnect the plugin.
- Card `meaning` must contain only plain English glosses (e.g. `["to study", "diligence"]`). Put kana in `reading`, usage and grammar in `explanation`, and never include romaji, pronunciation, kana, part of speech, or annotations inside `meaning`. The `reading` field contains kana, never romaji.
- Examples should be natural, with Japanese text in `jp` and, when available, Anki-style furigana in example `reading`, e.g. `日本語[にほんご]を 勉強[べんきょう]しています。`.
- In chat, when showing Japanese, give kana and romaji alongside kanji as appropriate; in a question that tests reading, never spoil the answer with reading or romaji.
- Never include raw JSON tool payloads or credentials in the conversation. Summarize results concisely in the user's language.
- Max 200 cards per add/suspend/delete request and 30 questions per quiz. Do not retry a refused quota call. Rate limit 429: respect Retry-After and avoid hammering.
- On free accounts, 50 agent-added cards and 3 AI-generated quizzes are allowed per UTC calendar month. Card deletions do not refund quota. Successful write results may include `freePlan` remaining counts; treat them as authoritative, not assumptions. If a quota refusal occurs, say plainly that the action isn't available on the user's current plan right now, say when the allowance resets, and offer to add cards by hand in the app instead. Do not promote, describe or recommend paid plans or upgrades, and never claim the assistant can buy, start, cancel or change a plan.
- Mention that newly created or edited cards appear on the phone after the next sync (pull to refresh). Never promise immediate propagation.
