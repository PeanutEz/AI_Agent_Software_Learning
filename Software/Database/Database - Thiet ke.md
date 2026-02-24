# 🏗️ Thiết kế Database — Hướng dẫn chi tiết (kèm ví dụ)

> 🎯 Mục tiêu: thiết kế database **đúng nghiệp vụ**, **dễ mở rộng**, **hiệu năng ổn**, và **dễ vận hành**.

## 🧭 Mục lục
- 🧠 Tư duy tổng quan: DB để làm gì?
- 🗺️ Quy trình thiết kế chuẩn
- 🧩 Mô hình dữ liệu: Conceptual → Logical → Physical
- 🔗 Quan hệ, khóa, ràng buộc (PK/FK/Unique/Check)
- 🧼 Chuẩn hóa (Normalization) & khi nào denormalize
- 🧬 Table inheritance (kế thừa bảng)
- 🧷 Index: chọn đúng theo query
- 🧾 Naming, kiểu dữ liệu, audit fields
- 🔒 Transaction, consistency, concurrency
- 🧳 Multi-tenancy, partitioning, archiving
- 🚚 Migration & versioning schema
- 🧪 Ví dụ hoàn chỉnh: E-commerce (đặt hàng)
- ✅ Checklist review database

---

## 🧠 1) Tư duy tổng quan
### ✅ Database thiết kế tốt thường có
- 🧾 **Dữ liệu đúng**: ràng buộc chặt chẽ, ít “rác”
- 🔍 **Truy vấn dễ**: cấu trúc rõ, join hợp lý
- ⚡ **Hiệu năng ổn**: index đúng, bảng không phình vô tội vạ
- 🔒 **An toàn**: phân quyền, audit, backup/restore
- 🔁 **Dễ tiến hóa**: migration rõ ràng, ít phá vỡ ngược

### ⚠️ Thiết kế DB không chỉ là “tạo bảng”
- DB là phần **tối quan trọng** của hệ thống: nếu model sai, code phía trên sẽ phải “chữa cháy” mãi.

---

## 🗺️ 2) Quy trình thiết kế chuẩn
### 🧭 Bước 1: Làm rõ nghiệp vụ & use-case
- 🗣️ Thuật ngữ nghiệp vụ: Customer/Order/Payment/Refund/Shipment…
- 🧩 Luồng chính: tạo đơn → thanh toán → giao hàng → hoàn trả
- 📌 Câu hỏi vàng:
  - Hệ thống cần lưu *cái gì*?
  - Trạng thái nào quan trọng? (state machine)
  - Báo cáo nào cần? (analytics/reporting)

### 🧩 Bước 2: Conceptual model (ERD cấp khái niệm)
- 🎯 Xác định entity & quan hệ ở mức “người đọc hiểu”

### 🧱 Bước 3: Logical model
- 🧾 Xác định bảng, cột, PK/FK, unique, cardinality (1-1, 1-n, n-n)
- 🧼 Chuẩn hóa

### ⚙️ Bước 4: Physical model
- 🧷 Index, partition, data types, default values
- 🔒 Transaction boundaries, constraints thực thi
- 🚚 Migration plan

### 🧪 Bước 5: Validate bằng query thật
- 🔍 Viết vài query đại diện: list, search, report
- 📏 Ước lượng dữ liệu (10k? 10M? 1B?)

---

## 🧩 3) Mô hình dữ liệu: Conceptual → Logical → Physical
### 🧠 Conceptual (khái niệm)
- 🧑 Customer
- 🧾 Order
- 📦 Product
- 💳 Payment
- 🚚 Shipment

### 🧾 Logical (bảng/quan hệ)
- `customers` 1-n `orders`
- `orders` 1-n `order_items`
- `products` 1-n `order_items`
- `orders` 1-n `payments` (hoặc 1-1 tùy nghiệp vụ)

### ⚙️ Physical (tối ưu triển khai)
- Kiểu dữ liệu cụ thể
- Index theo query
- Constraint/check

---

## 🔗 4) Khóa & ràng buộc
### 🔑 Primary Key (PK)
- ✅ Khuyến nghị: **surrogate key** (int/bigint/uuid)
- 📌 Nếu dùng UUID:
  - ưu: an toàn phân tán
  - nhược: index to hơn, có thể fragment

