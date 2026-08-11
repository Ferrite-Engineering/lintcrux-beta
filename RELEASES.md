# LintCrux Release Notes

All notable changes between beta builds. New builds are announced in
[Discussions → Announcements](../../discussions/categories/announcements), which
is also where the download links are posted while the beta is opening up.

---

## 0.7.1 — 2026-08-11

One fix, and it is the kind worth shipping on its own: clock-domain-crossing
analysis could report a genuinely unprotected crossing as protected.

### Fixed

- **A captured value in use is no longer mistaken for a synchronizer.**
  Recognising a two-flop chain came down to "some register in the destination
  domain reads this one's output" — which describes a synchronizer, and equally
  describes the most ordinary thing a design does with a value it just
  captured:

  ```verilog
  always @(posedge clk_b) captured <= data_bus;  // the crossing
  always @(posedge clk_b) scratch  <= captured;  // ordinary downstream use
  ```

  Structurally identical, opposite verdicts required. The consequence was that
  adding perfectly normal logic downstream of a crossing made the crossing stop
  being reported — a clean result on a real hazard, which is the one answer a
  CDC tool must never give. A related case reported an unsynchronized bus as
  "combinational logic inside the synchronizer", which kept the finding but
  described the wrong problem.

  A staging flop is private by definition, so recognition now requires the
  first flop's output to feed nothing but the second stage. **If you have run
  CDC analysis on a design where a crossed value is used downstream — which is
  most designs — re-run it on this build.** Both tiers were affected.

- **Findings name the bus.** Every crossing in a design written with a reset
  reported as "an unnamed 8-bit bus", because the name was read from a net that
  the elaborator generates rather than one you wrote. They now read
  `8-bit bus \`data\``.

### Also

- Other performance and quality enhancements.

---

## 0.7.0 — 2026-08-11

A clock-domain release. LintCrux now finds clock-domain crossings itself —
structurally, from the elaborated netlist — rather than pointing at someone
else's engine. Around it: a searchable catalog of every rule the engines can
emit, a shorter route from a violation back to the source that caused it, and
one file that opens a design across the whole suite.

### New

- **Clock-domain-crossing analysis.** Three rules in Open Core:
  `cdc/unsync-single-bit` (a single bit crosses with no synchronizer — the
  destination flop can go metastable), `cdc/unsync-multi-bit` (a bus crosses
  unsynchronized, so the destination can latch a mix of old and new bits — a
  value that never existed in the source domain), and `cdc/combo-in-sync`
  (combinational logic sits between the two flops of a synchronizer, which
  destroys the settling time it exists for while still looking like a
  synchronizer to a reviewer). Findings land in the same violations table as
  every other engine, with the same waivers, trends, SARIF export and
  cross-probe. Every run states what it analysed, so silence means "checked and
  clean" rather than "did not run".
- **Clock-tree tracing, a constraints file, and the fix that looks careful
  (Pro).** Open Core resolves each register's clock to the literal net driving
  it and treats every distinct net as asynchronous to every other. That cannot
  miss a crossing, but it also means a clock through a buffer, an enable gate
  or a divider looks like a different clock on the far side — on a design with
  one gated clock, a handful of real crossings can become hundreds of reported
  ones. Pro traces the clock tree through buffers, inverters, gates, muxes and
  divider flops, with nothing to configure, so one oscillator behind three
  clock nets is recognized as one domain. A `cdc.yaml` beside your sources
  declares what no structural analysis can see — clocks fed from a shared PLL,
  synchronous groups, handshake-qualified buses, crossings you have reviewed —
  and is read identically by the desktop app and by `lintcrux-pro --ci`,
  because a CDC gate that only works with a window open is not a gate. Pro also
  recognizes N-flop chains, gray-coded pointers, and `cdc/per-bit-sync-bus`:
  a bus "synchronized" with a separate two-flop chain per bit. Every warning
  goes quiet and the design is *more* dangerous than before — the chains
  resolve independently, so bits that changed together can land in different
  destination cycles and the receiver reads a value that was never present
  anywhere. Use a handshake or an async FIFO.
- **The Rule Browser.** 644 rules across all seven engines in one searchable
  catalog — every rule an engine can emit, with its severity, its tags and a
  link to its upstream documentation. Search by rule id or tag, filter by
  engine, or click a rule to filter the violations table down to it. It answers
  "what does this engine even check for" without waiting to trip over it.
- **A Sources panel, and a way back.** The left rail lists every source file the
  project holds, each with a one-click remove that rewrites the project and
  re-runs (the file on disk is untouched). On the right, **Back to engine
  status** returns from the Inspector to the run summary, and the diagnostics
  panel can be closed.
- **One file opens a design in all four products.** Write a `.crux-project`
  manifest at the root of your design — naming the dump, the RTL, the lint
  project and the regression config — check it in next to the RTL, and open it
  in any Crux product. LintCrux opens the `lint` artifact, and says so plainly
  when a manifest does not name one yet. All four products derive the same
  design identity from it, so cross-probing works exactly as it does when you
  open each file by hand. Open Core in all four products.

### Fixed

- **Closing a project while its waivers are still loading no longer throws.**
  The waiver store came back from its file I/O and emitted into a stream that
  had already closed, which surfaced as an unhandled error with a stack trace
  pointing at the wrong place. A store torn down mid-reload is ordinary
  shutdown, not a fault.

### Also

- Other performance and quality enhancements.

---

