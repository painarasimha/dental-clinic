# THE ONE-SHOT PROMPT — Build a premium dental-clinic website from scratch

> HOW TO USE THIS FILE
> 1. Copy this entire document into any capable coding agent (Claude Code, Cursor, Windsurf, etc.).
> 2. Fill in the PLACEHOLDERS block at the very top (the only thing you must change is the business
>    name; everything else has a sensible default).
> 3. Run the agent. It produces a complete, animated, multi-page static website that is visually and
>    behaviourally identical to the reference described here, with original AI-generated imagery,
>    working conversion form, legal pages, and two color themes.
>
> This prompt is intentionally exhaustive. It contains the full design system, the complete code for
> every shared component (navbar, footer, buttons, eyebrow pill, cards, sliders, scroll-reveal engine,
> hero lead-capture form), the exact copy for every section of every page, the full text of all legal
> pages, all image-generation prompts, the color-variant recipe, the SEO/meta, the accessibility and
> performance rules, the QA/acceptance checklist, and the deployment steps. Nothing is left to guess.

---

## 0. PLACEHOLDERS (edit these once, then never touch the rest)

```
[BUSINESS_NAME]   = "Lumora Dental"            # full brand, used in titles, footer, body copy
[SHORT_NAME]      = "Lumora"                   # one-word wordmark, used in the logo and "At Lumora, we…"
[TAGLINE]         = "Modern, Gentle Dentistry" # appended to the homepage <title> after a pipe
[EMAIL]           = "hello@lumoradental.com"
[PHONE_DISPLAY]   = "+91 93007512816"          # shown to users
[PHONE_TEL]       = "+9193007512816"           # used in tel: and wa.me links (no spaces / symbols)
[WHATSAPP_NUMBER] = "9193007512816"            # used in https://wa.me/<number>
[BOOKING_URL]     = "https://calendly.com/your-handle"   # every "Book/Get Appointment" CTA
[ACCENT]          = "#24a3b1"                   # primary accent (teal). Blue variant uses #2f80ff
[INK_DARK]        = "#011f23"                   # deep brand dark (text + dark sections). Blue: #06182e
[CREDIT]          = "Crafted by RapidXAI"       # footer maker credit
[CITY]            = "your city"                 # optional, used in a few sentences
```

When the clinic refers to itself in copy, write `[SHORT_NAME]` (e.g. "At [SHORT_NAME], we combine…").
Use `[BUSINESS_NAME]` in titles, the footer, and legal pages.

---

## 1. MISSION & DESIGN PRINCIPLES

Build a website that reads as a **real, original, premium dental practice** — never as a template.
The emotional target is: calm, clean, trustworthy, modern, warm. Think Apple-grade restraint meets a
boutique health brand.

Hard principles (do not violate):
1. **Self-contained.** Every asset (CSS, JS, fonts optional, images, icons) is local under `assets/`.
   Zero third-party image/CDN calls at runtime (Google Fonts via `<link>` is the only allowed remote).
2. **No build step.** Plain multi-page HTML + one CSS file + vanilla JS + GSAP. It must run from
   `python3 -m http.server` and deploy to GitHub Pages / Netlify with no compilation.
3. **Nothing is ever invisible.** Animations reveal content, but a safety net guarantees every element
   ends visible. A conversion form is NEVER hidden behind a scroll trigger.
4. **No blur in reveals.** Fade + slide only. (Blur filters leave images permanently blurry if a
   ScrollTrigger misfires.)
5. **No em dashes anywhere.** Not in copy, titles, meta, comments. Use commas, periods, or a pipe `|`.
6. **Accessibility.** Alt text on every image, semantic headings (one H1 per page), visible focus
   states, sufficient color contrast, `aria-label` on icon-only links.
7. **Conversion-first.** The hero carries a lead-capture form (name + phone, "callback in 10 minutes")
   in addition to the primary booking CTA. Every page funnels to booking.
8. **Premium feel.** Generous whitespace, soft shadows, rounded cards (18-20px), smooth easing
   (`power2.out`, ~0.9s), restrained motion.

---

## 2. OUTPUT: FILE TREE

Produce exactly this structure:

```
/
├── index.html              # Home
├── about.html              # About Us
├── service.html            # Services
├── blog.html               # Blog
├── privacy.html            # Privacy Policy
├── terms.html              # Terms & Conditions
├── cookies.html            # Cookie Policy
├── licenses.html           # Licenses
├── 404.html                # Not found
├── .nojekyll               # (empty file, so GitHub Pages serves all folders/assets verbatim)
├── README.md
├── assets/
│   ├── css/
│   │   └── styles.css       # the entire design system + components
│   ├── js/
│   │   ├── gsap.min.js
│   │   ├── ScrollTrigger.min.js
│   │   ├── SplitText.min.js   # optional; a plain fade-up works without it
│   │   └── jquery-3.5.1.min.js # only if you keep a Webflow-style slider; otherwise omit
│   └── img/
│       ├── logo.svg          # wordmark, dark text (for light backgrounds)
│       ├── logo-light.svg    # wordmark, white text (for nav over hero + footer)
│       ├── favicon.svg
│       ├── webclip.png       # 180x180 apple-touch-icon
│       └── gen_*.jpg         # all photography (see section 5)
└── variant-blue/             # OPTIONAL second theme (full copy, recolored — see section 12)
```

---

## 3. TECH STACK & GLOBAL SETUP

- **Fonts:** Sora (Google Fonts), weights 300/400/500/600/700.
  `<link href="https://fonts.googleapis.com/css2?family=Sora:wght@300;400;500;600;700&display=swap" rel="stylesheet">`
- **Animation:** GSAP 3.x + ScrollTrigger (+ SplitText optional). Load locally from `assets/js/`.
- **Every page `<head>` contains:**
```html
<meta charset="utf-8"/>
<meta content="width=device-width, initial-scale=1" name="viewport"/>
<title>[PAGE TITLE] | [BUSINESS_NAME]</title>
<meta name="description" content="[PAGE DESCRIPTION]"/>
<meta property="og:title" content="[PAGE TITLE] | [BUSINESS_NAME]"/>
<meta property="og:description" content="[PAGE DESCRIPTION]"/>
<meta property="og:type" content="website"/>
<meta property="og:image" content="assets/img/gen_hero.jpg"/>
<meta name="twitter:card" content="summary_large_image"/>
<link href="https://fonts.googleapis.com" rel="preconnect"/>
<link href="https://fonts.gstatic.com" rel="preconnect" crossorigin/>
<link href="https://fonts.googleapis.com/css2?family=Sora:wght@300;400;500;600;700&display=swap" rel="stylesheet"/>
<link href="assets/css/styles.css" rel="stylesheet"/>
<link href="assets/img/favicon.svg" rel="shortcut icon" type="image/svg+xml"/>
<link href="assets/img/webclip.png" rel="apple-touch-icon"/>
```
- **Every page, just before `</body>`:** load GSAP, ScrollTrigger, then the inline reveal engine and
  the lead-form handler (both given in full in section 6).
- **De-brand invariant:** a final `grep` over all HTML+CSS must return zero for any website-builder
  branding strings, generator meta tags, or builder-specific data attributes.

---

## 4. DESIGN SYSTEM (exact)

