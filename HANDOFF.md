# MycoQuest Shop: handoff

Updated 2026-11-26.

## What this is today

A static prelaunch project with no server, accounts, database, or payments.

The visitor demo is published on GitHub Pages at https://jefeh-create.github.io/MycoQuestShop/, deployed by
`.github/workflows/pages.yml` on every push to `main`. That workflow leaves out `lab.html` and removes its footer link.
The prep lab is not hosted.

| Page | Who it's for | What it does |
| --- | --- | --- |
| `Mycoquest.shop/Index.html` | Visitors (demo) | Trail map, shop concepts, recipe quests, demo grow log with XP, share card |
| `Mycoquest.shop/lab.html` | The owner, privately | Batches, observations, photos, genetics, suppliers, draft catalog, journey map, backups |

Both pages work in **English** (default) and **Spanish**, chosen with the selector in the header.

| Works for real, on this device | Demo or example only | Not built |
| --- | --- | --- |
| Prep lab: batches, observations, photos, genetics, suppliers, catalog drafts, journey map | Shop products and prices | Checkout, orders, inventory |
| Prep lab backup, restore, erase | XP, levels, moderation controls | Accounts and sign-in |
| Visitor trail, filters, recipes, grow check-ins, share card | Reactions on recipe posts | Server-side XP and review |
| Language choice (English, Spanish) | Milwaukee pickup and delivery (planned) | Following, public feed, uploads to a server |

Cultivation genetics, kits and equipment, supplements, and tinctures are explained on the visitor demo but have
**no listings**. Catalog statuses in the lab (researching, preparing, planned, unavailable, ready for review) never make
anything public or available.

## Where data lives

- Prep lab records: `localStorage` key `mycoquest-prep-lab-v1`. Photos: IndexedDB database
  `mycoquest-prep-lab-photos`, re-encoded as JPEG (removes EXIF and GPS).
- Visitor demo progress: `localStorage` key `mycoquest-demo-progress-v1`.
- Language choice: `localStorage` key `mycoquest-language` (a language code only, separate from records and backups).
- Everything is tied to the exact address (file, `127.0.0.1:8000`, and `localhost:8000` are separate). Not encrypted,
  not synced. The lab's Backup & storage page explains the loss risks and makes backup files.

## Languages

- Text lives in `Mycoquest.shop/i18n/` (`en.js` source, `es.js`, `hmn.js` disabled). See `i18n/README.md`.
- Spanish: 901 of 901 strings, U.S. Spanish with "tú". `es.js` `meta.review` flags 12 lines for review: food-storage and
  cooking guidance, cultivation safety, supplement and tincture wording, two common mushroom names, and the long XP rules.
  A fluent speaker should review all of it before a public launch.
- Hmong: structure only, not enabled, no machine translation.
- Saved values (stages, statuses, types) stay in English so data and backups never change with the language.
- Check: `node tools/check-translations.mjs`.

## Before a real shop can open

### Decisions only the owner can make

Verified products, prices, package sizes, stock, photos, labels; which species and genetics may
legally be sold where; food, supplement, and tincture rules and licences; pickup, delivery,
shipping, tax, refund, privacy, and community moderation policies.

### Build order

1. Choose a host that sends HTTP headers, and a commerce route. A hosted store or hosted
   checkout from an established provider is the smallest and safest first step. Check the
   provider's restricted-products rules before choosing, especially for genetics and tinctures.
2. Server-side catalog: the server owns product IDs, prices, stock, quantity limits, and totals.
3. Hosted checkout. Card details never touch this site. Mark an order paid only from a
   signature-verified, idempotent provider webhook.
4. Accounts, only if needed: a maintained auth provider, `Secure` `HttpOnly` `SameSite` cookies,
   CSRF protection, and ownership checks on every request (row-level security if using a
   database that supports it).
5. Server-side grow logs and community: private by default, moderation before publishing,
   report and block, data export and deletion, XP calculated on the server.
6. The prep lab becomes a signed-in, owner-only area on the server. Hiding a link is not access control.

### Security work still needed

- [ ] Do not publish `lab.html` on a public host as it is. Its code holds no data, but a hosted private area needs
      server-side sign-in and authorization.
- [ ] HTTPS, HSTS, and headers from the host: CSP (with `frame-ancestors 'none'`),
      `X-Content-Type-Options`, `Referrer-Policy`, `Permissions-Policy`.
- [ ] Remove `'unsafe-inline'` from the CSP by moving script and styles to files or hashing them.
      The current meta policy limits network access but does not stop injected inline script.
- [ ] Secrets only in the host's environment settings; none in this public repo or the browser.
- [ ] Server validation of all input; parameterised queries; rate limits and abuse controls.
- [ ] Uploads: check file contents on the server, cap size, re-encode images, strip location
      metadata, rename, store privately, serve from a separate origin. The browser checks
      here are a convenience, not a control.
- [ ] Safe error messages; logs without personal data or tokens.
- [ ] Dependency review and updates once there are dependencies.
- [ ] Independent security review before taking money.

The CSP allows scripts only from the page itself and the site's own files, and blocks all network requests from script.
Adding a backend means allowing its origin.

## Don't break

- Grow log v2 import: `{app: "MycoQuest grow log", version: 2, entries, recipes}`; extra keys ignored.
  Its import limit is 100 MB because those backups carry photos inline.
- Saved keys: `mycoquest-demo-progress-v1`, `mycoquest-prep-lab-v1`, `mycoquest-prep-lab-photos`, `mycoquest-language`.
- Prep lab backup format: `{app: "MycoQuest prep lab", version: 1, data, photos}`.
- Recipe ids are shared between the two pages (`demo.recipe.<id>.*`).
- Photo credits.

## Future idea: community board (not started)

Owner's idea (2026-11-26), for after launch. It's undecided whether this becomes a very simple social space or more of a
small blog.

- Regulars post grow cards and share what they know. They can see each other's posts and react: like, dislike, or a
  small set of reactions.
- The lab and the share space would feel like one connected thing, still as two tabs.
- It's meant to be a simple board built around grow cards. It's not meant to be a full social network.
- Posts can be built from what already exists: the visitor share card (private fields off by default) and lab batches,
  observations and photos.
- It needs a live backend: accounts and sign-in, server storage, uploads handled under the checklist above, moderation
  (review before publishing, report, block, remove), privacy settings, and data export and deletion. Build order step 5
  covers the same ground.
- Nothing should be faked in the meantime. A board that only lives in one browser can't show anyone else's posts.

## Open points

- This demo reaches level 50 at 53,000 XP. The standalone grow log's notes say 55,000.
  They are separate projects, so nothing was changed; confirm that is intended.
- Not yet checked with a screen reader, a contrast audit tool, a real phone, Safari, or Firefox.

## Preview

```
python -m http.server 8000 --bind 127.0.0.1 --directory Mycoquest.shop
```

Open http://127.0.0.1:8000/Index.html and http://127.0.0.1:8000/lab.html
