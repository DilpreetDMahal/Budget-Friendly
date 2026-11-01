# Learning Notes — Budget Tracker

## 1. Page layout: grid vs. flex

**What I had:** the body was a 2-column grid with rows sized as `0.5fr 3fr 0.5fr`, and `.main` was another 2-column grid.

**Problems:**
- `fr` rows split the *window height* in proportion, so the header and footer grew or shrank with the window instead of fitting their content.
- Both grids had 2 columns, but every area spanned both of them, so the columns did nothing.
- `.main`'s area map had 3 rows but only 2 row sizes, so the browser made up the missing row.

**Decision:** the body became a **flex column** (header → main → footer).

**Why:** the page is just three things stacked on top of each other. Nothing sits side by side, so a grid wasn't needed.

**Rule of thumb:** use grid where things really sit in **columns**, and let everything else stack normally.

## 2. Pushing the footer to the bottom

**Decision:** `flex-grow: 1` on `<main>`, and `min-height: 100vh` on the body.

**Why:**
- `min-height: 100vh` makes the body at least as tall as the window.
- `flex-grow: 1` tells *one* child to take up all the leftover height.
- It goes on `main`, not on the header, footer or body, because main is the part that should stretch.

**Testing trick:** give the footer a temporary border or background. If it sits at the very bottom of the window, it works. (Remove the test styles afterwards.)

## 3. Three equal summary cards

**What I had:** a flex row with `margin: auto` and `justify-content: center`, so the cards were only as wide as their text.

**Decision:** `.card-container` became a grid with `grid-template-columns: 1fr 1fr 1fr`.

**Why:** three equal columns is exactly what grid does well. `1fr` = an equal share of the space.

**Learned:**
- Once `1fr` columns fill the whole width, `justify-content` / `align-content: center` do nothing, because there's no free space left to center things in.
- `grid-area` only works when the **parent** is a grid. Leftover `grid-area` lines from an old layout do nothing.
- Named grid areas are optional while items stay in HTML order. They pay off when you want to rearrange things, e.g. on mobile.

## 4. Lining up content edges (the wrapper)

**Problem:** the header used 30px side padding and the cards used 16px plus auto margins, so "Budget Tracker" and the Balance card started at different x positions.

**First version:** a formula, `padding: 0 max(var(--gutter), calc((100% - var(--page-width)) / 2))`, on both the header and main. It worked, but it's hard to read.

**Final decision:** one reusable `.wrapper` class.

```css
.wrapper {
  width: 100%;                    /* stops it shrinking as a flex item */
  max-width: var(--page-width);   /* content stops growing at 1040px */
  margin: 0 auto;                 /* centers it */
  padding: 0 var(--gutter);       /* 24px from the screen edges on phones */
}
```

**Why:**
- It's simpler to read than the formula, and I can reuse it anywhere (footer next).
- Both the header content and main use it, so their left edges match.

**Learned:**
- The header *background* should still run edge to edge. So the `<header>` keeps the background, and a **new inner `<div class="wrapper">`** holds the title and nav. Trade-off: one extra div.
- **Auto side margins on a flex item stop it stretching**, so it shrinks to fit its content. That's why `width: 100%` is needed.
- Width and gutter live in `:root` variables (`--page-width`, `--gutter`), so I can change them in one place.

## 5. HTML structure

- **Semantic tags:** I switched from `<div class="main">` / `<div class="footer">` to `<main>` / `<footer>`. They describe the page better for screen readers and for reading the code.
- **Class names should describe what something is, not where it sits.** I renamed `grid-container` to `container` once the body stopped being a grid. (Still to fix: `left` / `right` on the nav links.)
- **Only one `<h1>` per page.** A dollar amount isn't a heading. (Still to fix: `#total-balance` is an `h1`.)

## 6. General habits

- **Use the color variables** (`var(--teal)`), not hex codes written out by hand, so changing a color in `:root` updates it everywhere.
- **Remove leftover CSS** when a layout changes. Dead lines make it confusing.
- **Save the file** before checking. An unsaved change looks like it "didn't work".

## Still to do

- [ ] Give `.main` a layout with a gap to space out the cards row and the panels row.
- [ ] `.main-container`: 2-column grid, with a narrower form and a wider transaction list.
- [ ] Footer: remove test styles, use `.wrapper`, add the line and text.
- [ ] Add amount elements to the Income and Expenses cards (match Balance).
- [ ] Replace the `h1` on the balance amount.
- [ ] Rename the `left` / `right` link classes; use `aria-current` for the active page.
- [ ] Remove `margin: auto` from `.link` (it does nothing).
- [ ] Move the font `preconnect` links above the stylesheets.
- [ ] Empty state: only show it when there are no transactions.
- [ ] Make the grids drop to 1 column on narrow screens.
