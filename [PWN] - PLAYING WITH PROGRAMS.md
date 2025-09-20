---
title: '[PWN] - PLAYING WITH PROGRAMS'

---

[toc]
# Dealing with Data
## What's the password?
![image](https://hackmd.io/_uploads/B1QXe92F1g.png)
## ...and again!
![image](https://hackmd.io/_uploads/BJoogq2tJl.png)
## Newline Troubles
- If you need to terminate input in a terminal without pressing Enter, here are some methods:
- Use EOF (End of File) Shortcut: 
    - Linux/macOS: Press Ctrl + D
    - Windows (Git Bash, WSL, or PowerShell): Press Ctrl + Z, then Enter
- You can redirect the output of `echo` to the stdin of /challenge/runme using **a pipe (|)**. The key is to use `-n` with echo to **disable the trailing newline.**
- To redirect a file's content to the stdin of `/challenge/runme`, use the following command: `/challenge/runme < file.txt`

![image](https://hackmd.io/_uploads/rkgUzq2F1e.png)

## Reasoning about files
```python=
import sys


try:
    entered_password = open("lpoz", "rb").read()
except FileNotFoundError:
    print("Input file not found...")
    sys.exit(1)
if b"\n" in entered_password:
    print("Password has newlines /")
    print("Editors add them sometimes /")
    print("Learn to remove them.")

correct_password = b"joqhiapd"

print(f"Read {len(entered_password)} bytes.")


if entered_password == correct_password:
    print("Congrats! Here is your flag:")
    print(open("/flag").read().strip())
else:
    print("Incorrect!")
    sys.exit(1)
```
```python=
try:
    entered_password = open("lpoz", "rb").read()
except FileNotFoundError:
    print("Input file not found...")
    sys.exit(1)
```
- Thử mở tệp tin có tên `lpoz` trong chế độ đọc nhị phân (`rb`).
- Nếu file `lpoz` tồn tại, nó sẽ được đọc hoàn toàn vào biến `entered_password` dưới dạng bytes.

## Specifying Filenames
```python=
import sys


try:
    entered_password = open(sys.argv[1], "rb").read()
except FileNotFoundError:
    print("Input file not found...")
    sys.exit(1)
if b"\n" in entered_password:
    print("Password has newlines /")
    print("Editors add them sometimes /")
    print("Learn to remove them.")

correct_password = b"qxubcqzf"

print(f"Read {len(entered_password)} bytes.")


if entered_password == correct_password:
    print("Congrats! Here is your flag:")
    print(open("/flag").read().strip())
else:
    print("Incorrect!")
    sys.exit(1)
```
```python=
try:
    entered_password = open(sys.argv[1], "rb").read()
except FileNotFoundError:
    print("Input file not found...")
    sys.exit(1)
```
- `sys.argv[1]`: Lấy tham số dòng lệnh thứ nhất làm tên file chứa mật khẩu.
- `open(sys.argv[1], "rb")`: Mở file trong chế độ đọc nhị phân (`rb`), đảm bảo rằng nó đọc chính xác dữ liệu, kể cả các ký tự không hiển thị như `\n` hoặc `\r`.
- `read()`: Đọc toàn bộ nội dung file vào biến `entered_password` dưới dạng chuỗi bytes (b"...").

![image](https://hackmd.io/_uploads/rk0jG6nF1g.png)
## Binary and Hex Encoding
- Cách Python biểu diễn các hệ số khác nhau
Python cho phép sử dụng các hệ số khác nhau như sau:
    - Thập phân (Decimal): Viết bình thường → 11
    - Nhị phân (Binary): Thêm 0b phía trước → 0b1011
    - Thập lục phân (Hexadecimal): Thêm 0x phía trước → 0xb
- Biểu diễn số trong dạng bytes
    - Trong Python, khi làm việc với bytes, ta dùng escape sequences.
    - Escape sequence bắt đầu bằng \x, theo sau là hai chữ số hex.
```python=
byte_data = b"\x11"  # Một byte có giá trị 0x11 (17 trong thập phân)
print(byte_data)      # Output: b'\x11'
print(int.from_bytes(byte_data, "big"))  # Output: 17
```
```python=
byte_data = b"\x48\x65\x6c\x6c\x6f"  # "Hello" trong ASCII
print(byte_data)  # Output: b'Hello'
```
```python=
import sys


print("Enter the password:")
entered_password = sys.stdin.buffer.read1()
correct_password = b"\xd6"

print(f"Read {len(entered_password)} bytes.")


entered_password = bytes.fromhex(entered_password.decode("l1"))


if entered_password == correct_password:
    print("Congrats! Here is your flag:")
    print(open("/flag").read().strip())
else:
    print("Incorrect!")
    sys.exit(1)
```
- `entered_password = bytes.fromhex(entered_password.decode("l1"))`: Chuyển đổi chuỗi hex thành bytes.

![image](https://hackmd.io/_uploads/r194zC2KJx.png)

## More Hex
![image](https://hackmd.io/_uploads/Skez40hYkg.png)
## Decoding Hex
- Cách gửi dữ liệu nhị phân thô (raw binary data) vào stdin của chương trình. 
-  **Cách 1: Viết script Python để xuất dữ liệu ra stdout và chuyển vào stdin của chương trình**
```python=
import sys
sys.stdout.buffer.write(b"\x96\x8a\xf5\x82\xba\xa6\xdb\xad")
```
- Sau đó chạy: `python script.py | ./challenge`
    - Cách này giúp đảm bảo rằng dữ liệu nhị phân không bị thay đổi bởi terminal hoặc trình chỉnh sửa văn bản.

- **Cách 2: Dùng Python để chạy và tương tác trực tiếp với chương trình**

```python=
from pwn import *
# Khởi chạy chương trình
p = process("/challenge/runme")

# Gửi dữ liệu nhị phân vào stdin của chương trình
p.write(b"\x96\x8a\xf5\x82\xba\xa6\xdb\xad")

# Đọc toàn bộ phản hồi từ chương trình
print(p.readall().decode())
```
- Cách này hữu ích khi bạn cần tự động hóa quá trình khai thác.

- **Cách 3: Dùng lệnh echo với chuỗi escape để in bytes ra stdout**
    - `echo` thường dùng để in chuỗi, nhưng với `-e`, nó sẽ hiểu các chuỗi escape (\xXX).
    - `-n` giúp loại bỏ ký tự xuống dòng (\n) thường được thêm vào cuối.
    - Kết hợp với |, bạn có thể gửi bytes thô vào stdin của chương trình.
`echo -e -n "\x96\x8a\xf5\x82\xba\xa6\xdb\xad" | ./challenge`

```python=
import sys


print("Enter the password:")
entered_password = sys.stdin.buffer.read1()
correct_password = b"c994be86fdbfc5a5"

print(f"Read {len(entered_password)} bytes.")


correct_password = bytes.fromhex(correct_password.decode("l1"))


if entered_password == correct_password:
    print("Congrats! Here is your flag:")
    print(open("/flag").read().strip())
else:
    print("Incorrect!")
    sys.exit(1)
```
- `correct_password = bytes.fromhex(correct_password.decode("l1"))`: Chuyển đổi chuỗi hex thành bytes.
- `c994be86fdbfc5a5`là một chuỗi hex, nên khi dùng bytes.fromhex(), nó sẽ chuyển thành `b'\xc9\x94\xbe\x86\xfd\xbf\xc5\xa5'`.

```python=
correct_password = b"c994be86fdbfc5a5"

correct_password = bytes.fromhex(correct_password.decode("l1"))
print(correct_password)
```

![image](https://hackmd.io/_uploads/Bk2sHC3tkl.png)
## Decoding Practices
```python=
import sys


def decode_from_bits(s):
    s = s.decode("latin1")
    assert set(s) <= {"0", "1"}, "non-binary characters found in bitstream!"
    assert len(s) % 8 == 0, "must enter data in complete bytes (each byte is 8 bits)"
    return int.to_bytes(int(s, 2), length=len(s) // 8, byteorder="big")


print("Enter the password:")
entered_password = sys.stdin.buffer.read1()
correct_password = b"1001011010001010111101011000001010111010101001101101101011101101"

print(f"Read {len(entered_password)} bytes.")


correct_password = decode_from_bits(correct_password)


if entered_password == correct_password:
    print("Congrats! Here is your flag:")
    print(open("/flag").read().strip())
else:
    print("Incorrect!")
    sys.exit(1)
```
- Hàm `decode_from_bits(s)`: Chuyển đổi một chuỗi bit (dạng '0' và '1') thành bytes.
```python=
def decode_from_bits(s):
    s = s.decode("latin1")  # Giải mã bytes thành chuỗi ký tự (str)
    assert set(s) <= {"0", "1"}, "non-binary characters found in bitstream!"
    assert len(s) % 8 == 0, "must enter data in complete bytes (each byte is 8 bits)"
    return int.to_bytes(int(s, 2), length=len(s) // 8, byteorder="big")
```
- `s = s.decode("latin1")`
    - s ban đầu là bytes (vì được truyền từ sys.stdin.buffer.read1()).
    - decode("latin1") giúp chuyển đổi nó thành chuỗi.
    - Lưu ý: "latin1" đảm bảo mỗi byte ánh xạ trực tiếp sang một ký tự mà không bị lỗi.
    - Ví dụ: `b"101010".decode("latin1")  # Trả về "101010"`
- `assert set(s) <= {"0", "1"}, "non-binary characters found in bitstream!"`
    - Kiểm tra tất cả ký tự trong s phải là '0' hoặc '1'.
    - Nếu có ký tự khác (ví dụ: "12345"), chương trình sẽ báo lỗi.
- `assert len(s) % 8 == 0, "must enter data in complete bytes (each byte is 8 bits)"`
    - Mỗi byte có 8 bit, nên độ dài chuỗi phải chia hết cho 8.
- `return int.to_bytes(int(s, 2), length=len(s) // 8, byteorder="big")`
    - `int(s, 2)`: Chuyển đổi chuỗi bit thành số nguyên (int).
    - `int.to_bytes(...)`: Chuyển số nguyên thành bytes.
    - `length=len(s) // 8`: Số byte cần có.
    - `byteorder="big"`: Big-endian, tức là byte có trọng số cao nằm trước.
Ví dụ:
```python=
decode_from_bits(b"0000000100000010")  
# Chuyển "0000000100000010" (binary) → 258 (decimal) → b'\x01\x02'
```
![image](https://hackmd.io/_uploads/BJhA0J6Fke.png)

## Encoding Practice
```python=
import sys


def decode_from_bits(s):
    s = s.decode("latin1")
    assert set(s) <= {"0", "1"}, "non-binary characters found in bitstream!"
    assert len(s) % 8 == 0, "must enter data in complete bytes (each byte is 8 bits)"
    return int.to_bytes(int(s, 2), length=len(s) // 8, byteorder="big")


print("Enter the password:")
entered_password = sys.stdin.buffer.read1()
correct_password = b"\xf5\xbe\xa1\xf5\x82\x83\x85\xf6"

print(f"Read {len(entered_password)} bytes.")


entered_password = decode_from_bits(entered_password)


if entered_password == correct_password:
    print("Congrats! Here is your flag:")
    print(open("/flag").read().strip())
else:
    print("Incorrect!")
    sys.exit(1)
```
- Code:
```python=
def bytes_to_binary(data):
    return ''.join(f'{byte:08b}' for byte in data)

password = b"\xf5\xbe\xa1\xf5\x82\x83\x85\xf6"
binary_password = bytes_to_binary(password)
print(binary_password)
```
- Output: `1111010110111110101000011111010110000010100000111000010111110110`

![image](https://hackmd.io/_uploads/HJGSHeaKJl.png)
## Hex-encoding ASCII
```python=
import sys


try:
    entered_password = open("xpln", "rb").read()
except FileNotFoundError:
    print("Input file not found...")
    sys.exit(1)
correct_password = b"lmuxtxjy"

print(f"Read {len(entered_password)} bytes.")


entered_password = bytes.fromhex(entered_password.decode("l1"))


if entered_password == correct_password:
    print("Congrats! Here is your flag:")
    print(open("/flag").read().strip())
else:
    print("Incorrect!")
    sys.exit(1)
```

- Giả sử `xpln` chứa dữ liệu sau:
`68656c6c6f`
- Khi chương trình đọc `xpln`, nội dung `entered_password` sẽ là: `b'68656c6c6f'`
- Sau bước `decode("l1")`, chuỗi được chuyển thành: **"68656c6c6f"**.
- Sau `bytes.fromhex(...)`, nó thành dạng byte: `b"hello"`.
- Chuyển từ string sang hex:
    - `hello` được chuyển thành dạng bytes bằng `.encode()`.
    - Sau đó `.hex()` chuyển từng byte thành dạng hexadecimal.
```python=
text = "hello"
hex_value = text.encode().hex()
print(hex_value)  # Output: 68656c6c6f
```

- Chuyển từ hex ngược lại string:
    - `bytes.fromhex()` chuyển hex thành bytes
    - `.decode()` chuyển bytes thành chuỗi ký tự ban đầu.
```python=
hex_string = "68656c6c6f"
text = bytes.fromhex(hex_string).decode()
print(text)  # Output: hello
```
- Code:
```python=
text = "lmuxtxjy"
hex_value = text.encode().hex()
print(hex_value)
```
- Ouput: `6c6d757874786a79`

![image](https://hackmd.io/_uploads/Hy9kZ0aKJg.png)
## Nested Encoding
```python=
import sys


try:
    entered_password = open(sys.argv[1], "rb").read()
except FileNotFoundError:
    print("Input file not found...")
    sys.exit(1)
correct_password = b"hnupggrr"

print(f"Read {len(entered_password)} bytes.")


entered_password = bytes.fromhex(entered_password.decode("l1"))
entered_password = bytes.fromhex(entered_password.decode("l1"))
entered_password = bytes.fromhex(entered_password.decode("l1"))
entered_password = bytes.fromhex(entered_password.decode("l1"))

if entered_password == correct_password:
    print("Congrats! Here is your flag:")
    print(open("/flag").read().strip())
else:
    print("Incorrect!")
    sys.exit(1)
```
- Code:
```python=
password = "hnupggrr"
for i in range(4):
    password = password.encode().hex()

print(password)
```
- Output:
`33333336333333383333333633363335333333373333333533333337333333303333333633333337333333363333333733333337333333323333333733333332`
![image](https://hackmd.io/_uploads/Byvdr0ptye.png)

## Hex-encoding UTF-8
```python=
import sys


try:
    entered_password = open(sys.argv[1], "rb").read()
except FileNotFoundError:
    print("Input file not found...")
    sys.exit(1)
correct_password = "📣 🎮 🐸 🆖".encode("utf-8")

print(f"Read {len(entered_password)} bytes.")


entered_password = bytes.fromhex(entered_password.decode("l1"))


if entered_password == correct_password:
    print("Congrats! Here is your flag:")
    print(open("/flag").read().strip())
else:
    print("Incorrect!")
    sys.exit(1)
```
- Code:
```python=
correct_password = "📣 🎮 🐸 🆖".encode("utf-8")
print(correct_password)

password = correct_password.hex()
print(password)
```
- Output:
```
b'\xf0\x9f\x93\xa3 \xf0\x9f\x8e\xae \xf0\x9f\x90\xb8 \xf0\x9f\x86\x96'
f09f93a320f09f8eae20f09f90b820f09f8696
```
![image](https://hackmd.io/_uploads/rJPvKRaF1l.png)

## UTF Mixups
```python=
import sys


try:
    entered_password = open("eovv", "rb").read()
except FileNotFoundError:
    print("Input file not found...")
    sys.exit(1)
correct_password = b"bseddhtm"

print(f"Read {len(entered_password)} bytes.")

assert entered_password != correct_password

entered_password = entered_password.decode("utf-16")
entered_password = entered_password.encode("latin1")


if entered_password == correct_password:
    print("Congrats! Here is your flag:")
    print(open("/flag").read().strip())
else:
    print("Incorrect!")
    sys.exit(1)
```
- Code:
```python=
correct_password = "bseddhtm"

password = correct_password.encode("utf-16")

print(password)
```
- Output:
`b'\xff\xfeb\x00s\x00e\x00d\x00d\x00h\x00t\x00m\x00'`

![image](https://hackmd.io/_uploads/H1ZiOgRYyg.png)
## Modifying Encoded Data
```python=
import sys


def reverse_string(s):
    return s[::-1]


print("Enter the password:")
entered_password = sys.stdin.buffer.read1()
correct_password = b"\x81\xa7[t\xc4;\xc0!"

print(f"Read {len(entered_password)} bytes.")


entered_password = entered_password[::-1]
entered_password = bytes.fromhex(entered_password.decode("l1"))


if entered_password == correct_password:
    print("Congrats! Here is your flag:")
    print(open("/flag").read().strip())
else:
    print("Incorrect!")
    sys.exit(1)
    
```
- Code:
```python=
correct_password = b"\x81\xa7[t\xc4;\xc0!"
entered_password = correct_password.hex()
entered_password = entered_password[::-1]

print(entered_password)
```
- Output:
`120cb34c47b57a18`

![image](https://hackmd.io/_uploads/SkK_y-AKke.png)

## Decoding Base64
```python=
import sys

import base64


print("Enter the password:")
entered_password = sys.stdin.buffer.read1()
correct_password = b"H9746gX780c="

print(f"Read {len(entered_password)} bytes.")


correct_password = base64.b64decode(correct_password.decode("l1"))


if entered_password == correct_password:
    print("Congrats! Here is your flag:")
    print(open("/flag").read().strip())
else:
    print("Incorrect!")
    sys.exit(1)
```
- Code:
```python=
import base64

correct_password = b"H9746gX780c="
correct_password = base64.b64decode(correct_password.decode("l1"))
print(correct_password)
```
- Output:
`b'\x1f\xde\xf8\xea\x05\xfb\xf3G'`

![image](https://hackmd.io/_uploads/HkvjLb0Kkl.png)
## Encoding Base64
```python=
import sys

import base64


print("Enter the password:")
entered_password = sys.stdin.buffer.read1()
correct_password = b"\xb6\x84<N\xe4\x8d\xd0\x03"

print(f"Read {len(entered_password)} bytes.")


entered_password = base64.b64decode(entered_password.decode("l1"))


if entered_password == correct_password:
    print("Congrats! Here is your flag:")
    print(open("/flag").read().strip())
else:
    print("Incorrect!")
    sys.exit(1)
```
- Code:
```python=
import base64

correct_password = b"\xb6\x84<N\xe4\x8d\xd0\x03"

entered_password = base64.b64encode(correct_password)

print(entered_password)
```
- Output: `b'toQ8TuSN0AM='`

![image](https://hackmd.io/_uploads/BJ9u_WAF1g.png)

## Dealing With Obfuscation
```python=
import sys

import base64


def encode_to_bits(s):
    return b"".join(format(c, "08b").encode("latin1") for c in s)


print("Enter the password:")
entered_password = sys.stdin.buffer.read1()
correct_password = b"F(\x9f\xdf\xe2\xc6\x9fX"

print(f"Read {len(entered_password)} bytes.")


correct_password = base64.b64encode(correct_password)
correct_password = base64.b64encode(correct_password)
correct_password = encode_to_bits(correct_password)
correct_password = correct_password.hex().encode("l1")


if entered_password == correct_password:
    print("Congrats! Here is your flag:")
    print(open("/flag").read().strip())
else:
    print("Incorrect!")
    sys.exit(1)
```
- Phân tích:
- `for c in s`
    - s là một chuỗi bytes, nghĩa là từng phần tử của s là số nguyên (int) từ 0 đến 255.
    - Khi ta lặp qua s, mỗi c sẽ là một giá trị số nguyên biểu diễn một ký tự (byte).
```python
s = b"AB"
for c in s:
    print(c)
```
- Output:
```
65
66
```
- `format(c, "08b")`
    - format(c, "08b") chuyển đổi số nguyên c thành một chuỗi nhị phân dài 8-bit.
    - "08b" có nghĩa là: 
        - "b": chuyển đổi số sang dạng nhị phân.
        - "08": đảm bảo luôn có 8 chữ số (thêm số 0 ở đầu nếu cần).
```python=
print(format(65, "08b"))  # 65 → '01000001'
print(format(66, "08b"))  # 66 → '01000010'
```
- Output:
```
01000001
01000010
```
- `.encode("latin1")`
    -  Chuỗi nhị phân "01000001" là một chuỗi string trong Python.
    -  .encode("latin1") biến nó thành một chuỗi bytes.
    -  "latin1" là một bảng mã hỗ trợ 256 ký tự (1 byte = 1 ký tự), đảm bảo rằng mỗi ký tự nhị phân sẽ thành một byte.
```python=
binary_string = "01000001"
encoded = binary_string.encode("latin1")
print(encoded)  # Output: b'01000001'
```
- `b"".join(...)`
    - `b"".join(...)` nối tất cả các bytes lại với nhau.
    - Kết quả cuối cùng là một chuỗi bytes chứa toàn bộ dữ liệu nhị phân.
```python=
s = b"AB"
result = b"".join(format(c, "08b").encode("latin1") for c in s)
print(result)
```
- Output:
`b'0100000101000010'`
- Code:
```python=
import base64


def encode_to_bits(s):
    return b"".join(format(c, "08b").encode("latin1") for c in s)

correct_password = b"F(\x9f\xdf\xe2\xc6\x9fX"

correct_password = base64.b64encode(correct_password)
correct_password = base64.b64encode(correct_password)
correct_password = encode_to_bits(correct_password)
correct_password = correct_password.hex().encode("l1")

print(correct_password)
```
- Output:
`b'3031303130313031303131303131303130313130313130303031313130303030303130313130313030313130313031303031303031313031303131313030313030313031303130303031303030313031303131303031303030313131303130313031303031313031303130313031313130313130303031313030313131303031'`

![image](https://hackmd.io/_uploads/rkQ9xfCt1g.png)

## Dealing With Obfuscation 2

```python=
import sys

import base64


def reverse_string(s):
    return s[::-1]


print("Enter the password:")
entered_password = sys.stdin.buffer.read1()
correct_password = b"\xc8\xf2#\xb4\x94@\x82N"

print(f"Read {len(entered_password)} bytes.")


entered_password = entered_password[::-1]
entered_password = base64.b64decode(entered_password.decode("l1"))
entered_password = bytes.fromhex(entered_password.decode("l1"))
entered_password = entered_password[::-1]

correct_password = correct_password[::-1]
correct_password = correct_password.hex().encode("l1")
correct_password = correct_password[::-1]
correct_password = correct_password[::-1]


if entered_password == correct_password:
    print("Congrats! Here is your flag:")
    print(open("/flag").read().strip())
else:
    print("Incorrect!")
    sys.exit(1)
```
- Code:
```python=
import base64

correct_password = b"\xc8\xf2#\xb4\x94@\x82N"
correct_password = correct_password[::-1]
correct_password = correct_password.hex().encode("l1")
print(f"Correct password: {correct_password}")

entered_password = correct_password
entered_password = entered_password[::-1]
entered_password = entered_password.hex()
entered_password = base64.b64encode(entered_password.encode())
entered_password = entered_password[::-1]
print(f"Enter password: {entered_password}")
```
- Output:
```
Correct password: b'4e824094b423f2c8'
Enter password: b'=QzM1YDOzIzM0MDMzkzM0MjM2QzMyMzMzYjNyMzM2gzM'
```
![image](https://hackmd.io/_uploads/r1wr4rCFyl.png)
# Talking Web
**Will be done in the future...**

# Program Misuse
- **Hệ thống quyền trong Linux**
    - Mỗi tiến trình (process) có một User ID (UID) và một Group ID (GID).
    - Mỗi tệp tin, thư mục được sở hữu bởi một người dùng (user) và một nhóm (group).
    - Các tiến trình con sẽ kế thừa từ tiến trình cha.
    - Một số UID có quyền cao hơn so với các UID khác.
    - UID 0 là tài khoản quản trị root, có quyền kiểm soát toàn bộ hệ thống.
    - **Ví dụ**: 
        - Nếu bạn là người dùng UID 1000, bạn không thể cài đặt phần mềm, tắt máy, hoặc chỉnh sửa file hệ thống.
        - Nhưng nếu bạn có UID 0 (root), bạn có thể làm mọi thứ trên hệ thống.
- **Privilege Escalation**:
    - Một cách để tăng quyền là sử dụng các tệp nhị phân SUID.
    - SUID (Set User ID) là một bit quyền đặc biệt trong Linux:
        - SUID: Khi chạy một tệp, tiến trình sẽ có UID của chủ sở hữu file, thay vì người chạy file.
        - SGID: Tương tự như SUID, nhưng áp dụng cho GID.
        - Sticky Bit: Giới hạn quyền xóa file trong thư mục chia sẻ.
    - Ví dụ: sudo, su, newgrp, v.v.

- eUID là gì?
    - Trong Linux có 3 loại UID/GID:
        - Real UID (rUID): Là UID thực sự của tiến trình.
        - Effective UID (eUID): Là UID được sử dụng để kiểm tra quyền truy cập.
        - Saved UID (sUID): Là UID có thể được chuyển đổi thành eUID.
    - Ví dụ:
        - rUID = 1000 (người dùng thường)
        - eUID = 0 (root, do sudo cấp quyền)
        - sUID = 1000 (có thể quay lại UID 1000 nếu cần)
- Tại sao UID 0 nguy hiểm?
    - Khi có UID 0 (root), bạn có thể:
        - Đọc tất cả tệp, bao gồm cả /proc/ và tệp thiết bị.
        - Chạy bất kỳ chương trình nào.
        - Giả lập bất kỳ UID/GID nào khác.
        - Debug bất kỳ chương trình nào.
    - Nếu một hacker chiếm được UID 0, họ có thể kiểm soát hoàn toàn hệ thống.

- Giảm thiểu tấn công thông qua `/bin/sh` (SUID Mitigation)
    - Hầu hết các lỗ hổng thực thi lệnh (command injection) thường sử dụng /bin/sh để chiếm quyền điều khiển hệ thống.
    - Cách giảm thiểu:
        - Khi /bin/sh chạy với SUID (eUID == 0 nhưng rUID ≠ 0), hệ thống sẽ tự động hạ quyền về rUID.
        - Điều này ngăn chặn hacker duy trì quyền root bằng cách mở shell từ SUID binary.
    - Cách bypass cơ chế này:
        - Dùng lệnh sh -p để giữ lại quyền SUID.
- Ví dụ: 
```
sudo chmod u+s /bin/sh
```
- Khi chạy /bin/sh, tiến trình sẽ bị hạ quyền:
```
    $ /bin/sh
    $ id
    uid=1000(user) euid=1000(user) gid=1000(user)
```

- Nhưng nếu chạy với sh -p, quyền eUID vẫn giữ nguyên:
```
$ /bin/sh -p
$ id
uid=1000(user) euid=0(root) gid=1000(user)
```
- Giảm thiểu rủi ro trong việc lạm dụng chương trình (Program Misuse)
    - Cách tiếp cận tổng quát để hạn chế lạm dụng chương trình:
    - Sandboxing: Chạy chương trình trong một môi trường biệt lập (sandbox) để giới hạn quyền truy cập vào dữ liệu nhạy cảm.

- Tóm lại: 
    - Lỗ hổng bảo mật là không thể tránh khỏi, nhưng có thể giảm thiểu thiệt hại bằng cách giới hạn quyền truy cập.
    - Các cách phổ biến:
        - Giảm quyền SUID khi mở shell
        - Tách quyền chương trình như Wireshark
        - Sử dụng sandbox để cô lập tiến trình nguy hiểm
## level1
![image](https://hackmd.io/_uploads/BJaidl1qJg.png)
- Gợi ý chương trình có thể khai thác:
    - `/bin/cat`
    - `/usr/bin/more`
    - `/usr/bin/find`
## level2
- Lệnh `more` – Xem nội dung file theo từng trang
- Chức năng: Hiển thị nội dung file theo từng trang, phù hợp với file dài.
- Cách sử dụng: `more filename.txt`
- Phím điều khiển:
    - `Space` → Chuyển sang trang tiếp theo.
    - `Enter` → Xuống từng dòng một.
    - `b` → Quay lại trang trước (chỉ có trong less, không có trong more).
    - `q` → Thoát.
- Nhược điểm: more chỉ cho phép di chuyển xuống, không thể cuộn lên.
- Ví dụ:
`more /etc/passwd`
- Hiển thị nội dung file /etc/passwd từng trang một.

![image](https://hackmd.io/_uploads/H1QXG-Jqyx.png)
## level3
- Lệnh `less` – Xem nội dung file linh hoạt hơn more
- Chức năng: Giống more, nhưng có thể cuộn lên và xuống, tìm kiếm nội dung.
- Cách sử dụng:
`less filename.txt`
- Phím điều khiển:
    - `Space` → Trang tiếp theo.
    - `b` → Quay lại trang trước.
    - `/từ_khóa` → Tìm kiếm từ khóa trong file.
    - `n` → Đến kết quả tìm kiếm tiếp theo.
    - `q` → Thoát.
- Ví dụ: `less /var/log/syslog`
- Xem log hệ thống một cách linh hoạt.
- Tại sao nên dùng less thay vì more?
    - less nhanh hơn vì nó không tải toàn bộ file vào bộ nhớ.
    - Có thể cuộn lên/xuống, tìm kiếm nội dung trong file.

![image](https://hackmd.io/_uploads/Hy1FQbkqkl.png)
![image](https://hackmd.io/_uploads/B1zv7ZJqJx.png)
## level4
- Lệnh `tail` – Hiển thị dòng cuối của file
- Chức năng: Hiển thị n dòng cuối cùng của file.
- Cú pháp:
`tail filename.txt`
- Mặc định, `tail` hiển thị 10 dòng cuối.
- Tùy chọn phổ biến:
    - `-n <số_dòng> `→ Chỉ định số dòng cuối cần hiển thị.
    - `-f `→ Theo dõi thay đổi trong file (hữu ích khi xem log).
    -` -c <số_byte>` → Hiển thị số byte cuối.
![image](https://hackmd.io/_uploads/SkENVW1qJx.png)
## level5
- Lệnh `head` – Hiển thị dòng đầu của file
- Chức năng: Hiển thị n dòng đầu tiên của một file.
- Cú pháp:
`head filename.txt`
- Mặc định,` head `hiển thị 10 dòng đầu tiên.
- Tùy chọn phổ biến:
    - `-n <số_dòng> `→ Chỉ định số dòng cần hiển thị.
    - `-c <số_byte>` → Hiển thị số byte đầu tiên.
- Ví dụ:
`head -5 /var/log/syslog`
- Hiển thị 5 dòng đầu tiên của file log hệ thống.

![image](https://hackmd.io/_uploads/B1PKNWJ51x.png)
## level6
- Lệnh `sort `– Sắp xếp dữ liệu trong file
- Chức năng: Sắp xếp dòng của file theo thứ tự tăng dần (mặc định).
- Cú pháp:
`sort filename.txt`
- Mặc định, sort sắp xếp theo thứ tự bảng chữ cái.
- Tùy chọn phổ biến:
    -` -r` → Sắp xếp giảm dần.
    - `-n` → Sắp xếp theo giá trị số.
    - `-k` <cột> → Sắp xếp theo một cột cụ thể.
    - `-u `→ Loại bỏ dòng trùng lặp.
- Ví dụ:
`sort -n scores.txt`
- Sắp xếp file scores.txt theo giá trị số.
`sort -k 2 employees.txt`
- Sắp xếp file employees.txt theo cột thứ 2.

![image](https://hackmd.io/_uploads/r1QREWJqJx.png)
## level7
- Lệnh vim – Trình soạn thảo văn bản nâng cao
- Chức năng: Chỉnh sửa file với nhiều tính năng mạnh mẽ.
- Cách sử dụng:
`vim filename.txt`
- Chế độ trong vim:
    - Normal mode (mặc định): Di chuyển, sao chép, xóa.
    - Insert mode (i hoặc a để vào): Gõ nội dung vào file.
    - Command mode (: để vào): Thực hiện lệnh như lưu, thoát, tìm kiếm.
- Lệnh phổ biến trong vim:
    - `i` → Chuyển sang chế độ nhập.
    - `Esc` → Thoát chế độ nhập.
    - `:w` → Lưu file.
    - `:q `→ Thoát.
    - `:wq` hoặc `ZZ` → Lưu và thoát.
    - `/từ_khóa` → Tìm kiếm từ khóa.
    
![image](https://hackmd.io/_uploads/S1iNnzy9ke.png)
![image](https://hackmd.io/_uploads/S1bX2Gyqkg.png)
## level8
- Lệnh emacs – Trình soạn thảo văn bản mạnh mẽ
- Chức năng: Chỉnh sửa file như vim, nhưng có giao diện thân thiện hơn.
- Cách sử dụng:
`emacs filename.txt`
- Lệnh phổ biến trong emacs:
    - `Ctrl + x, Ctrl + s` → Lưu file.
    - `Ctrl + x, Ctrl + c` → Thoát.
    - `Ctrl + s` → Tìm kiếm.
    - `Ctrl + g` → Hủy lệnh.

![image](https://hackmd.io/_uploads/Sy162Gk91x.png)
![image](https://hackmd.io/_uploads/rkrjhGk9ye.png)

## level9
![image](https://hackmd.io/_uploads/Bklsem19kl.png)
![image](https://hackmd.io/_uploads/ryqFgXkqkg.png)
## level10
- Lệnh `rev` trong Linux – Đảo ngược chuỗi ký tự
- Lệnh `rev` trong Linux dùng để đảo ngược thứ tự ký tự trên mỗi dòng của văn bản.
- Cú pháp:
`rev filename.txt`
- Ví dụ: Giả sử file `example.txt` có nội dung sau:
```
hello
world
Linux
```
- Chạy lệnh:
`rev example.txt`
- Kết quả:
```
olleh
dlrow
xuniL
```
- Chỉ thứ tự ký tự bị đảo ngược, không phải thứ tự dòng!
- Dùng `rev` với `echo` (nhập trực tiếp)
- Bạn có thể dùng rev với echo để đảo ngược một chuỗi trực tiếp:
`echo "hello world" | rev`
- Kết quả:
`dlrow olleh`

![image](https://hackmd.io/_uploads/r1VEZXJcye.png)
## level11
**Lệnh `od` trong Linux – Hiển thị nội dung file dưới dạng mã nhị phân, bát phân, thập lục phân**
- Lệnh `od` (octal dump) được sử dụng để hiển thị nội dung của một file ở dạng **bát phân (octal), thập lục phân (hex), nhị phân (binary), ASCII, hoặc các định dạng khác**.
- Cách sử dụng cơ bản
- Cú pháp:
`od filename`
- Mặc định, `od` hiển thị file ở hệ bát phân (octal).

- **Hiển thị nội dung file theo bát phân (octal)**
```
echo "Hello" > example.txt
od example.txt
```
- Kết quả:
`0000000 110 145 154 154 157 012`
`0000006`
    - Các số `110 145 154 154 157` là giá trị bát phân của ký tự `"Hello"`.
    - `012` là ký tự xuống dòng (\n).
    - `0000000` là địa chỉ byte đầu tiên trong file.
    
- **Hiển thị dưới dạng hệ thập lục phân (hex)**
`od -x example.txt`
- Kết quả:
`0000000 6548 6c6c 0a6f`
`0000006`
    - `6548` là mã hex của `"He"`, `6c6c` là `"ll"`, `0a6f` là `"\no"`.
    
- **Hiển thị dưới dạng ký tự ASCII**
`od -c example.txt`
- Kết quả:
`0000000   H   e   l   l   o  \n`
`0000006`
    - Hiển thị các ký tự có thể đọc thay vì mã số.
    
-  **Hiển thị dưới dạng số thập phân**
`od -d example.txt`
- Kết quả:
`0000000 25928 27756  2671`
`0000006`
    - Hiển thị mã ASCII dạng decimal.
    
- **Hiển thị dưới dạng nhị phân (binary)**
`od -b example.txt`
- Kết quả:
```
0000000 110 145 154 154 157 012
0000006
```
- Mặc định `-b` vẫn hiển thị bát phân. Muốn nhị phân thực sự, cần dùng `xxd`:
`xxd -b example.txt`
- Kết quả:
`00000000: 01001000 01100101 01101100 01101100 01101111 00001010  Hello.`
- Code:
```python=
strings =  "p   w   n   .   c   o   l   l   e   g   e   {   Y   z   l   MT   V   0   p   Q   3   5   t   6   a   a   0   h   _   Y   kh   M   w   1   P   2   4   .   0   1   M   1   E   D   L   5U   T   O   4   c   z   W }"
res = strings.replace(" ", "")
print(res)
```
![image](https://hackmd.io/_uploads/H1hnG7Jq1l.png)

## level12
- Lệnh `hd` (hexdump) trong Linux dùng để **hiển thị nội dung của một tệp hoặc luồng dữ liệu dưới dạng hexadecimal**. 
- Nó rất hữu ích khi bạn muốn kiểm tra dữ liệu nhị phân của một tệp, đặc biệt trong lĩnh vực reverse engineering, forensics, hoặc debugging file formats.
- Cú pháp:
`hd [tùy_chọn] [tệp]`
- Các tùy chọn quan trọng:
    - `-n <số_byte>`: Chỉ đọc một số byte nhất định từ tệp.
    - `-C`: Hiển thị output ở dạng giống xxd, với cả ký tự ASCII.
    - `-s <offset>`: Bắt đầu hiển thị từ vị trí offset chỉ định.
    - `-v`: Hiển thị đầy đủ (mặc định hd sẽ rút gọn các dòng trùng nhau).
    - `-b`: Hiển thị các byte dưới dạng octal (cơ số 8).
    - `-o`: Hiển thị các byte dưới dạng octal (giống -b, nhưng ở định dạng khác).
    - `-d`: Hiển thị dữ liệu dưới dạng decimal (cơ số 10).
    
![image](https://hackmd.io/_uploads/rJNoNVb91g.png)

## level13
- Lệnh `xxd` trong Linux là một công cụ mạnh mẽ dùng để hiển thị nội dung của tệp dưới dạng hexadecimal (thập lục phân), đồng thời có thể chuyển đổi từ hex về dạng nhị phân. 
- Cú pháp cơ bản
`xxd [tùy_chọn] [tệp]`
- Các tùy chọn quan trọng
    - `-p`: Chỉ hiển thị giá trị hex (plain hex dump, không có offset).
    - `-c <số_byte>`: Số byte hiển thị trên mỗi dòng (mặc định 16).
    - `-l <số_byte>`: Chỉ hiển thị số byte chỉ định.
    - `-s <offset>`: Bắt đầu từ byte offset cụ thể.
    - `-r`: Chuyển hex quay trở lại dạng nhị phân.
    - `-g <n>`: Nhóm các byte theo n byte.
    - `-u`: Hiển thị hex dưới dạng chữ in hoa.
    - `-e`: Đảo ngược thứ tự byte (endianness).

| Tính năng                 | xxd | hd           |
| ------------------------- | --- | ------------ |
| Hiển thị hex              | ✅  | ✅           |
| Hiển thị ASCII            | ✅  | ✅ (dùng -c) |
| Chuyển hex về binary (-r) | ✅  | ❌           |
- Nếu bạn cần biến đổi hex trở lại binary, xxd là lựa chọn tốt hơn hd.

![image](https://hackmd.io/_uploads/HykMq4Z51x.png)
## level14
Lệnh base32 trong Linux dùng để mã hóa và giải mã dữ liệu theo chuẩn Base32, một phương pháp mã hóa dữ liệu nhị phân thành ký tự ASCII an toàn để truyền tải. Base32 sử dụng 32 ký tự trong bảng chữ cái (A-Z, 2-7) và có thể thêm ký tự = để padding.
- Cú pháp cơ bản
`base32 [tùy_chọn] [tệp]`
- Các tùy chọn quan trọng
    - `-d`, `--decode`:	Giải mã từ Base32 về dữ liệu gốc.
    - `-i`, `--ignore-garbage`:	Bỏ qua các ký tự không hợp lệ khi giải mã.
    - `-w <số_cột>`:	Chia kết quả thành từng dòng với số ký tự cụ thể (mặc định 76).
    - `--help`:	Hiển thị trợ giúp.
    - `--version`:	Hiển thị phiên bản.

![image](https://hackmd.io/_uploads/S1YjhEW5kl.png)
## level15
![image](https://hackmd.io/_uploads/ByQPAVb5kx.png)
## level16
- Lệnh `split` trong Linux được sử dụng để chia nhỏ tệp tin lớn thành nhiều phần nhỏ.
- Cú pháp cơ bản: `split [OPTIONS] [FILE] [PREFIX]`
    - `FILE`: Tệp cần chia nhỏ. Nếu không chỉ định, split sẽ đọc từ stdin (dữ liệu nhập vào).
    - `PREFIX`: Tiền tố cho các file đầu ra (mặc định là **x**).
    - Nếu không có tùy chọn, `split` sẽ chia tệp thành các phần **100MB** theo mặc định.
- Các tùy chọn quan trọng
    - `-b <size>`	Chia file theo kích thước (có thể dùng K, M, G).
    - `-l <number>`	Chia theo số dòng.
    - `-d`	Đổi tên file đầu ra thành số (x00, x01...) thay vì chữ (xaa, xab...).
    - `-a <length>`	Độ dài của hậu tố (mặc định 2 ký tự: xaa, xab...).
    - `--verbose`	Hiển thị chi tiết quá trình chia tệp.
- Ví dụ: Chia file thành từng phần 100MB
`split -b 100M large_file.bin part_`
File output: `part_aa`, `part_ab`, `part_ac`...
    
![image](https://hackmd.io/_uploads/HJ3YBODcyl.png)

## level17
- Lệnh `gzip` là công cụ nén file phổ biến trên Linux, giúp giảm kích thước tệp tin bằng thuật toán DEFLATE. File sau khi nén có đuôi `.gz`.
- Cú pháp cơ bản:
`gzip [OPTIONS] FILE`
- Các tùy chọn quan trọng: 
    - `-d`	Giải nén file .gz (tương đương gunzip).
    - `-k`	Giữ lại file gốc sau khi nén.
    - `-r`	Nén tất cả file trong thư mục (đệ quy).
    - `-v`	Hiển thị thông tin chi tiết khi nén.
    - `-l`	Xem thông tin file đã nén.
- Ví dụ: 
`gzip myfile.txt`
- Output: `myfile.txt.gz` (file gốc bị xóa).

![image](https://hackmd.io/_uploads/rkQtn_vq1x.png)

## level18
- `bzip2` là công cụ nén file bằng thuật toán Burrows-Wheeler, tạo ra file .bz2.
- `bzcat` là công cụ giải nén và hiển thị nội dung của file `.bz2` mà không cần giải nén ra file mới.
- Lệnh `bzip2` – Nén file: `bzip2 [OPTIONS] FILE`
    - Ví dụ sử dụng: `bzip2 myfile.txt`
    - Tạo file: `myfile.txt.bz2`
- Lệnh `bunzip2` – Giải nén file: `bunzip2 FILE.bz2`
- Lệnh `bzcat` – Đọc file `.bz2` mà không cần giải nén: `bzcat FILE.bz2`
    - Ví dụ sử dụng: `bzcat myfile.txt.bz2`
    - Hiển thị nội dung myfile.txt mà không cần giải nén.
    -  Dùng bzcat với less hoặc grep
```
bzcat myfile.txt.bz2 | less
bzcat myfile.txt.bz2 | grep "keyword"
```
- Lọc nội dung file nén mà không cần giải nén.
- Các lệnh cơ bản: 
    - `bzip2 file.txt`: Nén file.txt → tạo file.txt.bz2 và xóa file.txt.
    - `bunzip2 file.txt.bz2`: Giải nén file.txt.bz2 → phục hồi file.txt.
    - `bzip2 -d file.txt.bz2`: Tương tự bunzip2, giải nén file.
    - `bzip2 -k file.txt`: Nén nhưng không xóa file gốc.
    - `bzip2 -c file.txt > file.bz2`: Nén file nhưng ghi dữ liệu nén vào file.bz2.
    - `bzip2 -t file.txt.bz2`: Kiểm tra file .bz2 có lỗi không.
    
![image](https://hackmd.io/_uploads/B1Lka0Ocyg.png)
## level19
- `zip` là lệnh dùng để nén nhiều file/thư mục thành một file .zip trong Linux. Nó hỗ trợ cả mật khẩu bảo vệ và nén nhiều mức độ khác nhau.
- Cú pháp cơ bản của zip
`zip [OPTIONS] archive.zip file1 file2 ...`
    - `archive.zip`: Tên file nén sẽ tạo.
    - `file1 file2`: Các file hoặc thư mục cần.
- Nén một file
`zip my_archive.zip myfile.txt`
    - Tạo `my_archive.zip` chứa `myfile.txt`.
- Nén nhiều file cùng lúc
`zip my_archive.zip file1.txt file2.txt image.png`
    - Tạo `my_archive.zip` chứa các file `file1.txt`, `file2.txt`, `image.png`.
- Nén cả thư mục
`zip -r my_archive.zip my_folder/`
    - Nén toàn bộ thư mục `my_folder/`.
    - `-r` (recursive): Nén đệ quy toàn bộ file trong thư mục.
- `zip`: Dùng để nén file/thư mục thành .zip.
- `unzip`: Dùng để giải nén file .zip.
- `zip -e`: Bảo vệ file .zip bằng mật khẩu.
- `zip -r`: Nén cả thư mục.
- `zip -d`: Xóa file khỏi .zip.g thư mục.
- **zcat my_archive.zip**

![image](https://hackmd.io/_uploads/Syor7ytc1g.png)
## level20
- Lệnh `tar` (tape archive) là một công cụ mạnh mẽ trong Linux dùng để nén và giải nén nhiều file/thư mục thành một file `.tar`, `.tar.gz`, `.tar.bz2`, v.v.
- Cú pháp: `tar [OPTIONS] -f archive.tar : files...`
- Các tùy chọn quan trọng:
    - `-c` → Tạo file .tar (Create)
    - `-x` → Giải nén file .tar (Extract)
    - `-v` → Hiển thị quá trình thực hiện (Verbose)
    - `-f` → Chỉ định file .tar (File)
    - `-z` → Dùng gzip để nén (.tar.gz)
    - `-j` → Dùng bzip2 để nén (.tar.bz2)
    - `-J` → Dùng xz để nén (.tar.xz)
- Nén nhiều file thành một file .tar:
`tar -cvf archive.tar file1.txt file2.txt image.png`
    - Tạo `archive.tar` chứa `file1.txt`, `file2.txt`, `image.png`.
- Nén cả thư mục
`tar -cvf archive.tar my_folder/`
    - Nén toàn bộ thư mục `my_folder/`.
- Tạo file .tar.gz (nén với gzip)
`tar -czvf archive.tar.gz my_folder/`
    - Dùng `-z` để nén với `gzip`, tạo `archive.tar.gz`.
- Tạo file .tar.bz2 (nén với bzip2)
`tar -cjvf archive.tar.bz2 my_folder/`
    - Dùng `-j` để nén với `bzip2`, tạo `archive.tar.bz2`.
- Giải nén file .tar
`tar -xvf archive.tar`
    - Giải nén toàn bộ nội dung file `archive.tar`.
- Giải nén file .tar.gz
`tar -xzvf archive.tar.gz`
    - Dùng `-z` để giải nén `.tar.gz`.
- Giải nén vào thư mục cụ thể
`tar -xvf archive.tar -C my_folder/`
    - Giải nén nội dung `archive.tar` vào thư mục `my_folder/`.
- Xem danh sách file trong .tar mà không giải nén
`tar -tvf archive.tar`
    - Liệt kê nội dung bên trong `archive.tar`.
- Kiểm tra nội dung file .tar.gz
`tar -tzvf archive.tar.gz`
    - Xem trước file trong `archive.tar.gz`.
- Thêm file vào file .tar đã có
`tar -rvf archive.tar newfile.txt`
    - Thêm `newfile.txt` vào `archive.tar`.
    - (Không áp dụng cho file .tar.gz).
- Xóa file khỏi file .tar
`tar --delete -f archive.tar file1.txt`
    - Xóa `file1.txt` khỏi `archive.tar`.
- Giải nén một file cụ thể trong .tar
`tar -xvf archive.tar file1.txt`
    - Giải nén chỉ `file1.txt` từ `archive.tar`.
- Giải nén nhiều file cụ thể trong .tar
`tar -xvf archive.tar file1.txt file2.txt`
    - Giải nén file1.txt và file2.txt.
- **Tổng kết**:
    - `tar -cvf archive.tar file/` Nén thư mục thành .tar
    - `tar -czvf archive.tar.gz file/`	Nén .tar.gz
    - `tar -xvf archive.tar`	Giải nén .tar
    - `tar -xzvf archive.tar.gz`	Giải nén .tar.gz
    - `tar -tvf archive.tar`	Xem nội dung .tar
    - `tar -xvf archive.tar file.txt`	Giải nén file cụ thể
- Nếu bạn muốn xem nội dung của một file cụ thể trong `.tar`, dùng `tar -xOvf` hoặc `tar --to-stdout -xf`:
`tar -xOvf archive.tar file1.txt`
Hoặc:
`tar --to-stdout -xf archive.tar file1.txt`
- In nội dung của `file1.txt` ra màn hình mà không giải nén toàn bộ `.tar`.

![image](https://hackmd.io/_uploads/Hykn0IYq1g.png)
## level21
ar (Archiver) là một công cụ dùng để tạo, chỉnh sửa và trích xuất nội dung từ các file archive (.a). Nó chủ yếu được sử dụng để quản lý thư viện tĩnh (static libraries) trong C/C++.
Cấu trúc lệnh ar
bash
Sao chép
Chỉnh sửa
ar [options] archive-file file(s)
options: Các tùy chọn (chế độ hoạt động của ar).
archive-file: Tên file archive (.a).
file(s): Danh sách file cần thêm hoặc trích xuất.
Chế độ hoạt động chính (chọn một trong số này)
Tùy chọn	Mô tả
r	Thêm hoặc thay thế file vào archive (.a).
d	Xóa file khỏi archive.
t	Liệt kê nội dung của archive.
x	Trích xuất file từ archive.
q	Thêm file vào cuối archive mà không kiểm tra trùng lặp.
m	Di chuyển file trong archive.
📌 2️⃣ Các tùy chọn bổ sung
Tùy chọn	Dùng kèm với	Mô tả
c	r, q	Tạo archive mới nếu chưa tồn tại.
s	r, q, d	Cập nhật chỉ mục của thư viện .a.
v	Tất cả	Hiển thị chi tiết quá trình thực thi.
o	x	Giữ nguyên thời gian chỉnh sửa file khi trích xuất.
📌 Tạo archive mới	ar rcs libexample.a file1.o file2.o	Tạo libexample.a chứa file1.o, file2.o.
📌 Xem danh sách file trong archive	ar t libexample.a	Hiển thị danh sách file trong libexample.a.
📌 Xem chi tiết file trong archive	ar tv libexample.a	Hiển thị thông tin chi tiết (kích thước, quyền, ngày sửa).
📌 Thêm file vào archive	ar r libexample.a file3.o	Thêm file3.o vào libexample.a.
📌 Trích xuất file từ archive	ar x libexample.a file1.o	Trích xuất file1.o từ libexample.a.
📌 Xóa file trong archive	ar d libexample.a file2.o	Xóa file2.o khỏi libexample.a.
📌 Cập nhật file trong archive	ar q libexample.a file4.o	Thêm file4.o mà không kiểm tra trùng lặp.
📌 Gộp nhiều archive thành một	ar m libnew.a libold.a	Gộp nội dung libold.a vào libnew.a.
Dùng strings để đọc nội dung text trong file
bash
Sao chép
Chỉnh sửa
strings libexample.a
📌 Liệt kê tất cả chuỗi có thể đọc được trong file .a.
![image](https://hackmd.io/_uploads/SJxYXwY91g.png)
