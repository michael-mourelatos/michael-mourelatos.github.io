# michaelmourelatos99-commits.github.io

Personal site. Static HTML, no build step, served by GitHub Pages.

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole site — markup, CSS and the tab script in one file |
| `portrait.jpg` | Headshot, 720x900, already web-optimised |
| `resume.pdf` | Linked from the masthead and Contact panel |

## Editing

Open `index.html`, find the `EDIT` comments and the `.todo` blocks, replace the
content, delete the `.todo` blocks. Commit and push — Pages rebuilds in about a
minute.

Preview locally first:

```bash
python -m http.server 8000
# http://localhost:8000
```

## How the tabs work

Standard ARIA tabs. Each tab button carries `aria-controls` pointing at a
`<section class="panel">` with a matching `id`. To add a section, add a button
to the `.tablist` and a panel with the same id — the script picks it up
automatically, no other changes needed. To remove one, delete both.

Deep links work: `/#experience` opens that tab directly, and the URL updates as
you switch. Arrow keys, Home and End move between tabs. Without JavaScript the
page falls back to a plain scrolling document with every section visible.

## Adding writing later

Create a `writing/` folder with one `.html` per post, then list each post inside
the Writing panel as an `<article class="entry">`. Still no build step.

## Design notes

- Type: Fraunces (display), Newsreader (body), IBM Plex Mono (nav and labels)
- Palette: warm paper `#f2eee6`, ink `#23201c`, deep pine accent `#1f5c52`
- Layout: vertical nav set against a continuous hairline; below 820px the nav
  becomes a horizontal strip pinned to the top