### 4.1 Color tokens (`:root` in styles.css)
```css
:root{
  --font: 'Sora','Helvetica Neue',Arial,sans-serif;

  /* PRIMARY RAMP — teal default. For the blue variant, swap to the blue ramp in section 12. */
  --primary-100:#eaf2ff;   /* very light tint (light section backgrounds) */
  --primary-200:#d6e6ff;
  --primary-300:#a9c9ff;
  --primary-400:#587d81;   /* muted */
  --primary-500:#24a3b1;   /* ACCENT (links, highlights, focus, button hover) */
  --primary-600:#1c91a1;
  --primary-700:#022f34;   /* dark */
  --primary-800:#002124;
  --primary-900:#011f23;   /* deepest — primary text + dark sections */

  --ink:#011f23;           /* body text on light */
  --muted:#5d6c7b;         /* secondary text */
  --bg:#fafafa;            /* page background */
  --bg-tint:#e2f1f3;       /* light teal section background */
  --line:#e4e8ea;          /* hairlines / input borders */
  --white:#ffffff;

  --radius-card:18px;
  --radius-input:12px;
  --radius-pill:999px;
  --shadow-soft:0 18px 50px rgba(2,47,52,.10);
  --shadow-card:0 24px 60px rgba(2,47,52,.12);
  --shadow-float:0 28px 70px rgba(2,47,52,.30);
  --maxw:1240px;
  --section-pad:clamp(64px,9vw,120px);
}
```

### 4.2 Type scale
- H1 (hero): `font-size:clamp(48px,7vw,96px); font-weight:600; line-height:1.02; letter-spacing:-2px;`
- H2 (section): `clamp(32px,5vw,64px); font-weight:600; line-height:1.08; letter-spacing:-1px;`
- H3 / card title: `clamp(20px,2.4vw,26px); font-weight:600;`
- Body: `16-18px; line-height:1.7; color:var(--muted);` (lead paragraphs use `--ink` lighter weight)
- Eyebrow / pill: `14px; font-weight:500; letter-spacing:.2px;`
- A highlighted word inside a heading uses `color:var(--primary-500)` (the accent), e.g.
  "Our **Experts** in Oral Health" with "Experts" accented.

### 4.3 Spacing & layout
- Page max width `var(--maxw)`, centered, with `padding-inline:clamp(20px,5vw,40px)`.
- Vertical section rhythm `var(--section-pad)` top and bottom.
- Cards: `border-radius:var(--radius-card); box-shadow:var(--shadow-card); background:var(--white);`
- Grid gaps 20-28px. Use CSS grid for card rows (`repeat(auto-fit,minmax(280px,1fr))` where flexible).

### 4.4 Buttons
Two variants, both pill-shaped with a circular arrow disc on the right:
- **Primary (dark):** `background:var(--primary-900); color:#fff;` arrow disc `background:var(--primary-500)`.
- **Light/ghost:** `background:#fff; color:var(--ink); border:1px solid var(--line);` arrow disc dark.
- Padding `14px 16px 14px 26px`, `font-weight:500`, `border-radius:var(--radius-pill)`.
- Hover: subtle lift `translateY(-1px)` and the disc rotates the arrow slightly or fills with accent.
- Arrow icon is the diagonal "↗" (a 12x12 SVG line path), `stroke:currentColor; stroke-width:1.5`.

Button HTML pattern:
```html
<a href="[BOOKING_URL]" target="_blank" class="btn btn-primary">
  <span class="btn-text">Book Appointment</span>
  <span class="btn-arrow" aria-hidden="true">
    <svg width="14" height="14" viewBox="0 0 12 12" fill="none">
      <path d="M4.70139 0.75L10.5303 0.750201L10.5303 6.55165M0.530334 10.75L10.2896 0.990932"
            stroke="currentColor" stroke-width="1.5"/>
    </svg>
  </span>
</a>
```

---

## 5. IMAGERY — generate everything (do not ship stock)

Generate every photo with an image model so the site is original and not a copy. Recommended for low
cost: Magnific `gpt-2`, quality `low`, resolution `1k`. Keep each slot's aspect ratio. Save each as a
JPG named `gen_<slot>.jpg` under `assets/img/` and reference it locally. If a slot uses responsive
`srcset`, point every candidate at the same JPG (identical file at all widths is valid).

A reusable photographic style suffix to append to each prompt:
> "photorealistic, premium healthcare advertising photography, bright airy modern dental clinic, soft
> natural daylight, subtle teal-and-white palette, shallow depth of field, high detail, no text."

Slots and prompts:
- `gen_hero` (16:9) — "A friendly female dentist in a mask and black gloves gently examining a relaxed,
  smiling female patient reclined in a dental chair."
- `gen_about_hero` (16:9) — "A friendly, diverse dental team of dentists and hygienists standing
  together in a modern clinic reception, welcoming."
- `gen_value` (4:3) — "A dentist showing a female patient her dental scan on a screen, explaining
  treatment, warm and reassuring."
- Team portraits (2:3, distinct people), `gen_team_1..6`:
  1. "warm female dentist early 30s, dark hair in a bun, white coat, dental clinic background"
  2. "male dentist early 40s with glasses and short beard, white coat, confident"
  3. "young female dental hygienist mid 20s, blonde, teal scrubs, cheerful"
  4. "distinguished male dentist 50s with grey hair, white coat, kind expression"
  5. "male dentist early 30s, short dark hair, teal scrubs, friendly smile"
  6. "female pediatric dentist 40s with curly hair, white coat, warm welcoming smile"
  (Prefix each with "professional studio portrait, clean soft background, friendly confident smile.")
- Testimonial avatars (1:1), `gen_testi_1..3`: "happy middle-aged man, casual"; "happy young woman,
  casual, bright smile"; "happy older woman 60s, warm genuine smile" (headshots, different people).
- Service thumbnails (4:3), `gen_service_1..4`: preventive cleaning/checkup; extreme closeup bright
  white smile (cosmetic); dentist holding a crown + implant model (restorative); smiling teen holding
  clear aligners (orthodontics).
- Story slider cards (3:4), `gen_story_1..5`: relaxed patient in chair (comfort); team greeting a
  family (community); bright welcoming reception (accessible); dentist giving attentive urgent care
  (emergency); person with a healthy bright smile holding a toothbrush (healthy habits).
- About gallery (4:3), `gen_gallery_1..5`: dentist consulting at a screen; happy team together; modern
  clinic interior with equipment; dentist gently treating a patient; candid team laughing.
- Blog thumbnails (16:9), `gen_blog_1..6`: toothbrush + floss + healthy-teeth flatlay; bright veneers
  smile; sugary foods vs a tooth model; person brushing correctly at a mirror; closeup of flossing;
  dentist explaining to an attentive patient.

### 5.1 Logo & favicon (code these as SVG, no model needed)
- Wordmark: the text "[SHORT_NAME]" in Sora 600, letter-spacing -0.3, next to a small tooth/drop mark
  filled with a gradient from `[ACCENT]` to `[INK_DARK]`, with a tiny white highlight circle.
- Provide `logo.svg` (dark text, for light pages) and `logo-light.svg` (white text, for the nav over
  the hero and the dark footer).
- Favicon: a rounded square (`rx=16` on a 64-viewport) filled with an `[ACCENT]`→dark gradient, a white
  tooth mark centered, and a small accent dot. Rasterize a 180px PNG `webclip.png` for apple-touch-icon.

Reference SVG for the favicon mark:
```html
<svg xmlns="http://www.w3.org/2000/svg" width="64" height="64" viewBox="0 0 64 64" fill="none">
  <defs><linearGradient id="fv" x1="0" y1="0" x2="64" y2="64" gradientUnits="userSpaceOnUse">
    <stop offset="0" stop-color="#24a3b1"/><stop offset="1" stop-color="#011f23"/></linearGradient></defs>
  <rect width="64" height="64" rx="16" fill="url(#fv)"/>
  <path d="M32 12c8.2 0 14.2 5.7 14.2 13.8 0 6.2-2 10-3.9 16.9-1.5 5.3-2.6 11-5.9 11-2.7 0-2.9-4.7-4.4-4.7s-1.7 4.7-4.4 4.7c-3.3 0-4.4-5.7-5.9-11C19.8 35.8 17.8 32 17.8 25.8 17.8 17.7 23.8 12 32 12Z" fill="#fff"/>
  <circle cx="40" cy="24" r="3.4" fill="#24a3b1"/>
</svg>
```

