# OpenHAL 9000 Site — Journey Journal

## 2026-04-12 — OG metadata and the social card

The site had no Open Graph tags — share a link on Slack, Twitter, or iMessage and you'd get a bare URL with no preview. Fixed that today with a proper OG image and full metadata.

The OG image (1200x630) was built by composing an HTML template with the `hal-eye.svg` asset, screenshotting it at exact dimensions via Playwright. The HAL eye sits left with a red ambient glow, "OpenHAL 9000" title right, tagline below, and a "Claude Code Plugin" badge. Dark background matches the site. It reads well at thumbnail scale — the red eye pops even at small sizes.

Meta tags cover both Open Graph (`og:type`, `og:title`, `og:description`, `og:url`, `og:image` with dimensions) and Twitter Cards (`summary_large_image`). All image URLs are absolute, pointing to the GitHub Pages deployment.

Commit: fa69de9.

## 2026-04-11 — The tunnel, the door, and the mobile reckoning

Today the OpenHAL landing page got its signature moment: a scroll-driven tunnel transition that pulls you through a porthole into HAL's memory room. You scroll, the white panel zooms past like the starship hull in 2001, and the red room bleeds through a feathered radial mask — blurring into focus as you enter. When the scroll completes, "Click anywhere to access memory core" fades in. It feels like the film.

The architecture is interesting. The tunnel overlay is a fixed fullscreen scrollable container with a 200vh tall child. A CSS named scroll timeline (`--tunnel`) is defined on it, and `timeline-scope` on the parent allows sibling elements (the room-stage) to reference that same timeline. No JavaScript drives the animation — it's pure CSS scroll-driven animations with `animation-timeline: --tunnel`. The panel, room mask, blur, vignette, and corner blur all animate independently off the same scroll progress.

We hit a bizarre CSS parser issue: the main `<style>` block silently drops any rules added after the tunnel `@keyframes` declarations. 787 rules parse, then nothing. The workaround is a second `<style>` block before `</head>`. Root cause unknown — possibly `@property --hole` or `timeline-scope` triggers a parser state that invalidates subsequent rules. This burned hours of debugging before we discovered it.

The 4th key now opens the tunnel directly — no intermediate "unlocked" state or viewport click. You unscrew four T-keys and you're in.

Then came mobile. Every section needed adaptation: hero screens hidden with HAL scaling to viewport height, capabilities terminal with wrapped code blocks, briefing section with HTML fallback headlines replacing the SVG arcs, vitals labels narrowed from 200px to 100px, install instructions with compact step numbers, LMC cabinet scaling naturally via aspect ratio, CTA planets scaled up to 90vw, iOS scroll lock with position:fixed. The hero was the hardest — centering a `max-content` flex container required `left: 50%; transform: translateX(-50%)` combined with a JS-calculated scale factor.

The blade ejection overlay (HAL eye + text captions) was removed — the audio still plays but the visual takeover is gone. A rotate-to-landscape prompt now appears on mobile portrait when entering the blade view.

Desktop got attention too: the hero now scales to fit any viewport height via a JS-computed `--hero-scale` custom property, and the LMC cabinet section is full viewport height with the door filling it proportionally.

Commits: 7462176 through df00b03. Microsoft Clarity analytics added. The site is responsive across all sections for the first time.
