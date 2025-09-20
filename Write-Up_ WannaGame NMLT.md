---
title: 'Write-Up: WannaGame NMLT'

---

# Write-Up: WannaGame NMLT
### **Sinh viên thực hiện:** Phan Thị Như Huỳnh - MSSSV: 24520717
### **Ngày hoàn thành:** 04/01/2025
### **Số bài làm:** 7
## **Mục lục**
1. [Bài 1: Hello pwner](#bài-1-hello-pwner)
2. [Bài 2: GiacMoTrua1](#bài-2-giacmotrua1)
3. [Bài 3: GiacMoTrua2](#bài-3-giacmotrua2)
4. [Bài 4: Easy Flag Checker](#bài-4-easy-flag-checker)
5. [Bài 5: substitution](#bài-5-substitution)
6. [Bài 6: hix](#bài-6-hix)
7. [Bài 7: DH](#bài-7-dh)
## **Bài 1: Hello pwner**
### **Thể loại:** Binary Exploitation
### **Đề bài:** 

Surprise! There's no binary.

Please write your first ever Hello world program using assembly, and compile it into an executable file, then run it.

**Instruction**
Write a program that prints out "Hello, world!" to terminal. In source code file, comment what each line of code means. Compile the program into an executable file. Include the command(s) you used to compile in the writeup.

**Flag for this**: `W1{welcome_to_assembly}`

### **Phân tích thử thách**
- Viết một chương trình bằng Assembly in ra dòng chữ `Hello, world!`.  
- Trong file mã nguồn, thêm comment giải thích từng dòng mã.  
- Biên dịch chương trình thành file thực thi và chạy.  
- Bao gồm các lệnh dùng để biên dịch trong write-up.  
### **Giải quyết bài toán**

#### **1. Mã nguồn Assembly**
Dưới đây là chương trình Assembly:

```assembly
section .data                  
    msg db "Hello, world!", 10, 0   

section .text                   
    global _start               

_start:
    mov rax, 1                  
    mov rdi, 1                  
    mov rsi, msg                
    mov rdx, 14                 
    syscall                     
    mov rax, 60                 
    xor rdi, rdi                
    syscall                    
```
#### **2. Giải thích mã**
- `section .data`: Khu vực dành cho dữ liệu tĩnh.
- `msg db "Hello, world!", 0`: Định nghĩa một chuỗi Hello, world! với ký tự xuống dòng (10 là mã ASCII cho `\n`)kết thúc bằng ký tự NULL.
- `section .text`: Khu vực dành cho mã chương trình.
- `global _start`: Khai báo điểm bắt đầu của chương trình.
- `mov rax, 1`: Đặt syscall number 1 (write) vào thanh ghi rax.
- `mov rdi, 1`: Ghi dữ liệu vào stdout (mã 1 đại diện cho stdout).
- `mov rsi, msg`: Đặt địa chỉ của chuỗi msg vào rsi.
- `mov rdx, 14`: Đặt độ dài chuỗi (14 ký tự) vào rdx.
- `syscall`: Thực hiện syscall write để in chuỗi ra màn hình.
- `mov rax, 60`: Đặt syscall number 60 (exit) vào rax.
- `xor rdi, rdi`: Đặt mã thoát (0) vào rdi.
- `syscall`: Thực hiện syscall exit để kết thúc chương trình.
#### **3. Biên dịch và chạy chương trình**
#### **a. Biên dịch mã Assembly thành file đối tượng (.o):**
`nasm -f elf64 hello.asm -o hello.o`
- `-f elf64`: Chọn định dạng ELF 64-bit.
- `hello.asm`: Tên file mã nguồn.
- `hello.o`: Tên file đối tượng được tạo ra.
#### **b. Liên kết file đối tượng thành file thực thi:**
`ld hello.o -o hello`
- `hello.o`: File đối tượng được tạo ở bước trước.
- `hello`: Tên file thực thi bạn muốn tạo.
#### **4. Kết quả**
Sau khi chạy chương trình, màn hình sẽ hiện thị như ảnh phía dưới:
![image](https://hackmd.io/_uploads/HJbGYlwU1x.png)
**Flag**: `W1{welcome_to_assembly}`
## **Bài 2: GiacMoTrua1**
### **Thể loại:** Reverse Engineering
### **Đề bài:** 
sleepin'

Author: lovely_boy10
**Tệp đính kèm**: [Giacmotrua1.pyc](https://ctf.cnsc.com.vn/assets/198416a3bbde541ff0dbe81d486ef0cb01bea05572f117251c515ef876d2e2ca/Giacmotrua1.pyc)
### **Phân tích thử thách**
- Dựa trên những gì đề bài cung cấp thì ta nhận thấy rằng bài toán yêu cầu tìm flag bằng cách phân tích file bytecode Python.
- **Công cụ sử dụng**:
    1. [PyLingual](https://pylingual.io/): Decompile Online sử dụng để decompile từ pyc về py.
    2. IDE: Hỗ trợ phân tích mã nguồn (**Visual Studio Code**).
- **Các bước thực hiện**:
    1. Sử dụng **PyLingual** để decompile file Giacmotrua1.pyc.
    2. Nhận được mã Python nguồn gốc và phân tích mã nguồn.
**Mã nguồn python sau khi decompile**
```python=
dic = [0] * 85
dic[0] = 33
dic[1] = 35
dic[2] = 36
dic[3] = 37
dic[4] = 38
dic[5] = 40
dic[6] = 41
dic[7] = 42
dic[8] = 43
dic[9] = 44
dic[10] = 45
dic[11] = 46
dic[12] = 47
dic[13] = 48
dic[14] = 49
dic[15] = 50
dic[16] = 51
dic[17] = 52
dic[18] = 53
dic[19] = 54
dic[20] = 55
dic[21] = 56
dic[22] = 57
dic[23] = 58
dic[24] = 59
dic[25] = 60
dic[26] = 61
dic[27] = 62
dic[28] = 63
dic[29] = 64
dic[30] = 65
dic[31] = 66
dic[32] = 67
dic[33] = 68
dic[34] = 69
dic[35] = 70
dic[36] = 71
dic[37] = 72
dic[38] = 73
dic[39] = 74
dic[40] = 75
dic[41] = 76
dic[42] = 77
dic[43] = 78
dic[44] = 79
dic[45] = 80
dic[46] = 81
dic[47] = 82
dic[48] = 83
dic[49] = 84
dic[50] = 85
dic[51] = 86
dic[52] = 87
dic[53] = 88
dic[54] = 89
dic[55] = 90
dic[56] = 91
dic[57] = 97
dic[58] = 98
dic[59] = 99
dic[60] = 100
dic[61] = 101
dic[62] = 102
dic[63] = 103
dic[64] = 104
dic[65] = 105
dic[66] = 106
dic[67] = 107
dic[68] = 108
dic[69] = 109
dic[70] = 110
dic[71] = 111
dic[72] = 112
dic[73] = 113
dic[74] = 114
dic[75] = 115
dic[76] = 116
dic[77] = 117
dic[78] = 118
dic[79] = 119
dic[80] = 120
dic[81] = 121
dic[82] = 122
dic[83] = 123
dic[84] = 125

flag = input('Let me help you check your flag: ')
length = len(flag)
ans = [0] * length * 2

for i in range(length):
    ans[i] = dic[ord(flag[i]) ^ 112]
    
for i in range(length, length * 2):
    ans[i] = ans[i - length]
    
fin = ''

for i in range((23 * length + 16) % length, (23 * length + 16) % length + length):
    fin += chr(ans[i])
    
if fin == 'R8Abq,R&;j%R6;kiiR%hR@k6iy0Ji.[k!8R,kHR*i??':
    print('Rightttt!')
    print('Heyy you are really lovely, i promise!')
else:
    print('Think more....')
```
#### **Phân tích chi tiết mã nguồn**:

##### **1. Biến `dic`:**
- Đây là danh sách chứa **85** phần tử, đại diện cho một bảng ánh xạ từ giá trị số (`ord`) sang một ký tự cụ thể.
- Các giá trị trong dic tương ứng với các ký tự đặc biệt, số, chữ cái in hoa và thường (ví dụ: 33 là `'!'`, 65 là `'A'`, 97 là `'a'`).

##### **2. Nhập liệu flag:**
- Người dùng nhập một chuỗi (giả định là flag) qua `input`.
- `flag` được xử lý qua các bước tính toán sau.

##### **3. Biến đổi flag thành ans**:
- Với mỗi ký tự trong `flag`, hàm `ord` lấy mã ASCII, sau đó XOR với `112` để tạo chỉ số ánh xạ trong `dic`.
- Giá trị ánh xạ từ `dic` được lưu vào danh sách `ans`.
- Phần sau của `ans` (từ `length` đến `2 * length`) là bản sao của phần đầu.

##### **4. Tạo chuỗi fin:**
- Biến `fin` được xây dựng từ một đoạn trong ans, với chỉ số bắt đầu là `(23 * length + 16) % length`.
- Các ký tự của `fin` được chuyển từ mã ASCII trong `ans` bằng hàm `chr`.

##### **5. Kiểm tra tính đúng của flag:**
- Nếu `fin` khớp với chuỗi cố định `'R8Abq,R&;j%R6;kiiR%hR@k6iy0Ji.[k!8R,kHR*i??'` thì flag được xác nhận là đúng.

### **Giải quyết bài toán**
#### 1. Thuật toán để tìm ra flag
##### **Công thức reverse**
###### **a. Xác định chuỗi fin:**
- Dựa trên mã nguồn ta biết được chuỗi `fin` bằng `'R8Abq,R&;j%R6;kiiR%hR@k6iy0Ji.[k!8R,kHR*i??'`, vì vậy nên chúng ta cần truy ngược để tìm được giá trị của `ans`.
###### **b. Phục hồi ans:**
- Do `ans[length:]` là bản sao của `ans[:length]`, chỉ cần tìm `ans[:length]`.
- Từ mỗi ký tự trong `fin`, sử dụng phép nghịch đảo hàm `chr` để lấy lại giá trị ASCII và lưu vào `ans`.
###### **c. Phục hồi flag:**
- Dựa vào giá trị `ans`, tìm ký tự ban đầu trong **flag** thông qua ánh xạ ngược từ `dic` và phép `XOR` với `112`.
##### Quy trình reverse
- Bắt đầu từ `fin`, tìm vị trí bắt đầu trong `ans` bằng công thức `(23 * length + 16) % length`.
- Duyệt qua `fin` và tìm giá trị ASCII tương ứng trong `ans`.
- Với mỗi giá trị `ans[i]`, duyệt qua `dic` để tìm giá trị `c` sao cho **`dic[ord(c) ^ 112] == ans[i]`**.
- Từ các giá trị `c`, ghép lại thành **flag**.
#### 2. Triển khai thuật toán 
```python=
dic = [0] * 85
dic[0] = 33
dic[1] = 35
dic[2] = 36
dic[3] = 37
dic[4] = 38
dic[5] = 40
dic[6] = 41
dic[7] = 42
dic[8] = 43
dic[9] = 44
dic[10] = 45
dic[11] = 46
dic[12] = 47
dic[13] = 48
dic[14] = 49
dic[15] = 50
dic[16] = 51
dic[17] = 52
dic[18] = 53
dic[19] = 54
dic[20] = 55
dic[21] = 56
dic[22] = 57
dic[23] = 58
dic[24] = 59
dic[25] = 60
dic[26] = 61
dic[27] = 62
dic[28] = 63
dic[29] = 64
dic[30] = 65
dic[31] = 66
dic[32] = 67
dic[33] = 68
dic[34] = 69
dic[35] = 70
dic[36] = 71
dic[37] = 72
dic[38] = 73
dic[39] = 74
dic[40] = 75
dic[41] = 76
dic[42] = 77
dic[43] = 78
dic[44] = 79
dic[45] = 80
dic[46] = 81
dic[47] = 82
dic[48] = 83
dic[49] = 84
dic[50] = 85
dic[51] = 86
dic[52] = 87
dic[53] = 88
dic[54] = 89
dic[55] = 90
dic[56] = 91
dic[57] = 97
dic[58] = 98
dic[59] = 99
dic[60] = 100
dic[61] = 101
dic[62] = 102
dic[63] = 103
dic[64] = 104
dic[65] = 105
dic[66] = 106
dic[67] = 107
dic[68] = 108
dic[69] = 109
dic[70] = 110
dic[71] = 111
dic[72] = 112
dic[73] = 113
dic[74] = 114
dic[75] = 115
dic[76] = 116
dic[77] = 117
dic[78] = 118
dic[79] = 119
dic[80] = 120
dic[81] = 121
dic[82] = 122
dic[83] = 123
dic[84] = 125

output = 'R8Abq,R&;j%R6;kiiR%hR@k6iy0Ji.[k!8R,kHR*i??'
length = len(output)  

start_index = (23 * length + 16) % length 

ans = [0] * length

for i in range(length):

    ans[i] = ord(output[(i + start_index) % length])

flag = ''

for i in range(length):
    original_char = None 
    for c in range(32, 127):  
        xor_result = ord(chr(c)) ^ 112  
        if 0 <= xor_result < len(dic) and dic[xor_result] == ans[i]:
            original_char = chr(c)  
            break
    if original_char is not None:
        flag += original_char  
print(f'Flag: {flag}')

```
**Kết quả chạy chương trình**:
![image](https://hackmd.io/_uploads/SJlnELWPIJg.png)
Từ đó ta suy ra **flag** của bài này là:
`W1{H3pe_y3U_w1ll_enJ9y_th2s_ch311_s0_m3c1!}`

## **Bài 3: GiacMoTrua2**
### **Thể loại:** Reverse Engineering
### **Đề bài:**
Author: lovely_boy10
**Tệp đính kèm**: [Giacmotrua2](https://ctf.cnsc.com.vn/assets/b610ec68e1fe1a4a69998129a92f068ce654b4d2ee524939084651e4a3a3c59d/GiacMoTrua2)
### **Phân tích thử thách**
- **Công cụ sử dụng**:
    1. Ghidra: Công cụ reverse engineering để decompile và phân tích mã nhị phân.
    2. IDE: Hỗ trợ phân tích mã nguồn (**Visual Studio Code**).
- **Các bước thực hiện**:
    1. Kiểm tra thông tin chi tiết về file `Giacmotrua2` 
![image](https://hackmd.io/_uploads/BySLobwLyx.png)
    Ta thấy file GiacMoTrua2 là một file `ELF`, `64-bit`, có tính năng bảo mật `PIE`. File này còn được liên kết động với thư viện hệ thống và có interpreter là `/lib64/ld-linux-x86-64.so.2`.
    2. Dùng lệnh `strings` để tìm các chuỗi có thể liên quan đến flag hoặc các thông báo lỗi.
    ![image](https://hackmd.io/_uploads/S1lniZPUJl.png)
    Sau khi phân tích các chuỗi thu được từ bộ nhớ chương trình, chúng ta có thể trích xuất chuỗi tiềm năng `W1{live_speels_a5_NoOn_4v4ry_emit!}`. Tuy nhiên, khi thử nộp chuỗi này làm flag, hệ thống đã thông báo rằng flag không chính xác. Điều này gợi ý rằng chuỗi thu được cần phải qua một số bước xử lý hoặc biến đổi để trở thành flag chính xác. 
    3. Decompile file bằng Ghidra.
    **Mã nguồn C sau khi decompile**
```cpp=
undefined8 main(void)

{
  long in_FS_OFFSET;
  int local_7c;
  char local_78 [104];
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  printf("Give me your secret string: ");
  fgets(local_78,100,stdin);
  if ((local_78[0] == 'W') && (local_78[1] == '1')) {
    Lookthis();
    for (local_7c = 0; local_7c < 0x21; local_7c = local_7c + 1) {
      if (flag[local_7c] != local_78[local_7c]) {
        puts("Maybe... It\'s not the correct flag!");
        goto LAB_001013a9;
      }
    }
    puts("Correct, Good eyes!");
  }
  else {
    puts("Did you forget flag format?");
  }
LAB_001013a9:
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```
#### **Phân tích chi tiết mã nguồn**:

##### 1. Biến và bộ nhớ cục bộ:
- `local_78` là một mảng ký tự (`104` bytes) dùng để lưu chuỗi đầu vào từ người dùng.
- `local_7c` là một biến đếm (int) dùng trong vòng lặp.
- `local_10` là một biến kiểm tra bảo mật stack.
##### 2. Cơ chế kiểm tra bảo mật stack:
- Biến `local_10` được gán giá trị từ `in_FS_OFFSET + 0x28`, là giá trị canary dùng để phát hiện việc ghi đè stack.
- Nếu giá trị này bị thay đổi khi chương trình kết thúc, hàm `__stack_chk_fail()` sẽ được gọi để dừng chương trình.
##### 3. Quá trình nhập chuỗi:
- Chuỗi được nhập từ người dùng bằng `fgets` với kích thước tối đa là `100` ký tự.
##### 4. Kiểm tra chuỗi đầu vào:
- Nếu ký tự đầu tiên (`local_78[0]`) là `'W'` và ký tự thứ hai (`local_78[1]`) là `'1'`, thì chương trình tiếp tục kiểm tra chuỗi với `flag`.
##### 5. So sánh chuỗi:
- Hàm `Lookthis()` được gọi (ta sẽ phân tích chi tiết hàm này sau).
- Một vòng lặp kiểm tra từng ký tự của chuỗi nhập vào (`local_78`) với một chuỗi được lưu trong `flag` (kích thước `33` bytes).
- Nếu có bất kỳ ký tự nào không khớp, chương trình in ra: `"Maybe... It's not the correct flag!"`.
- Nếu toàn bộ ký tự khớp với chuỗi flag, chương trình in ra: `"Correct, Good eyes!"`.
- Nếu không khớp hoặc định dạng chuỗi không đúng, thông báo lỗi sẽ xuất hiện.
**Mã nguồn hàm `Lookthis()`**
```cpp=
void Lookthis(void)

{
  int local_14;
  int local_10;
  int local_c;
  
  for (local_14 = 3; local_14 < 7; local_14 = local_14 + 1) {
    if (local_14 < 9 - local_14) {
      swap(flag + local_14,flag + (9 - local_14));
    }
  }
  for (local_10 = 8; local_10 < 0xe; local_10 = local_10 + 1) {
    if (local_10 < 0x15 - local_10) {
      swap(flag + local_10,flag + (0x15 - local_10));
    }
  }
  for (local_c = 0x1d; local_c < 0x21; local_c = local_c + 1) {
    if (local_c < 0x3d - local_c) {
      swap(flag + local_c,flag + (0x3d - local_c));
    }
  }
  return;
}
```
#### **Phân tích chi tiết hàm `Lookthis()`**:
##### Vòng lặp 1:
```cpp=
for (local_14 = 3; local_14 < 7; local_14 = local_14 + 1) {
    if (local_14 < 9 - local_14) {
        swap(flag + local_14, flag + (9 - local_14));
    }
}
```
- Phạm vi: Từ `local_14 = 3` đến `local_14 = 6` (`giá trị < 7`).
- Điều kiện hoán đổi: `local_14 < 9 - local_14`.
- Hoán đổi: `flag[3]` với `flag[6]`, `flag[4]` với `flag[5]`.
##### Vòng lặp 2:
```cpp=
for (local_10 = 8; local_10 < 0xe; local_10 = local_10 + 1) {
    if (local_10 < 0x15 - local_10) {
        swap(flag + local_10, flag + (0x15 - local_10));
    }
}
```
- Phạm vi: Từ `local_10 = 8` đến `local_10 = 13` (`giá trị < 0xe`, hay `14` trong thập phân).
- Điều kiện hoán đổi: `local_10 < 0x15 - local_10`  (hay `21 - local_10`).
- Hoán đổi: `flag[8]` với `flag[13]`, `flag[9]` với `flag[12]`, `flag[10]` với `flag[11]`.
##### Vòng lặp 2:
```cpp=
for (local_10 = 8; local_10 < 0xe; local_10 = local_10 + 1) {
    if (local_10 < 0x15 - local_10) {
        swap(flag + local_10, flag + (0x15 - local_10));
    }
}
```
- Phạm vi: Từ `local_c = 29` (`0x1d` trong thập phân) đến `local_c = 32`.
- Điều kiện hoán đổi: `local_c < 0x3d - local_c`.
- Hoán đổi: `flag[29]` với `flag[32]`, `flag[30]` với `flag[31]`.
##### Kết luận:
- Hàm này thực hiện các hoán đổi đối xứng trên các đoạn của mảng `flag`.
    - **Vòng lặp 1**: Hoán đổi đoạn `[3..6]` theo đối xứng.
    - **Vòng lặp 2**: Hoán đổi đoạn `[8..13]` theo đối xứng.
    - **Vòng lặp 3**: Hoán đổi đoạn `[29..32]` theo đối xứng.
### **Giải quyết bài toán**
#### 1. Thuật toán để tìm ra flag
Từ những gì ta phân tích được ở trên thì để tìm ra `flag` ban đầu, chúng ta cần lặp lại quá trình của hàm `Lookthis()` sẽ giúp ta tìm được `flag` thật sự vì hàm `Lookthis()` thực hiện một chuỗi các thao tác hoán đổi có tác dụng thay đổi nội dung chuỗi `flag` ban đầu bằng thực hiện hoán đổi **đối xứng**. 
#### 2. Triển khai thuật toán
```python=

flag = list("W1{live_speels_a5_NoOn_4v4ry_emit!}")

for local_14 in range(3, 7):  
    if local_14 < 9 - local_14:
        swap_idx = 9 - local_14
        flag[local_14], flag[swap_idx] = flag[swap_idx], flag[local_14]

for local_10 in range(8, 14):  
    if local_10 < 21 - local_10:
        swap_idx = 21 - local_10
        flag[local_10], flag[swap_idx] = flag[swap_idx], flag[local_10]
        
for local_c in range(29, 33):
    if local_c < 61 - local_c:
        swap_idx = 61 - local_c
        flag[local_c], flag[swap_idx] = flag[swap_idx], flag[local_c]

result_flag = "".join(flag)  
print(result_flag) 
```
**Kết quả chạy chương trình**:
![image](https://hackmd.io/_uploads/ryEcJmDIyl.png)
Từ đó ta suy ra **flag** của bài này là:
`W1{evil_sleeps_a5_NoOn_4v4ry_time!}`
## **Bài 4: Easy Flag Checker**
### **Thể loại:** Reverse Engineering
### **Đề bài:**
Give me flag !

Author: fuyosuru
**Tệp đính kèm**: [easy_flag_checker.rar](https://ctf.cnsc.com.vn/assets/e35cbedab5590d27d11d3d1a1c5379485fd83d7e5c1f7b6525fefa231f3966ed/easy_flag_checker.rar)
### **Phân tích thử thách**
- **Công cụ sử dụng**:
    1. Ghidra: Công cụ reverse engineering để decompile và phân tích mã nhị phân.
    2. IDE: Hỗ trợ phân tích mã nguồn (**Visual Studio Code**).
- **Các bước thực hiện**:
    1. Kiểm tra thông tin chi tiết về file
![image](https://hackmd.io/_uploads/B1_07qDUJl.png)
Ta thấy file easy_flag_checker là một file `ELF`, `64-bit`, có tính năng bảo mật `PIE`. File này còn được liên kết động với thư viện hệ thống và có interpreter là `/lib64/ld-linux-x86-64.so.2`.
    2. Decompile file bằng Ghidra.
    **Mã nguồn C sau khi decompile**
```cpp=
undefined8 FUN_00101269(void)

{
  long lVar1;
  byte *pbVar2;
  long in_FS_OFFSET;
  bool bVar3;
  int local_4fc;
  basic_string local_4f8 [32];
  int aiStack_4d8 [302];
  long local_20;
  
  local_20 = *(long *)(in_FS_OFFSET + 0x28);
  std::__cxx11::basic_string<>::basic_string();
                    /* try { // try from 001012ab to 001013b0 has its CatchHandler @ 001013d6 */
  std::operator<<((basic_ostream *)std::cout,"Give me flag: ");
  std::operator>>((basic_istream *)std::cin,local_4f8);
  lVar1 = std::__cxx11::basic_string<>::size();
  bVar3 = lVar1 == 0x14;
  for (local_4fc = 0; local_4fc < 0x14; local_4fc = local_4fc + 1) {
    pbVar2 = (byte *)std::__cxx11::basic_string<>::operator[]((ulong)local_4f8);
    aiStack_4d8[local_4fc] = (int)(char)(*pbVar2 ^ 0x38);
    if (aiStack_4d8[local_4fc] != *(int *)(&DAT_00104020 + (long)local_4fc * 4)) {
      bVar3 = false;
    }
  }
  if (bVar3) {
    std::operator<<((basic_ostream *)std::cout,"Correct!");
  }
  else {
    std::operator<<((basic_ostream *)std::cout,"Wrong!");
  }
  std::__cxx11::basic_string<>::~basic_string((basic_string<> *)local_4f8);
  if (local_20 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```
#### **Phân tích chi tiết mã nguồn**:

##### 1. Biến và bộ nhớ cục bộ:
- `local_4f8`: Một chuỗi để lưu đầu vào từ người dùng.
- `aiStack_4d8`: Một mảng số nguyên có `302` phần tử, nhưng thực tế chỉ sử dụng `20` phần tử đầu để lưu giá trị sau khi thực hiện `XOR`.
- `bVar3`: Một cờ logic để kiểm tra tính đúng đắn của chuỗi.
- `DAT_00104020`: Địa chỉ lưu trữ giá trị mã hóa (giá trị đúng cần so sánh sau khi `XOR`).
- `local_20`: Biến bảo vệ stack, dùng để phát hiện lỗi ghi đè bộ nhớ.
##### 2. Chi tiết từng đoạn mã:
###### **a. Lấy chuỗi từ người dùng**
```cpp=
std::operator<<((basic_ostream *)std::cout,"Give me flag: ");
std::operator>>((basic_istream *)std::cin,local_4f8);

```
- In ra màn hình dòng `"Give me flag: "`.
- Nhận chuỗi đầu vào từ người dùng và lưu vào `local_4f8`.
###### **.b Kiểm tra độ dài chuỗi**
```cpp=
lVar1 = std::__cxx11::basic_string<>::size();
bVar3 = lVar1 == 0x14;
```
- Lấy độ dài chuỗi `local_4f8`.
- Biến `bVar3` được đặt là `true` nếu độ dài chuỗi bằng `20` (`0x14`).
###### **c. Duyệt qua từng ký tự và thực hiện XOR**
```cpp=
for (local_4fc = 0; local_4fc < 0x14; local_4fc = local_4fc + 1) {
    pbVar2 = (byte *)std::__cxx11::basic_string<>::operator[]((ulong)local_4f8);
    aiStack_4d8[local_4fc] = (int)(char)(*pbVar2 ^ 0x38);
    if (aiStack_4d8[local_4fc] != *(int *)(&DAT_00104020 + (long)local_4fc * 4)) {
      bVar3 = false;
    }
}

```
- Vòng lặp duyệt qua `20` ký tự đầu tiên của chuỗi `local_4f8`.
###### **Các bước trong vòng lặp:**
**1. Truy cập ký tự tại vị trí `local_4fc`:**
        - Lấy ký tự thứ `local_4fc` trong chuỗi đầu vào `local_4f8`.
```cpp=
pbVar2 = (byte *)std::__cxx11::basic_string<>::operator[]((ulong)local_4f8);

```
**2. XOR với 0x38:**
        - Thực hiện `XOR` ký tự với `0x38` và lưu kết quả vào `aiStack_4d8` tại vị trí tương ứng.

```cpp=
aiStack_4d8[local_4fc] = (int)(char)(*pbVar2 ^ 0x38);
```
**3. So sánh kết quả với giá trị mã hóa:**
- So sánh giá trị vừa `XOR` với giá trị lưu trong `DAT_00104020`.
- Nếu không khớp, đặt `bVar3 = false`.
```cpp=
if (aiStack_4d8[local_4fc] != *(int *)(&DAT_00104020 + (long)local_4fc * 4)) {
  bVar3 = false;
}
```
**4. In kết quả kiểm tra**
```cpp=
if (bVar3) {
    std::operator<<((basic_ostream *)std::cout,"Correct!");
} else {
    std::operator<<((basic_ostream *)std::cout,"Wrong!");
}
```
- Nếu `bVar3 == true`, in `"Correct!"`.
- Nếu không, in `"Wrong!"`.
### **Giải quyết bài toán**
#### 1. Thuật toán để tìm ra flag
- Từ những gì ta phân tích được ở trên thì để tìm ra `flag` thực sự, cần đảo ngược quá trình: `XOR` giá trị mã hóa tại `DAT_00104020` với `0x38` để tìm ký tự ban đầu.
- Ta có giá trị của `DAT_00104020` dựa trên Ghidra như sau:
![image](https://hackmd.io/_uploads/ByBH95PL1x.png)

#### 2. Triển khai thuật toán
```python=
# Mảng giá trị thực tế từ vùng nhớ
encoded_data = [0x6f, 0x09, 0x43, 0x4e, 0x0b, 0x4a, 0x41, 0x67, 0x0b, 0x0c, 0x4b, 0x41, 0x67, 0x4a, 0x09, 0x5f, 0x50, 0x0f, 0x07, 0x45]

# Key XOR được sử dụng
xor_key = 0x38

# Giải mã từng ký tự
decoded_flag = ''.join(chr(byte ^ xor_key) for byte in encoded_data)

# In kết quả
print("Flag:", decoded_flag)
```
**Kết quả chạy chương trình**:
![image](https://hackmd.io/_uploads/HJXw3qPU1g.png)
Từ đó ta suy ra **flag** của bài này là:
`W1{v3ry_34sy_r1gh7?}`
## **Bài 5: substitution**
### **Thể loại:** Crypto
### **Đề bài:**
Author: ADP
**Tệp đính kèm**: [give_to_player.rar](https://ctf.cnsc.com.vn/assets/119dfe21e08ab02aad52ae455d50b3ed0209c082026cf50070ef1b07d40d34b3/give_to_player.rar)
### **Phân tích thử thách**
- **Công cụ sử dụng**: IDE: Hỗ trợ phân tích mã nguồn (**Visual Studio Code**).
- **Các bước thực hiện**: Giải nén `give_to_player.rar` thì ta nhận được kết quả như hình sau:
![image](https://hackmd.io/_uploads/rkZGC5wIye.png)
#### Phân tích chi tiết các file được cung cấp:
##### 1. File `chall.py`
```python=
KEY = {
    'A': 'Q', 'B': 'W', 'C': 'E', 'D': 'R', 'E': 'T', 'F': 'Y', 'G': 'U', 'H': 'I', 'I': 'O',
    'J': 'P', 'K': 'A', 'L': 'S', 'M': 'D', 'N': 'F', 'O': 'G', 'P': 'H', 'Q': 'J', 'R': 'K',
    'S': 'L', 'T': 'Z', 'U': 'X', 'V': 'C', 'W': 'V', 'X': 'B', 'Y': 'N', 'Z': 'M',
    'a': 'q', 'b': 'w', 'c': 'e', 'd': 'r', 'e': 't', 'f': 'y', 'g': 'u', 'h': 'i', 'i': 'o',
    'j': 'p', 'k': 'a', 'l': 's', 'm': 'd', 'n': 'f', 'o': 'g', 'p': 'h', 'q': 'j', 'r': 'k',
    's': 'l', 't': 'z', 'u': 'x', 'v': 'c', 'w': 'v', 'x': 'b', 'y': 'n', 'z': 'm',
}

def hehe(data, key):
    return ''.join(key.get(char, char) for char in data)

def encrypt(plaintext):
    substituted = hehe(plaintext, KEY)
    return substituted

if __name__ == "__main__":
    plaintext = "W1{???????????????}"
    encrypted = encrypt(plaintext)
    with open("encrypted.txt", "w") as f:
        f.write(encrypted)
```
- Chương trình sử dụng một bảng thay thế ký tự (`KEY`) để mã hóa văn bản.
- Hàm `hehe(data, key)` thay thế từng ký tự trong chuỗi `data` dựa trên cặp giá trị trong `key`.
- Hàm `encrypt(plaintext)` gọi `hehe` để mã hóa chuỗi `plaintext`.
##### 2. File `encrypted.txt`
- Kết quả mã hóa của `plaintext` là chuỗi:
```
V1{lxwlzozxzogf}
```
- `plaintext` ban đầu có định dạng `W1{???????????????}`, trong đó phần `"?"` đã bị mã hóa.
### **Giải quyết bài toán**
#### 1. Thuật toán để tìm ra flag
- Từ những gì ta phân tích được ở trên thì để tìm ra `flag` thực sự, ta cần dựa vào bảng thay thế (`KEY`), giải mã chuỗi `V1{lxwlzozxzogf}` để tìm **flag** gốc.
#### 2. Triển khai thuật toán
```python=
SUBSTITUTION_TABLE = {
    'A': 'Q', 'B': 'W', 'C': 'E', 'D': 'R', 'E': 'T', 'F': 'Y', 'G': 'U', 'H': 'I', 'I': 'O',
    'J': 'P', 'K': 'A', 'L': 'S', 'M': 'D', 'N': 'F', 'O': 'G', 'P': 'H', 'Q': 'J', 'R': 'K',
    'S': 'L', 'T': 'Z', 'U': 'X', 'V': 'C', 'W': 'V', 'X': 'B', 'Y': 'N', 'Z': 'M',
    'a': 'q', 'b': 'w', 'c': 'e', 'd': 'r', 'e': 't', 'f': 'y', 'g': 'u', 'h': 'i', 'i': 'o',
    'j': 'p', 'k': 'a', 'l': 's', 'm': 'd', 'n': 'f', 'o': 'g', 'p': 'h', 'q': 'j', 'r': 'k',
    's': 'l', 't': 'z', 'u': 'x', 'v': 'c', 'w': 'v', 'x': 'b', 'y': 'n', 'z': 'm',
}

DECRYPTION_TABLE = {encoded_char: original_char for original_char, encoded_char in SUBSTITUTION_TABLE.items()}

def decrypt(ciphertext):
    return ''.join(DECRYPTION_TABLE.get(char, char) for char in ciphertext)

encrypted_text = "V1{lxwlzozxzogf}"

decrypted_flag = decrypt(encrypted_text)

print(decrypted_flag)

```
**Kết quả chạy chương trình**:
![image](https://hackmd.io/_uploads/S1lnZoPL1g.png)
Từ đó ta suy ra **flag** của bài này là:
`W1{substitution}`
## **Bài 6: hix**
### **Thể loại:** Crypto
### **Đề bài:**
Author: s1gm4
**Tệp đính kèm**: [give_to_player.7z](https://ctf.cnsc.com.vn/assets/b862d2a2f708c6dece5e8b9b462cff47e6df2bee382061dbaec504bf2cf95faf/give_to_player.7z)
### **Phân tích thử thách**
- **Công cụ sử dụng**: IDE: Hỗ trợ phân tích mã nguồn (**Visual Studio Code**).
- **Các bước thực hiện**: Giải nén `give_to_player.rar` thì ta nhận được kết quả như hình sau:
![image](https://hackmd.io/_uploads/BJCG5owLJg.png)
#### Phân tích chi tiết các file được cung cấp:
##### 1. File `chall.py`
###### **a. Phân tích hàm `random_encrypt`:**
```python=
def random_encrypt(x):
    method = random.choice(methods)
    hash_obj = hashlib.new(method)
    hash_obj.update(x.encode())
    return hash_obj.hexdigest()
```
- Chức năng: Mã hóa chuỗi `x` bằng một phương pháp hash ngẫu nhiên từ danh sách `methods`.
- Danh sách phương pháp hash:
`md5, sha256, sha3_256, sha3_512, sha3_384, sha1, sha384, sha3_224, sha512, sha224`.
###### **b. Phân tích hàm `main`:**
```python=
for char in message:
    x = (ord(char) + 20) % 130
    x = hashlib.sha512(str(x).encode()).hexdigest()
    x = random_encrypt(x)
    enc.append(x)
```
- Mỗi ký tự trong `flag` được mã hóa qua 3 bước:

    - Biến đổi giá trị ASCII: 
        - `ord(char) + 20`: Lấy giá trị ASCII của ký tự và cộng thêm `20`.
        - `% 130`: Lấy modulo `130` để đảm bảo kết quả nằm trong phạm vi `[0, 129]`.
    - Hash SHA-512:
        - Kết quả từ bước trên được chuyển thành chuỗi `(str(x))`.
        - Sau đó hash bằng `SHA-512` để tạo chuỗi `hexdigest`.
    - Hash ngẫu nhiên: 
        - Kết quả từ bước `SHA-512` được hash lại bằng một phương pháp ngẫu nhiên từ `methods`.
### **Giải quyết bài toán**
#### 1. Thuật toán để tìm ra flag
- Để tìm `flag`, ta cần đảo ngược quá trình mã hóa. Tuy nhiên, vì hàm `random.choice` không cố định, nên cần sử dụng `brute force`.
#### 2. Triển khai thuật toán
```python=
import hashlib

methods = ['md5', 'sha256', 'sha3_256', 'sha3_512', 'sha3_384', 'sha1', 'sha384', 'sha3_224', 'sha512', 'sha224']

encrypted_memory = [
'f189636f8eef640b55d03387864fd17efd324453cc9276be5ff6bd4da88b13fca72438daaab00830a6d14330d37c0f7bee1e7c32d5dda0541a171f66a2343dc1', 
'1388cafa58065fa0c04372ce57f303cc4ec9fe62', 
'f6266e2849bf8b8575701814cc3f3eb5369e887db54b34e85b1e4608b4fbf5e5', 
'31f33ac191e818db784cf8321d70f84763db2b2e599f90cf65868eec85a10f20ae0e23aa1cd48c2f13eec355b2975089490761a291ac2a1bcf33f5fbecead431', 
'981e4bce5dede3faa51a936f650e2c1d64169493860c67d68a1ffbbfa32f58598e7869f3f11aefc1620ee8d3ebe4e5f5', 
'f06ffaaa6290bf47d26ba2c09c28dddd8f5bcad6ac464ec17fea48040acf1214d10bc109b7c47cffddb6bccd6b61b61a9e629a8f47ab26b80593f29c8c297489', 
'a7d95b3bbde885b4eaa76afc6572e18e4483351005f637fe1f5a7bc0b000fe1f', 
'85245de371c327440a5f343f27d6df361225806e679950bab3a5a336', 
'ea1923e909de3c3c3384ad9ae7696d73', 
'21df20aab35967470aada32375f535d4a735789bf0789fd421f85163c4d75c6e', 
'b9491ae1a9de40d30a86c00139bd7d6f496f5bf4ce013bc2d5a43a97', 
'03f061f60f3527b15ff31d31dcce0761', 
'981e4bce5dede3faa51a936f650e2c1d64169493860c67d68a1ffbbfa32f58598e7869f3f11aefc1620ee8d3ebe4e5f5', 
'f2a1a7e9dd5e6363050b0cdb0579ebfebdc5e348ab538bdcf47616139351cf2b9f92cb4d14446b3ad8bf182875b81e75', 
'24aaafc58a2b897aed5829b2e96d73b1de7cd680d76a1143cdc8baef', 
'6d80d11e5f1161ef86619dcdb186852b5218d6ac224b81b63555fe73741631c36ae0bcb5b3228fbed796c22dedeed587c9d65ddb825aee4fae92b6619e7ffd8f', 
'6f8b39550106044625102ee0cabf9fe1393f0013388633d5742fcc7e8df7708793a96885b9d18b795a2b0d9014704b9f', 
'ddf3c543be9cac44f3af078583fe5fddb64104d93308c146c23f52ff25b2a6e23606c42dc0060a4dd9b11b446759cb5de1844471eb3d6d25c43c6fcc0d8d60c4', 
'95f2739053cf64555b0c0662b5e2d63822433f7fcac6960de6d57efda427461a58c6e2ffac6da6f4caa9407df10cc0be', 
'a1bd4e0efc7ce8bd1d63433a0baa87e3a486fbfe2729d73d1dbf7d2822d201ee8726c6d94da1f09f1a53554e440ad6041ecab545b2085dc28c6f6849f0fcea23', 
'a7d95b3bbde885b4eaa76afc6572e18e4483351005f637fe1f5a7bc0b000fe1f', 
'2b4561a521a82af6a26dfb76078ca97ba53a720f7ee67d923a6d3a13', 
'b21ed1f3d501a8a842ef1b26ed3863cf10cf8231ee23a079f749cfa322702c8e', 
'd798a32b52384219f8779dccf8b2173f4b73f075cbeb4507ee83c94e', 
'b863fa3492fb87edcdef766f38a508ed', 
'9f876db4b58c1b7e499f35cdbd533a810060a0c8250bfc5421e0f42b2715b027', 
'4b14748ba0f3da581ddd7ec49dac41d34ea1ee6dae90818333b11501', 
'85153b2a5f8dea7f5488906cb65d61e9ac0666057636ff6b356dd4d8d0fc5d20', 
'6b91d6259827176bcb3f312a8faca297e56c7e627235b930cf8163b3e7a5328b', 
'b21ed1f3d501a8a842ef1b26ed3863cf10cf8231ee23a079f749cfa322702c8e', 
'4c8740f90af1055f194a4c8e1b69522da228812465eb72b82b35c927bc48bf9d', 
'b248b6b2f2c9365aa9a0e9b37a8057effd29bb2f34c79ec0b40124d08986832b5d227db95cb97b176541589985762d9a', 
'7260f9b5d1c58d0609523114ed324f396335d940f852dba558461b34c5a53630', 
'a1bd4e0efc7ce8bd1d63433a0baa87e3a486fbfe2729d73d1dbf7d2822d201ee8726c6d94da1f09f1a53554e440ad6041ecab545b2085dc28c6f6849f0fcea23', 
'1077caf3ed754ed8fbd49c76134906e8', 
'f3565219d115ec74a85056997cc25e98e3e4912a31c858c1e45b841047698e93', 
'83315b8fa07a35b12e3f47ebb365268b4a4a8ef2', 
'64c008d6460c2b98aba616b1d0d11a06b9df564b87d3aeedda83b36aacd3d0c160465109eb06c62e86e360cf026faa27a616dbbf2bec269be9ad128af96073bb', 
'60bbd94b3ac3ea7149fc6cd850d72d4f1750601275832815dd9a23d4c3757d84aca29d716da5dd72a0045f15ff969925', 
'94327e8c8321421e72f52cd726336e824630ec7dda31b07ce83f11b8234aea7a', 
'a69ef62254280226cc4223a2341c727afcd7ce4e3ffd3f2f1c57d9d3cd30659b52b1c2b56f911a7157041b5f0ff8176f', 
'3c904622c8d8d79c6704d50ae0175b049b3a5708705ecdce932fe426b9f46f1bd6585b8288c1d38f6301c31af5feac02', 
'a3939bf491ffd9824056e249d6e355d8423855f0'

]

def decrypt_char(enc_char):
    for method in methods:
        try:
            for i in range(130):  
                candidate = hashlib.sha512(str(i).encode()).hexdigest()
                hash_obj = hashlib.new(method)
                hash_obj.update(candidate.encode())
                if hash_obj.hexdigest() == enc_char:
                    original_char = chr((i - 20 + 130) % 130)
                    return original_char
        except:
            pass
    return '?'  
flag = ""
for enc_char in encrypted_memory:
    decrypted_char = decrypt_char(enc_char)
    flag += decrypted_char

print("Flag:", flag)

```
**Kết quả chạy chương trình**:
![image](https://hackmd.io/_uploads/By3-ToDLJl.png)
Từ đó ta suy ra **flag** của bài này là:
`W1{are_you_trying_to_predict_randomness@_@}`
## **Bài 7: DH**
### **Thể loại:** Crypto
### **Đề bài:**
Author: s1gm4
**Tệp đính kèm**: [chall.py](https://ctf.cnsc.com.vn/assets/3361e2a20a7bcee07993f137f849b768b9c65d40ec9ed1bc109e24614a0447f3/chall.py)
### **Phân tích thử thách**
- **Công cụ sử dụng**: IDE: Hỗ trợ phân tích mã nguồn (**Visual Studio Code**).
#### Phân tích chi tiết file được cung cấp:
```python=
from Crypto.Util.number import isPrime, long_to_bytes, getPrime
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad
from random import randint
from hashlib import sha256


FLAG = b"W1{fake-flag}"

class DH:   

    def __init__(self):
        self.gen_params()

    def gen_params(self):
        self.r = getPrime(512)

        while True:
            self.q = getPrime(42)
            self.p = (2 * self.q * self.r) + 1
            if isPrime(self.p):
                break

        while True:
            self.h = getPrime(42)
            self.g = pow(self.h, 2 * self.r, self.p)
            if self.g != 1:
                break   

        self.a = randint(2, self.p - 2)
        self.b = randint(2, self.p - 2)

        self.A, self.B = pow(self.g, self.a, self.p), pow(self.g, self.b, self.p)
        self.ss = pow(self.A, self.b, self.p)

    def encrypt(self, flag_part):
        key = sha256(long_to_bytes(self.ss)).digest()[:16]
        cipher = AES.new(key, AES.MODE_ECB)
        ct = cipher.encrypt(pad(flag_part, 16)).hex()
        return f"encrypted = {ct}"

    def get_params(self):
        return f"p = {self.p}\ng = {self.g}\nA = {self.A}\nB = {self.B}"


def main():

    dh = DH()
    print(dh.get_params())
    print(dh.encrypt(FLAG))

if __name__ == "__main__":
    main()

p = 85013941328859365232686230728938372320812319905627686919070637645614632817039920673725615375841158719310596592903101914818137738460649589340349796188816568005092757847
g = 20033344683527080232439150682925185454003164954955126339094967675384779782733210350757021743656898625398860187361281262413493941502725149445995471514781822892886669776
A = 76548721461171533747911417838852759206858825205673491250696441734297318615226024320798706656529038703728631231084155790148283919370554345818139818854112841655270107839
B = 2103083080159597422706551446020625757109756570951674830166998494220734179439318911618156966499109201221652320384817270671579741987575328177442670242481963924501204498
encrypted = "240e7b7678aaaa0dcbe06de7c5598a1ca0be7e2ae584bc7dfd2388cdb1d4fb6a37ceb94556757afc293999cbe5a5a2dbb4071ebf6cfd4332088555f9b2de1922"
```
Dựa trên file `chall.py` thì ta nhận thấy rằng đây là dạng bài `Diffie-Hellman` kết hợp với mã hóa `AES`.
### **Giải quyết bài toán**
#### 1. Thuật toán để tìm ra flag
- Giải bài toán Discrete Logarithm để tìm `a` hoặc `b`.
- Tính `s = A^b mod p` hoặc `s = B^a mod p`.
- Tạo khóa `AES` từ `s` và giải mã đoạn mã hóa để tìm thấy **flag**.
#### 2. Triển khai thuật toán
```python=
from Crypto.Util.number import long_to_bytes
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad
from hashlib import sha256
from sympy.ntheory import factorint, discrete_log
p = 85013941328859365232686230728938372320812319905627686919070637645614632817039920673725615375841158719310596592903101914818137738460649589340349796188816568005092757847
g = 20033344683527080232439150682925185454003164954955126339094967675384779782733210350757021743656898625398860187361281262413493941502725149445995471514781822892886669776
A = 76548721461171533747911417838852759206858825205673491250696441734297318615226024320798706656529038703728631231084155790148283919370554345818139818854112841655270107839
B = 2103083080159597422706551446020625757109756570951674830166998494220734179439318911618156966499109201221652320384817270671579741987575328177442670242481963924501204498
ciphertext = bytes.fromhex("240e7b7678aaaa0dcbe06de7c5598a1ca0be7e2ae584bc7dfd2388cdb1d4fb6a37ceb94556757afc293999cbe5a5a2dbb4071ebf6cfd4332088555f9b2de1922")

phi_p = (p - 1) // 2

factors = factorint(phi_p)
q = [k for k in factors.keys() if k.bit_length() == 42][0] 
r = [k for k in factors.keys() if k.bit_length() == 512][0]  
a = discrete_log(p, A, g) 
ss = pow(B, a, p)  

key = sha256(long_to_bytes(ss)).digest()[:16]  
cipher = AES.new(key, AES.MODE_ECB)  
flag = unpad(cipher.decrypt(ciphertext), 16)  

print(flag.decode())  
```
**Flag** của bài này là:
`W1{so_you_know_about_the_Diffie-Hellman-key_exchange}`