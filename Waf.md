<img width="1490" height="334" alt="image" src="https://github.com/user-attachments/assets/27422491-fa88-4103-962f-1668c1a13a55" /># Write up WAF 

Vào Chall thì nó cho mình một giao diện như vậy


Mình thử nhập vào `admin`

<img width="756" height="142" alt="image" src="https://github.com/user-attachments/assets/ef4edfdf-be98-4cb5-86a4-c77f80fd49a6" />


Bật soure code trang web lên coi có gì không 

<img width="517" height="491" alt="image" src="https://github.com/user-attachments/assets/b217c627-2947-4e0b-ab63-574ca610ec0d" />


Thì được một đoạn code thế này
```html

<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Bee</title>
</head>
<body>
  <p>Input your name:</p>
  <form action="/huraaaaa.html" method="GET">
    <input a="{a}" type="text" required>
    <button type="submit">Submit</button>
  </form>


  <!-- @app.after_request    
    def index(filename: str = "index.html"):
    if ".." in filename or "%" in filename:
        return "No no not like that :("
    
    -->
</body>
</html>
```
Lúc nãy sau khi mình nhập vào admin thì nó chuyển hướng mình đến đúng `/huraaaaaa.html` luôn .

Nhưng ở đây chúng ta thấy dòng 
```
<input a="{a}" type="text" required>
```
Nơi chúng ta nhập vào , nhưng nó không có biến nào ở đây để nhận giá trị mình nhập vào để submit cả .Vậy thì tức là cho dù mình có nhập bất cứ cái gì thì nó cũng không thể vào trong server được 

Để í đoạn này
```python
<!-- @app.after_request    
    def index(filename: str = "index.html"):
    if ".." in filename or "%" in filename:
        return "No no not like that :("
    
    -->
```
có hàm index ,nó nhận vào giá trị của biến `filename` , sau đó nếu trong `filename` tồn tại dấu `..` hoặc dấu `%` thì trả về `No no ...`

Lúc đầu mình nghĩ cái biến file name kia có thể là tham số ở sau `huraaaaaa.html?` kia 

Mình thử điền vào
```
huraaaa.html?file=index.html
```
<img width="1493" height="398" alt="image" src="https://github.com/user-attachments/assets/4f0c6c97-3b00-4b5d-bad1-22ff6bf7e788" />

Nhưng nó vẫn báo `Something Wrong` . Vậy có khi là tham số ở sau `huraaaa.html` không phải là filename

Mình thử nhập đại một đường dẫn `/etc/passwd` 

<img width="1493" height="360" alt="image" src="https://github.com/user-attachments/assets/60bdb6a6-3a54-44c6-96b4-f4d6e4f46b4a" />


Thì lúc này nó vẫn báo Something Wrong . Vậy chúng ta có thể đoán là , cái Something Wrong nó sẽ hiển thị khi mà đường dẫn không đúng , file không tồn tại

Lúc này mình mới nghĩ , có thể cái `huraaaaaa.html` kia nó cũng không tồn tại nên mới luôn luôn báo Something Wrong mỗi khi truy cập vào nó 

Vậy là cái biến `filename` kia có khi không phải là tham số của `hura.html` rồi . Mình thử thay URL thành `/../etc/passwd` 


Thì nó thông báo lại `No no not like that :(`. Vậy có thể khẳng định cái biến `filename` kia nó nhận giá trị từ đường dẫn và check xem cái đường dẫn có tồn tại kí tự `..` và `%` hay không

<img width="1487" height="415" alt="image" src="https://github.com/user-attachments/assets/de5a28fc-7f58-4e58-a174-c8b6e7396ac2" />


Với hint từ đề có `/flag.txt` thì suy luận được rằng bây giờ chúng ta cần đọc file `flag.txt` để lấy flag thôi 

Vấn đề là bây giờ làm sao để biết file đấy ở đâu , thì có cách là thử lùi đường dẫn . Nhưng các ký tự cần đều đã bị chặn . Bây giờ quy về việc nghĩ cách để bypass kí tự đấy 

Sử dụng đường dẫn tương đối thì không thể vì không biết rõ cụ thể `flag.txt` nằm đoạn nào . Mình thử dụng các ký tự unicode trong giống như dấu chấm để vượt qua filter để khi vào trong server tự đổi lại thành dấu chấm thật nhưng cũng thất bại 

Mình thử sử dụng dấu `{}` bọc bên ngoài dấu chấm thử 

<img width="1490" height="334" alt="image" src="https://github.com/user-attachments/assets/0e485053-59eb-4439-a8ed-5d71de5e5116" />

Thì kết quả là chúng ta có flag . Bởi vì bộ lọc chỉ tìm cái chuỗi chứa dấu 2 chấm , nên khi nhìn vào `{.}{.}` thì nó vượt qua được . Khi vào trong python xử lý thì khả năng code backend nó sẽ đưa về dạng dấu chấm thuần túy 



