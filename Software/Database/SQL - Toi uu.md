# 🗄️ SQL — Tối ưu (Playbook)

> 🎯 Mục tiêu: viết SQL **nhanh hơn, ổn định hơn, rẻ hơn** (CPU/IO/Lock), và **dễ bảo trì**.

## 🧭 Mục lục
- 🧠 Nguyên lý nền tảng (cái gì làm query chậm?)
- 🔍 Cách debug hiệu năng (Execution Plan, stats, logs)
- 🧱 Thiết kế schema & dữ liệu
- 🧷 Indexing (rất quan trọng)
- ✍️ Viết lại câu SQL (query rewrite)
- 🔁 JOIN / Subquery / CTE / Window
- 📄 Pagination & tìm kiếm
- 🧮 Aggregation & reporting
- 🔒 Locking, transaction, deadlock
- 🧊 Caching & giảm tải DB
- 🧰 Batch, bulk, ETL
- 🧳 Partitioning, sharding, archiving
- ⚙️ Cấu hình & vận hành
- ⚠️ Anti-patterns (những thứ hay làm chậm)
- ✅ Checklist tối ưu theo tình huống
- 🧪 “Bài tập” tự luyện

---

## 🧠 1) Nguyên lý nền tảng
### ⏱️ Query chậm thường vì
- 💽 **IO**: đọc quá nhiều page/row (full scan) do thiếu/không dùng index
- 🧠 **CPU**: sort/hash/aggregate nặng (ORDER BY, GROUP BY), biểu thức phức tạp
- 🔒 **Lock/Contention**: transaction dài, update nhiều, thiếu index dẫn tới lock lớn
- 🌊 **Cardinality sai**: DB ước lượng số dòng sai → chọn kế hoạch tệ
- 🧪 **Parameter sniffing** (tùy DB): plan tối ưu cho tham số A nhưng dở cho B
- 📦 **Network**: kéo quá nhiều cột/row về app

### 🏁 Mục tiêu tối ưu
- 📉 Giảm **rows scanned** và **bytes read**
- 🎯 Tăng **selectivity** (lọc sớm)
- 🧷 Dùng index đúng chỗ
- 🔁 Tránh làm lại công việc (precompute, cache)
- 🔒 Giảm thời gian giữ lock

---

## 🔍 2) Debug hiệu năng (đi từ đo → hiểu → sửa)
### 🧭 Quy trình chuẩn
1) 📏 Xác định query chậm (top N theo latency/cpu/reads)
2) 🔍 Lấy **Execution Plan** (estimated + actual nếu có)
3) 🧾 Ghi nhận: rows scanned vs rows returned, key lookups, sort, hash, spill to disk
4) 🧪 Thử giải pháp nhỏ nhất: thêm/sửa index, rewrite predicate
5) ✅ Re-test (cùng dữ liệu, cùng tham số)

### 🔍 Execution Plan: nhìn gì?
- 🧷 Index seek vs scan
- 🧱 Join strategy (nested loop / hash / merge)
- 🧮 Sort / Hash aggregate
- 🧳 “Key lookup”/bookmark lookup (thiếu covering index)
- 🧊 Spill: sort/hash tràn ra disk (thiếu memory/plan tệ)

### 🧾 Cần thống kê (statistics)
- 📈 DB dựa vào statistics để chọn plan
- 🔁 Khi dữ liệu thay đổi mạnh mà stats cũ → plan có thể tệ

---

## 🧱 3) Thiết kế schema & dữ liệu
### 🧩 Chọn kiểu dữ liệu hợp lý
- 📦 Dữ liệu nhỏ hơn → index nhỏ hơn → cache hiệu quả hơn
- ✅ Dùng kiểu số/boolean/date đúng thay vì string “cho nhanh”

### 🧭 Normalization vs Denormalization
- 🧼 Normalize: giảm trùng lặp, dễ đúng
- ⚡ Denormalize (có kiểm soát): giảm JOIN nặng ở chỗ đọc nhiều

### 🧾 Ràng buộc & khóa
- 🔑 Primary key hợp lý (thường dùng surrogate key)
- ✅ Foreign key giúp đúng dữ liệu, nhưng cân nhắc overhead khi bulk

### 🗃️ Dữ liệu “hot” vs “cold”
- 🔥 Hot: truy cập thường xuyên → tối ưu index/cache
- 🧊 Cold: lịch sử → archive/partition

---

## 🧷 4) Indexing (vũ khí số 1)
### 🧠 Nguyên tắc
- 🧷 Index giúp **lọc (WHERE)**, **join**, **sort (ORDER BY)**, đôi khi **group**
- ⚠️ Index không miễn phí: tốn storage + làm chậm INSERT/UPDATE/DELETE

### 🎯 4.1 Chọn cột index theo truy vấn thật
- ✅ Ưu tiên cột có tính chọn lọc cao (high selectivity)
- ✅ Index theo các pattern truy vấn phổ biến

### 🧱 4.2 Composite index (multi-column)
- 🧭 Quy tắc thực dụng: đặt cột theo thứ tự dùng trong `WHERE` và tính chọn lọc
- 📌 Phù hợp cho query dạng:
  - `WHERE a = ? AND b = ?` hoặc `WHERE a = ? AND b BETWEEN ...`

