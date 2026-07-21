# LintCrux Public Beta — Participant Guide

Thanks for taking part in the LintCrux public beta. This guide explains what the
beta is, how to get the most out of it, and how your feedback turns into a better
release.

## What the beta is

LintCrux is in **free, all-features-unlocked public beta**. Every capability —
including the Pro features (managed waivers, baseline and delta, trend tracking,
custom regex rules, Verible auto-fix, multi-project workspaces) — is enabled for
every beta user, with no license key required. Pro features still carry a `PRO`
badge so you know which tier they'll land in; during the beta the badge is
information, not a gate.

The application source is closed during the beta. This repository
(`lintcrux-beta`) is the public channel for bug reports, feature requests, help,
and the test-fixture corpus.

## Installing

Download the latest beta build for your platform from the LintCrux website; new
builds are announced in
[Discussions → Announcements](../../discussions/categories/announcements). Beta
builds self-identify in **Help → About LintCrux** — the version and build SHA are
one click away via **Copy Version Info**, and the in-app reporter fills them in
for you.

Supported platforms: **Linux**, **macOS**, and **Windows**. There is also a
**read-only web viewer** that opens a SARIF document in the browser — handy for
sharing a result set with someone who doesn't have the engines installed.

### You bring the engines

LintCrux is a dashboard, not a lint engine. It never parses your RTL itself — it
runs the tools you already have and normalizes what they print:

| Engine | Default | Notes |
|---|---|---|
| **Verilator** (`--lint-only`) | enabled | The functional-correctness workhorse |
| **Verible** | enabled | Style and consistency; also powers Pro's auto-fix |
| **Slang** | enabled | SystemVerilog conformance edge cases |
| **Yosys** checks | opt-in | Structural checks via `check` |
| **GHDL** | opt-in | VHDL — LintCrux routes `.vhd` here and nowhere else |
| **Svlint** | opt-in | Synthesizable-subset rules |

Put the binaries on your `PATH`, or point LintCrux at each one with
`--verilator-path`, `--verible-path`, `--slang-path`, `--yosys-path`,
`--ghdl-path`, `--svlint-path`, or via **Settings → Engines**. Enable the engines
you want per project.

Telling us the engine version in a bug report is the single highest-value line
you can add — output formats drift between releases.

## What's most useful to test

All feedback is welcome, but these areas move the needle most during beta:

- **Run it on your real RTL.** Not a toy module — the design with 400 warnings
  you've been ignoring. Tell us what the table gets wrong, what's slow, and what
  you can't filter down to.
- **Engine coverage and parsing.** Every engine, on your machine, at your
  version. **A violation the engine printed that LintCrux didn't show — or showed
  with the wrong file, line, column, rule ID, or severity — is the single most
  valuable bug you can file.** Multi-line messages, paths with spaces, non-ASCII
  filenames, and coloured terminal output are the usual culprits. Attach the raw
  engine output and we can turn it into a parser test in minutes.
- **Waiver authoring.** Waive a violation with a reason. Waive a rule across a
  file. Re-run and confirm it stays suppressed — and that the *right* thing stays
  suppressed when the line numbers move. Inline `lint_off` pragmas count too:
  tell us any pragma form we don't honour.
- **Baseline and delta.** Set a baseline on a dirty tree, make a change, and
  check that "new violations" means exactly what you'd expect after a rename, a
  file move, or a reformat.
- **Trend accuracy.** Run over days or over a branch's history. Do the rule
  trends, severity-class drift, and calendar heatmap match what actually
  happened? A trend line that disagrees with reality is a real bug, not a
  cosmetic one.
- **Severity mapping.** Each engine has its own severity scheme and LintCrux
  normalizes them. Anything mapped to a severity you'd argue with — and any rule
  you want to re-rank per project — is worth telling us about.
- **Projects and filelists.** Import your Vivado-style `.f` with `+incdir+`,
  `+define+`, `-f` recursion, and environment variables in paths. Anything that
  doesn't carry over is a bug.
- **Mixed-language repos.** Verilog, SystemVerilog, and VHDL in one project, with
  the right engine on each.
- **Export and hand-off.** Export SARIF and open it in the web viewer, or feed it
  to GitHub / GitLab code scanning. Anything they reject is a bug.
- **Cross-probing.** Run LintCrux next to WaveCrux or NetCrux and bounce a
  violation across. Report anything that gets acknowledged but doesn't highlight.
- **Cross-platform + window behaviour.** Resize aggressively, go full-screen, try
  a narrow window, switch light/dark themes and the colour presets.

## How to report

### Bugs and crashes — from inside the app (best)

Use **Help → Submit Issue** (also in the command palette and the About box). It
assembles a report with your app version, platform, OS, locale, and an optional
diagnostics snapshot and screenshot, then opens a pre-filled new-issue form in
this repo. This is the highest-signal way to report, because the reproduction
context is captured automatically.

**Privacy:** the report never includes RTL source, violation messages from your
code, or file paths — only counts, formats, and environment metadata. Each toggle
in the dialog shows exactly what it adds, and you see the full body before it's
sent.

Filing by hand works too: [bug report form](../../issues/new?template=bug_report.yml).

### Feature requests and ideas

Post them in [Discussions → Ideas](../../discussions/categories/ideas), where
other beta users can discuss and upvote them and we turn accepted ones into
tracked issues. Tell us the lint workflow you're trying to complete, not just the
widget you want — it helps us find the best solution.

### Questions, help, and discussion

[GitHub Discussions](../../discussions) is the single community hub for the
beta — [Q&A](../../discussions/categories/q-a) for help,
[Ideas](../../discussions/categories/ideas) for feature requests,
[Show and tell](../../discussions/categories/show-and-tell) for what you've
built. (We're keeping everything here rather than running a Discord or Slack, so
answers stay searchable and in one place.) Keep crashes and defects in Issues so
they hit the triage queue.

## How feedback is handled

- Issues are triaged and labelled (`bug`, `beta-feedback`, platform). The in-app
  reporter applies these automatically.
- Reproducible reports — *especially ones with an attached engine log or source
  file* — are prioritized, because we can turn them into a regression test.
- Fixture submissions that pass the license check are folded into the LintCrux
  test suite, so the bug you found stays fixed.

## Contributor recognition

The beta runs on community help, and we don't take it for granted. Meaningful
contributions during the beta — solid reproducible bug reports, engine-output
donations that expose real parser edge cases, translations, and community help —
are recognized when LintCrux launches. Details of the contributor program are
announced on the LintCrux website and in Discussions.

## After the beta

When LintCrux opens its source, the canonical repo becomes
[`Ferrite-Engineering/lintcrux`](https://github.com/Ferrite-Engineering/lintcrux)
and the in-app reporter retargets it automatically. This beta repo is archived
at that point. Until then, everything happens here.

Thank you for helping shape LintCrux.
