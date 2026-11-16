# Components

The prototype is one HTML file, so a "component" here is a class, its markup, its states and the tokens it uses. This document is the contract a developer would implement as React/Vue/Web Components; the reference implementation is the CSS and markup in `index.html`. States are expressed through standard attributes (`aria-pressed`, `aria-expanded`, `disabled`, `hidden`) and `data-*`, never through extra classes, so behaviour and accessibility stay in one place.

Tokens are referenced by their CSS name; see `tokens.css` / `tokens.json`.

## Primitives

### Button `.btn`
- Variants: default (surface `--panel2`, border `--line`), `.primary` (`--accent` / `--accentText`, the only coloured action), `.ghost` (transparent, `--muted` text), `.danger` (`--bad` border and text, transparent), `.small` (`--fs-s`, `--pad-btn-small`), `.choice` (display font, uppercase, for Accept / Reject).
- States: `:disabled` (opacity .5), `:hover` on `.choice` turns the border `--own`, `:focus-visible` outline `--own`.
- Rule: `--good` and `--bad` are feedback colours. Actions use `--accent`; Accept and Reject are deliberately the same neutral weight so colour does not nudge the decision.

### Link button `.linkbtn`
Quiet uppercase text action for researcher-only controls (results link on the "session complete" screen). `--fs-xs`, dotted underline, opacity .7 → 1 on hover.

### Back link `.back`
Plain text "← All sessions" / "← Start page" above a dashboard head. `--fs-m`, `--muted` → `--fg` on hover.

### Segmented switch `.seg`
`<span class="seg" role="group"><button aria-pressed>…</button>…</span>`. Pressed segment: `--panel2`, `--fg`, weight 600. Used for presentation condition A/B/C.

### Big segments `.segbig`
Touch-sized row of alternatives (min-height 44px) inside `.group`. Hover tint is `--own` at 12%; `.hov` mirrors the hover of the twin helm button on the plot.

### Pill `.pill`
Status label. `.ok` (`--goodSoft`/`--good`), `.over` (`--badSoft`/`--bad`), `.under` and `.run` (`--aiSoft`/`--ai`), `.n` (neutral `--panel2`/`--muted`). An optional leading `<i>` holds the glyph (✓ ● ▲).

### Condition tag `.ctag`
Single letter A/B/C in the display font inside a `--line` border; used in session lists.

### Card `.card`, dashboard card `.card.dash`
Surface `--panel`, border `--border`, radius `--r-l`, padding `--sp-5` (`--sp-7` for `.dash`).

### Eyebrow `.eyebrow`
Small-caps section label in the display font, `--ls-caps`, `--muted`; flex row so a second element (legend, note, button) sits at the right.

### Field `.field`
Uppercase `--fs-s` label wrapping a `select`, `input[type=text]` or `.seg`. Inputs: `--panel2`, `--border`, `--r-s`, `--pad-input`; `[aria-invalid="true"]` turns the border `--bad`.

### Data table `table`
`--fs-m`, cells `--pad-cell`, row dividers `--line`, headers `--muted` weight 500. Inside `.tablewrap` for horizontal scroll. `tr.hl` highlights with `--aiSoft` (the row a claim points at). In `.dash` tables cells are vertically centred and padded `--sp-3`.

### Note field `.notebox`
`textarea.notes` plus either explicit actions (`.noteacts` with Save `.btn.small.primary` and Cancel, shown only while the text differs from storage; `.dirty` paints the border `--ai`) or, with `data-auto`, silent autosave with a `--muted` "saving… / saved" in the corner (`.savedmsg`). Keyboard: Ctrl+Enter saves, Esc cancels (explicit mode only).

### Floating buttons `.fab`
Round `--size-fab` buttons fixed bottom-right, stacked: theme toggle `.themeBtn`, help `.helpBtn` ("?", `aria-expanded`), back-to-top `.toTop` (visible via `.on`). On the workspace they move to the left edge.

### Help drawer `.help` + `.helpVeil`
`role="dialog"` slid in from the right (`min(560px, 100%)`), with a close button, a `.toc` of pill links that scroll the drawer itself, `h3` sections and small tables. Opens from `.helpBtn`; closes on ✕, Esc or a click on the veil; focus returns to where it was. Holds the "how this prototype works" text so the explanation travels with the file.

### Toast `.toast`, note `.note`, fine print `.fine`
One-line feedback (`--good` by default, recoloured inline), secondary text `--fs-s --muted`, and `--fs-xs` legal text.

## Start page

### Stage `.stage` / `.plot` / `.panel`
Two-column console: live canvas with `.overlay` caption and `.sim` clock on the left, title and doors on the right. Fills the viewport under `body.is-start`; stacks under 880px.

### Door `.door` + `.doorpanel`
Entry card: `<button class="door" aria-expanded aria-controls>` with `<b>` title, `<span>` subtitle and `<i>` arrow (↓ / ↑). One door open at a time; its `.doorpanel` unfolds below with a `--line` left rule. Doors: Explore, Run a study session, Researcher dashboard, About the method.

### Recent sessions `.recent`
Inside the researcher's door: header row, up to three `.row` buttons (code `.code`, `.ctag`, time `.when`, status pill, score `.score`, arrow `.go`) and an `.all` link to the overview. Under 880px the time and the word after the score are hidden.

### Method `.method`, steps `.steps`, conditions `.conds`
The "About the method" block: research question `.q` with `--ai` emphasis, numbered steps with `--size-step` counters in `--ai`, and the three-row condition table.

## Participant screens

