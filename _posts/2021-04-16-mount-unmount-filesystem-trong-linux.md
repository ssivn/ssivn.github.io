---
title:  "Mount và Unmount trong Linux"
date:   2021-04-15
excerpt: "Tìm hiểu chi tiết về mount và unmount trong Linux"
project: true
tag:
- linux os 
- filesystem
- mount
- unmount
comments: true
---

**1. Mount filesystem trong Linux**

-	Khi ổ đĩa được phân vùng, Linux cần một số cách để truy cập dữ liệu trên các phân vùng. Không giống Windows (thực hiện bằng cách gán các ký tự ổ đĩa cho mỗi phân vùng), Linux sử dụng một cây thư mục (folder tree) thống nhất, trong đó mỗi phân vùng được gắn kết tại một điểm gắn kết trên cây đó.
-	Mount point là một thư mục được sử dụng như một cách để truy cập vào hệ thống tệp trên phân vùng và việc gắn kết hệ thống tệp là quá trình liên kết một hệ thống tệp nhất định với một cây thư mục cụ thể trong cây thư mục.
-	Trong ví dụ này, tôi muốn mount ổ ```/dev/sdb1``` vào thư mục ```/opt```, chúng ta thao tác như sau:

```
datnt@ssivn:~$ sudo mount /dev/sdb1 /opt/
```

-	Với thao tác như trên, chúng ta mới chỉ gắn kết các phân vùng tạm thời, nếu hệ thống bị restart, các phân vùng sẽ không tự động mount lại. Để có thể tự động mount sau khi khởi động lại hệ thống, chúng ta phải vào ```/etc/fstab``` để chỉnh sửa. 
-	Lấy UUID của phân vùng muốn mount với câu lệnh ```blkid```. Ví dụ tôi có một phân vùng ```/dev/sdb1``` chưa sử dụng:

```
datnt@ssivn:~$ blkid
/dev/sdb1: UUID="b47ba0dc-e775-4826-9c1b-ce35888731f5" TYPE="ext4" PARTUUID="bed53589-01"
datnt@ssivn:~$
```

- Tôi sẽ gán phân vùng này với thư mục ```/opt``` bằng cách khai báo trong ```/etc/fstab```

```
datnt@ssivn:~$ cat /etc/fstab
UUID=b47ba0dc-e775-4826-9c1b-ce35888731f5 /opt ext4 defaults 0 0
UUID=7c4d7353-3c9e-4aeb-b24a-2406d0819d71 / ext4 defaults 0 0
/swap.img       none    swap    sw      0       0
datnt@ssivn:~$
```

-	Sau khi lưu lại file fstab, gõ lệnh ```mount –a``` để hoàn tất, nếu như không báo lỗi gì tức là bạn đã thành công. Nếu có báo lỗi xảy ra, không được restart lại server tránh tình trạng hệ thống bị lỗi, đọc kỹ lỗi và xử lý đến khi không còn lỗi nữa.

```
datnt@ssivn:~$ sudo mount -a
```

**2. Unmount filesystem trong Linux**

-	Sau khi bạn đã thao tác xong với ổ đĩa và muốn tháo nó ra khỏi máy hoặc không sử dụng tới nó nữa, Unmount ổ đĩa (lệnh ```umount```) đó ra sẽ giúp bạn tháo ổ đĩa một cách an toàn mà không làm hỏng dữ liệu lưu trữ trong đó.
-	Trong ví dụ này, tôi sẽ unmount phân vùng vừa mount vào ```/opt``` ở phần trên, câu lệnh thao tác:

```
datnt@ssivn:~$ sudo umount /opt
```
