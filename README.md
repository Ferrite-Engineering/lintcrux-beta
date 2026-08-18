# LintCrux Beta

Welcome to the home of the **LintCrux public beta** — this is where you report
bugs, request features, get help, and browse the test-fixture corpus LintCrux is
validated against.

> **LintCrux** is a multi-engine RTL lint dashboard for HDL engineers — run
> Verilator, Verible, Slang, Yosys, GHDL, and Svlint together, get one sortable,
> filterable violation table instead of six terminal formats, waive what you've
> accepted, and watch the count go down. Linux, macOS, and Windows, plus a
> read-only web viewer for SARIF results.

**This repository contains no application source code.** During the public beta
the LintCrux source is closed; this repo exists purely as the public meeting
point for the beta:

- 🐞 **[Report a bug](../../issues/new?template=bug_report.yml)** — or just use
  **Help → Submit Issue** inside the app (recommended; it attaches diagnostics
  for you — see below).
- 🧪 **[Submit a test fixture](../../issues/new?template=fixture_submission.yml)** —
  hand us an engine log LintCrux misparses, or RTL that trips a rule it doesn't
  report, and we'll fold it into the suite.
- 💡 **[Request a feature / share an idea](../../discussions/categories/ideas)** —
  in Discussions, so it can be discussed and upvoted.
- 🙋 **[Ask a question / get help](../../discussions/categories/q-a)** — in Discussions.
- 📋 **[Release notes](RELEASES.md)** — what changed in each beta build.
- 📂 **[Browse the test fixtures](fixtures/)** — *this is what LintCrux tests
  against.* Real engine output, with the goldens derived from it. See
  [`fixtures/README.md`](fixtures/README.md).
- ▶️ **[Open an example project](examples/)** — two ready-to-run `.lintcrux`
  projects, plus a captured SARIF report you can import **with no lint engine
  installed**. See [`examples/README.md`](examples/README.md).

> **Two places, clear split.** The **[Issues](../../issues)** tab is a work
> queue — **bugs and fixture submissions only**. Everything conversational —
> questions, feature ideas, announcements, show-and-tell — lives in
> **[Discussions](../../discussions)**, the single community hub for the beta.
> (No Discord or Slack — Discussions keeps every answer searchable and in one
> place.)

LintCrux is part of the **EDACrux** suite and cross-probes with its siblings over
CXP. Their betas run the same way:
[WaveCrux](https://github.com/Ferrite-Engineering/wavecrux-beta) ·
[NetCrux](https://github.com/Ferrite-Engineering/netcrux-beta) ·
[SimCrux](https://github.com/Ferrite-Engineering/simcrux-beta).

---

## Reporting a bug — the easy way

The best bug reports come straight from the app, because they carry the
reproduction context automatically:

1. In LintCrux, open **Help → Submit Issue** (also in the command palette and the
   About box).
2. Pick which context to attach — app & environment, session state, a diagnostics
   snapshot, and (on desktop) a screenshot. App & environment is always on.
3. Hit **Submit**. LintCrux copies a formatted report to your clipboard and opens
   a pre-filled new-issue form **in this repository**. Paste if needed, drag in
   the screenshot, and submit.

No private file contents, rule messages from your code, or file paths are
included — only counts, formats, and environment metadata. See the in-app privacy
callout for exactly what each toggle adds.

Prefer to file by hand? Use the [bug report form](../../issues/new?template=bug_report.yml).

**Parsing bugs deserve one extra line:** tell us the engine and its exact version
(`verilator --version`, `verible-verilog-lint --version`, `slang --version`,
`ghdl --version`, `svlint --version`, `yosys -V`). LintCrux parses each engine's
text output, and those formats drift between releases — the version is usually
the whole story.

## Getting help

**[GitHub Discussions](../../discussions) is the community hub** — it's where all
the conversation happens:

- **[Q&A](../../discussions/categories/q-a)** — questions, "how do I…", workflow tips.
- **[Ideas](../../discussions/categories/ideas)** — feature requests and suggestions, upvotable.
- **[Show and tell](../../discussions/categories/show-and-tell)** — share what you've built.
- **[Announcements](../../discussions/categories/announcements)** — updates from us, including new beta builds.

**Bugs and crashes** → file an [Issue](../../issues) (above) instead, so they land
in the triage queue, not the discussion stream.

## What's in this repo

| Path | What it is |
|------|------------|
| [`README.md`](README.md) | This file. |
| [`RELEASES.md`](RELEASES.md) | What changed in each beta build. |
| [`docs/BETA_GUIDE.md`](docs/BETA_GUIDE.md) | How to join the beta, what to test, how feedback is handled, what you get for contributing. |
| [`docs/SUBMITTING_FIXTURES.md`](docs/SUBMITTING_FIXTURES.md) | How to contribute a lint fixture (and the license rules). |
| [`fixtures/`](fixtures/) | The engine-output + project test-fixture corpus LintCrux is tested against. |
| [`examples/`](examples/) | Ready-to-open projects you run in the app (and one SARIF report that needs no engine). |
| [`.github/ISSUE_TEMPLATE/`](.github/ISSUE_TEMPLATE/) | Bug / fixture issue forms (feature ideas go to Discussions). |

## After the beta

When LintCrux opens its source post-beta, the canonical repository becomes
[`Ferrite-Engineering/lintcrux`](https://github.com/Ferrite-Engineering/lintcrux),
and the open-core test fixtures live there alongside the code. This beta repo is
archived at that point; the in-app issue reporter automatically retargets the
open repo. Until then, **everything happens here.**

---

*LintCrux is built by [Ferrite Engineering](https://ferriteengineering.com).
Thanks for helping us make the beta better.*
