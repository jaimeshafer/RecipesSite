# Family-tree architecture

The tree is a static Hugo feature. It has no paid chart library, API, database, or external data source.

## Data model

`data/familytree.yaml` is the source of truth.

- `people` holds one record per person, identified by a stable `id`. `image` is optional.
- `relationships` holds parent-to-child connections using those IDs.
- `generation` determines the display row. Use sequential integer values; it is a layout hint, not a statement about exact age.

A person can occur in multiple relationships. For example, Brittney occurs once in `people`, once as a child of Shelly and Mark, and once as a parent with Jaime. This is what connects both sides of the family without duplicating her record.

## Adding a person

1. Add their `id`, `name`, and `generation` under `people`.
2. Add their ID to the appropriate `children` list, or create a new relationship.
3. If they have children, create a relationship whose `parents` list includes their ID.
4. If this is a new generation, include that number in `generations`.

## Photos

Place new photos in `static/img/family-tree/`. They are published at `/img/family-tree/`.
For a person with ID `lucy-shafer`, a file named `static/img/family-tree/lucy-shafer.jpg` is referenced in the data as:

```yaml
image: /img/family-tree/lucy-shafer.jpg
```

Square images work best; the tree crops photos to a circular portrait. People without an `image` field continue to display as name-only cards.

## Rendering

`layouts/shortcodes/family-tree.html` transforms this data at render time for the locally vendored Family Chart library. Family Chart automatically lays out branches, connectors, panning, zooming, and person focus.

The page itself is `content/family-tree/_index.md` and includes the shortcode with `{{< family-tree >}}`.

## Vendored library

The tree uses these pinned, locally served files:

- `static/vendor/family-chart/family-chart.min.js` and `family-chart.css` — Family Chart 0.9.0 (ISC license).
- `static/vendor/d3/d3.min.js` — D3 7.9.0 (ISC license).

Their license files are kept alongside the bundles. `package.json` and `pnpm-lock.yaml` track the exact source dependencies for security tooling. Hugo copies these files directly, so deployment remains `hugo`; pnpm is only needed when intentionally upgrading the bundled libraries.
