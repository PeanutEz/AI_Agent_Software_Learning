# 🧩 DDD (Domain-Driven Design) — Tổng quan

> 🎯 **Mục tiêu của DDD**: thiết kế phần mềm xoay quanh **nghiệp vụ (domain)** để code phản ánh đúng thực tế, dễ mở rộng và giảm rủi ro “logic bị rải rác”.

## 🧭 Mục lục
- 🔎 DDD là gì? (Hiểu đúng trước khi dùng)
- 🧠 Tư duy cốt lõi
- 🗺️ Strategic DDD: Bounded Context, Ubiquitous Language, Context Map
- 🧱 Tactical DDD: Entity, Value Object, Aggregate, Domain Event…
- 🏗️ Kiến trúc hay dùng (Layers / Hexagonal)
- 🧪 Ví dụ mini: Đặt hàng
- ✅ Checklist áp dụng DDD cho dự án
- ⚠️ Sai lầm phổ biến
- 📚 Lộ trình học nhanh

---

## 🔎 DDD là gì? (và không phải là gì)
- ✅ **DDD là cách tiếp cận** để **khám phá + mô hình hóa nghiệp vụ** và phản ánh mô hình đó vào code.
- ✅ DDD ưu tiên **đúng nghiệp vụ** và **tính tiến hóa** (evolvability) của hệ thống.
- ❌ DDD **không đồng nghĩa** Microservices.
- ❌ DDD **không phải** “tạo nhiều class cho sang”.
- ❌ DDD **không bắt buộc** dùng CQRS/Event Sourcing (chỉ là kỹ thuật hay đi kèm trong một số bài toán).

---

## 🧠 Tư duy cốt lõi
### 🗣️ 1) Ubiquitous Language (Ngôn ngữ chung)
- 📌 Bạn và chuyên gia nghiệp vụ thống nhất **từ vựng** và dùng nó xuyên suốt: tài liệu, cuộc họp, tên code.
- 🧩 Ví dụ: “Đơn hàng”, “Thanh toán”, “Giữ hàng”, “Hoàn tiền”, “Hủy đơn”, “Số dư ví”…

### 🧭 2) Domain trước, kỹ thuật sau
- 🥇 Ưu tiên làm rõ luật nghiệp vụ và biến nó thành **hành vi trong domain**.
- 🧰 Framework/DB/queue chỉ là “hạ tầng” phục vụ domain.

### 🧱 3) Bảo vệ luật nghiệp vụ (Invariants)
- ✅ Những luật kiểu “không được phép…” nên được **canh giữ ngay trong domain model**.
- 🎯 Mục tiêu: trạng thái domain luôn hợp lệ sau mỗi thao tác.

---

## 🗺️ Strategic DDD (Thiết kế chiến lược)
Strategic DDD giúp chia hệ thống theo đúng ranh giới nghiệp vụ để giảm “dính chùm”.

### 🧩 1) Bounded Context (Ngữ cảnh giới hạn)
- 📦 Một **Bounded Context** là nơi mà các khái niệm có **một nghĩa nhất quán**.
- 🧠 Cùng một từ có thể nghĩa khác ở context khác.

**Ví dụ** (E-commerce):
- 🛒 **Sales Context**: Order, Cart, Promotion
- 🧾 **Billing Context**: Invoice, Payment, Refund
- 📦 **Fulfillment Context**: Shipment, Warehouse, Picking

### 🧭 2) Context Map (Bản đồ ngữ cảnh)
- 🔗 Mô tả cách các context tích hợp:
  - ⬆️ Upstream / ⬇️ Downstream (bên nào “định nghĩa sự thật”)
  - 🛡️ **ACL (Anti-Corruption Layer)**: lớp chuyển đổi để tránh “nhiễm” model từ hệ khác
  - 📣 Tích hợp bằng API hay Event (Domain Event / Integration Event)

---

## 🧱 Tactical DDD (Thiết kế chiến thuật)
Đây là “bộ lego” để bạn mô hình hóa nghiệp vụ trong code.

### 👤 Entity
- 🪪 Có **identity** (ID) và tồn tại theo thời gian.
- ✅ So sánh bằng ID.

### 💎 Value Object
- 🧾 Không có identity, so sánh bằng **giá trị**.
- 🧊 Thường immutable.
- 🧩 Ví dụ: Money, Email, Address.

