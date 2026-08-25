# denoisejson

A JSON diff that hides everything identical, so what's left on screen is only what changed.

Paste two API responses into most diff tools and you get four hundred identical lines with six interesting ones buried in them. This one collapses identical subtrees to a single row, folds runs of unchanged keys into `12 unchanged`, and puts a map of the whole document down the right edge so you can see where the changes are before you scroll.

**[Try it](https://ionicether.github.io/denoisejson/)** — or download [`denoisejson.html`](denoisejson.html) and open it. That's the whole install. One file, no dependencies, nothing leaves your machine.

## Arrays

Insert one item at the top of a 400-element list and a position-based differ reports 400 changes. All technically true, all useless.

denoisejson matches array elements on identity instead, an approach [jsondiffpatch](https://github.com/benjamine/jsondiffpatch) came up with first. If every object in both arrays carries a field that's unique within its own array — `id`, `_id`, `uuid`, `key`, `slug`, `sku`, `name`, or `email` — that becomes the identity and the arrays are aligned on it by longest common subsequence. Reordering costs nothing, inserts stay local, and an edit is reported against the record it happened to.

With no such field, elements are matched on their contents, and a removal next to an insertion is paired back into a single edit. That's what turns `[{v:1},{v:2}]` → `[{v:1},{v:3}]` into `t[1].v: 2 → 3` instead of a delete plus an add.

Alignment is quadratic, so past about a million cell comparisons it gives up and pairs by index.

## Keys

| | |
|---|---|
| `n` / `p` | next and previous change |
| `/` | filter by path or value |
| `u` | show the unchanged keys anyway |
| `s` | swap A and B |
| `e` | show or hide the inputs |

Click a key to copy its path, a value to copy the value. **Copy changes** puts the whole list on your clipboard as text for pasting into a ticket.

Added is teal, removed is rose, modified is amber, and every row carries `+`, `−`, or `~` in the gutter, so colour is never the only thing telling you what happened.

It opens dark. The `◑` button switches to light and remembers.

Nothing is stored except that theme preference — debugging payloads tend to have credentials in them.

## Prior art

JSON diffing is well-trodden and most of the good ideas here aren't mine.

- [jsondiffpatch](https://github.com/benjamine/jsondiffpatch) — matches arrays on `key`/`id`/`name` and can hide unchanged values. The most complete option, and the one to use if you need patches or RFC 6902 output.
- [jd](https://github.com/josephburnett/jd) — LCS array diffing plus patch generation, with a WebAssembly UI.
- [json-diff-kit](https://github.com/RexSkz/json-diff-kit) — also recognises a removal beside an insertion as a modification. A proper React library.

What's different here is packaging: a single file you double-click, unchanged content hidden by default rather than behind a toggle, and the document map. If none of that matters to you, use jsondiffpatch.

## License

MIT
