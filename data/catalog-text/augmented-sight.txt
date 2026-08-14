Build a single-page hero site called "Ion 7 Optics" using **Vite + React 18 + TypeScript + Tailwind CSS 3 + lucide-react**. The app renders exactly one full-screen `<Hero />` component (no routing, no other sections). Set the HTML `<title>` to `Ion 7 Optics`.

### Font
In `index.html`, load this stylesheet:
`https://db.onlinewebfonts.com/c/4ff6f056f9052b835e33438a16ed81d4?family=Recepts+NF+W01+Regular`
In the global CSS, define a utility class `.font-recepts` with `font-family: 'Recepts NF W01 Regular', sans-serif`. This futuristic display font is used for the brand name, headline, nav "INQUIRE" pill, social links, and overlay menu. Everything else uses the default sans stack.

### Global CSS animations
Define three keyframe entrance animations (all use `animation-fill-mode: backwards` so delays work):
- `.animate-rise` — `rise-in` 0.8s `cubic-bezier(0.22, 1, 0.36, 1)`; `from { opacity: 0; transform: translateY(24px); }`
- `.animate-fade` — `fade-in` 0.8s ease-out; `from { opacity: 0; }`
- `.animate-zoom` — `zoom-in` 1.2s `cubic-bezier(0.22, 1, 0.36, 1)`; `from { opacity: 0; transform: scale(1.05); }`

Disable all three under `@media (prefers-reduced-motion: reduce)`.

### Page shell
The `<section>` fills the viewport: background `#E6E8EC`, `sm:h-screen sm:overflow-hidden`, and on mobile it scrolls normally with `pb-8`. Primary text/ink color is `#010101`; accent dark blue is `#123655`; the card arrow buttons use `#3D719D`.

### Top bar (absolute, `top-4`, full width, z-50, `px-4`, space-between)
- **Left, mobile only (`md:hidden`)**: the word `SYNTHCORTEX` in `.font-recepts`, `text-sm`, `tracking-[0.25em]`, white on mobile, `#010101` from `sm`. Animates with `.animate-rise` at 0.1s delay.
- **Left, desktop (`md:flex`)**: a pill nav — `rounded-full`, `border border-[#123655]`, `p-1`, containing 4 links: **Main, Story, Mission, Blog**. Each link is `rounded-full px-5 py-4 text-xs font-semibold tracking-wide`; the first ("Main") is active with `bg-white shadow-sm`, others get `hover:bg-white/70` with a 200ms color transition. `.animate-rise`, 0.1s delay.
- **Right cluster**: another bordered pill (`border-[#123655] p-1`, `gap-1.5`, `.animate-rise` 0.2s delay) containing:
  1. An `INQUIRE` link (hidden below `sm`) — `.font-recepts`, `bg-white px-9 py-4 text-xs tracking-widest`, `shadow-sm`, hover inverts to `bg-[#123655] text-white`.
  2. A round account button (11×11, 12×12 at `md`) — white circle with lucide `User` icon (size 18, strokeWidth 2.25), same hover inversion.
  3. A round menu toggle button, same styling, that cross-fades between lucide `AlignRight` and `X` icons: both icons are absolutely stacked; the inactive one is `rotate-±90 scale-50 opacity-0`, transitions `all duration-300`.

### Full-screen overlay menu (z-40)
A fixed `inset-0` panel with `bg-[#123655]` that toggles from the menu button. Closed state: `opacity-0 -translate-y-6 pointer-events-none`; open: `opacity-100 translate-y-0`. Transition is 500ms with timing function `cubic-bezier(0.22,1,0.36,1)`. While open, set `document.body.style.overflow = 'hidden'`. Contents, centered vertically:
- The 4 nav links in uppercase, `.font-recepts text-3xl sm:text-4xl tracking-[0.2em] text-white py-3 hover:opacity-60`, each staggering in with `transitionDelay = 120 + i * 70` ms (sliding up from `translate-y-8 opacity-0`).
- An `INQUIRE` white pill button (`rounded-full px-10 py-4 text-xs tracking-widest`, hover `bg-[#E6E8EC]`) with delay `120 + 4 * 70` ms.
- A bottom row (`pb-12`, gap-8) of social links `IN YT D X` in `.font-recepts text-sm tracking-widest text-white`, delay `180 + 4 * 70` ms.
- Clicking any link closes the menu.

### Center video panel with a notched corner
The video container: on mobile it's `relative mx-4 mt-5 h-[80vh]`; from `sm` it's absolutely positioned `inset-x-0 inset-y-5` with `px-[24%]` (so the video column occupies the middle 52% of the screen, full height minus 20px top/bottom).

