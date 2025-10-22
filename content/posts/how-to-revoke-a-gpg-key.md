---
title: "How to Revoke a GPG Key"
date: '2025-10-20T21:41:26+02:00'
draft: false
tags: ["gpg", "security", "revoke"]
categories: ["GPG", "GnuPG", "Security", "Software Engineering"]
---

I often feel the need to have a concise guide that explains how to revoke and remove correctly GPG keys - the keys those are obsolete or perhaps passphrases are forgotten. Revocation is hard if the private key is deleted and is only possible if original revocation certificate is available.

> Note: GPG 2.1+ automatically generates revocation certificates in ~/.gnupg/openpgp-revocs.d/ when creating GPG key-pairs stored on disc. 

## Step 1: Revoke the Key
Choose one of the following methods:

### Method A: Create New Revocation Certificate

> Requires the key passphrase

```bash

# 1. Get your key ID
gpg --list-secret-keys --keyid-format LONG your-email@example.com

# 2. Create revocation certificate
gpg --output revoke-key.asc --gen-revoke your-email@example.com or YOUR_KEY_ID

# For example
gpg --output revoke-key.asc --gen-revoke 5D1A6D0E5177F0B56ED60C84

# 3. Import the revocation certificate to revoke the key
gpg --import revoke-key.asc
```

### Method B: Use Auto-Generated Revocation Certificate
Using this method do not require passphrase, but requires the auto-generated .rev file when the key-pair was created.

```bash
# 1. Get your key ID
gpg --list-secret-keys --keyid-format LONG your-email@example.com

# 2. Edit the revocation file (remove the colon from the beginning)
nano ~/.gnupg/openpgp-revocs.d/YOUR_KEY_ID.rev
Edit the file: Change :-----BEGIN PGP PUBLIC KEY BLOCK-----  TO -----BEGIN PGP PUBLIC KEY BLOCK-----

# 3. Import the revocation certificate
gpg --import ~/.gnupg/openpgp-revocs.d/YOUR_KEY_ID.rev
```

## Step 2: Revoke on Key Server (Optional)
If your key was published on a key server:

```bash
# Skip this step if the key was never uploaded to a key server.
gpg --keyserver keyserver.ubuntu.com --send-keys YOUR_KEY_ID

```

## Step 3: Delete the Key Pair

```bash
# 1. List keys to confirm key ID
gpg --list-keys

# 2. Delete secret key first
gpg --delete-secret-key YOUR_KEY_ID

# 3. Delete public key
gpg --delete-key YOUR_KEY_ID
```

## Step 4: Verify Deletion
```bash

# Confirm keys are removed
gpg --list-keys
gpg --list-secret-keys
```
## Step 5: Clean Up
```bash
# Remove manually created revocation certificate (if using Method A)
rm revoke-key.asc

# Remove auto-generated revocation file (if using Method B)
rm ~/.gnupg/openpgp-revocs.d/YOUR_KEY_ID.rev
```
## Quick Reference

| Command                   | Purpose |
| --------                  | ------- |
| gpg --list-keys           | List public keys |
| gpg --list-secret-keys    | List private keys     |
| gpg --list-secret-keys --with-fingerprint --keyid-format LONG    | List secret keys with key ID and fingerprint |
| gpg --output revoke-keys.asc --gen-revoke EMAIL    | Generate revocation certificate    |
| gpg --import FILE.asc     | Import revocation certificate   |
| gpg --delete-secret-key YOUR_KEY_ID  | Delete private key   |
| gpg --delete-key YOUR_KEY_ID  | Delete public key   |

> Heads-up: Replace YOUR_KEY_ID and your-email@example.com with your actual key ID and email address.


### References:
1. https://stackoverflow.com/a/78464745




