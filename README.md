---
title: "MỘT SỐ VÍ DỤ LẬP TRÌNH HỢP NGỮ LC-3"
permalink: /
layout: default
header-includes: |
    \usepackage{tikz,pgfplots}
    \usepackage{fancyhdr}
    \pagestyle{fancy}
    \fancyhead[CO,CE]{This is fancy}
    \fancyfoot[CO,CE]{So is this}
    \fancyfoot[LE,RO]{\thepage}
---

## Trần Minh Nhật

Bộ môn Điện tử viễn thông, Khoa Điện - Điện tử

Trường Đại học Bách Khoa, ĐHQG TP.HCM

**1.  Nhắc lại về tập lệnh LC-3**

Tập lệnh LC-3 (LC-3 ISA) bao gồm 15 lệnh. Mỗi lệnh có chiều dài là 16 bit bao gồm 4 bit 
đầu là mã lệnh (opcode) và 12 bit sau là các toán hạng (operands). Tùy thuộc vào lệnh mà có 
thể có 1 hoặc nhiều toán hạng.

Có 5 cách định vị địa chỉ trong LC-3:

- Tức thời (immediate)

- Thanh ghi (register)

- PC-relative

- Gián tiếp (indirect)

- Base + offset

Có 3 nhóm lệnh trong LC-3, đó là nhóm lệnh thực thi (NOT, ADD, AND), nhóm lệnh di 
chuyển dữ liệu (LD, ST, LDI, STI, LDR, STR, LEA) và nhóm lệnh điều khiển (BR, 
JMP/RET, JSR/JSRR, RTI, TRAP).

**Ví dụ 1:**  Trừ hai thanh ghi R0 và R1, lưu 
kết quả vào thanh ghi R0.

```assembly 

NOT R1,R1
ADD R1,R1,#1
ADD R0,R0,R1

```
***

**Ví dụ 2:** Thực hiện phép OR hai thanh ghi 
R0 và R1, lưu kết quả vào thanh ghi R0.

```assembly 

NOT R0,R0
NOT R1,R1
AND R0,R0,R1
NOT R0,R0

```

***

**Ví dụ 3:** Chuyển dữ liệu từ thanh ghi R0 
sang thanh ghi R4


Cách 1: ```ADD R4,R0,#0```

Cách 2: ```AND R4,R0,#-1```

***

**Ví dụ 4:** Khởi tạo thanh ghi R2 với giá trị
ban đầu là 10.
```
AND R2,R2,0
ADD R2,R2,#10
```

Trong chương trình môn học, các bạn chỉ cần chú ý đến 2 lệnh điều khiển chính là lệnh rẽ
nhánh (BR) và lệnh TRAP. Các lệnh còn lại các bạn có thể xem thêm trong giáo trình.

LC-3 cung cấp 3 mã điều kiện (condition code) là N, Z, P. Sau mỗi lệnh có lưu dữ liệu vào 
thanh ghi DR, các mã điều kiện này sẽ thay đổi phụ thuộc vào kết quả trong thanh ghi DR 
vừa được lưu. Nếu kết quả là số dương thì cờ P được bật (P – positive), các cờ còn lại tắt. 
Tương tự cho trường hợp cờ Z (zero) và cờ N (negative).

***

**Ví dụ 5:** Sau lệnh ```AND R1,R1,#0``` thì dữ liệu được ghi vào thanh ghi DR là 0, như vậy cờ
Z được bật lên.

***

**Ví dụ 6:** Giả sử thanh ghi R1 đang chứa dữ liệu là ```#8```, sau lệnh ```ADD R1,R1,#-10``` thì dữ
liệu trong thanh ghi DR là ```#-2``` là số âm, như vậy cờ N sẽ được bật lên.

***

**Ví dụ 7:** Sau lệnh ```ST R2,#-10``` thì các cờ điều kiện sẽ không thay đổi. Lý do là vì lệnh ST 
không lưu dữ liệu vào thanh ghi.

