# AES Encryption Implementation

## Overview
This project implements **AES-128 encryption** in C++, focusing on **key expansion, substitution, shifting, mixing, and key addition** operations. The program encrypts an input message using a **16-byte key** and outputs the encrypted binary data.

## Features
- AES encryption for **128-bit data blocks**.
- Uses **SubBytes, ShiftRows, MixColumns, and AddRoundKey** transformations.
- **Generates round keys** using the AES **key expansion** process.
- **Processes input data in 16-byte blocks**.

## Mathematical Background
AES encryption operates on a **4x4 matrix (state matrix)** and follows transformations in **GF(2^8) (Galois Field 256)**.

### **Why 176-Byte Round Key Array?**
AES-128 operates with **10 encryption rounds**, each requiring a **16-byte subkey**, plus the **initial round key**:

\[
\text{Total key size} = 16 \times (10 + 1) = 176 \text{ bytes}
\]

### **Why ShiftRows?**
ShiftRows is used to **reduce redundancy** by spreading the influence of each byte across different columns, preventing identical encryption patterns.

## **Key Components**
### **1. S-Box (`SubstationBox`)**
- Used in the **SubBytes** transformation.
- Provides **non-linearity and security** by replacing each byte with a predefined value.

### **2. Multiplication Tables (`Multiplication222` and `Multiplication333`)**
- Used for **MixColumns** transformation.
- AES multiplication in **GF(2^8)** follows modular arithmetic rules, simplified using these lookup tables.

### **3. Round Keys (`RoundKeys[176]`)**
- Stores all **11 round keys** required for AES-128 encryption.
- Generated using **key expansion** from an initial 16-byte key.

### **4. Round Constant (`rcon[10]`)**
- Array of constants used during **key expansion**.
- Derived from **powers of 2 in GF(2^8)**.
- Ensures **each round key is unique** by modifying the key in each round.

## **Function Breakdown**
### **1. Key Expansion (`GenerateRoundKeys`)**
- Expands a **16-byte key** into **176 bytes** of round keys.
- Uses **S-Box substitution** and **round constants (RCON)** for non-linearity.

#### **Key Expansion Process**
**Step 1:** Copy the original key into the first **16 bytes** of `RoundKeys`:
```cpp
while (i < 16) { 
    RoundKeys[i] = key[i]; 
    i++; 
}
```

**Step 2:** Expand the remaining **160 bytes** using:
- **Byte rotation (Left Rotation)** every **16 bytes**:
```cpp
if (i % 16 == 0) {
    unsigned char t = temp[0]; 
    temp[0] = temp[1]; 
    temp[1] = temp[2]; 
    temp[2] = temp[3]; 
    temp[3] = t; 
}
```

- **Substitution using S-Box**:
```cpp
for (int j = 0; j < 4; j++) {
    temp[j] = SubstationBox[temp[j]];
}
```

- **XOR with the round constant (RCON)**:
```cpp
temp[0] ^= rcon[rconIndex++];
```

- **Generate the next 4 bytes by XORing with the previous round key**:
```cpp
for (int j = 0; j < 4; j++) {
    RoundKeys[i] = RoundKeys[i - 16] ^ temp[j];
    i++;
}
```

### **2. SubBytes (`SubstituteBytes`)**
- Each byte is replaced using the **S-Box** to add confusion.

### **3. ShiftRows (`ShiftRows`)**
- Ensures **diffusion** by rearranging rows:
  - **Row 0:** No shift
  - **Row 1:** Shift left by 1 byte
  - **Row 2:** Shift left by 2 bytes
  - **Row 3:** Shift left by 3 bytes

### **4. MixColumns (`MixColumns`)**
- **Mixes bytes within each column** using matrix multiplication in **GF(2^8)**:
```cpp
tmp[col] = Multiplication222[Block[col]] ^ Multiplication333[Block[col + 1]] ^ Block[col + 2] ^ Block[col + 3];
```

### **5. AddRoundKey (`AddRoundKey`)**
- XORs the **current state matrix** with the corresponding round key.

### **6. AES Algorithm (`AES_algorithm`)**
- **Initial Round:** Adds the first round key.
- **9 Main Rounds:**
  - `SubBytes → ShiftRows → MixColumns → AddRoundKey`
- **Final Round:**
  - `SubBytes → ShiftRows → AddRoundKey` (no MixColumns)
- **Returns the encrypted block.**

## **Main Function (`main`)**
```cpp
std::vector<uint8_t> data;  // Stores input plaintext
uint8_t key[16];            // Stores AES encryption key
char charecter;
```

### **How `main` Works**
1. **Reads a 16-byte encryption key** from input:
```cpp
for (int i = 0; i < 16 && std::cin.get(charecter); i++) {
    key[i] = static_cast<uint8_t>(charecter);
}
```
2. **Reads the remaining plaintext message into `data`**:
```cpp
while (std::cin.get(charecter)) {
    data.push_back(static_cast<uint8_t>(charecter));
}
```
3. **Encrypts the message in 16-byte blocks:**
```cpp
for (size_t i = 0; i < data.size(); i += 16) {
    uint8_t message[16] = { 0 };
    std::copy_n(data.begin() + i, std::min<size_t>(16, data.size() - i), message);
    AES_algorithm(message, key);
    std::cout.write(reinterpret_cast<char*>(message), 16);
}
```

### **How the Code Works**
1. Waits for **16 bytes of input** as a key.
2. Reads **data from standard input** in **16-byte chunks**.
3. **Encrypts each chunk using AES**.
4. **Outputs the encrypted binary data.**

## **Summary**
- The AES implementation follows **SubBytes, ShiftRows, MixColumns, and AddRoundKey** transformations.
- Key expansion **ensures unique round keys**.
- The main function **reads plaintext, encrypts it in 16-byte blocks, and outputs encrypted data**.
- `ShiftRows` **removes redundancy**, and `MixColumns` **spreads data across the block**.

This README provides a **detailed breakdown of the code and its mathematical foundation** for easy reference. 🚀
