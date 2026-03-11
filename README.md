Daily works will be posted here!!!


CryptoHack (Beginner Challenges):

Some basics used for completing the challenges:

chr converts ascii to alphabets or plain text
ord converts plain text to ascii
bytes.fromhex(x) converts hexadecimal to bytes
x.decode() converts byte stirng to string
x.encode() converts string to bytes
base64 is another encryption standard
  to get plain text which encyrpted using base64
      first convert hexadecimal text to byte string, then the byte string to base64 staring using base64.b64encode(x)
Crypto.Util.number is a module (file) inside the Python crypto library PyCryptodome.
  It contains helper functions for working with numbers in cryptography, especially for algorithms like RSA.
  use import * to import all the libraries
  long_to_bytes(n) converts integers to bytes
  bytes_to_long(n) converts bytes to integers
KEY1 = int("a6c8b6733c9b22de7bc0253266a3867df55acde8635e19c73313", 16) to convert hexadecimal to integer



Task 1 – XOR Starter
Problem

We are given a list of integers. Each integer represents a character that has been XORed with the value 0x32.
Our goal is to recover the original message.

Approach

To decrypt the message:

Iterate through each number in the list.

XOR it with 0x32.

Convert the resulting value into a character using chr().

Join all characters together to form the flag.

Code
import sys

ords = [81, 64, 75, 66, 70, 93, 73, 72, 1, 92, 109, 2, 84, 109, 66, 75, 70, 90, 2, 92, 79]

print("Here is your flag:")
print("".join(chr(o ^ 0x32) for o in ords))

Output
crypto{z3n_0f_pyth0n}

Task 2 – ASCII Values
Problem

We are given a list of ASCII values.
We need to convert them into characters to reveal the hidden flag.

Approach

Each number in the list represents the ASCII code of a character.
Using Python’s chr() function, we convert each integer into its corresponding character.

Code
a=[99, 114, 121, 112, 116, 111, 123, 65, 83, 67, 73, 73, 95, 112, 114, 49, 110, 116, 52, 98, 108, 51, 125]

for i in a:
    print(chr(i), end="")

Output
crypto{ASCII_pr1nt4bl3}

Task 3 – Hex Strings
Problem

The flag is given as a hexadecimal string.
We must convert the hex string into readable text.

Approach

Python provides bytes.fromhex() which converts a hex string into raw bytes.
Then we decode those bytes into text.

Code
a= "63727970746f7b596f755f77696c6c5f62655f776f726b696e675f776974685f6865785f737472696e67735f615f6c6f747d"

b = bytes.fromhex(a)
print(b.decode())

Output
crypto{You_will_be_working_with_hex_strings_a_lot}

Task 4 – Base64 Encoding
Problem

We are given a hex string and need to convert it into Base64.

Approach

Convert the hex string to bytes.

Encode the bytes using Base64.

Code
import base64

a="72bca9b68fc16ac7beeb8f849dca1d8a783e8acf9679bf9269f7bf"
b = bytes.fromhex(a)

print(base64.b64encode(b).decode())

Output
crypto/Base+64+Encoding+is+Web+Safe/

Task 5 – Large Integer to Bytes
Problem

A large integer is given.
We must convert it into bytes to recover the hidden message.

Approach

The Crypto.Util.number library provides long_to_bytes() which converts large integers into byte strings.

Code
from Crypto.Util.number import *

n = 11515195063862318899931685488813747395775516287289682636499965282714637259206269

message = long_to_bytes(n)
print(message.decode())

Output
crypto{3nc0d1n6_4ll_7h3_w4y_d0wn}

Task 6 – XOR with Single Character
Problem

We are given the text "label" and need to XOR each character with 13.

Approach

Convert each character to ASCII using ord().

XOR it with 13.

Convert back to a character using chr().

Code
text = "label"
result = ""

for ch in text:
    result += chr(ord(ch) ^ 13)

print(result)

Output
aloha

Task 7 – XOR Properties
Problem

Several XOR relationships between keys are given.
Using the associative property of XOR, we must recover the flag.

Approach

The XOR operation is associative:

A ^ B ^ C = A ^ (B ^ C)

Using the given values, we combine the keys to derive the flag.

Code
from Crypto.Util.number import *

KEY1 = int("a6c8b6733c9b22de7bc0253266a3867df55acde8635e19c73313", 16)
KEY2_XOR_KEY1 = int("37dcb292030faa90d07eec17e3b1c6d8daf94c35d4c9191a5e1e", 16)
KEY2_XOR_KEY3 = int("c1545756687e7573db23aa1c3452a098b71a7fbf0fddddde5fc1", 16)
FINAL = int("04ee9855208a2cd59091d04767ae47963170d1660df7f56f5faf", 16)

FLAG = FINAL ^ KEY1 ^ KEY2_XOR_KEY3

print(long_to_bytes(FLAG).decode())

Output
crypto{x0r_i5_ass0c1at1v3}

Task 8 – Brute Force Single Byte XOR
Problem

The ciphertext is XOR encrypted with a single byte key.
We must brute-force all possible keys (0–255) to find readable text.

Approach

Convert the hex ciphertext into bytes.

XOR each byte with every possible key from 0–255.

Check which output contains "crypto".

Code
F1 = "73626960647f6b206821204f21254f7d694f7624662065622127234f726927756d"

key1 = bytes.fromhex(F1)

for key in range(256):

    result = ""

    for byte in key1:
        result += chr(byte ^ key)

    if "crypto" in result:
        print (result)

Output
crypto{0x10_15_my_f4v0ur173_by7e}

Task 9 – Repeating Key XOR
Problem

We are given an XOR encrypted message and the flag format crypto{}.
Using this known plaintext, we recover the key and decrypt the message.

Step 1 – Recover the Key
F1 = "0e0b213f26041e480b26217f27342e175d0e070a3c5b103e2526217f27342e175d0e077e263451150104"

data = bytes.fromhex(F1)
known = b"crypto{"

key = b""
for i in range(len(known)):
    key += bytes([data[i] ^ known[i]])

print(key.decode())

Recovered key:

myXORkey

Step 2 – Decrypt the Ciphertext
F1 = "0e0b213f26041e480b26217f27342e175d0e070a3c5b103e2526217f27342e175d0e077e263451150104"

data = bytes.fromhex(F1)

key = b"myXORkey"

flag = ""
for i in range(len(data)):
    flag += chr(data[i] ^ key[i % len(key)])

print(flag)

Output
crypto{1f_y0u_Kn0w_En0uGH_y0u_Kn0w_1t_4ll}
