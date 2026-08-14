# T03-Parsa-Fayyazi — grading

Graded against `../../reference-task/` using `../../grading/CHECKLIST.md`.

- **Commit graded:** `e3189f5` ("task-3")
- **Files graded:** `index.html`, `style/index.css`

## Score: raw **47/100** + curve **+20** (raw <50) = **final 67 / 100** · Band: قابل قبول (60+)

Raw breakdown: Design 36/60 · Code 11/40. Curve tiers (per `grading/CHECKLIST.md`):
raw `<50`→+20 · `50–69`→+15 · `70–79`→+10 · `≥80`→+0. Band is based on the **final**,
post-curve score.

> Correct RTL foundation, all components present in the markup, and a real (if
> non-tokenized) color match to the spec. But the responsive system is desktop-first
> (only `max-width` queries) and, worse, several of its breakpoints used `display:none`
> to hide core content — the entire purchase/cart sidebar disappeared below 830px, and
> the grand total, item prices, product thumbnails and section headings vanished below
> 860px. That's not a styling gap, it's data going missing on most phone widths. Combined
> with zero BEM naming (pure `#id` selectors), no `min-width:0` guard, unlabeled form
> inputs (`<h5>` instead of `<label for>`), and checkboxes instead of a radio group for
> shipping, code-structure points were the biggest loss.

## Breakdown

### A. Design fidelity — 36/60
| # | Item | Score | Note |
|---|------|-------|------|
| A1 | RTL correctness | 7/8 | `dir="rtl" lang="fa"` (`index.html:2`); right/left column order correct via DOM order in a flex row; no *explicit* `text-align` anywhere in the CSS — right-alignment works only via the inherited `dir` default, unlike the reference's explicit rule. |
| A2 | Font link valid | 4/4 | Self-hosted `@font-face` (`style/index.css:44-47`) pointing at `font/Estedad.woff2`, which exists in the repo — link resolves. |
| A4 | Design tokens / colors | 4/8 | Primary red `--color-3: #cc312d` matches spec exactly; grey `--color-2: #f7f7f7` and a border token present; but no text-color token at all (body text renders default black, not `#151310`), and **no radius scale** — radii are hardcoded per-rule (`20px`/`40px`/`12px`/`16px`/`32px`/`44px`) instead of reused tokens. |
| A5 | Responsive breakpoints | 1/14 | Base state is desktop (70/30 flex row), not mobile single-column — opposite of the required mobile-first direction. Worse: `#purchase-information{display:none}` at ≤830px hid the *entire* cart/summary sidebar, and a `≤860px` rule hid the grand total, item prices, product thumbnails and two section headings outright. No `560/992/1200` tier, no sticky sidebar, no widened 1200px sidebar. |
| A6 | Restyled native controls | 4/8 | `<select>` correctly uses `appearance:none` plus a custom caret positioned on the visual left (`style/index.css:118-121`, `index.html` inline caret svg). Shipping options used `<input type="checkbox">` (not `radio`), were **not** `hidden`, had no default-selected option, and had no selected-state visual swap at all. |
| A7 | Component completeness | 11/12 | All blocks present: recipient name/phone, province+city+plaque+postal+address, two priced shipping options, three product rows with qty controls, subtotal/shipping/grand-total, Zibal note + CTA. Docked 1 bullet: neither shipping option was preselected (no `checked`, no selected style). |
| A8 | Visual polish | 5/6 | Pill radii present on inputs/CTA (though hardcoded, not tokenized — see A4); card containers and the `#shopping-cart-status` badge pill present; hover states present on shipping options — but no `:focus-visible` ring anywhere in the CSS (the universal reset even zeroes `outline` globally with nothing restoring it). |

### B. Code structure — 11/40
| # | Item | Score | Note |
|---|------|-------|------|
| B1 | Pure HTML+CSS | 6/6 | Zero `<script>` tags; all interactivity is visual-only. |
| B2 | BEM naming | 0/8 | No BEM anywhere — the whole page is styled with plain `#id` selectors (`#user-information`, `#delivery-address`, `#tipax`…) and undashed classes (`.products`, `.image-product`). |
| B3 | CSS custom properties reused | 5/8 | Extensive `:root` var usage reused throughout (`--dis-f`, `--color-*`, `--po-r`, etc. each referenced dozens of times) — mechanically satisfies "reused via var()" — but names are abbreviations, not design tokens, and radii/text-color are never tokenized at all (see A4). |
| B4 | Mobile-first | 0/5 | Every media query is `max-width` (10 desktop-first breakpoints: 1200/1140/1060/990/900/880/860/830/600/400px) — the reverse of the required mobile-first, `min-width` convention. |
| B5 | Grid/flex overflow guard | 0/4 | No `min-width:0` anywhere in the original CSS. |
| B6 | Semantic markup + labels | 0/6 | No `<main>/<section>/<aside>`; every field label was an `<h5>` with no `for`, and every input/select/textarea had no `id` — zero label-input association anywhere. |
| B7 | Icon reuse / DRY | 0/3 | The quantity-control plus icon and the trash icon (each a multi-path inline `<svg>`) were pasted byte-for-byte 3× (once per product row) instead of a `<symbol>`+`<use>` sprite. |

