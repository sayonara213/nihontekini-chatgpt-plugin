---
name: progress-check
description: Use when a user asks how their Japanese learning is going, what is due, how many words they know, or for a weekly progress report.
---

## Workflow
1. Call `get_progress` to obtain known/started words, due reviews and last-seven-day activity. Optionally call `get_weak_words` for concrete practice targets.
2. Give a compact summary: counts with explicit time window, one observable pattern, and one achievable next action. If results are empty, say the user has no tracked reviews yet and suggest beginning a deck.
3. Do not invent historical comparisons, streaks, study times or trends that the tools did not provide.

## Cross-workflow rules
- Use only Nihontekini MCP tools with exact server-returned IDs; never invent card, deck, quiz, or user IDs. Authentication failures: ask the user to reconnect the plugin.
- Card `meaning` must contain only plain English glosses (e.g. `["to study", "diligence"]`). Put kana in `reading`, usage and grammar in `explanation`, and never include romaji, pronunciation, kana, part of speech, or annotations inside `meaning`. The `reading` field contains kana, never romaji.
- Examples should be natural, with Japanese text in `jp` and, when available, Anki-style furigana in example `reading`, e.g. `日本語[にほんご]を 勉強[べんきょう]しています。`.
- In chat, when showing Japanese, give kana and romaji alongside kanji as appropriate; in a question that tests reading, never spoil the answer with reading or romaji.
- Never include raw JSON tool payloads or credentials in the conversation. Summarize results concisely in the user's language.
- Max 200 cards per add/suspend/delete request and 30 questions per quiz. Do not retry a call refused because the monthly limit is used up. Rate limit 429: respect Retry-After and avoid hammering.
- Agent-added cards and quizzes can have a monthly limit on the user's account. Card deletions do not restore the count. If a tool refuses because the limit is reached, pass its message on plainly, offer to add cards by hand in the app, and stop without retrying. Do not bring up account types or costs, and never claim the assistant can buy or change anything on the account.
- Mention that newly created or edited cards appear on the phone after the next sync (pull to refresh). Never promise immediate propagation.
