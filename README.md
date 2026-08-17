# poc-skills

A collection of skills focused on proof of concept (POC) work — validating whether a tool, API, or service is worth adopting before committing real effort to it.

`poc-start` is the entry point: it scaffolds and runs a feasibility POC that answers the most basic question — **can we actually use this to do the thing?** — with running code and evidence rather than opinions.

It is only the **first step** of validation. The scope is deliberately narrow: the simplest working usage of the tool. Security, performance and legal/compliance are explicitly out of scope and are recorded as open questions for later stages. It is not a prototype, not an integration, and not production code.

---

## Using it

Install `poc-start.skill` in Claude (it is a packaged skill: `SKILL.md`, references and the viewer template in one file). Then start a POC by typing:

```
/poc-start
```

or just by describing the intent — *"quero validar se o Stripe consegue emitir boleto"*, *"será que dá para usar a API do Twilio para isso?"*. You do not need the word "POC".

### What happens

1. **Language** — it asks which language to work in. Everything you read afterwards is in that language.
2. **Scope** — what the tool is and the single core action that would prove it usable. Answer with the target stack if the POC will land in an existing codebase.
3. **Research** — the tool is researched against fixed axes: how to connect, how to call the core feature, where to try it safely.
4. **Manual checklist** — `MANUAL-SETUP.md` lists what only you can do (create the account, generate the key) and which `.env` key each item produces. Work through it while the rest is built.
5. **Build and run** — the flows are written, the viewer is scaffolded, and the POC is actually executed. A step that needs a credential you haven't provided stops as `blocked` rather than pretending.
6. **Verdict** — `findings.md` (is it usable) and `LEARNINGS.md` (what would have made this run faster).

### Running the POC yourself

```bash
cd poc-<tool>-<purpose>-<date>
npm install
npm run poc          # → http://localhost:5180
```

Fill `.env` first — `MANUAL-SETUP.md` says which keys, `.env.example` documents them all.

## The viewer

One local page, no build step. Every step is executed against the real API from Node, and everything the call produced is recorded to disk.

- **Edit before sending** — each header and each top-level body field is its own input, with a synced raw-JSON view. Nothing is mocked; edits apply to the next send.
- **Fixtures** — sample inputs (a photo, a PDF, a payload) render inline, and *Replace…* swaps in a local file without touching the one the POC shipped with.
- **Readable responses** — JSON renders as a navigable tree, including payloads nested as a JSON string inside a field, which is how MCP and other RPC-style APIs answer.
- **Files involved** — the code path a step flows through, route → controller → service, opening in your editor.
- **Environment switch** — when the POC declares more than one, switching re-points the calls and resets the run.
- **Reset** — clears recorded outputs and returns every request to what `steps.json` declares.

## `.env` keys worth knowing

| Key | Purpose |
|---|---|
| `POC_ENVIRONMENT` | Which environment the run starts on. Stamped onto every recorded response |
| `NAME__SANDBOX` / `NAME__PRODUCTION` | Per-environment values — `{{NAME}}` resolves to the active one, falling back to bare `NAME` |
| `POC_PROJECT_ROOT` | Where the target codebase lives, so the *files involved* trail can open |
| `POC_EDITOR` | `code`, `subl`, `idea`… searched on PATH and in the usual app locations |
| `POC_PORT` | Viewer port (default `5180`) |

`.env` is never committed and is never collected through the viewer — you fill it in your own editor. Values whose key looks like a credential are redacted from the UI and from every recorded `request.json`.

## Notes

Each POC keeps its own `LEARNINGS.md`, and it stays with that POC. Lessons are never carried across tools: two APIs that look alike fail differently, and an inherited claim reads like evidence when it is a guess.

If a POC needs a credential *file* rather than a key — a service-account JSON, a `.pem`, a `.p12` — add it to that POC's `.gitignore` as soon as you download it.
