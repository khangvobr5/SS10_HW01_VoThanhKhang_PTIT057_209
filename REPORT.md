# BÁO CÁO KỸ THUẬT: PHÂN TÍCH KIẾN TRÚC CONTAINER VS VIRTUAL MACHINE

## 1. Tổng quan

Trong quá trình phát triển và triển khai phần mềm, doanh nghiệp thường sử dụng máy chủ ảo (Virtual Machine - VM) để chạy các ứng dụng. Tuy nhiên, khi số lượng ứng dụng tăng lên, việc quản lý nhiều hệ điều hành riêng biệt làm tiêu tốn tài nguyên và tăng thời gian triển khai.

Docker Container là giải pháp ảo hóa ở cấp hệ điều hành, cho phép đóng gói ứng dụng cùng các thư viện và thành phần phụ thuộc vào một Image. Container chia sẻ Linux Kernel của hệ điều hành máy chủ nên nhẹ hơn so với VM trong nhiều trường hợp.

Việc chuyển từ mô hình sử dụng VPS truyền thống sang Docker giúp doanh nghiệp triển khai ứng dụng nhanh hơn, sử dụng tài nguyên hiệu quả hơn và thuận tiện khi mở rộng hệ thống.

## 2. So sánh kiến trúc Virtual Machine và Container

| Tiêu chí | Virtual Machine (VM) | Container |
|---|---|---|
| Dung lượng Image | Thường lớn do bao gồm hệ điều hành khách và các thành phần cần thiết. | Thường nhỏ hơn vì chủ yếu chứa ứng dụng, thư viện và các thành phần phụ thuộc. |
| Thời gian khởi động | Thường mất từ vài giây đến vài phút để khởi động hệ điều hành. | Thường khởi động trong vài giây hoặc nhanh hơn. |
| Hao phí OS | Mỗi VM thường chạy một hệ điều hành khách riêng, tiêu tốn RAM, CPU và dung lượng lưu trữ. | Các container trên cùng máy chủ Linux chia sẻ Kernel nên giảm hao phí tài nguyên hệ điều hành. |
| Mức độ cô lập | Cô lập mạnh hơn ở cấp máy ảo, có Kernel riêng cho từng hệ điều hành khách. | Cô lập bằng các cơ chế của Kernel, nhưng không có Kernel riêng cho từng container. |
| Khả năng mở rộng | Có thể mở rộng nhưng việc tạo và quản lý VM thường tốn nhiều tài nguyên hơn. | Có thể tạo, xóa và triển khai nhiều container nhanh chóng, thuận tiện cho hệ thống cần mở rộng linh hoạt. |

Nhìn chung, VM phù hợp khi cần môi trường hệ điều hành riêng biệt hoặc ranh giới cô lập mạnh. Container phù hợp với các ứng dụng cần triển khai nhanh, đóng gói nhất quán giữa môi trường phát triển và môi trường chạy thật. Hai công nghệ có thể kết hợp với nhau, chẳng hạn chạy Docker bên trong một VM.

## 3. Cơ chế cô lập và quản lý tài nguyên của Container

### 3.1. Namespaces

Namespaces là cơ chế của Linux Kernel giúp tạo ra các không gian nhìn nhận tài nguyên riêng biệt cho từng nhóm tiến trình. Nhờ đó, mỗi container có thể hoạt động như một môi trường độc lập với các container khác.

Ba loại Namespaces quan trọng gồm:

- **PID Namespace:** Cô lập không gian định danh tiến trình (Process ID). Tiến trình trong container có thể nhìn thấy các tiến trình thuộc namespace của nó thay vì toàn bộ tiến trình trên máy chủ.
- **Network Namespace:** Cô lập các tài nguyên mạng như giao diện mạng, bảng định tuyến và thông tin cổng mạng. Container có thể có môi trường mạng riêng.
- **Mount Namespace:** Cô lập góc nhìn về các điểm gắn kết hệ thống tệp (Mount points). Container có thể sử dụng cấu trúc hệ thống tệp riêng mà không cần nhìn thấy toàn bộ các điểm gắn kết của máy chủ.

Namespaces giúp cô lập môi trường hoạt động nhưng không có nghĩa là container hoàn toàn an toàn tuyệt đối. Mức độ an toàn còn phụ thuộc vào quyền truy cập, cấu hình bảo mật và cách vận hành hệ thống.

### 3.2. Control Groups (cgroups)

Control Groups, thường được viết tắt là cgroups, là cơ chế của Linux Kernel dùng để theo dõi, phân loại và quản lý mức sử dụng tài nguyên của các nhóm tiến trình.

Hai tài nguyên quan trọng gồm:

- **CPU:** Có thể giới hạn hoặc phân bổ mức sử dụng CPU cho container. Điều này giúp một ứng dụng không chiếm toàn bộ năng lực xử lý của máy chủ.
- **RAM:** Có thể đặt giới hạn bộ nhớ cho container. Khi container vượt giới hạn, hệ thống có thể từ chối cấp phát thêm bộ nhớ hoặc kích hoạt cơ chế xử lý khi thiếu bộ nhớ, tùy tình huống và cấu hình.

Namespaces chịu trách nhiệm chính về cô lập không gian tài nguyên, trong khi cgroups chịu trách nhiệm chính về quản lý và giới hạn tài nguyên. Hai cơ chế kết hợp giúp container hoạt động độc lập hơn và hạn chế ảnh hưởng đến các ứng dụng khác trên cùng máy chủ.

## 4. Thông số Docker trên hệ thống

Các thông số dưới đây được trích xuất từ kết quả thực tế của lệnh `docker info` trên máy tính đang kiểm tra.

| Thông số | Giá trị thực tế |
|---|---|
| Server Version |   29.6.1 |
| Cgroup Driver |  cgroupfs |
| Containers | 5 |
| Images | 7 |

## 5. Kết luận

Virtual Machine và Container đều là những công nghệ quan trọng trong triển khai ứng dụng hiện đại. VM cung cấp môi trường hệ điều hành riêng biệt, còn Container giúp đóng gói ứng dụng gọn nhẹ và triển khai nhanh bằng cách chia sẻ Kernel của hệ điều hành máy chủ.

Nhờ Namespaces và cgroups, Linux cung cấp các cơ chế nền tảng để cô lập tiến trình, mạng, hệ thống tệp và kiểm soát việc sử dụng CPU, RAM. Vì vậy, Docker giúp doanh nghiệp tận dụng tài nguyên hiệu quả hơn, chuẩn hóa môi trường triển khai và mở rộng ứng dụng linh hoạt.

Tuy nhiên, Container không thay thế hoàn toàn VM. Việc lựa chọn công nghệ cần dựa trên yêu cầu về bảo mật, khả năng tương thích, hiệu năng và kiến trúc hệ thống.
