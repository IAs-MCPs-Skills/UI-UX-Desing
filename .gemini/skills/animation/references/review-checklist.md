# Review Checklist

When reviewing UI animation code, output a markdown table:

| Before | After | Why |
|--------|-------|-----|
| `transition: all 300ms` | `transition: transform 200ms ease-out` | Specify exact properties; avoid `all` |
| `transform: scale(0)` | `transform: scale(0.95); opacity: 0` | Nothing in the real world appears from nothing |
| `ease-in` on UI element | `ease-out` or custom curve | ease-in feels sluggish; ease-out gives instant feedback |
| `transform-origin: center` on popover | `var(--radix-popover-content-transform-origin)` | Popovers scale from trigger, not center (modals exempt) |
| Animation on keyboard action | Remove animation entirely | Keyboard actions repeat 100+ times/day |
| Duration > 300ms on UI element | Reduce to 150–250ms | Faster = more responsive feel |
| Hover animation without media query | Add `@media (hover: hover) and (pointer: fine)` | Touch devices trigger hover on tap |
| Keyframes on rapidly-triggered element | Use CSS transitions | Transitions are interruptible; keyframes restart |
| Framer Motion `x`/`y` props under load | `transform: "translateX()"` | Full transform string is hardware-accelerated |
| Same enter/exit transition speed | Exit faster than enter | Enter 2s, exit 200ms — fast system response |
| All elements appear at once | Add stagger delay (30–80ms between items) | Cascading feels more natural |
| No `:active` state on button | `transform: scale(0.97)` on `:active` | Buttons must feel responsive to press |

## The Sonner Principles (for component authors)

1. **Developer experience is key.** No hooks, no context, no complex setup.
2. **Good defaults matter more than options.** Ship beautiful out of the box.
3. **Naming creates identity.** Sacrifice discoverability for memorability when appropriate.
4. **Handle edge cases invisibly.** Users never notice, and that is exactly right.
5. **Use transitions, not keyframes, for dynamic UI.** Transitions retarget smoothly on interruption.
6. **Build great documentation.** Interactive examples lower the barrier to adoption.

## Cohesion Principle

Animation feels satisfying when the whole experience is cohesive. Easing and duration should fit the personality of the component:

- **Playful component** — can be bouncier, slightly slower
- **Professional dashboard** — crisp and fast, minimal bounce
- **Marketing page** — can afford more drama and longer durations

Match the motion to the mood.

## Review Process

1. Play animation at full speed — does it feel right?
2. Slow to 3x — check easing, origin, multi-property sync
3. Step frame by frame (Chrome DevTools) for timing issues
4. Test on real device for touch/gesture interactions
5. Check `prefers-reduced-motion` behavior
6. Review the next day with fresh eyes
