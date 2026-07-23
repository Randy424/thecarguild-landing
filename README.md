# The Car Guild — landing page (Netlify deploy)

Static site, no build step. `index.html` is the only page — the live/default design. Motion design: scroll-reveal, smooth scroll, a full-page ambient particle field (very low density — a light scatter, no connecting lines), parallax-drifting decorative blobs, and flip/zoom scroll-reveal on the promise cards.

(This started as two variants compared locally — a restrained one and a bolder showcase one, `index-bold.html`. The bold direction won, after tuning: removed the connecting lines between particles and cut the particle count twice — 70/30 (desktop/mobile) → 35/14 → 16/6. `index-bold.html` was then promoted to replace `index.html` and deleted.)

## What's already done
- Form has `data-netlify="true"`, a `form-name` hidden field, and a honeypot field (`bot-field`) for spam protection — this is what makes Netlify auto-detect and capture submissions.
- JS submits via `fetch()` (AJAX) so the page keeps its inline "you're on the list" success state instead of a hard page reload.
- Every field Netlify needs a `name` attribute on (`email`, `name`, `area`, `role`) is wired.
- Mobile pass: added a sub-480px breakpoint (stacks the name/area fields, tightens padding), 44px min touch targets on the role-toggle buttons.
- Scroll-reveal via [AOS](https://michalsnik.github.io/aos/) (flip/zoom on the promise cards, staggered), smooth wheel scrolling via [Lenis](https://lenis.darkroom.engineering/), a light ambient particle scatter via [tsParticles](https://particles.js.org/) with parallax-drifting decorative blobs behind two sections — all loaded via CDN (`<script src>` tags, no build step), and all gated behind `prefers-reduced-motion` so they fully disable for users who've asked for reduced motion at the OS level.

## Local development

This is a static file — no build step — but the form's `fetch('/')` submission and Netlify's form detection only work against an actual Netlify backend, so opening the file directly (`file://...`) or a plain static server will make the form silently fail on submit. Two options:

**Quick visual check (effects, layout, mobile) — no form testing needed:**
```
cd ~/Repositories/thecarguild-landing
npx live-server
```
Opens with live reload on file save. Fine for checking particles/scroll/responsive layout, but form submission will error (no `/` endpoint to POST to).

**Full check including form submission (once the site is connected to Netlify — see below):**
```
npm install -g netlify-cli   # one-time
cd ~/Repositories/thecarguild-landing
netlify link                 # connects this folder to the deployed site
netlify dev
```
`netlify dev` proxies form submissions to the real Netlify Forms backend, so you can test an actual end-to-end signup locally before pushing.

## Deploy steps

### 1. GitHub repo
Done — pushed to [github.com/Randy424/thecarguild-landing](https://github.com/Randy424/thecarguild-landing). Future changes: commit and `git push` from this folder as normal.

### 2. Connect Netlify
1. Go to [app.netlify.com](https://app.netlify.com) → sign up / log in with GitHub.
2. **Add new site → Import an existing project → GitHub** → authorize Netlify → select `thecarguild-landing`.
3. Build settings: leave **Build command** blank and **Publish directory** as `/` (it's a static file, nothing to build).
4. Click **Deploy site**. You'll get a live URL like `thecarguild-landing.netlify.app` in under a minute.

### 3. Confirm forms are working
1. In the Netlify dashboard, go to **Forms** — after your first deploy, "waitlist" should appear as a detected form (Netlify parses the static HTML at build/deploy time, so this happens automatically because of `data-netlify="true"`).
2. Submit a test entry on the live page.
3. Refresh **Forms → waitlist** — your test submission should appear. You can also set up email notifications under **Forms → Settings and usage → Form notifications**.

### 4. (Later) Custom domain
Once `thecarguild.com` is purchased:
1. Netlify dashboard → **Domain settings → Add a custom domain** → enter `thecarguild.com`.
2. Netlify gives you either nameservers to point the registrar at, or a couple of DNS records (A/CNAME) to add at your registrar. Either way it's a 5-minute change, and Netlify auto-provisions free HTTPS once DNS propagates.

## Notes
- Free Netlify tier covers 100 form submissions/month — plenty for early validation.
- Every `git push` to `main` auto-redeploys the live site — no manual redeploy step needed.
- If you ever want a redirect page (`/thanks`) instead of the current inline JS success state, that's a config change, not a rebuild — ask and it's a small edit.
- **Known issue to watch:** during testing, Lenis's smooth-scroll inertia occasionally fought with rapid/repeated scroll input (page position jumped unpredictably). Try it yourself with fast trackpad flicks — if it feels janky, we can tune down `duration`/`wheelMultiplier` in the Lenis config or drop it.
