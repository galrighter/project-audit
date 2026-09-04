# T17 — Accessibility & Localization (incl. RTL)

**Purpose.** Assess whether the product's interfaces can be used by everyone who is entitled to use them (WCAG 2.2 AA as the yardstick, plus applicable law) and whether the product is built for the languages, scripts, regions and formats it targets — with explicit attention to right-to-left languages (Hebrew, Arabic, Persian, Urdu) and bidirectional text, which most codebases get wrong.
**Start from.** UI components and templates, design system, CSS (logical properties, `dir`), locale files and i18n framework config, formatting utilities (dates, numbers, currency), email/notification/PDF templates, accessibility tests and lint rules.
**Cross-links.** Legal obligations are T14 · market signals from locales/currencies feed T12 · frontend performance is T19 · automated tests in CI are T04.

## Checklist

### A. Accessibility (WCAG 2.2 AA)
Semantic structure (headings, landmarks, lists, tables) · keyboard operability and visible focus, no traps, logical order · name/role/value for custom widgets (ARIA used correctly, not as decoration) · color contrast (text 4.5:1, large text/UI 3:1) and information not by color alone · alt text and media alternatives · forms: labels, instructions, error identification and suggestions, autocomplete · target size (24×24 CSS px minimum) and dragging alternatives · motion, autoplay and flashing controls · zoom to 200%/400% and reflow at 320 px · consistent navigation and help · authentication without cognitive tests (2.2) · focus not obscured (2.2) · status messages announced · accessible PDFs and emails where produced · mobile: screen-reader labels, dynamic type, touch targets.

### B. Accessibility process
Automated checks (axe, Lighthouse, pa11y, eslint-plugin-jsx-a11y) in CI · manual testing with a screen reader (NVDA/VoiceOver/TalkBack) evidence · accessibility statement and feedback channel · component library conformance · designers' annotations.

### C. Internationalization architecture
All user-visible strings externalized (grep for hard-coded strings) · a real i18n framework (ICU messages, plurals, gender, select) · no string concatenation for sentences · locale-aware date/time (time zones!), number, currency and unit formatting (`Intl`, ICU, `date-fns-tz`, Babel/`gettext`) · sorting and searching with locale collation · input handling (Unicode normalization, non-Latin names, phone/address formats) · text expansion tolerance in layouts (German +30%) · locale negotiation and persistence · fonts covering the scripts · translation workflow (keys, context, screenshots, review) and completeness (missing keys, stale strings, machine-translated leftovers) · pseudo-localization runs.

### D. Right-to-left and bidirectional text
`dir="rtl"` set at the document/app root per locale and on embedded content · CSS logical properties (`margin-inline-start`, `padding-inline`, `inset-inline`, `text-align: start`) instead of left/right · mirrored layout for navigation, progress, carousels, breadcrumbs, back/forward icons — but not for logos, media controls, clocks, or numeric charts · bidi isolation for mixed text (`<bdi>`, `unicode-bidi: isolate`, `\u2066–\u2069`) so numbers, URLs, emails, product codes and Latin words render correctly inside RTL sentences · numerals and units, percent and currency placement per locale · input fields and caret direction, placeholder alignment, phone-number and code inputs forced LTR · right-aligned tables and proper column order · icons with directional meaning flipped · line-height and font metrics suited to Hebrew/Arabic (ascenders/diacritics clipped?) · truncation and ellipsis with bidi · maps, PDFs, emails and push notifications in RTL · testing on real devices in Hebrew/Arabic locales.

### E. Regional and content localization
Currencies and tax display (→T12) · address and name forms · calendars and week start · legal text per region · imagery and color connotations · support hours and time zones · localized SEO (hreflang) and app-store listings.

## Signals and red flags
`text-align: left` and `margin-left` throughout a product that serves Hebrew users · numbers and mixed Latin/Hebrew strings rendering out of order · contrast failures on primary buttons · custom dropdowns without keyboard support · `alt=""` on informative images · a locale file with 40% missing keys · dates shown in server time zone · forms that reject non-ASCII names.

## Commands (shell) and no-shell fallbacks
- `npx @axe-core/cli <url>`, `lighthouse --only-categories=accessibility`, `pa11y`; grep `margin-left|padding-left|text-align: left|float: left` vs logical properties; count locale keys per language and diff; run pseudo-localization if the framework supports it.
- No shell: read components, CSS and locale files via GitHub; PageSpeed Insights accessibility score on a public URL; manual review of screenshots in RTL locale if provided.

## Output
Findings `T17-###`, the WCAG checklist result by principle, the i18n architecture verdict, the RTL/bidi checklist result, the translation-completeness table, score 1–5 with anchor, and open questions.
