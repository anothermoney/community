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

Your **Back up** export is a JSON file you control. Store it somewhere safe
(it contains your data) — and remember it's the only way back if you lose your
device or password.

> Have a security question or want to report an issue? See the
> [FAQ](/faq) for how to get in touch.