## 0.6.0 — 2026-08-04

An engines release, with a RISC-V flavour: the lowRISC / OpenTitan style guide
now ships as a rule profile, and every lint engine we advertise was audited
against the real binary rather than against what the manifest claimed.

### New

- **The lowRISC / OpenTitan style guide as a rule profile.** Select it and
  LintCrux checks your RTL against the same Verible conventions the OpenTitan
  and Ibex projects use — useful whether or not you are working on RISC-V, and
  the obvious starting point if you are.
- **Open Source Files is on the toolbar**, gated on having a project open, so
  the action is reachable without going hunting for it.
- **Two example projects and a no-engine SARIF report.** Open them with nothing
  installed and see real findings, including what a report looks like before
  you have wired up an engine.

### Fixed

- **If you have run projects with GHDL, slang or Verilator, re-run them on this
  build.** Those engines could report fewer findings than they should have.
- **Every engine download URL in the manifest is real.** The ones that were not
  fetchable are corrected or removed — notably a Verible pin that could not be
  downloaded at all, which is now guarded against recurring.
- **The GHDL and Slang adapters work against their pinned versions.** Both were
  written against different releases than the ones we ship.
- **Verilator is audited against the actual binary**, and project fixtures are
  now captured from real runs rather than hand-written expectations.
- **Project goldens no longer carry the capture host's absolute paths**, so
  re-capture is reproducible on any machine.

### Also

- **Linux requirements are now measured, not asserted.** Our published glibc
  figure had drifted from what we actually shipped; every release build now
  verifies it. LintCrux requires glibc 2.34, which means it runs on RHEL /
  Rocky / AlmaLinux 9, Ubuntu 22.04+ and Debian 12+.
- Other performance and quality enhancements.

---

## 0.5.0 — 2026-07-31

A release about working across more than one project at a time, and about
Windows finally behaving.

### New

- **Work across projects.** A project switcher, a recents panel, and
  cross-project search, so a codebase split over several LintCrux projects is
  one place to look rather than several.
- **Severity and trend colours come from the theme.** Severity and trend now
  resolve through proper theme token catalogs, so they stay legible and
  consistent in both light and dark rather than being hard-coded.

### Fixed

- **Verilator and GHDL are found on Windows.** LintCrux now augments the
  persistent PATH to locate the engines, instead of failing to find an
  installed toolchain.
- **Windows paths in engine output parse correctly.** Drive-letter paths
  (`C:\…`) in Verilator / GHDL diagnostics were being mis-parsed, so those
  violations did not land where they belonged.
- **The CLI advertises the right exit code.** The usage block claimed exit 4
  for an unusable positional argument; it is 64.
- **Quit works from every route.** The menu item did nothing on some screens.
- **The violations pane no longer overflows** with long rule names or paths.
- **A sensible minimum window size** (800×500) on macOS, Windows and Linux,
  so the layout can no longer be crushed into an unusable state.

### Also

- **One consistent suite.** The menu bar, toolbar, status bar and Settings are
  now shared components across all four apps, and panels moved to a
  VS Code-style dock model: bottom, right and left regions, tabs you can drag
  between docks, restore bars for collapsed regions, and direction-aware hide
  controls. The run control is now a single morphing button with real
  enablement, and the duplicate footer tally is gone. The welcome screen
  gained an animated app logo and now shows the running version — handy in
  the browser, where there is no menu bar to check.
- Other performance and quality enhancements.

> **A note on version numbers.** LintCrux desktop builds shipped as part of the
> 2026.07 suite beta before this file caught up. The app now reports `0.5.0`,
> matching its three siblings and the suite release it ships in.

---

## Unreleased — 0.1.0

The first public beta. Notes land here the day it ships; until then this file is
the placeholder that tells you where to look.

What 0.1.0 is expected to cover, so you know what to point at it:

- **Six lint engines, one table.** Verilator, Verible, and Slang run by default;
  Yosys checks, GHDL, and Svlint are opt-in per project. Each engine's output is
  parsed into a single unified violation model.
- **Violation table** — virtualized, sortable by column, filterable by severity,
  rule, and file, with a search dialog and three built-in filter presets.
- **Source navigation** — the source preview pane, and double-click to open the
  violation's exact line in your editor.
- **Per-project severity overrides** and inline `// verilator lint_off` /
  `lint_on` pragma waivers.
- **Export** to SARIF 2.1.0, JSON, CSV, and HTML.
- **Projects** — a `.lintcrux` project file, raw source files, or a Vivado-style
  `.f` filelist (with `+incdir+`, `+define+`, recursive `-f`, and environment
  variables); `.lintcrux-session` and `.lintcrux-workspace` for multi-tab
  workspaces with split panes.
- **Auto-reload and incremental re-run** when your sources change.
- **Cross-probing over CXP** — highlight a rule or source location from a peer
  Crux app.
- Linux, macOS, and Windows, plus a read-only web viewer that opens a SARIF
  document with no engine installed.
- Four display languages: English, 简体中文, 日本語, 한국어.

Pro-tier features — managed waivers with an audit log, baseline and delta,
trend tracking (rule trends, severity-class drift, project trends, the calendar
heatmap), custom regex rules, saved filter presets, violation bookmarks, lint-run
caching, Verible auto-fix, multi-project workspaces, and CXP origination to
WaveCrux and NetCrux — are **unlocked for everyone during the beta**, and carry a
`PRO` badge so you can tell which is which.
