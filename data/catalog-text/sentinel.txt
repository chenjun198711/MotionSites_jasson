Build a single-page dark-themed landing site for a fictional AI security product called "Sentra" using **Vite + React 18 + TypeScript + Tailwind CSS + lucide-react**. The page has 4 parts: a canvas splash screen, a fixed navbar, a full-screen video Hero, an "About" section, and a "Products" bento-grid section. Follow every detail below exactly.

## Project setup

- Vite React-TS template, Tailwind CSS v3, lucide-react for icons.
- Path alias `@` → `./src` (configure in vite.config.ts via `resolve.alias` and tsconfig paths).
- File structure:
  - `src/App.tsx`
  - `src/components/SplashScreen.tsx`
  - `src/components/Navbar.tsx`
  - `src/components/Hero.tsx`
  - `src/components/About.tsx`
  - `src/components/Products.tsx`
  - `src/hooks/useReveal.ts`

## Fonts (index.html)

- `<title>AI-Native Security Platform</title>`
- Load two fonts in `<head>`:
  1. `<link href="https://db.onlinewebfonts.com/c/8cb707a9b8a73f8a7403336b861c3074?family=BubbledotICG-FinePos" rel="stylesheet">` — a retro dot-matrix display font
  2. `<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@300;400;500;600;700&display=swap" rel="stylesheet">`

## Global CSS (index.css)

- Tailwind base/components/utilities directives.
- `html, body { overflow-x:hidden; -webkit-font-smoothing:antialiased; -moz-osx-font-smoothing:grayscale; }`
- `body { font-family:'JetBrains Mono', monospace; background:#000; }`

## Tailwind config

Extend theme with:
- `fontFamily`: `bubbledot: ['BubbledotICG-FinePos','monospace']`, `jetbrains: ['JetBrains Mono','monospace']`
- Keyframes + animations (all `0.8s cubic-bezier(0.16, 1, 0.3, 1) forwards`):
  - `fade-up`: from `opacity:0; translateY(30px)` → `opacity:1; translateY(0)`
  - `fade-in`: from `opacity:0` → `opacity:1`
  - `scale-in`: from `opacity:0; scale(0.95)` → `opacity:1; scale(1)`

## App.tsx

- State `showSplash` (initially true). Renders in order: fixed background video layer, `{showSplash && <SplashScreen onComplete={() => setShowSplash(false)} />}`, `<Navbar />`, `<Hero />`, `<About />`, `<Products />`, all inside `<div className="relative">`.
-Fixed background video behind everything, as the FIRST child of the root div: <div className="fixed inset-0 z-0 pointer-events-none"> (do NOT use a negative z-index; instead give Navbar, Hero, About, and Products relative z-10 so they stack above it, and never put a bg-black on the root div or the Hero section) containing:
  - `<video src="https://d8j0ntlcm91z4.cloudfront.net/user_38xzZboKViGWJOttwIXH07lWA1P/hf_20260723_002250_8de0b140-0dc3-4082-a021-51112f01618c.mp4" autoPlay loop muted playsInline className="w-full h-full object-cover" />`
  - overlay `<div className="absolute inset-0 bg-black/30" />`

## SplashScreen.tsx — canvas "pixel iris" reveal

Full-screen fixed overlay (`fixed inset-0 z-[9999]`, `transition-opacity duration-400`, opacity 0 when fading, `pointerEvents:'none'` while fading) containing a `<canvas>` sized to the window.
Animation logic (requestAnimationFrame):
- Black rect fills the canvas; a circular hole is punched from the center outward using `globalCompositeOperation = 'destination-out'`.
- `maxRadius = Math.sqrt(w*w + h*h) / 2`, `blockSize = 10`, `duration = 1800` ms, easing `easeOutCubic (1 - (1-t)^3)`.
- Pre-generate 360 ring blocks: each `{ angle: (2πi/360) + (Math.random()-0.5)*0.02, offsetFactor: 0.85 + Math.random()*0.3 }`.
- Each frame: clear, fill black, punch a filled circle of radius `currentRadius - blockSize*2`, then punch each of the 360 blocks as `blockSize`-square rects at `r = currentRadius * offsetFactor`, plus a second ring of 180 blocks at `r = currentRadius * (0.92 + Math.random()*0.12)` with size `blockSize*0.8` — giving a noisy pixelated edge to the expanding circle.
- On completion set `fading=true`, then call `onComplete()` after 400 ms.
- Handle window resize by re-sizing the canvas; clean up rAF and listeners on unmount.

## Navbar.tsx