## Strengths
- Correct RTL foundation (`dir`/`lang`, visual right/left column split).
- All required components exist in the markup — nothing structurally missing.
- Primary color and general grey/border palette are close to the Figma spec.
- Custom-property usage is real (just not covering radii/text-color, and not BEM-scoped).
- `<select>` restyling (native appearance removed, custom caret on the visual left) already matched the reference technique.

## Gaps found (fixed in the working tree — see below)
1. `style/index.css` — `#purchase-information{display:none}` at `≤830px` hid the entire cart/summary sidebar on any narrow/mobile viewport → removed; `#contaiter` now switches to `flex-direction:column` and both children go full-width at that breakpoint instead, so the summary stacks below the form rather than vanishing.
2. `style/index.css` (`≤860px` rule) — `.total, #price .line, .image-product, .uncollected-price, #title-how-to-send, #shopping-cart-status{display:none}` hid the grand total, item prices, product thumbnails and two section headings on most phone widths → rule removed, content stays visible.
3. `style/index.css` (`≤880px` rule) — `#advertisement{display:none}` hid the Zibal payment note on most phone widths → removed.
4. `index.html` — shipping options used `<input type="checkbox">` (both independently toggleable, neither preselected) instead of a radio group → changed to `<input type="radio" name="ship">`, first option `checked` + `hidden` (matching the reference's technique), wrapped in `<label>` so the whole row is clickable, and added `#tipax:has(input:checked), #vanguard:has(input:checked)` to `style/index.css` to swap in the same selected visual treatment already used for `:hover`.
5. `index.html` — every field used an unassociated `<h5>` instead of a bound label (7 fields: name, phone, province, city, plaque, postal code, address) → converted to `<label for="fld-…">` with matching `id="fld-…"` added to each input/select/textarea; updated the 3 CSS rules that had targeted those `h5`s to target `label` instead (`style/index.css`).
6. `index.html` — no semantic landmarks → `#contaiter` → `<main>`, `#user-information` → `<section>`, `#purchase-information` → `<aside>` (ids unchanged, so no other CSS/selectors needed updating).
7. `index.html` — the plus-icon and trash-icon SVGs were duplicated verbatim 3× (once per product row) → consolidated into a hidden `<symbol id="ic-plus">`/`<symbol id="ic-trash">` sprite near the top of `<body>`, with the 6 call sites replaced by `<use xlink:href="#ic-…">`.
8. `style/index.css` — reset sets `outline:0` globally with nothing restoring it → added a `:focus-visible` ring (`outline: 2px solid var(--color-3)`), matching the reference.
9. `style/index.css` — no `min-width:0` overflow guard anywhere → added to `#contaiter`, `#user-information`, `#purchase-information`, `.products`, `.product-information` (the flex containers most likely to be blown out by native input/select intrinsic widths or the fixed-width product thumbnails).

**Not fixed / left as recommendations** (to keep the diff reviewable and avoid a full rewrite):
- The responsive system is still desktop-first (`max-width` throughout, magic-number breakpoints at 1200/1140/1060/990/900/880/860/830/600/400 instead of the reference's `560/992/1200` `min-width` tiers) and layout inside `#user-information` still depends on `position:absolute` children with hand-tuned pixel heights (`490px`/`530px`) rather than normal flow. A true mobile-first rewrite of the responsive system would be needed for full A5/B4 credit but is out of scope for a well-scoped fix pass.
- No BEM renaming was attempted (B2) — the entire page is `#id`-selector-driven; converting to `block__element--modifier` would mean touching effectively every HTML tag and CSS selector in the file.
- Radius values and text color remain hardcoded, not tokenized (A4/B3 partial).
- The small inline utility icons (address "select" plus-icon, province/city caret) still use hardcoded stroke colors (`#CC312D`, `#2F0D0C`) rather than `currentColor`.
- The three product thumbnails are inline `<svg>` wrapping base64-encoded PNGs (a Figma raster export), inflating `index.html` to ~640KB. This is a valid approach per the checklist (real image content, not a fixed-width bug), just unusually heavy — left as-is since replacing it with lighter assets isn't a scored rubric item.

## Deviations of note (different but acceptable)
- Right-form/left-summary split is achieved via plain DOM order in a `flex` row (RTL default), not a CSS grid — same visual result as the reference, not penalized.
- Selected-shipping-option styling uses `:has(input:checked)` (a live, pure-CSS reflection of the actual radio state) rather than a static `.ship-option--selected` class — functionally equivalent to the reference and arguably more robust since there's no JS to toggle a class.

## Status
All fixes above are applied to the working tree (`index.html`, `style/index.css`) but **not
committed** at grading time — verified via `git status` showing only modified-in-place files,
no new commits, `git rev-parse --short HEAD` still `e3189f5`. The score above reflects the
**as-submitted** code; a re-grade after these fixes would score meaningfully higher on A5, A6,
A8, B3, B5, B6, and B7. Verification was done by direct HTML/CSS inspection, an HTML-parse
check (parses clean), a CSS brace-balance check, and grep-based duplicate/id-consistency checks
(all label `for=` targets match unique `id=`s, brace count balanced 103/103); interactive
in-browser confirmation at the 375/768/992/1200px breakpoints was not possible in this session
(local browser tooling failed to load the page) — a manual open-in-browser check is recommended
before merge.