### 🧷 Foreign Key (FK)
- ✅ Giúp **đúng dữ liệu** và hỗ trợ join logic
- ⚠️ Cân nhắc khi bulk write cực lớn (overhead)

### 🏷️ Unique constraint
- ✅ Ngăn trùng dữ liệu nghiệp vụ: email, order_code…

### ✅ Check constraint
- ✅ Ràng buộc miền giá trị: `quantity > 0`, `status IN (...)`

### 🧠 Cascade rules
- ⚠️ `ON DELETE CASCADE` tiện nhưng dễ “xóa dây chuyền”
- ✅ Với dữ liệu quan trọng, thường dùng **soft delete** hoặc chặn xóa

---

## 🧼 5) Chuẩn hóa (Normalization)
### 🎯 Mục tiêu
- Giảm trùng lặp
- Tránh update anomaly

### ✅ Quy tắc thực dụng
- Bắt đầu với **3NF** (thường đủ)
- Denormalize có kiểm soát khi:
  - query đọc nhiều và join nặng
  - dữ liệu ít thay đổi
  - chấp nhận eventual consistency

### 🧩 Denormalize thường gặp
- Lưu `order_total` trong `orders` thay vì tính lại từ `order_items` mỗi lần
- Lưu `customer_name_snapshot` trong đơn để “đóng băng” tại thời điểm mua

---

## 🧬 6) Table inheritance (kế thừa bảng)
Trong thiết kế database quan hệ, “inheritance của table” thường được hiểu là **cách mô hình hóa** quan hệ “cha–con” giữa các subtype (giống OOP), chứ không chỉ là một tính năng riêng của DB.

### 🧠 Khi nào bạn sẽ cần?
- 👤 Có entity chung và nhiều subtype: `person` → `employee`, `customer`
- 💳 Có thực thể kiểu “method”/“channel”: `payment_method` → `card`, `bank_transfer`, `wallet`
- 📄 Có “tài nguyên” nhiều loại: `asset` → `file`, `image`, `link`

### 🎯 Tiêu chí chọn mô hình
- 🔍 Query “tất cả subtype” có nhiều không?
- 🧷 Cần constraints chặt theo subtype đến mức nào?
- ⚡ Hiệu năng ưu tiên đọc hay ghi?
- 🧩 Subtype có nhiều field riêng hay chỉ 1–2 cột?

---

## 6.1 🧾 Single Table Inheritance (STI) — 1 bảng cho tất cả subtype
**Ý tưởng**: 1 bảng chứa cột chung + cột riêng, thêm cột `type` (discriminator).

✅ Ưu điểm
- ⚡ Query polymorphic (lấy “tất cả”) đơn giản, ít JOIN
- 🧠 Dễ triển khai ban đầu

⚠️ Nhược điểm
- 🕳️ Nhiều cột NULL
- 🧩 Ràng buộc “theo subtype” khó (thường cần CHECK/trigger)

**Ví dụ**
```sql
CREATE TABLE persons (
  id BIGINT PRIMARY KEY,
  type VARCHAR(16) NOT NULL, -- 'EMPLOYEE' | 'CUSTOMER'
  full_name VARCHAR(255) NOT NULL,

  -- employee-only
  employee_code VARCHAR(32) NULL,
  department VARCHAR(64) NULL,

  -- customer-only
  loyalty_level VARCHAR(16) NULL,
  marketing_opt_in BOOLEAN NULL,

  CONSTRAINT ck_persons_type CHECK (type IN ('EMPLOYEE', 'CUSTOMER'))
);
```

📌 Gợi ý constraint theo subtype (ý tưởng)
- Nếu DB hỗ trợ CHECK mạnh, bạn có thể ràng buộc kiểu:
  - `type = 'EMPLOYEE' → employee_code IS NOT NULL`
  - `type = 'CUSTOMER' → loyalty_level IS NOT NULL`

---

## 6.2 🧩 Class Table Inheritance (CTI / Joined) — tách bảng cha và bảng con
**Ý tưởng**: bảng cha chứa field chung; mỗi subtype có bảng riêng. PK bảng con **đồng thời** là FK trỏ về bảng cha (quan hệ 1-1).

✅ Ưu điểm
- 🧼 Không có NULL thừa
- ✅ Constraints rõ ràng cho từng subtype (NOT NULL, CHECK, unique…)

