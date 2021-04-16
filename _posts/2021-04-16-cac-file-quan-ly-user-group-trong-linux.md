---
title:  "Các file quản lý user, group trong Linux"
date:   2021-04-16
excerpt: "Chi tiết các file quản lý user, group trong Linux chi tiết nhất"
project: true
tag:
- linux os 
- user
- group
- quản lý user
- quản lý group
comments: true
---

-	Có 4 file chính quản lý người sử dụng trên Linux: ```/etc/passwd```, ```/etc/shadow```, ```/etc/group```, ```/etc/gshadow```

<h1>1. /etc/passwd</h1>

-	Là file text chứa thông tin tài khoản của tất cả người dùng trên hệ thống. Ngoài việc lưu trữ thông tin, nó còn lưu trữ các quyền của user, ID của user,… Thông thường các user sẽ chỉ có quyền read với file này, trừ tài khoản root có quyền write

```
datnt@ssivn:~$ sudo cat /etc/passwd
[sudo] password for datnt:
root:x:0:0:root:/root:/bin/bash
...
datnt:x:1000:1000:Ngo Tien Dat:/home/datnt:/bin/bash
datnt@ssivn:~$
```

-	Cấu trúc các trường trong file /etc/passwd (mỗi trường cách nhau bởi dấu “:” )
```
-	datnt: là tên của tài khoản
-	x: nội dung này thông báo rằng mật khẩu đã được mã hóa và lưu trong file /etc/shadow
-	1000: User ID
-	1000: Group ID
-	Ngo Tien Dat: Mô tả ngắn về user (có thể bỏ trống)
-	/home/datnt: thư mục chính của tài khoản trên hệ thống
-	/bin/bash: quyền  
```

<h1>2. /etc/shadow</h1>

- Lưu trữ thông tin mật khẩu đăng nhập của người dùng hệ thống. Chỉ có tài khoản root hoặc tài khoản có quyền sudo mới có quyền truy cập file.

```
datnt@ssivn:~$ sudo cat /etc/shadow
root:*:18113:0:99999:7:::
...
datnt:$6$YuoTLXA2MjsYwewA$mfEQBSplGwmDjPMEwbsoG4OJjAW6mPPSjRz3WJxGJn/BsocPsnMKOCKRYQUepcM/UHCvjcsz4wAxhsYPuk/zm/:18684:0:99999:7:::
datnt@ssivn:~$
```

-	Các trường trong /etc/shadow được phân cách với nhau bởi dấu “:”, với các thông tin:

```
o	Trường đầu tiên là username
o	Trường thứ 2 là mật khẩu đã được mã hóa, nó có thể bắt đầu bằng ký tự “$”, khi đó, ký tự tiếp theo sẽ là thuật toán mã hóa áp dụng cho mật khẩu, cụ thể: 
$1 = MD5
$2 = Blowfish Algorihm
$2a = eksblowfish algorithm
$5 = SHA-256
$6 = SHA-512
o	Trường thứ 3: khoảng thời gian (tính bằng ngày) từ 1/1/1970 cho tới lần đổi mật khẩu gần nhất
o	Trường thứ 4: là thời gian tối đa còn lại để cho phép người dùng đổi mật khẩu, nếu là 0 thì người dùng có thể đổi mật khẩu bất cứ khi nào.
o	Trường thứ 5: thời gian hiệu lực tối đa của mật khẩu (ngày hết hạn mật khẩu), nếu là 99999 có nghĩa là vô hạn.
o	Trường thứ 6: là khoảng thời gian trước khi mật khẩu hết hạn, hệ thống sẽ cảnh báo cho người dùng. Ví dụ ở trên đang là số 7: tức là trước khi hết hạn 7 ngày, hệ thống sẽ cảnh báo
o	Trường thứ 7: khoảng thời gian mà tài khoản đã hết hạn đăng nhập, ví dụ nếu như trường này là 2, nghĩa là tài khoản đã hết hạn đăng nhập 2 ngày.
o	Trường thứ 8: khoảng thời gian mà tài khoản hết hạn đăng nhập tính từ 1/1/1970 (tính bằng ngày)
```

-	Ngoài ra, trường mật khẩu còn chứa một vài ký tự đặc biệt:
-	Nếu trường password rỗng: tức là người dùng không có mật khẩu đăng nhập
-	Nếu trường password là ```!```: đăng nhập bằng mật khẩu của người dùng bị chặn, nhưng có thể đăng nhập bằng các phương pháp khác (như ssh key)
-	Nếu trường password là ```**LK**``` hoặc  ```*``` tức là tìa khoản bị khóa, không thể đăng nhập bằng mật khẩu, nhưng có thể đăng nhập bằng phương pháp khác.

<h1>3. /etc/group</h1>

- File này giữ thông tin nhóm cho mỗi tài khoản, mọi tài khoản có quyền truy cập và đọc file này, nhưng chỉ có tài khoản root mới có quyền write

```
datnt@webserver:~$ sudo cat /etc/group
root:x:0:
...
datnt:x:1000:
```

-	Mỗi trường của etc/group cũng cách nhau bởi dấu ```:```, trong đó:

```
-	Trường thứ 1: thông tin tên nhóm
-	Trường thứ 2: thông báo tình trạng của mật khẩu. Nếu một người dùng không phải trong group nhưng muốn đăng nhập vào sẽ cần phải có mật khẩu này. Nếu là “x” tức là mật khẩu đã được đặt mã hóa, nếu là “!” thì không người dùng nào được phép truy cập nhóm với lệnh “newgrp”. Nếu là “!!” thì cũng tương tự như “!” nhưng còn thêm ý nghĩa là nhóm này chưa đặt mật khẩu lần nào, còn nếu là null (trống) thì tức là chỉ có những thành viên trong nhóm mới đăng nhập vào nhóm được.
-	Trường thứ 3: Group ID của nhóm
-	Trường thứ 4: danh sách những user thành viên của nhóm
```

<h1>4. /etc/gshadow</h1>

- File này giữ các thông tin tài khoản nhóm bảo mật. File này chỉ được phép đọc bởi tài khoản root cũng như user có quyền sudo. Cũng giống như với các file khác, các trường của file ```/etc/gshadow``` cách nhau bởi dấu ```:```

```
datnt@webserver:~$ sudo cat /etc/gshadow
root:*::
...
datnt:!::
```

-	Thông tin các trường:

```
o	Trường thứ 1: tên nhóm
o	Trường thứ 2: Mật khẫu đã được mã hóa, tính năng và sử dụng như với /etc/group.
o	Trường thứ 3: Quản trị viên của nhóm.
o	Trường thứ 4: các thành viên của nhóm.
```
