RPM and YUM : Tips and Tricks

# File RPM (Red Hat Package Manager) : Red Hat based Linux distribution.

- Tại sao lại cần RPM package?
	- Theo hình thức ban đầu, tất cả các file sử dụng trong Linux đều ở dạng **tar** . Những file tar sẽ chứa 
	tất cả files liên quan tới phần mềm đó. Người dùng muốn sử dụng phải trích xuất file và cài đặt nó. Điều này 
	sẽ dẫn tới 2 bất lợi :
	
		-  Một phần mềm khi được cài đặt sẽ khó để theo dõi tất cả các file đã được cài đặt trên những đường dẫn 
		khác nhau trong hệ thống, do đó khó để xóa hay cập nhật phần mềm. 
		- Nếu phần mềm đó có những phần bổ trợ thì người dùng phải cài bằng tay chúng trước khi cài phần mềm.
		
=> File RPM chứa tất cả các thông tin về phiên bản, danh sách các files, các phần bổ trợ, mô tả về package... do vậy 
	sẽ luôn theo dõi được các thành phần nó.	

	- Một trong những ưu điểm của loại file này mà openSUSE hiện đang dùng là nó có thể tạo các file nâng cấp PatchRPM 
	hoặc DeltaRPM chỉ chứa phần thay đổi để cài vá vào bản đã cài trước đó. Các file nâng cấp đó có dung lượng nhỏ nên 
	quá trình tải về từ Internet và cài nhanh hơn kiểu tải cả file deb mới về cài thay thế file cũ như ở Ubuntu hiện nay.

	- Ubuntu sử dụng package system cài đặt bằng apt từ Debian, file có đuôi .deb, muốn cài đặt file rpm trên
	ubuntu cần convert từ rmp sang deb.

- File RPM khác file DEB là nội dung file đều đã dịch sang mã máy. Trong một file RPM có các thành phần chính sau:

	- Mã định danh file là một file RPM.
	- Chữ ký số để kiểm tra tính toàn vẹn và xác thực nguồn gốc file.
	- Phần đầu file chứa các meta-information như tên gói phần mềm, version, kiến trúc vi xử lý, danh sách các file trong gói, v.v..
	- Phần thân file lưu theo định dạng cpio và nén bằng gzip.
	
## RPM Command : 

Nếu trong máy không có gói rmp nào, download **Linux RPM Package** bằng lệnh :

`wget http://mirror.downloadvn.com/apache//directory/apacheds/dist/2.0.0-M24/apacheds-2.0.0-M24-x86_64.rpm`

1. Liệt kê danh sách các gói RPM đã cài đặt trên hệ thống :

`rpm -qa`

2. Kiểm tra một phần mềm cụ thể nào đó đã được cài đặt trên hệ thống chưa:

`rpm -qa <package-name>`

vd : `rpm -qa httpd`

3. Kiểm tra xem package nào liên quan tới một file cụ thể :

`rpm -qa <file-path>

vd :

`rpm -qf /etc/yum.conf`

4. Liệt kê tất cả file trong package :

`rpm -ql <package-name>`

vd :

`rpm -ql yum`

5. Kiểm các các gói bổ trợ của RPM package : 

`rpm -qR <package-name>`

vd : 

`rpm -qR yum`

6. Liệt kê sự thay đổi trong log của package :

`rpm -q --changelog <package-name>

vd :

`rpm -q --changelog yum | less`


## Lệnh YUM 


- Yellowdog Updater Modified (yum) là một công cụ dòng lệnh để quản lí các gói RPM packages và 
các kho chứa chúng. Khi công cụ RPM không thể cài đặt các gói bổ trợ của package theo mặc định,
thì `yum` sẽ đảm nhận việc này.

- Để cài đặt, gỡ bỏ, cập nhật các RPM packages từ hệ thống và kiểm tra lịch sử `yum`, bạn cần 
chạy các lệnh với quyền `root user`.

1. Để cài đặt RPM package :

`# yum install <package-name>`

vd :

`yum install httpd`

Lệnh trên sẽ cài đặt gói `httpd` với các phần mềm bổ trợ đi kèm của nó. Nếu có một phần bổ trợ nào
không còn trong  repository, qá trình sẽ bị lỗi.

- Trong quá trình cài đặt, sẽ có lựa chọn **[y/d/N]**
	-  `d` : download only
	- `y` : yes
	- `N` : No
	
- Bạn có thể cài đặt nhiều gói RPM cùng lúc :

`yum install <pkg1> <pkg2>....<pkgn>`

2. Gỡ bỏ RPM package :

`# yum erase <package name>`

vd :

`# yum erase httpd`

3. Cập nhật RPM package :

`# yum update <package name>`

vd :

`# yum update httpd`

4. Cập nhật tất cả các file RPM trong hệ thống :

`# yum update`

5. Tìm kiếm thông tin về package :

`# yum info <package name>`

vd :

`# yum info httpd`

6. Tìm ra package nào cung cấp một lệnh/thư viện cụ thể :

`# yum provides <binary/command name>`

vd :

`# yum provides sar`

7. Kiểm tra lịch sử của các yum transaction trong hệ thống, sử dụng lệnh dưới để hiển thị transaction-id, thời gian , hoạt động :

`# yum history`

- Sử dụng lệnh `history` để xem lịch sử hoạt động của yum và hoàn tác nó :

`# yum history undo <transaction id>`
`# yum history undo 2 -y`

8. Hiển thị repository đã được cấu hình trong hệ thống :

`# yum repolist`

9. Xóa dữ liệu cache trong thư mục đường dẫn `/var/cache/yum/` directory :

`# yum clean all`

## Tạo một yum repository :




