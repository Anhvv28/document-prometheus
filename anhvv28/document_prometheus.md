# Prometheus

## 1. overview

- Là hệ thống giám sát với tính năng thu thập và lưu trữ dữ lieu metric theo thời gian từ các service và application .
- Chủ yếu tập trung vào các metrics , sử dung **PromQL**(Prometheus Query language) để select dữ lieu metric.
- Support cảnh báo **(Alerting)** , tích hợp với cảnh báo **Alertmanager** .

## 2. Metrics

- **Metric name** là tên duy nhất xác định một loại số liệu cụ thể mà Prometheus thu thập. Tên của metric thường phản ánh loại thông tin mà metric đó đo lường, ví dụ như số lượng yêu cầu HTTP, mức sử dụng CPU, dung lượng bộ nhớ, v.v.

  - **quy tắc đặt tên metric**: Tên của metric phải bắt đầu bằng một ký tự chữ cái và chỉ có thể chứa các ký tự chữ cái, số và dấu gạch dưới (_).

  - **Cấu trúc metric name**: Nên đặt tên metric theo cấu trúc bao gồm loại tài nguyên hoặc dịch vụ được đo lường và chỉ số cụ thể.

```sh
Ví dụ,với các chỉ số về HTTP request, một metric có thể có tên là :
http_requests_total (tổng số yêu cầu HTTP).
Đây là một metric đo lường tổng số lượng yêu cầu HTTP đến một máy chủ.
```

- **Metric Label** là các cặp giá trị key-value được gắn kèm với một metric, giúp cung cấp ngữ cảnh bổ sung và làm rõ hơn về dữ liệu được thu thập. Các nhãn cho phép bạn phân loại, chia nhỏ và lọc dữ liệu metric theo các thuộc tính cụ thể, chẳng hạn như loại yêu cầu HTTP, mã trạng thái trả về, hoặc nguồn gốc của yêu cầu.
   - **Key (tên nhãn)**: Tên của nhãn, như method, status_code, instance.
   - **Value (giá trị nhãn)**: Giá trị tương ứng với nhãn, như GET, 200, web-1.
```sh
VD:
http_requests_total{method="GET", status_code="200"}
- Metric name: http_requests_total — tổng số lượng yêu cầu HTTP.
- Labels:
 - method="GET" — phương thức HTTP là GET.
 - status_code="200" — mã trạng thái trả về là 200 (OK).
```
- Đếm số lượng yêu cầu HTTP đến server theo từng phương thức HTTP (GET, POST) và mã trạng thái trả về (200, 404, 500, v.v.).
```sh
Metric Name: http_requests_total — Tổng số lượng yêu cầu HTTP.
Metric Labels:
method — nhãn mô tả phương thức HTTP (GET, POST, v.v.).
status_code — nhãn mô tả mã trạng thái HTTP (200, 404, 500, v.v.).
Kết quả:
http_requests_total{method="GET", status_code="200"} 2500
http_requests_total{method="POST", status_code="404"} 15
http_requests_total{method="GET", status_code="500"} 5
```
## 3. Funciton
- Prometheus hỗ trợ nhiều function trong PromQL để xử lý và phân tích dữ liệu metrics. Một số function phổ biến bao gồm:
```sh
rate(): Tính toán tốc độ thay đổi của các counter theo thời gian.
  Ví dụ: rate(http_requests_total[5m]) tính tốc độ thay đổi request HTTP trong 5 phút qua.

increase(): Tính sự gia tăng của một counter qua một khoảng thời gian.
  Ví dụ: increase(http_requests_total[1h]) tính tổng số lượng request HTTP trong 1 giờ qua.

avg_over_time(): Tính giá trị trung bình của metric qua một khoảng thời gian.
  Ví dụ: avg_over_time(node_cpu_seconds_total[5m]) tính trung bình thời gian CPU trong 5 phút qua.

histogram_quantile(): Tính phần trăm phân vị của histogram.
  Ví dụ: histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket[5m])) by (le)) tính phần trăm phân vị 95th của thời gian phản hồi HTTP request.
```
## 4. Alerting Rule
