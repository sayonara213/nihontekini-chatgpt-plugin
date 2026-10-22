---
name: build-deck
description: Use when a user asks to build or extend Japanese vocabulary decks from a topic, JLPT level, pasted text, lyrics, menus, signs, or photos.
---

## Workflow
1. Identify the learner's topic/level and derive distinct, useful Japanese terms. For copyright-protected text, extract only vocabulary needed for study, not entire lyrics.
2. Call `list_decks`. Reuse a suitable user-owned deck or choose `deck_name` for a new deck. Never attempt to add to a predefined `own: false` deck.
3. If the user wants to avoid known vocabulary, call `get_learned_words` and filter the returned terms. For more than a handful of additions, call `get_plan` before writing.
4. If `get_plan` shows a monthly card limit, cap this turn's planned additions at actual `agentLimits.cardsLeft`. If fewer slots remain than requested, prioritize practical words and clearly disclose the number omitted. Avoid any add_cards call that would exceed available allowance: quota failures are all-or-nothing. Terms already in the deck may be skipped but don't assume that will free quota.
5. Prepare 1–2 natural examples and an informative concise nuance for each word. For standard dictionary words omit `reading`/`meaning` when appropriate so JMdict can populate them; for slang and fixed expressions provide clean English glosses and kana.
6. Call `add_cards` in chunks of no more than 200 and no more than the verified allowance. On a limit error, stop and pass the message on without retrying.
7. Report new, duplicate/skipped and omitted counts based on real tool results, not intent. State where the deck appears on sync.

## Cross-workflow rules
- Use only Nihontekini MCP tools with exact server-returned IDs; never invent card, deck, quiz, or user IDs. Authentication failures: ask the user to reconnect the plugin.
- Card `meaning` must contain only plain English glosses (e.g. `["to study", "diligence"]`). Put kana in `reading`, usage and grammar in `explanation`, and never include romaji, pronunciation, kana, part of speech, or annotations inside `meaning`. The `reading` field contains kana, never romaji.
- Examples should be natural, with Japanese text in `jp` and, when available, Anki-style furigana in example `reading`, e.g. `日本語[にほんご]を 勉強[べんきょう]しています。`.
- In chat, when showing Japanese, give kana and romaji alongside kanji as appropriate; in a question that tests reading, never spoil the answer with reading or romaji.
- Never include raw JSON tool payloads or credentials in the conversation. Summarize results concisely in the user's language.
- Max 200 cards per add/suspend/delete request and 30 questions per quiz. Do not retry a refused quota call. Rate limit 429: respect Retry-After and avoid hammering.
- Agent-added cards and quizzes can have a monthly limit on the user's account. Successful write results may include the remaining counts; treat them as authoritative, not assumptions. Card deletions do not restore the count. If a tool refuses because the limit is reached, pass its message on plainly with the reset date, offer to add cards by hand in the app, and stop without retrying. Do not discuss plans, pricing or subscriptions, and never claim the assistant can buy or change anything on the account.
- Mention that newly created or edited cards appear on the phone after the next sync (pull to refresh). Never promise immediate propagation.
