Hi, Can you speak Thai ?
Yes, I can speak Thai.
มา วันนี้เราจะมาทำข้อ Hidden in plainsight หมวด Forensics 

Step 1 สร้าง Directory และนำเข้าไฟล์
ใช้คำสั่ง mkdir ใช้สำหรับสร้างไดเรกทอรี (หรือโฟลเดอร์) ใหม่บนระบบไฟล์
```
mkdir <new directory>
```
ตามด้วย คำสั่ง cd ใช้สำหรับเปลี่ยนไดเรกทอรีหรือโฟลเดอร์ปัจจุบันในเทอร์มินัล
```
cd <new directory>
```
ตามด้วย คำสั่ง wget ใช้สำหรับดาวน์โหลดไฟล์จากอินเทอร์เน็ตโดยตรงผ่านบรรทัดคำสั่ง
```
┌──(kali㉿kali)-[~]
└─$ mkdir isus01    

┌──(kali㉿kali)-[~]
└─$ cd isus01      

┌──(kali㉿kali)-[~/isus01]
└─$ wget https://challenge-files.picoctf.net/c_saffron_estate/e40e3ca60def2457974330549c0ebc3248ae75bc4f6ca700b85fca2c6be00083/img.jpg   
--2025-10-16 09:22:09--  https://challenge-files.picoctf.net/c_saffron_estate/e40e3ca60def2457974330549c0ebc3248ae75bc4f6ca700b85fca2c6be00083/img.jpg
Resolving challenge-files.picoctf.net (challenge-files.picoctf.net)... 18.239.134.85, 18.239.134.106, 18.239.134.126, ...
Connecting to challenge-files.picoctf.net (challenge-files.picoctf.net)|18.239.134.85|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 73503 (72K) [application/octet-stream]
Saving to: ‘img.jpg’

img.jpg                 100%[============================>]  71.78K   317KB/s    in 0.2s    

2025-10-16 09:22:10 (317 KB/s) - ‘img.jpg’ saved [73503/73503]
```

Step 2 exiftool
ใช้คำสั่ง exiftool เราจะใช้มันสำหรับอ่านข้อมูลเมตา
```
┌──(kali㉿kali)-[~/isus01]
└─$ exiftool img.jpg         
ExifTool Version Number         : 13.10
File Name                       : img.jpg
Directory                       : .
File Size                       : 74 kB
File Modification Date/Time     : 2025:09:29 17:29:24-04:00
File Access Date/Time           : 2025:10:16 09:22:10-04:00
File Inode Change Date/Time     : 2025:10:16 09:22:10-04:00
File Permissions                : -rw-rw-r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
Comment                         : c3RlZ2hpZGU6Y0VGNmVuZHZjbVE9
Image Width                     : 640
Image Height                    : 640
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 640x640
Megapixels                      : 0.410
```
สังเกตตรง Comment ลักษณะแบบนี้ต้องถอดรหัสด้วย Base64 โดยการเข้า CyberChef
<img width="1920" height="1080" alt="Screenshot 2025-10-16 203655 (1)" src="https://github.com/user-attachments/assets/e2d96c84-336f-4843-b952-0469c152e16f" />
หรือใช้คำสั่ง echo บน kali linux
```
echo "c3RlZ2hpZGU6Y0VGNmVuZHZjbVE9" | base64 -d
```
จะได้
```
┌──(kali㉿kali)-[~/isus01]
└─$ echo "c3RlZ2hpZGU6Y0VGNmVuZHZjbVE9" | base64 -d                    
steghide:cEF6endvcmQ=
```
เอา cEF6endvcmQ= มาถอดรหัสด้วย base64 อีกที โดยการเข้า CyberChef หรือใช้คำสั่งบน kali ก็ได้ จะได้
```
┌──(kali㉿kali)-[~/isus01]
└─$ echo "cEF6endvcmQ=" | base64 -d                
pAzzword
```

Step 3 Steghide
```
steghide --extract -sf img.jpg -p pAzzword
```
ความหมายของแต่ละส่วน
• steghide
  โปรแกรมสำหรับซ่อน/ดึงข้อมูลจากไฟล์ (steganography tool) — รองรับไฟล์เช่น JPEG, WAV, BMP, AU เป็นต้น
• --extract
  บอก steghide ว่าให้ดึง (extract) ข้อมูลที่ถูกซ่อนไว้ในไฟล์เป้าหมายออกมา
• -sf img.jpg
  -sf ย่อมาจาก stegofile — ระบุชื่อไฟล์ที่มีข้อมูลถูกซ่อนอยู่ ในที่นี้คือ img.jpg
• -p pAzzword
  ระบุรหัสผ่าน (passphrase) ที่ใช้สำหรับถอดข้อมูล (ถ้าขณะฝังไฟล์ตอนแรกคนเขาใส่ passphrase ไว้ ต้องใส่ให้ถูกต้อง)
```
┌──(kali㉿kali)-[~/isus01]
└─$ steghide --extract -sf img.jpg -p pAzzword 
wrote extracted data to "flag.txt".
```
จากนั้นก็ใช้คำสั่ง cat เราจะใช้มันในการใช้แสดงเนื้อหาของไฟล์
```
┌──(kali㉿kali)-[~/isus01]
└─$ cat flag.txt             
picoCTF{XXXXX}
```
XXXXX ไม่ใช้ flag นะ คือปิดไว้ จะได้ทำเอง

เพิ่มเติม
- ที่ใช้ exiftool เพราะ Hints บอกว่า "Download the jpg image and read its metadata" แปลว่า "ดาวน์โหลดภาพ jpg และอ่านข้อมูลเมตา"
- ที่ใช้ steghide เพราะถอดรหัสจาก "c3RlZ2hpZGU6Y0VGNmVuZHZjbVE9" แล้วได้ "steghide:cEF6endvcmQ=" เราเลยรู้ว่ามีการใช้ steghide
