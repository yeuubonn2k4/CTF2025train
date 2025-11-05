#### Nhiều trang web sử dụng HTTP. Tuy nhiên đến nay, chỉ các trang web thương mại điện tử mới thực sự thấy phiền khi sử dụng HTTPS. 

+ HTTPS khắc phục lỗi truyền dữ liệu không được mã hóa của HTTP bằng cách sử dụng chứng chỉ SSL hoặc hiện nay là TLS . Điều này giúp tạo kết nối được mã hóa an toàn giữa máy chủ và trình duyệt, từ đó bảo vệ thông tin nhạy cảm bị đánh cắp khi thông tin được chuyển giữa máy chủ và trình duyệt.

=> HTTPS = HTTP + SSL/TLS.

+ Vậy chứng chỉ SSL/TLS là gì? Nó là một tập tin kỹ thuật số do CA phát hành. Chứng chỉ chứa:

```
- Thông tin về domain của website.
- Public key của server (để mã hóa dữ liệu).
- Thông tin xác thực từ CA.
```

*Cách HTTPS bảo vệ dữ liệu:*

```
1. Trình duyệt gửi yêu cầu kết nối an toàn đến máy chủ.
2. Server gửi chứng chỉ SSL (chứa public key) cho trình duyệt.
3. Trình duyệt kiểm tra xem chứng chỉ này có hợp lệ, được cấp bởi CA uy tín hay không.
4. Nếu hợp lệ, trình duyệt tạo 1 "khóa bí mật tạm thời", mã hóa bằng public key của máy chủ và gửi lại.
5. Máy chủ dùng private key của mình để giải mã session key.
6. Từ đây, mọi dữ liệu truyền giữa hai bên đều được mã hóa bằng session key đó (mã hóa đối xứng, rất nhanh).
```

- “HTTPS hoạt động ở tầng ứng dụng, sử dụng giao thức SSL/TLS ở tầng giao vận để mã hóa dữ liệu.”

- HTTP/2, HTTP/3 và HTTPS khác nhau như nào?

Bản chất các phiên bản này được sửa đổi để mang lại tính hiệu quả cao hơn (HTTP2 trao đổi dữ liệu ở dạng nhị phân và server có thể chủ động truyền các phản hồi đến cache của máy khách thay vì chờ đợi 1 yêu cầu HTTP mới".

HTTP3 thì hỗ trợ phát trực tuyến theo thời gian thực và yêu cầu truyền dữ liệu hiện đại khác đạt hiệu quả cao hơn.

