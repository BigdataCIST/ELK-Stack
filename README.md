# ELK Stack

### Mục lục 
* [1. Tổng quan về ELK Stack](#overview)
* [2. Cài đặt](#install)
* [3. Xây dựng chương trình](#application)

<a name="overview"></a>
## 1. Tổng quan về ELK Stack

- **ELK Stack** là tập hợp của 3 sản phẩm mã nguồn mở là Elasticsearch, Logstash và Kibana được sử dụng cho việc quản lý log. Tuy nhiên sau này sự ra đời của Beats để shipping logs do đó ELK Stack thường được gọi luôn là Elastic Stack. 
-  **Luồng hoạt động**:
    - **Filebeat**: là một beats application, nó có nhiệm vụ theo dõi sự thay đổi trong các log files rồi sau đó forwards những sự thay đổi đó qua cho logstash.
    - **Logstash**: có nhiệm vụ nhận logs từ Filebeat, xong sẽ thực hiện việc filter trên các logs đó, rồi cuối cùng là gửi lên cho Elasticsearch
    - **Elasticsearch**: là một NoSQL database đã được optimized để lưu trữ document có cấu trúc (structured documents). Structured documents được lưu dưới dạng JSON
    - **Kibana** sẽ là giao diện hiển thị dữ liệu của Elasticsearch thành đồ thị, graphs, tables để người dùng có thể dễ dàng hình dung, đưa ra các phân tích, quyết định dựa trên các biểu đồ, hình vẽ,…

![elk_stack](https://user-images.githubusercontent.com/103992475/182336538-2583ec35-aeab-4384-9186-67dabbdb1d04.png)


<a name="install"></a>
## 2. Cài đặt 

<a name="application"></a>
## 3. Xây dựng chương trình 
