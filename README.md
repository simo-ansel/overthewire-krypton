# OverTheWire **Krypton** — Walkthrough & Appunti

[![Stato](https://img.shields.io/badge/Completato-Livelli_0%E2%86%927-brightgreen)](#)
[![Sistema](https://img.shields.io/badge/OS-Ubuntu_22.04_LTS-lightgrey)](#)
[![Ultimo aggiornamento](https://img.shields.io/badge/Aggiornamento-2025--12--02-blue)](#)

> Percorso riproducibile attraverso **OverTheWire — Krypton**.  
> Questo README documenta l’approccio, i comandi e le tecniche usate per risolvere i livelli.  
> Tutte le **password sono oscurate** in conformità con la policy ufficiale di OverTheWire.

---

## 🧭 Introduzione
**Krypton** esplora i fondamenti della *crittografia classica*: cifrari a sostituzione, rotazioni, Vigenère e manipolazione di dati.

Ogni livello richiede:
- ragionamento crittografico;
- uso di strumenti Linux;
- talvolta scrittura di piccoli script.

---

## 🧰 Tools — principali comandi e servizi

### 🔐 Crittografia / Encoding
`tr` `xxd` `base64` `strings` `od` `cut`

### 🧩 Analisi & debugging
`gdb` `file` `strings` `xxd -r` `hexdump`

### 📁 Filesystem & utility
`ls` `cd` `pwd` `find` `cp` `mv` `cat` `chmod` `touch`

### 🔡 Testo & stream processing
`grep` `cut` `sort` `uniq` `awk` `sed`

### 🐍 Script / Automazioni
`python3`

### 🌐 Rete
`ssh`

---

# 🧩 Percorso dei Livelli

<details>
<summary><b>Indice rapido livelli 0 → 7</b></summary>

- [🔹 Livello 0 → 1](#-livello-0--1)
- [🔹 Livello 1 → 2](#-livello-1--2)
- [🔹 Livello 2 → 3](#-livello-2--3)
- [🔹 Livello 3 → 4](#-livello-3--4)
- [🔹 Livello 4 → 5](#-livello-4--5)
- [🔹 Livello 5 → 6](#-livello-5--6)
- [🔹 Livello 6 → 7](#-livello-6--7)

</details>

---

## Formato dei contenuti
Ogni livello segue la struttura:

1. **🎯 Obiettivo**
2. **💻 Comandi principali**
3. **🧠 Spiegazione**
4. **🪄 Takeaway**

---

## 🔹 Livello 0 → 1

### 🎯 Obiettivo
Decodificare un semplice cifrario *Base64* per ottenere la password successiva.

### 💻 Comandi principali
```bash
ssh krypton1@krypton.labs.overthewire.org -p 2231
echo "S1JZUFRPTklTR1JFQVQ=" | base64 -d
# Output: [REDACTED]
```

### 🧠 Spiegazione
Krypton inizia con un encoding base64: non è cifratura, solo rappresentazione dei dati.

### 🪄 Takeaway
- Base64 non cifra, **solo codifica**.

---

## 🔹 Livello 1 → 2

### 🎯 Obiettivo
Analizzare uno script ROT-13 e decodificare la password.

### 💻 Comandi principali
```bash
cat /krypton/krypton1/krypton2
tr 'A-Za-z' 'N-ZA-Mn-za-m' < krypton2
# Output: [REDACTED]
```

### 🧠 Spiegazione
Il livello introduce **ROT13**, una rotazione di 13 caratteri dell’alfabeto.

### 🪄 Takeaway
- ROT13 è il caso particolare più noto del cifrario a rotazione.

---

## 🔹 Livello 2 → 3

### 🎯 Obiettivo
Recuperare la password cifrata via Caesar Cipher (ROT-n).

### 💻 Comandi principali
```bash
# Preparazione ambiente
cd /krypton/krypton2
mktemp -d
cd /tmp/tmpdir
ln -s /krypton/krypton2/keyfile.dat
chmod 777 .

# Prova encryption
echo "AAAAA" > encrypt.txt
/krypton/krypton2/encrypt encrypt.txt
cat ciphertext
# Output: MMMMM

# Decodifica del file krypton3
cat krypton3 | tr 'A-Z' 'O-ZA-N'
# Output: [REDACTED]
```

### 🧠 Spiegazione
Livello dedicato alla **rotazione arbitraria** (Caesar Cipher): non più ROT13 fisso.

### 🪄 Takeaway
- Brute-force ROT-n richiede massimo 25 tentativi.

---

## 🔹 Livello 3 → 4

### 🎯 Obiettivo
Decifrare un cifrario monoalfabetico usando **frequency analysis**.

### 💻 Comandi principali
```bash
cd /krypton/krypton3
# Analisi frequenze
for i in {A..Z}; do cat found1 found2 found3 | tr -cd $i | wc -c | tr -d '\n'; printf " $i \n"; done | sort -nr

# Sostituzione basata su frequenze inglesi
cat krypton4 | tr 'SQJUBNGCDZVWMYTXKELAFIORHP' 'EATSORNIHCLDUPYFWGMBKVXQJZ'
# Output: [REDACTED]
```

### 🧠 Spiegazione
Il cifrario monoalfabetico usa una **mappatura fissa** tra plaintext e ciphertext.  
La frequenza delle lettere inglesi permette di dedurre la chiave.

### 🪄 Takeaway
- Frequency analysis è efficace su sostituzioni monoalfabetiche.

---

## 🔹 Livello 4 → 5

### 🎯 Obiettivo
Decifrare un **Vigenère Cipher** con chiave nota di lunghezza 6.

### 💻 Comandi principali
```bash
# Analizza found1 per ottenere la chiave
# Decodifica krypton5
# Output decodificato: [REDACTED]
```

### 🧠 Spiegazione
Il Vigenère Cipher introduce la **polialfabetica**: una lettera del plaintext può mappare a più lettere nel ciphertext a seconda della chiave.

### 🪄 Takeaway
- Con chiave nota, Vigenère è facilmente decifrabile.

---

## 🔹 Livello 5 → 6

### 🎯 Obiettivo
Decifrare un Vigenère Cipher con **chiave sconosciuta**.

### 💻 Comandi principali
```bash
# Usare Kasiski's Test su found1/found2 per stimare lunghezza chiave
# Chiave individuata: KEYLENGTH
# Decodifica krypton6
# Output decodificato: [REDACTED]
```

### 🧠 Spiegazione
- Test di Kasiski permette di stimare la lunghezza della chiave.
- Dopo aver ottenuto la lunghezza, la chiave può essere determinata e applicata.

### 🪄 Takeaway
- Vigenère con chiave sconosciuta richiede **analisi statistica**.

---

## 🔹 Livello 6 → 7

### 🎯 Obiettivo
Decifrare un **One-Time Pad / Stream Cipher** usando Known Plaintext Attack.

### 💻 Comandi principali
```bash
cd /krypton/krypton6/onetime
# Creazione plaintext noto
python3 -c "print('A'*100)" > a.txt
/krypton/krypton6/encrypt6 a.txt cipher_a.txt
# Output cipher_a.txt → usare per ricavare la chiave
# Decodifica krypton7 usando la chiave derivata
# Output decodificato: [REDACTED]
```

### 🧠 Spiegazione
- One-Time Pad con chiave lunga e casuale è teoricamente **non decifrabile senza la chiave**.  
- L’attacco sfrutta plaintext noto per ottenere la chiave, poi decifra il messaggio.

### 🪄 Takeaway
- Randomness e chiavi lunghe rendono un cifrario **sicuro contro frequency analysis**.
- Con Known Plaintext Attack è possibile ricostruire la chiave e decifrare.

---

# 🏁 Epilogo

Completare **Krypton** consolida concetti crittografici fondamentali:
- cifrari a sostituzione;
- rotazioni;
- Vigenère;
- manipolazione di rappresentazioni (base64, hex, ASCII).

Un wargame essenziale per chi studia cybersecurity.

---

## 🙏 Crediti

**OverTheWire — Krypton** è un progetto degli autori OverTheWire.  
Tutti i diritti dei contenuti originali appartengono ai rispettivi proprietari.
