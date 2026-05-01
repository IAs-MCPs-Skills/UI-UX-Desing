# Easing Decisions

## The Decision Tree

```
Entering or exiting the screen?
  Yes → ease-out (starts fast, feels responsive)
  No →
    Moving or morphing while on screen?
      Yes → ease-in-out (natural acceleration/deceleration)
    Hover or color change?
      Yes → ease
    Constant motion (marquee, progress bar)?
      Yes → linear
    Default → ease-out
```

**Never use ease-in for UI animations.** It starts slow — the exact moment the user is watching most closely. A dropdown with `ease-in` at 300ms *feels* slower than `ease-out` at the same 300ms.

## Custom Easing Curves

The built-in CSS easings are too weak. Use strong custom curves:

```css
:root {
  /* Strong ease-out for UI interactions */
  --ease-out: cubic-bezier(0.23, 1, 0.32, 1);

  /* Strong ease-in-out for on-screen movement */
  --ease-in-out: cubic-bezier(0.77, 0, 0.175, 1);

  /* iOS-like drawer curve (from Ionic Framework) */
  --ease-drawer: cubic-bezier(0.32, 0.72, 0, 1);
}
```

**Resources:** [easing.dev](https://easing.dev/) and [easings.co](https://easings.co/) — don't create curves from scratch.

## Duration Table

| Element | Duration |
|---------|----------|
| Button press feedback | 100–160ms |
| Tooltips, small popovers | 125–200ms |
| Dropdowns, selects | 150–250ms |
| Modals, drawers | 200–500ms |
| Marketing / explanatory | Can be longer |

**Rule: UI animations should stay under 300ms.**

## Perceived Performance

- A **fast-spinning spinner** makes loading feel faster (same load time, different perception)
- A **180ms select** feels more responsive than a **400ms** one
- **Instant tooltips** after the first one is open make the whole toolbar feel faster

## Asymmetric Enter/Exit Timing

```css
/* Release: fast */
.overlay {
  transition: clip-path 200ms ease-out;
}

/* Press: slow and deliberate */
.button:active .overlay {
  transition: clip-path 2s linear;
}
```

Slow where the user is deciding. Fast where the system is responding.

## Stagger Animations

```css
.item {
  opacity: 0;
  transform: translateY(8px);
  animation: fadeIn 300ms ease-out forwards;
}

.item:nth-child(1) { animation-delay: 0ms; }
.item:nth-child(2) { animation-delay: 50ms; }
.item:nth-child(3) { animation-delay: 100ms; }
.item:nth-child(4) { animation-delay: 150ms; }

@keyframes fadeIn {
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
```

Keep stagger delays short (30–80ms). Never block interaction while stagger plays.
