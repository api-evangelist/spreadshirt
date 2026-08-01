---
name: Upload a design and publish it as an article
description: Upload a design file to SpreadConnect (SPOD), pick a product type, generate a preview, and create a sellable article.
api: openapi/spreadshirt-spreadconnect-openapi.json
operations:
  - uploadDesign
  - getProductTypes
  - getProductTypeViews
  - getProductTypePreviews
  - createArticle
  - getArticle
---

# Upload a design and publish it as an article (SpreadConnect / SPOD)

Turn a design file into a sellable print-on-demand article.

## Auth
Send `X-SPOD-ACCESS-TOKEN` on every request.

## Steps
1. **Upload the design** — `uploadDesign` (`POST /designs/upload`), PNG under 10 MB. Keep the
   returned design id.
2. **Choose a product type** — `getProductTypes` (`GET /productTypes`); inspect placement areas
   with `getProductTypeViews` (`GET /productTypes/{productTypeId}/views`) and confirm the design
   fits the printable hotspots.
3. **Generate a mockup (optional)** — `getProductTypePreviews`
   (`POST /productTypes/{productTypeId}/previews`) to render the design on the product.
4. **Create the article** — `createArticle` (`POST /articles`) binding the design to the product
   type and views, with price and metadata.
5. **Verify** — `getArticle` (`GET /articles/{articleId}`); subscribe to `Article.added` /
   `Article.updated` webhooks to stay in sync.

## Rules
- Rate limit: 60 requests/minute per integration.
- Errors return `ErrorResponse {orderId, reason}` as `application/json`.
- Validate against staging (`https://rest.spreadconnect-staging.app`) first.
