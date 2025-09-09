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