Trong LC-3, những lệnh có thủ tục lưu dữ liệu vào thanh ghi đó là NOT, AND, ADD, LD, 
LDR, LDI, LEA, những lệnh còn lại chắc chắn không làm thay đổi các mã điều kiện. Dựa 
vào các mã điều kiện này, chúng ta có thể thiết lập rẽ nhánh tùy vào yêu cầu của đề. Lệnh để
rẽ nhánh là lệnh BR (opcode 0000). Ba bit tiếp theo để xác định điều kiện (n, z, p), 9 bit còn 
lại xác định độ dời PCoffset. Nếu bit 11 (bit ứng với mã lệnh n) bằng 1, PC sẽ nhảy khi cờ N 
được bật lên. Tương tự khi bit 10 và bit 9 bằng 1. Có thể có nhiều hơn 1 bit trong 3 bit n, z, p 
bằng 1, khi đó điều kiện sẽ là “hoặc”.

***

**Ví dụ 8:** Mã lệnh ```0000 011 000001000``` có ý nghĩa là nếu cờ Z hoặc cờ P bằng 1 thì câu 
lệnh tiếp theo thực thi sẽ nằm ở địa chỉ PC + #8.


***

**Ví dụ 9:** Viết chương trình bắt đầu từ x3010, kiểm tra nếu R0 dương thì nhảy đến câu lệnh ở
địa chỉ x3020.
```
x3010: ADD R0,R0,#0
x3011: BRp x0E
```
*Nhận xét:* Bởi vì ta chưa biết các cờ điều kiện trước đó ứng với thanh ghi nào cả, vì vậy không 
thể đặt lệnh x3010: BRp x0F được. Phải gọi 1 lệnh để set các cờ điều kiện dựa vào thanh 
ghi R0 trước, sau đó mới kiểm tra các cờ dùng lệnh BRp. Câu lệnh ở x3010 không có ý nghĩa 
thực thi, tuy nhiên có ý nghĩa set các cờ điều kiện theo thanh ghi mong muốn.

***

**Ví dụ 10:** So sánh hai thanh ghi R0 và R1, nếu thanh ghi R0 lớn hơn hoặc bằng R1 thì nhảy 
đến lệnh ở địa chỉ x3020. Chương trình bắt đầu từ x3050.

*Phân tích:* để so sánh ta sẽ phải dùng phép trừ hai thanh ghi.

```
x3050: NOT R1,R1
x3051: ADD R1,R1,#1
x3052: ADD R0,R1,R1
```

Sau lệnh vừa rồi, thanh ghi kết quả R0 đó chính là hiệu của R0 và R1 trước đó, vì 
vậy ta không cần phải gọi lệnh như ví dụ 9 nữa. Tiếp theo sẽ là lệnh ```x3053: BRzp PCoffset9```.
Để tính PC offset, ta cần tính độ dời bằng cách trừ thanh ghi đích cho thanh ghi PC. Chú ý là 
sau pha lấy lệnh thì PC được tăng thêm 1, như vậy ```PC = x3054```.
Lúc này ```PCoffset = x3020 – x3054 = xFFCC``` (bỏ qua việc bị tràn số), rút gọn lại 9 bit (vì lệnh 
này chỉ có 9 bit cho phần offset) là ```x1CC:
x3053: BRzp x1CC```
Một cách khác các bạn tính phép tính trên sẽ thu được ```PCoffset = x-34```, như vậy có thể viết 
lệnh:
```x3053: BRzp x-34```
Từ câu lệnh rẽ nhánh này, ta có thể sử dụng phương pháp lặp để giảm bớt khối lượng lập 
trình. Có hai phương pháp để lặp, cách thứ nhất là dùng biến đếm (tương ứng với vòng lặp 
for mà các bạn đã được học trước đây), cách còn lại là dùng trị canh (tương ứng với vòng lặp 
while). Các bạn có thể tham khảo thêm trong giáo trình. Trong bài viết này trình bày hai ví 
dụ về hai cách lặp.

**2. Một số ví dụ quan trọng lập trình hợp ngữ LC-3**

**Bài 1:** Viết chương trình hợp ngữ hoàn chỉnh thực hiện các yêu cầu sau:

- Nhập lần lượt hai số (từ 0 đến 9) từ bàn phím.

