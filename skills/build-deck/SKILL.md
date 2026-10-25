---
name: build-deck
description: Use when a user asks to build or extend Japanese vocabulary decks from a topic, JLPT level, pasted text, lyrics, menus, signs, or photos.
---

## Workflow
1. Identify the learner's topic/level and derive distinct, useful Japanese terms. For copyright-protected text, extract only vocabulary needed for study, not entire lyrics.
2. Call `list_decks`. Reuse a suitable user-owned deck or choose `deck_name` for a new deck. Never attempt to add to a predefined `own: false` deck.
3. If the user wants to avoid known vocabulary, call `get_learned_words` and filter the returned terms.
4. Put the most practical words first. A refused add_cards call adds nothing; if the refusal says only some cards can be added, retry once with at most that many of the most practical words and clearly disclose the number omitted. Terms already in the deck may be skipped but don't assume that will free room.
5. Prepare 1–2 natural examples and an informative concise nuance for each word. For standard dictionary words omit `reading`/`meaning` when appropriate so JMdict can populate them; for slang and fixed expressions provide clean English glosses and kana.
6. Call `add_cards` in chunks of no more than 200. If it refuses, handle it as in step 4.
7. Report new, duplicate/skipped and omitted counts based on real tool results, not intent. State where the deck appears on sync.

## Cross-workflow rules
- Use only Nihontekini MCP tools with exact server-returned IDs; never invent card, deck, quiz, or user IDs. Authentication failures: ask the user to reconnect the plugin.
- Card `meaning` must contain only plain English glosses (e.g. `["to study", "diligence"]`). Put kana in `reading`, usage and grammar in `explanation`, and never include romaji, pronunciation, kana, part of speech, or annotations inside `meaning`. The `reading` field contains kana, never romaji.
- Examples should be natural, with Japanese text in `jp` and, when available, Anki-style furigana in example `reading`, e.g. `日本語[にほんご]を 勉強[べんきょう]しています。`.
- In chat, when showing Japanese, give kana and romaji alongside kanji as appropriate; in a question that tests reading, never spoil the answer with reading or romaji.
- Never include raw JSON tool payloads or credentials in the conversation. Summarize results concisely in the user's language.
- Send at most 200 cards per add/suspend/delete request and 30 questions per quiz. On a 429 Too Many Requests, respect Retry-After and avoid hammering.
- If a tool refuses (`isError`), pass its message on plainly and stop without retrying unless the message says what to change. Never claim the assistant can buy or change anything on the account.
- Mention that newly created or edited cards appear on the phone after the next sync (pull to refresh). Never promise immediate propagation.
