# misaki-studio-vercel

The boilerplate a Misaki Studio project drops into to run as a **static site on
Vercel**, on your own Vercel account: the exported pages, CMS content and
assets. No server code — it is a Next.js static export, which Vercel serves as
is.

## Run it

```bash
npm install
cp .env.example .env        # SITE_NAME, SITE_URL
npx vercel link             # once: picks or creates the Vercel project
```

In Misaki Studio, point an export target at this clone:

| Field | Path |
|---|---|
| React src path | `<clone>/src` |
| Asset path | `<clone>/public` |

Export, then:

```bash
npm run build      # next build → out/   (+ robots/sitemap/llms from the export)
npm run deploy     # vercel build, then vercel deploy --prebuilt
```

`--prebuilt` uploads the build output, not your source: your site is built on
your machine, never on Vercel's builders. For CI, set `VERCEL_TOKEN`,
`VERCEL_ORG_ID` and `VERCEL_PROJECT_ID` in the environment — never on the
command line.

## What is generated, what is shell

| Where | What | Who writes it |
|---|---|---|
| `src/`, `public/` | the site — pages, components, CMS content, media | **the exporter** |
| `scripts/generate-seo-files.mjs` | robots / sitemap / llms | **the exporter** (`scripts/seo-files.mjs` runs it when it is there, so a fresh clone builds too) |
| `vercel.json`, `next.config.js` | the deploy shape | this repo |

The 404 page is the export's own `404.html`; Vercel serves it for any path the
export does not have.

## Custom domains

Add the domain to the Vercel project (dashboard or `vercel domains add`), set
`SITE_URL` to match and rebuild, so the absolute URLs in the sitemap and
`llms.txt` point at it.

## Not here yet

**A project with a `database/` folder.** Its views and actions call the
generated backend, which this target does not host — the site deploys, and
those calls have nowhere to go. Database hosting is the next phase: a hosted
Postgres (yours or Misaki's) and a connection string, and the backend runs as
one Vercel function beside the site. The adapter for that is already built and
tested; it is not shipped here until the database side exists.

**Env safety.** `next.config.js` inlines only `SITE_NAME`, `SITE_URL`,
`BASE_PATH` and `CDN_URL` into the browser bundle (`PUBLIC_ENV`). Anything else
in `.env` stays out of the site.
