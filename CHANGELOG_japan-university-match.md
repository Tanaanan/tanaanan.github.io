# Changelog — `japan-university-match.html`

Log of all changes made to the "Find Your Japan University Match" tool in this session.

---

## 1. Japanese (日本語) language support

- Added a third language button (**日本語**) alongside ไทย / EN in the nav.
- Full Japanese translations for every piece of static text: nav, hero, "how it works" steps, planner quiz, compare table, badge legend, validation section, limitations, the research-team note, and footer.
- Added Japanese fields to all 6 university-cluster archetypes (name, tagline, tags, description, difficulty, best-for, academic core, international-programs note).
- Added Japanese labels for the radar chart features, university-card badges/warnings/tips, discipline breakdowns, and the map legend.
- Replaced the old binary Thai/English ternaries throughout the rendering code with a single `L(lang, en, th, ja)` helper so all three languages render consistently everywhere (cluster cards, compare table, planner match result).

## 2. Interactivity pass

- **Quick filter chips**: All / Has IDP / English Taught / Tuition Discount / National / Private — combine with the existing search box to filter university cards live.
- **Favorites system**: a ☆ star on each university card to shortlist it, plus a "★ Favorites" filter chip to view only starred schools.
- **Scroll-spy navigation**: the top nav links highlight to show which section is currently in view while scrolling.
- **Planner progress indicator**: 4 dots fill in as each quiz question is answered, plus a "↺ Start over" button to reset the quiz.
- **Radar chart tooltips**: hovering/focusing a data point on a cluster's feature radar shows the exact value (e.g. "Admission: 57.6"), with a hover-grow effect.
- Added an acknowledgment card crediting **International SciKU Branding (ISB), Faculty of Science, Kasetsart University, Thailand** for its support (translated into all three languages).

## 3. Shortlist export (print / download)

- **🖨️ Print / Save as PDF**: builds a rich, print-only summary — a comparison table at the top, then per-university cards with colored archetype badges, IDP/ETP/tuition badges, a 6-stat grid, a full color-coded discipline bar chart, and the personalized "Thai Student Tip" — all matching the on-screen design.
- **⬇️ Download as text**: a `.txt` file with the same level of detail (stats, top disciplines, personalized tip), suitable for pasting into an application tracker.
- **Clear all**: wipes the shortlist after a confirmation prompt.
- Refactored the discipline-list and "Thai tip" logic into shared helper functions (`buildDiscList`, `buildThaiTip`) so the on-screen cards and the print/export view can never drift out of sync.
- All of the above fully localized in Thai/English/Japanese.

## 4. Bug fixes

- **Favoriting a university no longer collapses the cluster you're browsing.** Root cause: the filter-recompute function was force-closing every cluster whenever no search/filter was active. Fixed with a `manualOpenState` map that tracks what the user actually clicked open/closed — now preserved correctly even across language switches.
- **Discipline bars / badges / cluster swatches were invisible in the saved PDF.** Root cause: Chrome/Edge strip anything styled purely with `background`/`background-color` when printing unless explicitly forced. Fixed with `print-color-adjust: exact` in the print stylesheet. Verified with a real headless-Chromium print-to-PDF test (before: blank bars: 0% visible; after: fully rendered).
- **Saving to PDF produced a stack of extra blank pages.** Root cause: the rest of the (very tall) page was hidden with `visibility:hidden`, which still reserves its full layout height for pagination. Switched to `display:none` on everything but the print view. Verified with a controlled test: old CSS → 9 pages (8 blank), new CSS → 1 page.
- **Favorites now intentionally reset on every page refresh** (previously persisted via `localStorage`, per updated requirements) — the favorites `Set` is session-only in memory, and any leftover `localStorage` value from the older persisted version is actively cleared on load.

## 5. Mobile responsiveness pass

