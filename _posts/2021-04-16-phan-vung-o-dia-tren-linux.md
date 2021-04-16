---
title:  "Phân vùng ổ đĩa trên Linux"
date:   2021-04-16
excerpt: "Phân vùng ổ đĩa là gì? Hướng dẫn phân vùng ổ đĩa trên Linux"
project: true
tag:
- linux os 
- partition of disk
- disk partition
- fdisk
comments: true
---

**1. Phân vùng là gì?**
- Là tập hợp các vùng ghi nhớ dữ liệu trên các cylinder gần nhau với dung lượng theo thiết lập của người sử dụng để sử dụng cho các mục đích khác nhau. Sự phân chia phân vùng giúp cho ổ đĩa cứng có thể định dạng các loại tập tin khác nhau để có thể cài đặt nhiều hệ điều hành trên cùng 1 ổ đĩa cứng.

**2. Phân vùng ổ đĩa trên Linux sử dụng fdisk**
- Trên hệ thống Linux/UNIX, fdisk là công cụ CLI được sử dụng để phân chia phân vùng ổ đĩa.

- Hiển thị ổ đĩa có trên Linux: Để tạo được phân vùng ổ cứng trên Linux, bạn phải biết được tên hiện tại của ổ đĩa đó đang đặt là gì. Sử dụng lệnh ```lsblk -fp``` để hiển thị ổ cứng và dung lượng của ổ để bắt đầu quá trình tạo ổ.

```
datnt@ssivn:~$ lsblk -fp
NAME        FSTYPE   LABEL UUID                                 MOUNTPOINT
/dev/loop0  squashfs                                            /snap/core/7270
/dev/sda
├─/dev/sda1
└─/dev/sda2 ext4           7c4d7353-3c9e-4aeb-b24a-2406d0819d71 /
/dev/sdb
/dev/sr0
datnt@ssivn:~$
```

- Tạo phân vùng mới: Nhập lệnh ```fdisk /new_partition``` để bắt đầu tạo phân vùng mới. Ở đây phân vùng mới tôi cần tạo là ```/dev/sdb```

```
datnt@ssivn:~$ sudo fdisk /dev/sdb

Welcome to fdisk (util-linux 2.31.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0xbed53589.

Command (m for help):
```

- Gõ lệnh ```n``` để bắt đầu tạo phân vùng, hệ thống sẽ bắt chúng ta khai báo phân vùng mới này là primary (phân vùng chính) hoặc extended (phân vùng mở rộng). Gõ ```p``` để tạo phân vùng mới

```
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
```

-	Tiếp đến, nhập số phần vùng sẽ được tạo, tối đa 4 phân vùng primary. Trong ví dụ này, tôi tạo 1 phân vùng nên sẽ nhập 1 hoặc ấn enter ```(default = 1)```. Hệ thống sẽ thông báo yêu cầu bạn nhập kích thước của phân vùng mới sẽ tạo. Bạn có thể nhập kích thước bắt đầu và kết thúc của phân vùng trong mục First sector và Last sector. Bạn cũng có thể để default và ấn enter, hệ thống sẽ tạo phân vùng là tất cả không gian sẵn có.

```
Partition number (1-4, default 1): 1
First sector (2048-41943039, default 2048):
Last sector, +sectors or +size{K,M,G,T,P} (2048-41943039, default 41943039): +5gb

Created a new partition 1 of type 'Linux' and of size 4.7 GiB.

Command (m for help):
```
- Gõ ```w``` để lưu thay đổi

```
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

- Sai khi tạo phân vùng mới, cần phải khai báo với hệ điều hành để cập nhật bảng phân vùng. Sử dụng lệnh ```partprobe /partition```

```
datnt@ssivn:~$ sudo partprobe /dev/sdb
datnt@ssivn:~$
```

**3. Xóa phân vùng ổ đĩa trên Linux**

-	Để xóa phân vùng đã có, sử dụng lệnh ```fdisk /partition```, gõ lệnh “d” sau đó chọn phân vùng cần xóa trong ổ đĩa đó.

```
datnt@webserver:~$ sudo fdisk /dev/sdb

Welcome to fdisk (util-linux 2.31.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): d
Selected partition 1
Partition 1 has been deleted.

Command (m for help):
```