⚠️ Nhược điểm
- 🔁 Query theo subtype cần JOIN
- 🧾 Query “tất cả subtype” cần UNION hoặc view

**Ví dụ**
```sql
CREATE TABLE persons (
  id BIGINT PRIMARY KEY,
  full_name VARCHAR(255) NOT NULL,
  kind VARCHAR(16) NOT NULL,
  CONSTRAINT ck_person_kind CHECK (kind IN ('EMPLOYEE', 'CUSTOMER'))
);

CREATE TABLE employees (
  person_id BIGINT PRIMARY KEY,
  employee_code VARCHAR(32) NOT NULL,
  department VARCHAR(64) NOT NULL,
  CONSTRAINT fk_employees_person FOREIGN KEY (person_id) REFERENCES persons(id)
);

CREATE TABLE customers (
  person_id BIGINT PRIMARY KEY,
  loyalty_level VARCHAR(16) NOT NULL,
  marketing_opt_in BOOLEAN NOT NULL,
  CONSTRAINT fk_customers_person FOREIGN KEY (person_id) REFERENCES persons(id)
);
```

📌 Lưu ý quan trọng
- Cần tránh trường hợp 1 `person` vừa là employee vừa là customer (trừ khi nghiệp vụ cho phép).
- Thường sẽ dùng `kind` + logic đảm bảo dữ liệu đúng (tùy DB có thể dùng trigger/constraint).

---

## 6.3 🧱 Concrete Table Inheritance (table-per-concrete-class) — mỗi subtype 1 bảng đầy đủ
**Ý tưởng**: không có bảng cha; mỗi subtype có bảng riêng chứa cả cột chung + riêng.

✅ Ưu điểm
- ⚡ Query theo subtype nhanh, không JOIN
- 🧠 Constraints theo subtype đơn giản

⚠️ Nhược điểm
- ♻️ Trùng lặp cột chung → schema change tốn công
- 🧾 Query “tất cả subtype” phải UNION

**Ví dụ**
```sql
CREATE TABLE employees (
  id BIGINT PRIMARY KEY,
  full_name VARCHAR(255) NOT NULL,
  employee_code VARCHAR(32) NOT NULL,
  department VARCHAR(64) NOT NULL
);

CREATE TABLE customers (
  id BIGINT PRIMARY KEY,
  full_name VARCHAR(255) NOT NULL,
  loyalty_level VARCHAR(16) NOT NULL,
  marketing_opt_in BOOLEAN NOT NULL
);
```

---

## 6.4 🐘 PostgreSQL “table inheritance” (INHERITS) — nên hiểu để tránh nhầm
PostgreSQL có tính năng kế thừa bảng:

```sql
CREATE TABLE persons (
  id BIGINT PRIMARY KEY,
  full_name TEXT NOT NULL
);

CREATE TABLE employees (
  employee_code TEXT NOT NULL
) INHERITS (persons);
```

📌 Điểm cần nhớ
- Đây không giống “CTI” ở trên; nó là cơ chế đặc thù của PostgreSQL.
- Có nhiều khác biệt về index/constraint/FK và hành vi query (ví dụ query parent có thể “đi qua” child tùy cú pháp).
- Nếu mục tiêu là chia dữ liệu lớn theo thời gian, **partitioning khai báo** thường là lựa chọn phù hợp hơn.

---

## ✅ Chọn mô hình nhanh
- 🥇 STI: subtype ít cột riêng, query polymorphic nhiều, cần đơn giản
- 🥇 CTI: subtype nhiều cột riêng, cần constraints rõ, chấp nhận JOIN
- 🥇 Concrete: query theo subtype riêng là chính, chấp nhận UNION khi cần tổng hợp

---

## 🧷 7) Index: thiết kế theo query
### 🧠 Nguyên tắc
- 🧷 Index phục vụ `WHERE`, `JOIN`, `ORDER BY`, đôi khi `GROUP BY`
- ⚠️ Index nhiều quá làm write chậm

### 📌 Chọn index theo 3 câu hỏi
- Query lọc theo cột nào nhất?
- Sort theo cột nào nhất?
- Join theo cột nào nhất?

### 🧱 Composite index
- ✅ Tối ưu query dạng: `WHERE a = ? AND b = ? ORDER BY c`
- ⚠️ Thứ tự cột trong index rất quan trọng

