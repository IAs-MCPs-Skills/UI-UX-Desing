# Animation Performance

## Only Animate transform and opacity

These properties skip layout and paint, running on the GPU.

```
✅ transform   — compositor only
✅ opacity     — compositor only
❌ padding     — triggers layout + paint + composite
❌ margin      — triggers layout + paint + composite
❌ height      — triggers layout + paint + composite
❌ width       — triggers layout + paint + composite
❌ top/left    — triggers layout + paint + composite
```

## CSS Variables Inheritance Warning

Changing a CSS variable on a parent recalculates styles for all children.

```js
// Bad: triggers recalc on all children
element.style.setProperty('--swipe-amount', `${distance}px`);

// Good: only affects this element
element.style.transform = `translateY(${distance}px)`;
```

## Framer Motion Hardware Acceleration

Framer Motion shorthand properties (`x`, `y`, `scale`) use `requestAnimationFrame` on the main thread — NOT hardware-accelerated.

```jsx
// NOT hardware accelerated (drops frames under load)
<motion.div animate={{ x: 100 }} />

// Hardware accelerated (stays smooth even when main thread is busy)
<motion.div animate={{ transform: "translateX(100px)" }} />
```

This matters when the browser is simultaneously loading content, running scripts, or painting.

## CSS Animations Beat JS Under Load

CSS animations run off the main thread. When the browser is busy loading a new page, Framer Motion animations (using `requestAnimationFrame`) drop frames. CSS animations remain smooth.

- Use CSS for predetermined animations
- Use JS for dynamic, interruptible ones

## Web Animations API (WAAPI)

JavaScript control with CSS performance. Hardware-accelerated, interruptible, no library needed.

```js
element.animate(
  [
    { clipPath: 'inset(0 0 100% 0)' },
    { clipPath: 'inset(0 0 0 0)' }
  ],
  {
    duration: 1000,
    fill: 'forwards',
    easing: 'cubic-bezier(0.77, 0, 0.175, 1)',
  }
);
```

## Debugging Animations

### Slow motion testing

Temporarily increase duration to 2–5x normal, or use browser DevTools animation inspector.

Things to look for in slow motion:
- Do colors transition smoothly, or are two distinct states overlapping?
- Does easing feel right, or does it start/stop abruptly?
- Is `transform-origin` correct?
- Are multiple animated properties (opacity, transform, color) in sync?

### Frame-by-frame inspection

Step through animations in Chrome DevTools (Animations panel). Reveals timing issues invisible at full speed.

### Test on real devices

Connect your phone via USB, visit local dev server by IP address, use Safari's remote devtools. The Xcode Simulator is an alternative but real hardware is better for gesture testing.
