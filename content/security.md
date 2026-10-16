---
title: Security design
slug: security
order: 5
description: The technical companion to the privacy page — which keys protect your book, what leaves your device, what the sync relay can and cannot see, and what we still want to improve.
---

# Security design

[Privacy & security](/privacy) gives the short version. This page is the long
one, written for people who read threat models before they trust an app with
their finances. It describes what is **shipped today** (October 2026), not what
we plan to build, and it ends with the limitations we know about.

## Five principles

1. **No account, no server, no permission needed.** A local book never talks to
   anything. Everything else is opt-in.
2. **Encrypt first, then move.** Nothing leaves your device unless it is already
   ciphertext under a key only your devices hold.
3. **We hold no keys.** There is no recovery service, no escrow and no backdoor.
   If you lose your password and your backups, your book is gone. We think that
   is the right trade for a finance app.
4. **Standard, audited primitives.** Argon2id, AES-256-GCM, Ed25519, WebAuthn,
   SQLCipher — from maintained, independently audited libraries. No home-made
   cryptography.
5. **Say what it can't do.** Security claims without limits are marketing. The
   last section of this page lists ours.

![**Where your data lives.** A local book sends nothing anywhere. The two optional paths, a cloud copy and a shared book, only ever carry ciphertext.](/security/where-data-lives.svg?v=3)

## Your book at rest

Each book is one file, encrypted with a key that only exists on your device.

| | In the browser | On iOS and Android |
|---|---|---|
| **Container** | An AES-256-GCM-encrypted image of the database, kept in the browser's own storage for this site only | An SQLCipher 4 database (AES-256-CBC with HMAC-SHA-512 integrity) |
| **Data key** | A random 256-bit key generated when the book is created | Derived from your password with 600,000 rounds of PBKDF2-SHA-512 |
| **Password → key** | Argon2id, 64 MiB of memory, 3 passes, a random 16-byte salt per record | PBKDF2 inside SQLCipher (see limitations) |
| **Changing the password** | Re-wraps the data key in milliseconds; the book itself is not re-encrypted | Re-keys the database |
| **Other ways to unlock** | A **passkey**: the WebAuthn PRF extension turns a passkey plus a random per-book salt into a key that unwraps the data key. User verification is required every time, and the unwrapped key lives only in memory | **Biometrics**: the OS keychain or keystore holds the unlock secret, bound to this device and to the biometric set enrolled at the time. Excluded from device backups |

Every random value — keys, salts, nonces — comes from the operating system's
cryptographic random generator. Every encryption uses a fresh nonce. The app
checks new passwords for strength and refuses weak ones.

![**Unlocking in the browser.** A random data key encrypts the book. Each way you can unlock, password or passkey, holds its own wrapped copy of that key.](/security/browser-keys.svg?v=3)

A book can also be created **without a password**. That is a deliberate choice
for people who want convenience over protection on a device they trust: such a
book is obfuscated, not encrypted, and the app says so where you make the choice.

## What leaves your device

**A local book: nothing.** There is no analytics, no telemetry and no tracking
of what you do with your money.

**Crash reports** are off unless you turn them on, and even then the app asks
you at the moment of the crash. A report waits on your device until you choose
to send it, and before it is shown to you it is scrubbed: book names, amounts,
e-mail addresses, tokens and the addresses of any relay or cloud server are
removed.

**A cloud copy** (optional, premium) uploads the encrypted book file to a
folder in *your* Google Drive or Dropbox account. It is the same bytes under the
same key as the book on your device, so the cloud provider holds ciphertext it
cannot open. We do not run a storage server. The app signs in with
authorization-code OAuth and PKCE; on iOS and Android the refresh token lives in
the keychain or keystore. Passkey records are never uploaded.

**A shared book** (optional, premium) sends encrypted change events to a sync
relay. The next section is about that relay.


## The sync relay: what it sees and what it can't

[Collaboration](/collaboration) keeps one book in sync across devices through a
relay. The relay is designed as an **honest-but-curious** service: assume it
stores everything it receives and reads everything it can. Here is what that
amounts to.

![**What the relay sees.** It reads the outside of every envelope and forwards it. It cannot open one.](/security/relay-view.svg?v=3)

