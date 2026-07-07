# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

`notenbuch.html` is a **single self-contained HTML file** — a German teacher's grade book ("Notenbuch"). All HTML, CSS, and vanilla JS (`"use strict"`, no frameworks) live inline in this one file. There is no build step, no package manager, no dependencies, no tests, and no git.

To run it: open `notenbuch.html` in a browser (Chrome/Edge recommended — see persistence below). The whole UI is in German; keep all user-facing strings, comments, and identifiers in German to match the existing style.

## Persistence model

There is **no server and no localStorage**. All data lives in a user-chosen `.json` file on disk:

- **Chrome/Edge** use the File System Access API (`showOpenFilePicker`/`showSaveFilePicker`, gated by `hatFSA`). With a `fileHandle` open, `markiereAenderung()` autosaves via a 1.5s debounce timer directly back into the file.
- **Other browsers** fall back to a hidden `<input type=file>` for open and a download link for save (no autosave).

`migriere(state)` runs on every load to upgrade older file formats to the current shape (e.g. synthesizing per-class `kategorien` from a legacy global `einstellungen.gewichte`). **When you change the persisted data shape, add a migration step here** so existing files keep working.

## Grading domain model (the core logic)

State is `{ version, klassen: [] }`. Each `klasse` has `schueler`, `leistungen`, `overrides`, and `kategorien`, plus a `notensystem` fixed at creation:

- `"note"` — Sek I, grades **1–6** (1 = best), quarter-grade steps, with a year grade.
- `"np"` — Kursstufe, **Notenpunkte 0–15** (15 = best), each half-year is its own final grade, **no** year grade.

The two systems invert direction (best grade is the min for `note`, the max for `np`), so grade math throughout branches on `system`/`notensystem`. `NP_BAND` maps 0–15 back onto the 1–6 color bands (`g1`–`g6`).

**Calculation chain (bottom-up):**
- `leistungsNote(l, sid)` — one student's grade on one Leistung. Either a directly-entered `note`, or derived from per-Teilaufgabe points (`vp`) summed and looked up in `l.schluessel` via `noteAusSchluessel`.
- `kategorieSchnitt(kl, sid, kategorie, hj)` — weighted mean of all Leistungen in a category, each Leistung weighted by its `gewicht` (a per-Leistung *Faktor*).
- `gesamtDezimal(kl, sid, hj)` — weighted mean across top-level categories by category `gewicht` (percent).
- `jahresDezimal` = `gesamtDezimal(..., null)` (whole year; `note` system only).

**Two critical rules that thread through the code:**
1. A missing entry (`leistungsNote` returns `null`) means "not planned for this student" and is **excluded**, never counted as 0. Preserve this.
2. **Category folding** (`kat.faltetZu`): a category can fold into another (e.g. "Test" or "GFS" counts toward "Klassenarbeit") instead of carrying its own weight. `kategorieVon(kl, id)` resolves a Leistung's type to its effective top-level category. Only categories that are `aktiv` and have no `faltetZu` contribute a percent weight; those weights are meant to sum to 100 (checked/shown in the Gewichtung view, but not enforced — missing categories are renormalized per student).

**Notenschlüssel** (points→grade thresholds) generators: `notenschluesselLinear`, `notenschluesselKnick` (two-segment interpolation with a bend at grade 4), and `notenschluesselKursstufeFix` (fixed 30-VP table). Thresholds are stored per-Leistung in `l.schluessel` and are hand-editable in the UI.

**Overrides** (`kl.overrides[sid]`): teachers can manually pin a `hj1`/`hj2`/`jahr` grade, overriding the computed value. Borderline cases at x.5 (`istGrenzfall`) are flagged orange to invite an override.

## Rendering

Full re-render on every change — no virtual DOM, no diffing. `renderAll()` → `renderKlassenNav` + `renderViewNav` + `renderMain()`; `renderMain` dispatches on `aktiveView` (`uebersicht` | `leistungen` | `schueler` | `einstellungen`) or `offeneLeistung` (the grade-entry mask). Each `renderX` builds an HTML string via template literals, assigns `innerHTML`, then re-attaches event handlers by querying `data-*` attributes. Any state mutation should call `markiereAenderung()` (marks dirty + schedules autosave) and then re-render.

**Always pass user data through `escapeHtml()`** when interpolating into these template-literal strings — it's the only XSS guard. `csvEscape` is the separate escaper for CSV export.
