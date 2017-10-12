# Tìm hiểu ZFS ( Z - Filesystem ) 


### 1. Định nghĩa :

- ZFS là một trình quản lí ổ đĩa mà các lvm kết hợp với công nghệ RAID với chức năng đảm bảo tính toàn vẹn cho dữ liệu. 
Mỗi block dữ liệu (block of data) được đọc/ghi bởi cơ chế ZFS dều được kiểm tra tính toàn vẹn và khôi phục lại nếu có 
lỗi xảy ra.

- ZFS có thể cài đặt trên hầu hết các hệ thống của Linux như Debain/Ubuntu và Red Hat/CentOS.

### 2. Đặc điểm :

#### 2.1 ZFS Pooled Storage

- Quản lí bộ nhớ vật lí bằng hình thức storage pools .
- Gộp các thiết bị lưu trữ thành 1 hoặc nhiều pool . Trong Pool sẽ hiển thị các đặc tính vật lí của bộ nhớ ( vị trí thiết bị, dữ liệu còn trống.. )
- Tất cả các tập tin đều chia sẻ bộ nhớ trong pool với nhau. 
- Không cần xác định trước kích thước tập tin, các tập tin hệ thống giãn nở trong diện tích dung lượng của pool.
- Khi một thiết bị lưu trữ mới được thêm vào pool thì các file systems sẽ tự động sử dụng luôn không gian đĩa mới cho các tiến trình mà không 
	cần phải cấu hình bằng lệnh .

#### 2.2 Checksums and Self- Healing Data 

- Với ZFS, tất cả dữ liệu và siêu dữ liệu ( metadata ) được phê duyệt bằng thuật toán người dùng có thể chọn để kiểm tra.
	
- ZFS checksums được lưu trữ với hình thức tất cả lỗi sẽ được phát hiện và có thể khôi phục lại files một cách cẩn thận.
	
	+ Tất cả hoạt động kiểm duyệt checksum và khôi phục dữ liệu được thực hiện ở lớp tệp hệ thống và trong suốt đối với các ứng dụng.
		
- ZFS cung cấp một cơ chế tự phục hồi dữ liệu. Nó lưu dữ liệu với rất nhiều bản sao khác nhau trong pool, khi một bản bị bad blocks, 
nó sẽ lấy dữ liệu chính xác từ một bản sao dự phòng khác và sửa chữa lại file bị hỏng.
	
#### 2.3 Transactional Semantics

- ZFS là một dạng file hệ thống tệp giao dịch
	