### 🎁 Covering index
- ✅ Nếu query chỉ cần vài cột, thiết kế index “cover” để giảm lookup

---

## 🧾 8) Naming, data types, audit fields
### 🏷️ Naming
- ✅ Dùng `snake_case` hoặc `camelCase` nhất quán
- ✅ Bảng số nhiều (`orders`) hoặc số ít (`order`) — chọn 1 style
- ✅ Cột FK đặt rõ: `customer_id`

### 🧱 Data types
- ✅ Tiền tệ: tránh float. Dùng `DECIMAL(19,4)` hoặc lưu **integer cents**
- ✅ Thời gian: dùng UTC, lưu `timestamp` rõ timezone (tùy DB)
- ✅ Text: giới hạn độ dài nếu biết trước

### 🕵️ Audit fields
- Gần như dự án nào cũng nên có:
  - `created_at`, `updated_at`
  - `created_by`, `updated_by` (nếu cần)
  - `deleted_at` (soft delete)

---

## 🔒 9) Transaction, consistency, concurrency
### 🧠 Chọn mức consistency theo nghiệp vụ
- 💰 Tài chính: cần mạnh (strong consistency)
- 📊 Analytics: có thể eventual

### ⏳ Transaction boundary
- ✅ Transaction ngắn, chỉ bao gồm thao tác DB
- ❌ Tránh gọi API bên ngoài trong transaction (giữ lock lâu)

### 🔒 Tránh race condition
- ✅ Unique constraint + retry
- ✅ Optimistic locking (version)
- ✅ Idempotency key cho payment/webhook

---

## 🧳 10) Multi-tenancy, partitioning, archiving
### 🧩 Multi-tenancy (nhiều khách hàng/tenant)
- ✅ Option A: chung schema, thêm `tenant_id` vào mọi bảng
- ✅ Option B: schema riêng/DB riêng (cách ly tốt hơn)
- ⚠️ Luôn index theo `(tenant_id, ...)` nếu query theo tenant

### 🗂️ Partitioning
- ✅ Partition theo thời gian (log, order history)
- ✅ Query theo range nhanh hơn nếu có partition pruning

### 🧊 Archiving
- ✅ Đưa dữ liệu cũ sang bảng/DB lạnh
- ✅ Giữ bảng “hot” nhỏ để index hiệu quả

---

## 🚚 11) Migration & versioning schema
### ✅ Best practices
- 📦 Mỗi thay đổi schema là 1 migration có id
- 🔁 Migration **forward-only** (ít rollback), có kế hoạch fix bằng migration mới
- 🧩 Thay đổi lớn làm theo 2–3 bước:
  - thêm cột mới → ghi song song → backfill → chuyển đọc → xóa cột cũ

### ⚠️ Tránh downtime
- ✅ Thêm index concurrently/online (nếu DB hỗ trợ)
- ✅ Tránh lock bảng lớn lâu

---

## 🧪 12) Ví dụ hoàn chỉnh: E-commerce (đặt hàng)
### 🧩 Nghiệp vụ tối thiểu
- Customer đặt Order gồm nhiều items
- Mỗi item tham chiếu Product
- Order có trạng thái: `NEW`, `PAID`, `CANCELLED`, `SHIPPED`
- Payment lưu giao dịch

### 🗺️ ERD (mô tả bằng chữ)
- `customers (1) ─── (n) orders`
- `orders (1) ─── (n) order_items`
- `products (1) ─── (n) order_items`
- `orders (1) ─── (n) payments`

### 🧾 DDL minh họa (SQL chung)
> Lưu ý: cú pháp `CHECK`, `GENERATED`, `UUID`… có thể khác nhau tùy DB.

