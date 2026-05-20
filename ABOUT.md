# About This Repository

## Purpose

This repository delivers a Tampermonkey userscript that re-themes the Amazon Payroll Portal at `https://payroll.amazon.work` using the AWS Cloudscape "Polaris Dark Mode" token palette so it visually matches Optics, the AWS Console, and other Cloudscape-based internal tools.

## Design Principles

- **Cloudscape palette as ground truth.** Surfaces, text tiers, links, borders, focus rings, and primary actions all use Cloudscape tokens (`#161d26` body, `#42b4ff` link, `#ff9900` primary action). No hand-picked hex values.
- **Two-layer architecture.** CSS injected at `document-start` for an immediate dark paint; JS computed-style scanner as the safety net for inline styles, runtime injections, and shadow roots.
- **Hands off media.** Images, video, canvas, picture, and SVG content are excluded from color overrides — payroll dashboards often use SVG bar/line charts that depend on green/red semantics. Inputs are also excluded from the global text recolor so form fields keep readable foreground from the input style block.
- **Hands off the cascade.** No `filter: invert(1)`. Brand colors, status pills, and Amazon Orange CTAs are preserved.
- **Defensive against framework toggles.** A class observer re-asserts `awsui-polaris-dark-mode` on `<body>` if the portal removes it, and `color-scheme: dark` plus `data-color-scheme=dark` are re-asserted on every observer tick.

## Script Architecture

`amazon_payroll_dark_mode.user.js` runs in five phases:

1. **Framework hook.** Sets `color-scheme: dark`, `data-color-scheme=dark`, and `data-theme=dark` on `<html>`, plus the Cloudscape `awsui-polaris-dark-mode` body class.
2. **CSS injection at `document-start`.** A single stylesheet covers nuclear text, transparent cascade, headings, stat amounts, links, inputs, buttons, primary CTAs, cards/widgets, tables with zebra striping, modals, menus, dropdowns, popovers, tooltips, the left navigation, the top bar, banners (info/error/warning), tabs, selected/active states, badges/pills/chips, breadcrumbs, progress bars, scrollbars, and stubborn inline-style overrides.
3. **JS nuclear pass.** Walks every non-media, non-input element, parses computed `background-color` and `color`, and reassigns to the Cloudscape surface or text tier whose luminance bucket matches (>200 → primary, >140 → secondary, >100 → tertiary; brand-blue heuristic → tertiary). Excludes all SVG descendants so chart segments are untouched.
4. **Top-bar detection.** Combines selector- and position-based detection (`fixed`/`sticky`/`absolute` with `top < 100px`) to force header surfaces to `#161d26`.
5. **Mutation observer + load-time safety net.** 250 ms debounce on subtree mutations; explicit re-runs at 500 ms, 1500 ms, and 3000 ms after `load` to catch lazy-loaded payroll widgets and statement rows. Open shadow roots receive the same stylesheet appended.

## Why This Repository Exists

`payroll.amazon.work` is the internal portal Amazon employees use for payroll-adjacent workflows. It ships only a light theme. This script delivers a single, predictable, Cloudscape-aligned dark experience that survives SPA route changes and lazy content while preserving any chart semantics the portal uses to communicate pay or tax composition.

## Maintenance Notes

- Keep `amazon_payroll_dark_mode.user.js` at the repository root so the GitHub raw URL is the canonical Tampermonkey install link.
- Cloudscape tokens are the source of truth. If Cloudscape revises the dark palette, update the constants block at the top of the userscript.
- Validate after each portal update: shell, navigation, top bar, banners, tabs, statement viewer, dashboard tiles.
- Do not add an SVG recolor block. Charts depend on their original fills/strokes.

## Source References

- `AWS\Amazon AWS Dark Mode Standard for LLM.md` (v3.0) — palette, CSS rules, nuclear pass, observer pattern, anti-patterns, framework hooks.
- `Amazon Dark Mode LLM Playbook\AMAZON_DARK_MODE_LLM_PLAYBOOK.md` (v1.0) — naming, folder scheme, source-notation rules, repo initialization workflow under the `BarnsAWS` GitHub org.