---

## 6. GLOBAL BUILDING BLOCKS (full code)

### 6.1 Navbar (transparent, absolute over the hero, white links)
The nav sits over the dark hero image and scrolls away with it (`position:absolute`). Use the WHITE
logo here so it is readable. Links are white (`#f3f6ff`). Right side is the primary "Get Appointment"
button to `[BOOKING_URL]`. Include a "Pages" dropdown listing the legal pages.

```html
<header class="nav">
  <div class="nav-inner">
    <a href="index.html" class="nav-logo" aria-label="[BUSINESS_NAME] home">
      <img src="assets/img/logo-light.svg" alt="[BUSINESS_NAME] logo" height="30"/>
    </a>
    <nav class="nav-links" aria-label="Primary">
      <a href="index.html">Home</a>
      <a href="about.html">About Us</a>
      <a href="service.html">Services</a>
      <a href="blog.html">Blog</a>
      <div class="nav-dropdown">
        <button class="nav-dropdown-toggle" aria-expanded="false">Pages</button>
        <div class="nav-dropdown-list">
          <a href="about.html">About</a><a href="service.html">Services</a>
          <a href="blog.html">Blog</a><a href="privacy.html">Privacy</a>
          <a href="terms.html">Terms</a><a href="cookies.html">Cookies</a>
          <a href="licenses.html">Licenses</a>
        </div>
      </div>
    </nav>
    <a href="[BOOKING_URL]" target="_blank" class="btn btn-light nav-cta">
      <span class="btn-text">Get Appointment</span><span class="btn-arrow">↗</span>
    </a>
  </div>
</header>
```

### 6.2 Eyebrow pill (used above every section heading)
A small rounded pill: an inline sparkle icon + a short label. The default sparkle is a 4-point sparkle
(distinct, premium). Icon uses `fill="currentColor"`.
```html
<span class="eyebrow"><svg width="12" height="12" viewBox="0 0 12 12" aria-hidden="true">
  <path d="M6 0C6.4 3.2 8.8 5.6 12 6C8.8 6.4 6.4 8.8 6 12C5.6 8.8 3.2 6.4 0 6C3.2 5.6 5.6 3.2 6 0Z"
        fill="currentColor"/></svg> Our Story</span>
```
```css
.eyebrow{display:inline-flex;align-items:center;gap:8px;padding:8px 16px;border-radius:var(--radius-pill);
  background:rgba(2,47,52,.06);color:var(--primary-700);font-size:14px;font-weight:500;}
.eyebrow svg{color:var(--primary-500);}
```

### 6.3 The scroll-reveal engine (CRITICAL — paste verbatim before `</body>` on every page)
Mark every content block you want to animate with `data-reveal`. The engine fades+slides them up as
they enter the viewport, refreshes triggers after images load, and has a 2.6s safety net that forces
anything still hidden to show. NO blur.
```html
<script src="assets/js/gsap.min.js"></script>
<script src="assets/js/ScrollTrigger.min.js"></script>
<script>
(function () {
  function run() {
    if (!window.gsap) return;
    var hasST = !!window.ScrollTrigger;
    if (hasST) gsap.registerPlugin(ScrollTrigger);
    var els = gsap.utils.toArray('[data-reveal]').filter(function (el) {
      return !el.closest('.nav') && !el.closest('.nav-dropdown-list');
    });
    els.forEach(function (el) {
      gsap.fromTo(el, { opacity: 0, y: 40 }, {
        opacity: 1, y: 0, duration: 0.9, ease: 'power2.out',
        scrollTrigger: hasST ? { trigger: el, start: 'top 95%', once: true } : undefined
      });
    });
    if (hasST) { window.addEventListener('load', function(){ ScrollTrigger.refresh(); }); ScrollTrigger.refresh(); }
    setTimeout(function () { els.forEach(function (el) {
      if (parseFloat(getComputedStyle(el).opacity) === 0) gsap.set(el, { opacity: 1, y: 0 });
    }); }, 2600);
  }
  document.readyState !== 'loading' ? setTimeout(run, 150)
    : document.addEventListener('DOMContentLoaded', function(){ setTimeout(run, 150); });
})();
</script>
```

### 6.4 Animated counters (stat numbers)
Any element `.stat-number` with text like "25+", "10k+", "92%" counts up from 0 when scrolled into
view, preserving its suffix.
```html
<div class="stat-number">25+</div>
```
```html
<script>
(function(){
  function counters(){
    if(!window.gsap||!window.ScrollTrigger) return;
    gsap.utils.toArray('.stat-number').forEach(function(el){
      var raw=el.textContent.trim(); var target=parseFloat(raw.replace(/[^\d.]/g,''))||0;
      var suffix=raw.replace(/[\d.,\s]/g,'');
      gsap.fromTo(el,{innerText:0},{innerText:target,duration:2.4,ease:'power2.out',snap:{innerText:1},
        scrollTrigger:{trigger:el,start:'top 88%',once:true},
        onUpdate:function(){el.textContent=Math.ceil(this.targets()[0].innerText).toLocaleString()+suffix;}});
    });
  }
  document.addEventListener('DOMContentLoaded',function(){setTimeout(counters,200);});
})();
</script>
```

### 6.5 Sliders (story + testimonials)
Two horizontal sliders, each with: a track of slides, dot navigation, and VISIBLE circular prev/next
arrows (←/→), plus pointer-drag. Auto-advance optional. Minimal vanilla implementation:
```html
<div class="slider" data-slider>
  <button class="slider-arrow slider-prev" aria-label="Previous">‹</button>
  <div class="slider-viewport"><div class="slider-track">
    <div class="slide">…card 1…</div><div class="slide">…card 2…</div><!-- … -->
  </div></div>
  <button class="slider-arrow slider-next" aria-label="Next">›</button>
  <div class="slider-dots"></div>
</div>
```
```html
<script>
(function(){
  function initSlider(root){
    var track=root.querySelector('.slider-track');
    var slides=[].slice.call(track.children);
    var dotsWrap=root.querySelector('.slider-dots');
    var i=0, n=slides.length;
    function go(k){ i=(k+n)%n; track.style.transform='translateX(-'+(i*100)+'%)';
      [].forEach.call(dotsWrap.children,function(d,j){d.className=j===i?'dot active':'dot';}); }
    slides.forEach(function(_,j){ var b=document.createElement('button'); b.className='dot';
      b.setAttribute('aria-label','Go to slide '+(j+1)); b.onclick=function(){go(j);}; dotsWrap.appendChild(b); });
    root.querySelector('.slider-prev').onclick=function(){go(i-1);};
    root.querySelector('.slider-next').onclick=function(){go(i+1);};
    // drag
    var sx=0,drag=false;
    root.addEventListener('pointerdown',function(e){drag=true;sx=e.clientX;});
    window.addEventListener('pointerup',function(e){ if(!drag)return; drag=false;
      var dx=e.clientX-sx; if(Math.abs(dx)>50) go(dx<0?i+1:i-1); });
    go(0);
  }
  document.addEventListener('DOMContentLoaded',function(){
    document.querySelectorAll('[data-slider]').forEach(initSlider);
  });
})();
</script>
```
Each slide shows a photo with a large metric overlay + a one-line caption (story slider), or a quote +
avatar + name + role (testimonial slider). Arrows are circular discs (44px), white bg, dark icon,
`box-shadow:var(--shadow-soft)`, positioned at the vertical center on each side. Dots: small pills,
active dot uses `var(--primary-500)`.

