### SSL Pinning & Signature checking 

- SSL Pinning: là kỹ thuật mà ứng dụng lưu trữ sẵn certificate hoặc public key của máy chủ trong mã nguồn. Khi kết nối HTTPS được thiết lập, ứng dụng sẽ so sánh certificate thực tế từ server với certificate đã pin sẵn. Nếu không khớp, ứng dụng sẽ từ chối kết nối. Đây là hành động gần như là bắt buộc với tất cả các ứng dụng sử dụng giao thức HTTPS. Tuy nhiên, trên Goole developer page, sẽ có 1 đoạn warning:

=> Mục tiêu là đảm bảo ứng dụng chỉ kết nối với máy chủ "đúng" của mình, tránh bị giả mạo.

<img width="871" height="230" alt="image" src="https://github.com/user-attachments/assets/736935f3-02b1-4f6f-8b3c-265a275fc2ed" />

=> Đây chính là nhược điểm và rủi ro khi sử dụng SSL Certificate Pinning trong ứng dụng Android. Các rủi ro mà SSL Pining có thể có:

+ Khi chứng chỉ của server hết hạn, khi thay đổi nhà cung cấp chứng chỉ (CA), khi chuyển sang server khác hoặc domain khác và khi bắt buộc cập nhât cấu hình bảo mật thì lúc này certificate của server mới sẽ không khớp với certificate được pin sẵn trong ứng dụng. Điều này khiến cho ứng dụng sẽ không thể kết nối được với server. Nếu người dùng chưa cập nhật app mới (có pin mới) thì họ sẽ mất khả nnawg truy cập dịch vụ hoàn toàn.

=> Vì vậy, nếu sử dụng SSL pinning thì chúng ta sẽ pải handle được các case liên quan đến certification và phải tính đến khả năng force update đối với client khi có exception liên quan đến SSL Certificate.

- Self Signature (Chứng chỉ tự ký): là một chứng chỉ được ký bởi chính chủ sở hữu của nó, không thông qua tổ chức cấp chứng chỉ (CA) như DigiCert,...

=> Chủ thể của chứng chỉ tự đóng vai trò là CA và tự ký chứng chỉ cho chính mình.

=> Điều này được dùng để ngăn chặn (phần nào đó) việc APK bị sửa đổi, thêm mã độc.

>Hầu hết lập trình viên Android đều từng ít nhất 1 lần tải APK từ nguồn không chính thống: apkpure, apkresult hoặc các game trên appstorevn.

*Ví dụ: Attacker có thể modify một file APK cho app ngân hàng/ ví điện tử nào đó, ở giao diện đăng nhập thay vì việc gửi thông tin đăng nhập thì attacker sẽ log lại thông tin đăng nhập này (password,..) và gửi về máy chủ của attacker chẳng hạn.

= Một file APK có thể được sửa đổi và sign đi sign lại nhiều lần, cách nhanh nhất để kiểm tra 1 file APK có bị sửa đổi rồi sign lại hay không là *check signature* của file APK. lý do thực hiện check là do signature là duy nhất, nó chỉ được gen bởi key dùng để sign apk và không có cách nào từ signature trong apk sinh ngược được ra sign key cả.

***Các bước check signature của file APK***:

1. Dùng Keytool để lấy SHA của file APK release:

```
keytool -printcert -jarfile app-release.apk
```
- Lúc này sẽ cho ra kết quả:

```
MD5: B3:4F:BE:07:AA:78:24:DC:CA:92:36:FF:AE:8C:17:DB
SHA1: 16:59:E7:E3:0C:AA:7A:0D:F2:0D:05:20:12:A8:85:0B:32:C5:4F:68XXXX
SHA256: 1XXXXXXXXXXXX1XXXXXXXXXXXX1XXXXXXXXXXXX1XXXXXXXXXXXX1XXXXXXXX
```

=> Trong 3 loại mã hóa này tương đương với 3 mức độ: thấp, trung bình và cao. Thường thì nhận dạng certificate sẽ sử dụng SHA1 để nhận dạng.

2. Trên source code ứng dụng, khi vào một màn hình nào đó chúng ta có thể check SHA dựa vào đoạn code:

```
Signature[] sigs = context.getPackageManager().getPackageInfo(context.getPackageName(), PackageManager.GET_SIGNATURES).signatures; for (Signature sig : sigs) { Trace.i("MyApp", "Signature hashcode : " + sig.hashCode()); }
```

= Đoạn code này thực hiện lấy thông tin của ứng dụng hiện tại (APK đang chạy,..), lấy ra danh sách chữ ký mà file APK đó đã được ký. Sau đó duyệt qua từng chữ ký và in ra giá trị hash code. Hàm getPackageInfo(..., PackageManager.GET_SIGNATURES), hệ thống sẽ đọc thông tin chữ ký của APK đang chạy.

=> Sau khi lấy được danh sách chữ ký, chúng ta sẽ compare các mã sha này và có thể phán đoán được ứng dụng có bị sửa đổi hay không.

=> Tóm lại, bước 1 là lấy fingerprint của certificate chuẩn mà bạn chấp nhận. Còn bước 2 check chữ ký khi app chạy trên thiết bị.



XXXX

