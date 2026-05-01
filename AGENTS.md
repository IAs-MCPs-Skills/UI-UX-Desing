# AGENTS.md

Instructions for AI coding agents (OpenAI Codex, Copilot, Cursor, Windsurf, etc.) working in this repository.

## Project Overview

**Antigravity Kit** is an AI-powered design intelligence toolkit providing searchable databases of UI styles, color palettes, font pairings, chart types, and UX guidelines. It works as a skill/workflow for AI coding assistants.

## Search Command

```bash
python3 src/ui-ux-pro-max/scripts/search.py "<query>" --domain <domain> [-n <max_results>]
```

**Domains:** `product` · `style` · `typography` · `color` · `landing` · `chart` · `ux`

**Stacks:** `html-tailwind` (default) · `react` · `nextjs` · `astro` · `vue` · `nuxtjs` · `nuxt-ui` · `svelte` · `swiftui` · `react-native` · `flutter` · `shadcn` · `jetpack-compose`

## Architecture

```
src/ui-ux-pro-max/        # Source of Truth — edit here
├── data/                 # Canonical CSV databases
├── scripts/              # search.py, core.py, design_system.py
└── templates/            # base/ and platforms/ configs

cli/                      # npm package (uipro-cli)
└── assets/               # Bundled copy of src/ — sync before publishing

.claude/skills/           # Claude Code skills
.gemini/skills/           # Gemini CLI skills
```

**Source of Truth:** always edit in `src/ui-ux-pro-max/`. Never edit in `.claude/` or `.gemini/` directly.

## Git Workflow

- Never push directly to `main`
- Branch: `git checkout -b feat/...` or `fix/...`
- PR: `gh pr create`

---

# Animation Engineering Skill

> Based on Emil Kowalski's design engineering philosophy — [animations.dev](https://animations.dev/)

Use this knowledge when implementing animations, transitions, gestures, or reviewing UI motion code.

## Should This Animate at All?

| Frequency | Decision |
|-----------|----------|
| 100+ times/day (keyboard, command palette) | No animation. Ever. |
| Tens of times/day (hover, list nav) | Remove or drastically reduce |
| Occasional (modals, drawers, toasts) | Standard animation |
| Rare / first-time (onboarding, celebrations) | Can add delight |

**Never animate keyboard-initiated actions.**

## Easing Decision Tree

```
Entering or exiting the screen?    → ease-out
Moving/morphing while on screen?   → ease-in-out
Hover or color change?             → ease
Constant motion (marquee)?         → linear
Default?                           → ease-out
```

**Never use ease-in for UI animations.** It starts slow — the exact moment the user is watching most closely.

### Custom Easing Curves

```css
:root {
  --ease-out:    cubic-bezier(0.23, 1, 0.32, 1);
  --ease-in-out: cubic-bezier(0.77, 0, 0.175, 1);
  --ease-drawer: cubic-bezier(0.32, 0.72, 0, 1); /* iOS-like */
}
```