### 6.6 Hero lead-capture form (conversion centerpiece — paste into the hero)
A white card titled "Book a visit" with subtext "Get a callback within 10 minutes", a name field, a
phone field, and a "Request a callback" button. On submit it opens a prefilled WhatsApp message to the
clinic and swaps to a success state. It is ALWAYS visible (never gated by a scroll reveal).
```html
<div class="lead-form_card">
  <div class="lead-form_head">
    <div class="lead-form_title">Book a visit</div>
    <div class="lead-form_sub">Get a callback within 10 minutes</div>
  </div>
  <form class="lead-form" onsubmit="return leadSubmit(event)">
    <input class="lead-form_input" type="text" name="name" placeholder="Your name" required/>
    <input class="lead-form_input" type="tel" name="phone" placeholder="Phone number" required/>
    <button class="lead-form_btn" type="submit">Request a callback</button>
    <div class="lead-form_note">No spam. A real person calls you back, fast.</div>
  </form>
  <div class="lead-form_success" style="display:none">
    <div class="lead-form_title">Thanks! You're all set.</div>
    <div class="lead-form_sub">Our team will call you within 10 minutes.</div>
  </div>
</div>
```
```css
.lead-form_card{margin-top:24px;max-width:430px;background:#fff;border-radius:20px;padding:24px 24px 20px;box-shadow:var(--shadow-float);}
.lead-form_title{font-family:var(--font);font-size:21px;font-weight:600;color:var(--primary-900);letter-spacing:-.4px;}
.lead-form_sub{font-size:14px;color:var(--muted);margin-top:3px;}
.lead-form{display:flex;flex-direction:column;gap:11px;margin-top:16px;}
.lead-form_input{height:50px;border:1px solid var(--line);border-radius:var(--radius-input);padding:0 16px;font-family:inherit;font-size:15px;color:var(--ink);outline:none;background:#fafcfc;transition:border-color .2s,background .2s;}
.lead-form_input:focus{border-color:var(--primary-500);background:#fff;}
.lead-form_btn{height:52px;border:none;border-radius:var(--radius-pill);background:var(--primary-900);color:#fff;font:600 15px var(--font);cursor:pointer;transition:transform .15s,background .2s;}
.lead-form_btn:hover{background:var(--primary-500);transform:translateY(-1px);}
.lead-form_note{font-size:12px;color:#9aa6ad;text-align:center;margin-top:2px;}
@media(max-width:991px){.lead-form_card{max-width:100%;}}
```
```html
<script>
function leadSubmit(e){
  e.preventDefault();
  var f=e.target;
  var name=(f.querySelector('input[name="name"]').value||'').trim();
  var phone=(f.querySelector('input[name="phone"]').value||'').trim();
  if(!name||!phone) return false;
  var msg=encodeURIComponent("Hi [BUSINESS_NAME], I'm "+name+". Please call me back at "+phone+" for a dental appointment.");
  try{ window.open("https://wa.me/[WHATSAPP_NUMBER]?text="+msg,"_blank"); }catch(_){}
  var card=f.closest('.lead-form_card');
  if(card){ f.style.display='none';
    var h=card.querySelector('.lead-form_head'); if(h) h.style.display='none';
    var ok=card.querySelector('.lead-form_success'); if(ok) ok.style.display='block'; }
  return false;
}
</script>
```
(Optional upgrade: also POST the lead to a backend/Supabase table or a form service. The WhatsApp
hand-off works with zero backend and reaches the clinic instantly, which fits "callback in 10 minutes".)

### 6.7 Footer (dark, with accent top border)
```html
<footer class="footer">
  <div class="footer-inner">
    <div class="footer-top">
      <a href="tel:[PHONE_TEL]" class="footer-phone">[PHONE_DISPLAY]</a>
      <a href="mailto:[EMAIL]" class="footer-email">[EMAIL]</a>
    </div>
    <div class="footer-grid">
      <div class="footer-brand">
        <img src="assets/img/logo-light.svg" alt="[BUSINESS_NAME] logo" height="30"/>
        <p>Advanced technology, a caring team, and treatments designed to keep your smile healthy for life.</p>
        <a href="[BOOKING_URL]" target="_blank" class="btn btn-light">Get Appointment <span class="btn-arrow">↗</span></a>
      </div>
      <div class="footer-col"><h4>Navigation</h4>
        <a href="index.html">Home</a><a href="about.html">About</a>
        <a href="service.html">Services</a><a href="blog.html">Blog</a></div>
      <div class="footer-col"><h4>Legal</h4>
        <a href="terms.html">Terms &amp; Conditions</a><a href="cookies.html">Cookies</a>
        <a href="licenses.html">Licenses</a><a href="404.html">404</a></div>
      <div class="footer-col"><h4>Follow us</h4>
        <a href="#" aria-label="Facebook">Facebook</a><a href="#" aria-label="X">X</a>
        <a href="#" aria-label="LinkedIn">LinkedIn</a><a href="#" aria-label="Instagram">Instagram</a></div>
    </div>
    <div class="footer-bottom">
      <span>&copy; 2026 [BUSINESS_NAME]. [CREDIT]</span>
      <span>&copy; 2026 [BUSINESS_NAME] &middot; <a href="privacy.html">Privacy Policy</a></span>
    </div>
  </div>
</footer>
```
```css
.footer{background:var(--primary-900);color:#cdd8da;border-top:3px solid var(--primary-500);}
.footer a{color:#cdd8da;text-decoration:none;} .footer a:hover{color:#fff;}
.footer-top{display:flex;flex-wrap:wrap;gap:8px 48px;font-size:clamp(28px,5vw,56px);font-weight:600;color:#fff;letter-spacing:-1px;}
.footer-grid{display:grid;grid-template-columns:1.4fr 1fr 1fr 1fr;gap:32px;margin:48px 0;}
.footer-col h4{color:#fff;font-size:16px;margin:0 0 16px;} .footer-col a{display:block;margin:10px 0;}
.footer-bottom{display:flex;justify-content:space-between;border-top:1px solid rgba(255,255,255,.12);padding-top:24px;font-size:14px;}
@media(max-width:780px){.footer-grid{grid-template-columns:1fr 1fr;} .footer-bottom{flex-direction:column;gap:8px;}}
```

---

## 7. PAGE: HOME (index.html)

Order of sections, each wrapped so `data-reveal` animates the inner blocks (NOT the hero form):

### 7.1 Hero (full-bleed photo background + dark gradient overlay)
- Background: `gen_hero.jpg`, with an overlay `linear-gradient(90deg, rgba(1,31,35,.72), rgba(1,31,35,.25))`
  so the left-aligned white text is readable.
- Navbar overlaid on top (section 6.1).
- Content (left): H1 "Trusted Dental Care for Every Generation"; paragraph "We combine modern
  technology with heartfelt service to ensure every generation."; primary button "Book Appointment"
  → `[BOOKING_URL]`.
- Lead-capture form card (section 6.6) placed top-right of the hero (or under the button on mobile).
- Keep the hero at least `min-height:92vh`.

### 7.2 Our Story slider
- Eyebrow "Our Story". H2 "Redefining Dental Care with Trust, Innovation in Dental Wellness".
- A story slider (section 6.5) of 5 photo cards, each = a metric + label + caption:
  1. `gen_story_1` — "92% / Comfort & Care / Patients say they feel less anxious during visits with us"
  2. `gen_story_2` — "3/4 / Trusted by Community / New patients come from word-of-mouth referrals"
  3. `gen_story_3` — "7 Mi / Quick & Accessible / Just 7 minutes is the average wait time before being seen"
  4. `gen_story_4` — "24/7 / Emergency Support / Sudden pain or injury? Our team is here for you 24/7"
  5. `gen_story_5` — "85% / Healthy Habits / We are proud to help our community stay ahead of dental issues"

### 7.3 Value / Why us
- Eyebrow "Why [SHORT_NAME]". H2 (two lines, accent the second), paragraph, "Learn More" button, and
  the `gen_value.jpg` photo beside the text.
- Copy: "At [SHORT_NAME], we combine expertise, compassion, and modern technology to create a dental
  experience that patients truly value."

