# Submitting a Test Fixture

A **fixture** is a small input plus the result LintCrux *should* produce from it.
Fixtures are how LintCrux proves its engine parsers stay correct: every fixture in
[`fixtures/`](../fixtures/) runs in the test suite, so once an input is in, the
behavior it captures can never silently regress.

If you found an engine message LintCrux drops, misplaces, or mis-ranks — **that
message is the most valuable thing you can give us.** This guide explains how to
submit one and the rules it has to follow.

## The fastest path

1. Open the
   **[fixture submission form](../../issues/new?template=fixture_submission.yml)**.
2. Attach the raw engine output (the exact stdout/stderr text) and the command
   line that produced it — plus the RTL, if you can share it.
3. Tell us the engine and version, what LintCrux currently does, and what it
   *should* do.
4. Confirm the licensing (below).

We take it from there: trim it, snapshot the expected SARIF, document its
provenance, and fold it into the suite. You'll be credited on the resulting
change.

## What a LintCrux fixture actually looks like

There are two shapes, and picking the right one makes your submission land much
faster.

### 1. An engine-output fixture — for parser bugs

This is the one we want most, because it needs **no engine installed** to
reproduce. Three files:

```
my_case/
├── cmdline.txt              # verilator --lint-only -Wall top.sv
├── output.txt               # the exact raw text the engine printed
└── expected.sarif.json      # the violations LintCrux must produce from it
```

That's it. If LintCrux drops a warning, puts it on the wrong line, attributes it
to the wrong file, assigns the wrong severity, or chokes on a multi-line message,
`output.txt` is the entire bug. Paths with spaces, non-ASCII filenames, ANSI
colour codes, continuation lines, and lines from a newer engine version are all
existing fixture categories — yours will fit right in.

**You don't have to write the `expected.sarif.json`.** Just tell us what's wrong
in the issue and attach `output.txt` + `cmdline.txt`; we'll write the golden.

### 2. A project fixture — for rules that should have fired

If the bug is "the engine should have flagged this and nothing showed up", we
need the RTL:

```
my_project/
├── project.lintcrux        # which engines, includes, defines, top module
├── top.sv                  # the source that trips (or should trip) the rule
└── expected.sarif.json     # the violations the run must produce
```

This shape covers engine invocation, language routing (VHDL to GHDL, `.sv` to the
SystemVerilog engines), severity overrides, filelist expansion, and waiver
matching. It needs the engine installed, so keep the source to a handful of
lines.

## What makes a great fixture

- **Small and focused.** One rule, the fewest lines that still fire it. For
  parser bugs, one engine, a handful of output lines. We can trim, but a tight
  input is gold.
- **A known-correct answer.** The bug isn't "the table looks wrong" — it's
  "Verilator printed `%Warning-WIDTHTRUNC` at `alu.sv:88:14` and LintCrux shows
  it at line 87." The more precisely you can state the expected result, the
  faster it becomes a test.
- **Name the engine and version.** `verilator --version`,
  `verible-verilog-lint --version`, `slang --version`, `ghdl --version`,
  `svlint --version`, `yosys -V`. Roughly half of all parser bugs are
  version-specific, and version drift is itself a fixture category.
- **Raw text, not a screenshot.** Copy the terminal output verbatim, colour codes
  and all — the escape sequences are frequently the bug.

## Licensing — please read

Fixtures we publish must be redistributable, because [`fixtures/`](../fixtures/)
is public. We can only accept fixtures under a permissive license:

> **MIT, BSD-2-Clause, BSD-3-Clause, Apache-2.0, ISC, CC0, or public domain.**

- **Your own hand-written module?** Easiest case — by submitting it you agree to
  contribute it under CC0 / public domain so it can live in the test suite.
- **Derived from an open-source project?** Only if that project is under one of
  the licenses above. Tell us the project, the commit/version, the engine
  version, and the exact command line you ran. We record this as provenance.
- **From proprietary, GPL, or AGPL sources, or anything you can't relicense?**
  We can't accept it — please don't attach it. A *hand-rebuilt* minimal case that
  reproduces the same behavior without copying the original is fine. For parser
  bugs this is usually easy: retype the offending output line with your
  identifiers replaced by `foo` / `bar`, and the parser bug survives intact.

Submissions without clear, permissive provenance can't be published, and our
tooling refuses to publish a captured fixture that lacks a provenance record.

## How fixtures are organized

See [`fixtures/README.md`](../fixtures/README.md) for the full layout. In short:

- `generated/` — deterministic cases emitted by our own generators, each with a
  golden `expected.sarif.json`.
- `captured/` — output captured from **permissively-licensed** open-source
  projects at a named engine version, each with a `PROVENANCE.md` recording the
  source project, commit, license, engine version, and capture command, plus a
  hand-verified anchor violation.

Your submission typically becomes a new `captured/` entry (with provenance) or a
new `generated/` case if we can reproduce it with a generator.

Thank you — every case you contribute makes LintCrux's parsers more correct for
everyone.
