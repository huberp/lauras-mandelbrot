# Laura's Mandelbrot

A colourful Mandelbrot set renderer that runs entirely in your browser — no installation, no dependencies, just open and enjoy.
It is built with [p5.js](https://p5js.org/) and renders a 640×640 pixel image of the Mandelbrot set with a vivid, custom colour palette.

---

## Viewing the page

The project is published via **GitHub Pages** and can be opened directly at:

**<https://huberp.github.io/lauras-mandelbrot/>**

No build step is needed. The page loads the p5.js library from a CDN and renders the fractal immediately on page load.

---

## What is the Mandelbrot set?

The **Mandelbrot set** is one of the most famous objects in mathematics. It lives in the **complex plane** — a 2-D coordinate system where the horizontal axis represents the *real* part of a complex number and the vertical axis represents the *imaginary* part.

### The formula

For every point **c** in the complex plane, the following iterative recurrence is applied starting from **z₀ = 0**:

```math
z_{n+1} = z_n^2 + c
```

where both **z** and **c** are complex numbers. A complex number has the form:

```math
c = a + bi \quad (a, b \in \mathbb{R},\; i = \sqrt{-1})
```

Squaring a complex number follows the rule:

```math
z^2 = (u + vi)^2 = u^2 - v^2 + 2uvi
```

so each iteration expands to:

```math
u' = u^2 - v^2 + a
```
```math
v' = 2uv + b
```

This is exactly what the renderer computes in its inner loop.

### Escape criterion

A point **c** belongs to the Mandelbrot set if the sequence **z₀, z₁, z₂, …** never escapes to infinity. In practice, if the magnitude `|z| = √(u² + v²)` exceeds **2** (i.e. `u² + v² > 4`), the sequence will diverge. The iteration is capped at a maximum of 256 steps.

- Points that **never escape** (counted up to `max_iterations`) are drawn **black** — they are *inside* the set.
- Points that **escape quickly** are assigned a colour based on how many iterations they survived, producing the characteristic halo of colours around the set boundary.

### Mapping pixels to the complex plane

The canvas is 640×640 pixels. Each pixel `(xcoord, ycoord)` is mapped to a complex number `c = a + bi` using a simple linear transform:

| Axis | Canvas range | Complex range |
|------|-------------|---------------|
| Real (horizontal) | 0 → 639 | −2.0 → 1.0 |
| Imaginary (vertical) | 0 → 639 | −1.25 → 1.25 |

```
C_real = C_real_min + xcoord × delta_C_real       delta_C_real = (C_real_max − C_real_min) / 640
C_img  = C_img_min  + ycoord × delta_C_img        delta_C_img  = (C_img_max  − C_img_min)  / 640
```

This window is chosen to show the entire Mandelbrot set with a comfortable margin, centred slightly left of the origin because the set extends further into negative real values.

### Colouring

Points outside the set are coloured with a multi-channel formula driven by the escape iteration count `i`:

```
R = (i % 4) × 85         — cycles through 4 red levels
G = 255 − (i % 5) × 64   — cycles through 5 green levels
B = (i % 6) × 50         — cycles through 6 blue levels
```

This produces the vivid, banded colour gradients visible around the set's boundary and in the filaments of the fractal.
