**Build a single-page React + Vite + TypeScript landing page using Tailwind CSS and lucide-react icons. No extra UI libraries.**

**Fonts (load in index.html via `<link>` tags):**
- `Suisse Intl Regular` from `https://db.onlinewebfonts.com/c/eb270b13b84d5a43462737e3fb0a268d?family=Suisse+Intl+Regular`
- `SuisseIntl-Medium` from `https://db.onlinewebfonts.com/c/c446362802681bacaacbad0f39bfc1a5?family=SuisseIntl-Medium`
- Set `<title>` to "Urbi - Geo-intelligence Platform"

**Global CSS (index.css):**
- Tailwind base/components/utilities directives
- `body` font-family: `'Suisse Intl Regular', sans-serif` with antialiased smoothing
- `.font-heading` → `'SuisseIntl-Medium', sans-serif`
- `.font-body` → `'Suisse Intl Regular', sans-serif`
- `.liquid-glass` class: `background: rgba(255,255,255,0.01)`, `background-blend-mode: luminosity`, `backdrop-filter: blur(4px)` (with -webkit prefix), `border: none`, `box-shadow: inset 0 1px 1px rgba(255,255,255,0.1)`, `position: relative`, `overflow: hidden`
- `.liquid-glass::before` pseudo-element: `content: ''`, `position: absolute`, `inset: 0`, `border-radius: inherit`, `padding: 1.4px`, background is a 180deg linear-gradient: `rgba(255,255,255,0.45)` at 0%, `rgba(255,255,255,0.15)` at 20%, `rgba(255,255,255,0)` at 40% and 60%, `rgba(255,255,255,0.15)` at 80%, `rgba(255,255,255,0.45)` at 100%. Use `-webkit-mask` with `linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0)`, `-webkit-mask-composite: xor`, `mask-composite: exclude`, `pointer-events: none`.
- `.hero-card` → `border-radius: 20px`, `overflow: hidden`
- `.hero-card video` → `position: absolute`, `inset: 0`, `width: 100%`, `height: 100%`, `object-fit: cover`
- `.stat-card` → `background: white`, `box-shadow: 0 2px 20px rgba(0,0,0,0.04)`

**vite.config.ts:** Standard Vite + React config. Set `@` path alias to `./src`. Add `optimizeDeps.exclude: ['lucide-react']`.

**Tailwind config:** Default, no custom theme extensions.

**Page structure (App.tsx):**

The page has two main sections inside a root `div.overflow-x-clip`:

**Section 1 — Scroll-driven hero (250vh tall, sticky inner):**
- Outer wrapper: `relative` div with `height: '250vh'` inline style, `ref` attached.
- Inner sticky div: `sticky top-0 h-screen w-full bg-[#F6F5FA] p-2 md:p-3 flex flex-col`

**Navbar (absolute, floating):**
- `nav` with `absolute top-4 left-4 right-4 md:top-7 md:left-7 md:right-7 z-50 bg-[#26114F] rounded-2xl px-4 md:px-8 py-3`
- Left: brand "nova" in `font-heading text-[#F5A623] text-2xl md:text-3xl font-bold tracking-tight`
- Center (desktop only, `hidden md:flex gap-10`): four links — "Features" (button with ChevronDown icon, `gap-1.5`), "Past results" (anchor), "Nova Atlas" (anchor), "End users" (anchor). All `text-white/90 text-[15px] hover:text-white transition-colors`.
- Right (desktop): "Let's talk" button — `bg-white rounded-2xl pl-6 pr-2 py-2 text-[#26114F] text-[15px] font-body hover:bg-white/90 transition-colors` with a `w-10 h-10 bg-[#26114F]/10 rounded-xl` span containing an ArrowRight icon (`w-4 h-4 text-[#26114F]`).
- Mobile: hamburger button (`md:hidden`, `w-10 h-10`, `text-white`) toggling Menu/X icons with opacity+rotation transitions (`duration-300 ease-out`). When open, Menu fades/rotates to `opacity-0 rotate-90 scale-75`; X appears at `opacity-100 rotate-0 scale-100`.
- Mobile menu dropdown: `md:hidden overflow-hidden transition-all duration-500 ease-[cubic-bezier(0.16,1,0.3,1)]` with `max-h-[400px] opacity-100` when open, `max-h-0 opacity-0` when closed. Inner content: `pt-6 pb-4 flex flex-col gap-1` with `translate-y-0` when open, `-translate-y-4` when closed. Contains the same four links (text-base, `py-3 px-2 rounded-xl hover:bg-white/5`) and a full-width "Let's talk" button below a `border-t border-white/10` divider.

**Hero area (`w-full h-full relative flex items-center justify-center`):**

Three floating stat cards (desktop only, `hidden lg:block`), each `w-[300px] h-[340px]` (bottom card is `w-[420px] h-[260px]`), absolutely positioned, with opacity and transform driven by scroll progress:
- Left card: `absolute left-0 top-1/2 -translate-y-1/2 z-10`, white/80 bg, `rounded-3xl p-8 flex flex-col justify-end shadow-sm backdrop-blur-sm`. Shows "3,2M" (`font-heading text-[#26114F] text-5xl`) and "total road length<br />surveyed" (`font-body text-[#26114F]/60 text-sm mt-3 leading-relaxed`).
- Right card: `absolute right-0 top-1/2 -translate-y-1/2 z-10`, same styling. Shows "4,1M" and "firm profiles data<br />in 19 locations".
- Bottom card: `absolute bottom-[-120px] left-1/2 -translate-x-1/2 z-10`, `bg-white/90 rounded-3xl p-8 flex flex-col justify-end overflow-hidden shadow-sm backdrop-blur-sm` with a background image via inline style: `url(https://images.higgs.ai/?default=1&output=webp&url=https%3A%2F%2Fd8j0ntlcm91z4.cloudfront.net%2Fuser_38xzZboKViGWJOttwIXH07lWA1P%2Fhf_20260723_124127_70fc3c63-59b1-4385-a518-b1b30e6ea436.png&w=1280&q=85)`, `backgroundSize: cover`, `backgroundPosition: center`. Shows "42M" and "weekly active users<br />in 172,633 places".

