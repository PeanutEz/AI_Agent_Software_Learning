# 🧩 Microservice — Tổng quan chi tiết (kèm ví dụ)

> 🎯 **Microservices** là kiến trúc trong đó hệ thống được chia thành nhiều **service nhỏ**, mỗi service tập trung vào **một capability nghiệp vụ**, có thể **triển khai (deploy) độc lập**, và giao tiếp qua **network** (HTTP/gRPC/message broker).

## 🧭 Mục lục
- 🔎 Microservice là gì? (điểm phân biệt với monolith)
- ✅ Khi nào nên dùng / ❌ khi nào không
- 🧩 Cách tách service (Bounded Context, business capability)
- 🗄️ Dữ liệu trong microservices (database per service)
- 🔗 Giao tiếp: sync vs async
- 🔄 Tính nhất quán (consistency), Saga
- 🚪 API Gateway & BFF
- 🧱 Resilience (timeout/retry/circuit breaker)
- 🔍 Observability (log/metrics/tracing)
- 🚚 Deploy, versioning, migration
- 🔐 Security (service-to-service)
- ⚠️ Sai lầm phổ biến
- 🧪 Ví dụ: E-commerce microservices

---

## 🔎 1) Microservice là gì? (hiểu đúng)
### ✅ Microservice có 3 đặc trưng quan trọng
- 🧩 **Business-focused**: chia theo nghiệp vụ/capability, không chia theo kỹ thuật (UI/DB)
- 🚀 **Independent deployable**: mỗi service có vòng đời build/deploy riêng
- 🧠 **Owns its data**: mỗi service sở hữu dữ liệu và luật của mình (thường “database per service”)

### 🧱 Microservice vs Monolith
- 🧱 **Monolith**: 1 codebase/1 deployable (có thể modular monolith vẫn rất tốt)
- 🧩 **Microservices**: nhiều deployable → linh hoạt, nhưng phức tạp vận hành cao

📌 Nhận xét thực dụng:
- Microservices không “xịn hơn” monolith; nó là **đánh đổi** để đổi lấy khả năng scale team/deploy.

---

## ✅ 2) Khi nào nên dùng? Khi nào không?

## ✅ Nên dùng microservices khi
- 👥 Nhiều team làm song song, cần deploy độc lập
- 🔁 Domain lớn, ranh giới nghiệp vụ rõ (có Bounded Context)
- 📈 Một số phần cần scale khác nhau (payment/search/high traffic)
- 🧰 Tổ chức đã có năng lực vận hành: CI/CD, monitoring, on-call, SRE

## ❌ Không nên (hoặc chưa nên) khi
- 🧑‍💻 Team nhỏ, sản phẩm còn thay đổi nhanh, chưa ổn định domain
- 🧪 Chưa có observability/CI-CD tốt
- 🧩 Chia service theo cảm tính (“tách cho vui”) → distributed monolith

⭐ Lời khuyên: thường nên bắt đầu bằng **Modular Monolith**, sau đó tách dần khi có “pain thật”.

---

## 🧩 3) Cách tách service (decomposition)
### 🎯 Nguyên tắc tách service
- 🧠 Theo **business capability** / **Bounded Context** (DDD)
- 🔒 Mỗi service có **data + logic** của riêng nó
- 📦 Mỗi service có **API hợp đồng rõ ràng**

### 📌 Tránh tách theo lớp kỹ thuật
- ❌ UserService chỉ chứa controller, DBService chứa SQL… (sai)
- ✅ UserService sở hữu nghiệp vụ user: đăng ký, xác thực, hồ sơ...

### 🧭 Tín hiệu ranh giới tốt
- Từ vựng/thuật ngữ thay đổi theo ngữ cảnh
- Các luật nghiệp vụ khác nhau rõ ràng
- Mỗi phần có vòng đời release độc lập

---

## 🗄️ 4) Dữ liệu trong microservices
### 🥇 Database per service
- ✅ Mỗi service có database/schema riêng
- ✅ Service khác không được query DB trực tiếp

### 🔁 Join dữ liệu giữa services làm sao?
- ❌ Không join cross-db như monolith
- ✅ Dùng:
  - API call (sync) khi cần “đọc tức thì”
  - Event + read model/materialized view (async) khi cần tổng hợp

### 🧾 Ví dụ: Order cần thông tin Customer
- Cách sync: Order gọi Customer API để lấy snapshot
- Cách async: Customer phát event `CustomerUpdated` → Order cập nhật bản copy (denormalized)

---

## 🔗 5) Giao tiếp giữa services: sync vs async
### ☎️ Sync (HTTP/gRPC)
✅ Phù hợp khi:
- cần phản hồi ngay (user request)
- luồng đơn giản

⚠️ Rủi ro:
- latency cộng dồn, dễ cascade failure

### 📣 Async (Message broker: Kafka/RabbitMQ/SQS…)
✅ Phù hợp khi:
- side-effects, workflow dài
- cần tách rời, chịu lỗi tốt hơn

⚠️ Rủi ro:
- eventual consistency, debug khó hơn

