---
title: Collaboration
slug: collaboration
order: 3
---

# Sharing a book across devices

another.money can keep one book in sync across several devices — your phone and
laptop, or two people sharing a household budget — while staying
**end-to-end encrypted**. The sync relay only ever stores ciphertext; it can't
read your accounts, amounts, or names.

> Collaboration is an optional, premium feature. A single local book never needs
> it and never talks to a server.

## How it works

1. **Invite.** On the device that owns the book, open **Set up collaboration**
   and create an invite. You get a link/QR code to share with the other device.
   The book password is shared separately — it's never in the link.
2. **Join.** The other device opens the invite, enters the shared password, and
   requests to join.
3. **Approve.** The owner approves the request with one tap. You don't have to
   be on the invite screen at the moment they join — the next time you open the
   app you'll be prompted to approve any waiting requests.
4. **Sync.** From then on, both devices sync automatically: changes flow through
   the relay, encrypted, and merge on each device.

## Offline-friendly

Every device works fully offline and merges cleanly when it reconnects —
edits made on different devices combine without clobbering each other, and
deletes are respected. You can keep using the app on a plane and it'll catch up
later.

## Good to know

- **Same password.** All devices on a shared book use the same book password —
  it's what derives the encryption keys.
- **Removing a device.** The owner can revoke a device; it loses access on its
  next sync.
- **It's still your data.** Even while shared, the book stays a local encrypted
  file on each device. Turning sync off leaves each device with its own copy.
