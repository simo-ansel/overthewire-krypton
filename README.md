# OverTheWire Krypton — Walkthrough & Notes

[![Status](https://img.shields.io/badge/Status-Completed%20%230%E2%86%927-brightgreen)](#)
[![System](https://img.shields.io/badge/OS-Ubuntu_22.04_LTS-lightgrey)](#)
[![Last Updated](https://img.shields.io/badge/Updated-2025--12--02-blue)](#)

> Practical walkthrough and technical notes for the OverTheWire Krypton wargame.
> This repository documents the cryptographic techniques, tools and reasoning used to solve levels 0–7.
> Passwords are redacted in accordance with OverTheWire's policy.

---

## Introduction

Krypton is a cryptography-focused wargame covering classical ciphers, encoding and basic cryptanalysis.

The challenge introduces:

- Base64 encoding
- ROT13
- Caesar ciphers
- Monoalphabetic substitution
- Frequency analysis
- Vigenère encryption
- Kasiski examination
- Known-plaintext attacks
- XOR-based encryption
- Stream cipher concepts

All exercises were performed in an isolated Linux environment.

---

## Tools

### Cryptography and Encoding

```text
tr
xxd
base64
strings
od
cut
```

### Text Processing

```text
grep
sort
uniq
awk
sed
```

### Binary Analysis

```text
file
gdb
strings
xxd
hexdump
```

### Filesystem Utilities

```text
ls
cd
pwd
find
cp
mv
cat
chmod
touch
```

### Scripting

```text
Python 3
```

### Remote Access

```text
ssh
```

---

## Methodology

The general approach used throughout Krypton was:

1. Inspect the available ciphertext, files and encryption utilities.
2. Identify the cryptographic transformation being used.
3. Determine whether the key space or statistical structure can be exploited.
4. Use Linux utilities or scripts to analyse the ciphertext.
5. Recover the plaintext or cryptographic key.
6. Document the weakness demonstrated by the level.

The focus is on understanding why each cryptographic construction can be broken rather than simply reproducing the final command.

---

## Level 0 → 1

### Objective

Decode a Base64-encoded value to obtain the password for the next level.

### Method

Connect to the challenge environment:

```bash
ssh krypton1@krypton.labs.overthewire.org -p 2231
```

Decode the supplied value:

```bash
echo "S1JZUFRPTklTR1JFQVQ=" | base64 -d
```

### Takeaway

Base64 is an encoding scheme, not encryption. It provides no confidentiality and can be reversed directly.

---

## Level 1 → 2

### Objective

Analyse a ROT13 transformation and recover the next password.

### Method

Inspect the provided file:

```bash
cat /krypton/krypton1/krypton2
```

Apply ROT13:

```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m' < krypton2
```

### Takeaway

ROT13 is a Caesar cipher with a fixed rotation of 13 positions. Its deterministic transformation provides no meaningful security.

---

## Level 2 → 3

### Objective

Recover a password encrypted with an unknown Caesar cipher rotation.

### Method

Prepare the working environment:

```bash
cd /krypton/krypton2
mktemp -d
```

Create known plaintext:

```bash
echo "AAAAA" > encrypt.txt
```

Encrypt it with the provided utility:

```bash
/krypton/krypton2/encrypt encrypt.txt
cat ciphertext
```

The known plaintext and ciphertext reveal the rotation used by the cipher.

Apply the inverse rotation to the target ciphertext:

```bash
cat krypton3 | tr 'A-Z' 'O-ZA-N'
```

### Takeaway

A Caesar cipher has only 25 non-trivial rotations, making exhaustive search trivial.

---

## Level 3 → 4

### Objective

Break a monoalphabetic substitution cipher using frequency analysis.

### Method

Move to the challenge directory:

```bash
cd /krypton/krypton3
```

Estimate the frequency of each character:

```bash
for i in {A..Z}; do
    cat found1 found2 found3 |
    tr -cd "$i" |
    wc -c |
    tr -d '\n'
    printf " $i\n"
done | sort -nr
```

Use the resulting frequency distribution to infer the substitution mapping.

Apply the recovered mapping:

```bash
cat krypton4 | tr 'SQJUBNGCDZVWMYTXKELAFIORHP' 'EATSORNIHCLDUPYFWGMBKVXQJZ'
```

### Takeaway

Monoalphabetic substitution preserves statistical properties of the plaintext. Frequency analysis can therefore be used to recover the substitution alphabet.

---

## Level 4 → 5

### Objective

Decrypt a Vigenère ciphertext using the recovered key.

### Method

Analyse the supplied material to determine the Vigenère key, then apply the corresponding decryption process to `krypton5`.

The key establishes a repeating sequence of Caesar shifts, so once it is known, the ciphertext can be decrypted directly.

### Takeaway

Vigenère is polyalphabetic, but a known key makes the encryption reversible. The security of the system therefore depends heavily on protecting the key.

---

## Level 5 → 6

### Objective

Break a Vigenère cipher with an unknown repeating key.

### Method

Use the supplied ciphertext samples to identify repeated sequences and estimate the key length using Kasiski examination.

The general workflow is:

```text
Identify repeated ciphertext patterns
        ↓
Measure distances between occurrences
        ↓
Find common factors
        ↓
Estimate key length
        ↓
Split ciphertext by key position
        ↓
Perform frequency analysis
        ↓
Recover the key
        ↓
Decrypt the message
```

### Takeaway

A repeating Vigenère key introduces statistical patterns. Kasiski examination and frequency analysis can exploit those patterns to recover the key.

---

## Level 6 → 7

### Objective

Recover the key material used by the supplied encryption program through a known-plaintext attack and use it to decrypt the next ciphertext.

### Method

Move to the encryption directory:

```bash
cd /krypton/krypton6/onetime
```

Create known plaintext:

```bash
python3 -c "print('A' * 100)" > a.txt
```

Encrypt it:

```bash
/krypton/krypton6/encrypt6 a.txt cipher_a.txt
```

Because both plaintext and ciphertext are known, their relationship can be analysed to recover the keystream or key material used by the implementation.

The recovered material can then be applied to the Krypton 7 ciphertext.

### Takeaway

A correctly implemented One-Time Pad provides perfect secrecy only when the key is truly random, at least as long as the plaintext, kept secret and never reused.

The challenge demonstrates how weaknesses in key generation or key reuse can undermine an otherwise secure construction.

---

## Techniques Demonstrated

Krypton provides practical experience with:

- Encoding versus encryption
- Caesar ciphers
- ROT13
- Monoalphabetic substitution
- Statistical frequency analysis
- Vigenère cryptanalysis
- Kasiski examination
- Known-plaintext attacks
- XOR and keystream analysis
- Classical cryptographic weaknesses
- Linux-based cryptographic tooling

---

## Key Lessons

The challenge reinforces several fundamental principles:

- Encoding does not provide confidentiality.
- Small key spaces can be exhaustively searched.
- Statistical properties can expose weaknesses in substitution ciphers.
- Repeating keys create exploitable patterns.
- Key generation and key reuse are critical to cryptographic security.
- A strong cryptographic construction can still become insecure through weak implementation.

---

## Conclusion

Completing Krypton 0–7 provides a practical introduction to classical cryptography and basic cryptanalysis.

The challenge progresses from simple encoding and substitution ciphers to statistical attacks and known-plaintext analysis, providing a foundation for understanding why modern cryptographic systems require strong algorithms, secure key management and reliable randomness.

---

## Credits

OverTheWire Krypton is maintained by the OverTheWire project and its contributors.

All original challenge content and infrastructure belong to their respective authors.

This repository contains personal notes and walkthrough material created for educational purposes.
