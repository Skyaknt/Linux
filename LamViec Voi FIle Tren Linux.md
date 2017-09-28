# Làm việc với files trên Linux

## Dòng dữ liệu ( the file streams )
- Khi các lệnh được thực thi, theo mặc định thì sẽ có 3 luồng cơ bản:
1. Đầu vào tiêu chuẩn hoặc **stdin(0)** ( standard input ) : Thường là những thiết bị nhập input cho Shell ví dụ như bàn phím.
2. Đầu ra tiêu chuẩn hoặc **stdout(1)** ( standard output ) : Hiển thị kết quả các lệnh lên terminal (hoặc màn hình) cho chúng ta thấy.
3. Lỗi cơ bản hoặc **stderr(2)** ( standard error ) : Hiển thị ra các lỗi trong quá trình thực hiện một lệnh hoặc một công việc nào đó.
 

### Chuyển hướng output: 
- Sử dụng ">" để chuyển hướng các kết quả từ việc thực thi lệnh (stdout) :
Ví dụ : Thực thi lệnh ls -l được ghi vào file test.txt thay vì in thẳng ra màn hình :

`
root@ubuntu:/home/songle# vi test.txt
root@ubuntu:/home/songle# ls -l > test.txt
root@ubuntu:/home/songle# ls
demo1  hi.ssh  song  song1  songle2  test.txt
root@ubuntu:/home/songle# cat test.txt
total 20
drwxr-xr-x 3 root root 4096 Sep 26 21:50 demo1
-rw-r--r-- 1 root root 6189 Sep 28 00:38 hi.ssh
-rw-r--r-- 2 root root   10 Sep 27 00:00 song
-rw-r--r-- 2 root root   10 Sep 27 00:00 song1
lrwxrwxrwx 1 root root   10 Sep 26 23:55 songle2 -> songle.ssh
-rw-r--r-- 1 root root    0 Sep 28 00:40 test.txt
`

- Chú ý : khi file test.txt đã có nội dung, nếu ta ghi thẳng vào thì nội dung cũ sẽ mất. Để khắc phục điều này
ta sử dụng ">>" - nó sẽ ghi thêm dữ liệu vào cuối file mà không thay thế dữ liệu cũ.

### Chuyển hướng Error :

- Sử dụng "2>" để chuyển hướng các lỗi , giúp tránh làm phiền người dùng. 
Ta có thể chuyển hướng một thông báo lỗi tới một file text để sau này đọc dễ hơn, hoăc chuyển tới /dev/null
để không phải xem nữa.
Ví dụ :  khi muốn đọc file laptop.ssh bằng lệnh cat, nhưng trong thư mục không có file này, do đó sẽ có lỗi báo 
ra màn hình. Nếu không muốn nhìn thấy thông báo này, ta chuyển hướng tới /dev/null 

`
root@ubuntu:/home/songle# cat laptop.ssh
cat: laptop.ssh: No such file or directory
root@ubuntu:/home/songle# cat laptop.ssh 2>/dev/null
root@ubuntu:/home/songle#

`
*Chúng ta cũng có thể chuyển hướng stderr và stdout vào cùng 1 tập tin bằng cách sau:*
`

$ cmd 2>&1 hi.ssh
`
hoặc
`
$ cmd &> hi.ssh

### Đường ống dẫn ( pipes )
- Kí hiệu "|"
- Cho phép lấy kết quả của lệnh trước để làm input cho lệnh sau nó. 
- Sử dụng cho việc lọc dữ liệu

Ví dụ : Kiểm tra trong máy đã cài phần bổ trợ Remote từ xa ssh chưa, nếu chỉ sử dụng lệnh `dpkg` nó sẽ liệt kê
ra tất cả phần mềm đã cài. Muốn chỉ hiển thị phần mềm ssh thì sử dụng với lệnh `grep` để lọc dữ liệu :

```
root@ubuntu:~# dpkg -l | grep ssh
ii  openssh-client                     1:7.2p2-4ubuntu2.2                         amd64        secure shell (SSH) client, for secure access to remote machines
ii  openssh-server                     1:7.2p2-4ubuntu2.2                         amd64        secure shell (SSH) server, for secure access from remote machines
ii  openssh-sftp-server                1:7.2p2-4ubuntu2.2                         amd64        secure shell (SSH) sftp server module, for SFTP access from remote machines
ii  ssh-import-id                      5.5-0ubuntu1                               all          securely retrieve an SSH public key and install it locally
```  