Nav links array: `['About','Products','Media','FAQ','Contact']`. State: `active` (default 'About'), `mobileOpen`, `isDark`.
- Scroll listener (passive): `isDark = scrollY > window.innerHeight * 0.7` (logo/hamburger flip to black over light sections).
- When `mobileOpen`, set `document.body.style.overflow='hidden'`.
- `<nav className="fixed top-0 left-0 right-0 z-50 px-4 sm:px-6 lg:px-10 py-4">`, flex justify-between:
  1. **Logo**: inline SVG 32×32, viewBox `0 0 256 256`, single path with fill white (black when `isDark`, white when mobile menu open), `transition-colors duration-300`. Path (a 4-petal pinwheel of quarter-circles):
     `M 0 128 C 70.692 128 128 185.308 128 256 L 64 256 C 64 220.654 35.346 192 0 192 Z M 256 192 C 220.654 192 192 220.654 192 256 L 128 256 C 128 185.308 185.308 128 256 128 Z M 128 0 C 128 70.692 70.692 128 0 128 L 0 64 C 35.346 64 64 35.346 64 0 Z M 192 0 C 192 35.346 220.654 64 256 64 L 256 128 C 185.308 128 128 70.692 128 0 Z`
  2. **Center pill (desktop only, `hidden lg:flex`)**: white rounded-lg bar with shadow-sm; each link is a button `px-5 py-2.5 text-sm font-jetbrains font-medium rounded-lg`; active = `bg-black text-white`, inactive = `text-black/70 hover:text-black`.
  3. **Desktop CTA**: black rounded-lg button composed of a `w-9 h-9 m-0.5 bg-white/10 rounded-md` square holding a ChevronRight (w-4 h-4, white, `group-hover:translate-x-0.5`) + label "Request a Demo" (`px-4 py-2.5 text-sm font-jetbrains font-medium text-white`).
  4. **Mobile hamburger (`lg:hidden`)**: `w-10 h-10 rounded-lg bg-white/10 backdrop-blur-md border border-white/10`; Menu and X icons cross-fade/rotate (Menu hides via `rotate-90 opacity-0 scale-75` when open; X shown; both `transition-all duration-300 ease-out`); Menu icon color flips black when `isDark`.
- **Mobile overlay menu**: fixed inset-0 z-40, `transition-all duration-700 ease-[cubic-bezier(0.16,1,0.3,1)]`, backdrop `bg-black` at 95% opacity (click closes). Content column `pt-24 pb-10 px-6` justify-between:
  - Links as large buttons (`text-2xl font-jetbrains font-medium rounded-xl px-4 py-4`), active = `text-white bg-white/10`, else `text-white/60`; staggered entrance `translate-y-8 → 0` with `transitionDelay = 150 + i*70` ms opening (and `(n-i)*30` ms closing); each shows a ChevronRight on the right only when active.
  - Bottom: full-width white CTA button ("Request a Demo" + ChevronRight, `rounded-xl text-lg`, `active:scale-[0.98]`), delayed `150 + n*70 + 50` ms; caption below: "AI-Native Security Platform" (`text-white/30 text-xs`, centered).

## Hero.tsx

`<section className="relative w-full h-screen flex flex-col">` (transparent — the fixed App background video shows through).
- Centered top content (`pt-40 sm:pt-52`):
  - Eyebrow: "AI-Native Security Platform" — `font-jetbrains text-xs sm:text-sm tracking-[0.3em] uppercase text-white/70 mb-10 sm:mb-16`, `opacity-0 animate-fade-up` with `animationDelay:'0.3s'`.
  - H1: "See Risk. Stop Spread." — `font-bubbledot text-3xl sm:text-5xl md:text-6xl lg:text-7xl text-white leading-[0.95] tracking-tight`, fade-up delay 0.5s.
- Bottom bar (`mt-auto`, flex col→row at sm, items-end justify-between, `pb-8 sm:pb-12`):
  - Left: "Identify weak signals, map attack paths, and stop incidents before they escalate." — `font-jetbrains text-xs sm:text-sm text-white/60 max-w-sm leading-relaxed`, fade-up delay 0.7s.
  - Right CTA (fade-up delay 0.9s): black `rounded-xl` group button; inner `w-11 h-11 m-0.5 rounded-lg` square in **#F64302** (orange) with white ChevronRight (`w-5 h-5`, `group-hover:translate-x-0.5`); label "Request a Demo" `px-5 py-3.5 text-base font-jetbrains font-medium text-white`.

## useReveal.ts hook

`useReveal(threshold = 0.15)` → `{ ref, visible }`. IntersectionObserver on ref; when intersecting, set visible true and unobserve. Sections pass `0.1`.

## About.tsx

`<section ref={ref} className="relative w-full min-h-screen overflow-hidden bg-[#f0eded]">` — light section.
- Absolute-fill background video: `src="https://d8j0ntlcm91z4.cloudfront.net/user_38xzZboKViGWJOttwIXH07lWA1P/hf_20260722_225830_b3939dce-a958-415e-8f05-189499b2412e.mp4"` autoPlay loop muted playsInline, object-cover.
- Overlay column `min-h-screen px-4 sm:px-6 lg:px-10 pt-28 pb-10 sm:pb-16 justify-between`. All items start `opacity-0` and get `animate-fade-up` only when `visible`:
  - Top-left (max-w-xs sm:max-w-sm): paragraph "A unified security system that connects key signals and surfaces real threats before they escalate." (`text-xs sm:text-sm text-black/80`, delay 0.1s) + CTA (delay 0.25s): white rounded-xl shadow-sm button, inner `w-11 h-11 m-0.5` **black** rounded-lg square with white ChevronRight, label "Request a Demo" in black.
  - Bottom row (col→row at md, items-end justify-between):
    - Left (max-w-md, delay 0.4s) H2 `text-2xl sm:text-3xl lg:text-4xl font-medium text-black leading-snug`: “Meet Sentra. Built to `<span class="text-black/40">connect signals</span>` and surface real risk”
    - Right (max-w-lg, text-right, delay 0.55s) `text-xl sm:text-2xl lg:text-3xl font-medium`: “Trace risky activity and understand how `<span class="text-black/40">isolated events</span>` connect into `<span class="font-bold">real attack paths</span>`”