### 📦 4.3 Covering index
- 🎁 Index “cover” khi query chỉ cần cột nằm trong index → tránh lookup về table
- ✅ Giảm IO mạnh cho truy vấn đọc nhiều

### 🔁 4.4 Index cho JOIN
- ✅ Index cột join ở bảng “bị join vào” (foreign key side)
- ✅ Với join nhiều, index đúng giúp DB chọn nested loop hiệu quả

### 🧭 4.5 Index cho ORDER BY
- ✅ Nếu `ORDER BY` cùng thứ tự với index, DB có thể tránh sort
- ⚠️ `ORDER BY` khác hướng/cột → vẫn sort

### 🧯 4.6 Index cho LIKE / tìm kiếm
- ✅ `LIKE 'abc%'` thường dùng index (prefix)
- ❌ `LIKE '%abc'` thường không dùng index (leading wildcard)
- 🔎 Full-text search / trigram index (tùy DB) cho tìm kiếm tự do

### 🧨 4.7 Index & NULL / boolean / low-cardinality
- ⚠️ Cột quá ít giá trị (boolean) thường index kém hiệu quả
- ✅ Nhưng có thể hữu ích nếu kết hợp composite index hoặc partial index

### 🧼 4.8 Dọn & bảo trì index
- 🧹 Xóa index không dùng (đo bằng usage stats)
- 🔁 Rebuild/reorg (tùy DB) nếu fragment cao

---

## ✍️ 5) Viết lại câu SQL (Query Rewrite)
### 🎯 5.1 Chỉ lấy thứ cần
- ✅ `SELECT col1, col2` thay vì `SELECT *`
- ✅ Giảm băng thông, giảm key lookup (khi dùng covering)

### 🧠 5.2 SARGable predicates (để DB dùng index)
- ✅ Viết điều kiện dạng “cột so với hằng số”
- ❌ Tránh bọc cột trong hàm ở WHERE:
  - ❌ `WHERE DATE(created_at) = '2026-02-24'`
  - ✅ `WHERE created_at >= '2026-02-24' AND created_at < '2026-02-25'`

### 🧮 5.3 Tránh implicit conversion
- ⚠️ So sánh khác kiểu (string vs int) có thể làm DB bỏ index
- ✅ Ép kiểu ở hằng số/parameter đúng kiểu cột

### 🧩 5.4 IN vs EXISTS vs JOIN
- ✅ `EXISTS` hay tốt khi chỉ cần “có tồn tại”
- ✅ `IN` ổn với danh sách nhỏ
- ⚠️ `JOIN` có thể nhân bản dòng (cần DISTINCT/GROUP) → tốn

### 🧻 5.5 OR condition
- ⚠️ `WHERE a = ? OR b = ?` có thể làm plan xấu
- ✅ Giải pháp:
  - tách `UNION ALL` (nếu phù hợp)
  - tạo index phù hợp

### 🧹 5.6 DISTINCT: dùng khi thật sự cần
- ⚠️ DISTINCT thường kéo theo sort/hash
- ✅ Thay bằng thiết kế join đúng hoặc EXISTS

---

## 🔁 6) JOIN / Subquery / CTE / Window
### 🤝 6.1 Chọn đúng loại JOIN
- ✅ INNER JOIN khi chắc chắn có match
- ✅ LEFT JOIN khi cần giữ dòng bên trái
- ⚠️ LEFT JOIN + WHERE điều kiện bên phải dễ vô tình biến thành INNER

### 🧠 6.2 Join order & filter sớm
- ✅ Lọc sớm bảng lớn trước khi join
- ✅ Đảm bảo có index ở cột join

### 🧩 6.3 CTE
- ✅ CTE giúp code rõ ràng
- ⚠️ Tùy DB, CTE có thể materialize (tốn) hoặc inline
- ✅ Nếu CTE bị materialize và lớn → cân nhắc temp table/index

### 🪟 6.4 Window functions
- ✅ Tuyệt vời cho rank, running total, dedupe
- ⚠️ Cần index hỗ trợ ORDER BY/PARTITION để giảm sort

---

## 📄 7) Pagination & tìm kiếm
### 🧾 7.1 Offset pagination (LIMIT/OFFSET)
- ⚠️ Offset lớn sẽ chậm vì DB vẫn phải bỏ qua nhiều dòng

### 🧭 7.2 Keyset pagination (seek method)
- ✅ Nhanh & ổn định: dựa trên “mốc” (last_seen_id/created_at)
- 📌 Ví dụ ý tưởng:
  - `WHERE (created_at, id) < (?, ?) ORDER BY created_at DESC, id DESC LIMIT 50`

### 🔎 7.3 Search
- ✅ Full-text index cho search tự do
- ✅ Tránh `%term%` trên bảng lớn nếu không có index phù hợp

---

## 🧮 8) Aggregation & reporting
### 🧠 8.1 Pre-aggregation
- ✅ Tạo bảng summary/materialized view cho báo cáo nặng
- ✅ Cập nhật theo batch hoặc theo event

