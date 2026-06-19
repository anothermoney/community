# Contributing to another.money content

Thanks for helping make another.money clearer and more useful! This repository
holds the **website + help content** as plain Markdown. You don't need to know
how the app is built to improve it.

## Ways to help

- **Fix or clarify** a page in [`content/`](./content) — typos, confusing
  wording, out-of-date steps.
- **Add a page** — drop a new `.md` file in `content/` with front-matter
  (`title`, `slug`, `order`). It becomes a new route automatically.
- **Add screenshots** — put images in `content/assets/` and reference them with
  a relative path.
- **Answer a recurring question** — add it to `content/faq.md`.

## How to submit

1. Edit the Markdown (right in GitHub's web editor is fine for small changes).
2. Open a pull request describing what you changed and why.
3. A maintainer reviews for accuracy and merges. Merged changes go live on the
   site automatically.

## Style

- **Accurate first.** Never claim a feature that doesn't exist or overstate
  what's shipped. Mark things "alpha" when they are.
- **Plain and calm.** Short sentences, concrete steps, no marketing hype. This
  is a money app — readers are trusting us; the writing should sound trustworthy.
- **Respect privacy claims.** Statements about encryption/privacy must match how
  the app actually behaves. If unsure, ask in the PR rather than guessing.
- **Link, don't repeat.** Cross-link related pages instead of duplicating.

## What doesn't go here

This repo is content only. App bugs and feature requests belong in the app's
issue tracker (or the in-app bug report), not in content PRs — though pointing
out where the docs are *wrong* is always welcome.
