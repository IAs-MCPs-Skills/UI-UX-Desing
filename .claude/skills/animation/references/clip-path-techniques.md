# clip-path Techniques

`clip-path` is not just for shapes. It is one of the most powerful animation tools in CSS.

## The inset Shape

`clip-path: inset(top right bottom left)` defines a rectangular clipping region.

```css
/* Fully hidden from right */
.hidden  { clip-path: inset(0 100% 0 0); }

/* Fully visible */
.visible { clip-path: inset(0 0 0 0); }

/* Reveal from left to right */
.overlay {
  clip-path: inset(0 100% 0 0);
  transition: clip-path 200ms ease-out;
}
.button:active .overlay {
  clip-path: inset(0 0 0 0);
  transition: clip-path 2s linear;
}
```

## Tabs with Perfect Color Transitions

1. Duplicate the tab list
2. Style the copy as "active" (different background, different text color)
3. Clip the copy so only the active tab is visible
4. Animate the clip on tab change

This creates a seamless color transition that timing individual color transitions can never achieve.

## Hold-to-Delete Pattern

```css
.overlay {
  clip-path: inset(0 100% 0 0);
  transition: clip-path 200ms ease-out; /* release: fast */
}

.button:active .overlay {
  clip-path: inset(0 0 0 0);
  transition: clip-path 2s linear; /* press: slow */
}

.button:active {
  transform: scale(0.97); /* press feedback */
}
```

## Image Reveals on Scroll

```css
.image {
  clip-path: inset(0 0 100% 0); /* hidden from bottom */
  transition: clip-path 600ms cubic-bezier(0.23, 1, 0.32, 1);
}

.image.in-view {
  clip-path: inset(0 0 0 0);
}
```

Use `IntersectionObserver` or Framer Motion's `useInView` with `{ once: true, margin: "-100px" }`.

## Comparison Sliders

```css
.top-image {
  clip-path: inset(0 50% 0 0); /* adjust right value based on drag */
  transition: none; /* no transition during drag */
}
```

No extra DOM elements needed, fully hardware-accelerated.
