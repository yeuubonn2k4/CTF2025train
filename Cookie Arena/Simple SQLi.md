# Description
The system will check your userlevel entered. If it is 0, it will say guest. But in the database, 0 is also "admin". The system will check if userid returns admin, then FLAG will be displayed. Please try to log in to the admin account

# Solutions:

= Giao diện `Login`:

<img width="1540" height="572" alt="image" src="https://github.com/user-attachments/assets/2c7350bb-6583-4e48-ad82-4a1374822346" />

= Khi tôi nhập 0 vào thì hiện `hello guest` còn nhập số khác và `admin` thì sẽ hiện `Wrong`

= Khi này truy vấn sẽ thành `SELECT * FROM users WHERE userlevel='$input';`

=> Khi nhập 0 vào thì truy vấn sẽ thành `SELECT * FROM users WHERE userlevel=0;` và kết quả đúng là có thể lấy được user admin và FE chỉ in ra 'guest'

= Tôi chợt nhớ lại ý tưởng 1 bài đã làm và có suy nghĩ sẽ làm thử `0 or userid='admin'`

= Khi tôi nhập vậy thì hiện lỗi:
<img width="813" height="162" alt="image" src="https://github.com/user-attachments/assets/1bf82106-398a-47e8-9805-5ee7acf39f5e" />

=> Có thể đã bị sai cú pháp

= Lúc này truy vấn thành:

```
SELECT * FROM users WHERE userlevel='0 or userid='admin'';
```

=> Lỗi vì lúc này sẽ thành `0 or userid=`

=> Sửa payload thành `'0 or userid='admin'--`

= Lúc này truy vấn thành:

```
SELECT * FROM users WHERE userlevel=''0 or userid='admin'--';
```

=> Lấy được Flag:
<img width="904" height="267" alt="image" src="https://github.com/user-attachments/assets/064394fc-f90a-447a-8d5f-f944d3a889dc" />
