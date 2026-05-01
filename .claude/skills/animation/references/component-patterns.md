# Component Patterns

## Buttons Must Feel Responsive

Add `transform: scale(0.97)` on `:active`. Instant feedback makes the UI feel like it is truly listening.

```css
.button {
  transition: transform 160ms ease-out;
}

.button:active {
  transform: scale(0.97);
}
```

Scale should be subtle (0.95–0.98). Applies to any pressable element.

## Never Animate from scale(0)

Nothing in the real world disappears and reappears completely. Start from `scale(0.95)` or higher, combined with opacity.

```css
/* Bad */
.entering { transform: scale(0); }

/* Good */
.entering {
  transform: scale(0.95);
  opacity: 0;
}
```

## Make Popovers Origin-Aware

Popovers should scale in from their trigger, not from center.

```css
/* Radix UI */
.popover {
  transform-origin: var(--radix-popover-content-transform-origin);
}

/* Base UI */
.popover {
  transform-origin: var(--transform-origin);
}
```

**Exception: modals.** Modals keep `transform-origin: center` — they are not anchored to a specific trigger.

## Tooltips: Skip Delay on Subsequent Hovers

Once one tooltip is open, adjacent tooltips should open instantly with no animation.

```css
.tooltip {
  transition: transform 125ms ease-out, opacity 125ms ease-out;
  transform-origin: var(--transform-origin);
}

.tooltip[data-starting-style],
.tooltip[data-ending-style] {
  opacity: 0;
  transform: scale(0.97);
}

/* Skip animation on subsequent tooltips */
.tooltip[data-instant] {
  transition-duration: 0ms;
}
```

## Use Blur to Mask Imperfect Transitions

When a crossfade feels off, add subtle `filter: blur(2px)` during the transition. Blur bridges the visual gap by blending two states, tricking the eye into perceiving a single smooth transformation.

```css
.button {
  transition: transform 160ms ease-out;
}

.button:active {
  transform: scale(0.97);
}

.button-content {
  transition: filter 200ms ease, opacity 200ms ease;
}

.button-content.transitioning {
  filter: blur(2px);
  opacity: 0.7;
}
```

Keep blur under 20px. Heavy blur is expensive, especially in Safari.

## Animate Entry with @starting-style

Modern CSS way to animate element entry without JavaScript:

```css
.toast {
  opacity: 1;
  transform: translateY(0);
  transition: opacity 400ms ease, transform 400ms ease;

  @starting-style {
    opacity: 0;
    transform: translateY(100%);
  }
}
```

Legacy fallback for older browsers:

```jsx
useEffect(() => {
  setMounted(true);
}, []);
// <div data-mounted={mounted}>
```

## CSS Transitions Over Keyframes for Interruptible UI

CSS transitions can be interrupted and retargeted mid-animation. Keyframes restart from zero.

```css
/* Interruptible — good for UI */
.toast {
  transition: transform 400ms ease;
}

/* Not interruptible — avoid for dynamic UI */
@keyframes slideIn {
  from { transform: translateY(100%); }
  to   { transform: translateY(0); }
}
```

## CSS Transform Reference

### translateY with percentages
Percentage values in `translate()` are relative to the element's own size. Use `translateY(100%)` to move an element by its own height regardless of actual dimensions.

```css
.drawer-hidden { transform: translateY(100%); }
.toast-enter   { transform: translateY(-100%); }
```

### scale() scales children too
Unlike `width`/`height`, `scale()` also scales children. Font size, icons, and content scale proportionally — this is a feature, not a bug.

### 3D transforms for depth
```css
.wrapper {
  transform-style: preserve-3d;
}

@keyframes orbit {
  from { transform: translate(-50%, -50%) rotateY(0deg) translateZ(72px) rotateY(360deg); }
  to   { transform: translate(-50%, -50%) rotateY(360deg) translateZ(72px) rotateY(0deg); }
}
```