## Products.tsx — 3-column bento grid

`<section ref={ref} className="w-full min-h-screen md:h-screen bg-[#E8E5E9] p-2 sm:p-3">` with `grid grid-cols-1 md:grid-cols-3 gap-2 sm:gap-3` filling the height. Each column card: `rounded-2xl overflow-hidden min-h-[500px] md:min-h-0`, entrance `animate-scale-in` when visible (delays 0.1s / 0.25s / 0.4s); inner elements use `animate-fade-up` with the delays given below.

**Column 1** — background image card:
- `<img>` absolute-fill object-cover, src:
  `https://images.higgs.ai/?default=1&output=webp&url=https%3A%2F%2Fd8j0ntlcm91z4.cloudfront.net%2Fuser_38xzZboKViGWJOttwIXH07lWA1P%2Fhf_20260722_191209_40d3e692-2aac-48e5-ba92-550850a7db90.png&w=1920&q=85` (alt "Trace")
- Content column `justify-between p-5 sm:p-6`:
  - Top (delay 0.3s): "01" in `font-bubbledot text-5xl sm:text-6xl text-black/80 tracking-tight`; below it "A unified layer for connecting the signals most teams investigate separately." (`text-xs sm:text-sm text-black/70 mt-4`).
  - Bottom stack (gap-2):
    - Bar card (delay 0.45s): `bg-[#EBE8EB] rounded-xl pl-4 py-2 pr-2` flex between — text "From fragmented alerts to one system view" + `w-11 h-11` black rounded-lg square with white ChevronRight (w-4 h-4).
    - 5-col grid row (gap-2): left card `col-span-3` (delay 0.55s) `bg-[#EBE8EB] rounded-xl p-4` — "The challenge is knowing what connects, what moves, and what matters now." with "Trace" below in `font-bubbledot text-5xl sm:text-6xl text-black mt-3`; right card `col-span-2` (delay 0.65s) justify-end items-end — "W/04" in bubbledot 5xl/6xl.

**Column 2** — video card:
- `<video>` absolute-fill object-cover autoPlay loop muted playsInline, src:
  `https://d8j0ntlcm91z4.cloudfront.net/user_38xzZboKViGWJOttwIXH07lWA1P/hf_20260722_230022_88a58d3b-c44b-42c2-b2de-5a5e06086a14.mp4`
- Content `justify-between p-6 sm:p-8`:
  - Top (delay 0.45s), white/70 xs–sm text: paragraph 1 "A unified security system designed to correlate events across identities, endpoints, cloud assets, and behavior — so teams can see what matters before threats escalate."; paragraph 2 (mt-4) "Sentra connects weak signals across the environment, reconstructs attack paths, and helps teams act before risk escalates."; then "S/01" in `font-bubbledot text-5xl sm:text-6xl text-white mt-8`.
  - Bottom (delay 0.6s): `w-12 h-12 bg-[#F64302] rounded-xl` square with white ChevronRight (w-5 h-5).

**Column 3** — white container `bg-white rounded-2xl p-2 sm:p-3 flex flex-col gap-2 sm:gap-3`:
- Row 1 (flex-1, inner gap-2): video tile (delay 0.55s, uses `animate-fade-in` not fade-up) `rounded-xl overflow-hidden flex-1 min-h-0` with absolute-fill video src:
  `https://d8j0ntlcm91z4.cloudfront.net/user_38xzZboKViGWJOttwIXH07lWA1P/hf_20260723_003358_7f4eb81b-8e3e-47cb-9542-e1172cb3a8d3.mp4`
  then bar card (delay 0.65s) identical to Column 1's bar: "From fragmented alerts to one system view" + black square ChevronRight.
- Row 2 (flex-1, delay 0.75s): `bg-[#EBE8EB] rounded-xl p-5 sm:p-6 flex flex-col justify-end` — "A/01" in bubbledot black 5xl/6xl, then "Act where risk is real" in `font-jetbrains text-xl sm:text-2xl text-black leading-snug mt-2`.

## Key palette reference

- Page black: `#000`; hero overlay `bg-black/30`
- Accent orange: `#F64302`
- About bg: `#f0eded`; Products bg: `#E8E5E9`; inner cards: `#EBE8EB`; white cards `#fff`
- All easing: `cubic-bezier(0.16, 1, 0.3, 1)` ("ease-out-expo" feel)