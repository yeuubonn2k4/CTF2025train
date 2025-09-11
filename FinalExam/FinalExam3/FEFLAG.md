> Tham khảo một số nguồn

***Hãy khai thác lỗ hổng và truy cập vào cơ sở dữ liệu. FLAG nằm trong bảng chứa giá trị cấu hình hệ thống.***

=> Tình nghi SQLi.

= Sử dụng `dirsearch` để quét hệ thống:
<img width="1460" height="418" alt="image" src="https://github.com/user-attachments/assets/742a1dff-129c-4eb1-94c5-a4e7a5a05f48" />

= Sau khi quét xong thấy có mỗi /assets/, truy cập vào thì bị 403 cấm, còn nếu vào cổng 1337 thì chuyển hướng mãi

= Khi click vào liên kết đề bài, thấy hiện ra 1 trang đăng nhập có 3 trường `Username`, `Password`, `Captcha`. Vì có trường `captcha` nên sẽ loại đi hướng `Brute force` nhé:
<img width="793" height="435" alt="image" src="https://github.com/user-attachments/assets/7ec0a11b-87b7-4664-b6b0-428b6a047eaf" />

= Thấy khi nhập sai `Capcha` thì sẽ hiện ngay `Capcha failed` còn nhập đúng thì sẽ hiện `Failed`

= Ctrl U thấy có 2 vấn đề:

+ File `captcha.php`: file render random ra captcha

+ /?action=status&type=version: để lấy version rồi ghi vào chỗ Firmware Version

= Thấy có ?action nên nghi nghi `Path Traversal`. Nhưng test mãi không thấy.

= Test lỗi cái `/?action=status&type=version`:
<img width="952" height="367" alt="image" src="https://github.com/user-attachments/assets/7575c22f-94d5-4451-ab5a-f3b728b43e8c" />
<img width="951" height="307" alt="image" src="https://github.com/user-attachments/assets/afe92559-02ed-470a-914e-dbd4fbd2c089" />

= Test lại SQLi bằng cách dùng ' hoặc " để xem nó có lỗi không? Test version' thì thấy lỗi `error message`. 
<img width="883" height="184" alt="image" src="https://github.com/user-attachments/assets/ce642022-8e68-4d69-ad05-646894154205" />

= Khi test đến dấu " thì hiện lỗi SQLite:
<img width="956" height="301" alt="image" src="https://github.com/user-attachments/assets/a057077c-9b81-401d-bea1-ce1de333f8fb" />

= Câu query sẽ thành:

```
SELECT ... FROM .... WHERE type="$_GET['type']"
```
= Dùng `order by` để test xem có bao nhiêu cột. `?action=status&type=version" order by 1--` bắt đầu test từ 1--. nếu có trả về kết quả thì nghĩa là cột đó có tồn tại.

<img width="934" height="197" alt="image" src="https://github.com/user-attachments/assets/3e762bcf-9f34-484f-a079-6588271a3103" />

= Cứ như vậy đến 4-- thì lỗi:
<img width="1866" height="378" alt="image" src="https://github.com/user-attachments/assets/f40a2fa6-ce67-4850-a948-c07c42f32b01" />

=> Có nghĩa là chỉ có 3 cột thôi. bây giờ trích xuất dữ liệu của bảng:

= Thử " SELECT không được nên thử sang " UNION xem sao. Thử với câu lệnh `"UNION SELECT 1,2,3--` để test ra cột thứ 3 sẽ hiển thị.

<img width="748" height="141" alt="image" src="https://github.com/user-attachments/assets/86edad5d-eee7-456a-9128-9b3cdd45b881" />

= Trích xuất các tên bảng trong cột thứ 3:

```
http://103.97.125.56:30922/?action=status&type=version%22%20UNION%20SELECT%201,2,group_concat(tbl_name)%20FROM%20sqlite_master%20WHERE%20type=%27table%27%20and%20tbl_name%20NOT%20like%20%27sqlite_%%27--%20-
```

= Kết quả hiển thị có 2 bảng là `users` và `configurations`:

<img width="780" height="191" alt="image" src="https://github.com/user-attachments/assets/e51763da-6406-45ba-87ae-0db25e1f9eef" />

= Đọc writeup người anh em Tùng Núi, thì thấy họ dùng payload này:

```
vlxx" union select 1,2,sql FROM sqlite_master WHERE type!='meta' AND sql NOT NULL AND name ='table_name'--
```

= Với bảng `configurations`:
<img width="941" height="213" alt="image" src="https://github.com/user-attachments/assets/6ec038cc-272f-4860-9f53-60dfb587645a" />

= Với bảng `users`:
<img width="936" height="195" alt="image" src="https://github.com/user-attachments/assets/4dda0d81-0e89-4661-ba12-0938d62c60c1" />

