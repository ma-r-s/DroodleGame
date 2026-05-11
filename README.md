# Droodles

A matching-puzzle web game built from Roger Price's _Droodles_, a 1953 collection of
absurdist visual riddles. You are shown a grid of minimalist drawings on one side and a
shuffled set of captions on the other. Match each drawing with its caption. Some are
obvious in retrospect. Most are completely impossible.

**Play:** [droodles.ma-r-s.com](https://droodles.ma-r-s.com)
**Writeup:** [ma-r-s.com/projects/droodles](https://www.ma-r-s.com/projects/droodles)

## What is a Droodle

Roger Price coined the term in 1953 for a small square drawing captioned with a deadpan
title that does not explain it. A few black lines, a punchline, and the joke happening
entirely in the gap between the two. Price published several books of them across the
1950s; he later co-created [Mad Libs](https://en.wikipedia.org/wiki/Mad_Libs).

## How the data was built

The droodles in this game come from a scan of the _Droodles Compendium_. Extracting them
was the interesting part of the project:

1. Each book page is scanned at uniform resolution.
2. An OpenCV pipeline detects the rectangular panel regions by contour size and aspect
   ratio.
3. The caption strip below each panel is OCR'd, then cleaned up (titlecase, manual fixups
   for the cases OCR mangled).
4. The output is a single `src/lib/image_mappings.json` keyed by filename, with the
   alt-text and caption per droodle.

The runtime app reads that JSON and never touches OpenCV or OCR; the data pipeline is
authoring-only. Roughly three hundred droodles are included.

## Tech stack

- [SvelteKit](https://svelte.dev/) 2 with Svelte 5 runes
- [Tailwind CSS](https://tailwindcss.com/) for styling
- [shadcn-svelte](https://shadcn-svelte.com/) button primitive
- [lucide-svelte](https://lucide.dev/) icons
- Deployed to [Vercel](https://vercel.com/) via `adapter-auto`

## Local development

Requires [bun](https://bun.sh/).

```bash
bun install
bun run dev
```

Then open [http://localhost:5173](http://localhost:5173).

## Build

```bash
bun run build       # production build
bun run preview     # preview the production build locally
```

## Project structure

```
src/
├── app.css                       # Tailwind base + theme tokens
├── app.html                      # HTML shell
├── lib/
│   ├── components/ui/button/     # Button primitive
│   ├── image_mappings.json       # Droodle metadata: filename, alt, caption
│   └── utils.js                  # cn() helper for class merging
└── routes/
    ├── +layout.svelte            # Root layout (loads global CSS)
    ├── +page.svelte              # The game
    └── www/+page.svelte          # About page
static/
└── droodles/                     # ~300 PNG panels
```

## Acknowledgments

All droodles are the work of Roger Price (1918-1990). This project is a non-commercial
tribute and is not affiliated with the rights holders of the original books. If you enjoy
the form, buy the
[Droodles Compendium](https://tallfellow.com/droodles-compendium/) from Tallfellow Press.

## License

MIT for the code in this repository. The droodles themselves are © the estate of Roger
Price. See `LICENSE` for details.