Resources: [easing.dev](https://easing.dev/) · [easings.co](https://easings.co/)

## Duration Table

| Element | Duration |
|---------|----------|
| Button press feedback | 100–160ms |
| Tooltips, small popovers | 125–200ms |
| Dropdowns, selects | 150–250ms |
| Modals, drawers | 200–500ms |
| Marketing / explanatory | Can be longer |

**UI animations should stay under 300ms.**

## Component Patterns

### Buttons — press feedback
```css
.button { transition: transform 160ms ease-out; }
.button:active { transform: scale(0.97); }
```

### Never animate from scale(0)
```css
/* Bad  */ .entering { transform: scale(0); }
/* Good */ .entering { transform: scale(0.95); opacity: 0; }
```

### Popovers — origin-aware scaling
```css
/* Radix UI */
.popover { transform-origin: var(--radix-popover-content-transform-origin); }
/* Base UI */
.popover { transform-origin: var(--transform-origin); }
```
Modals are exempt — keep `transform-origin: center`.

### Entry with @starting-style
```css
.toast {
  opacity: 1;
  transform: translateY(0);
  transition: opacity 400ms ease, transform 400ms ease;
  @starting-style { opacity: 0; transform: translateY(100%); }
}
```

### Blur for imperfect crossfades
```css
.button-content.transitioning {
  filter: blur(2px);
  opacity: 0.7;
  transition: filter 200ms ease, opacity 200ms ease;
}
```
Keep blur under 20px. Heavy blur is expensive in Safari.

### Stagger animations
```css
.item { animation: fadeIn 300ms ease-out forwards; }
.item:nth-child(1) { animation-delay: 0ms; }
.item:nth-child(2) { animation-delay: 50ms; }
.item:nth-child(3) { animation-delay: 100ms; }
@keyframes fadeIn { to { opacity: 1; transform: translateY(0); } }
```
Keep stagger delays 30–80ms. Never block interaction during stagger.

### Asymmetric enter/exit timing
```css
.overlay               { transition: clip-path 200ms ease-out; } /* release: fast */
.button:active .overlay { transition: clip-path 2s linear; }     /* press: deliberate */
```

## clip-path Techniques

```css
.hidden  { clip-path: inset(0 100% 0 0); } /* hidden from right */
.visible { clip-path: inset(0 0 0 0); }    /* fully visible */
```

- **Hold-to-delete:** `inset(0 100% 0 0)` → `inset(0 0 0 0)` over 2s linear on press; reverse 200ms on release
- **Scroll reveal:** `inset(0 0 100% 0)` → `inset(0 0 0 0)` when element enters viewport
- **Comparison slider:** clip top image based on drag position — no extra DOM elements needed

## Spring Animations

Use springs for drag interactions, interruptible gestures, and decorative mouse-tracking.

```jsx
import { useSpring } from 'framer-motion';

const springRotation = useSpring(mouseX * 0.1, { stiffness: 100, damping: 10 });
```

**Config:**
```js
{ type: "spring", duration: 0.5, bounce: 0.2 }  // Apple approach
```

Keep bounce 0.1–0.3. Springs maintain velocity when interrupted — CSS keyframes restart from zero.

### Momentum-based dismissal
```js
const velocity = Math.abs(swipeAmount) / timeTaken;
if (Math.abs(swipeAmount) >= SWIPE_THRESHOLD || velocity > 0.11) dismiss();
```

## Performance

**Only animate `transform` and `opacity`** — GPU-only, skips layout and paint.

```js
// Bad: triggers style recalc on all children
element.style.setProperty('--swipe-amount', `${distance}px`);

// Good: only affects this element
element.style.transform = `translateY(${distance}px)`;
```

**Framer Motion shorthand (`x`, `y`, `scale`) is NOT hardware-accelerated:**
```jsx
// Drops frames under load
<motion.div animate={{ x: 100 }} />

// Hardware-accelerated
<motion.div animate={{ transform: "translateX(100px)" }} />
```

**WAAPI — JS control with CSS performance:**
```js
element.animate(
  [{ clipPath: 'inset(0 0 100% 0)' }, { clipPath: 'inset(0 0 0 0)' }],
  { duration: 1000, fill: 'forwards', easing: 'cubic-bezier(0.77, 0, 0.175, 1)' }
);
```

## Accessibility

```css
@media (prefers-reduced-motion: reduce) {
  .element { animation: fade 0.2s ease; /* remove transform-based motion */ }
}

@media (hover: hover) and (pointer: fine) {
  .element:hover { transform: scale(1.05); }
}
```

## Code Review Table

When reviewing animation code, always output a markdown table:

| Before | After | Why |
|--------|-------|-----|
| `transition: all 300ms` | `transition: transform 200ms ease-out` | Specify exact properties; avoid `all` |
| `transform: scale(0)` | `transform: scale(0.95); opacity: 0` | Nothing in the real world appears from nothing |
| `ease-in` on UI element | `ease-out` or custom curve | ease-in feels sluggish at the critical first moment |
| `transform-origin: center` on popover | `var(--radix-popover-content-transform-origin)` | Popovers scale from trigger (modals exempt) |
| Animation on keyboard action | Remove entirely | Repeated 100+ times/day — never animate |
| Duration > 300ms on UI element | Reduce to 150–250ms | Faster = more responsive perception |
| Hover without media query | Add `@media (hover: hover) and (pointer: fine)` | Touch devices trigger hover on tap |
| Keyframes on rapidly-triggered element | Use CSS transitions | Transitions are interruptible; keyframes restart |
| Framer Motion `x`/`y` props under load | `transform: "translateX()"` string | Full transform string is hardware-accelerated |
| Same enter/exit speed | Exit faster than enter | Fast system response, deliberate user action |
| All elements appear at once | Stagger 30–80ms between items | Cascading feels more natural |
| No `:active` state on button | `transform: scale(0.97)` on `:active` | Buttons must feel responsive to press |
