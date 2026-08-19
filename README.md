# OverTheWire Krypton — Walkthrough & Notes

[![Status](https://img.shields.io/badge/Completed-Levels_0%E2%86%927-brightgreen)](#)
[![System](https://img.shields.io/badge/OS-Ubuntu_22.04_LTS-lightgrey)](#)
[![Last Updated](https://img.shields.io/badge/Updated-2025--12--02-blue)](#)

> A practical walkthrough of OverTheWire Krypton.
> This repository documents the techniques, commands and reasoning used to solve the levels.
> Passwords are redacted in accordance with OverTheWire's policy.

---

## Introduction

Krypton is a cryptography-focused wargame covering classical cryptographic techniques and basic cryptanalysis.

This repository documents the solutions for levels 0–7, including:

- Base64 encoding
- ROT13
- Caesar ciphers
- Brute-force rotation
- Monoalphabetic substitution ciphers
- Frequency analysis
- Vigenère ciphers
- Kasiski examination
- Known-plaintext attacks
- XOR-based encryption
- Stream ciphers
- Linux command-line cryptographic analysis

All exercises were performed in an isolated lab environment using Linux.

---

## Tools

### Cryptography and Encoding

- tr
- xxd
- base64
- strings
- od
- cut

Used for character substitution, encoding and decoding, byte-level inspection and data analysis.

### Analysis and Debugging

- gdb
- file
- strings
- xxd -r
- hexdump

Used for analysing binaries, encrypted files and raw byte representations.

### Filesystem and Utilities

- ls
- cd
- pwd
- find
- cp
- mv
- cat
- chmod
- touch

Used for navigating the challenge environment and preparing files for analysis.

### Text Processing

- grep
- cut
- sort
- uniq
- awk
- sed

Used for filtering, counting and analysing ciphertext.

### Scripting

- Python 3

Used to automate cryptographic analysis and repetitive operations.

### Network

- ssh

Used to access the Krypton challenge environment.

---

## General Methodology

The main workflow used throughout Krypton was:

1. Inspect the challenge files and understand how the encryption is implemented.
2. Identify the cryptographic algorithm or transformation being used.
3. Determine whether the key or transformation can be recovered from available information.
4. Use Linux utilities to manipulate and analyse the ciphertext.
5. Automate repetitive cryptographic operations when necessary.
6. Recover the plaintext and identify the underlying cryptographic weakness.
7. Document the security lesson demonstrated by the level.

Common techniques include:

- Base64 decoding
- ROT13 decoding
- Caesar cipher brute force
- Frequency analysis
- Monoalphabetic substitution
- Vigenère cryptanalysis
- Kasiski examination
- Known-plaintext attacks
- XOR analysis
- Stream cipher analysis
- Byte-level data manipulation

---

## Level 0 → 1

### Objective

Decode a Base64-encoded value to obtain the next password.

### Method

Connect to the Krypton environment:

```bash
ssh krypton1@krypton.labs.overthewire.org -p 2231