```sql
CREATE TABLE customers (
  id            BIGINT PRIMARY KEY,
  email         VARCHAR(255) NOT NULL,
  full_name     VARCHAR(255) NOT NULL,
  created_at    TIMESTAMP NOT NULL,
  updated_at    TIMESTAMP NOT NULL,
  CONSTRAINT uq_customers_email UNIQUE (email)
);

CREATE TABLE products (
  id            BIGINT PRIMARY KEY,
  sku           VARCHAR(64) NOT NULL,
  name          VARCHAR(255) NOT NULL,
  price_cents   BIGINT NOT NULL,
  created_at    TIMESTAMP NOT NULL,
  updated_at    TIMESTAMP NOT NULL,
  CONSTRAINT uq_products_sku UNIQUE (sku),
  CONSTRAINT ck_products_price CHECK (price_cents >= 0)
);

CREATE TABLE orders (
  id               BIGINT PRIMARY KEY,
  customer_id      BIGINT NOT NULL,
  status           VARCHAR(32) NOT NULL,
  total_cents      BIGINT NOT NULL,
  created_at       TIMESTAMP NOT NULL,
  updated_at       TIMESTAMP NOT NULL,
  CONSTRAINT fk_orders_customer FOREIGN KEY (customer_id) REFERENCES customers(id),
  CONSTRAINT ck_orders_total CHECK (total_cents >= 0)
);

CREATE TABLE order_items (
  id            BIGINT PRIMARY KEY,
  order_id      BIGINT NOT NULL,
  product_id    BIGINT NOT NULL,
  quantity      INT NOT NULL,
  unit_cents    BIGINT NOT NULL,
  created_at    TIMESTAMP NOT NULL,
  CONSTRAINT fk_items_order FOREIGN KEY (order_id) REFERENCES orders(id),
  CONSTRAINT fk_items_product FOREIGN KEY (product_id) REFERENCES products(id),
  CONSTRAINT ck_items_qty CHECK (quantity > 0),
  CONSTRAINT ck_items_unit CHECK (unit_cents >= 0)
);

CREATE TABLE payments (
  id            BIGINT PRIMARY KEY,
  order_id      BIGINT NOT NULL,
  provider      VARCHAR(32) NOT NULL,
  provider_txn  VARCHAR(128) NOT NULL,
  amount_cents  BIGINT NOT NULL,
  status        VARCHAR(32) NOT NULL,
  created_at    TIMESTAMP NOT NULL,
  CONSTRAINT fk_payments_order FOREIGN KEY (order_id) REFERENCES orders(id),
  CONSTRAINT uq_provider_txn UNIQUE (provider, provider_txn),
  CONSTRAINT ck_pay_amount CHECK (amount_cents > 0)
);
```

### 🧷 Index gợi ý theo query thường gặp
- 📌 “Danh sách đơn của 1 khách theo thời gian”:
```sql
CREATE INDEX ix_orders_customer_created ON orders(customer_id, created_at DESC);
```

- 📌 “Lấy items của 1 đơn”:
```sql
CREATE INDEX ix_items_order ON order_items(order_id);
```

- 📌 “Tìm product theo sku” (unique đã tạo index tùy DB):
```sql
CREATE INDEX ix_products_sku ON products(sku);
```

### 🔍 Query minh họa
- 📄 Lấy đơn + items:
```sql
SELECT o.id, o.status, o.total_cents, i.product_id, i.quantity, i.unit_cents
FROM orders o
JOIN order_items i ON i.order_id = o.id
WHERE o.id = ?;
```

- 📄 List đơn của khách (pagination kiểu keyset):
```sql
SELECT id, status, total_cents, created_at
FROM orders
WHERE customer_id = ?
  AND (created_at, id) < (?, ?)
ORDER BY created_at DESC, id DESC
LIMIT 50;
```

---

## ✅ 13) Checklist review database (rất thực tế)
- 🧩 Model có phản ánh đúng nghiệp vụ & trạng thái không?
- 🔑 PK/FK/Unique/Check đã đủ để chặn dữ liệu sai chưa?
- 🧼 Có chỗ nào denormalize? Có cơ chế giữ đồng bộ không?
- 🧷 Index đã bám theo query thật chưa? Có index thừa không?
- 📦 Dữ liệu lớn dần: có kế hoạch partition/archive không?
- 🔒 Transaction: có thao tác nào giữ lock lâu không?
- 🚚 Migration: có tránh breaking change/ downtime không?
- 🧯 Backup/restore: đã test restore chưa?

---

## 📝 Ghi chú của tôi
- 🎯 Use-case quan trọng nhất của hệ thống: …
- 🔍 Top 5 query quan trọng nhất: …
- 🧷 Index cần có tương ứng: …
- ⚠️ Rủi ro dữ liệu sai lớn nhất: …