The inner wrapper is `overflow-hidden rounded-2xl` and gets a **dynamic CSS `clip-path`** computed in JS: a rounded rectangle (corner radius 16) whose **bottom-left corner is cut off by a 45° chamfer of 160px** with 16px-radius rounded corners at both ends of the diagonal. Implement it as a `path()` string built from the element's `offsetWidth`/`offsetHeight` (use `d = r * 0.7071` for the diagonal corner offsets), recomputed via a `ResizeObserver` in `useLayoutEffect`. Concretely: `M r,0 → L w−r,0 → Q w,0 w,r → L w,h−r → Q w,h w−r,h → L c+r,h → Q c,h c−d,h−d → L d,h−c+d → Q 0,h−c 0,h−c−r → L 0,r → Q 0,0 r,0 Z` with `r=16`, `c=160`.

Inside, a `<video>` with `autoPlay muted loop playsInline`, `object-cover` filling the panel, entering with `.animate-zoom` at 0.25s delay. Source:
`https://d8j0ntlcm91z4.cloudfront.net/user_38xzZboKViGWJOttwIXH07lWA1P/hf_20260728_132314_415ee576-38fb-4cd1-a465-c24ccf2c192c.mp4`
(The video shows a woman with dark bob haircut, black turtleneck, headphones, and a glowing orange neon visor on a deep blue background.)

### Headline block (overlaps the video's left edge on desktop)
Absolutely positioned relative to the video container: on mobile `bottom-56 right-5 text-right`; from `sm`, `left-8 top-[38%] -translate-y-1/2 text-left` — which places it in the light-gray gutter left of the video. All `.font-recepts`:
- Eyebrow: `SYNTHCORTEX`, `text-xs tracking-[0.3em]`, rise-in at 0.45s. White on mobile, `#010101` from `sm`.
- `<h1>`: `ION 7` then a line break then `OPTICS` (the OPTICS span gets `tracking-[0.25em]`, the h1 itself `tracking-[0.08em]`), `text-3xl sm:text-4xl lg:text-5xl leading-[1.1]`, rise-in at 0.55s.

### Tagline block (bottom-left gutter on desktop)
Positioned `bottom-5 right-5 max-w-[230px] text-right` on mobile; `sm:bottom-3 sm:left-8 sm:max-w-[260px] sm:text-left`:
- `WE BUILD THE FUTURE` — `.font-recepts text-sm tracking-[0.2em]`, rise-in 0.7s.
- Paragraph, `text-xs leading-relaxed`, rise-in 0.8s: *"A daring next chapter for human capability, our pioneering neural hardware fuses precision engineering with living tissue, giving every wearer the power to reach beyond boundaries."*

### Product cards (right gutter, vertically centered on desktop)
A flex row: on mobile centered below the video (`mt-4 gap-3 px-4`); from `sm` absolutely at `right-8 top-1/2 -translate-y-1/2` with `gap-4`. Two cards, each `w-[160px] sm:w-[165px] lg:w-[185px] rounded-3xl border border-[#D0D0D2] bg-white p-1.5 sm:p-2`, cursor-pointer, hover lifts `-translate-y-1` (300ms). Cards rise in staggered at `0.9s + i * 0.12s`. Each card:
- Square image in an `overflow-hidden rounded-2xl` wrapper, `object-cover`, scaling to 1.05 on group hover (500ms).
- Footer row (`px-1.5 py-2 sm:px-2 sm:py-3`, space-between): label in `text-[10px] sm:text-[11px] font-semibold` and a circular `bg-[#3D719D]` button (7×7, 8×8 at `sm`) holding a lucide `ArrowUpRight` (size 15, strokeWidth 2.5) that rotates 45° on group hover.

Card 1 — label **"Your Senses, Upgraded."**, image:
`https://images.higgs.ai/?default=1&output=webp&url=https%3A%2F%2Fd8j0ntlcm91z4.cloudfront.net%2Fuser_38xzZboKViGWJOttwIXH07lWA1P%2Fhf_20260727_223258_895c67a6-5c28-4dd1-adfd-3c4725f315ed.png&w=1280&q=85`

Card 2 — label **"The signature series"**, image:
`https://images.higgs.ai/?default=1&output=webp&url=https%3A%2F%2Fd8j0ntlcm91z4.cloudfront.net%2Fuser_38xzZboKViGWJOttwIXH07lWA1P%2Fhf_20260727_222546_b770d1fb-2759-4331-a940-1b587a7848f0.png&w=1280&q=85`

### Social links (desktop only, bottom-right corner)
`absolute bottom-8 right-8`, hidden below `sm`, gap-6: the four links `IN`, `YT`, `D`, `X` in `.font-recepts text-sm tracking-widest text-[#010101] hover:opacity-60`, each rising in at `1.15s + i * 0.08s`.

### Choreography summary
On load everything animates in one pass: top bar pills (0.1s / 0.2s) → video zoom (0.25s) → SYNTHCORTEX eyebrow (0.45s) → headline (0.55s) → "WE BUILD THE FUTURE" (0.7s) → paragraph (0.8s) → card 1 (0.9s) → card 2 (1.02s) → socials (1.15–1.39s).

---