---
title: Privacy & security
slug: privacy
order: 4
---

# Privacy & security

another.money is built so that **your financial data stays yours**. The short
version: it lives on your device, encrypted with your password, and nothing is
uploaded just to use the app.

## Local-first

- No sign-up, no account, no email required to use the app.
- Your book is a single file stored on your device.
- The app works fully offline.

## Encryption

- Each book is **encrypted at rest** with a key derived from your password using
  a strong, memory-hard key-derivation function (Argon2id).
- On supported devices you can unlock with **biometrics** (Face ID / fingerprint)
  instead of typing the password each time.
- Because the key comes from your password, **we can't recover a lost password** —
  there's no backdoor. Keep your password safe and keep a backup of your book.

## Sharing stays end-to-end encrypted

If you turn on [collaboration](/collaboration):

- Changes are encrypted on your device **before** they leave it, and only the
  devices that hold the book password can decrypt them.
- The sync relay stores and forwards **ciphertext only** — it cannot read your
  accounts, balances, payees, or notes.

## What leaves your device

- Nothing, for a local-only book.
- For a shared book: encrypted change-data to the sync relay, plus the minimum
  metadata needed to route it. No analytics or tracking of your finances.

## Backups

- **Book file** — the main backup. **More → Backup & restore → Save book
  file…** exports your whole book as a single portable file that stays
  **encrypted exactly like the book itself**: whoever holds the file still
  needs your password. Store it anywhere you like and restore it on any
  device with **Open book file…**.
- **Plain-text export** — the same screen can also serialise everything to a
  human-readable snapshot. It is **not encrypted**: it's the one path that
  exposes your data without the password, so treat that file accordingly.
  (It's also the copy that would survive a forgotten password.)

Either way the file is yours alone — and a recent backup is the only way back
if you lose your device.

> Have a security question or want to report an issue? See the
> [FAQ](/faq) for how to get in touch.
