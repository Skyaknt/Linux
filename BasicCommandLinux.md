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


