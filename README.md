# ELK Stack

### Mục lục 
* [1. Tổng quan về ELK Stack](#overview)
* [2. Cài đặt](#install)
    * [2.1 Cài đặt ELK Stack](#elk_stack_install)
    * [2.2 Cài đặt FileBeat](#filebeat_install)
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
* Cài đặt gồm 2 phần:
    * **Chương trình và Filebeat:** Chương trình được viết bằng python để sinh ra file log, trong khí đó filebeat được cài trên máy để đọc và gửi file log qua ELK stack.
    * **ELK Stack:** monitor và phân tích log, được cài đặt bằng docker-compose.  

Hiện tại trong chương trình đang dùng version 7.

<a name="elk_stack_install"></a>
### 2.1 Cài đặt ELK Stack 

* Tạo file *logstash.conf* trong folder logstash/config
   ```
   input {
      beats {
        port => 5044
            }
        }
   filter {
      json {
           source => "message"
           }
         }
   output {
      elasticsearch {
           hosts => [ "elasticsearch" ]
                    }
         }
   ```
*  File *docker-compose.yml*
   ```
   version: '3.3'
   services:
     elasticsearch:
       image: docker.elastic.co/elasticsearch/elasticsearch:7.7.1
       container_name: 'elasticsearch'
       volumes:
         - data:/usr/share/elasticsearch/data
       environment:
         node.name: es01
         cluster.name: es-docker-cluster
         ES_JAVA_OPTS: '-Xms256m -Xmx256m'
         network.bind_host: 0.0.0.0
         network.host: 0.0.0.0
         discovery.type: single-node
       ports:
         - '9200:9200'
         - '9300:9300'
       # ulimits:
       #   memlock:
       #     soft: -1
       #     hard: -1
       networks:
         - elk
     kibana:
       image: docker.elastic.co/kibana/kibana:7.7.1
       ports:
         - "5601:5601"
       depends_on:
         - elasticsearch
       networks:
         - elk
     logstash:
       image: docker.elastic.co/logstash/logstash:7.7.1
       volumes:
         - ./logstash/config/logstash.conf:/usr/share/logstash/pipeline/logstash.conf:ro
       ports:
         - "5044:5044/tcp"
         - "5000:5000/udp"
         - "9600:9600"
       environment:
         LS_JAVA_OPTS: "-Xmx256m -Xms256m"
       networks:
         - elk
       depends_on:
         - elasticsearch

   networks:
     elk:
       driver: bridge

   volumes:
     data:
   ```
* Run docker:
   ```
   docker compose up -d
   ```

<a name="filebeat_install"></a>
### 2.2 Cài đặt FileBeat

* Cài đặt trên Ubuntu 20.04:
    * Tải file:
    ```
    curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.7.1-linux-x86_64.tar.gz
    ```
    * Giải nén:
    ```
    tar xzvf filebeat-7.7.1-linux-x86_64.tar.gz
    ```
    * CD vào folder vừa được giải nén sau khi tải về để sửa file filebeat.yml như sau:
    ```
   filebeat.inputs:
   - type: log
      enabled: true
      paths:
         - /Users/locpham/workspace/go-elasticsearch/logs/go.log
   output.logstash:
      hosts: ["localhost:5044"]
    ```
    ***Chú ý:*** paths - định nghĩa đường dẫn chứa file log để push lên elasticsearch
   * Chạy filebeat:
   ```
   sudo chown root filebeat.yml
   sudo ./filebeat -e
   ```

* Cài đặt trên hệ điệu hành khác:
[tài liệu tham khảo](https://www.elastic.co/guide/en/beats/filebeat/7.7/filebeat-getting-started.html)

<a name="application"></a>
## 3. Xây dựng chương trình 
