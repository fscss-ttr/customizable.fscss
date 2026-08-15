# customizable.fscss

Pure CSS page-styling system for the FSCSS ecosystem. It provides the components most sites need repeatedly — navigation, hero sections, feature grids, call-to-action banners, footers, 404 pages, and long-form article typography — as a single module that can be imported in full or in part, then restyled per page through CSS custom properties.

License: MIT
Repository: https://github.com/fscss-ttr/customizable.fscss
Requires FSCSS v1.1.24 or later.

## Table of Contents

1. [Overview](#1-overview)
2. [Installation](#2-installation)
3. [Design Tokens](#3-design-tokens)
4. [Selective Importing](#4-selective-importing)
5. [Components](#5-components)
   - [Navigation](#navigation)
   - [Badge](#badge)
   - [Hero Section](#hero-section)
   - [Images](#images)
   - [Buttons](#buttons)
   - [Feature Grid](#feature-grid)
   - [Call-to-Action Banner](#call-to-action-banner)
   - [Footer](#footer)
   - [404 Page](#404-page)
   - [Article Typography (page-prose)](#article-typography-page-prose)
6. [Customizing a Page](#6-customizing-a-page)
7. [Responsive Behavior](#7-responsive-behavior)
8. [Full Example](#8-full-example)
9. [Design Token Reference](#9-design-token-reference)
10. [Notes on Usage](#10-notes-on-usage)

## 1. Overview

Most landing pages, terms pages, about pages, and 404 pages share the same underlying structure: a navigation bar, a heading with supporting text, a grid of cards, a closing call to action, and a footer. `customizable.fscss` treats each of these as an independent, composable mixin rather than a fixed template. A page can use all of them, a handful, or just the typography component for a blog post — nothing is bundled together that has to be taken as a whole.

Every visual property that a project is likely to want to change — color, radius, spacing, font — is exposed as a CSS custom property through `page-root`. Overriding a token changes it everywhere it is used, without editing the module itself.

## 2. Installation

CDN, runtime compilation:

```html
<script src="https://cdn.jsdelivr.net/npm/fscss@1.1.24/exec.min.js" defer></script>
<style>
  @import((*) from customizable)
</style>
```

CLI, compiled ahead of time for production:

```bash
npm install -g fscss
fscss page.fscss page.css
```

```html
<link rel="stylesheet" href="page.css">
```

The CLI output is identical CSS with no runtime dependency, and is the recommended path once a page is no longer a prototype.

## 3. Design Tokens

`page-root` writes a set of custom properties to `:root` (or any selector passed to it). These are the only values that should be edited when adapting the module to a different brand.

```css
@page-root()
```

To use a different accent color or corner radius, override the token after calling `page-root`:

```css
@page-root()

:root {
  --page-accent: #0fcbe8;
  --page-radius: 12px;
}
```

Because every component reads from these variables rather than hardcoded values, a two-line override changes the accent color across the navigation, badge, buttons, and CTA banner simultaneously.

## 4. Selective Importing

Unlike a single fixed stylesheet, `customizable.fscss` supports importing only the mixins a given page actually uses:

```css
@import((page-root, page-base, page-404, page-prose, page-btn, page-btn-primary) from customizable)
```

A 404 page has no reason to load hero, feature grid, or footer styles it will never render. Importing `(*)` loads every mixin in the module, which is appropriate for a full marketing landing page that uses most of the components at once:

```css
@import((*) from customizable)
```

Selective importing keeps the compiled output proportional to what a given page actually needs, rather than shipping the full module on every page of a site.

## 5. Components

### Navigation

```css
@page-nav(.page-nav)
```

```html
<nav class="page-nav">
  <span class="page-logo">
    <img src="logo.svg" alt="Brand">
    Brand
  </span>
  <div class="page-nav-links">
    <a href="#">Features</a>
    <a href="#">Pricing</a>
    <a href="#">Docs</a>
  </div>
</nav>
```

The navigation bar is sticky by default and applies a blurred background as the page scrolls beneath it. Links wrap on narrow viewports rather than overflowing.

### Badge

```css
@page-badge(.page-badge)
```

```html
<span class="page-badge">Now live — version 1.0</span>
```

A small pill component with a glowing dot, typically placed above a hero heading to announce a launch, version, or status.

### Hero Section

```css
@page-hero(.page-hero)
```

```html
<section class="page-hero">
  <span class="page-badge">Now live — version 1.0</span>
  <h1>A heading that describes the product in one line</h1>
  <p>A supporting sentence that expands on the heading without repeating it.</p>
  <div class="page-hero-actions">
    <a href="#" class="page-btn page-btn-primary">Primary action</a>
    <a href="#" class="page-btn page-btn-ghost">Secondary action</a>
  </div>
</section>
```

Heading size scales fluidly between a minimum and maximum using `clamp()`, so no separate mobile override is required for the heading itself.

### Images

Three related mixins cover image usage across a page:

```css
@page-img(.page-img)
@page-hero-image(.page-hero-image)
@page-figure(.page-figure)
```

`page-img` is the general-purpose image class, used inside feature cards or anywhere an image needs consistent border radius and border treatment. `page-hero-image` is a larger variant intended for a product screenshot or mockup placed below the hero copy, with a heavier shadow. `page-figure` wraps an image together with a caption, intended for use inside `page-prose` on blog posts and articles.

```html
<img class="page-hero-image" src="screenshot.png" alt="Product screenshot">

<figure class="page-figure">
  <img class="page-img" src="diagram.png" alt="Architecture diagram">
  <figcaption>Figure 1. System architecture overview.</figcaption>
</figure>
```

### Buttons

```css
@page-btn(.page-btn)
@page-btn-primary(.page-btn-primary)
@page-btn-ghost(.page-btn-ghost)
```

```html
<a href="#" class="page-btn page-btn-primary">Primary</a>
<a href="#" class="page-btn page-btn-ghost">Secondary</a>
```

`page-btn` provides shared sizing and interaction behavior. `page-btn-primary` and `page-btn-ghost` are applied alongside it to determine the visual treatment.

### Feature Grid

```css
@page-features(.page-features)
@page-feature-card(.page-feature-card)
```

```html
<div class="page-features">
  <div class="page-feature-card">
    <img class="page-img" src="feature-1.png" alt="">
    <h3>Feature name</h3>
    <p>A short description of what the feature does.</p>
  </div>
</div>
```

The grid uses `auto-fit` with a minimum column width, so it reflows automatically as columns run out of space, and collapses to a single column below 640px.

### Call-to-Action Banner

```css
@page-cta-banner(.page-cta-banner)
```

```html
<section class="page-cta-banner">
  <h2>A closing statement that prompts action</h2>
  <p>One supporting sentence.</p>
  <a href="#" class="page-btn page-btn-primary">Call to action</a>
</section>
```

### Footer

```css
@page-footer(.page-footer)
@page-footer-bottom(.page-footer-bottom)
```

```html
<footer class="page-footer">
  <div class="page-footer-top">
    <div class="page-footer-col">
      <h4>Product</h4>
      <a href="#">Features</a>
      <a href="#">Pricing</a>
    </div>
    <div class="page-footer-col">
      <h4>Company</h4>
      <a href="#">About</a>
      <a href="#">Blog</a>
    </div>
  </div>
</footer>
<div class="page-footer-bottom">
  <span>Copyright notice</span>
  <div>
    <a href="#">Social link</a>
  </div>
</div>
```

The footer is split into two components deliberately: `page-footer` handles the multi-column link grid, and `page-footer-bottom` handles the closing copyright row. Sites that only need a simple one-line footer can use `page-footer-bottom` on its own.

### 404 Page

```css
@page-404(.page-404)
```

```html
<div class="page-404">
  <div class="page-404-code">404</div>
  <h1>Page not found</h1>
  <p>A short explanation of what happened, and a way back.</p>
  <a href="#" class="page-btn page-btn-primary">Return home</a>
</div>
```

`page-404` fills the viewport and centers its content vertically and horizontally, independent of whatever else is on the page.

### Article Typography (page-prose)

```css
@page-prose(.page-prose)
```

```html
<div class="page-prose">
  <h1>Article title</h1>
  <p>Body paragraph.</p>
  <h2>A section heading</h2>
  <p>Body paragraph.</p>
  <blockquote>A quoted passage.</blockquote>
</div>
```

`page-prose` applies a readable heading scale, paragraph color, link color, list indentation, blockquote styling, and inline code formatting to any block of long-form content. It is intended for terms of service pages, about pages, and blog posts, where the content is primarily text rather than structured components.

## 6. Customizing a Page

Two independent layers of customization are available, and most pages will use both.

The first is token overriding through `page-root`. This changes color, radius, spacing, and font globally or on a scoped selector, without touching any component definitions:

```css
@page-root()

.dark-variant {
  --page-bg: #050508;
  --page-accent: #ff6b35;
}
```

The second is selective importing, described in section 4. A page imports only the components it uses, then arranges them in whatever order and combination the page requires. There is no fixed page template being filled in; the components are building blocks assembled per page.

A typical landing page imports navigation, hero, features, CTA banner, and footer. A 404 page imports only `page-404` and a button. A blog post imports navigation, `page-prose`, `page-figure`, and footer. Each page loads only what it uses.

## 7. Responsive Behavior

Every structural component includes its own breakpoint at 640px, defined inside the mixin itself rather than as a separate stylesheet. This means responsive behavior travels with the component when it is imported, and does not need to be reimplemented per page.

At and below 640px: the navigation reduces padding and wraps its links, the hero reduces padding and stacks its action buttons to full width, the feature grid collapses to a single column, the CTA banner reduces padding, and the footer's column grid reduces from its default column count to two columns.

Pages that need a different breakpoint or additional responsive behavior can add further media queries in their own stylesheet after importing the module; the module's own breakpoints do not need to be removed to do this.

## 8. Full Example

```html
<script src="https://cdn.jsdelivr.net/npm/fscss@1.1.24/exec.min.js" defer></script>

<style>
@import((*) from customizable)

@page-root()
@page-base(body)
@page-nav(.page-nav)
@page-badge(.page-badge)
@page-hero(.page-hero)
@page-hero-image(.page-hero-image)
@page-btn(.page-btn)
@page-btn-primary(.page-btn-primary)
@page-btn-ghost(.page-btn-ghost)
@page-features(.page-features)
@page-feature-card(.page-feature-card)
@page-img(.page-img)
@page-cta-banner(.page-cta-banner)
@page-footer(.page-footer)
@page-footer-bottom(.page-footer-bottom)
</style>

<nav class="page-nav">
  <span class="page-logo">Brand</span>
  <div class="page-nav-links">
    <a href="#">Features</a>
    <a href="#">Pricing</a>
  </div>
</nav>

<section class="page-hero">
  <span class="page-badge">Now live — version 1.0</span>
  <h1>A modern landing page, built entirely in CSS</h1>
  <p>One supporting sentence describing the product.</p>
  <div class="page-hero-actions">
    <a href="#" class="page-btn page-btn-primary">Get started</a>
    <a href="#" class="page-btn page-btn-ghost">Learn more</a>
  </div>
  <img class="page-hero-image" src="screenshot.png" alt="Product screenshot">
</section>

<div class="page-features">
  <div class="page-feature-card">
    <img class="page-img" src="feature-1.png" alt="">
    <h3>Feature name</h3>
    <p>Short description.</p>
  </div>
</div>

<section class="page-cta-banner">
  <h2>Ready to get started?</h2>
  <p>One closing sentence.</p>
  <a href="#" class="page-btn page-btn-primary">Start now</a>
</section>

<footer class="page-footer">
  <div class="page-footer-top">
    <div class="page-footer-col">
      <h4>Product</h4>
      <a href="#">Features</a>
    </div>
  </div>
</footer>
<div class="page-footer-bottom">
  <span>Copyright notice</span>
</div>
```

## 9. Design Token Reference

| Variable | Default | Purpose |
|---|---|---|
| --page-bg | #0b0a10 | Page background |
| --page-surface | #14121c | Secondary surface background |
| --page-card | #1a1826 | Card and feature background |
| --page-border | rgba(157,126,255,.12) | Default border color |
| --page-text | #f2effa | Primary text color |
| --page-muted | #8b86a3 | Secondary and body text color |
| --page-accent | #2ee6a6 | Primary accent color |
| --page-accent-2 | #17c98a | Secondary accent, used in gradients |
| --page-radius | 20px | Standard corner radius |
| --page-radius-lg | 28px | Larger corner radius, used on hero images |
| --page-pad | 24px | Base horizontal padding unit |
| --page-font | 'Syne', sans-serif | Base font family |

## 10. Notes on Usage

The module does not include a hamburger menu or mobile navigation drawer. On narrow viewports, navigation links wrap onto additional lines rather than collapsing behind a menu icon, since implementing a functioning menu toggle requires JavaScript, which is intentionally outside the scope of this module. Projects that require a collapsing mobile menu should add that behavior separately.

Image mixins style the presentation of an image element but do not handle loading, optimization, or lazy loading. These remain the responsibility of the page itself or the platform serving the image.

Components read from custom properties set by `page-root`. If `page-root` is not called, or is called after a component that depends on its tokens, the affected properties fall back to the browser's default handling of an undefined custom property, which is typically an initial or inherited value rather than the intended design token. `page-root` should be called first, before any other mixin in the module.
