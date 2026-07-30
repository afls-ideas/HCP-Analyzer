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

## Navigation: one function, both environments

The same `handleOpen` handler works on the iPad **and** online — no environment detection needed:

```js
handleOpen(event) {
    const id = event.currentTarget.dataset.id;
    if (!id) return;

    const deeplink = `lsc://deeplink/lightning/r/Account/${id}/view`;
    this.lastDeeplink = deeplink;

    try {
        window.location.href = deeplink;
    } catch (e) {
        this.navigateToRecord(id);
    }
}
```

It always attempts the native `lsc://` deeplink first. On the **iPad** (AFLS mobile app), the web view recognizes the `lsc://` scheme and navigates natively — the assignment succeeds. **Online** (desktop browser), `lsc://` isn't a registered scheme, so Lightning Web Security's `SecureLocation.href` throws synchronously (`"supports http:, https:, mailto: schemes and relative urls"`). The `catch` picks that up and falls back to standard Lightning navigation for the same record via `NavigationMixin` (`navigateToRecord`, which calls `NavigationMixin.Navigate` to the record's `standard__recordPage`).

The thrown error itself is the signal that the scheme is unsupported — so a single function covers both the native mobile deeplink and the traditional online navigation path.

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
