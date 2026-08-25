# grantcrafter_landing

Marketing site for [GrantCrafter.org](https://grantcrafter.org). The
application lives separately at `app.grantcrafter.org`, in
[rijalalaina/GrantCrafter](https://github.com/rijalalaina/GrantCrafter).

## What this is

Hand-written static HTML with one stylesheet and one script. There is no
framework, no build step and no dependencies — `public/` is the site, served
exactly as it appears here.

```
public/
  index.html          home: hero, workflow, features, pricing
  pricing/            plan comparison, including free-tier limits
  help/  faq/         support
  privacy/  terms/    legal notices
  contact/            form, posts to app.grantcrafter.org/api/contact
  styles.css          the whole design system, ~5 KB
  site.js             theme toggle, price toggle, contact form
  sitemap.xml  robots.txt
```

Keeping it dependency-free is deliberate: the site is small enough that a
build step would cost more than it saves, and a marketing page that cannot
break at deploy time is worth more than one that is elegant to author.

## Local development

No install. Serve `public/` with anything:

```bash
python3 -m http.server -d public 4321   # http://localhost:4321
```

Paths are absolute (`/faq/`, `/styles.css`), so opening the files directly
with `file://` will not resolve them — use a server.

## Deploying

Cloudflare Pages, connected to this repository. `wrangler.jsonc` declares
`public/` as the output directory, so the project needs **no build command**
— a non-empty one is what failed every build before the site was committed
here.

| Setting          | Value    |
| ---------------- | -------- |
| Build command    | *(empty)* |
| Output directory | `public` |
| Root directory   | `/`      |

## Keeping claims true

Every plan feature and limit stated on this site is enforced in the
application, not aspirational. Before changing a claim here, check what
actually gates it:

- Plan entitlements — `src/lib/auth/guards.ts` and the `checkSubscriptionTier`
  calls in the API routes.
- Plan copy and prices — `src/lib/stripe.ts`.
- Free-tier daily allowances — `src/lib/usage-limits.ts`, overridable by the
  operator at `/admin/settings`. If those are re-tuned, the numbers on
  `/pricing/`, `/faq/`, `/help/`, `/terms/` and the Free plan card change with
  them.

The legal notices name Blissfulplan Publishing Ltd. (company 15418196) as the
operator. They are the authoritative statement of who runs the service.