=> Thấy bảng users có trường `username` và `password` nên tập trung vào bảng users

= Lấy username và password:

<img width="1471" height="155" alt="image" src="https://github.com/user-attachments/assets/ddb57ec1-49c9-40d2-bda1-8f31e20bee8c" />

=> username là 'jonathan', password là `1ff61e91349d3f6623a81ccd3d881fa1` và username `cookie`  `97c2bd1615963162bd4e0caca037ba9e` garfield

= Nhưng mà đăng nhập không được, lấy thử dữ liệu bảng `configurations` gồm name và content

<img width="1520" height="188" alt="image" src="https://github.com/user-attachments/assets/a1150c62-60ca-4733-9da1-cb567e542e2d" />

=> Ra Flag:

`CHH{BA5ic_5qL_iNj3CtiON}`

***Flag2: Sau khi truy cập vào cơ sở dữ liệu. Hãy tìm mật khẩu của cookie ở dạng hash. Thực hiện crack và đăng nhập bạn sẽ có FLAG số 2***

= Vừa tìm được giá trị cookie ở trên :v 

= Crack thì ra cả username và password đều là `garfield`:
<img width="1492" height="405" alt="image" src="https://github.com/user-attachments/assets/3429d0cb-3e30-4a49-b0d0-8955c0c901de" />

= Đăng nhập thành công. Ảo vãi mình nhận ra mình crack sai cái hash:
<img width="1329" height="498" alt="image" src="https://github.com/user-attachments/assets/c7fd1586-3768-4652-8cf7-1ab8a45bd4e5" />

= Phải stop lab làm lại thì mình đã thu được flag:
<img width="1454" height="865" alt="image" src="https://github.com/user-attachments/assets/4600003b-b650-4488-a7df-c87d862171ec" />

***Flag3: Flag số 3 nằm trong file /etc/passwd, hãy tìm cách đọc chúng.***

=> Path traversal

= Phát hiện giao diện để `Path traversal`:

<img width="722" height="377" alt="image" src="https://github.com/user-attachments/assets/90718edd-9173-4f50-bdc6-63d666cb5741" />

= Thử ấn `Back up` thì nó tạo cho 1 mình file zip. tôi tải về thì thấy có:

<img width="801" height="323" alt="image" src="https://github.com/user-attachments/assets/f567e6a7-dad2-4ddf-9087-566924a42852" />

= Nghĩa là chức năng `Upload firmware` sẽ update các file mình tải lên vào những file cũ kia.

= CHức năng `Message log` có:
<img width="1205" height="371" alt="image" src="https://github.com/user-attachments/assets/ee69313b-d71e-4ec0-a5e5-1355558d661e" />

= Thấy có 1 param file :> chèn path traversal ở đây được

<img width="895" height="212" alt="image" src="https://github.com/user-attachments/assets/922dcde7-3773-4182-81a6-04b76b1ed609" />

= Thử thêm xem sao:
<img width="981" height="205" alt="image" src="https://github.com/user-attachments/assets/a172c3e7-e465-4f40-be70-1f2854bb4576" />

= Thử đến 8 cái không thấy gì, liệu có bị mã hóa không :> thử tải payload về Brute force thì thấy ở cái 
<img width="771" height="47" alt="image" src="https://github.com/user-attachments/assets/f7c5890d-b97f-4601-93ca-c4999a50dea8" />

= Thấy có flag :>

<img width="1274" height="711" alt="image" src="https://github.com/user-attachments/assets/aa39dcab-eeb6-4d81-9b6e-bfeef8eda565" />

***Flag4: Đây là FLAG cuối cùng. Hãy tìm cách đọc file /flagXXXX.txt. XXXX là giá trị random ký tự và số.***

=> Command Injection

= Chức năng `System`:

= Có 2 chức năng con là: `Download một file chứa các firmware` và `Upload 1 file zip chứa các firmware của mình để upgrade cho những firmware đang tồn tại trong hệ thống`

=> ý tưởng: Upload file zip chứa các file bash chứa mã thực thi:

<img width="518" height="156" alt="image" src="https://github.com/user-attachments/assets/0e9a5dc1-8aa5-48df-970e-82d0dd6239cb" />

= xong zip lại file txt đó
<img width="978" height="412" alt="image" src="https://github.com/user-attachments/assets/9b9ce486-166d-48fd-8fec-8dfa2e95cc0e" />

<img width="911" height="252" alt="image" src="https://github.com/user-attachments/assets/cdd5570c-1309-4f2e-b905-b7708c7600e6" />

= Ta có thể đoán được vậy bởi đây là trang web quản trị máy in, khi nào admin cần
dùng 1 lệnh nào đó sẽ phải chạy các file bash này, nên nếu admin gọi tên file, thì
đồng thời sẽ thực thi luôn cả lệnh của ta