### 7.4 Services overview
- Eyebrow "Our Services". H2 "Comprehensive Dental Care for Every Smile".
- Four cards (thumbnail + title + one-line + arrow), linking to service.html:
  - Preventive dentistry — "Routine checkups and cleanings that keep problems away."
  - Cosmetic dentistry — "Whitening, veneers, and smile makeovers."
  - Restorative treatments — "Fillings, crowns, and implants to restore your smile."
  - Orthodontics — "Modern braces and clear aligners for a straighter bite."

### 7.5 Team
- Eyebrow "Our Team". H2 "Our **Experts** in Oral Health" (accent "Experts"). Paragraph: "Each member
  of our clinical staff is not only highly qualified but deeply passionate about helping patients
  achieve healthier smiles."
- Three doctor cards (photo + name + role + small social icons). Use names whose implied gender matches
  the generated portrait, e.g. "Dr. Olivia Thompson — Pediatric Dentist" (female photo), "Dr. Rishi
  Menon — General Dentist", "Dr. Emman Collins — Implant Specialist".

### 7.6 Testimonials slider
- Eyebrow "Testimonials". H2 "Voices of **Trust and Care**".
- Testimonial slider of 3 quote cards (avatar + name + role + quote):
  - Kristin Watson, Business Owner: "I have always been nervous about visiting the dentist, but
    [SHORT_NAME] changed everything. The staff is warm, and the care is exceptional. I finally enjoy
    smiling again."
  - Marcus Lee, Teacher: "Booking was effortless and the callback came in minutes. Best dental
    experience I have had."
  - Aisha Rahman, Designer: "Gentle, modern, and genuinely caring. My whole family comes here now."

### 7.7 Closing CTA (strong photo section)
- A full-width section with a background photo (`gen_about_hero.jpg`) under a dark gradient overlay
  (`linear-gradient(115deg, rgba(1,31,35,.93), rgba(1,31,35,.72))`).
- Eyebrow "Get In Touch". H2 "Let's Talk Teeth, We're **Just a Smile Away**". Paragraph "Your health
  journey starts with one simple step, we are here to guide you." Primary button "Get Started" →
  `[BOOKING_URL]`.

### 7.8 Footer (section 6.7).

---

(Continued in Part 2 below: About, Services, Blog pages; full legal page text; 404; the blue color
variant recipe; SEO/meta; accessibility & performance; QA/acceptance checklist; and deployment.)

---

## 8. PAGE: ABOUT (about.html)

### 8.1 Hero (lighter than home; nav over a soft tinted top)
- Eyebrow "About Us". H1 "Trusted Dental Experts, Decades of Care".
- Paragraph: "At [SHORT_NAME], we combine compassion, innovation, and expertise to provide care you
  can trust. Your smile is our mission, and your comfort is our priority."
- Primary button "Book An Appointment" → [BOOKING_URL].
- Right side: two animated stat counters (section 6.4):
  - "25+" / "Years of Dental Excellence"
  - "10k+" / "Smiles Transformed"

### 8.2 Our Story
- Eyebrow "Our Story". H2 "Redefining Dental Care with Trust, Innovation in Dental Wellness, For more
  than two decades". Paragraph: "[SHORT_NAME] has been dedicated to improving lives through healthier
  smiles. From our humble beginnings to becoming a trusted name in dental care, our mission has never
  changed." "Learn More" button.
- A masonry gallery of 5 images: `gen_gallery_1..5` (varying sizes, rounded corners, `data-reveal`).

### 8.3 Success metrics
- Eyebrow "Our Success Number". H2 "Quick facts that highlight our impact".
- Cards with imagery + big numbers: "50K+ Patient Visits", "4.9 Google Rating", "98% Would Recommend",
  "15+ Specialists".

### 8.4 Awards / certifications
- A row/slider of award badges (use simple monochrome badge images or styled cards). Eyebrow
  "Recognition". H2 "Awarded for care that patients feel".

### 8.5 Team grid
- Eyebrow "Our Team". H2 "Meet the people behind your smile". A grid of all 6 doctors
  (`gen_team_1..6`) with names + roles (genders matching the portraits).

### 8.6 Closing CTA + Footer (reuse 7.7 + 6.7).

---

## 9. PAGE: SERVICES (service.html)

### 9.1 Hero
- Eyebrow "Our Services". H1 "Comprehensive Dental Care for You".
- Paragraph: "From routine checkups to advanced cosmetic and restorative work, we offer the full range
  of dental care under one calm, modern roof."
- Primary button "Book An Appointment".

### 9.2 Service cards (4, with thumbnails)
Each card: thumbnail (`gen_service_1..4`), title, 2-line description, "Learn more" arrow.
- Preventive dentistry — "Cleanings, exams, fluoride, and sealants. The simplest way to avoid bigger
  problems later."
- Cosmetic dentistry — "Teeth whitening, veneers, and bonding to give you a smile you are proud of."
- Restorative treatments — "Fillings, crowns, bridges, and implants to restore function and beauty."
- Orthodontics — "Modern braces and clear aligners tailored to your bite and lifestyle."

### 9.3 Expandable service list (accordion)
Rows (title + short description + a toggle arrow that expands details):
- "Restorative treatments — Fillings, crowns, implants, and more to restore the function and beauty of
  your smile."
- "Orthodontics — Straighten your teeth and improve your bite with modern braces or clear aligners
  designed for comfort."
- "Pediatric dentistry — Gentle, friendly care that helps children build healthy habits early."
- "Emergency care — Same-day appointments for sudden pain, injury, or urgent issues."

### 9.4 FAQ accordion
- Eyebrow "FAQ". H2 "Questions We Get Often".
- Q/A items (click to expand one at a time):
  - "How often should I visit the dentist? We recommend a routine dental checkup every six months to
    maintain optimal oral health and catch any issues early."
  - "Do you offer emergency dental services? Yes. We keep same-day slots open for urgent pain, injury,
    and broken teeth. Call us or request a callback and we will fit you in fast."
  - "Will my treatment be painful? We focus on gentle, modern, low-pain techniques and will always
    talk you through every step before we begin."
  - "Do you accept new patients? Always. Book a visit or request a callback and we will get you set up."
  - "How much does treatment cost? Costs depend on the treatment. We give clear, upfront estimates
    before any work begins, with no surprises."

### 9.5 Closing CTA + Footer.

---

## 10. PAGE: BLOG (blog.html)

### 10.1 Hero
- H1 "Insights for a Healthier Smile". Paragraph "From everyday habits to advanced treatments, our
  dental experts share insights to help you care for your smile."

### 10.2 Featured post (large card)
- Big image left (`gen_blog_4`), content right: category pill "Preventive Care", "Oral Health Tips ·
  April 30, 2026", title "The ultimate guide to brushing: are you doing it right?", excerpt "Most
  people brush too hard and too fast. Here is the gentle, two-minute technique dentists actually
  recommend, plus the tools that make it easier." "Read More" button.

### 10.3 Post grid (6 cards)
Each: image, category, title, 1-2 line excerpt, date.
- `gen_blog_1` — "Preventive Care" — "5 daily dental habits that protect your smile" — "Small routines,
  big payoff. The everyday habits that keep your teeth strong for decades."
- `gen_blog_2` — "Cosmetic" — "How veneers transform your smile" — "What veneers can and cannot do, and
  whether they are right for you."
- `gen_blog_3` — "Nutrition" — "Foods that quietly harm your teeth" — "The everyday foods and drinks
  that erode enamel, and what to reach for instead."
- `gen_blog_5` — "Preventive Care" — "The truth about flossing" — "Does flossing really matter? The
  evidence, and the easiest way to make it a habit."
- `gen_blog_6` — "Education" — "Dental myths, busted" — "No, sugar is not the only villain. A dentist
  separates fact from fiction."
- `gen_blog_4` — "Preventive Care" — "The ultimate guide to brushing" — "The gentle two-minute
  technique dentists recommend."

