---
title: '[PICOCTF - FORENSICS] - Medium Level'

---

**MỤC LỤC:**

[TOC]

# shark on wire 1
**Description**
We found this [packet capture](https:jupiter.challenges.picoctf.org/static/483e50268fe7e015c49caf51a69063d0/capture.pcap). Recover the flag.
**Cách giải:**
- Đề bài cung cấp cho chúng ta một file pcap nên việc đầu tiên ta làm đó chính là phân tích file này trên WireShark
- Thường thì chúng ta sẽ **quan tâm đến giao thức TCP và UDP.**
**Phân tích giao thức TCP:**
- Khi ta phân tích giao thức TCP, ta chọn follow the stream:
![image](https://hackmd.io/_uploads/BkMJ8Wcdkg.png)
- Khi ta chuyển stream id qua 10 cái tất cả thì ta không tìm thấy điều gì cả.
![image](https://hackmd.io/_uploads/ryGW8bq_Jl.png)
Nên ta sẽ chuyển qua giao thức UDP:
**Phân tích giao thức UDP:**
- Khi ta phân tích giao thức UDP, ta chọn follow the stream:
![image](https://hackmd.io/_uploads/Byq_8b5Oyg.png)
- ta chọn show as ASCII để thử tìm flag.
![image](https://hackmd.io/_uploads/SysjU-cOye.png)
- Khi ta kiểm tra đến stream thứ 6 thì ta thấy:
![image](https://hackmd.io/_uploads/r1Ogvb9Oyx.png)
**Flag**: picoCTF{StaT31355_636f6e6e}

# Wireshark doo dooo do doo...
**Description**
Can you find the flag? [shark1.pcapng](https://mercury.picoctf.net/static/b44842413a0834f4a3619e5f5e629d05/shark1.pcapng).
**Phân tích TCP**:
- Khi ta phân tích giao thức TCP, ta chọn follow the stream:
![image](https://hackmd.io/_uploads/r1M2qb9uke.png)
- Khi ta dò tìm trong các stream thì ta thấy tại stream thứ 5 có dòng chữ `Gur synt vf cvpbPGS{c33xno00_1_f33_h_qrnqorrs}` có vẻ như là flag đã bị mã hóa bởi [**rot13**](https://privacycanada.net/rot13-cipher/#:~:text=Rot13%20is%20actually%20pretty%20simple,%E2%98%91%EF%B8%8F%20B%20becomes%20O).
- Ta thử decode trên **CyberChef** và nhận được kết quả như sau:
![image](https://hackmd.io/_uploads/B1oTs-9_ye.png)
**Flag**: picoCTF{p33kab00_1_s33_u_deadbeef}
# Matryoshka doll
**Description**
Matryoshka dolls are a set of wooden dolls of decreasing size placed one inside another. What's the final one? Image: [this](https://mercury.picoctf.net/static/b6205dd933ec01c022c4e6acbdf11116/dolls.jpg)
**Hints**:
Wait, you can hide files inside files? But how do you find them?
**Cách giải bài**:
- Dựa vào gợi ý đề bài đưa ra nên ta đã tìm cách làm thế nào có thể tìm được files ẩn trong một ảnh.
- Ta đã tìm được một số cách trong bài viết [Beginners CTF Guide: Finding Hidden Data in Images](https://infosecwriteups.com/beginners-ctf-guide-finding-hidden-data-in-images-e3be9e34ae0d) và ta quyết định sẽ sử dụng **binwalk** để tìm file ẩn trong ảnh.
- Đầu tiên ta tải tools bằng câu lệnh` sudo apt install binwalk -y`. Sau đó, ta thực hiện theo hướng dẫn để tìm file ẩn trong ảnh dolls.jpg.
![image](https://hackmd.io/_uploads/rJACqfcOkl.png)
- Sau đó ta sử dụng câu lệnh để giải nén **`binwalk -e --run-as=root dolls.jpg`**
![image](https://hackmd.io/_uploads/Hy1QoGqdJe.png)
- Sau khi giải nén thì ta tìm thấy thư mục `base_images`. Trong thư mục lại có hình ảnh `2_c.jpg` và vẫn chưa tìm được flag nên ta tiếp tục thực hiện công việc tìm file ẩn trong `2_c.jpg` xem có hay không. 
![image](https://hackmd.io/_uploads/H1N1hzqu1x.png)
- Tiếp theo đó thì ta lại thấy tệp hình ảnh `3_c.jpg`, vì vậy nên ta cứ tiếp tục tìm file ẩn trong ảnh cho ta khi ta tìm được file `flag.txt`
![image](https://hackmd.io/_uploads/SJoV6Mqdye.png)
- Khi xem file này thì ta nhận flag.
**Flag**: 
`picoCTF{4f11048e83ffc7d342a15bd2309b47de}`
# like1000
**Description:**
This [.tar file](https://jupiter.challenges.picoctf.org/static/52084b5ad360b25f9af83933114324e0/1000.tar) got tarred a lot.
**Cách giải**:
-ta thử giải nén file 1000.tar thì ta được như sau:
![image](https://hackmd.io/_uploads/SkFnxN5uye.png)
- Vì vậy ta là file đã nén 1000 lần như cái tên của bài đã nói lên. Việc giải nén tay sẽ mất rất nhiều thời gian nên ta đã search cách để giải nén file bằng Python và viết đoạn mã như sau:
```python=
import tarfile 

for i in range(998, 0, -1): 
	filename = str(i) + '.tar' 
	tar = tarfile.open(filename) 
	tar.extractall()
	tar.close() 
```
![image](https://hackmd.io/_uploads/ByODbN9_Jg.png)
- Khi thấy `1.tar` ta đã giải nén: 
![image](https://hackmd.io/_uploads/Bk2OZNqdye.png)
Và ta tìm được flag qua file flag.png:
![image](https://hackmd.io/_uploads/rJ2qWN9dkx.png)
**Flag**: picoCTF{l0t5_0f_TAR5}
# c0rrupt
**Description:**
We found this [file](https://jupiter.challenges.picoctf.org/static/ab30fcb7d47364b4190a7d3d40edb551/mystery). Recover the flag.

**Cách giải:**
![image](https://hackmd.io/_uploads/H1LEGviOJx.png)
- Khi ta sử dụng lệnh `file` thì kết quả là data nên ta không thể biết đây là file loại gì nên ta phân tích file trên [hex editor](https://hexed.it/).
- Khi ta phân tích thì ta để ý phần header có vẻ giống phần header của 1 file **PNG**. [File signature của **PNG**](https://medium.com/@0xwan/png-structure-for-beginner-8363ce2a9f73) có dạng như sau: `89 50 4E 47 0D 0A 1A 0A`. 
- Tiếp theo signature là **IHDR chunk** nên ta sửa lại cho đúng.
![image](https://hackmd.io/_uploads/rJAi7Douke.png)
- Khi `pngcheck` thì ta thấy có 2 errors xuất hiện như sau:
![image](https://hackmd.io/_uploads/rkwb4Djd1l.png)
- ` chunk pHYs at offset 0x00042, length 9: 2852132389x5669 pixels/meter` nhìn vào đây ta thấy có vẻ kích thước `2852132389` rất lớn so với `5669`.
- Cấu trúc của [pHYs](http://www.libpng.org/pub/png/spec/1.2/PNG-Chunks.html) như sau:
![image](https://hackmd.io/_uploads/r1FvNPiOkg.png)
- **pHYs** chỉ định kích thước pixel hoặc tỷ lệ khung hình mong muốn để hiển thị hình ảnh. 
![image](https://hackmd.io/_uploads/ByYESvouJx.png)
- X axis: `aa 00 16 25`, Y axis: `00 00 16 25`, Unit specifier: `01`.
- Nhìn vào đây ta thấy giá trị `aa 00 16 25` là rất lớn nên ta thấy `aa 00 16 25` thành `00 00 16 25`.
- Tiếp tục `pngcheck`:
![image](https://hackmd.io/_uploads/SkuvLwiukg.png)
- Ta thấy vẫn còn lỗi là `invalid chunk length (too large)` mà ta không biết là chunk nào.
- Nhìn vào hex editor ta thấy:
![image](https://hackmd.io/_uploads/SkDlvPodJg.png)
- Nhìn vào ta thấy có vẻ đây là **IDAT chunk**. Cấu trúc của chunk như sau:
![image](https://hackmd.io/_uploads/BJfBwvsd1e.png)
- Trước `chunk type` là `size of chunk data`. Ta thấy size là `aa aa ff a5` (nghĩa là 2863333285 trong hệ 10) rất lớn. Nên ta thử thay `aa aa` thành `00 00`. 
- `pngcheck` ta thấy không còn lỗi `No errors detected in mystery_fix3 (9 chunks, 96.3% compression).`
- Mở file ta thấy flag.
![mystery_fix3](https://hackmd.io/_uploads/rk1NFvjd1x.png)
**FLag:** `picoCTF{c0rrupt10n_1847995}`
# What Lies Within
**Description:**
There's something in the [building](https://jupiter.challenges.picoctf.org/static/011955b303f293d60c8116e6a4c5c84f/buildings.png). Can you retrieve the flag?
**Cách giải**:
- Flag được ẩn trong bit ít quan trọng nhất của mỗi giá trị pixel.
- **`zsteg`** là một công cụ dùng để tìm dữ liệu ẩn trong ảnh **định dạng PNG** thông qua kỹ thuật LSB (Least Significant Bit) Steganography.Còn đối với **JPG** thì là **`steghide`**.
![image](https://hackmd.io/_uploads/r12xBcjdkg.png)
**Flag:**
`picoCTF{h1d1ng_1n_th3_b1t5}`
# Trivial Flag Transfer Protocol
**Description:**
Figure out how they moved the [flag.](https://mercury.picoctf.net/static/e4836d9bcc740d457f4331d68129a0bc/tftp.pcapng)

**Cách giải:**
- Đề bài cung cấp file `tftp.pcap` nên mở file trong `WireShark` để phân tích file.
- Thường thì đề bài có thể sẽ ẩn flag trong các luồng **TCP** hay **UDP** nên ta sẽ thử tìm thử xem.
![image](https://hackmd.io/_uploads/r16-O5jOyg.png)
- Lướt qua một số stream thì ta thấy như sau:
![image](https://hackmd.io/_uploads/HJ68OciOye.png)
- Vì vậy nên ta đoán rằng là ta có thể export file.
![image](https://hackmd.io/_uploads/SyrsOcsdJg.png)
- Quả thật có file ta có thể tải về thử tất cả xem để nghiên cứu xem sao:
![image](https://hackmd.io/_uploads/rJvAd5i_Jl.png)
- Trong các file tải về có file `instruction.txt` đáng ngờ nên ta thử mời lên xem và thấy nội dung là `GSGCQBRFAGRAPELCGBHEGENSSVPFBJRZHFGQVFTHVFRBHESYNTGENAFSRE.SVTHERBHGNJNLGBUVQRGURSYNTNAQVJVYYPURPXONPXSBEGURCYNA
` nhìn có vẻ như là một thông điệp gì đó đã bị mã hóa `rot13` nên ta giải mã và nhận được: `TFTPDOESNTENCRYPTOURTRAFFICSOWEMUSTDISGUISEOURFLAGTRANSFER.FIGUREOUTAWAYTOHIDETHEFLAGANDIWILLCHECKBACKFORTHEPLAN`. 
- Nhìn thì có vẻ vô nghĩa nhỉ? Nhưng mà khi ta nhìn kỹ lại thì: `TFTP DOESNT ENCRYPT OUR TRAFFIC SOWE MUST DISGUISE OUR FLAG TRANSFER. FIGURE OUT AWAY  TO HIDE THE FLAGAND I WILL CHECK BACK FOR THE PLAN`
- Câu trên có nhắc tới `plan` làm ta nghĩ đến ngay file `plan.txt`.
- Trong các file tải về có file `instruction.txt` đáng ngờ nên ta thử mời lên xem và thấy nội dung là `VHFRQGURCEBTENZNAQUVQVGJVGU-QHRQVYVTRAPR.PURPXBHGGURCUBGBF` nhìn có vẻ như là một thông điệp gì đó đã bị mã hóa `rot13` nên ta giải mã và nhận được: `IUSEDTHEPROGRAMANDHIDITWITH-DUEDILIGENCE.CHECKOUTTHEPHOTOS`. 
- Câu giải mã có thể hiểu: `I USED THE PROGRAM AND HID IT WITH - DUEDILIGENCE. CHECK OUT THE PHOTO`.
- Câu này đã đưa cho chúng ta gợi ý rằng flag được giấu trong 3 bức ảnh mà chúng ta nhận được khi nãy.
- Để tìm flag đã bị ẩn trong ảnh dạng `.bmp` ta có thể sử dụng câu lệnh `steghide`.
![image](https://hackmd.io/_uploads/SJmbsco_1e.png)
**Flag:**
`picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}`
# Eavesdrop
**Description:**
Download this packet capture and find the flag.
- [Download packet capture](https://artifacts.picoctf.net/c/134/capture.flag.pcap)

**Cách giải:**
- Thường thì flag ẩn sẽ liên quan đến **TCP** hoặc **UDP**
- **Phân tích stream TCP**:
![image](https://hackmd.io/_uploads/ryxLaa2s_kg.png)
- Hiện lên đoạn hội thoại như sau. Phân tích đoạn hội thoại ta thấy cách để **decrypt** là sử dụng câu lệnh ` openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123` từ `file.des3` ta sẽ nhận được là `file.txt`.
- Câu lệnh cho thấy file đã được mã hóa bằng thuật toán **Triple DES (DES3)** và được giải mã bằng `openssl`.
- Cờ `-d` chỉ ra đây là quá trình giải mã.
- `-salt` giúp tăng cường bảo mật bằng cách sử dụng muối (salt) để tránh các cuộc tấn công dựa trên từ điển.
- `-k supersecretpassword123` cung cấp khóa để giải mã.
- Truyền qua `9002`: Gợi ý rằng họ đang truyền `file` trực tiếp (có thể qua `TCP/UDP`) mà không cần máy chủ trung gian.
**Note:** Sử dụng **DES3** không còn an toàn vì **Triple DES** đã bị loại bỏ khỏi tiêu chuẩn bảo mật hiện đại (thay vào đó nên dùng **AES**).
- Vì vậy **nên**: để tìm file thì ta cần tìm port `9002`. Ta sử dụng filter bằng câu lệnh **`tcp.port == 9002`**.
![image](https://hackmd.io/_uploads/S1-Kk6odyx.png)
- Ta thấy truyền đi 1 file 74 bytes. Chọn follow the stream rồi chuyển dạng `raw` rồi lưu `file.des3`.
![image](https://hackmd.io/_uploads/rJU6Jao_1l.png)
- Sau khi sử dụng câu lệnh ta có thì ta giải mã được `file.txt`:
![image](https://hackmd.io/_uploads/Byvfgao_1x.png)
**Flag**:
`picoCTF{nc_73115_411_dd54ab67}`

# Packets Primer
**Description:**
Download the packet capture file and use packet analysis software to find the flag.
- [Download packet capture](https://artifacts.picoctf.net/c/195/network-dump.flag.pcap)

**Cách giải**:
- Thông thường thì ta nên xem thử phân tích stream của **TCP** và **UDP**
**Phân tích TCP**:
- Ta thấy ngay flag.
![image](https://hackmd.io/_uploads/S1dA-pi_1e.png)
**Flag**:
`picoCTF{p4ck37_5h4rk_b9d53765}`

# Lookey here
**Description:**
Attackers have hidden information in a very large mass of data in the past, maybe they are still doing it.
Download the data [here.](https://artifacts.picoctf.net/c/125/anthem.flag.txt)
**Cách giải**:
- Đề bài cung cấp cho ta một file văn bản nên ta thử `ctrl + F` "picoCTF" thì ta tìm được flag.

**Flag:**
`picoCTF{gr3p_15_@w3s0m3_58f5c024}`

# Redaction gone wrong
**Description:**
Now you DON’T see me.
This [report](https://artifacts.picoctf.net/c/84/Financial_Report_for_ABC_Labs.pdf) has some critical data in it, some of which have been redacted correctly, while some were not. Can you find an important key that was not redacted properly?

**Cách giải**:
- Đề bài cung cấp cho ta một file pdf có nội dung như sau:
![image](https://hackmd.io/_uploads/By8SIsh_1g.png)
- Ta sử dụng lệnh **`pdftotext [options] <PDF-file> [<text-file>]`** để chuyển pdf sang văn bản xem sao.
![image](https://hackmd.io/_uploads/B1u9LihO1g.png)
- Sử dụng lệnh `grep`:
![image](https://hackmd.io/_uploads/SJDnUjnukl.png)

**Flag:**
`picoCTF{C4n_Y0u_S33_m3_fully}`
# advanced-potion-making
**Description:**
Ron just found his own copy of advanced potion making, but its been corrupted by some kind of spell. Help him recover it!

**Cách giải:**
- Thử sử dụng lệnh file để xem file loại gì nhưng chỉ thấy là `data` nên ta nghĩ nó có thể bị hỏng phần `header`.
![image](https://hackmd.io/_uploads/By2Rrnh_kg.png)
- Khi sử dụng `hex editor` thì ta thấy phần **PNG** signature đã bị hỏng và kích thước của **IHDR chunk** không đúng.
![image](https://hackmd.io/_uploads/HkKGS32d1g.png)
- Ta sửa lại như dưới đây:
![image](https://hackmd.io/_uploads/rkkd8n2_Jx.png)
- Sử dụng `pngcheck` thử xem còn lỗi nào không:
![image](https://hackmd.io/_uploads/SJd2Ihhukx.png)
- Khi ta mở bức ảnh lên xem nó chỉ toàn là màu đỏ:
![advanced-potion-making_4](https://hackmd.io/_uploads/BJI1D2nd1x.png)
- Sau đó ta thử `zsteg`, `binwalk` nhưng không tìm thấy thêm gì cả nên ta thử sử dụng [tools](https://aperisolve.fr/) để phân tích và thay đổi màu:
![image](https://hackmd.io/_uploads/Sy2ud2hu1l.png)
**Flag:**
`picoCTF{w1z4rdry}`

# Enhance!
**Description:**
Download this image file and find the flag.
[Download image file](https://artifacts.picoctf.net/c/101/drawing.flag.svg)
**Cách giải:**
- Kiểm tra loại file:
![image](https://hackmd.io/_uploads/HkPtpa3d1e.png)
- Đây là lần đầu ta biết đến `file svg` nên ta đã thử tra và có thể tóm tắt như sau: **[SVG (Scalable Vector Graphics)](https://www.w3schools.com/graphics/svg_intro.asp)** là định dạng hình ảnh dựa trên XML dùng để hiển thị đồ họa vector.
- Ta tra thử cách để phân tích `file svg` và ta tìm thấy [trang web](https://www.svgviewer.dev/) này. Khi phân tích ta thấy được đoạn mã **XML** như sau:
```xml=
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<!-- Created with Inkscape (http://www.inkscape.org/) -->

<svg
   xmlns:dc="http://purl.org/dc/elements/1.1/"
   xmlns:cc="http://creativecommons.org/ns#"
   xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
   xmlns:svg="http://www.w3.org/2000/svg"
   xmlns="http://www.w3.org/2000/svg"
   xmlns:sodipodi="http://sodipodi.sourceforge.net/DTD/sodipodi-0.dtd"
   xmlns:inkscape="http://www.inkscape.org/namespaces/inkscape"
   width="210mm"
   height="297mm"
   viewBox="0 0 210 297"
   version="1.1"
   id="svg8"
   inkscape:version="0.92.5 (2060ec1f9f, 2020-04-08)"
   sodipodi:docname="drawing.svg">
  <defs
     id="defs2" />
  <sodipodi:namedview
     id="base"
     pagecolor="#ffffff"
     bordercolor="#666666"
     borderopacity="1.0"
     inkscape:pageopacity="0.0"
     inkscape:pageshadow="2"
     inkscape:zoom="0.69833333"
     inkscape:cx="400"
     inkscape:cy="538.41159"
     inkscape:document-units="mm"
     inkscape:current-layer="layer1"
     showgrid="false"
     inkscape:window-width="1872"
     inkscape:window-height="1016"
     inkscape:window-x="48"
     inkscape:window-y="27"
     inkscape:window-maximized="1" />
  <metadata
     id="metadata5">
    <rdf:RDF>
      <cc:Work
         rdf:about="">
        <dc:format>image/svg+xml</dc:format>
        <dc:type
           rdf:resource="http://purl.org/dc/dcmitype/StillImage" />
        <dc:title></dc:title>
      </cc:Work>
    </rdf:RDF>
  </metadata>
  <g
     inkscape:label="Layer 1"
     inkscape:groupmode="layer"
     id="layer1">
    <ellipse
       id="path3713"
       cx="106.2122"
       cy="134.47203"
       rx="102.05357"
       ry="99.029755"
       style="stroke-width:0.26458332" />
    <circle
       style="fill:#ffffff;stroke-width:0.26458332"
       id="path3717"
       cx="107.59055"
       cy="132.30211"
       r="3.3341289" />
    <ellipse
       style="fill:#000000;stroke-width:0.26458332"
       id="path3719"
       cx="107.45217"
       cy="132.10078"
       rx="0.027842503"
       ry="0.031820003" />
    <text
       xml:space="preserve"
       style="font-style:normal;font-weight:normal;font-size:0.00352781px;line-height:1.25;font-family:sans-serif;letter-spacing:0px;word-spacing:0px;fill:#ffffff;fill-opacity:1;stroke:none;stroke-width:0.26458332;"
       x="107.43014"
       y="132.08501"
       id="text3723"><tspan
         sodipodi:role="line"
         x="107.43014"
         y="132.08501"
         style="font-size:0.00352781px;line-height:1.25;fill:#ffffff;stroke-width:0.26458332;"
         id="tspan3748">p </tspan><tspan
         sodipodi:role="line"
         x="107.43014"
         y="132.08942"
         style="font-size:0.00352781px;line-height:1.25;fill:#ffffff;stroke-width:0.26458332;"
         id="tspan3754">i </tspan><tspan
         sodipodi:role="line"
         x="107.43014"
         y="132.09383"
         style="font-size:0.00352781px;line-height:1.25;fill:#ffffff;stroke-width:0.26458332;"
         id="tspan3756">c </tspan><tspan
         sodipodi:role="line"
         x="107.43014"
         y="132.09824"
         style="font-size:0.00352781px;line-height:1.25;fill:#ffffff;stroke-width:0.26458332;"
         id="tspan3758">o </tspan><tspan
         sodipodi:role="line"
         x="107.43014"
         y="132.10265"
         style="font-size:0.00352781px;line-height:1.25;fill:#ffffff;stroke-width:0.26458332;"
         id="tspan3760">C </tspan><tspan
         sodipodi:role="line"
         x="107.43014"
         y="132.10706"
         style="font-size:0.00352781px;line-height:1.25;fill:#ffffff;stroke-width:0.26458332;"
         id="tspan3762">T </tspan><tspan
         sodipodi:role="line"
         x="107.43014"
         y="132.11147"
         style="font-size:0.00352781px;line-height:1.25;fill:#ffffff;stroke-width:0.26458332;"
         id="tspan3764">F { 3 n h 4 n </tspan><tspan
         sodipodi:role="line"
         x="107.43014"
         y="132.11588"
         style="font-size:0.00352781px;line-height:1.25;fill:#ffffff;stroke-width:0.26458332;"
         id="tspan3752">c 3 d _ 2 4 3 7 4 6 7 5 }</tspan></text>
  </g>
</svg>
```
- Đoạn mã khá dài nhưng ta có thể thấy được flag ngay trong đoạn mã **XML**.

**Flag:**
`picoCTF{3nh4nc3d_24374675}`

# File types
**Description:**
This file was found among some files marked confidential but my pdf reader cannot read it, maybe yours can.
You can download the file from [here](https://artifacts.picoctf.net/c/82/Flag.pdf).

**Cách giải:**
- Ban đầu ta được cung cấp file `Flag.pdf` nhưng mà như đề bài cũng có nói là ta không mở được. 
- Khi ta sử dụng lệnh `file` thì thực ra file đề cho là ` shell archive text `.

**Note:**
*Shell Archive (shar file)* là một định dạng tập tin chứa nhiều file được đóng gói trong một script shell *(Bash script)*. 
- Sử dụng lệnh `sh Flag.shar` để giải nén file.
- Sau khi giải nén thì ta lại nhận được file **`current ar archive`** (một archive file (tệp lưu trữ) theo định dạng AR (Unix Archive)).
- Để giải nén file từ archive AR: **`ar x flag`**
- Sau khi giải nén thì ta lại nhận được file `cpio archive` (một CPIO archive, CPIO là một công cụ và định dạng lưu trữ phổ biến trên hệ thống Unix/Linux, dùng để đóng gói và giải nén các file.).
- Để giải nén: **`cpio -idu < flag`**
- Sau khi giải nén thì ta lại nhận được file `bzip2 compressed data, block size = 900k`.

**Tóm tắt cách giải:** Ta phải giải nén liên tục nhiều lần cho đến khi nhận được file là `ASCII text` với nội dung là `7069636f4354467b66316c656e406d335f6d406e3170756c407431306e5f
6630725f3062326375723137795f39353063346665657d0a`.
- Chuỗi nhận được là một chuỗi hex (hệ thập lục phân). Để chuyển nó thành dạng văn bản ASCII, ta cần thực hiện việc chuyển đổi từ hex sang ASCII.
- **`xxd -r -p`: Chuyển đổi chuỗi hex sang văn bản ASCII.**
![image](https://hackmd.io/_uploads/rkyxoC2uyx.png)

**Flag:**
`picoCTF{f1len@m3_m@n1pul@t10n_f0r_0b2cur17y_950c4fee}`
# St3g0
**Description:**
Download this image and find the flag.
- [Download image](https://artifacts.picoctf.net/c/216/pico.flag.png)

**Cách giải:**
- Đề bài cung cấp cho ta một file `png` và cả tên đề bài là **St3g0** hay `Steg` làm ta nghĩ ngay đến sử dụng `zsteg`.
![image](https://hackmd.io/_uploads/rk789Mp_ke.png)
**Flag:**
`picoCTF{7h3r3_15_n0_5p00n_a1062667}`
# Disk, disk, sleuth
**Description:**
Use `srch_strings` from the sleuthkit and some terminal-fu to find a flag in this disk image: [dds1-alpine.flag.img.gz](https://mercury.picoctf.net/static/2f998eee12730cf5766624681212a441/dds1-alpine.flag.img.gz)

**Cách giải:**
- Sử dụng lệnh [**srch_strings**](https://manpages.debian.org/jessie/sleuthkit/srch_strings.1) để hiển thị các chuỗi có thể in được trong các tệp và kết hợp lệnh `grep` để tìm kiếm.
![image](https://hackmd.io/_uploads/BJ8c2ZpOkx.png)
**Flag:**
`picoCTF{f0r3ns1c4t0r_n30phyt3_267e38f6}`

# hideme
**Description:**
Every file gets a flag.
The SOC analyst saw one image been sent back and forth between two people. They decided to investigate and found out that there was more than what meets the eye [here](https://artifacts.picoctf.net/c/260/flag.png).

**Cách giải:**
- Đầu tiên ta thử sử dụng `zsteg` nhưng không thu được điều gì.
![image](https://hackmd.io/_uploads/SJ4p2Mauyl.png)
- Tiếp theo, ta sử dụng `binwalk` để tìm file ẩn và thật sự có file ẩn trong ảnh.
![image](https://hackmd.io/_uploads/Sy5xTMaOyx.png)
- Tiếp đến ta sử dụng câu lệnh `binwalk  -e --run-as=root flag.png` để giải nén.
![image](https://hackmd.io/_uploads/B1ZL6faO1e.png)
- ta thấy thư mục tên là `secret` và bên trong chứa ảnh `flag.png`. Mở ảnh lên ta thấy `flag`.

**Flag:**
`picoCTF{Hiddinng_An_imag3_within_@n_ima9e_ad9f6587}`

# tunn3l v1s10n
**Description:**
We found this [file](https://mercury.picoctf.net/static/da18eed3d15fd04f7b076bdcecf15b27/tunn3l_v1s10n). Recover the flag.

**Cách giải:**
- Thử sử dụng lệnh `file` và `exiftool` thì ta có kết quả như sau:
![image](https://hackmd.io/_uploads/SkI8WE0O1g.png)
- Đây là một file `.bmp` nhưng lại mở lên không được nên ta nghĩ file đã bị hỏng ở đâu đó nên ta mở `hex editor` lên để phân tích.
![image](https://hackmd.io/_uploads/S1gCZ4CO1e.png)
- Ta thấy file signature đã đúng rồi. Ta tra cứu [**BMP format**](https://en.wikipedia.org/wiki/BMP_file_format) như ảnh sau:
![image](https://hackmd.io/_uploads/BJL7G4ROye.png)
- Như ảnh thì ta có thể thấy là ở phần **DIB Header** thì `Number of bytes = 40` nghĩa là phải có giá trị hex là `28 00 00 00` trong khi:
![image](https://hackmd.io/_uploads/SkzofN0uke.png)
- Giá trị ta thấy lại là `0xD0BA = 53434` (do ta đang để chế độ là `little-endian` nên **LSB** sẽ là `BA`) ta cần sửa lại thành `28 00 00 00`. Ta thu được hình ảnh như sau:
![image](https://hackmd.io/_uploads/ry1YmNC_yl.png)
- Hình ảnh đã được khôi phục nhưng mà ta vẫn chưa thu được flag nên ta lại tiếp tục xem lại `hex editor`. Để ý thấy rằng [cấu trúc BMP](https://www.ece.ualberta.ca/~elliott/ee552/studentAppNotes/2003_w/misc/bmp_file_format/bmp_file_format.htm) sau file signature sẽ là file size:
![image](https://hackmd.io/_uploads/HyQxNVAuye.png)
- Nhìn vào editor ta thấy `filesize = 0x2C268E = 2893454` và để ý rằng phần `DataOffset = 0xD0BA = 53434` (Bình thường, nó là `54 byte (0x36)`, nghĩa là dữ liệu ảnh bắt đầu ngay sau phần header và thông tin metadata cơ bản. Nhưng trong trường hợp này, `DataOffset = 53434 byte (0xD0BA)`, tức là dữ liệu ảnh bắt đầu muộn hơn rất nhiều so với bình thường.)
- Vì vậy nên ảnh có thể đang hiển thị từ giữa thay vì từ đầu. Ta thử thay đổi chiều cao và chiều rộng để thử tìm flag.
- Image Width = 1134, Image Height = 306, ta thử thay đổi chiều cao trước. Vì `filesize = 0x2C268E = 2893454` nên ta có thể tính chiều cao như sau:
$$
\text{height} = \frac{(2893454 - 54)}{3 \times 1134} = 850
$$
- Vì vậy ta cần thay đổi `32 01` thành `52 03`:
![image](https://hackmd.io/_uploads/HyUGFN0_yl.png)
- Ta thu được ảnh sau:
![image](https://hackmd.io/_uploads/S1JlY4Cdyl.png)

**Flag:**
`picoCTF{qu1t3_a_v13w_2020}`

# Pitter, Patter, Platters
**Description:**
'Suspicious' is written all over this disk image. Download [suspicious.dd.sda1](https://jupiter.challenges.picoctf.org/static/ca14d110858381cb297c1d22d62391a3/suspicious.dd.sda1)

**Cách giải 1:**
- Sử dụng lệnh `file`:
![image](https://hackmd.io/_uploads/BkFn9U0uyg.png)
- Để phân tích `disk image` 
- Dùng `fls` để tìm inode của các file có thể chứa slack space:
![image](https://hackmd.io/_uploads/ry4mi8R_kg.png)
- Ta tìm thấy 1 tệp `suspicious.txt` ở node 12 nên dùng `icat` để xem nội dung của file:
![image](https://hackmd.io/_uploads/r1ryAICOJx.png)
- Ta tìm chuỗi `Nothing to see here! But you may want to look here` trong file `suspicious.dd.sda1` và hiển thị `offset` của chuỗi đó trong file.
![image](https://hackmd.io/_uploads/SyZKAICOyg.png)
-  Trích xuất và hiển thị `200 byte` dữ liệu từ `suspicious.dd.sda1`, bắt đầu từ vị trí offset `0x200400`. Ta thấy flag được viết ngược như ảnh trên.

**Cách giải 2:**
- Hints nhắc đến cho ta về `slack space`. `Slack Space` là phần dung lượng thừa trong một `cluster` (cụm) mà hệ thống file không sử dụng, nhưng có thể chứa dữ liệu còn sót lại từ các tệp đã bị xóa hoặc sửa đổi. 

**Note về disk image:**
`Disk image` bao gồm:
- Tệp và thư mục: Bao gồm tất cả dữ liệu được lưu trên ổ đĩa.
- Tệp khởi động (boot files): Dùng để giúp hệ điều hành khởi động.
- Bảng phân bổ tệp (FAT): Hệ thống quản lý vị trí của các tệp trên ổ đĩa.
- Slack space: Không gian chưa sử dụng trong các sector của ổ đĩa, có thể chứa dữ liệu còn sót lại.

**Phân tích disk image sử dụng Autopsy:**
- Đầu tiên ta cần tạo **New Case** sau đó vào phần **Add Data** và add file `suspicious.dd.sda1`.
- Vào phần cài đặt và chỉnh hiển thị `slack space` (vì Autopsy để mặc định là hide)
![image](https://hackmd.io/_uploads/HyoqlD0_yg.png)
- Ta thấy cờ được viết reversed:
![image](https://hackmd.io/_uploads/Hk4TlPR_kl.png)

**Flag:**
`picoCTF{b3_5t111_mL|_<3_dd9707cd}`

# PcapPoisoning

**Description:**
How about some hide and seek heh?
Download this [file](https://artifacts.picoctf.net/c/376/trace.pcap) and find the flag.

**Cách giải:**
- Phân tích bằng WhiteShark thì thấy chỗ đó có vẻ khả nghi nên ta đã xem thử sao.
![image](https://hackmd.io/_uploads/SJ4wuw0uyx.png)
- Kết quả là flag bị giấu trong packet:
![image](https://hackmd.io/_uploads/S1G6_wRdkx.png)
**Flag:**
`picoCTF{P64P_4N4L7S1S_SU55355FUL_f621fa37}`
# Sleuthkit Intro
**Description:**
Download the disk image and use `mmls` on it to find the size of the Linux partition. Connect to the remote checker service to check your answer and get the flag.
Note: if you are using the webshell, download and extract the disk image into /tmp not your home directory.
[Download disk image](https://artifacts.picoctf.net/c/164/disk.img.gz)
Additional details will be available after launching your challenge instance.

**Cách giải:**
- Sử dụng lệnh `mmls` (Displays the layout of a disk, including the unallocated spaces) như đề bài yêu cầu.
![image](https://hackmd.io/_uploads/BkVljvCu1l.png)
- Phân vùng Linux (`0x83`) bắt đầu từ sector `2048` và kết thúc tại sector `20479`, có Length là `202752 sectors`. Tức là phân vùng này chiếm **202752 sectors**, với mỗi sector có kích thước 512 byte. 
**Flag:**
`picoCTF{mm15_f7w!}`

# Sleuthkit Apprentice
**Description:**
Download this disk image and find the flag.
Note: if you are using the webshell, download and extract the disk image into /tmp not your home directory.
- [Download compressed disk image](https://artifacts.picoctf.net/c/138/disk.flag.img.gz)

**Cách giải:**
- Sử dụng **Autopsy** để phân tích:
![image](https://hackmd.io/_uploads/BJXmuO0_ke.png)

**Flag:**
`picoCTF{by73_5urf3r_2f22df38}`

# Disk, disk, sleuth! II
![image](https://hackmd.io/_uploads/Hkl42uCuyg.png)
- `fsstat -o 2048 dds2-alpine.flag.img` (`fsstat` trích xuất thông tin về file system của một phân vùng hoặc ảnh đĩa mà không cần phải mount nó)
![image](https://hackmd.io/_uploads/Sy9SnuAdJg.png)
- `fls -i raw -f ext3 -o 2048 -r dds2-alpine.flag.img| grep down-at-the-bottom` có nghĩa là:
    - `fls`: Dùng để liệt kê các tệp tin và thư mục từ ảnh đĩa forensics.
    - `-i raw`: Chỉ định định dạng của ảnh đĩa là raw image (tức là ảnh đĩa thô, không phải định dạng nén hoặc đặc biệt khác như AFF hay EWF).
    - `-f ext3`: Chỉ định hệ thống tập tin là ext3. Điều này giúp fls biết cách đọc cấu trúc dữ liệu trong ảnh đĩa.
    - `-o 2048`: Offset tính theo sector, nghĩa là hệ thống tập tin bắt đầu từ sector `2048`.
    - `-r`: Chế độ đệ quy (recursive), tức là liệt kê toàn bộ cây thư mục, **kể cả các tệp nằm sâu bên trong các thư mục con**.
    - `| grep down-at-the-bottom`: Lọc kết quả để tìm kiếm bất kỳ tệp tin hoặc thư mục nào có chứa chuỗi `down-at-the-bottom`.
- ` icat -i raw -f ext3 -o 2048 dds2-alpine.flag.img 18291` (Trích xuất nội dung của một tệp từ hệ thống tập tin trong disk image ở inode `18291`)
![image](https://hackmd.io/_uploads/ByIihdR_Jx.png)

**Flag:**
`picoCTF{f0r3ns1c4t0r_n0v1c3_db59daa5}`
# Mob psycho
**Description:**
Can you handle APKs?
Download the android apk [here](https://artifacts.picoctf.net/c_titan/140/mobpsycho.apk).

**Cách giải:**
- Đầu tiên, ta giải nén bằng câu lệnh `unzip mobpsycho -d mobpsycho_extracted`
![image](https://hackmd.io/_uploads/HJDi4BkK1x.png)
- Có nhiều thư mục và tập tin sau khi giải nén nhưng ta thấy thư mục `res` có vẻ là khả nghi nhất:
![image](https://hackmd.io/_uploads/BkhX8Byt1l.png)
- Khi vào thư mục `res` thì ta thấy có rất nhiều thư mục con khác:
![image](https://hackmd.io/_uploads/rJ8YUHyFJx.png)
- Sau khi kiểm tra qua vài thư mục thì tại thư mục `color` ta tìm thấy tệp flag bằng câu lệnh `ls -l | grep flag`:
![image](https://hackmd.io/_uploads/HJO6LryKyx.png)
- Nội dung file `flag.txt` và giải mã thì ta nhận được flag:
![image](https://hackmd.io/_uploads/S1RALS1Yke.png)

**Flag:**
`picoCTF{ax8mC0RU6ve_NX85l4ax8mCl_5e67ea5e}`

# FindAndOpen

**Description:**
Someone might have hidden the password in the trace file.
Find the key to unlock [this file](https://artifacts.picoctf.net/c/496/flag.zip). [This tracefile](https://artifacts.picoctf.net/c/496/dump.pcap) might be good to analyze.

**Cách giải:**
- Phân tích bằng `WireShark` thì ta có kết quả như sau:
![image](https://hackmd.io/_uploads/BJ7Q0I1t1e.png)
- Điều trên đưa cho ta gợi ý về việc tìm password trên `Ethernet`. ta lướt qua một lượt thì tìm được manh mối là có thể flag đã bị chia ra:
![image](https://hackmd.io/_uploads/ByqvA8ytJg.png)
- Có vẻ đây là văn bản đã bị mã hóa `base64` nên ta thử xem có tìm được flag từ đây không:
![image](https://hackmd.io/_uploads/ByYtRIyFJl.png)
- Kết quả giải mã là: 
![image](https://hackmd.io/_uploads/BJzkkw1Fkx.png)
- Có vẻ đây là một phần của flag, ta cố tìm phần còn lại trên `Ethernet` nhưng không có kết quả nên ta nghĩ là có thể đây chính là password để unzip file `flag.zip` ban đầu:
![image](https://hackmd.io/_uploads/H1IVkDJKJe.png)

**Flag:**
`picoCTF{R34DING_LOKd_fil56_succ3ss_5ed3a878}`