| The relay **can** see | The relay **cannot** |
|---|---|
| An opaque book identifier (a hash of the owner's public key) | Read any account, transaction, payee, category, note or amount |
| Each device's public key, its role (owner or member) and the **label you gave it** | Forge a change that your devices would accept as genuine |
| How many change events there are, their sizes and when they arrive | Learn or guess the book password from anything it holds, except by brute force against a weak password (see limitations) |
| The schema version and the key epoch (a counter that increases when the password is rotated) | Add a device, remove a device or rotate keys |

Only you can run the roles it cannot: the book's **owner device holds a private
certificate authority**. It issues a signed token to each device it approves,
and signs the revocation list the relay enforces. The 24-word recovery phrase
you are shown when you set up sharing is the seed of that authority. It lets you
manage devices from a new phone; it does **not** decrypt data, because the data
key comes from the book password, which the phrase never contains.

**On the wire.** Every change event is encrypted with AES-256-GCM under a key
derived from the book password with Argon2id; the authenticated header binds
the event to the book and its sequence number, so events cannot be replayed
into another book or reordered unnoticed. Every request to the relay is signed
by the device's own Ed25519 key, with the method, path, host, body hash,
timestamp and a one-time nonce under the signature, and the relay keeps a
replay cache.

**Pairing.** An invite is a one-time 128-bit token that expires after an hour.
The link carries it in the URL fragment, which browsers never send to servers,
so it does not land in anyone's access log. The joining device proves it knows
the book password without sending it, both sides show a six-character safety
code to compare, and the owner approves with one tap. The joiner also verifies
that the authority the relay hands it matches the one in the invite, so a
malicious relay cannot substitute its own.

![**Pairing a device.** Four steps; the password never travels, and both sides compare a safety code.](/security/pairing.svg?v=3)

**Running your own.** The relay is a single small binary. We publish signed
releases (Sigstore `cosign`), a one-shot installer that verifies the signature
before installing, and a container image. An invite link carries the relay
address, so a book paired through your relay never contacts ours. If you would
rather not run one, the hosted relay at `sync.another.money` is open during the
beta and runs with the same code, rate limits and request-size caps.

## The web app itself

A web app's weakest point is the browser, so the hosted app at
`app.another.money` ships with:

- A strict Content Security Policy with **Trusted Types enforced**, no inline
  scripts and no third-party scripts at all.
- Subresource integrity on the password-hashing worker.
- HTTPS only, with HSTS and preload submitted.
- No service worker: there is no cached copy of the app that could go stale or
  be poisoned.
- Pinned dependencies with a minimum release age before any update is accepted,
  and a dependency audit in continuous integration.

## Backups, exports and leaving

- **Book file** (`.amdb`): the encrypted book, byte for byte. Anyone who finds
  the file still needs your password.
- **Readable exports** (`.anmoney` backup, JSON snapshot, CSV): plain text by
  design, so you can leave with everything. The app labels them as unencrypted
  wherever it offers them. Treat them like cash.
- **Restore treats every file as hostile.** A backup is data, never
  instructions: the app rebuilds the book from the file's rows and never
  executes schema statements found inside it.

## Known limitations

These come from two internal reviews (July and October 2026) that drove most of
what is described above. We publish them because you cannot evaluate a security
claim without its edges.

1. **Shared-book confidentiality is the strength of the book password.** All
   devices on a shared book derive the same key from the same password. There
   is no forward secrecy, and removing a device does not change the key until
   you also rotate the password. A revoked device that kept a copy of the
   relay's ciphertext could try to brute-force a new password offline. Use a
   long passphrase for a shared book. A per-epoch group key wrapped to each
   device is the planned replacement.
2. **The web data key does not rotate on a password change.** Changing the
   password re-wraps the same key. If you believe a password *and* a copy of
   your encrypted book were both taken, export the book and import it into a
   new one. A real re-key path is planned.
3. **On iOS and Android the database key is derived straight from the
   password** through SQLCipher's PBKDF2, with no separate data key. Biometric
   unlock therefore stores the unlock secret rather than a wrapped key, and a
   stolen database file can be attacked at GPU speed against a weak password.
   The planned fix is the same envelope the web app already has, with the data
   key held by hardware-backed storage.
4. **The relay sees metadata.** Device labels, timing and sizes are visible to
   whoever runs the relay. Run your own if that matters to you.
5. **The browser is the trust boundary.** A compromised browser, a malicious
   extension or a device someone else controls can read what you see on screen.
   The content security policy limits what a web page can do to itself; it
   cannot protect you from the machine it runs on.
6. **No external audit yet.** The reviews so far were internal. An independent
   review is on the roadmap; the findings will be published here.

## Reporting a security issue

Please report security problems privately rather than in a public issue. Use
GitHub's private vulnerability reporting on the
[community repository](https://github.com/anothermoney/community/security/advisories/new).
We will acknowledge within a few days, fix what needs fixing, and credit you
here if you wish.
