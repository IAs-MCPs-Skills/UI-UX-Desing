---
name: animation
description: UI animation engineering skill based on Emil Kowalski's philosophy. Use when implementing animations, transitions, gestures, spring physics, or reviewing UI motion code. Covers easing decisions, duration tables, clip-path techniques, drag interactions, accessibility (prefers-reduced-motion), and performance (transform/opacity only, CSS vs JS). Produces Before/After markdown tables when reviewing code.
argument-hint: "[component or animation type]"
license: MIT
metadata:
  author: claudekit
  version: "1.0.0"
  source: Emil Kowalski — animations.dev
---

# Animation Engineering

> Knowledge from Emil Kowalski's design engineering philosophy.
> For deeper study: [animations.dev](https://animations.dev/)

## When to Use This Skill

- Implementing entry/exit animations (modals, drawers, toasts, tooltips)
- Choosing easing curves and durations for UI elements
- Adding press/hover feedback to interactive elements
- Building drag, swipe, and gesture interactions
- Reviewing existing animation code for issues
- Deciding whether to animate at all
- Optimizing animation performance
- Handling `prefers-reduced-motion` accessibility

## Core References

| Reference | Purpose |
|-----------|---------|
| [easing-decisions.md](references/easing-decisions.md) | When to use which easing + duration tables |
| [component-patterns.md](references/component-patterns.md) | Buttons, popovers, tooltips, toasts |
| [clip-path-techniques.md](references/clip-path-techniques.md) | Clip-path animations, reveals, tabs, sliders |
| [spring-animations.md](references/spring-animations.md) | Spring physics, drag, gesture interactions |
| [performance.md](references/performance.md) | GPU acceleration, CSS vs JS, WAAPI |
| [accessibility.md](references/accessibility.md) | prefers-reduced-motion, touch hover states |
| [review-checklist.md](references/review-checklist.md) | Code review table with Before/After/Why |

---

## Quick Decision Guide

### Should this animate at all?

| Frequency | Decision |
|-----------|----------|
| 100+ times/day (keyboard, command palette) | No animation. Ever. |
| Tens of times/day (hover, list nav) | Remove or drastically reduce |
| Occasional (modals, drawers, toasts) | Standard animation |
| Rare / first-time (onboarding, celebrations) | Can add delight |

**Never animate keyboard-initiated actions.** Raycast has no open/close animation — that is the right call.

### Easing quick reference

```
Entering or exiting?           → ease-out
Moving/morphing on-screen?     → ease-in-out
Hover / color change?          → ease
Constant motion (marquee)?     → linear
Default?                       → ease-out
```

**Never use ease-in for UI animations.** It starts slow — the exact moment the user is watching most closely.

### Duration quick reference

| Element | Duration |
|---------|----------|
| Button press feedback | 100–160ms |
| Tooltips, small popovers | 125–200ms |
| Dropdowns, selects | 150–250ms |
| Modals, drawers | 200–500ms |
| Marketing / explanatory | Can be longer |

**UI animations should stay under 300ms.**

---

## Review Format (Required)

When reviewing animation code, ALWAYS output a markdown table:

| Before | After | Why |
|--------|-------|-----|
| `transition: all 300ms` | `transition: transform 200ms ease-out` | Specify exact properties; avoid `all` |
| `transform: scale(0)` | `transform: scale(0.95); opacity: 0` | Nothing in the real world appears from nothing |
| `ease-in` on dropdown | `ease-out` with custom curve | ease-in feels sluggish; ease-out gives instant feedback |
| No `:active` state | `transform: scale(0.97)` on `:active` | Buttons must feel responsive to press |
| `transform-origin: center` on popover | `var(--radix-popover-content-transform-origin)` | Popovers scale from trigger, not center (modals stay centered) |

**Never use a list with "Before:" / "After:" on separate lines. Always a table.**