- Sử dụng cơ chế COW ( coppy on write ) để quản lí bằng cách sử dụng các bản sao của filesystem gốc.

		
	+ Dữ liệu gốc không bị ghi đè lên.
	
	+ Những sự thay đổi trên filesystem gốc sẽ được sao ra một bản mới và sửa trên bản  mới đó.
		
	+ Khi xảy ra sự cố như mất điện, hệ thống treo.. file system gốc sẽ không bị ảnh hưởng,
		chỉ mất đi các thông tin mới được chỉnh sửa trên bản sao của nó.
		
 ![Imgur](https://i.imgur.com/Ibs6UQr.png)
 
Ví dụ :
	
- Có một process A và file gốc là AS. từ A có các process con là A1, A2, A3.. 
Nếu không sử dụng COW, mỗi process con sẽ có riêng resource và sử dụng bộ nhớ và tài nguyên rất lớn. 
Với COW, tất cả các process con sinh ra đều trỏ vào resource nguồn là AS, 
sau này process nào cần sửa chữa dữ liệu thì nó sẽ copy AS ra một bản mới, và sửa chữa trên đó.  
 
		
		
#### 2.4 Unparalledled Scalability 

- Khả năng mở rộng tuyệt vời.
	
- Hệ thống tập tin chính là 128-bit và có thể mở rộng đến 256-bit.
	
- Dữ liệu metadata được phân bổ động, không cần phải quy định trước các inodes để lưu dữ liệu và giới hạn bộ nhớ của tập tin.

- Thư mục có thể mở rộng đến 248 ( 256 nghìn tỉ ) mục, và số lượng các file là không giới hạn trong một file hệ thống. 
	
#### 2.5 ZFS Snapshots

- Một snapshot là một bản sao chỉ có thể đọc của một system file hoặc volume.
	
- Được tạo ra nhanh và dễ dàng, ban đầu sẽ không tiêu tốn thêm tài nguyên nào của bộ nhớ.
	
Tuy nhiên khi dữ liệu trong thư mục gốc thay đổi, bản snapshot sẽ sử dụng thêm bộ  nhớ để lưu những thay đổi đó.
=> snapshot sẽ ngăn chặn việc dữ liệu bị giải phóng trở lại pool.
	
	
#### 2.6 Simplified Administration

- ZFS cung cấp một mô hình quả lí đơn giản hóa và khoa học :
		
	+ Dễ dàng tạo và quản lí hệ thống tập tin mà không yêu cầu nhiều lệnh hoặc file cấu hình.
		
	+ Dễ dàng tạo các quy tắc, quản lí các mountpoint.. bằng một lệnh duy nhất.
		
	+ Kiểm tra, thay thế các thiết bị mà không cần biết lệnh.
		
	+ Gửi và nhận các snapshots.
 
- ZFS quản lí hệ thống tập tin thông qua một mô hình phân cấp.
		
	+ Các file hệ thống là trung tâm của việc kiểm soát.
		
	+ Filesystems cũng như các file bình thường, không có dung lượng quá lớn vì vậy bạn có thể 
	tạo các filesystem cho mỗi user, project, workspace..
		
		
### 3. Cơ chế :

**ZFS cung cấp một phương pháp đọc/ghi dữ liệu với nhiều mountpoints, dàn đều trên các ổ đĩa. Các ổ đĩa có thể được gộp lại 
thành các nhóm khác nhau để phù hợp với các cơ chế :**

- **Mirror** : Dữ liệu sẽ được sao lưu như nhau trên các ổ đĩa - tương tự như RAID 1. Đây chỉ đơn giản là một bản sao của một 
đĩa khác mỗi khi dữ liệu bị thay đổi.
	+ Số đĩa cần : >= 2
	=> Đảm bảo an toàn cho dữ liệu (  backup plan), nhưng cần nhiều dung lượng ổ đĩa.
	
- **Stripe** : Dữ liệu lưu trên tất cả các đĩa có sẵn cùng lúc - tương đương RAID 0. Trong một mảng có 2 ổ đĩa, một nửa dữ liệu
lưu trên đĩa 1, một nửa nằm trên đĩa 2.
	+ Số đĩa cần : >=2 đĩa
	=> Tốc độ cao nhưng không có phương án dự phòng. Một ổ hỏng là dữ liệu sẽ bị hỏng.
	
- **RAID-Z** : Một bản nâng cấp từ RAID 5
	
	![Imgur](https://i.imgur.com/iQhLaYW.gif)
	
	+ Tránh được "Write hone" bằng cách sử dụng COW - Copy on write ( khi mất điện đột ngột lúc đang ghi dữ liệu, 
	sẽ có trường hợp không thể biết được data blocks hoặc parity blocks nào vừa được ghi trùng với dữ liệu đã được
	ghi trong các ổ stripe, và không xác định được là dữ liệu nào đã bị lỗi, đó gọi là hiện tượng "write hole" ).
	+ Số đĩa cần : >= 3 đĩa 
	=> Vẫn đảm bảo tốc độ đọc/ghi bằng stripe
	=> Khi một trong 3 ổ đĩa hỏng, dữ liệu vẫn được phục hồi. Trừ khi cả 3 ổ cùng hỏng.
	+ Nếu muốn thêm an toàn, có thể dùng RAID 6 ( RAID-Z2 trên cơ sở của ZFS) để có 2 lần dự phòng.

- **RAID-Z 2** và **RAID-Z 3** : Về cơ bản gống RAID-Z tuy nhiên sẽ có 2 - 3 ổ đĩa được dùng để dự phòng parity.
	
	![Imgur](https://i.imgur.com/EfR6V1S.gif)	
	
	- **RAID-Z 2** : 
	Số đĩa cần : >= 4
	+ **RAID-Z 3** :
	Số đĩa cần : >= 5
	=> Sử dụng trong môi trường có dữ liệu quan trọng.
		
## Tham khảo : 

(1) https://viblo.asia/p/tan-man-ve-copy-on-write-WrJvYKXBeVO
	
(2) http://aptech.fpt.edu.vn/chitiet.php?id=198
	
(3) http://www.geekyprojects.com/storage/what-is-raid-levels-and-types/

(4) https://docs.oracle.com/cd/E23824_01/html/821-1448/gbciq.html
