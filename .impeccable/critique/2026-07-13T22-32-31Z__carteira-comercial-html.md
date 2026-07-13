---
target: carteira-comercial.html
total_score: 21
p0_count: 1
p1_count: 3
timestamp: 2026-07-13T22-32-31Z
slug: carteira-comercial-html
---
Method: dual-agent (Assessment A: isolated design-review sub-agent · Assessment B: isolated detector/browser-evidence sub-agent — neither saw the other's output)

## Design Health Score

| # | Heuristic | Score | Key Issue |
|---|-----------|-------|-----------|
| 1 | Visibility of System Status | 3 | Toasts/sync-info are good, but `#toast` has no `aria-live`/`role="status"` — silent for screen readers |
| 2 | Match Between System and Real World | 3 | Domain vocabulary (CNPJ, maquininha, Dock) is fluent; the "only Status==='Aprovado' counts" rule is applied silently, never surfaced in copy |
| 3 | User Control and Freedom | 2 | No undo anywhere; client delete and tab delete are instant with zero confirmation |
| 4 | Consistency and Standards | 2 | Three different destructive-action patterns coexist: native `confirm()`, a custom modal, and nothing |
| 5 | Error Prevention | 2 | CNPJ/duplicate checks are solid, but a reversed período (De/Até) silently yields an all-zero table with no warning |
| 6 | Recognition Rather Than Recall | 3 | Auto-suggests client name from sales history; gap: zero visible help anywhere |
| 7 | Flexibility and Efficiency of Use | 1 | Everything is one-row-at-a-time: no bulk select, no re-sortable columns (permanently sorted by resultado desc) |
| 8 | Aesthetic and Minimalist Design | 3 | Clean and restrained; undercut by near-invisible input borders (~1.3:1 contrast, measured) |
| 9 | Error Recovery | 2 | Upload error copy is well-worded but identical regardless of actual cause; no recovery once a client is deleted |
| 10 | Help and Documentation | 0 | No tooltip, no help affordance, no explanation of the "Aprovado"-only filter or the two distinct red badge states |
| **Total** | | **21/40** | **Acceptable — significant improvements needed before users are happy** |

## Anti-Patterns Verdict

**LLM assessment**: No, this does not read as AI-generated. No generic card grids, no gradient text, no glassmorphism, no marketing eyebrows, no hero-metric template. The IBM Plex Sans/Mono pairing, the receipt metaphor (perforated edge, dashed dividers, monospace values), and the shell/paper palette form a coherent, deliberate system matching the documented "recibo" personality. The one spot brushing against a banned pattern is `.brand-mark`'s diagonal gradient on the "$" icon — small and restrained, not a real offender, but the only gradient in the file and worth a second glance.

**Deterministic scan**: The static CLI scan (`detect.mjs --json`, no config/ignore overrides) is genuinely clean — exit 0, `[]`. But the **browser-rendered** scan (script-injected into a live page, not just parsed source) found 6 real issues the static pass structurally can't catch because they depend on resolved CSS custom properties and computed styles:
- 2× `low-contrast`: `#ffffff` on `#0F8A5F` at 4.4:1 (need 4.5) — the "Carregar arquivo de vendas" primary button
- 1× `low-contrast`: `#0F8A5F` on `#ffffff` at 4.4:1 — the "R$ 0,00" result value (passes in practice: this is 22px bold, which only needs 3:1 as large text — a detector false-positive-in-context, not a real failure)
- 2× `tiny-text`: 11px mono labels/table headers
- 1× `cream-palette`: page background `rgb(241,238,228)`

All 6 are real, source-verified matches (no fabricated findings) — but two need design judgment layered on top of the raw flag: the cream-palette hit is technically correct (it IS a cream/beige background) but is a **false positive for AI-slop intent** — this is a pre-existing, deliberately-kept palette from before this project even had Impeccable installed, not a default choice we're making now (PRODUCT.md's own principle: "refinar o sistema existente, não descartá-lo"). Similarly the 11px tiny-text flag is consistent with the product register's permission for density in a data-heavy tool; it's not automatically wrong. The genuinely actionable one is the button contrast — see Priority Issues.

