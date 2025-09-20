---
title: '[CRYPTO - PICOCTF] - Easy Level'

---

**MỤC LỤC:**

[TOC]
# Mod 26
**Description**
Cryptography can be easy, do you know what ROT13 is? `cvpbPGS{arkg_gvzr_V'yy_gel_2_ebhaqf_bs_ebg13_GYpXOHqX}`
**Cách làm:**
- **ROT13** là một thuật toán mã hóa rất đơn giản trong mật mã học.
- **Cách hoạt động**: mỗi chữ cái trong bảng chữ cái Latin được thay thế bằng chữ cái cách nó 13 vị trí.
    - **Ví dụ:** A → N, B → O, C → P, …, M → Z.
- Vì bảng chữ cái có **26 chữ**, nên áp dụng ROT13 hai lần sẽ quay lại văn bản ban đầu.
![image](https://hackmd.io/_uploads/SJWugsa5gx.png)

# The Numbers
Description
The [numbers](https://jupiter.challenges.picoctf.org/static/f209a32253affb6f547a585649ba4fda/the_numbers.png)... what do they mean?
**Cách làm:**
- A1Z26 cipher: Đây là cách mã hóa cực kỳ cơ bản: mỗi chữ cái trong bảng chữ cái tiếng Anh được thay bằng số tương ứng với vị trí của nó.
```
A = 1
B = 2

…

Z = 26

3 1 20 → C A T (CAT).
```
- Giải mã: 16 = P, 9 = I, 3 = C, 15 = O, 3 = C, 20 = T, 6 = F, suy ra ta có: PICOCTF
- Làm tương tự ta được flag là `PICOCTF{THENUMBERSMASON}`

# hashcrack
**Description**
A company stored a secret message on a server which got breached due to the admin using weakly hashed passwords. Can you gain access to the secret stored within the server?

**Cách làm:**
- MD5 (Message-Digest Algorithm)
    - Độ dài bit: 128 bit
    - Độ dài hex: 32 ký tự
    - Ví dụ:`5d41402abc4b2a76b9719d911017c592`
- SHA-1 (Secure Hash Algorithm 1)
    - Độ dài bit: 160 bit
    - Độ dài hex: 40 ký tự
    - Ví dụ: `aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d`
- SHA-224 (SHA-2 family)
    - Độ dài bit: 224 bit
    - Độ dài hex: 56 ký tự
    - Ví dụ: `d14a028c2a3a2bc9476102bb288234c415a2b01f828ea62ac5b3e42f`
- SHA-256 (SHA-2 family)
    - Độ dài bit: 256 bit
    - Độ dài hex: 64 ký tự
    - Ví dụ: `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855`
- Ghi chú:
    - Các hash được biểu diễn dưới dạng hex (hệ cơ số 16) nên mỗi byte (8 bit) sẽ được biểu diễn bằng 2 ký tự hex.
    - Độ dài có thể giúp nhận diện nhanh loại hash khi gặp phải trong các bài CTF hoặc phân tích.
- Để unhash có nhiều cách làm nhưng để cho nhanh thì ta có thể sử dụng tool có sẵn sau [HASH TOOL](https://md5hashing.net/hash)
![Screenshot 2025-09-12 235703](https://hackmd.io/_uploads/Hk-GbwGoel.png)
- Dựa vào độ dài thì ta có thể phân loại từng loại hash theo thứ tự như sau: md5 - SHA1 - SHA256
![image](https://hackmd.io/_uploads/SkIgNvMixe.png)
- Flag tìm được: `picoCTF{UseStr0nG_h@shEs_&PaSswDs!_4c95d69f}`

# interencdec
**Description**
Can you get the real meaning from this file.
Download the file [here](https://artifacts.picoctf.net/c_titan/111/enc_flag).
![image](https://hackmd.io/_uploads/SJKCyuzjxe.png)
- Sau khi decode mã đề cho thì ta lại nhận được văn bản bị mã hóa bởi Caesar (shift = 7 do ta biết dạng flag là `picoCTF{}`)
![image](https://hackmd.io/_uploads/rJccW_zsle.png)
- Flag: `picoCTF{caEsar_d3cr9pt3d_890d2379}`

# Even RSA Can Be Broken?
![image](https://hackmd.io/_uploads/H1Jwubmigg.png)