### 10.4 Footer.

> Blog post links may point to `#` or to a single shared `blog-post.html` template if you choose to
> build one. Keep them non-dead (no 404s).

---

## 11. LEGAL PAGES (full text) + 404

All legal pages share one lightweight layout: a slim header (logo + "Back to home" link), a centered
column (`max-width:760px`), Sora type, `--ink` text, `[ACCENT]` links, and a simple footer line
"© 2026 [BUSINESS_NAME]". Each has an H1, a "Last updated: June 2026" line, and the sections below.
Use real, readable copy (not lorem ipsum).

### 11.1 privacy.html — Privacy Policy
- Intro: "At [BUSINESS_NAME], your privacy and trust matter to us as much as your smile. This policy
  explains what information we collect when you visit our website or book an appointment, and how we
  keep it safe."
- "Information we collect": "When you book a visit or contact us, we may collect your name, phone
  number, email address, and any details you choose to share about your dental needs. We also collect
  basic, anonymous analytics about how our site is used so we can improve it."
- "How we use it": appointments + scheduling; responding to questions; reminders and, with consent,
  occasional practice updates.
- "How we protect it": "We treat your health and contact information as confidential and store it
  securely. We never sell your personal data. We share it only with the clinicians and systems directly
  involved in your care, or where required by law."
- "Your choices": access, correction, deletion via [EMAIL]; opt out of non-essential messages anytime.
- "Contact": [EMAIL] or the booking page.

### 11.2 terms.html — Terms & Conditions
- Intro: "Welcome to [BUSINESS_NAME]. By using this website and booking appointments through it, you
  agree to the following terms."
- "Use of this website": informational only, not a substitute for professional dental advice.
- "Appointments": bookings are requests confirmed by our team; please give 24 hours notice to
  reschedule or cancel; repeated no-shows may affect future availability.
- "Intellectual property": all text, images, logos, and design are the property of [BUSINESS_NAME].
- "Limitation of liability": not liable for indirect or incidental damages from use of the website;
  outcomes vary and are discussed in person.
- "Contact": [EMAIL].

### 11.3 cookies.html — Cookie Policy
- Intro: "This Cookie Policy explains how [BUSINESS_NAME] uses cookies and similar technologies when
  you visit our website."
- "What are cookies": small text files that help a website function and remember preferences; they
  cannot harm your device.
- "How we use cookies": essential (keep the site working), analytics (anonymous usage), preference
  (remember choices).
- "Managing cookies": control or delete via browser settings; blocking some may affect the site.
- "Contact": [EMAIL].

### 11.4 licenses.html — Licenses
- Intro: "This page sets out the licensing terms for the content and assets used across the
  [BUSINESS_NAME] website."
- "Site content": owned by [BUSINESS_NAME]; no reproduction without written permission.
- "Imagery": owned, generated for exclusive use, or licensed for use within this website only.
- "Third-party software": animations by GSAP (standard license); the Sora typeface under the SIL Open
  Font License.
- "Permitted use": you may view and link to the site; copying layouts, code, or assets requires consent.
- "Contact": [EMAIL].

### 11.5 404.html
Standalone, no nav. Centered: a large "404", "We couldn't find that page.", and a dark pill button
"Back to home" → index.html. Same font + favicon as the rest.
```html
<!DOCTYPE html><html lang="en"><head><meta charset="utf-8"/>
<title>Page not found | [BUSINESS_NAME]</title>
<meta content="width=device-width, initial-scale=1" name="viewport"/>
<link href="https://fonts.googleapis.com/css2?family=Sora:wght@300;400;500;600;700&display=swap" rel="stylesheet"/>
<link href="assets/img/favicon.svg" rel="shortcut icon" type="image/svg+xml"/>
<style>body{margin:0;height:100vh;display:flex;flex-direction:column;align-items:center;justify-content:center;
background:#fafafa;color:#011f23;font-family:Sora,Arial,sans-serif;text-align:center;padding:24px}
h1{font-size:clamp(64px,12vw,140px);margin:0;letter-spacing:-3px}p{color:#5d6c7b;font-size:18px;margin:8px 0 28px}
a{background:#011f23;color:#fff;text-decoration:none;padding:14px 26px;border-radius:999px;font-weight:500}</style>
</head><body><h1>404</h1><p>We couldn't find that page.</p><a href="index.html">Back to home</a></body></html>
```

---

## 12. COLOR VARIANT (second theme) — brighter blue

Produce a second, visually distinct theme WITHOUT changing layout, copy, or structure. Duplicate the
whole site into `variant-blue/` and apply these changes only:

