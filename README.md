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

Cloudflare Pages project `grantcrafter-landing`, connected to this
repository.

**The dashboard's build settings are authoritative.** `wrangler.toml` declares
`pages_build_output_dir = "public"`, but a git build ignored it and published
an empty deployment — succeeding while serving 404 for every page. Until the
dashboard matches, git builds will keep doing that.

Set these under Settings → Builds & deployments:

| Setting          | Value     |
| ---------------- | --------- |
| Build command    | *(empty)* |
| Output directory | `public`  |
| Root directory   | `/`       |

Until then, deploy by direct upload, which does not use the build settings at
all:

```bash
npx wrangler pages deploy public --project-name grantcrafter-landing --branch main
```

Verify a deployment before trusting it. An empty publish returns 200 on `/`
because Pages serves its own fallback, so check a real page:

```bash
curl -sL -o /dev/null -w '%{http_code}\n' https://grantcrafter.org/pricing/
```

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
