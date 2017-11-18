# Chương 11 : Logical Volume Manager layout

Về cơ bản, việc sắp xếp Logical Volume Manager hiển thị như sau :

	- **Logical Volume(s) : `/dev/fileserver/share` , `/dev/fileserver/backup` , `/dev/fileserver/media`
	- **Volume Group(s) : `fileserver`
	- **Physical Volume(s) : `/dev/sdb1` , `/dev/sdc1` , `/dev/sdd1`, `/dev/sde1`

- Trên một hoặc nhiều **physical volume**	có thể tạo một hoặc nhiều **volume group**
trên mỗi **volume group** có thể tạo một hoặc nhiều **logical volume**.

- Nếu sử dụng nhiều **physical volume**, mỗi **logical volume** có thể có dung lượng lớn hơn một **physical
volume** .

	- Không nên sử dụng hết dung lượng vật lý cho các **logical volume** để sau này có thể mở rộng dung
	lượng một **logical volume** nào đó khi cần.

- Với LVM , một ổ đĩa cứng hoặc một tập các đĩa cứng hoặc các phân vùng ( partition) khác nhau trên một 
đĩa cứng có thể được phân bổ tới một hoặc nhiều **physical volume** .

	- **Physical Volume** có thể được đặt trên những **block devices** ( hard drives ) khác cái mà 
	có thể mở rộng lên 2 hoặc nhiều ổ đĩa hơn .

- **Các Physical Volumes** được gộp thành các **logical volumes** , trừ phân vùng lưu thư mục **/boot**.

	- Phân vùng lưu trữ **/boot** không thể đặt trên **logical volume group** vì boot loader không thể đọc được nó.
	Nếu muốn lưu thư mục `/boot` trên logical volume, phải tạo một phân vùng mới không thuộc volume group để lưu `/boot`.
	
	- Vì physical volume không thể được mở rộng sang các ổ đĩa vật lý khác, cho nên nếu muốn tạo một phân vùng mới để
	lưu trữ thư mục hệ thống, nên tạo nhiều **physical volume** trên một ổ đĩa.
	
- Các **Volume Group** có thể được chia thành nhiều **Logical Volume** , các logical volume sẽ được gán cho các **mount points**,
như là `/home` và chúng sẽ được định dạng theo các chuẩn như **ext3, ext4..** .

	- Khi các logical volume đã sử dụng hết dung lượng, nó có thể được cấp thêm từ volume group. 
	
	- Khi một ổ đĩa cứng mới được gắn hệ thống, nó có thể được thêm vào **volume group**  và các logical volume trên 
	VG đó có thể mở rộng thêm dung lượng.
	
	
## Tạo LVM layout ( Hiển thị LVM )

- Máy lab :  CentOS 7
- Phần mềm lab : VMWare Workstation 14 


### 1. Trên máy CentOS 7, bổ sung thêm ổ `sdb` dung lượng 20GB để sử dụng cho việc tạo LVM layout .

- B1. Dùng lệnh `lsblk` để kiểm tra xem trong máy có những ổ cứng nào được gắn vào :

- B2. Tạo partition cho ổ `sdb` : **fdisk /dev/sdb**

	-  Trong đó : 
		- Chọn "n" để bắt đầu tạo partition
		- Chọn "p" để tạo partition primary
		- Chọn "1" để tạo partition primary 1
		- Tại "First sector (2048-20971519, default 2048)"" để mặc định
		- Tại "Last sector, +sectors or +size{K,M,G} (2048-20971519, default 20971519)"" bạn chọn "+5G" để partition bạn tạo ra có dung lượng 5G
		- Chọn "w" để lưu lại và thoát

	- **Thay đổi định dạng của partion mới tạo thành LVM** :
	
		- Trong đó: 
		- Bạn chọn "t" để thay đổi định dạng partition
		- Bạn chọn "8e" để đổi thành LVM
		- Chọn "w" để lưu lại và thoát.
		
