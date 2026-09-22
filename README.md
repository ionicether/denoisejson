# denoisejson

A JSON diff that hides everything identical. What's left on screen is what changed.

Paste two API responses into most diff tools and you get 400 identical lines with 6 interesting ones buried in them. This one collapses identical subtrees to a single row, folds runs of unchanged keys into `12 unchanged`, and maps the whole document down the right edge so you can see where the changes are before you scroll.

[**Try it**](https://morley.dev/denoisejson/), or grab [`denoisejson.html`](denoisejson.html) and open it. That's the install. One file, no deps, nothing leaves your machine.

## Arrays

Insert one item at the top of a 400-element list → a position-based differ reports 400 changes. All true, all useless.

This matches array elements on identity instead (an idea [jsondiffpatch](https://github.com/benjamine/jsondiffpatch) had first). If every object in both arrays has a field that's unique within its own array (`id`, `_id`, `uuid`, `key`, `slug`, `sku`, `name`, `email`), that becomes the identity, and the arrays get aligned on it by longest common subsequence. Reordering costs nothing. Inserts stay local. An edit gets reported against the record it happened to.

No such field? Elements match on contents, and a removal sitting next to an insertion gets paired back into one edit. That's what turns `[{v:1},{v:2}]` → `[{v:1},{v:3}]` into `t[1].v: 2 → 3` instead of a delete plus an add.

Alignment is quadratic, so past ~1M cell comparisons it gives up and pairs by index.

## Keys

| | |
|---|---|
| `n` / `p` | next / previous change |
| `/` | filter by path or value |
| `u` | show unchanged keys anyway |
| `s` | swap A and B |
| `e` | show/hide the inputs |

Click a key → copies its path. Click a value → copies the value. **Copy changes** dumps the whole list as text for pasting into a ticket.

Teal = added, rose = removed, amber = modified. Every row also carries `+`, `−`, or `~` in the gutter, so color is never the only thing telling you what happened.

Opens dark (`◑` switches it, and remembers). Nothing else is stored, since debugging payloads tend to have credentials in them.

## Prior art

JSON diffing is well-trodden and most of the good ideas here aren't mine.

- [jsondiffpatch](https://github.com/benjamine/jsondiffpatch) → matches arrays on `key`/`id`/`name`, can hide unchanged values. Most complete option. Use it if you need patches or RFC 6902 output.
- [jd](https://github.com/josephburnett/jd) → LCS array diffing + patch generation, ships a WASM UI.
- [json-diff-kit](https://github.com/RexSkz/json-diff-kit) → also pairs a removal beside an insertion into a modification. A proper React library.

**Use those** if you need the diff as *data*: something to store, send, apply, or reverse. They're patch systems. This isn't one, and never will be.

**Use this** if you just need to look at two payloads right now:

- Nothing to install, nothing to download. Open the link and it's running. No npm, no bundler, no React host app, no Go binary.
- Everything happens in your browser. No server, no upload, no request leaves the page, so pasting a production payload isn't a decision you have to think about.
- Save denoisejson.html and it works offline, forever. One file, no dependencies, nothing to fetch. Fine on an airgapped box, behind a corporate firewall, or on a plane.
- Unchanged content is gone by default, not behind a toggle. Different default, and it's the whole point.
- Built to be read: `n`/`p` to walk the changes, `/` to filter, and a map of the entire document so you know where the changes are before you scroll.

Roughly: they answer *what is the change, as something I can manipulate?* This answers *what changed, so I can see it?* If you're building sync, audit logs, or undo → not this.

## Notes

`index.html` and `denoisejson.html` are the same file. Pages serves the first, you download the second. Edit one → copy it over the other.

A personal tool, shared in case it's useful. Issues welcome, PRs probably not.

## License

MIT
