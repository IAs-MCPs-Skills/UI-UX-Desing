# Spring Animations

Springs feel more natural than duration-based animations because they simulate real physics. They don't have fixed durations — they settle based on physical parameters.

## When to Use Springs

- Drag interactions with momentum
- Elements that should feel "alive" (like Apple's Dynamic Island)
- Gestures that can be interrupted mid-animation
- Decorative mouse-tracking interactions

## Spring-Based Mouse Interactions

```jsx
import { useSpring } from 'framer-motion';

// Without spring: feels artificial, instant
const rotation = mouseX * 0.1;

// With spring: feels natural, has momentum
const springRotation = useSpring(mouseX * 0.1, {
  stiffness: 100,
  damping: 10,
});
```

Use when the animation is **decorative** — it doesn't serve a function. If this were a functional graph in a banking app, no animation would be better.

## Spring Configuration

**Apple's approach (recommended — easier to reason about):**

```js
{ type: "spring", duration: 0.5, bounce: 0.2 }
```

**Traditional physics (more control):**

```js
{ type: "spring", mass: 1, stiffness: 100, damping: 10 }
```

Keep bounce subtle (0.1–0.3). Avoid bounce in most UI contexts. Use it for drag-to-dismiss and playful interactions.

## Interruptibility Advantage

Springs maintain velocity when interrupted — CSS animations and keyframes restart from zero. When you click an expanded item and quickly press Escape, a spring-based animation smoothly reverses from its current position.

## Gesture and Drag Interactions

### Momentum-based dismissal

```js
const timeTaken = new Date().getTime() - dragStartTime.current.getTime();
const velocity = Math.abs(swipeAmount) / timeTaken;

if (Math.abs(swipeAmount) >= SWIPE_THRESHOLD || velocity > 0.11) {
  dismiss();
}
```

Don't require dragging past a threshold. A quick flick should be enough.

### Damping at boundaries

When a user drags past the natural boundary, apply damping. The more they drag, the less the element moves. Things in real life don't suddenly stop — they slow down first.

### Pointer capture for drag

Once dragging starts, set the element to capture all pointer events. Ensures dragging continues even if the pointer leaves element bounds.

### Multi-touch protection

```js
function onPress() {
  if (isDragging) return;
  // Start drag...
}
```

Ignore additional touch points after initial drag begins. Without this, switching fingers causes the element to jump.

### Friction instead of hard stops

Allow upward drag with increasing friction instead of preventing it entirely. Feels more natural than hitting an invisible wall.
