# CoPilot announcements

The feed behind **What's new** in [SOCFortress CoPilot](https://github.com/socfortress/CoPilot).
Every CoPilot deployment reads `feed.json` from this repository's `main` branch:

```
https://raw.githubusercontent.com/socfortress/copilot-announcements/main/feed.json
```

CoPilot's backend fetches it at most once an hour, so a change shows up within the hour. It shows
admins and analysts in the analyst console (never Customer Portal users) a toolbar entry
with a dot for anything they haven't seen, and a modal at sign-in for unseen `popup` items.

## Publishing an announcement

Add an object at the top of `announcements` and merge to `main`. Fields:

| Field | Required | Notes |
|---|---|---|
| `id` | yes | Unique and never reused: `[a-z0-9][a-z0-9._-]{0,79}`. Whether a user has seen an item is remembered by id, so changing it shows the item again as new. |
| `category` | yes | `product`, `release`, `advisory`, `event` or `promotion`. Deployments can hide `promotion` (`ANNOUNCEMENTS_HIDE_PROMOTIONS=true`). |
| `title` | yes | Up to 120 characters. |
| `summary` | no | One line, up to 300 characters, shown in the list. |
| `body_markdown` | no | Up to 20,000 characters of Markdown. Raw HTML is not rendered, and images in the Markdown show as links. |
| `image_url` | no | An https image shown above the title (use `images/` here and its raw URL). Wide images about 1200×500 work best. |
| `image_alt` | no | The image's alt text. |
| `cta` | no | `{ "label": "...", "url": "https://..." }`, a button that opens in a new tab. Label up to 40 characters. |
| `published_at` | yes | ISO 8601, e.g. `2026-10-22T15:00:00Z`. An item dated in the future stays hidden until then, so you can schedule one. |
| `expires_at` | no | Hidden from this time on. |
| `popup` | no | `true` shows it in the sign-in modal to users who haven't seen it. Use it sparingly. |
| `audience` | no | Any of `admin`, `analyst`, `responder`. Defaults to all three. Responders also see `analyst` items. |
| `min_version` / `max_version` | no | Only CoPilot versions in this range (inclusive) show the item, e.g. `"min_version": "0.1.99"`. |

Rules CoPilot enforces (an item that breaks one is dropped, never the whole feed):

- Every URL is `https://`, with no credentials.
- The file stays under 512 KB. Delete expired items now and then.
- `version` stays `1`.

Unknown fields are ignored, so adding one never breaks older CoPilot versions.

Check the file before merging: `python3 -m json.tool feed.json > /dev/null`.
