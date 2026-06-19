# another.money — community content

This folder holds the **content** for the [another.money](https://another.money)
website: the marketing copy, getting-started guide, FAQ, and help pages. It is
deliberately just **Markdown** so anyone can improve it with a pull request —
no build tooling, no app knowledge required.

The website is intentionally tiny: a static shell that renders the Markdown in
[`content/`](./content). Editing a page here changes the live site; adding a new
`.md` file under `content/` adds a new page.

## Layout

| File | Becomes |
|---|---|
| `content/home.md` | the landing page (`/`) |
| `content/getting-started.md` | `/getting-started` |
| `content/collaboration.md` | `/collaboration` |
| `content/privacy-and-security.md` | `/privacy` |
| `content/faq.md` | `/faq` |

Each page starts with a short front-matter block:

```md
---
title: Getting started
slug: getting-started
order: 2
---
```

- `title` — shown in the nav + browser tab.
- `slug` — the URL path (omit for `home`).
- `order` — position in the nav.

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md). In short: edit the Markdown, open a PR,
keep it accurate and friendly. Screenshots go in `content/assets/`.

## Tone

Plain, honest, no hype. another.money is a personal-finance app you can trust
with your money data — the writing should earn that trust: concrete, calm, and
never overclaiming. Say "alpha" where things are still alpha.
