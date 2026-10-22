# Nihontekini plugin for ChatGPT

Lets ChatGPT work with your [Nihontekini](https://nihontekini.app) flashcards. Ask ChatGPT to
fill your decks with Japanese words, explain a word and save it as a card, fix cards, check how
you are doing, or quiz you on the words you keep forgetting. Changes reach your phone the next
time the app syncs.

> **Early access.** The Nihontekini iPhone app is not on the App Store yet; it is in testing
> and coming soon. The plugin and its server already work: you can connect, build decks and
> take quizzes in ChatGPT today, and your cards are waiting in your account when the app comes
> out. See [nihontekini.app](https://nihontekini.app) for the launch.

## About Nihontekini

Nihontekini (日本的に, にほんてきに, nihonteki ni, "the Japanese way") is an iPhone app for learning
Japanese with Anki-style flashcards and FSRS spaced repetition. Each card has the word, its
reading, English meanings, an explanation of nuance and usage, example sentences with furigana,
and tags. Readings, meanings and examples you leave out are filled in from the JMdict
dictionary. There are predefined JLPT decks and your own decks; ChatGPT works with both.

## What you can ask ChatGPT

| Skill | Use it for | Try |
|---|---|---|
| `build-deck` | Add cards from a topic, JLPT level, pasted text, song lyrics or a photo of a menu or sign | "Add 20 N5 food words to a deck called Kitchen" |
| `explain-word` | What a word, phrase or kanji means, with the option to save it as a card | "What does 木漏れ日 mean? Save it to my deck" |
| `quiz-me` | A quiz on your weakest words, one question at a time, saved to the app | "Quiz me on the words I keep forgetting" |
| `fix-cards` | Fix a reading or meaning, move cards between decks, suspend words you already know, delete cards (ChatGPT asks first) | "I already know ありがとう, stop showing it" |
| `progress-check` | A short summary: words started and known, reviews due, your weakest words | "How am I doing this week?" |
| `plan-and-limits` | Which plan you're on and what is left of this month's AI allowance | "How many AI cards and quizzes do I have left this month?" |

## Install

Setup steps for ChatGPT are on [nihontekini.app/chatgpt](https://nihontekini.app/chatgpt).

When you connect, you sign in once in the browser with your email and a one-time code (or Sign
in with Apple), then approve the connection. If you don't have an account yet, signing in
creates one. You can revoke the connection any time in the app under **Me > AI agents**.

## Plans

Studying your own cards is always free. On the free plan, AI agents such as ChatGPT can add 50
cards and make 3 quizzes a month, and the allowance resets on the 1st (UTC). Premium removes
those limits, unlocks the predefined JLPT N4 to N1 decks and makes the in-app Test me
unlimited. Premium is bought only in the iPhone app, under **Me > Premium**; ChatGPT can tell
you what is left but can't buy or change your plan.

## Data

The plugin has no code of its own. It is skills (instructions for ChatGPT) and one remote MCP
server, `https://api.nihontekini.app/mcp`, run by Nihontekini. Through it, ChatGPT reads and
changes your Nihontekini data: decks and cards, review history and progress, quizzes and your
answers, and your plan and how much of the free monthly allowance is left. It sends nothing
anywhere else. See the [privacy policy](https://nihontekini.app/privacy) and
[terms](https://nihontekini.app/terms).

## Support

Questions and bug reports: [help@nihontekini.app](mailto:help@nihontekini.app).

## For developers

| Path | What it is |
|---|---|
| `plugin.json` | Plugin manifest (listing copy, icon, starter prompts) |
| `.codex-plugin/plugin.json` | The same manifest in the Codex plugin layout, pointing at `skills/` and `.mcp.json` |
| `.mcp.json`, `mcp.json` | The Nihontekini remote MCP server: Streamable HTTP, OAuth with dynamic client registration, scopes `read` and `write` |
| `skills/` | The six skills above; each has an `agents/openai.yaml` with its display name and MCP dependency |
| `assets/logo.png` | Square app icon, also used as the composer icon |

Raise `version` in both manifests with every release. The server is POST-only, so a `GET /mcp`
returning 405 is expected. To poke at the tools directly, run
`npx @modelcontextprotocol/inspector@latest`, choose Streamable HTTP with the URL above and
sign in with OAuth.

The MCP server lives in `apps/api/src/mcp/` of
[nihontekini-main](https://github.com/sayonara213/nihontekini-main).
