# Deploying the ORSIVA website to orsiva.space

This is a single self-contained file (`index.html`) — no build step, no server code. That makes hosting cheap and simple. Below is the fastest reliable route, then two alternatives, then how to point your `orsiva.space` domain at it, then how to turn on demo-request emails.

---

## Recommended: Cloudflare Pages (free, fast, free SSL)

**Why:** free tier is generous, global CDN, automatic HTTPS, and it manages your domain DNS in the same place — fewer moving parts.

1. Create a free account at https://dash.cloudflare.com (you do the sign-up and password yourself).
2. In the dashboard: **Workers & Pages → Create → Pages → Upload assets** (the "Direct Upload" option — no GitHub needed).
3. Name the project `orsiva`.
4. Drag the `website` folder (containing `index.html`) into the upload box, or drag `index.html` directly. Click **Deploy**.
5. You'll get a live URL like `orsiva.pages.dev` within ~30 seconds. Check it works.
6. To attach your domain: **Custom domains → Set up a custom domain → enter `orsiva.space`**. If you bought the domain through Cloudflare, DNS is configured automatically. If not, see "Pointing your domain" below.

To update the site later: re-upload the new `index.html` to the same project (or, if you want auto-deploys, connect a GitHub repo instead of direct upload).

---

## Alternative A: Netlify (also free, drag-and-drop)

1. Sign up at https://app.netlify.com.
2. **Add new site → Deploy manually** → drag the `website` folder onto the drop zone.
3. Live instantly on a `*.netlify.app` URL.
4. **Domain settings → Add a custom domain → `orsiva.space`** and follow the DNS instructions.

## Alternative B: GitHub Pages (free, good if your code is already on GitHub)

1. Put `index.html` in a public repo (e.g. `orsiva-site`).
2. **Settings → Pages → Source: Deploy from branch → `main` / root.**
3. Site goes live at `https://<username>.github.io/orsiva-site/`.
4. Add `orsiva.space` under **Settings → Pages → Custom domain** (GitHub Pages now provisions HTTPS for custom domains automatically).

---

## Pointing your `orsiva.space` domain at the site

You bought `orsiva.space` from a registrar (e.g. where you registered it). You'll add DNS records the host gives you. Typical pattern:

- **Apex (`orsiva.space`):** the host will give you either an `A` record (an IP) or ask you to use their nameservers. Cloudflare Pages and Netlify both prefer you point the apex via a `CNAME`-flattening / `ALIAS` record they provide.
- **`www.orsiva.space`:** add a `CNAME` pointing to the platform URL (e.g. `orsiva.pages.dev` or `your-site.netlify.app`).

Easiest path: if you move the domain's **nameservers** to Cloudflare (free), then Cloudflare Pages wires everything up for you and you skip manual records entirely. HTTPS certificates are issued automatically on all three platforms — allow up to a few minutes to an hour after DNS propagates.

> Note: I can't make DNS or registrar changes for you (they require your account login). Do those steps yourself; I can talk you through any screen.

---

## Turning on demo-request emails

Right now the demo form shows the confirmation but doesn't send anywhere. To receive submissions by email **without any backend**:

1. Go to https://formspree.io and create a free account (50 submissions/month free).
2. **New Form** → set the destination to the email you want requests sent to (e.g. `contact@orsiva.space`).
3. Formspree gives you an endpoint like `https://formspree.io/f/abcdwxyz`.
4. Open `index.html`, find this line near the bottom (in the `<script>`):

   ```js
   const DEMO_ENDPOINT = "https://formspree.io/f/YOUR_FORM_ID";
   ```

   Replace it with your real endpoint:

   ```js
   const DEMO_ENDPOINT = "https://formspree.io/f/abcdwxyz";
   ```

5. Re-deploy (re-upload the file). Done — submissions now land in your inbox with all fields (name, email, organisation, role, fleet size, orbital regime, message).

Alternatives to Formspree if you prefer: **Getform**, **Web3Forms** (no account, free), or **Netlify Forms** (if you host on Netlify — add `netlify` to the `<form>` tag and it captures automatically). I can wire any of these in for you once you pick one.

---

## A note on the "register once" behaviour

The one-time demo lock uses the visitor's **own browser memory** (localStorage). It prevents the same person re-submitting from the same browser, which is what you asked for. It does **not** stop someone using a different browser/device or clearing their data — true one-submission-per-person requires the backend (Formspree above) to de-duplicate by email. For a marketing site, the browser-level lock is the normal and sufficient approach.

---

## Quick checklist

- [ ] Host `index.html` (Cloudflare Pages recommended)
- [ ] Confirm the `*.pages.dev` / `*.netlify.app` preview works
- [ ] Point `orsiva.space` + `www` at the host
- [ ] Wait for HTTPS to go green
- [ ] Create Formspree form, paste endpoint into `DEMO_ENDPOINT`, re-deploy
- [ ] Submit a test demo request and confirm the email arrives