Card animation logic: `cardsOpacity = Math.max(0, (progress - 0.2) / 0.6)`, `cardsTranslate = Math.max(0, 1 - (progress - 0.2) / 0.6)`. Left card translates X by `-cardsTranslate * 80`, right card by `+cardsTranslate * 80`, bottom card translates Y by `+cardsTranslate * 50`. All keep `translateY(-50%)` or `translateX(-50%)` base transforms.

**Video background (`.hero-card`, `absolute z-[5] bg-[#1a0a3e]`):**
- Inline styles: `top: '0px'`, `left` and `right` set to `${videoInset}px` where `videoInset = progress * (isMobile ? 40 : 180)`, `bottom: '0px'`, `transition: 'none'`.
- Inside: a `<video>` with `className="absolute inset-0 w-full h-full object-cover"`, `autoPlay muted loop playsInline`, and `src="https://d8j0ntlcm91z4.cloudfront.net/user_38xzZboKViGWJOttwIXH07lWA1P/hf_20260723_022902_8be98604-ea2f-4664-b867-c48d1854f798.mp4"`.

**Hero content overlay (`relative z-20 flex flex-col items-center justify-center text-center px-4`):**
- Badge: `liquid-glass rounded-full px-4 md:px-5 py-2 md:py-2.5 flex items-center gap-2 md:gap-2.5 mb-6 md:mb-8` containing a User icon (`w-3.5 h-3.5 md:w-4 md:h-4 text-white`) and text "Spatial data intelligence" (`font-body text-xs md:text-sm text-white`).
- Headline: `font-heading text-white text-3xl sm:text-4xl md:text-5xl lg:text-[64px] leading-[1.1] max-w-4xl mb-8 md:mb-12` with `letterSpacing: '-0.03em'`. Text: "Advanced maps data" `<br />` "Superior navigation".
- Two buttons (centered, `flex flex-col sm:flex-row items-center gap-3 md:gap-4`):
  - "Let's talk": `bg-[#FFBA00] rounded-2xl pl-5 md:pl-6 pr-2 py-2 text-[#26114F] font-body text-sm md:text-[15px] hover:bg-[#e6a800] transition-colors` with a `w-9 h-9 md:w-10 md:h-10 bg-white rounded-xl` span containing ArrowRight (`w-4 h-4 text-[#26114F]`).
  - "Discover benefits": `liquid-glass flex items-center gap-3 rounded-2xl pl-5 md:pl-6 pr-2 py-2 text-white font-body text-sm md:text-[15px] hover:bg-white/10 transition-colors` with a `w-9 h-9 md:w-10 md:h-10 bg-white/30 rounded-xl` span containing a Play icon (`w-4 h-4 text-white fill-white`).

**Scroll logic (useEffect):**
- Listen to `scroll` event (passive). Compute `progress` (0–1) from the section's `getBoundingClientRect().top` relative to `scrollable = offsetHeight - window.innerHeight`. Set state.
- `isMobile` state via `window.innerWidth < 768` with resize listener.

**Section 2 — "Powered by" logos:**
- `section.bg-[#F6F5FA] pt-[20vh] pb-16 md:pb-24 px-4 md:px-6`
- `max-w-5xl mx-auto` container
- "Powered by" text: `text-center text-xs md:text-sm tracking-[0.25em] text-[#26114F]/60 uppercase font-body mb-10 md:mb-16`
- Two rows of logos (`flex flex-col items-center gap-8 md:gap-12`), each row `flex items-center justify-center gap-8 md:gap-16 flex-wrap`:
  - Row 1 (3 logos), Row 2 (2 logos)
  - Each logo: `<img>` with `className="h-24 sm:h-36 md:h-56 object-contain"` and `alt="Company logo"`
  - Logo URLs (in order):
    1. `https://soft-zoom-63098134.figma.site/_assets/v11/37053da3fd09feb291b60d82d23d7b0cf25e1dd4.png`
    2. `https://soft-zoom-63098134.figma.site/_assets/v11/6dc0eb28bacbaeb6fe5d2607ca4c0fe852dbb89c.png`
    3. `https://soft-zoom-63098134.figma.site/_assets/v11/2326b26c0400c807cf83705859dca000e890284a.png`
    4. `https://soft-zoom-63098134.figma.site/_assets/v11/d5d3924821336718bff7133aed81dbdb882fbfd7.png`
    5. `https://soft-zoom-63098134.figma.site/_assets/v11/028861ef7136c48418d438153398cd241a89329e.png`

**Icons used (all from lucide-react):** `ArrowRight, Play, ChevronDown, User, Menu, X`

**Color palette:** `#26114F` (deep purple/navy), `#F5A623` (amber/gold brand), `#FFBA00` (yellow button), `#F6F5FA` (light lavender background), `#1a0a3e` (video card bg).

**Dependencies:** react, react-dom, lucide-react, tailwindcss, vite, typescript.

---