- Tính tổng của hai số này và in ra màn hình theo cú pháp: “Tong cua hai so la: XX”. Trong đó XX tương ứng với kết quả.

- Lưu kết quả này vào 3 ô nhớ tại địa chỉ x4000, x4001, x4002 theo thứ tự: số nhập thứ nhất, số nhập thứ hai, tổng của 2 số.

```
        .ORIG x3000
        IN 
        LD R1,ASCII_0 
        NOT R1,R1
        ADD R1,R1,#1
        ADD R2,R0,R1 
        IN
        ADD R3,R0,R1 
        ADD R4,R2,R3 
        LD R0,DIA_CHI_LUU 
        STR R2,R0,#0 
        STR R3,R0,#1
        STR R4,R0,#2
        LEA R0,STROUT 
        PUTS
        LD R1,ASCII_0
        ADD R5,R4,#-10 
        BRzp XUAT_2_CHU_SO
        ADD R0,R4,R1 
        OUT
        BR KET_THUC
XUAT_2_CHU_SO ADD R0,R1,#1 
        OUT
        ADD R0,R5,R1 
        OUT
KET_THUC HALT
ASCII_0 .FILL #48
DIA_CHI_LUU .FILL x4000
STROUT .STRINGZ “Tong cua hai so la: “
        .END


```


***

**Bài 2:** Viết chương trình thực hiện nhập 2 số (từ 0 đến 9) từ bàn phím, tính hiệu của 2 số
này và in ra màn hình.

*Phân tích:* Ta thấy rằng hiệu của 2 số 1 chữ số chắc chắn cũng chỉ có 1 chữ số. Tuy nhiên hiệu 
này có thể là số âm, vì vậy cần phải dùng câu lệnh rẽ nhánh. Khi gặp trường hợp số âm, ta in 
ra dấu “-“, sau đó chuyển kết quả thành số dương (bù 2) và in ra màn hình.

```
        .ORIG x3000
        IN
        ADD R1,R0,#0 
        IN
        NOT R0,R0
        ADD R0,R0,#1
        ADD R1,R1,R0 ;R1 := R1-R0
        BRzp IN_CHU_SO
        LD R0,MINUS_SIGN 
        OUT
        NOT R1,R1
        ADD R1,R1,#1
IN_CHU_SO LD R0,ASCII_0
        ADD R0,R0,R1
        OUT
        HALT
MINUS_SIGN .FILL #45 ;"-"
ASCII_0 .FILL #48 ;"0"
        .END


```
***

**Bài 3:** Viết chương trình thực hiện nhập 2 số (từ 0 đến 9) từ bàn phím, tính tích hai số này 
và in ra màn hình.

*Phân tích:* Có 2 thuật toán chính cần phải thực hiện trong bài này:

- Sử dụng lặp biết trước số lần để tính tích của hai số
- Sử dụng lặp không biết trước số lần để tách một số bất kì thành 2 chữ số hàng chục và 
hàng đơn vị.

```
        .ORIG x3000
        IN
        LD R1,ASCII_0M 
        ADD R2,R0,R1 
        IN
        ADD R3,R0,R1
        AND R4,R4,#0 
LAP ADD R2,R2,#-1
        BRn TACH_SO
        ADD R4,R4,R3
        BR LAP
TACH_SO AND R2,R2,#0 
LAP2 ADD R4,R4,#-10
        BRn TIEP
        ADD R2,R2,#1 
        BR LAP2
TIEP ADD R3,R4,#10 
        LD R1,ASCII_0
        ADD R2,R2,#0 
        BRz XUAT_DON_VI
        ADD R0,R2,R1
        OUT
XUAT_DON_VI ADD R0,R3,R1
        OUT
        HALT
ASCII_0 .FILL #48 ;"0"
ASCII_0M .FILL #-48
        .END

```
***

**Bài 4:** So sánh hai số 1 chữ số được nhập từ bàn phím (lần lượt nhập vào là a và b)

- Nếu a > b thì in ra màn hình: "A lon hon B"

- Nếu a < b thì in ra màn hình: "A be hon B"
- Nếu a = b thì in ra màn hình "Hai so bang nhau"

