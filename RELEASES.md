# LintCrux Release Notes

All notable changes between beta builds. New builds are announced in
[Discussions → Announcements](../../discussions/categories/announcements), which
is also where the download links are posted while the beta is opening up.

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
