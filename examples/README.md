# LintCrux Examples

**These are projects you open, not data a test asserts against.** Everything in
[`fixtures/`](../fixtures/) exists to be checked by the suite; everything here
exists to be run by you. We publish them so evaluating LintCrux doesn't start
with "now write a project file" — clone this repo, open one, press `F5`.

> ⚙️ **This tree is generated.** It's mirrored from the LintCrux source tree by
> tooling — don't edit it here.

Launch LintCrux, then **File → Open Project…** (`Cmd/Ctrl+O`), pick the
`project.lintcrux` in one of the directories below, and run it with
**Tools → Run All Engines** (`F5`).

Each example points only at a source file committed beside it, using paths
relative to its own `project.lintcrux`, so it works from any checkout with no
editing.

| Example | What it demonstrates | Needs |
|---|---|---|
| [`getting-started/`](getting-started/project.lintcrux) | The core loop on SystemVerilog: two engines running in parallel, 7 findings over 6 rules, click-to-source, the inspector's rule metadata. | `verilator` **and/or** `verible-verilog-lint` on `PATH` |
| [`vhdl-getting-started/`](vhdl-getting-started/project.lintcrux) | The VHDL path: GHDL's `--warn-*` set driven from the project's `perEngineOptions`, and why `--warn-hide` and `--warn-unused` are worth reading together. | `ghdl` on `PATH` |
| [`getting-started/report.sarif`](getting-started/report.sarif) | **No engine at all.** A real captured report of the example above — open it with **File → Import SARIF report…** to see the violation table, filters and inspector with nothing installed. | nothing |

An engine that is not installed is simply skipped; the run-status panel says
which ones ran. `getting-started` is therefore useful with only one of its two
engines present — and `report.sarif` is useful with none.

## Installing an engine

```bash
# macOS
brew install verilator verible ghdl
# Debian / Ubuntu
sudo apt install verilator ghdl      # verible ships its own release tarball
```

Only `verilator` is needed to get something out of `getting-started`, and it is
the least friction of the three.

## Neither example is clean, on purpose

A report with nothing in it demonstrates nothing. Both designs compile and would
simulate; every finding is the kind of defect a linter exists to catch and a test
bench can easily miss.

### `getting-started/alu.sv` — 7 findings

| Engine | Rule | What it caught |
|---|---|---|
| verilator | `LATCH` | `always_comb` with an incomplete `case` and no `default` infers a latch. The single most consequential finding in the file. |
| verilator | `CASEINCOMPLETE` | the same defect named from the other direction — 3 of 8 `op` encodings unhandled |
| verilator | `UNUSEDSIGNAL` | `carry_scratch` is driven every cycle and read by nobody |
| verilator | `UNDRIVEN` | `overflow_flag` is read by `zero` and driven by nobody |
| verilator | `WIDTHTRUNC` | 8 bits assigned to a 4-bit output, silently dropping the top half |
| verible | `case-missing-default` | the style rule for the same gap, from an independent tool |
| verible | `explicit-parameter-storage-type` | `parameter WIDTH = 8` with no type |

Four of the five Verilator findings only exist because LintCrux passes `-Wall`;
`WIDTHTRUNC` is on by default. That difference is worth knowing when comparing
LintCrux's output against a bare `verilator --lint-only`.
`perEngineOptions.verilator.warnFlags` in the project file controls it.

### `vhdl-getting-started/counter.vhd` — 3 findings

| Rule | What it caught |
|---|---|
| `hide` | a process variable named `value` shadows the architecture signal `value` |
| `unused` | `spare_flag` — declared, never read |
| `unused` | `value`, the *signal* — which looks used, because the process is full of `value`. Every one of those references is the shadowing variable. The two warnings are one bug. |

## Found a bug in one of these?

That's a good find — an example failing is a bug report with a reproduction
already attached. [File an issue](../../issues/new?template=bug_report.yml) and
name the example, plus your engine version (`verilator --version`,
`verible-verilog-lint --version`, `ghdl --version`). LintCrux parses each
engine's text output and those formats drift between releases, so the version is
usually the whole story.