### Briefing `.brief` + check-in `.checkin`
Instruction card with `.steps`, then a strip with the participant code input (mono, `--fs-4xl`, letter-spaced), session facts and a full-width uppercase Start button.

### Session complete `.donescreen`
Faded copy of the start canvas behind a centred message with the code in a pill `.code`; the researcher's `.linkbtn.corner` sits bottom-right.

## Workspace (operator's instrument panel)

Grid `.work`: 4px timer line `.topline` across the top, then rail · data pane · plot · side. Stacks under 880px.

### Rail `.rail`
Back button `.railbtn` and the vertical tab `.vtab` "Sensor data" (`aria-expanded`, `aria-controls`). Opening the data is a measured act, so it lives here, physically apart from the advisory.

### Sensor data pane `.datapane`
320px panel with eyebrow + clock, the data table and a note on radar vs AIS. `hidden` when closed; the grid columns are placed explicitly so closing it does not shift the plot.

### Plot `.plotarea`
Canvas with `.hud` (brand, projection note), `.sim` clock, `.legend` with swatches `.sw-own .sw-target .sw-ai .sw-muted` (+ `.sw-dashed .sw-dotted`), and the helm `.helm` of five `.hb` round buttons positioned by script at the end of each ghost heading (`.port` is bordered `--bad`). Canvas colours are read from the tokens at draw time. The shorter side spans 2 × 4.7 nm; when the plot is narrower than 0.75 of its height (data pane open) the visible radius follows the range to the target (1.2–4.7 nm) and eases towards it, so the two vessels keep filling the picture. Helm radii and the AI heading length scale with the zoom; vectors and ranges stay true to the mile.

### Advisory `.advisory`
`.tag` (AI ADVISORY), `.act` manoeuvre in the display font `--fs-6xl`, `.enc` encounter line (A only), `.conf` confidence block: `.num` (percentage in B, word in C), `.lbl`, `.meter` of five `<i>` segments filled by `--p`. In C, `.claims` lists `.claim` buttons (label, `.val` in mono, `.check` "check →"), each ≥ 44px, `.hl` when it is the claim the data pane is highlighting; `.rule` holds the COLREG rule and safety limit.

### Decision `.decision`
Eyebrow with `.timeout` note, `.alts` (hidden until Reject; `.group` rows of `.segbig`), `.pair` of two `.btn.choice` (Accept advice / Reject with `aria-expanded`), `.toast`, `#truthBox` with `.truth .correct|.wrong|.plausible` callout in explore mode, `.explore-tools`.

### Trial bar `.trialbar` / explore bar `.explorebar`
Study: "Trial n / 8" in the display font and the countdown in `--ai`. Explore: encounter select and condition switch.

## Researcher dashboard

### Dashboard head `.dhead`
Title `h1` with optional `small`, `.dmeta` line, `.acts` with `.flag` (⚑ warnings), `.navses` prev/next, the Download dropdown `.dd` + `.menu` (`role="menu"`, items with `<b>`, `<span>`, `<em>` filename; `.sep`; `.quiet`), and New session / Start page.

### Research question row `.rq`
Two columns: question `h2`, explanation `.note`, verdict `.rq-verdict` (left rule, `<b>` in `--own`); right, `.rq-cmp` with one box per condition: `h3` condition name, `<b>` big value (`--fs-6xl`), optional `.bar` with `--w`, caption. `.bad` paints value and bar `--bad`; `em.warn` inside the value paints `--ai`.

### Trial sequence `.rows`
Label column `.rl` and four 8-column rows: `.cells` of `.cell.ok|.over|.under`, `.tbars.t` decision-time bars (`--h`, `.to` timeout in `--bad`), `.tbars` data-open bars in `--own` (`.zero` when not opened), `.encs` encounter captions. `.summary` of six big numbers below.

### Reliance matrix `.matrix` / `.mq`
3×3 grid: headers `.h`, quadrants `.mq.ok|.over|.under` with the count in the display font.

### Rate list `.rate`
Rows `.r`: label `.k`, bar `.bar > i` (`--w`), value `.v`. Acceptance by advice type.

### Key-value list `.kvl`
`dl` with mono `dd`; `dd.warn` in `--ai`.

### Timelines `.tl` / `.track`
One `.track` per trial, 0–45 s: `.open` span (data open, `--own` at 55%), `.click` (claim, `--ai`), `.altm` (alternatives, `--muted`), `.dec.ok|.over|.under` glyph at the decision, `.to` red edge on timeout. `.axis` under the list. `.track` is also used standalone in review cards.

### Trials table
Standard table; rows with a note carry `data-tr` and `aria-expanded`, an arrow in `.nmark` (↓ / ↑) and a folded `.noterow` beneath.

### Live review card `.tc`
Session in progress: number `.num`, `h3` with pill (outcome, `.live` "now" or `.pill.n` upcoming), `.adv` sentence, `.track`, `.line` facts (`.bad` for warnings), autosaving `.notebox.auto`. Modifiers `.done`, `.now` (border `--own`), `.todo` (opacity .72), `.warn` (border `--bad`). The `.sofar` strip above repeats the six session numbers.

### Pulse `.live`
Inline status with a pulsing `--own` dot; the keyframe is disabled under `prefers-reduced-motion`.

## Layout rules
- Content max width `--content-max`, page padding `--page-pad`, safe-area insets respected on the workspace.
- One breakpoint at 880px (stack), one at 820px tall (tighter start console).
- Scroll snapping only on the start page (`html.snap`), lifted during programmatic smooth scrolls.
- All motion (timer line, fade of the back-to-top, pings, pulse) respects `prefers-reduced-motion`.