### 🧩 Aggregate & Aggregate Root
- 📦 **Aggregate**: một cụm object cần nhất quán theo luật.
- 🚪 **Aggregate Root**: “cổng duy nhất” để sửa bên trong aggregate.
- 🧠 Quy tắc thực dụng:
  - ✅ Mọi thay đổi quan trọng đi qua Root
  - ✅ Invariants được kiểm soát trong Aggregate
  - ⚠️ Tránh aggregate quá to (transaction nặng, lock nhiều)

### 🗄️ Repository
- 📥/📤 Cung cấp cách lấy/lưu **Aggregate Root**.
- ✅ Domain không phụ thuộc DB cụ thể.

### 🧠 Domain Service
- 🧰 Chứa nghiệp vụ quan trọng nhưng **không thuộc về một entity/value object**.
- 📌 Ví dụ: tính phí vận chuyển dựa trên nhiều yếu tố.

### 📣 Domain Event
- 🗞️ Sự kiện nghiệp vụ đã xảy ra: `OrderPaid`, `OrderCancelled`.
- 🎯 Dùng để tách “hậu quả” khỏi hành vi chính.

---

## 🏗️ Kiến trúc hay dùng cùng DDD
### 🧅 Layered (đơn giản, phổ biến)
- 🧠 **Domain**: model + luật (quan trọng nhất)
- 🎮 **Application**: use-case orchestration (gọi domain, repo, publish event)
- 🧱 **Infrastructure**: DB, message broker, email…
- 🌐 **Presentation**: API/CLI/UI

### 🔌 Hexagonal / Clean Architecture (khi dự án lớn)
- 🎯 Domain ở giữa
- 🔁 Phụ thuộc hướng vào trong (ports & adapters)

---

## 🧪 Ví dụ mini: Đặt hàng (Order)
### 🎯 Luật (invariants) mẫu
- ❌ Không thể thanh toán đơn đã bị hủy.
- ❌ Không thể thêm sản phẩm sau khi đã thanh toán.
- ✅ Tổng tiền = tổng line items + phí ship − giảm giá.

### 🧩 Gợi ý mô hình
- 🧾 `Order` (Aggregate Root)
- 📄 `OrderLine` (Entity con)
- 💎 `Money`, `Address` (Value Object)
- 📣 Domain Events: `OrderPlaced`, `OrderPaid`

### 🧠 Cách viết “đúng tinh thần DDD”
- ✅ Thay vì: `OrderService.pay(orderId)` rồi update DB rải rác
- ✅ Làm: `order.pay(paymentInfo)` và chính `Order` kiểm tra luật

---

## ✅ Checklist áp dụng DDD (thực dụng)
1) 🗣️ Chốt từ vựng nghiệp vụ (Ubiquitous Language)
2) 🗺️ Vẽ Bounded Context sơ bộ (đừng tách quá sớm)
3) 🧩 Chọn 1 context quan trọng nhất để làm trước
4) 🧱 Tìm Aggregate Root và các invariants quan trọng
5) 🧪 Viết use-cases ở Application layer (thin)
6) 🗄️ Đặt Repository cho aggregates
7) 📣 Dùng Domain Event cho side-effects (gửi mail, đồng bộ, analytics…)

---

## ⚠️ Sai lầm phổ biến
- 🧟 **Anemic Domain Model**: domain chỉ có data, còn luật nằm hết ở service.
- 🧩 Tách quá nhiều bounded context khi chưa hiểu nghiệp vụ.
- 🏷️ Dùng thuật ngữ kỹ thuật thay vì thuật ngữ nghiệp vụ.
- 🧱 Aggregate quá lớn: mọi thứ “dồn vào một root” → chậm, khó đồng bộ.

---

## 📚 Lộ trình học nhanh (7 ngày)
- 📌 Ngày 1: Ubiquitous Language + ví dụ thuật ngữ
- 🗺️ Ngày 2: Bounded Context + Context Map (vẽ sơ đồ)
- 🧱 Ngày 3: Entity vs Value Object (làm bài tập nhỏ)
- 🧩 Ngày 4: Aggregate + invariants (mô hình Order)
- 🗄️ Ngày 5: Repository + Unit of Work (khái niệm)
- 📣 Ngày 6: Domain Event + tách side-effects
- 🧰 Ngày 7: Tổng hợp: viết 3 use-case end-to-end

---

## 📝 Ghi chú của tôi
- 💡 Domain quan trọng nhất trong bài toán của tôi là: …
- 🧩 3 thuật ngữ nghiệp vụ hay bị hiểu khác nhau: …
- ⚠️ 3 invariants quan trọng nhất cần bảo vệ: …