### 🗓️ 8.2 Group by theo thời gian
- ✅ Index theo (date, dimension)
- ✅ Partition theo thời gian (nếu DB hỗ trợ) giúp query theo range nhanh

### 🧾 8.3 COUNT(*)
- ⚠️ Trên bảng rất lớn, COUNT(*) thường tốn
- ✅ Dùng approximate count (tùy DB) hoặc số liệu tổng hợp

---

## 🔒 9) Locking, transaction, deadlock
### 🧠 Quy tắc vàng
- ⏳ Transaction càng ngắn càng tốt
- 🎯 Chỉ lock đúng thứ cần, đúng lúc cần

### 🔥 Nguyên nhân deadlock hay gặp
- 🔁 Update nhiều bảng theo thứ tự khác nhau giữa 2 luồng
- 🧷 Thiếu index → update/scan lock rộng
- 🧾 Transaction dài + retry không kiểm soát

### ✅ Cách giảm deadlock
- 🧭 Thống nhất thứ tự update bảng/cột
- 🧷 Thêm index đúng để DB lock ít hơn
- 🔁 Retry có backoff cho lỗi deadlock (ở app)

---

## 🧊 10) Caching & giảm tải DB
### 🧠 Các lớp cache
- 🧳 CDN (static)
- 🚀 App cache (in-memory)
- 🧰 Distributed cache (Redis)

### ✅ Chiến lược thực dụng
- 📌 Cache “read heavy” + dữ liệu ổn định
- 🧹 Cache invalidation theo event (khi update)
- 🧯 Cẩn thận cache stampede (thêm TTL jitter/lock)

---

## 🧰 11) Batch, bulk, ETL
- 📦 Bulk insert/update thay vì từng row
- 🧾 Chia batch nhỏ để tránh lock lâu
- 🧱 Dùng staging table + merge (tùy DB)

---

## 🧳 12) Partitioning, sharding, archiving
### 🗂️ Partitioning
- ✅ Partition theo thời gian/tenant giúp query range nhanh + maintenance dễ
- ⚠️ Cần đảm bảo query có điều kiện partition key để “pruning”

### 🧊 Archiving
- ✅ Đưa dữ liệu cũ ra bảng khác / kho lạnh
- ✅ Giữ bảng “hot” nhỏ để index hiệu quả

### 🧩 Sharding
- 🧠 Khi 1 node không đủ: chia theo shard key (tenant_id/user_id)
- ⚠️ Đổi shard key rất khó → chọn kỹ

---

## ⚙️ 13) Cấu hình & vận hành
- 📊 Monitor: slow query log, p95/p99 latency, CPU, IO, buffer cache hit
- 🔁 Connection pool: size hợp lý, tránh mở quá nhiều
- 🧠 Memory: đủ để tránh spill
- 🧰 Autovacuum/cleanup (tùy DB) để tránh bloat

---

## ⚠️ 14) Anti-patterns (rất hay gặp)
- 🧨 `SELECT *` trên bảng rộng
- 🧨 `%term%` trên cột không có full-text/trigram
- 🧨 WHERE bọc cột bằng hàm (không SARGable)
- 🧨 OFFSET pagination cho page sâu
- 🧨 N+1 query (lặp query theo từng item)
- 🧨 JOIN không có index ở cột join
- 🧨 Dùng DISTINCT để “chữa cháy” join sai
- 🧨 Transaction dài + thao tác UI/HTTP bên trong transaction
- 🧨 Dữ liệu lịch sử phình to nhưng không archive

---

## ✅ 15) Checklist tối ưu theo tình huống
### 🐢 Query chậm bất ngờ
- 🔍 Lấy execution plan (actual nếu có)
- 📏 So rows returned vs scanned
- 🧷 Kiểm tra index đúng không
- 🧪 Kiểm tra stats có stale không

### 🔥 DB CPU cao
- 🧮 Tìm query sort/hash/aggregate nặng
- 🧷 Thêm covering index nếu lookup nhiều
- 🧊 Cache các query read-heavy

### 🔒 Deadlock / lock timeout
- ⏳ Rút ngắn transaction
- 🧷 Thêm index để lock hẹp
- 🧭 Đồng nhất thứ tự update

### 📈 Dữ liệu tăng, performance tụt dần
- 🧊 Archive/partition
- 🧹 Dọn index thừa, rebuild nếu fragment/bloat

---

## 🧪 16) Bài tập tự luyện
- 🧩 Bài 1: Viết lại điều kiện ngày để SARGable
- 🧷 Bài 2: Thiết kế index cho query có WHERE + ORDER BY + LIMIT
- 🔁 Bài 3: Thay OFFSET pagination bằng keyset pagination
- 📣 Bài 4: Phân tích plan và chỉ ra: scan, sort, lookup

---

## 📝 Ghi chú của tôi
- 🧠 3 query chậm nhất trong dự án: …
- 🧷 Index đang thiếu (theo WHERE/JOIN/ORDER): …
- ⚠️ Anti-pattern hay gặp nhất của team: …
