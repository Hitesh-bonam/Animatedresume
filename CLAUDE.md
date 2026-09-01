# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-file animated HTML resume/CV for Bonam Hitesh Phaneendra (DevOps Engineer). The entire site — markup, CSS, and JavaScript — lives in [index.html](index.html). There is no build system, package manager, or test suite; it's a static page meant to be opened directly in a browser or served via a static file server.

## Development

- Open [index.html](index.html) directly in a browser to preview, or use the configured Live Server (VS Code `Live Server` extension, port 5501 per [.vscode/settings.json](.vscode/settings.json)).
- No install, build, lint, or test commands exist — there is no `package.json` or tooling in this repo.
- All changes are made directly in [index.html](index.html); there are no other source files to keep in sync.

## Structure of index.html

The file is organized top-to-bottom as three blocks within the single HTML document:

1. **`<style>` block** — all CSS, driven by CSS custom properties defined on `:root` (light theme) and overridden under `[data-theme="dark"]` (dark theme). Every color, shadow, and glow references these variables (`--primary`, `--bg`, `--card`, `--text-main`, etc.) rather than hardcoded values, so theme changes should go through the variable definitions, not individual rules.
2. **Body markup** — a `.container` of `.content-card` sections (Professional Summary, Core Skills, Professional Experience, Education, Certificates — each carries an `id` matching a `.section-nav` link for scrollspy), preceded by a hero `<header>` and `.stats-grid`, and followed by a `<footer>`. Resume content (jobs, skills, education, certs) is plain markup in these sections — edit it directly rather than via data/config.
3. **`<script>` block** — vanilla JS (no framework, no dependencies) handling: theme toggle + persistence (`localStorage`, guarded with try/catch since it's unavailable in some contexts), scroll-progress bar, animated stat counters and skill-bar fills (triggered via `IntersectionObserver` on scroll into view), scrollspy for the sticky section nav, a pointer-only cursor-spotlight effect, back-to-top button, and print/share actions.

## Key conventions

- Theming is entirely CSS-variable based (`[data-theme="light"|"dark"]` on `<body>`); when adding new UI elements, define colors via existing `var(--...)` tokens so both themes stay consistent. Initial theme is applied by an inline script at the top of `<body>` (before other content renders) to avoid a flash of the wrong theme — don't move theme-setting logic to `DOMContentLoaded`, and don't reintroduce JS-driven icon swapping (the toggle's sun/moon icon is pure CSS via `::before` on `[data-theme]`).
- Skill proficiency bars use a `--skill-percent` inline custom property on `.skill-fill`, combined with the `.skill-card.animated` class toggle (added via JS on scroll-into-view) to trigger the width transition — don't set the width directly.
- Animations respect `prefers-reduced-motion` via a global override near the end of the `<style>` block; new animated elements should continue to be covered by that existing wildcard rule rather than needing individual opt-outs.
- Responsive breakpoint is `max-width: 768px`, handled in a single `@media` block near the end of the stylesheet.
- A `@media print` block (end of `<style>`) hides chrome-only UI (action bar, back-to-top, section nav, background effects) and forces cards/animations into their final visible state, since the resume's primary "download" path is browser print-to-PDF — keep this block in sync when adding new fixed/animated elements.
- Interactive controls (`.action-btn`, `.theme-toggle`, `.back-to-top`, social links) are real `<button>`/`<a>` elements with `aria-label`s, not styled `<div>`s — keep new controls keyboard- and screen-reader-accessible the same way.
- The social links in the header (`.social-links`) point to LinkedIn, GitHub profile, and this repo's own GitHub URL (used as the "portfolio" link, since it mirrors this project's git remote) — update these three together if any of them change.
