# LintCrux Test Fixtures

**This is what LintCrux tests against.** Every case here is part of the LintCrux
test suite — each input has a known-correct expected result, and the engine
parsers, severity mapping, and reporting are validated against it on every
change. We publish the corpus so you can see exactly how LintCrux is verified,
replay the cases yourself, and
[contribute your own](../docs/SUBMITTING_FIXTURES.md).

During the public beta all features are unlocked, so you can open both the
open-core and the Pro fixtures in the app and watch every feature work against
them.

> ⚙️ **This tree is generated.** It's mirrored from the LintCrux test suite by
> tooling — don't edit it here. To contribute a fixture, use the
> **[fixture submission form](../../issues/new?template=fixture_submission.yml)**;
> see [SUBMITTING_FIXTURES.md](../docs/SUBMITTING_FIXTURES.md).

Looking for something you can just **open and run**? That's
[`examples/`](../examples/) — two ready-made projects, one of which needs no
lint engine installed at all. This tree is test data; those are tools.

## Layout

```
fixtures/
├── open-core/
│   ├── engines/<engine>/       # Raw engine output → expected SARIF, per case
│   │   ├── ghdl/               #   captured/ + generated/
│   │   ├── slang/              #   captured/ + generated/
│   │   ├── svlint/
│   │   ├── verible/
│   │   ├── verilator/
│   │   └── yosys/
│   ├── projects/               # Whole .lintcrux projects + recorded engine runs
│   └── sarif/                  # A SARIF report for the import path
└── pro/
    ├── waivers/                # Waiver matching + audit
    ├── trends/                 # Trend alerting
    └── cxp_originate/          # Cross-probe target resolution
```

## The two fixture shapes

**Engine-output cases** (`engines/<engine>/<tier>/<case>/`) are the backbone.
Each case is three files: `cmdline.txt` (the exact invocation, e.g.
`verilator --lint-only -Wall top.sv`), `output.txt` (the raw text the engine
printed, byte for byte), and `expected.sarif.json` (the violations LintCrux must
produce from it). Because the engine output is committed, these run with **no
lint engine installed** — which is what makes them cheap enough to have one for
every parser edge case: multi-line messages, paths with spaces, non-ASCII
filenames, ANSI colour codes on a TTY, unrecognized lines, and output-format
drift between engine versions.

**Project cases** (`projects/<name>/`) are a `project.lintcrux` plus the sources
it names plus an `expected.sarif.json`. These cover engine invocation, language
routing (`.vhd` to GHDL, `.sv`/`.v` to the SystemVerilog engines), severity
overrides, and end-to-end aggregation across engines — including a deliberately
clean project, so "reports nothing" stays a tested outcome.

## The goldens are derived from real binaries, not written by hand

This is the part worth reading. `projects/<name>/expected.sarif.json` is not
somebody's idea of what the engines *should* say. Every project fixture also
commits:

| File | What it is |
|---|---|
| `engine-output/<engineId>.json` | the recorded subprocess — argv, stdout, stderr, exit code, byte for byte |
| `capture.json` | which binary produced it, at which version, on what day — and any engine left unverified, with the reason |
| `expected.sarif.json` | the golden, **derived** from those recordings by replaying them through the real parsers |

That structure exists so an expectation cannot drift away from what the tools
actually do. A golden can only change if the recording behind it changes, and
`capture.json` forces every recording to name the binary and version that
produced it — so "we could not verify this engine on this machine" is something
the corpus states out loud rather than something that reads identically to "we
checked it." Every engine every project fixture enables currently has a
recording from a real binary; none are unverified.

You can replay all of it. The recordings are plain JSON, and the paths inside
the goldens are relative to the fixture directory, so they mean the same thing
in your checkout as in ours.

## The two fixture tiers

- **`generated/`** — deterministic cases emitted by LintCrux's own generators.
  100% reproducible; the unit-test backbone.
- **`captured/`** — output captured from **permissively-licensed open-source
  projects** at a named engine version, exercising the parsers against text
  nobody on this team wrote. Each `captured/` case carries a **`PROVENANCE.md`**
  recording the source project, commit, license, engine version, capture command,
  and a hand-verified anchor violation, alongside the same `expected.sarif.json`.

Community submissions land in `captured/`. A static test enforces the layout —
no loose files outside a case directory — and blocks any captured fixture whose
license falls outside the allow-list.

## Licensing

`generated/` fixtures are produced by LintCrux's own generators and are released
into the public domain (CC0).

`captured/` fixtures retain the license of the upstream project they were derived
from — always one of **MIT, BSD-2-Clause, BSD-3-Clause, Apache-2.0, ISC, CC0, or
public domain**. The exact source and license for each is in that case's
`PROVENANCE.md`. LintCrux's publishing tool **aborts** rather than publish a
captured fixture that lacks a provenance record, and the test suite blocks any
fixture outside the license allow-list.

If you reuse a captured fixture, honor the upstream license named in its
`PROVENANCE.md`.