---

## 🔄 6) Consistency & Saga (quan trọng)
Trong microservices, rất khó dùng transaction ACID xuyên nhiều DB.

### ✅ Saga pattern
- Saga = chuỗi bước, mỗi bước là transaction cục bộ
- Nếu bước sau fail → chạy **compensation** (hoàn tác) các bước trước

2 kiểu triển khai:
- 🎼 **Orchestration**: 1 saga orchestrator điều phối
- 🕺 **Choreography**: các service phản ứng qua event (không có “nhạc trưởng”)

📌 Gợi ý thực dụng:
- Workflow phức tạp → orchestration dễ kiểm soát hơn
- Workflow đơn giản → choreography nhẹ hơn

---

## 🚪 7) API Gateway & BFF
### 🚪 API Gateway
- 1 “cửa” cho client: auth, rate limit, routing, aggregation

### 🧩 BFF (Backend For Frontend)
- Mỗi frontend (web/mobile) có backend “tùy biến” để giảm chatty calls

---

## 🧱 8) Resilience patterns (để hệ thống không sập dây chuyền)
- ⏱️ **Timeout**: luôn đặt timeout cho call network
- 🔁 **Retry** có backoff + jitter (tránh retry storm)
- 🧯 **Circuit breaker**: ngắt khi downstream lỗi liên tục
- 🧊 **Bulkhead**: cô lập tài nguyên (thread pool/queue)
- ✅ **Idempotency**: request lặp không gây double charge/double create

---

## 🔍 9) Observability
Microservices bắt buộc phải “nhìn thấy được” hệ thống:
- 🧾 **Centralized logging**: có correlation id
- 📈 **Metrics**: p95/p99 latency, error rate, saturation
- 🧵 **Distributed tracing**: trace xuyên service

📌 Tip: mọi request nên có `trace_id` / `correlation_id`.

---

## 🚚 10) Deploy, versioning, migration
- 🚀 CI/CD cho từng service
- 🧾 Version API (backward compatible)
- 🧩 Migration schema: từng service tự quản, tránh breaking
- 🐳 Thường dùng container (Docker) + orchestrator (Kubernetes) khi hệ lớn

---

## 🔐 11) Security service-to-service
- 🔑 AuthN/AuthZ: mTLS/JWT/OAuth2 tùy mô hình
- 🧾 Principle of least privilege
- 🛡️ Secret management (Vault/KMS)

---

## ⚠️ 12) Sai lầm phổ biến
- 🧨 **Distributed monolith**: service tách ra nhưng phụ thuộc chặt, deploy không độc lập
- 🧨 Chatty communication: 1 request gọi 10 service
- 🧨 Thiếu observability/on-call → sự cố không điều tra nổi
- 🧨 Không có idempotency → thanh toán bị double
- 🧨 Tách service trước khi hiểu domain → phải gộp lại

---

## 🧪 13) Ví dụ: E-commerce microservices
### 🧩 Các service mẫu
- 🧑 Customer Service: hồ sơ khách, địa chỉ
- 📦 Catalog Service: sản phẩm, giá
- 🛒 Order Service: giỏ/đơn hàng, trạng thái đơn
- 💳 Payment Service: thanh toán, hoàn tiền
- 🧮 Inventory Service: tồn kho
- 🚚 Shipping Service: giao hàng
- 📣 Notification Service: email/sms

### 🔄 Luồng “đặt hàng và thanh toán” (choreography bằng event)
1) 🛒 Order tạo đơn: `OrderCreated`
2) 🧮 Inventory nghe event → giữ hàng (reserve) → `InventoryReserved`
3) 💳 Payment nghe event → tạo payment → `PaymentSucceeded` hoặc `PaymentFailed`
4) 🛒 Order cập nhật trạng thái theo event
5) 🚚 Shipping tạo shipment sau khi payment thành công
6) 📣 Notification gửi mail

### 🧾 Pseudo-event
- `OrderCreated { orderId, customerId, items }`
- `InventoryReserved { orderId }`
- `PaymentSucceeded { orderId, txnId }`
- `PaymentFailed { orderId, reason }`

### 🧬 Saga compensation (khi Payment fail)
- PaymentFailed → InventoryService “release reservation”
- OrderService chuyển trạng thái `CANCELLED`

---

## ✅ Checklist áp dụng microservices
- 🧩 Có ranh giới Bounded Context rõ chưa?
- 🗄️ Có database per service chưa? (không query chéo DB)
- 📣 Có event + idempotency + retry policy?
- 🔍 Có logging/metrics/tracing đầy đủ?
- 🚀 Có CI/CD, rollout strategy (blue/green/canary)?
- 🧯 Có circuit breaker/timeout/bulkhead?
- 👥 Có mô hình ownership & on-call rõ?

---

## 📝 Ghi chú của tôi
- 🎯 Mục tiêu dùng microservices (scale team/scale traffic/independent deploy): …
- 🧩 Dự án của tôi có những bounded context: …
- ⚠️ Rủi ro lớn nhất nếu tách sớm: …
