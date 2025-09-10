> Tham khảo 1 số nguồn

* Using dirsearch to scan all the system:

<img width="1474" height="543" alt="image" src="https://github.com/user-attachments/assets/aa835c35-6d57-4648-b917-263e6aba2423" />

* Now, we look that we have 2 flow: `Needn't to login` and `Need to login`:

* Needn't to login:

= Ở trang chủ có 3 chức năng là `Home`, `Register`, `Login và List of Public Closed Vul`. Khi nhấp vào các bài viết trong List này thấy lỗi => Chưa có đủ quyền truy cập

= Trang Home (http://103.97.125.56:30476/home), thấy toàn bài viết mà chúng ta chưa đủ quyền xem được.

= Ở trang Register thấy có 2 trường là Username và Password. 

=> Khả năng bị SQLi, StoreXSS.

= Ở trang Login thấy có 2 trường là Username và Password.

=> Khả năng bị SQLi, StoreXSS. 

* Need to login:

= Sau khi đăng nhập, có 4 chức năng là Home, Dashboard, Submit Vul, Logout.

= Khi mở Home, thấy có 2 bài report như trước khi đăng nhập, nhưng khi truy cập vào thấy hiện nội dung bài báo. Tuy nhiên khi truy cập target thì không truy cập được.

<img width="723" height="376" alt="image" src="https://github.com/user-attachments/assets/cc8da8e9-e9c8-4cec-9a49-99fe4a3803f3" />
<img width="785" height="727" alt="image" src="https://github.com/user-attachments/assets/dbb22f45-23cc-4bcc-9594-ff31f76fa2fb" />

= Nhưng khi truy cập bài viết kia, truy cập target thì lại được. => IDOR. Để người dùng ngoài xem được dữ liệu.

= Khi truy cập target trang này, chúng ta thấy có 1 trường name, và khi nhập thấy có reflect 

<img width="1341" height="539" alt="image" src="https://github.com/user-attachments/assets/75ee1e12-81d6-4516-8aaf-cab44c32bd8f" />

= Chức năng Dashboard (http://103.97.125.56:30476/dashboard) hiện tất cả bài viết mình đã nộp:
<img width="1307" height="924" alt="image" src="https://github.com/user-attachments/assets/1330e319-d48a-41fd-9cc1-5311e69f5cde" />

= Chức năng Submit Vul (http://103.97.125.56:30476/submit_vulnerability) có 3 trường có thể nhập, 2 trường tick 

=> SQLI hoặc StoreXSS.

= Khi thử nộp kiểu Private hoặc Public thì thấy Staff chỉ Comment bài Private thôi. 

=> Chúng ta có thể lợi dụng để lấy cookie staff bằng StoredXSS.

<img width="672" height="510" alt="image" src="https://github.com/user-attachments/assets/dd747032-eba1-42dc-bc02-bfe643a9bb0c" />

= Nó phát hiện ra chèn XSS ở Proof of Concept thì staff sẽ không phản hồi lại, còn chèn XSS ở Description thì lại phản hồi lại => Có thể chèn XSS ở Description thử xem.

= Test mãi không thấy XSS. Có thể dính IDOR, lần lượt xem các vulnerablitity từ /1. Đến tận /21 thì mới được flag:
<img width="1423" height="720" alt="image" src="https://github.com/user-attachments/assets/9ab782a9-66de-45e5-8cc2-f2d332d91f95" />

***Flag2: Đoán đúng tài khoản và mật khẩu của một trong những staff, bạn sẽ nhận được FLAG số 2.***

= Truy cập /staff thì thấy bị `Permission Deny`.

= Truy cập /backup thì thấy `Only localhost`.

= ruy cập /console thì thấy có :
<img width="1431" height="584" alt="image" src="https://github.com/user-attachments/assets/602ad781-6279-4f8c-9e0d-d47806f2cb82" />

= Thử đăng ký dưới tên staff1, staff2 thì thấy đã tồn tại. Không có hint khó Brute force quá, thử đăng nhập vs username `pentester` xem sao. Search `top 100 password` on google rồi thay vào. Thấy thay `123456` được luôn.

<img width="1614" height="978" alt="image" src="https://github.com/user-attachments/assets/2480978d-56e9-400a-aaf8-8f841c9ecfbf" />

= Thử bruteforce với tài khoản `staff2` thì thu được password:

<img width="1382" height="468" alt="image" src="https://github.com/user-attachments/assets/c1c9609b-503a-41ca-b049-59d87a6f42f3" />

***Flag3: Chức năng /backup chỉ có staff ở localhost được phép truy cập. Nếu tìm được cách qua mặt chúng, bot sẽ gửi cho bạn FLAG số 3 thông qua chức năng Note.***

=> SSRF

= Người ta bảo chức năng /backup chỉ có staff ở localhost được truy cập. Vậy giờ mình chèn địa chỉ localhost vào đâu?

= Mình để ý thấy có duy nhất report này bot trả về:

<img width="765" height="914" alt="image" src="https://github.com/user-attachments/assets/bedaefa0-e0d0-43ea-bc59-0730073b388b" />

= Nghĩa là con bot sẽ đi vào cái target trong report. Khi submit 1 target về http://localhost hay 
127.0.0.1 sẽ có thông báo là:
<img width="409" height="43" alt="image" src="https://github.com/user-attachments/assets/e267a503-f87b-4e17-9457-c2da6cda426c" />

= Nghĩ lại `localhost` chính là địa chỉ chính máy tính mình sử dụng. Thông thường `localhost` được ánh xạ tới địa chỉ IP 127.0.0.1, trong IPv6 `localhost` được ánh xạ đến địa chỉ `::1`. Nghĩa là với IPv4 thì toàn bộ dải địa chỉ từ 127.0.0.0 đến 127.255.255.255 đều được dành riêng cho localhost, tuy nhiên địa chỉ thường dùng nhất là 127.0.0.1. Thử 127.0.0.2 thì lại được. Giờ ngồi đợt bot trả lời để lấy flag thôi 🩹

Flag:

`
CHH{5srF_requeST_7O_1n7ERna1}
`

***Flag4: Flag số 4 được lưu trong cơ sở dữ liệu. Hãy tìm kiếm và khai thác lỗ hổng SQL Injection để lấy được nó.***

= Trong trang web có các chức năng có thể bị nhiễm SQL Injection : Register/ Login (test rồi nhưng không thấy bị), Submit Vui, Note (chức năng chỉ tài khoản staff triangle)

= Chức năng Submit Vul test ' hay " đều render y như vậy 

=> Tạm bỏ qua, thử chức năng khác.

= Chức năng `Note`, gõ ' thì hiện ra lỗi SQLi:
<img width="822" height="103" alt="image" src="https://github.com/user-attachments/assets/4a2df6b8-a201-44bb-ad0a-9b1abaa41b9d" />

= Cấu trúc câu query nhận được:

```
INSERT INTO `note` VALUES (NULL, 23, 4, '<cai_minh_inject>', CURRENT_TIMESTAMP);
```

= Thử  sử dụng `||` để nối chuỗi với ví dụ:

```
'||(SELECT 'BC_abcabcxyzxyz')||'
```

= Lúc này truy vấn sẽ thành :

```
INSERT INTO `note` VALUES (NULL, 23, 4, ''||(SELECT 'BC_abcabcxyzxyz')||'', CURRENT_TIMESTAMP);
```

= Nó sẽ nối 2 chuỗi rỗng với kết quả SELECT. Nó sẽ trả về:
<img width="416" height="93" alt="image" src="https://github.com/user-attachments/assets/6035c795-ba1b-4c4c-80e9-c8218953840e" />

= Lấy kết cấu của database:

```
'||(SELECT group_concat(sql) FROM sqlite_sqlite_schema)||'
```

= Nó hiện ra toàn câu lệnh để tạo bằng trong database đang được sử dụng:
<img width="854" height="478" alt="image" src="https://github.com/user-attachments/assets/d0521097-7c0e-431a-807d-5876c8b81366" />

= Thấy có bảng `flag` và file `s3cr3t`:

= Khi trích xuất flag ta được:
<img width="630" height="638" alt="image" src="https://github.com/user-attachments/assets/170f60ed-cb0a-4a95-9db5-52041e200e88" />

***Flag5: Khai thác Lỗ hổng Command Injection trong chức năng Export sẽ giúp bạn lấy được FLAG cuối cùng. Hãy tìm cách đọc file /flag.txt***

= Thử nhập 1 `Submit Vulnerability` có `Proof of Concept` là:

<img width="564" height="332" alt="image" src="https://github.com/user-attachments/assets/fd2f4f67-6432-4958-bcf5-16a52b6b7e49" />

= Ấn Export thì thấy với file txt đã bị xóa mất `` và pdf thì mã hóa --la` biến thành comment.
<img width="634" height="225" alt="image" src="https://github.com/user-attachments/assets/74aee0ad-b031-46a9-8f7c-327f248c7d62" />

= Sửa thành ``ls -la`` vẫn bị mã hóa:
<img width="705" height="265" alt="image" src="https://github.com/user-attachments/assets/7ffbac5b-37b9-4677-8f48-ccb804581ccf" />

=> Nghĩa là cứ ` trong txt thì bị xóa, còn pdf thì dấu space thành -

= Chèn payload `<link rel="attachment" href="file:///flag.txt">` vào Note:
<img width="1805" height="882" alt="image" src="https://github.com/user-attachments/assets/70e858b0-3b7c-424d-a6d0-45fbedd4d36a" />

= Export ra file không bị thay đổi tên nhưng ta đã nhúng payload kia vào rồi:
<img width="786" height="310" alt="image" src="https://github.com/user-attachments/assets/38e1ff37-c161-4aeb-bcbb-d6743e48f79a" />

= Dùng `pdfdetach` để gỡ flag.txt ra:
<img width="563" height="285" alt="image" src="https://github.com/user-attachments/assets/e3de1168-f221-45e5-a471-0f3e42084220" />

= Flag là

`CHH{COMmaNd_InJeCTIOn_pdF_d020dc87bcd5ccb1eaf6c673eeef2d32}`







