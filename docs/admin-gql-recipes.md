# admin_gql recipes

Common Admin GraphQL queries for verifying live store state. Use these instead of guessing or relying on stale exports.

## Setup

`~/.local/bin/admin_gql` provides direct Admin GraphQL access to `arcticfresh.myshopify.com`. Token in `~/.config/arcticfresh/admin.token` (chmod 600), sourced from the Northbound app's offline session. If `admin_gql` returns 401, ask the user to refresh the token.

API version defaults to `2026-04`. Override with a second arg: `admin_gql 'query { ... }' 2025-10`.

**Don't mutate data** — the token has write scopes, but theme work should never write to the live store.

## Recipes

### Smoke test (auth working)

```bash
admin_gql '{ shop { name } }' | jq .
```

### Check if a collection exists and has an admin-uploaded image

Useful before relying on `collection.image` filters in Liquid (see `docs/horizon-notes.md` → "`collection.image` vs `collection.featured_image`").

```bash
admin_gql 'query { collectionByHandle(handle: "fruits-vegetables") { handle title image { url } productsCount { count } } }' | jq .
```

`image: null` means no admin-uploaded image (only product fallbacks).

### Read product metafields

For NNC eligibility, shipping class, etc. Theme code reads NNC product data from `custom.shipping_class` and `custom.nnc_item_code` (Northbound is the source of truth).

```bash
admin_gql 'query { productByHandle(handle: "some-handle") { handle metafields(first: 20) { nodes { namespace key value } } } }' | jq .
```

### Search the catalog by title / tag / vendor

```bash
admin_gql 'query { products(first: 10, query: "vendor:Kirkland") { nodes { handle title vendor } } }' | jq .
```

### Confirmed live collection handles

Verified 2026-04-29:

- `meat`
- `fruits-vegetables`
- `dairy-eggs`
- `pantry`
- `frozen-food`
- `drinks`
- `home-lifestyle`
- `health-beauty-pharmacy`

**Baby & Kids has no umbrella collection** — needs creating in admin if it should appear on the homepage department strip.

For background on the legacy data layout, see `references/northbound/docs/resources/current-site-setup.md`.
