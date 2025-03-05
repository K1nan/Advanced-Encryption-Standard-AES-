# AES Encryption Implementation

## Overview
This project implements the **AES (Advanced Encryption Standard) algorithm** in C++. It provides an encryption function that processes a 128-bit block of plaintext using a 128-bit key.

## Features
- AES encryption for 128-bit data blocks
- Uses **Substitution Bytes**, **Shift Rows**, **Mix Columns**, and **Add Round Key** transformations
- Generates round keys using the **AES key expansion** process
- Processes input data in 16-byte blocks

## How It Works
1. The program reads a **16-byte key** from standard input.
2. It reads the plaintext message from standard input in blocks of 16 bytes.
3. It encrypts each block using the **AES encryption algorithm**.
4. The encrypted output is written to standard output.
