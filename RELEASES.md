# LintCrux Release Notes

All notable changes between beta builds. New builds are announced in
[Discussions → Announcements](../../discussions/categories/announcements), which
is also where the download links are posted while the beta is opening up.

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