1. Override the primary ramp (append at the END of the variant's styles.css so it wins):
```css
:root{
 --primary-100:#eaf2ff; --primary-200:#d6e6ff; --primary-300:#a9c9ff;
 --primary-400:#6aa8ff; --primary-500:#2f80ff; --primary-600:#1f6fe5;
 --primary-700:#0b2a55; --primary-800:#07203f; --primary-900:#06182e;
 --ink:#06182e; --bg-tint:#e8f1ff;
}
```
2. Sweep any hardcoded teal hexes in the variant's CSS/HTML/SVGs to blue:
   `#24a3b1 -> #2f80ff`, `#011f23 -> #06182e`, `#022f34 -> #0b2a55`, `#002124 -> #07203f`,
   `#1c91a1 -> #1f6fe5`, `#587d81 -> #5b76a8`, `#3fc7d6 -> #6aa8ff`, light tint `#e2f1f3 -> #e8f1ff`,
   `#ddebec -> #e4edff`. Recolor the logo + favicon SVGs the same way. The CTA overlay becomes
   `rgba(6,24,46,...)`.
3. Optionally change the eyebrow sparkle glyph to a different mark so the two themes feel distinct.

Result: identical structure and motion, brighter blue identity. Because most colors come from
`var(--primary-*)`, the buttons, links, focus states, accents, hero/CTA overlays, and the lead form all
recolor automatically.

---

## 13. SEO / META (per page)

- index.html: title "[BUSINESS_NAME] | [TAGLINE]"; description "[BUSINESS_NAME] is a modern dental
  clinic offering gentle, expert care, from preventive checkups and cosmetic dentistry to restorative
  treatments and orthodontics. Book your visit today."
- about.html: title "About | [BUSINESS_NAME]"; description about the practice and team.
- service.html: title "Our Services | [BUSINESS_NAME]"; description of the service range.
- blog.html: title "Dental Health Blog | [BUSINESS_NAME]"; description of the blog.
- Legal pages: descriptive titles + one-line descriptions.
- Set `og:title`, `og:description`, `og:image` (the hero), `twitter:card=summary_large_image`.
- Use a real, neutral OG image (the hero photo), never a builder placeholder.

---

## 14. ACCESSIBILITY & PERFORMANCE

- One `<h1>` per page; logical heading order.
- Every `<img>` has meaningful `alt`. Icon-only links have `aria-label`.
- Color contrast: white text only over the dark overlays/sections; dark text on light.
- Visible keyboard focus (`:focus-visible` outline using `--primary-500`).
- Respect reduced motion: wrap reveal/counter setup so that if
  `window.matchMedia('(prefers-reduced-motion: reduce)').matches`, you set elements visible
  immediately and skip the animations.
- Lazy-load below-the-fold images (`loading="lazy"`); the hero image loads eager.
- Keep total JS tiny (GSAP + ~3 small inline scripts). No heavy frameworks.
- Use `font-display: swap` (the Google Fonts URL already includes `&display=swap`).

---

## 15. QA / ACCEPTANCE CHECKLIST (run before declaring done)

Drive the running site in a real browser (Playwright) and verify ALL of:
1. Every page loads with ZERO console errors and ZERO external image/CDN requests (only Google Fonts).
2. ALL text is visible: hero H1, every section H2 and paragraph. Nothing stuck at opacity 0.
3. NO image is blurry or broken (check `naturalWidth>0` for every `<img>`; computed `filter` has no
   non-zero blur).
4. Scroll reveals fire as sections enter view; stat counters animate to their targets (e.g. 25+, 10k+).
5. Both sliders: visible arrows + dots + pointer-drag all change the active slide.
6. The hero lead form: filling name + phone and submitting hides the form, shows the success state,
   and opens a prefilled `wa.me/[WHATSAPP_NUMBER]` message.
7. Every nav and footer link resolves, including all 5 legal/404 pages. No dead links, no malformed
   anchors.
8. Every "Book/Get/Request" CTA points to [BOOKING_URL] (booking) or the WhatsApp/callback flow.
9. The phone shows [PHONE_DISPLAY] everywhere and `tel:` links use [PHONE_TEL].
10. The nav logo is the WHITE variant and is readable over the dark hero.
11. `grep` the source: zero website-builder branding, zero generator meta, zero em dashes.
12. Run the blue variant through 1-11 as well; confirm the theme is blue and the layout is unchanged.

---

## 16. DEPLOYMENT (GitHub Pages, free)

1. `git init`, commit all files, create a public GitHub repo, push to `main`.
2. Ensure a top-level empty `.nojekyll` file exists (so Pages serves the `assets/` and `variant-blue/`
   folders verbatim).
3. Enable Pages from branch `main`, path `/` (root). The teal site serves at the repo root; the blue
   variant at `/variant-blue/`.
4. Wait for the build to finish, then verify the LIVE url in a browser (not just curl): hero renders,
   images load, form works, no console errors.
5. (Alt host) For Netlify: drag-drop the folder or `netlify deploy --prod`, publish dir = project root.

---

## 17. BUILD ORDER (recommended)

1. Scaffold the file tree + `styles.css` design tokens + base layout.
2. Build shared components (nav, footer, buttons, eyebrow, cards, slider, reveal engine, lead form).
3. Generate all imagery; wire the logo/favicon.
4. Build index.html section by section, then about, services, blog.
5. Build the 5 legal/404 pages.
6. Run the full QA checklist; fix everything it surfaces.
7. Duplicate to `variant-blue/` and apply the recolor; re-run QA on it.
8. Deploy to GitHub Pages; verify live.

Deliver the full tree, the README, and the run/deploy instructions. The end result must feel calm,
premium, trustworthy, and convert visitors into booked appointments.

---

## 18. COMPLETE COMPONENT CSS (drop into styles.css after the tokens)

This is the full stylesheet for the shared components and layout. Combined with section 4 tokens, it is
enough to render the entire site. Adjust class names if you prefer, but keep the behaviour.

```css
/* ---------- base / reset ---------- */
*,*::before,*::after{box-sizing:border-box;}
html{scroll-behavior:smooth;}
body{margin:0;font-family:var(--font);color:var(--ink);background:var(--bg);-webkit-font-smoothing:antialiased;line-height:1.7;}
img{max-width:100%;display:block;}
a{color:inherit;}
h1,h2,h3,h4{font-weight:600;letter-spacing:-1px;line-height:1.08;margin:0;color:var(--ink);}
h1{font-size:clamp(48px,7vw,96px);letter-spacing:-2px;line-height:1.02;}
h2{font-size:clamp(32px,5vw,64px);}
p{margin:0;color:var(--muted);}
:focus-visible{outline:2px solid var(--primary-500);outline-offset:2px;border-radius:4px;}
.container{max-width:var(--maxw);margin:0 auto;padding-inline:clamp(20px,5vw,40px);}
.section{padding-block:var(--section-pad);}
.section.tint{background:var(--bg-tint);}
.text-accent{color:var(--primary-500);}
.lead{font-size:clamp(16px,1.4vw,18px);color:var(--muted);max-width:60ch;}

/* ---------- buttons ---------- */
.btn{display:inline-flex;align-items:center;gap:14px;padding:13px 16px 13px 26px;border-radius:var(--radius-pill);
  font:500 15px var(--font);text-decoration:none;cursor:pointer;border:none;transition:transform .15s ease,background .2s ease,color .2s ease;}
.btn-arrow{display:inline-flex;align-items:center;justify-content:center;width:34px;height:34px;border-radius:999px;font-size:13px;transition:transform .2s ease;}
.btn:hover{transform:translateY(-1px);}
.btn:hover .btn-arrow{transform:rotate(45deg);}
.btn-primary{background:var(--primary-900);color:#fff;}
.btn-primary .btn-arrow{background:var(--primary-500);color:#fff;}
.btn-light{background:#fff;color:var(--ink);border:1px solid var(--line);}
.btn-light .btn-arrow{background:var(--primary-900);color:#fff;}

/* ---------- navbar ---------- */
.nav{position:absolute;top:0;left:0;right:0;z-index:50;}
.nav-inner{max-width:var(--maxw);margin:0 auto;padding:22px clamp(20px,5vw,40px);display:flex;align-items:center;justify-content:space-between;gap:24px;}
.nav-links{display:flex;align-items:center;gap:28px;}
.nav-links a,.nav-dropdown-toggle{color:#f3f6ff;text-decoration:none;font-size:15px;font-weight:400;background:none;border:none;font-family:inherit;cursor:pointer;}
.nav-links a:hover{opacity:.8;}
.nav-dropdown{position:relative;}
.nav-dropdown-list{position:absolute;top:130%;left:0;background:#fff;border-radius:14px;box-shadow:var(--shadow-card);padding:10px;display:none;min-width:180px;}
.nav-dropdown:hover .nav-dropdown-list{display:block;}
.nav-dropdown-list a{display:block;color:var(--ink);padding:9px 12px;border-radius:8px;}
.nav-dropdown-list a:hover{background:var(--bg-tint);}
@media(max-width:880px){.nav-links{display:none;}}

/* ---------- hero ---------- */
.hero{position:relative;min-height:92vh;display:flex;align-items:center;color:#fff;overflow:hidden;}
.hero::before{content:"";position:absolute;inset:0;background:linear-gradient(90deg,rgba(1,31,35,.74),rgba(1,31,35,.22));z-index:1;}
.hero-bg{position:absolute;inset:0;width:100%;height:100%;object-fit:cover;z-index:0;}
.hero .container{position:relative;z-index:2;display:grid;grid-template-columns:1.3fr .9fr;gap:40px;align-items:center;width:100%;}
.hero h1{color:#fff;}
.hero .lead{color:rgba(255,255,255,.86);margin:18px 0 28px;}
@media(max-width:991px){.hero .container{grid-template-columns:1fr;} .hero{min-height:auto;padding-block:140px 80px;}}

/* ---------- eyebrow pill ---------- */
.eyebrow{display:inline-flex;align-items:center;gap:8px;padding:8px 16px;border-radius:var(--radius-pill);
  background:rgba(2,47,52,.06);color:var(--primary-700);font-size:14px;font-weight:500;margin-bottom:18px;}
.eyebrow svg{color:var(--primary-500);}
.on-dark .eyebrow{background:rgba(255,255,255,.12);color:#fff;}

/* ---------- generic card grid ---------- */
.card-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(260px,1fr));gap:24px;margin-top:40px;}
.card{background:#fff;border-radius:var(--radius-card);box-shadow:var(--shadow-card);overflow:hidden;}
.card img{aspect-ratio:4/3;object-fit:cover;width:100%;}
.card-body{padding:22px;}
.card h3{font-size:21px;margin-bottom:8px;}
.card p{font-size:15px;}

/* ---------- team ---------- */
.team-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(240px,1fr));gap:24px;margin-top:40px;}
.team-card{position:relative;border-radius:var(--radius-card);overflow:hidden;}
.team-card img{aspect-ratio:3/4;object-fit:cover;width:100%;}
.team-card .name-plate{position:absolute;left:16px;right:16px;bottom:16px;background:#fff;border-radius:14px;padding:14px 16px;display:flex;align-items:center;justify-content:space-between;}
.team-card .name-plate b{font-size:17px;} .team-card .name-plate span{display:block;color:var(--muted);font-size:13px;}

/* ---------- slider ---------- */
.slider{position:relative;margin-top:40px;}
.slider-viewport{overflow:hidden;border-radius:var(--radius-card);}
.slider-track{display:flex;transition:transform .55s cubic-bezier(.22,.61,.36,1);}
.slide{min-width:100%;}
.story-slide{position:relative;aspect-ratio:16/10;border-radius:var(--radius-card);overflow:hidden;}
.story-slide img{position:absolute;inset:0;width:100%;height:100%;object-fit:cover;}
.story-slide .overlay{position:absolute;inset:0;background:linear-gradient(180deg,transparent 30%,rgba(1,31,35,.85));}
.story-slide .meta{position:absolute;left:24px;right:24px;bottom:24px;color:#fff;}
.story-slide .metric{font-size:40px;font-weight:600;}
.slider-arrow{position:absolute;top:50%;transform:translateY(-50%);width:44px;height:44px;border-radius:999px;border:none;background:#fff;color:var(--ink);font-size:20px;cursor:pointer;box-shadow:var(--shadow-soft);z-index:3;}
.slider-prev{left:-10px;} .slider-next{right:-10px;}
.slider-dots{display:flex;justify-content:center;gap:8px;margin-top:20px;}
.slider-dots .dot{width:8px;height:8px;border-radius:999px;border:none;background:#cfd8da;cursor:pointer;transition:width .2s,background .2s;}
.slider-dots .dot.active{width:26px;background:var(--primary-500);}

/* ---------- testimonial ---------- */
.testi-card{background:#fff;border-radius:var(--radius-card);box-shadow:var(--shadow-card);padding:40px;text-align:center;max-width:680px;margin:0 auto;}
.testi-card img{width:84px;height:84px;border-radius:999px;object-fit:cover;margin:0 auto 16px;}
.testi-card .quote{font-size:clamp(18px,2vw,24px);color:var(--ink);font-weight:500;line-height:1.5;}
.testi-card .who{margin-top:16px;font-weight:600;} .testi-card .role{color:var(--muted);font-size:14px;}

/* ---------- accordion (services + FAQ) ---------- */
.acc-item{border-bottom:1px solid var(--line);}
.acc-head{display:flex;align-items:center;justify-content:space-between;gap:16px;padding:22px 0;cursor:pointer;font-size:clamp(18px,2vw,22px);font-weight:600;}
.acc-head .plus{flex:none;width:28px;height:28px;border-radius:999px;border:1px solid var(--line);display:grid;place-items:center;transition:transform .2s;}
.acc-item.open .plus{transform:rotate(45deg);background:var(--primary-900);color:#fff;border-color:var(--primary-900);}
.acc-body{max-height:0;overflow:hidden;transition:max-height .3s ease;color:var(--muted);}
.acc-item.open .acc-body{max-height:240px;padding-bottom:22px;}

/* ---------- closing CTA (photo) ---------- */
.cta{position:relative;color:#fff;text-align:center;background-size:cover;background-position:center;
  background-image:linear-gradient(115deg,rgba(1,31,35,.93),rgba(1,31,35,.72)),url('../img/gen_about_hero.jpg');}
.cta .container{padding-block:clamp(80px,12vw,140px);}
.cta h2{color:#fff;max-width:18ch;margin:14px auto;}
.cta .lead{color:rgba(255,255,255,.86);margin:0 auto 28px;text-align:center;}

/* ---------- stat counters ---------- */
.stats{display:flex;gap:48px;flex-wrap:wrap;}
.stat-number{font-size:clamp(40px,6vw,72px);font-weight:600;color:var(--primary-500);letter-spacing:-2px;}
.stat-label{color:var(--muted);font-size:15px;}

/* ---------- masonry gallery ---------- */
.gallery{columns:3;column-gap:18px;margin-top:40px;}
.gallery img{width:100%;border-radius:var(--radius-card);margin-bottom:18px;break-inside:avoid;}
@media(max-width:780px){.gallery{columns:2;} .footer-top{font-size:34px;}}
@media(max-width:520px){.gallery{columns:1;}}

/* ---------- reduced motion ---------- */
@media(prefers-reduced-motion:reduce){
  [data-reveal]{opacity:1!important;transform:none!important;}
  *{scroll-behavior:auto!important;}
}
```

---

## 19. RESPONSIVE RULES (mobile-first checks)

- Navbar: collapse links into a hamburger under 880px; the menu opens as a full-width sheet with the
  same links + the booking CTA.
- Hero: switch the two-column grid to a single column under 991px; the lead form drops below the
  headline + button. Reduce `min-height` so content is not cramped.
- Card grids: `auto-fit minmax(260px,1fr)` naturally reflows to 2 then 1 column.
- Footer grid: 4 columns to 2 (under 780px) to 1; the big phone/email scales down.
- Story/testimonial sliders: full-width slides; arrows move slightly inward; dots remain centered.
- Type: every heading uses `clamp()` so it scales without media queries.
- Tap targets: minimum 44px height for buttons, inputs, slider arrows, dots.

---

## 20. CONTENT BANK (extra copy you can mix in)

Use these to enrich pages or rotate copy. All on-brand, all em-dash-free.

Hero headline alternatives:
- "Trusted Dental Care for Every Generation"
- "A Calmer Way to Care for Your Smile"
- "Modern Dentistry, Genuinely Gentle Hands"

Value props (use as small feature rows or chips):
- "Same-day emergency appointments"
- "Transparent, upfront pricing"
- "Gentle, low-pain techniques"
- "Family and pediatric friendly"
- "State-of-the-art equipment"
- "Callbacks within 10 minutes"

Extra FAQ items:
- "Is my first visit just a consultation? Your first visit usually includes an exam, a gentle clean,
  and a clear plan for anything that needs attention, with costs explained before we start."
- "Do you treat children? Yes, our team loves working with kids and helping them build confidence at
  the dentist early."
- "What if I am anxious about the dentist? Tell us. We go slower, explain everything, and use the
  gentlest techniques available so you stay comfortable."
- "Can I pay in installments? We offer flexible payment options for larger treatments. Ask us during
  your visit."

Extra testimonials:
- "James Park, Engineer: The team explained every option clearly and never pushed anything. Refreshing."
- "Priya Nair, Nurse: I booked at 9am and got a callback by 9:08. The appointment itself was painless."
- "Tom Becker, Chef: My kids actually look forward to their checkups now. That says everything."

Extra blog ideas (titles + excerpts):
- "When should kids first see a dentist? Earlier than most parents think. Here is the simple timeline."
- "Whitening that actually works (and what to skip). A dentist ranks the options by safety and results."
- "Sensitive teeth? Here is what is really going on. The common causes and the fixes that help fast."
- "What to do in a dental emergency before you reach us. Five calm steps for pain, knocks, and breaks."

Microcopy:
- Form success: "Thanks! You're all set. Our team will call you within 10 minutes."
- Form note: "No spam. A real person calls you back, fast."
- Newsletter (optional): "Healthy-smile tips, once a month. No spam, ever."
- 404: "We couldn't find that page."

---

## 21. NOTES FOR THE GENERATING AGENT

- Prefer CSS variables for all colors so the second theme is a token swap, not a rewrite.
- Keep one stylesheet; do not split into many files.
- Do not introduce any framework, bundler, or CSS preprocessor.
- Generate images at low cost first; you can upscale the hero later if needed.
- After building, run the section 15 QA checklist in a real browser and fix everything it surfaces
  before declaring done. Loading a page is not a test; interacting with it is.
- Keep the codebase free of any website-builder fingerprints and em dashes.

End of one-shot prompt.
