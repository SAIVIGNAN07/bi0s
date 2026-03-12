CryptoHack Challenge Writeup
Tasks 1–9: Encoding & XOR Fundamentals
Platform: CryptoHack | Language: Python 3

Overview
This writeup covers nine CryptoHack challenges focused on encoding schemes and XOR-based cryptography. Each challenge builds on the previous, introducing progressively more complex XOR operations from single-byte brute-force to repeating-key decryption.

Task 1 — Python Warmup (XOR with 0x32)
Challenge
An array of integer ordinal values is given. XOR each value with 0x32 and convert the result to characters to reveal the flag.
Code
ords = [81, 64, 75, 66, 70, 93, 73, 72, 1, 92, 109, 2, 84, 109,         66, 75, 70, 90, 2, 92, 79] print("".join(chr(o ^ 0x32) for o in ords))

Explanation
Each integer in the list is XORed with the constant key 0x32 (decimal 50). The result is passed to chr() to convert the integer back to a character, and the characters are joined into the final string. Python's built-in XOR operator ^ performs bitwise XOR directly on integers.
Flag
FLAG: crypto{z3n_0f_pyth0n}

Task 2 — ASCII Ordinals to Text
Challenge
A list of decimal ASCII code points is provided. Convert each to its character representation to form the flag.
Code
a = [99,114,121,112,116,111,123,65,83,67,73,73,95,112,114,49,110,116,52,98,108,51,125] for i in a:     print(chr(i), end="")

Explanation
The chr() built-in maps an integer (0–127 in ASCII range) to its corresponding character. No encoding or XOR is needed here — this is a pure ASCII lookup exercise that introduces the concept of representing text as numbers.
Flag
FLAG: crypto{ASCII_pr1nt4bl3}

Task 3 — Hex String Decoding
Challenge
A hex-encoded string is provided. Decode it to reveal the plaintext flag.
Code
a = "63727970746f7b596f755f77696c6c5f62655f776f726b696e675f776974685f6865785f737472696e67735f615f6c6f747d" b = bytes.fromhex(a) print(b.decode())

Explanation
bytes.fromhex() converts a hexadecimal string into a raw bytes object. Each pair of hex digits represents one byte. Calling .decode() on the resulting bytes interprets them as a UTF-8 (ASCII) string, yielding the plaintext. This is a foundational skill since hex encoding is ubiquitous in cryptography for representing binary data in a human-readable form.
Flag
FLAG: crypto{You_will_be_working_with_hex_strings_a_lot}

Task 4 — Hex to Base64
Challenge
A hex string is provided. Convert it to a Base64-encoded string.
Code
import base64 a = "72bca9b68fc16ac7beeb8f849dca1d8a783e8acf9679bf9269f7bf" b = bytes.fromhex(a) print(base64.b64encode(b).decode())

Explanation
Base64 encodes binary data as printable ASCII by grouping every 3 bytes into 4 characters chosen from a 64-character alphabet. First, bytes.fromhex() decodes the hex string to raw bytes. Then base64.b64encode() re-encodes those bytes as Base64. The final .decode() converts the returned bytes object to a Python string for printing. Base64 is commonly used to safely transmit binary data over text-based protocols.
Flag
FLAG: crypto/Base+64+Encoding+is+Web+Safe/

Task 5 — Integer to Bytes
Challenge
A large integer is provided. Convert it back to the bytes it represents, then decode as ASCII.
Code
from Crypto.Util.number import long_to_bytes n = 11515195063862318899931685488813747395775516287289682636499965282714637259206269 message = long_to_bytes(n) print(message.decode())

Explanation
Cryptographic algorithms often represent messages as large integers (especially RSA). The long_to_bytes() function from pycryptodome converts such an integer back into a big-endian byte string by repeatedly extracting 8 bits at a time. Once we have the bytes, .decode() yields the original ASCII string. This challenge demonstrates the standard big-endian byte-order convention used in most cryptographic protocols.
Flag
FLAG: crypto{3nc0d1n6_4ll_7h3_w4y_d0wn}

Task 6 — ROT13 / XOR with 13
Challenge
XOR every character in the word "label" with 13 and print the result.
Code
text = "label" result = "" for ch in text:     new_char = chr(ord(ch) ^ 13)     result += new_char print(result)

Explanation
XORing an ASCII character with 13 is equivalent to applying ROT13 within certain ranges of the ASCII table. The key insight is that XOR with 13 (0x0D) flips the last 4 bits of characters whose upper nibble is even, effectively rotating within a 16-character block. For the input "label", this produces "aloha".
Output
FLAG: aloha

