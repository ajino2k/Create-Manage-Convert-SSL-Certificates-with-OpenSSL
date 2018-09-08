# Create-Manage-Convert-SSL-Certificates-with-OpenSSL
Sẽ có rất nhiều tình huống mà bạn phải đối phó với OpenSSL theo nhiều cách khác nhau, và ở đây tôi đã liệt kê chúng cho bạn như là một bảng note tiện dụng.

Trong bài viết này, tôi sẽ nói về các lệnh OpenSSL thường được sử dụng.
Một số chữ viết tắt liên quan đến chứng chỉ.

* SSL – Secure Socket Layer
* CSR – Certificate Signing Request
* TLS – Transport Layer Security
* PEM – Privacy Enhanced Mail
* DER – Distinguished Encoding Rules
* SHA – Secure Hash Algorithm
* PKCS – Public-Key Cryptography Standards

## 1 . Create new Private Key and Certificate Signing Request

```openssl req -out khamphataybac.csr -newkey rsa:2048 -nodes -keyout khamphataybac.key```

Lệnh trên sẽ tạo CSR và tệp khóa RSA 2048 bit. Nếu bạn định sử dụng chứng chỉ này trong Apache hoặc Nginx, thì bạn cần gửi tệp CSR này tới cơ quan cấp chứng chỉ và họ sẽ cấp cho bạn chứng chỉ đã ký chủ yếu ở định dạng der hoặc pem mà bạn cần định cấu hình trong máy chủ web Apache hoặc Nginx.

## 2 . Create Self-Signed Certificate

```openssl req -x509 -sha256 -nodes -newkey rsa:2048 -keyout khamphataybac.key -out khamphataybac.pem```

Lệnh trên sẽ tạo ra một chứng chỉ tự ký và tệp khóa với RSA 2048 bit. Tôi cũng đã bao gồm sha256 vì nó được coi là an toàn nhất vào lúc này.
Tips: theo mặc định, nó sẽ tạo chứng chỉ tự ký hợp lệ chỉ trong một tháng để bạn có thể xem xét xác định tham số –days để mở rộng tính hợp lệ.

Ví dụ: có ký tự hợp lệ trong hai năm.

```openssl req -x509 -sha256 -nodes -days 730 -newkey rsa:2048 -keyout khamphataybac.key -out khamphataybac.pem```

## 3 . Verify CSR file

```openssl req -noout -text -in khamphataybac.csr```

Xác minh là điều cần thiết để đảm bảo bạn đang gửi CSR tới cơ quan cấp phát hành với các chi tiết được yêu cầu.

## 4 . Create RSA Private Key

```openssl genrsa -out private.key 2048```

Nếu bạn chỉ cần tạo khóa riêng RSA, bạn có thể sử dụng lệnh trên. Tôi đã bao gồm 2048 để mã hóa mạnh hơn.

## 5. Remove Passphrase from Key

```openssl rsa -in certkey.key -out nopassphrase.key```

Nếu bạn đang sử dụng cụm từ mật khẩu trong tệp khóa và sử dụng Apache , Nginx thì mỗi khi bạn bắt đầu, bạn phải nhập mật khẩu. Nếu bạn khó chịu khi nhập mật khẩu, bạn có thể sử dụng ở trên openssl rsa -in khamphataybac.key -check để xóa khóa mật khẩu khỏi khóa hiện có.

## 6. Verify Private Key

```openssl rsa -in certkey.key –check```

Nếu bạn nghi ngờ về tập tin quan trọng của bạn, bạn có thể sử dụng lệnh trên để kiểm tra.

## 7. Verify Certificate File

```openssl x509 -in certfile.pem -text -noout```

Nếu bạn muốn xác nhận hợp lệ dữ liệu chứng chỉ như CN, OU, v.v. thì bạn có thể sử dụng lệnh trên để cung cấp cho bạn chi tiết chứng chỉ.

## 8. Verify the Certificate Signer Authority

```openssl x509 -in certfile.pem -noout -issuer -issuer_hash```

Tổ chức phát hành chứng chỉ ký mọi chứng chỉ và trong trường hợp bạn cần kiểm tra chúng, bạn có thể sử dụng lệnh trên.

## 9. Check Hash Value of A Certificate

```openssl x509 -noout -hash -in bestflare.pem```

## 10. Convert DER to PEM format

```openssl x509 –inform der –in khamphataybac.der –out khamphataybac.pem```

Thông thường, tổ chức phát hành chứng chỉ sẽ cấp cho bạn chứng chỉ SSL ở định dạng .der và nếu bạn cần sử dụng chúng ở định dạng apache hoặc .pem, bạn có thể sử dụng lệnh trên để chuyển đổi chúng.

### 11. Convert PEM to DER format

```openssl x509 –outform der –in khamphataybac.pem –out khamphataybac.der```

## 12. Create CSR using existing private key

```openssl req –out certificate.csr –key existing.key –new```

Nếu bạn không muốn tạo khóa riêng mới thay vì sử dụng khóa hiện có, bạn có thể thực hiện với lệnh trên

## 13. Test SSL certificate of particular URL

```openssl s_client -connect khamphataybac.com:443 –showcerts```

Tôi sử dụng điều này khá thường xuyên để xác thực chứng chỉ SSL của URL cụ thể từ máy chủ. Điều này rất tiện lợi để xác thực giao thức, mật mã và các chi tiết cert

## 14. Check PEM File Certificate Expiration Date

```openssl x509 -noout -in khamphataybac.pem -dates```

## 15. Check Certificate Expiration Date of SSL URL

```openssl s_client -connect khamphataybac.com:443 2>/dev/null | openssl x509 -noout –enddate```

## 16. Check if SSL V2 or V3 is accepted on URL

To check SSL V2  
```openssl s_client -connect khamphataybac.com:443 -ssl2```

To Check SSL V3  
```openssl s_client -connect khamphataybac.com:443 –ssl3```

To Check TLS 1.0  
```openssl s_client -connect khamphataybac.com:443 –tls1```

To Check TLS 1.1  
```openssl s_client -connect khamphataybac.com:443 –tls1_1```

To Check TLS 1.2  
```openssl s_client -connect khamphataybac.com:443 –tls1_2```
