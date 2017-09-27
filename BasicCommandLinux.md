# Linux - Basic Commands

## Xác định vị trí các ứng dụng, dịch vụ :
- Phụ thuộc vào từng thành phần và mức ảnh hưởng tới hệ thống mà các chương trình và các gói phần mềm dược lưu
trên nhiều thư mục khác nhau. Nhìn chung các chương trình thực thi sẽ được lưu trên các đường dẫn sau : 
```
/bin
/usr/bin
/sbin
/usr/sbin
/opt.
```

- Một cách để định vị vị trí của các chương trình là sử dụng lệnh `which` . Ví dụ, để tìm chính xác vị trí của 
chương trình tên "diff" trong hệ thống, ta sử dụng lệnh :
```
$ which diff
/usr/bin/diff
```
-  Nếu lệnh `which` không tìm thấy chương trình đó, `whereis` là một lựa chọn dự phòng khá tốt vì nó sẽ tìm đến 
tất cả các gói trên một khu vực bao quát tất cả các thư mục hệ thống :

```
$ whereis diff
diff: /usr/bin/diff /usr/share/man/man1/diff.1.gz

```

## Truy cập thư mục :

**Các lệnh sau dùng để di chuyển giữa các thư mục:**

![Imgur](https://i.imgur.com/wG8NdAN.png)

## Xem danh sách các file hệ thống

- Lệnh `tree` rất hữu dụng để nhìn một cách tổng thể cấu trúc của các thư mục trong hệ thống :

![Imgur](https://i.imgur.com/kxhV03P.png)

## Gán liên kết mềm và liên kết cứng 

- Lệnh `ln` có thể sử dụng để tạo các liên kết cứng hoặc mềm :

Giả sử một file tên file1.txt đã tồn tại. Với liên kết cứng - file2.txt sẽ được tạo bằng lệnh :

` ln file1.txt file2.txt `

	-  Note : liên kết cứng sẽ tạo ra 1 file vật lí cùng trỏ tới mục nhập inode của file vật lí gốc. 
	2 file sẽ cùng tồn tại đồng đẳng như nhau, **nếu xóa file gốc thì dữ liệu hoàn toàn không bị mất** nó chỉ mất
	khi không còn liên kết nào đến inode nữa .

```
# ls -l file*
-rw-r--r--. 2 root root 604 Feb 16 11:49 file1.txt
-rw-r--r--. 2 root root 604 Feb 16 11:49 file2.txt
# ls -li file*
134415251 -rw-r--r--. 2 root root 604 Feb 16 11:49 file1.txt
134415251 -rw-r--r--. 2 root root 604 Feb 16 11:49 file2.txt
```

- Lựa chọn `-i` sẽ cho in ra cột đầu tiên của số i-node( điểm truy cập riêng biệt của mỗi file ).
Với 2 file file1.txt và file2.txt ở trên, chúng có cùng một i-node để gọi tới, là 1 file gốc nhưng nó sẽ có 
nhiều hơn 1 tên gắn liền với nó.

```
# ln file1.txt file3.txt
# ls -li file*
134415251 -rw-r--r--. 3 root root 604 Feb 16 11:49 file1.txt
134415251 -rw-r--r--. 3 root root 604 Feb 16 11:49 file2.txt
134415251 -rw-r--r--. 3 root root 604 Feb 16 11:49 file3.txt
```
- Nếu thay đổi file file3.txt , thì các file1.txt và file2.txt cũng sẽ bị thay đổi theo.

**Link mềm ( symbolic or soft link) được tạo bằng option `-s` :**

```
# ln -s file1.txt file4.txt
# ls -li file*
134415251 -rw-r--r--. 3 root root 644 Feb 16 11:59 file1.txt
134415251 -rw-r--r--. 3 root root 644 Feb 16 11:59 file2.txt
134415251 -rw-r--r--. 3 root root 644 Feb 16 11:59 file3.txt
134415252 lrwxrwxrwx. 1 root root   9 Feb 16 11:59 file4.txt -> file1.txt
```

- Chú ý rằng file4.txt không phải là một file bình thường, nó chỉ là một liên kết tới file1 và nó có một số inode riêng. 
Link mềm không tạo ra một bản sao khác như link cứng, mà chỉ tạo một liên kết giống như 1 lối tắt tới file1 ở ngoài thư mục home.
Khi xóa file1 gốc thì file4 cũng sẽ không truy cập được nữa.
- Không giống link cứng, link mềm có thể trỏ tới các file con ở những file hệ thống khác nhau. Nó có thể trỏ đến
liên kết đã tạo nếu file gốc còn tồn tại, trường hợp file gốc không còn thì link đó sẽ trở thành link treo ( dangling link)
 và không trỏ tới file nào hết.
- Link cứng ( hard link) rất hữu dụng vì nó tiết kiệm bộ nhớ. Tuy nhiên ở một số trường hợp thì cần phải cẩn trọng khi sử dụng nó.
Ví dụ, khi bạn xóa file1 hoặc file2, số inode vẫn được giữ nguyên.  Khi bạn tạo một file mới có tên trùng với file bạn đã xóa ở trên,
thì mặc định liên kết inode sẽ được trỏ tới vị trí file đã xóa. Do đó liên kết cũ với file chưa bị xóa có thể sẽ bị mất.

