# DEPRECATED — superseded by the Web Dark Mode Bundle

> ⚠️ **This standalone userscript is deprecated as of v2.0 of the bundle.**
>
> All sites previously covered by this repo (and 10 others) are now consolidated into a single auto-updating Tampermonkey userscript:
>
> **https://github.com/BarnsAWS/Web-Dark-Mode-Bundle**
>
> Direct install URL:
>
> `https://raw.githubusercontent.com/BarnsAWS/Web-Dark-Mode-Bundle/main/web_dark_mode_bundle.user.js`
>
> ## Migration
>
> 1. Open Tampermonkey dashboard.
> 2. **Disable or remove** this script (otherwise two scripts will fight on the same page).
> 3. Open the install URL above and click **Install**.
> 4. Tampermonkey will auto-update from `main` going forward.
>
> ## Why the bundle exists
>
> - One install, one auto-update across all covered sites.
> - Shared Cloudscape v3.3 engine — every site gets the same standard at the same time.
> - Adding a new site is a one-file change to `SITES` in the bundle.
>
> The original README/ABOUT and userscript are retained below this notice for archival / reference.
>
> ---

# Amazon Payroll Dark Mode TamperMonkey Script

A Tampermonkey userscript that applies an AWS Cloudscape-aligned dark theme to `https://payroll.amazon.work` (Amazon internal Payroll portal).

## What This Repository Contains

- `amazon_payroll_dark_mode.user.js` — the Tampermonkey userscript.
- `README.md` — installation and verification instructions.
- `ABOUT.md` — purpose, design principles, and architecture notes.

## Behavior Coverage

The script:

- forces a Cloudscape "Polaris Dark Mode" palette on the portal shell, navigation, top header, content pane, and dashboard widgets
- preserves the full Cloudscape text hierarchy and brightens stat amounts (Gross / Take Home / Taxes / etc.) to emphasis text
- applies semantic alert tints (red error, blue info, amber warning, green success) and rounds banner corners to the Cloudscape `12px` radius
- styles the Cloudscape top navigation, side navigation, breadcrumbs, tabs, and selected/active states
- forces dark surfaces on dropdowns, popovers, modals, menus, listboxes, tooltips, and the awsui dropdown surfaces
- **leaves all SVG and canvas chart content untouched** so any payroll bar/line/donut visualizations keep their original semantics
- runs a JS nuclear pass with luminance-based reclassification for inline-styled or runtime-injected light surfaces
- detects sticky/fixed top bars by position and forces them to `--bg-primary` (`#161d26`)
- pierces open shadow roots and re-applies the same stylesheet
- observes DOM mutations with a 250 ms debounce and runs delayed safety passes at 500 ms, 1500 ms, and 3000 ms after `load`
- defensively re-attaches the `awsui-polaris-dark-mode` body class against framework toggles

## Install on Chrome

1. Install Tampermonkey: <https://chromewebstore.google.com/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo>
2. Open the Tampermonkey dashboard and confirm it is enabled.
3. Open the raw userscript URL and let Tampermonkey prompt for install:
   `https://raw.githubusercontent.com/BarnsAWS/Amazon-Payroll-Dark-Mode-TamperMonkey-Script/main/amazon_payroll_dark_mode.user.js`
4. Click **Install**.
5. Visit `https://payroll.amazon.work` and refresh once.

## Install on Firefox

1. Install Tampermonkey: <https://addons.mozilla.org/en-US/firefox/addon/tampermonkey/>
2. Open the Tampermonkey dashboard.
3. Open the raw userscript URL above and click **Install**.
4. Visit `https://payroll.amazon.work` and refresh once.

## Verification Checklist

- [ ] Page body and main content render on `#161d26`.
- [ ] Top bar renders on `#161d26` with a `#424650` bottom border.
- [ ] Side navigation is dark with readable labels; the active page shows a blue-tinted selected state.
- [ ] Cards, widgets, and Cloudscape `awsui_container` panels render on `#1b232d` with `#424650` borders.
- [ ] Dashboard stat amounts are bright white emphasis text.
- [ ] Tabs — selected tab is bright white with a `#42b4ff` underline; inactive tabs are muted `#8c8c94`.
- [ ] Banners render with the correct semantic tint (info=blue, error=red, warning=amber).
- [ ] Tables have alternating `#1b232d` / `#232b37` rows and `#232b37` headers.
- [ ] Links are `#42b4ff` and lighten to `#89cbff` on hover.
- [ ] Dropdowns and popovers open on `#1b232d` and item hover lifts to `#232b37`.
- [ ] Primary CTAs render in Amazon Orange `#ff9900` with dark text and the Cloudscape pill `20px` radius.
- [ ] No bright white flash on initial load.
- [ ] Charts (if present) keep their original fills/strokes.

## Troubleshooting

- **Bright sections after load** — hard refresh (Ctrl+F5) and confirm Tampermonkey is enabled for `payroll.amazon.work`.
- **Top bar still bright** — switch tabs/sections once; the position-based top-bar detector runs on every mutation.
- **Embedded iframe still light** — Tampermonkey does not inject into cross-origin iframes by default. The script's `@match` includes `https://*.payroll.amazon.work/*`; if the portal embeds iframes from other Amazon domains, add a matching `@match` line.
- **PDF statement viewer renders bright** — PDF.js viewers run in a sandboxed iframe; rely on the PDF reader's native dark mode or open the PDF in a new tab.
- **Cloudscape component looks half-dark** — the portal may have its own `awsui-polaris-dark-mode` class observer fighting the script. The class observer in the script re-asserts the class; if the issue persists, file it with the affected component's class names.

## Source References

- AWS Cloudscape Dark Mode Standard for LLM v3.0 (color tokens, nuclear pass, observer pattern, top-bar detection, anti-patterns)
- Amazon Dark Mode LLM Playbook v1.0 (file/folder scheme, source-notation rules, repo init workflow)
