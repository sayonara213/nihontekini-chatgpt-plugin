---
name: fix-cards
description: Use when the user wants to find, correct, move, suspend, unsuspend, or delete Japanese flashcards in Nihontekini.
---

## Workflow
1. Call `find_cards` with exact term or filter. If many matches, present enough context to disambiguate before changes.
2. For edits or moves, ensure `own` is true and call `edit_card` with only requested fields. Keep meaning English-gloss-only; explain nuance elsewhere. Existing scheduling progress is retained.
3. For "already know" or "skip" requests, prefer `suspend_cards` (or unsuspend when requested), which is supported for predefined as well as own cards. Max 200 IDs per call.
4. Never edit/delete predefined cards (`own: false`). Explain that they can only be suspended.
5. Before `delete_cards`, enumerate exact cards and confirm explicitly in the current conversation because deletion destroys progress; don't proceed based on a vague "clean up" request. Delete only confirmed owned IDs, max 200 per batch.
6. Report only confirmed tool changes and remind about the next iOS sync. Handle auth failure, duplicates or missing records by explaining what was found, not guessing IDs.

## Cross-workflow rules
- Use only Nihontekini MCP tools with exact server-returned IDs; never invent card, deck, quiz, or user IDs. Authentication failures: ask the user to reconnect the plugin.
- Card `meaning` must contain only plain English glosses (e.g. `["to study", "diligence"]`). Put kana in `reading`, usage and grammar in `explanation`, and never include romaji, pronunciation, kana, part of speech, or annotations inside `meaning`. The `reading` field contains kana, never romaji.
- Examples should be natural, with Japanese text in `jp` and, when available, Anki-style furigana in example `reading`, e.g. `日本語[にほんご]を 勉強[べんきょう]しています。`.
- In chat, when showing Japanese, give kana and romaji alongside kanji as appropriate; in a question that tests reading, never spoil the answer with reading or romaji.
- Never include raw JSON tool payloads or credentials in the conversation. Summarize results concisely in the user's language.
- Max 200 cards per add/suspend/delete request and 30 questions per quiz. Do not retry a refused quota call. Rate limit 429: respect Retry-After and avoid hammering.
- Agent-added cards and quizzes can have a monthly limit on the user's account. Successful write results may include the remaining counts; treat them as authoritative, not assumptions. Card deletions do not restore the count. If a tool refuses because the limit is reached, pass its message on plainly with the reset date, offer to add cards by hand in the app, and stop without retrying. Do not discuss plans, pricing or subscriptions, and never claim the assistant can buy or change anything on the account.
- Mention that newly created or edited cards appear on the phone after the next sync (pull to refresh). Never promise immediate propagation.
