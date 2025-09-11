= Bài này tương tự bài `Baby Logger Middleware` nhưng mà mình sẽ nâng cao hơn khi sử dụng `UNION` và đánh vào `Usser-Agent`. Bài này vẫn dạng chèn `SQLi vào câu lệnh INSERT INTO`

<img width="1919" height="936" alt="image" src="https://github.com/user-attachments/assets/688731bf-e918-48d3-a8cd-ba670eede698" />

= Để ý thấy hiện 6 cột, mình sẽ test thử xem đúng không :>

= Thử chèn vào `User-Agent` thì thấy có render:
<img width="1859" height="294" alt="image" src="https://github.com/user-attachments/assets/3f2ce2d4-f04f-42f1-8416-5ddfbe21d367" />

= Khi lấy không đủ số cột hoặc thừa nó sẽ hiện lỗi:
<img width="1874" height="322" alt="image" src="https://github.com/user-attachments/assets/b21a34de-68b3-46b1-ac04-269569caaf9c" />

= Sau khi `UNION SELECT 1,2,3,4,5,6-- -` thì thấy render => Có 6 cột
<img width="1871" height="459" alt="image" src="https://github.com/user-attachments/assets/f76761eb-f997-416c-a3fb-469f39574790" />

= Bây giờ xem các bảng trong CSDL, vì vừa lỗi hiện là SQLite nên mình sẽ test với SQLite và mình chọn cột 6 là cột hiển thị:

<img width="1869" height="872" alt="image" src="https://github.com/user-attachments/assets/dd18cac4-26e0-461b-8561-dfb5aacc59f3" />

= Thấy hiện 2 bảng là bảng `logger` và `flag` . Trong bảng `flag` có `secr3t_flag`. giờ mình sẽ lấy flag:

<img width="1867" height="403" alt="image" src="https://github.com/user-attachments/assets/bc6ac413-46f1-4f66-a439-5bd9b761b465" />

= Flag là:

`
CHH{L0G9eR_5QL1_in_uPdaTe_1bebebc95c40b7ff6aad7ae9aaf975e0}
`
