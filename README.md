# RecipesSite

Recipes from the family, built with [Hugo](https://gohugo.io/).

## Local development

Run the Hugo development server:

```sh
hugo server
```

Build the production site into `public/`:

```sh
hugo
```

## Deployment

This site deploys from GitHub to a Cloudflare Worker using Static Assets. The
deployment configuration is in [`wrangler.jsonc`](wrangler.jsonc); it serves
Hugo's generated `public/` directory.

In Cloudflare Workers Builds, configure the connected GitHub repository with:

| Setting | Value |
| --- | --- |
| Root directory | `/` |
| Production branch | `main` |
| Build command | `hugo` |
| Production deploy command | `npx wrangler deploy` |
| Non-production deploy command | `npx wrangler versions upload` |

Pushes to `main` build and deploy the production site. Pushes to other branches
build a preview version instead.

### Custom domain

To serve the site at `recipes.shafehaven.com`, open the `recipes-site` Worker
in Cloudflare and go to **Settings → Domains & Routes → Add → Custom Domain**.
Enter `recipes.shafehaven.com`. Because `shafehaven.com` is managed in
Cloudflare, Cloudflare creates the DNS record and HTTPS certificate
automatically.

Use only one primary hostname for this site. Adding another custom domain to the
same Worker makes that hostname serve the same recipe site.

### Previous Vercel deployment

There is no Vercel configuration in this repository. To stop Vercel from
deploying this GitHub repository, open the old Vercel project and choose
**Settings → Git → Disconnect**. Keep the project until the Cloudflare domain
is working, then delete it from Vercel when you no longer need the fallback.

## Family tree

The family tree’s source of truth is [data/familytree.yaml](data/familytree.yaml).

- Add each person once in `people`, using a stable lowercase ID.
- Add parent/child connections under `relationships` using those IDs.
- Include `gender: M` or `gender: F`; the Family Chart layout requires it.
- `image` is optional. Place portraits under `static/img/family-tree/` and reference them as `/img/family-tree/person-id.jpg`.

The page renderer is [layouts/shortcodes/family-tree.html](layouts/shortcodes/family-tree.html). It adapts the YAML into the library’s relationship format at build time, so family data remains easy to maintain without directly editing JavaScript.

### Library maintenance

The interactive renderer is locally vendored and pinned:

- Family Chart 0.9.0: `static/vendor/family-chart/`
- D3 7.9.0: `static/vendor/d3/`

Their license notices are stored next to the files. `package.json` and `pnpm-lock.yaml` record the precise source dependencies for Dependabot and audit tooling; they are not required by deployment.

To upgrade intentionally, use pnpm to update the exact version, replace the matching `dist` assets in `static/vendor/`, retain the license files, then run `hugo` and manually test the tree. Do not load these assets from a CDN: the deployed site must remain self-contained.

See [FAMILY_TREE.md](FAMILY_TREE.md) for the full data-model reference.
