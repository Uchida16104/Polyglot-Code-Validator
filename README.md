# Polyglot Code Validator

A browser-based code validation workbench with a **single-file application**: all markup, application CSS, JavaScript, dependency configuration, and Mermaid documentation live in [`index.html`](./index.html).

**Repository name:** `polyglot-code-validator`

## Features

- Code editor with line numbers, syntax highlighting, language selection, keyboard shortcuts, and a sample snippet for each language.
- Capability-aware validation for JavaScript, TypeScript, Python, PHP, SQL (SQLite dialect), JSON, HTML, and CSS.
- Clearly labeled structural-only checks for languages that need their own compiler or parser, including Rust, C, C++, C#, Java, Ruby, Go, Bash, YAML, and Markdown.
- Accessible result summaries with issue severity, line/column locations when available, and a brief explanation of validation limits.
- Optional local draft persistence. Draft text is not persisted unless the user clicks **Save draft** or explicitly enables **Remember this draft**.
- Three Mermaid diagrams for the validation pipeline, application architecture, and error-handling workflow.
- Graceful degradation when optional CDN dependencies cannot load.
- Responsive layout, debounced editor highlighting, input-size protection, and reduced-motion support.

## Run it

1. Download or clone this repository.
2. Open `index.html` in a current desktop or mobile browser.
3. Allow network access for optional CDN-hosted dependencies. The interface itself is a single HTML file, but the full validator is **not fully offline** unless its third-party assets are downloaded and bundled locally.

For the most predictable browser behavior, serve the directory over HTTP instead of opening it as a `file://` URL:

```bash
cd polyglot-code-validator
python3 -m http.server 8000
```

Then open <http://localhost:8000>.

No npm install, build step, server-side application, or project manifest is required.

## Validation coverage

| Language | Method | What it checks | Important limitation |
|---|---|---|---|
| JavaScript | Acorn parser | JavaScript syntax | No runtime behavior, lint rules, or type checks |
| TypeScript | TypeScript compiler parser | TypeScript parse diagnostics | Parsing only; does not run a full type-checking program or resolve project types |
| Python | PyScript worker + `ast.parse` | Python grammar/syntax | Submitted code is parsed, not run; runtime and dependency errors are not detected |
| PHP | `php-parser` | PHP syntax / AST parsing | No PHP runtime, Composer autoloading, or framework checks |
| SQL | `sql.js` / SQLite `prepare` | One SQLite statement's parse/prepare stage | SQLite dialect only; one statement at a time; no statement stepping or database-specific checks |
| JSON | Native `JSON.parse` | Strict JSON syntax | JSON5, comments, and trailing commas are not accepted |
| HTML | Browser `DOMParser` + common tag-nesting heuristic | HTML parsing and selected mismatched/unclosed-tag patterns | HTML parsing is error-recovering; this is not a full HTML conformance validator |
| CSS | Lightweight lexical scan | Braces, brackets, strings, and comments | Not a full CSS grammar, cascade, compatibility, or stylelint check |
| Rust, C, C++, C#, Java, Ruby, Go, Bash, YAML, Markdown | Generic lexical structure scan | Common unmatched delimiters and unterminated strings/comments | **Not full language syntax validation.** Use the corresponding compiler, interpreter, parser, or linter for authoritative results. |

A successful result means only that the selected checker did not detect a problem within its stated scope. It does not establish correctness, security, performance, or runtime behavior.

## Libraries and roles

Libraries are loaded from versioned CDN URLs in `index.html`; most language validators are loaded only when needed.

- **EaseMotion CSS** — optional animation-first CSS utilities.
- **Tailwind CSS** and **Bootstrap** — responsive UI utilities and baseline components.
- **Motion** (Motion One successor) — optional UI entrance animation.
- **HTMX**, **Alpine.js**, and **hyperscript** — small UI interaction enhancements.
- **php.js** — optional PHP-style JavaScript utility functions; it is not a PHP parser.
- **php-parser** — actual PHP syntax parser used by validation.
- **sql.js** — WebAssembly SQLite parser/prepare support.
- **PyScript** — on-demand Python parsing in a worker.
- **highlight.js** — editor syntax highlighting.
- **Mermaid** — embedded documentation diagrams.
- **LocalStorage** — language preference and user-approved draft persistence.

If a dependency is blocked by a network policy, browser setting, CSP, or CDN outage, the application reports the problem and either falls back to structural checks or tells you that the relevant validator is unavailable. A real offline distribution must bundle third-party CSS, JavaScript, and WebAssembly assets locally and verify each library's license.

## Security and privacy notes

- The tool does **not execute the submitted source code** as part of validation.
- Python uses `ast.parse` in a PyScript worker. SQL uses `prepare` and does not step the statement. JavaScript is parsed by Acorn, not passed to `eval` or executed.
- The editor enforces a 100 KiB source limit to reduce accidental resource exhaustion.
- Source code stays in the current browser tab unless you explicitly save a draft in browser LocalStorage. CDN requests will still disclose ordinary network metadata to the relevant CDN providers.
- LocalStorage is not encrypted. Do not save secrets, production credentials, private keys, or confidential code on a shared device.
- Browser-side parsing is not a sandbox guarantee for arbitrary third-party libraries. Keep dependencies pinned, review updates, and consider a restrictive Content Security Policy for production deployment.

## Included files

```text
polyglot-code-validator/
├── README.md
└── index.html
```

## Suggested verification checklist

- [ ] Validate a valid and invalid JavaScript sample and inspect line/column reporting.
- [ ] Validate valid and invalid TypeScript and Python snippets.
- [ ] Validate PHP syntax with and without `<?php`.
- [ ] Submit one valid and one invalid SQLite statement.
- [ ] Try malformed JSON, HTML nesting, CSS braces, and generic delimiter errors.
- [ ] Disable network access and check that the app remains usable with a clear capability warning.
- [ ] Save a draft, reload the page, and confirm opt-in persistence.
- [ ] Test keyboard-only navigation, mobile viewport behavior, and reduced-motion preferences.

## License

This repository's original code is provided under the MIT License. Third-party libraries retain their respective licenses; see their upstream projects for details.