- [B3. Để tạo LVM layout, đầu tiên tạo physical volume :](#b3)

```
# pvcreate /dev/sdb1
  Physical volume "/dev/sdb1" successfully created
# pvs
  PV         VG   Fmt  Attr PSize   PFree
  /dev/sdb1       lvm2 ---    5g 	  5g
# pvscan
  PV
  /dev/sdb1           lvm2 [5 GiB]
  Total: 3 [20 GiB] / in use: 2 [5 GiB] / in no VG: 1 [5 GiB]
# pvdisplay
 "/dev/sdb1" is a new physical volume of "5 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb1
  VG Name
  PV Size               5 GiB
  Allocatable           NO
  PE Size               0
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               ajGCMg-Y4cG-v4AD-Wxma-TaE5-zQig-XmnYAx
```

- B4. Tạo **volume group**

```
# vgcreate storage /dev/sdb1
  Volume group "storage" successfully created
# vgscan
  Reading all physical volumes.  This may take a while...
  Found volume group "storage" using metadata type lvm2
# vgs
  VG      #PV #LV #SN Attr   VSize   VFree
  storage   1   0   0 wz--n-  5g 	   5g
# vgdisplay
  --- Volume group ---
  VG Name               storage
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               5 GiB
  PE Size               4.00 MiB
  Total PE              59618
  Alloc PE / Size       0 / 0
  Free  PE / Size       59618 / 5 GiB
  VG UUID               nEcTxG-p5K6-npqD-OVeX-dRI1-aWP9-o4D1Z1
```

- B5. Tạo **Logical Volume** 

```
# lvcreate -L 1G -n db-area storage
  Logical volume "db-area" created.
# lvcreate -L 1G -n users-area storage
  Logical volume "users-area" created.
# lvcreate -L 1G -n staging-area storage
  Logical volume "staging-area" created.
# lvcreate -l 100%FREE -n spare storage
  Logical volume "spare" created.
# lvs
  LV           VG      Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  db-area      storage -wi-a-----  1.00g
  spare        storage -wi-a-----  2.00g
  staging-area storage -wi-a-----  1.00g
  users-area   storage -wi-a-----  1.00g
# lvscan
  ACTIVE            '/dev/storage/db-area' [1.00 GiB] inherit
  ACTIVE            '/dev/storage/users-area' [1.00 GiB] inherit
  ACTIVE            '/dev/storage/staging-area' [6.00 GiB] inherit
  ACTIVE            '/dev/storage
```

- B6. Sau khi tạo xong các Logical Volumes, tiến hành định dạng hình thức lưu trữ trên các LV và mount 
chúng đến các mount point :

```
# mkfs.ext4 /dev/storage/db-area
# mkfs.ext4 /dev/storage/users-area
# mkfs.ext4 /dev/storage/staging-area
# mkfs.ext4 /dev/storage/spare

# mkdir /db
# mount /dev/storage/db-area /db
# mkdir /users
# mount /dev/storage/users-area /users
# mkdir /staging
# mount /dev/storage/staging-area /staging
```

	-  Ở đây ta định dạng các LV theo hình thức file system ext4.
	
## Mở rộng một LVM layout 

Trên máy Local CentOS, có 2 ổ đĩa cứng `/dev/sda` và `/dev/sdb` . Trên `/dev/sda` được chia như nhau :

```
[root@compute1 ~]# fdisk -l /dev/sda

Disk /dev/sda: 32.2 GB, 32212254720 bytes, 62914560 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000b8ee4

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200    62914559    30407680   8e  Linux LVM
```

`/dev/sda` khi được cài đặt hệ điều hành lên, sẽ được chia thành 2 partion :
	- `/dev/sda1` dùng cho phân vùng `boot` phục vụ việc khởi động hệ thống. 
	- `/dev/sda2` là phân vùng của LVM layout . Cung cấp nơi lưu trữ (LV )cho các phân vùng `/root` , `/swap` ...

```
[root@compute1 ~]# lvmdiskscan
  /dev/cl/root [      26.99 GiB]
  /dev/sda1    [       1.00 GiB]
  /dev/cl/swap [       2.00 GiB]
  /dev/sda2    [     <29.00 GiB] LVM physical volume
  /dev/sdb     [      20.00 GiB]  
  3 disks
  2 partition
  0 LVM physical volume whole disks
  1 LVM physical volume
```

```
[root@compute1 ~]# pvs
  PV         VG Fmt  Attr PSize   PFree
  /dev/sda2  cl lvm2 a--  <29.00g 4.00m
[root@compute1 ~]# lvs
  LV   VG Attr       LSize  Pool Origin Data%  Meta%  Move Log                                                                                                        Cpy%Sync Convert
  root cl -wi-ao---- 26.99g                                                                                                                                          
  swap cl -wi-ao----  2.00g                                                                                                                                          
```

- Partition hoạt động ở hình thức LVM Physical Volume là `/dev/sda2` , nó là một phần thuộc 
VG `cl` . 
	
	- Ở lớp trên cùng của VG đó là các LV : `/root` , `/swap` 
	
- Khi cần tăng dung lượng của LVM layout với phân vùng mới thuộc vào ổ cứng thứ 2 `/dev/sdb`, ta tiến
hành tạo partion nhỏ cho `/dev/sdb` theo <a name="b3"> B3 ở phần trên </a>

- Khi tạo xong `/dev/sdb1` với dung lượng 5G , tiến hành mở rộng dung lượng của VG `cl` :

```
# vgextend cl /dev/sdb1
  Volume group "os" successfully extended
```

- Sử dụng `pvscan` để quét tất cả các ổ đĩa đang được dùng làm Physical Volume. 

```
# pvscan
  PV /dev/sda2   VG cl   lvm2 [5 GiB / 0   free]
  PV /dev/sdb1   VG cl   lvm2 [20 GiB / 20 GiB free]
  Total: 2 [40 GiB] / in use: 2 [40 GiB] / in no VG: 0 [0   ]
```
- Tiếp theo, tăng dung lượng của LV `/dev/cl/