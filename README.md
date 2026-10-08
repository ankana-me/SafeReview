# SafeReview

**An open-source AI code reviewer that checks AI-written code for security problems, keeps secrets away from the AI, and explains every risk in plain language.**

Hacktoberfest 2026 project · [Live demo (design + scanner)](https://YOUR-USERNAME.github.io/safereview/) · [Demo video](#) <!-- replace links -->

## What the project does

You paste code, usually code written with an AI assistant, and SafeReview reviews it for security problems in three steps:

1. **Detect:** deterministic rules find hardcoded secrets (passwords, API keys, tokens, AWS key formats) and risky patterns (string-built SQL, `eval()`, unsafe C functions like `gets()` and `strcpy()`).
2. **Protect:** every detected secret is replaced with `[REDACTED]` before any code is sent to the AI model.
3. **Explain:** Gemma 4, an open-weight model, explains each finding for a beginner: why it is dangerous, how an attacker could abuse it, and how to fix it.

The rules decide what counts as a finding. The AI never acts as the detector, because language models can miss real issues or invent false ones. It only explains.

## The problem it solves

- A password or API key left in source code can end up in a public repository, where anyone can find and use it.
- AI assistants can write code that works but is insecure, and beginners often accept it without review.
- Pasting code into an AI tool can itself leak real credentials at the moment of sending.

SafeReview gives beginners a quick, zero-setup check with explanations they can understand. It is a first line of defense and a learning tool, not a replacement for tools like GitHub secret scanning or Gitleaks.

## Setup and installation

**Requirements:** [Node.js](https://nodejs.org) 18 or newer (LTS recommended) and a free Gemini API key from [Google AI Studio](https://aistudio.google.com/apikey).

```bash
git clone https://github.com/YOUR-USERNAME/safereview.git
cd safereview
npm install
```

Create your own `.env` file (it is ignored by git and must never be committed):

```bash
cp .env.example .env
```

Open `.env` and replace `your-key-here` with your real key:

```
GEMINI_API_KEY=your-key-here
GEMMA_MODEL=gemma-4-26b-a4b-it
PORT=3000
```

Then open `docs/app.js` and change `USE_BACKEND = false` to `USE_BACKEND = true` to turn on the AI explanations.

## How to run the project

```bash
npm start
```

Open <http://localhost:3000>, click **Load sample**, then **Review**.

**Frontend only (no key needed):** open `docs/index.html` in a browser, or use the hosted demo above. With `USE_BACKEND = false`, the scanner and redaction run in the browser and the explanations come from built-in text instead of Gemma.

## Project structure

```
safereview/
├── server.js        Express server: serves the site, calls Gemma, keeps the API key private
├── package.json
├── .env.example     Template for your own .env (the real .env is never committed)
└── docs/
    ├── index.html   Page structure
    ├── styles.css   Dark theme and animations
    └── app.js       Scanner, redaction, results, demo animation
```

## Technologies and frameworks

| Part | Choice |
|---|---|
| Frontend | HTML, CSS, vanilla JavaScript |
| Backend | Node.js with Express |
| Security scanner | JavaScript rules (regular expressions) |
| AI model | Gemma 4, accessed through the Gemini API |
| Configuration | dotenv |
| Development assistant | GitHub Copilot |
| License | MIT |

## AI models used

**Gemma 4** (`gemma-4-26b-a4b-it` by default; `gemma-4-31b-it` also works), an open-weight model from Google, reached through the Gemini API. It is used only to turn each rule-based finding into a beginner-friendly explanation and fix. The model name can be changed in `.env`.

**GitHub Copilot** helped during development. <!-- Replace with your real examples: what it generated (rules, tests, docs), and what you corrected by hand. -->

## Security practices

- The API key lives only in a local `.env` file, which is excluded from version control.
- Secrets are redacted in the browser, and the server scrubs known secret formats again before calling the model.
- Pasted code is treated strictly as data: instructions are kept separate from it, the model's reply is parsed as JSON and filtered to known finding types, and results are shown as plain text, never as HTML.
- On errors, the server logs only the error message, not the pasted code. <!-- Confirm this matches your final code. -->
- All demo credentials in this project are fake.

## Limitations and dependencies

- **Missed problems:** rules will miss secrets in unusual formats and vulnerabilities that match no rule.
- **False alarms:** harmless text, such as example values in documentation, may be flagged.
- **AI mistakes:** Gemma may give an incomplete or wrong explanation. Always review suggested fixes.
- **Limited context:** only the pasted snippet is seen, not the whole application.
- **Prompt injection:** pasted code could contain text that tries to instruct the model. Mitigations are in place, but the risk cannot be fully eliminated.
- **External processing:** the AI step sends redacted code to the Gemini API, so use fake credentials only. If a real key was ever pasted, rotate it.
- **Redaction is not a guarantee:** a secret the rules do not recognise will still reach the model.
- **Not a full audit:** SafeReview does not replace a complete security review.
- **Dependencies:** a Gemini API key, an internet connection for the AI step, and Node.js 18+. The hosted GitHub Pages demo has no server, so it cannot call Gemma.

## Test results

<!-- Fill in with your real results before presenting: how many fake test snippets (including AI-generated ones), how many findings were caught, misses, and false alarms. -->

## Roadmap

More languages and rules, entropy-based detection, repository and git-history scanning, a local-model mode (for example via Ollama) for fully private use, server-side scanning, and an editor extension that checks code before it is shared with any AI tool.

## License

MIT
