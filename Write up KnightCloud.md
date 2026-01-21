# Write up KnightCloud

<img width="1920" height="920" alt="image" src="https://github.com/user-attachments/assets/001152e2-64ab-4c2e-89a1-a255b4ddb28d" />

Vào Challenge thì cho một giao diện như vậy

Mình thử tạo một tài khoản hợp lệ xem sao 

<img width="1490" height="757" alt="image" src="https://github.com/user-attachments/assets/7df6322e-ef79-49a3-8c1b-e5137cd00a47" />

Sau đó lấy Login lại vào 

<img width="1920" height="921" alt="image" src="https://github.com/user-attachments/assets/ecb709bb-9e40-4b76-b4f5-98c51675c2c2" />

Mục tiêu là Unlock mấy cái `Premium Feature` đấy 

Mình thấy cái `Full name` lúc đầu của mình đăng kí được nó sẽ hiển thị ra màn hình cùng với Welcome . Mình đoán khả năng nó sử dụng hàm `render_template()` . Mình thử đăng kí lại một tài khoản khác

```
fullName: {{7*7}}
password: 12345678
email: check5@gmail.com
```
Login vào lại 

<img width="1488" height="746" alt="image" src="https://github.com/user-attachments/assets/d094d421-f80e-4431-9e2c-ac38d9438b58" />

Nhưng nó không thực hiện `7*7` . Vậy là không thể dùng SSTI ở đây được . Mình thử SQL injection ở vào các ô Username, password và fullName kia . Nó không hề chặn các kí tự như các dấu nháy , dấu comment nhưng cũng không hệ thực thi được các payload truyền vào

Mình thử gây lỗi ở các body request bằng cách thêm kí hiệu mảng vào sau email , password, fullName

Thì nó báo `Email and password required` . Mặc dù mình có nhập vào nhưng khả năng nó rỗng nên mới báo như vậy 

<img width="1491" height="815" alt="image" src="https://github.com/user-attachments/assets/d6dd64a9-1219-4219-aa53-fa576c52861c" />

Cái này không khai thác được thêm cái gì . Mình thử thêm dấu ngoặc vuông bọc vào giá trị . Tức chuyển cái giá trị truyền vào từng tham số kia từ dạng chuỗi sẽ thành dạng mảng 

<img width="1492" height="744" alt="image" src="https://github.com/user-attachments/assets/1e2a492a-21c6-4ca6-94af-c83d83395e27" />

Nhận thấy được phía backend nó đã thêm dấu escape trước những dấu nháy kép được truyền vào . Lúc này khá bí, vì không có hướng nào để khai thác cả . Nghĩ lại mục tiêu ban đầu là cần làm sao để lên premium . Để í kí tí thì ở trong Response trả về nó có trả về body có set là
```
"subscriptionTier":"free"
```
Vậy thì khả năng làm sao để chuyển chữ free thành premium . Có khả năng là vậy 

Ở phần body request ở phần register , mình thử thêm thuộc tính `"subscriptionTier":"premium"`

<img width="1491" height="807" alt="image" src="https://github.com/user-attachments/assets/2bbc2d14-bc8a-461f-b296-059f35f82168" />

Kết quả là vẫn bị `"subscriptionTier":"free"` , đoán thêm một vài thuộc tính như là `"role":"admin"` , mấy cái liên quan này nọ nhưng nó vẫn trả về `"subscriptionTier":"free"`

Khả năng mỗi tài khoản mới được register thì đều được set là `"subscriptionTier":"free"` như vậy rồi 

Nếu vậy thì phải tạo tài khoản rồi phải có cái chức năng gì đó để update , unlock cái `"subscriptionTier":"premium"` kia . Nhưng nếu thế thì cũng phải tìm được cái route của nó và cái phần body request cần phải có những tham số gì

Mình thử lên xem lại source code của trang web coi có link source code nào đáng ngờ không 

Ở trong `/register` thì có một file như thế này 

<img width="1189" height="448" alt="image" src="https://github.com/user-attachments/assets/1b711ec4-dd90-4d29-8420-1f484ded9277" />

<img width="1897" height="973" alt="image" src="https://github.com/user-attachments/assets/eeecff6b-b93f-4fc5-9ad1-ecaf7cf5f70f" />

Một file index.js và rồi ở đầu nó có dòng đầu là `import{ ..... }` gì gì đó ở đầu . Mình thử cop vào nhờ con gemini pro đọc hộ code 

các bạn tham khảo :`https://gemini.google.com/app/a2e8419ce5e8e92b`

Các bạn để í dòng này 
```
examples:{upgradeUserExample:{endpoint:"/api/internal/v1/migrate/user-tier",method:"POST",body:{u:"user-uid-here",t:"premium"},
```
Nó cho một ví dụ về cách để lên được premium . Chúng tần cần 
```
Gửi đến: URL: /api/internal/v1/migrate/user-tier
Với: Method: POST
Và nội dung body
  u:"user-uid-here",t:"premium"
```

Áp dụng vào bài triển thôi 
```
POST /api/internal/v1/migrate/user-tier HTTP/1.1
Host: 23.239.26.112:8091
Content-Length: 60
Accept-Language: en-US,en;q=0.9
Accept: application/json, text/plain, */*
Content-Type: application/json
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/143.0.0.0 Safari/537.36
Origin: http://23.239.26.112:8091
Referer: http://23.239.26.112:8091/register
Accept-Encoding: gzip, deflate, br
Connection: keep-alive

{"u":"50473fac-6630-4bf1-8107-4156a7fec5d4","t":"premium"
}
```
Kết quả là: 
<img width="1495" height="813" alt="image" src="https://github.com/user-attachments/assets/a87df8e7-4e50-479a-bd2a-ebe5278dc0e8" />

CHúng ta update được lên premium 



