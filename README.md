# HCP Analyzer

A Lightning Web Component for the **Life Sciences Cloud (LSC) mobile app** that lists Accounts and deeplinks the native app to a record via the `lsc://` URL scheme.

## What it does

- Queries Accounts with the **GraphQL wire adapter** (`lightning/uiGraphQLApi`) — a plain, unfiltered `Account` query with `orderBy` + `first`, the technique proven to work **offline** in the LSC mobile runtime.
- Renders a tappable list of accounts.
- On tap, navigates the mobile app to the record:

  ```
  lsc://deeplink/lightning/r/Account/{id}/view
  ```

  The mobile web view intercepts the `lsc://` scheme and navigates natively.

## Component

`force-app/main/default/lwc/lscMobileInline_hcpAnalyzer`

- Master label: **HCP Analyzer**
- API version: **66.0**
- Targets: `lightning__Tab`, `lightning__AppPage`, `lightning__HomePage`, `lightning__UrlAddressable`
- Config property: `mobileHeight` (Integer, default `700`) — height in pixels for mobile display.

## Notes

- The `gql` tag does **not** support `${}` interpolation — a substituted value is stringified into the query as `[object Object]` and breaks normalization. The query is kept static by design.
- Filtering by a null `recordId` (or using `getLookupRecords`, which doesn't resolve in this runtime) silently returned 0 rows — hence the plain unfiltered query.

## Deploy

```bash
sf project deploy start --source-dir force-app
```
