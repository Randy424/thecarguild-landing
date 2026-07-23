# The Car Guild — landing page (Netlify deploy)

Single-file static site (`index.html`) wired for Netlify Forms. No build step, no dependencies.

## What's already done
- Form has `data-netlify="true"`, a `form-name` hidden field, and a honeypot field (`bot-field`) for spam protection — this is what makes Netlify auto-detect and capture submissions.
- JS submits via `fetch()` (AJAX) so the page keeps its inline "you're on the list" success state instead of a hard page reload.
- Every field Netlify needs a `name` attribute on (`email`, `name`, `area`, `role`) is wired.

## Deploy steps (from scratch)

### 1. Create the GitHub repo
```
cd ~/Repositories/thecarguild-landing
git init
git add index.html README.md
git commit -m "Add The Car Guild landing page"
```
Then on github.com: **New repository** → name it (e.g. `thecarguild-landing`) → do **not** initialize with a README (you already have one) → create.
```
git remote add origin https://github.com/<your-username>/thecarguild-landing.git
git branch -M main
git push -u origin main
```

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
