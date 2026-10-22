---
name: quiz-me
description: Use when a user asks to quiz, test, drill, or practice weak Japanese words from their Nihontekini decks.
---

## Workflow
1. Choose recorded vs informal mode. Recorded mode calls `get_plan` to check free quiz allowance and uses `make_quiz` (max 30 questions), which consumes one AI-generated quiz and saves a quiz record; avoid making a quiz just to preview it. Informal mode calls `get_learned_words`/`get_weak_words` and is not recorded.
2. If a user wants the weakest vocabulary, use `get_weak_words` to choose the level. If recorded mode is requested, call `make_quiz` once; handle empty decks, auth errors and `isError` quota refusal without retrying. When a free quota is exhausted, suggest informal practice from existing words or in-app options; do not imply that informal assistant-generated practice is necessarily exempt if it creates a server quiz.
3. Ask one question at a time. Do not reveal correct option, kana, or romaji before the user answers if those are under test. Keep correct answers private while asking.
4. Give short feedback after each response; store the user's selection as the exact requested `picked` representation. Don't record questions they didn't answer.
5. For recorded quizzes, call `record_quiz_answers` once using the exact returned quiz_id and card_ids, up to 30 responses. If the session ends early, record only answered questions when appropriate.
6. Give the score and 3–5 words to revisit. If no practice data exists, explain what to review first.

## Cross-workflow rules
- Use only Nihontekini MCP tools with exact server-returned IDs; never invent card, deck, quiz, or user IDs. Authentication failures: ask the user to reconnect the plugin.
- Card `meaning` must contain only plain English glosses (e.g. `["to study", "diligence"]`). Put kana in `reading`, usage and grammar in `explanation`, and never include romaji, pronunciation, kana, part of speech, or annotations inside `meaning`. The `reading` field contains kana, never romaji.
- Examples should be natural, with Japanese text in `jp` and, when available, Anki-style furigana in example `reading`, e.g. `日本語[にほんご]を 勉強[べんきょう]しています。`.
- In chat, when showing Japanese, give kana and romaji alongside kanji as appropriate; in a question that tests reading, never spoil the answer with reading or romaji.
- Never include raw JSON tool payloads or credentials in the conversation. Summarize results concisely in the user's language.
- Max 200 cards per add/suspend/delete request and 30 questions per quiz. Do not retry a refused quota call. Rate limit 429: respect Retry-After and avoid hammering.
- On free accounts, 50 agent-added cards and 3 AI-generated quizzes are allowed per UTC calendar month. Card deletions do not refund quota. Successful write results may include `freePlan` remaining counts; treat them as authoritative, not assumptions. If a quota refusal occurs, report it clearly, say when it resets, and explain the in-app Me > Premium option or manual card creation. Never claim the assistant can buy, start, cancel, or change Premium; do not pressure the user or repeat an upsell after it has been explained.
- Mention that newly created or edited cards appear on the phone after the next sync (pull to refresh). Never promise immediate propagation.