*Phân tích:* Bài này không quá khó, chỉ cần lấy hiệu hai kí tự vừa được nhận vào và rẽ nhánh 
hai lần.

```
        .ORIG x3000
        IN
        ADD R1,R0,#0 
        IN
        NOT R0,R0
        ADD R0,R0,#1
        ADD R1,R1,R0 ;R1 := R1-R0
        BRn BE_HON
        BRp LON_HON
        LEA R0,STR_BANG ;2 so bang nhau
        BR IN_CHUOI
BE_HON LEA R0,STR_BE
        BR IN_CHUOI
LON_HON LEA R0,STR_LON
IN_CHUOI PUTS
        HALT
STR_BE .STRINGZ “A be hon B”
STR_LON .STRINGZ “A lon hon B”
STR_BANG .STRINGZ “Hai so bang nhau”
        .END

```
***

**Bài 5:** Mở rộng bài số 4, in ra màn hình giá trị lớn nhất, giá trị bé nhất của 3 số được 
nhập từ bàn phím.

*Phân tích:* Sử dụng phép lặp biết trước số lần, có thể mở rộng thay số 3 bởi số lượng bất kì.
Vì các phép so sánh là phép trừ nên có thể dùng trực tiếp mà không cần phải chuyển từ mã 
ASCII sang số.

Chức năng của các biến trong chương trình:

- R1: lưu số lần lặp còn lại

- R2: lưu giá trị nhỏ nhất

- R3: lưu giá trị lớn nhất

```
        .ORIG x3000
        LD R1,SO_LAN_LAP 
        LD R2,INIT_MIN 
        LD R3,INIT_MAX 
LAP IN
        NOT R4,R0 
        ADD R4,R4,#1
        ADD R5,R2,R4 
        BRnz TIEP_TUC_1
        ADD R2,R0,#0
TIEP_TUC_1 ADD R5,R3,R4
        BRzp TIEP_TUC_2
        ADD R3,R0,#0
TIEP_TUC_2 ADD R1,R1,#-1 
        BRp LAP 
        LEA R0,STR_MIN 
        PUTS
        ADD R0,R2,#0
        OUT
        LEA R0,STR_MAX
        PUTS
        ADD R0,R3,#0
        OUT
        HALT
SO_LAN_LAP .FILL #3
INIT_MIN .FILL #57
INIT_MAX .FILL #48
STR_MIN .STRINGZ “So be nhat la: “
STR_MAX .STRINGZ “\nSo lon nhat la: “
        .END

```

***

**Bài 6:** Nhập một chuỗi các số có một chữ số từ bàn phím, kết thúc khi nhập vào ký tự #. 

In ra màn hình tổng của dãy số vừa được nhập (giả sử rằng tổng của dãy này nhỏ hơn 100). 
Lưu các số vừa được nhập vào vào bộ nhớ từ địa chỉ x4000, kết thúc bằng giá trị “#”.

Cho biết mã ASCII của kí tự “#” là x23.

```
        .ORIG x3000
        LD R1,ASCII_#M
        LD R2,ASCII_0M
        LD R3,DIA_CHI_DAU
        AND R4,R4,#0 
LAP IN
        ADD R5,R0,R1
        BRz NGUNG_LAP
        ADD R5,R0,R2
        STR R5,R3,#0 
        ADD R3,R3,#1 
        ADD R4,R4,R5 
        BR LAP
NGUNG_LAP STR R0,R3,#0 
        LEA R0,STR_OUT
        PUTS
        AND R2,R2,#0 
LAP2 ADD R4,R4,#-10
        BRn TIEP
        ADD R2,R2,#1 
        BR LAP2
TIEP ADD R3,R4,#10 
        LD R1,ASCII_0
        ADD R2,R2,#0 
        BRz XUAT_DON_VI
        ADD R0,R2,R1
        OUT
XUAT_DON_VI ADD R0,R3,R1
        OUT
        HALT
ASCII_0 .FILL #48 ;"0"
ASCII_0M .FILL #-48
ASCII_#M .FILL x-23 ;"#"
DIA_CHI_DAU .FILL x4000
STR_OUT .STRINGZ "Tong cua day so la: "
        .END

```
