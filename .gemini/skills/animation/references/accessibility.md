# Accessibility

## prefers-reduced-motion

Animations can cause motion sickness. Reduced motion means **fewer and gentler** animations, not zero. Keep opacity and color transitions that aid comprehension. Remove movement and position animations.

```css
@media (prefers-reduced-motion: reduce) {
  .element {
    animation: fade 0.2s ease;
    /* Remove transform-based motion */
  }
}
```

```jsx
const shouldReduceMotion = useReducedMotion();
const closedX = shouldReduceMotion ? 0 : '-100%';
```

## Touch Device Hover States

Touch devices trigger hover on tap, causing false positives. Gate hover animations behind this media query:

```css
@media (hover: hover) and (pointer: fine) {
  .element:hover {
    transform: scale(1.05);
  }
}
```

## Animation Decision Framework for Accessibility

When in doubt:
- Keep color transitions — they aid comprehension
- Keep opacity transitions — they signal state changes
- Remove position-based motion — it causes the most vestibular discomfort
- Remove scale animations on large elements
- Never autoplay animations that loop indefinitely without a pause control
