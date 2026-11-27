# Plex Metadata Provider OpenAPI specification

`openapi.yaml` describes the API implemented by a Plex-compatible custom
metadata provider, derived from the [Plex Media Server documentation](https://developer.plex.tv/pms/#section/API-Info/Metadata-Providers).

## Important: feature paths are discovered at runtime

The provider's root endpoint (`GET /`) returns a `MediaProvider` object whose
`Feature` array contains a `key` for the `metadata` and `match` features. Those
keys define the real endpoint paths. This specification uses the common paths
from the Plex documentation (`/library/metadata` and
`/library/metadata/matches`) so the operations can be expressed as static
OpenAPI paths; substitute the paths advertised by a specific provider when
making requests.

## Covered operations

| Method | Documented path | Purpose |
| --- | --- | --- |
| `GET` | `/` | Provider identifier, supported types, and feature paths |
| `GET` | `/library/metadata/{ratingKey}` | Metadata for one movie, show, season, or episode |
| `GET` | `/library/metadata/{ratingKey}/children` | Paged child items (seasons or episodes) |
| `GET` | `/library/metadata/{ratingKey}/grandchildren` | Paged grandchild items (episodes) |
| `GET` | `/library/metadata/{ratingKey}/images` | All image assets for an item |
| `POST` | `/library/metadata/matches` | Catalog matches from contextual hints |

The schemas cover the provider root response, metadata objects (including
type-specific attributes), nested `Children`, image arrays, GUID schemes,
external IDs, people, ratings, and episode orderings, plus the documented
`X-Plex-` headers, paging headers, and response-customization parameters.

## Conventions

- JSON is the documented format for these providers; request JSON with
  `Accept: application/json`. XML is the PMS default generally, but is not
  described by this specification.
- `X-Plex-` headers may also be sent as query string parameters; both forms
  are defined in the spec.
- `/children` and `/grandchildren` must support `X-Plex-Container-Start` and
  `X-Plex-Container-Size`; an unpaged request returns at most 20 items.
- Match results may be reduced by `includeFields`/`excludeFields` and related
  parameters, so they use a relaxed schema (`MatchResultItem`) rather than the
  full metadata schema.
- Custom provider identifiers must match
  `tv.plex.agents.custom.[a-zA-Z0-9.]+` and are also used as the GUID scheme.
- The Plex docs are inconsistent about the episode-ordering parameter name:
  the `SeasonType` section calls it `episodeOrdering`, while the query-parameter
  tables call it `episodeOrder`. The spec follows the tables.

## Validation

The document is OpenAPI 3.1.0. It was checked against the official
[OAS 3.1 meta-schema](https://spec.openapis.org/oas/3.1/schema/2022-10-07),
and all embedded JSON examples were checked against their response and request
schemas. Common tooling can be used to lint or render it, for example:

```sh
npx @redocly/cli lint openapi.yaml
```

## License

Apache License 2.0. See `LICENSE` and `NOTICE`. The Plex API description this
spec is derived from declares the same license; use of Plex's services remains
governed by Plex's Terms of Service.