Task 7 — XOR Key Derivation (Associativity)
Challenge
Given KEY1, (KEY2 XOR KEY1), and (KEY2 XOR KEY3), recover (KEY1 XOR KEY2 XOR KEY3) and use it to decrypt FINAL.
Code
from Crypto.Util.number import long_to_bytes  KEY1           = int("a6c8b6733c9b22de7bc0253266a3867df55acde8635e19c73313", 16) KEY2_XOR_KEY1  = int("37dcb292030faa90d07eec17e3b1c6d8daf94c35d4c9191a5e1e", 16) KEY2_XOR_KEY3  = int("c1545756687e7573db23aa1c3452a098b71a7fbf0fddddde5fc1", 16) FINAL          = int("04ee9855208a2cd59091d04767ae47963170d1660df7f56f5faf", 16)  FLAG = FINAL ^ KEY1 ^ KEY2_XOR_KEY3 print(long_to_bytes(FLAG).decode())

Explanation
XOR is associative and commutative: (A XOR B) XOR A = B. We need to find KEY3 to decrypt FINAL. Using the given values:
•	KEY2 = KEY2_XOR_KEY1 XOR KEY1
•	KEY3 = KEY2_XOR_KEY3 XOR KEY2 = KEY2_XOR_KEY3 XOR KEY2_XOR_KEY1 XOR KEY1
•	FINAL XOR KEY1 XOR KEY2 XOR KEY3 = plaintext
Rather than computing each key separately, we exploit associativity to collapse the XOR chain: FINAL ^ KEY1 ^ KEY2_XOR_KEY3 directly gives the flag.
Flag
FLAG: crypto{x0r_i5_ass0c1at1v3}

Task 8 — Single-Byte XOR Brute-Force
Challenge
A hex-encoded ciphertext is provided. It was XORed with a single unknown byte (0–255). Find the key and recover the plaintext.
Code
F1 = "73626960647f6b206821204f21254f7d694f7624662065622127234f726927756d" key1 = bytes.fromhex(F1)  for key in range(256):     result = ""     for byte in key1:         result += chr(byte ^ key)     if "crypto" in result:         print(result)

Explanation
Since the key space for a single-byte XOR is only 256 values (0–255), a brute-force attack is trivial. Each candidate key is XORed with every byte of the ciphertext. If the resulting string contains the known plaintext substring "crypto", we have found the correct key. This leverages known-plaintext analysis — a common technique where partial knowledge of the plaintext helps narrow the search. The winning key was 0x11 (decimal 17).
Flag
FLAG: crypto{0x10_15_my_f4v0ur173_by7e}

Task 9 — Repeating-Key XOR Decryption
Challenge
A hex ciphertext was encrypted with a repeating XOR key. A hint suggests the key starts with "crypto{". Recover the key and decrypt the full message.
Step 1 — Recover the Key
F1 = "0e0b213f26041e480b26217f27342e175d0e070a3c5b103e2526217f27342e175d0e077e263451150104" data = bytes.fromhex(F1) known = b"crypto{"  key = b"" for i in range(len(known)):     key += bytes([data[i] ^ known[i]]) print(key.decode())   # Output: myXORke

Explanation (Step 1)
Since we know the flag starts with "crypto{", we XOR the first 7 bytes of the ciphertext with the first 7 bytes of the known plaintext. This directly reveals the first 7 bytes of the key: "myXORke". Assuming the key is "myXORkey" (8 characters — a natural English word + common suffix), we proceed to full decryption.
Step 2 — Decrypt with Full Key
data = bytes.fromhex(F1) key = b"myXORkey"  flag = "" for i in range(len(data)):     flag += chr(data[i] ^ key[i % len(key)]) print(flag)

Explanation (Step 2)
Repeating-key XOR cycles the key across the entire plaintext using modular indexing (i % len(key)). Once we assumed the full key is "myXORkey", we iterate over the ciphertext bytes and XOR each with the corresponding key byte (wrapping around). This yields the complete flag.
Flag
FLAG: crypto{1f_y0u_Kn0w_En0uGH_y0u_Kn0w_1t_4ll}


Summary Table
Task	Topic	Key Technique	Flag
1	Python XOR	chr(o ^ 0x32)	z3n_0f_pyth0n
2	ASCII Lookup	chr(decimal)	ASCII_pr1nt4bl3
3	Hex Decode	bytes.fromhex()	You_will_be_working...
4	Hex→Base64	base64.b64encode()	Base+64+Encoding...
5	Int→Bytes	long_to_bytes()	3nc0d1n6_4ll_7h3...
6	XOR with 13	chr(ord(ch) ^ 13)	aloha
7	XOR Associativity	A^B^A = B	x0r_i5_ass0c1at1v3
8	Single-Byte Brute	Iterate 0–255	0x10_15_my_f4v0ur173...
9	Repeating-Key XOR	Known-plaintext + modular key	1f_y0u_Kn0w...