- **Header no longer wraps mid-word on narrow screens.** "JP Uni Match" was breaking into "JP Uni" / "Match" across two lines, with the tagline stacking into 3 lines, because the brand name + tagline + 3-way language switch didn't fit on one row. Fixed by hiding the decorative tagline and tightening spacing under 480px.
- **Added a "swipe to see more" hint** above the comparison table on mobile, since it's a wide table inside a horizontally-scrollable container with no visual affordance otherwise.
- Verified the rest of the page (planner, cluster cards, radar/map charts, discipline bars, uni-card badges, shortlist bar, validation/limitations grids, research-team note, footer) already stacks and wraps correctly at 375px width.

## 6. Publication status correction

- Changed every reference from "published" to accurately reflect **in-press** status at **IJIET, 2026**:
  - Hero pill: "IJIET published study" → "In press at IJIET (2026)"
  - "Published in" info chip → label "In press at", value "IJIET · 2026 (in press)"
  - Research-team note paragraph and footer citation updated to say "currently in press"
  - Planner quiz text no longer implies the study is already published
- Left the "📅 Data collected: 2025" note untouched — that's the data-collection year, not the journal's publication status.

## 7. Thai Baht (฿) exchange-rate note

- Added a small info box below the hero stats: *"All costs on this page are shown in US dollars (USD). Quick reference: $1 ≈ ฿36 — exchange rates shift, so check today's rate before budgeting."*
- Translated into Thai and Japanese; styled distinctly (soft green) from the source-note box above it.

## 8. Accessibility

- Cluster cards are now real keyboard/screen-reader-accessible controls: `role="button"`, `tabindex="0"`, `aria-expanded` / `aria-controls`, Enter/Space activation, and a visible focus ring.
- `aria-expanded` stays correctly synced no matter how a cluster opens (click, keyboard, a matching search/filter, or the planner auto-opening your quiz result) via one shared `setClusterOpen()` helper.
- Decorative icons (emoji swatch, caret arrow) marked `aria-hidden`.
- Favorite ★ buttons now expose `aria-pressed` and a per-university `aria-label` (e.g. "Favorite Meiji University") instead of a generic label.

## 9. Open Graph / social share preview

- Added `og:title`, `og:description`, `og:image`, `og:url`, `og:type`, `og:locale` (+ alternates for th/en/ja), matching `twitter:card` tags, a `<meta name="description">`, and a canonical link.
- Generated a new branded 1200×630 preview image (`japan_u_images/og-cover.png`) in the site's own color palette, since none existed — so sharing the link on LINE/Facebook/Twitter now shows a proper title, description, and image card.

## 10. "Compare your shortlist" table

- New **🌟 Compare your shortlist** panel appears automatically under the export bar once at least one university is starred — a side-by-side table (university, archetype, admission, living cost, review, IDP/ETP/tuition) built from the same favorites data as the export feature.
- Each row has its own ★ button that stays in sync with the star on the corresponding cluster card in either direction.
- **Made more visual and easier to read:**
  - Admission, living cost, and review are shown as small colored **bar gauges**, scaled relative only to the universities in your own shortlist (not the full 40).
  - The **cheapest option** gets a 💰 badge and a light-green highlighted cell; the **highest-rated option** gets a 🏆 badge — both bold, with a small legend explaining the icons. Skipped entirely when only one university is starred, since there's nothing to compare against.
- **Responsive without horizontal scrolling**: below 700px width, the table automatically becomes a stack of labeled cards (one per university) instead of a cramped 8-column table — verified at 375px with zero horizontal overflow. At 700px+ it's a normal table.
- **Finalized on the PDF/print export too**: the print view's summary table now has the identical bar gauges and 💰/🏆 winner highlighting as the on-screen version, sharing the same `favComparePct()` scaling logic and `FAVCOMPARE_I18N` legend text — verified with a real headless-Chromium print-to-PDF render, not just the DOM.

---

*All changes verified via automated browser testing (console-error checks, functional JS assertions, and — for the PDF fixes — real headless-Chromium print-to-PDF comparisons) across Thai, English, and Japanese, at both desktop and mobile widths.*
