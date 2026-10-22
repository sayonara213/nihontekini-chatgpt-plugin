---
name: explain-word
description: Use when the user asks what a Japanese term, phrase, or kanji means and may want to save it as a Nihontekini card.
---

## Workflow
1. Call `search_dictionary` for kanji/kana/romaji or English keyword when a lookup is relevant; do not present guessed JMdict entries as confirmed results.
2. Explain meanings, reading, register, usage, common confusions and one natural example. Present Japanese with kana and romaji in chat. If ambiguous, explain possible senses and ask which one applies.
3. Optionally call `find_cards` to see whether the word is already in the user's decks. Never claim it is saved unless confirmed.
4. If user asks to add it, follow build-deck logic: list decks, check what is left this month if useful, and add it with clean English glosses, not romaji in `meaning`.
5. If the dictionary doesn't find a term, say so and offer a tentative language explanation separately without inventing an entry.

## Cross-workflow rules
- Use only Nihontekini MCP tools with exact server-returned IDs; never invent card, deck, quiz, or user IDs. Authentication failures: ask the user to reconnect the plugin.
- Card `meaning` must contain only plain English glosses (e.g. `["to study", "diligence"]`). Put kana in `reading`, usage and grammar in `explanation`, and never include romaji, pronunciation, kana, part of speech, or annotations inside `meaning`. The `reading` field contains kana, never romaji.
- Examples should be natural, with Japanese text in `jp` and, when available, Anki-style furigana in example `reading`, e.g. `日本語[にほんご]を 勉強[べんきょう]しています。`.
- In chat, when showing Japanese, give kana and romaji alongside kanji as appropriate; in a question that tests reading, never spoil the answer with reading or romaji.
- Never include raw JSON tool payloads or credentials in the conversation. Summarize results concisely in the user's language.
- Max 200 cards per add/suspend/delete request and 30 questions per quiz. Do not retry a refused quota call. Rate limit 429: respect Retry-After and avoid hammering.
- Agent-added cards and quizzes can have a monthly limit on the user's account. Successful write results may include the remaining counts; treat them as authoritative, not assumptions. Card deletions do not restore the count. If a tool refuses because the limit is reached, pass its message on plainly with the reset date, offer to add cards by hand in the app, and stop without retrying. Do not discuss plans, pricing or subscriptions, and never claim the assistant can buy or change anything on the account.
- Mention that newly created or edited cards appear on the phone after the next sync (pull to refresh). Never promise immediate propagation.