**Visual evidence**: Both sub-agents rendered the page headless (this sandbox has no interactive browser for a live "[Human]" tab) and read the resulting screenshots directly — empty state, a populated table (3 sample clients added through the real UI), row hover, and the "Nova aba promocional" modal all confirmed visually, not assumed from source. No overflow/clipping/broken-layout was visible in the *desktop* viewport; the real breakage is mobile-only (see P0 below), confirmed via direct DOM measurement (`scrollWidth` vs. viewport width, plus simulated wheel/touch-drag that didn't move the table).

## Overall Impression

The system this tool is built on is genuinely good — restrained, on-brand, not a shred of AI-slop. The gap is almost entirely in what happens *after* the happy path: nothing here is undoable, nothing here is reachable by keyboard beyond basic tab order, nothing here is announced to a screen reader, and on a phone the two most consequential columns in the table (who has volume, and the controls to act on it) are physically unreachable. The single biggest opportunity is closing that gap between "looks trustworthy" and "behaves safely" — right now the visual design writes a check the interaction design doesn't cash.

## What's Working

1. **The receipt metaphor is executed with genuine restraint** — dashed rule, perforated edge, mono digits for money — it earns its personality without decorating for decoration's sake.
2. **Auto-fill of client name from historical sales data** (`suggestedNameForCNPJ`) is a small, real intrinsic-load reducer — exactly the kind of scaffolding a product-register tool should reward.
3. **Upload feedback copy is unusually good**: "Base acumulada: N transações aprovadas... histórico agora cobre 01/07 a 13/07" tells the rep precisely what happened, not just "success."

## Priority Issues

**[P0] Mobile table clips the Status and Actions columns off-screen entirely.**
- **Why it matters**: Measured directly: `.table-wrap` has `overflow:hidden` while the table's real width (642px) exceeds the 390px-viewport's available 348px. Neither wheel-scroll nor touch-drag moves it. That means on a phone, the status badge (who has volume) and every row action (Excluir, Mover para…) are not just cramped — they are unreachable by any normal gesture. A rep checking their portfolio from the field cannot delete or move a single client, and cannot even see who has volume.
- **Fix**: Give the table wrapper `overflow-x:auto` below the 760px breakpoint already used elsewhere in the file, or collapse to a stacked-card layout on mobile.
- **Suggested command**: `/impeccable layout`

**[P1] The primary action button's text fails contrast — measured at 4.4:1, needs 4.5:1.**
- **Why it matters**: "Carregar arquivo de vendas (Dock)" — the single most important call-to-action on the page — renders white text on `--green` (#0F8A5F) at 13px, which is real body-size text (13px bold doesn't clear the "large text" 14px-bold threshold), and fails WCAG AA by a hair. This was caught only by the browser-rendered scan, not the static one.
- **Fix**: The `--green-strong` (#0C6E4C, 5.43:1) token already added during the last polish pass for badge text is the right fix here too — use it as the button background instead of `--green` wherever white text sits on top.
- **Suggested command**: `/impeccable polish` (small, contained token swap)

**[P1] Destructive actions have three inconsistent guardrail levels.**
- **Why it matters**: Wiping the entire portfolio requires a native `confirm()`; deleting a promotional tab or a single client requires nothing — one click, instant, no undo. The smaller, far more frequent action has the weaker guardrail, which is backwards from what error prevention should look like, and a misclick permanently loses a client's manually-entered delivery date and history.
- **Fix**: Pick one pattern for every delete — an undo-toast with a 5s "Desfazer" reads better here than more modals — and apply it uniformly to client delete, tab delete, and portfolio reset.
- **Suggested command**: `/impeccable harden`

**[P1] Every form input lacks a programmatic label, and the toast has no `aria-live`.**
- **Why it matters**: `#inCnpj`, `#inNome`, `#inEntrega`, and `#modalInput` all sit next to `<label>` tags with no `for` attribute (confirmed: zero `for` attributes exist in the document) — no input in the app has a real accessible name. Separately, `#toast` has no `aria-live`/`role="status"`, so every confirmation and error message is invisible to screen-reader users. This is a total blackout of heuristic #1 for that population, not a minor gap.
- **Fix**: Add matching `id`/`for` pairs on every label; set `role="status" aria-live="polite"` on `#toast`.
- **Suggested command**: `/impeccable harden`

**[P2] The one feature that matters depends on an unbundled external CDN script, against the tool's own positioning.**
- **Why it matters**: Both assessments independently flagged this. PRODUCT.md's own positioning is "arquivo único, sem backend, fricção zero" — but reading the Dock export requires `cdnjs.cloudflare.com`'s xlsx script to load. On a locked-down corporate network (plausible at a fintech/adquirente), the upload silently breaks and shows "Confirme se é a exportação de transações da Dock (.xlsx)" — blaming the file when the real cause is a missing library never being loaded in the first place.
- **Fix**: Inline/bundle the XLSX parser so the file is truly dependency-free, matching what the product already claims to be. (Google Fonts failing is lower-stakes — it degrades typography to a system fallback, not a feature.)
- **Suggested command**: `/impeccable harden`

## Persona Red Flags

**Alex (Power User)**
- No keyboard shortcut anywhere beyond native Tab/Enter; the "Nova aba promocional" and "Renomear aba" modals have no Escape-to-dismiss — Alex will hit Esc reflexively and nothing happens.
- The table is permanently sorted by `resultado` descending with no alternative — Alex can't sort by `entrega` to triage overdue machine deliveries, or alphabetically to find one client fast.
- Moving 30 clients out of a finished promo tab means 30 individual dropdown interactions — the classic "one-item-at-a-time workflow where batch would be natural" red flag.

**Sam (Accessibility)**
- Confirmed via grep: zero `for`-linked labels exist anywhere in the document — screen readers fall back to placeholder text only, which disappears on typing and isn't a reliable accessible name.
- `#toast` has no `aria-live` — every "Cliente incluído," "Cliente excluído," and upload-error message is silent to AT users.
- Input borders (`var(--line)`, #DEDACB) measure ~1.2–1.4:1 contrast against white/paper-dim backgrounds — far under the 3:1 minimum for UI-component boundaries (WCAG 1.4.11); confirmed visually, the CNPJ/Nome/Entrega boxes barely read as bounded fields.
- On mobile, the status badge and row-action controls are physically clipped out of the viewport with no scroll affordance — a low-vision or motor-impaired user has no path to those controls at all, not even a difficult one.

**Riley (Stress Tester)**
- Setting "De" after "Até" (reversing the período) produces a silent all-zero receipt and table — indistinguishable from "every client genuinely has no volume," which is precisely the signal this tool exists to surface accurately. No inline validation catches it.

## Minor Observations

- Two visually-identical red "off" badges ("Sem dados de venda" vs. "Sem volume no período") mean different things (never sold vs. zero in this window) but render identically — a first-time user can't tell them apart without reading closely.
- `#periodStart`/`#periodEnd` (the header date pickers) are excluded from the app's own `:focus-visible` rule set — they fall back to the browser-default focus ring, inconsistent with the rest of the app's ink-colored outline.
- Table `<th>` cells don't declare `scope="col"` — a small semantic gap for screen-reader table navigation.
- The detector's `tiny-text` (11px) flag is consistent with the product register's density permission for a data-dense tool — not automatically wrong, but worth a look if any of that text is genuinely hard to read at arm's length.
- `.brand-mark`'s diagonal gradient is the one gradient in the file — restrained, but worth a second glance given the product register's gradient-text ban (this isn't text, but it's adjacent).

## Questions to Consider

1. Three different guardrail levels exist for deleting things (`confirm()`, a modal, nothing) — if every destructive action got one consistent, lightweight pattern (an undo toast) instead, would the tool feel both safer and less naggy at the same time?
2. The tool's whole pitch is "open the HTML, zero install, zero friction" — does leaning on a third-party CDN for the one feature that matters (reading the Dock file) actually deliver on that promise?
3. If "reconciling this week's file against last week's" — not "viewing a static list" — were treated as the primary designed task, would clients still be organized one dropdown-move at a time, or would something like a diff view ("3 clients went from zero to active since your last upload") become the real headline of the screen?
