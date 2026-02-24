# 🧩 Software Pattern (Design Pattern) — Học chi tiết

> 🎯 **Software pattern** là “mẫu giải pháp” đã được đúc kết cho những vấn đề thiết kế lặp đi lặp lại trong phần mềm. Pattern không phải là thư viện hay framework, mà là **cách tổ chức code/đối tượng/luồng xử lý** để hệ thống dễ mở rộng, dễ thay đổi, giảm phụ thuộc.

## 🧭 Mục lục
- 🔎 Pattern là gì? (hiểu đúng)
- 🧠 Vì sao cần pattern?
- 🧱 Phân loại pattern (GoF + architectural)
- ✅ Cách chọn pattern đúng
- ⚠️ Anti-pattern (dùng sai sẽ hại)
- 🧪 Ví dụ nhanh (có code minh họa)
- 📚 Lộ trình học gợi ý

---

## 🔎 1) Pattern là gì? (Hiểu đúng trước)
### ✅ Định nghĩa dễ hiểu
- 🧩 **Pattern** = một *cách giải quyết* (solution) cho một *vấn đề thiết kế* (problem) trong một *bối cảnh* (context) nhất định, kèm theo *đánh đổi* (trade-offs).

### ❌ Pattern không phải
- ❌ “Best practice cho mọi nơi”
- ❌ Một đoạn code copy-paste
- ❌ Framework/Library

### 🧠 3 yếu tố của pattern
- 📌 **Context**: khi nào vấn đề xuất hiện
- 🧷 **Forces/Constraints**: yêu cầu, ràng buộc, mâu thuẫn
- ✅ **Solution**: cấu trúc + trách nhiệm + cách phối hợp

---

## 🧠 2) Vì sao cần pattern?
### 🎯 Lợi ích
- 🧩 Tăng khả năng mở rộng (extensibility)
- 🔁 Giảm lặp code, giảm phụ thuộc chặt (tight coupling)
- 🧠 Dễ đọc: team có “ngôn ngữ chung” ("đoạn này dùng Strategy", "chỗ này Decorator")
- 🧪 Dễ test hơn: tách responsibilities rõ

### 💸 Chi phí
- 🧱 Nhiều abstraction hơn → code dài hơn
- 🧠 Cần kỷ luật và hiểu đúng để tránh over-engineering

---

## 🧱 3) Phân loại pattern

## 🧰 3.1 GoF Patterns (23 pattern kinh điển)
### 🏭 Creational (Tạo đối tượng)
- 🏭 Factory Method / Abstract Factory
- 🧱 Builder
- 🧬 Prototype
- 🧍 Singleton (ít khuyến khích trong app hiện đại, dễ gây state ẩn)

### 🧱 Structural (Cấu trúc)
- 🔌 Adapter
- 🎀 Decorator
- 🧩 Composite
- 🎭 Facade
- 🧠 Proxy
- 🪶 Flyweight
- 🧷 Bridge

### 🎮 Behavioral (Hành vi)
- 🧠 Strategy
- 👀 Observer
- 🧾 Command
- 🔁 Iterator
- 🧪 Template Method
- 🧭 State
- 🧑‍⚖️ Mediator
- 🧷 Chain of Responsibility
- 🧳 Memento
- 🧠 Interpreter (ít dùng)
- 🤝 Visitor (dùng khi cấu trúc ổn định, thêm operation thường xuyên)

---

## 🏗️ 3.2 Architectural / Enterprise Patterns (hay gặp trong dự án)
- 🧅 Layered Architecture (Presentation / Application / Domain / Infrastructure)
- 🔌 Hexagonal (Ports & Adapters) / Clean Architecture
- 🧱 MVC / MVP / MVVM (UI)
- 📦 Microservices (khi phù hợp)
- 📣 Event-Driven Architecture
- 🧾 CQRS (tách command/query)
- 🗄️ Repository, Unit of Work
- 🧩 Dependency Injection (DI)

---

## ✅ 4) Cách chọn pattern đúng (thực dụng)
### 🧭 Hỏi 5 câu trước khi dùng pattern
- 1) 🔁 Vấn đề này có **lặp lại** không?
- 2) 🧩 “Điểm thay đổi” (change axis) là gì? (ví dụ: thêm phương thức thanh toán, thêm loại vận chuyển…)
- 3) 🎯 Mục tiêu là: mở rộng dễ hơn, test dễ hơn, hay tách hạ tầng?
- 4) 🧱 Pattern có làm code khó hiểu hơn không?
- 5) 📏 Cái giá (complexity) có xứng đáng không?

### 📌 Quy tắc vàng
- ✅ **Đơn giản trước**, chỉ dùng pattern khi có “lý do thay đổi” rõ ràng.

---

## ⚠️ 5) Anti-pattern (dùng sai sẽ hại)
- 🧱 **Over-engineering**: chưa cần mở rộng đã dựng 10 interface
- 🧟 **God Object**: 1 class ôm hết logic
- 🧅 **Spaghetti dependencies**: phụ thuộc vòng, khó test
- 🧊 **Premature optimization**: tối ưu khi chưa đo

---

## 🧪 6) Ví dụ nhanh (dễ học)
Dưới đây là các ví dụ theo kiểu “khi nào dùng + lợi ích + code minh họa”. Code minh họa dùng cú pháp gần TypeScript/Java/C# (đọc dễ, không phụ thuộc ngôn ngữ).

---

## 🧠 6.1 Strategy — khi có nhiều cách làm cùng 1 việc
### 📌 Bài toán
- Bạn có nhiều cách tính phí ship: theo km, theo khu vực, theo đối tác.
- Muốn thêm cách mới mà không sửa code cũ nhiều.

### ✅ Giải pháp
- Tách thuật toán thành các strategy.

```ts
interface ShippingFeeStrategy {
  calc(orderTotal: number, distanceKm: number): number;
}

class DistanceStrategy implements ShippingFeeStrategy {
  calc(orderTotal: number, distanceKm: number) {
    return distanceKm * 5000;
  }
}

class FreeOverThresholdStrategy implements ShippingFeeStrategy {
  constructor(private threshold: number) {}
  calc(orderTotal: number, distanceKm: number) {
    return orderTotal >= this.threshold ? 0 : distanceKm * 5000;
  }
}

class CheckoutService {
  constructor(private shippingStrategy: ShippingFeeStrategy) {}

  checkout(orderTotal: number, distanceKm: number) {
    const shippingFee = this.shippingStrategy.calc(orderTotal, distanceKm);
    return { orderTotal, shippingFee, grandTotal: orderTotal + shippingFee };
  }
}
```

✅ Lợi ích: thêm chiến lược mới không đụng `CheckoutService`.

---

## 👀 6.2 Observer — khi cần “phát sự kiện” cho nhiều bên
### 📌 Bài toán
- Khi `OrderPaid`: gửi email, trừ kho, ghi log, đẩy analytics.

### ✅ Giải pháp
- Order phát event, các subscriber lắng nghe.

```ts
type EventHandler<T> = (event: T) => void;

class EventBus {
  private handlers = new Map<string, Function[]>();

  subscribe<T>(eventName: string, handler: EventHandler<T>) {
    const list = this.handlers.get(eventName) ?? [];
    list.push(handler);
    this.handlers.set(eventName, list);
  }

  publish<T>(eventName: string, event: T) {
    (this.handlers.get(eventName) ?? []).forEach(h => h(event));
  }
}

// Usage
const bus = new EventBus();

bus.subscribe('OrderPaid', (e: { orderId: string }) => {
  // send email
});
bus.subscribe('OrderPaid', (e: { orderId: string }) => {
  // deduct inventory
});

bus.publish('OrderPaid', { orderId: 'O-001' });
```

✅ Lợi ích: thêm tác vụ sau thanh toán mà không sửa code thanh toán.

---

## 🔌 6.3 Adapter — khi tích hợp hệ ngoài có interface “lệch”
### 📌 Bài toán
- App bạn muốn interface `PaymentGateway.pay(amount)`
- Nhưng cổng A lại có `charge(total, currency, metadata)`.

### ✅ Giải pháp
- Viết adapter để “chuẩn hóa”.

```ts
interface PaymentGateway {
  pay(amountVnd: number, orderId: string): string; // returns txnId
}

class VendorAClient {
  charge(total: number, currency: string, metadata: any): { id: string } {
    return { id: 'txn_123' };
  }
}

class VendorAAdapter implements PaymentGateway {
  constructor(private vendor: VendorAClient) {}

  pay(amountVnd: number, orderId: string) {
    const res = this.vendor.charge(amountVnd, 'VND', { orderId });
    return res.id;
  }
}
```

✅ Lợi ích: đổi vendor khác không ảnh hưởng phần còn lại.

---

## 🎀 6.4 Decorator — thêm tính năng mà không sửa class gốc
### 📌 Bài toán
- Bạn có `NotificationService.send()`
- Muốn thêm retry, logging, rate limit mà không sửa service gốc.

```ts
interface Notifier {
  send(to: string, msg: string): void;
}

class EmailNotifier implements Notifier {
  send(to: string, msg: string) {
    // send email
  }
}

class LoggingDecorator implements Notifier {
  constructor(private inner: Notifier) {}
  send(to: string, msg: string) {
    // log before
    this.inner.send(to, msg);
    // log after
  }
}

const notifier = new LoggingDecorator(new EmailNotifier());
notifier.send('a@b.com', 'Hello');
```

✅ Lợi ích: ghép nhiều decorator linh hoạt.

---

## 🏭 6.5 Factory Method — tạo object theo rule, tránh if-else rải rác
### 📌 Bài toán
- Tùy loại file mà dùng parser khác nhau.

```ts
interface Parser { parse(input: string): any; }

class JsonParser implements Parser { parse(s: string) { return JSON.parse(s); } }
class CsvParser implements Parser { parse(s: string) { /*...*/ return []; } }

class ParserFactory {
  static create(fileType: 'json' | 'csv'): Parser {
    switch (fileType) {
      case 'json': return new JsonParser();
      case 'csv': return new CsvParser();
    }
  }
}
```

✅ Lợi ích: gom logic tạo object vào 1 chỗ.

---

## 🗄️ 6.6 Repository (Enterprise pattern) — tách domain khỏi DB
### 📌 Bài toán
- Domain code không muốn phụ thuộc SQL/ORM.

```ts
class Order {
  constructor(public id: string, public status: 'New' | 'Paid') {}
}

interface OrderRepository {
  findById(id: string): Order | null;
  save(order: Order): void;
}

class PayOrderUseCase {
  constructor(private repo: OrderRepository) {}

  execute(orderId: string) {
    const order = this.repo.findById(orderId);
    if (!order) throw new Error('Not found');

    order.status = 'Paid';
    this.repo.save(order);
  }
}
```

✅ Lợi ích: test use-case dễ (mock repo), đổi DB/ORM ít ảnh hưởng.

---

## 📚 7) Lộ trình học gợi ý
- 🧠 Bước 1: Nắm 6 pattern hay dùng nhất: Strategy, Observer, Adapter, Decorator, Factory, Facade
- 🧪 Bước 2: Làm mini-project (order/payment/notification) và áp pattern đúng chỗ
- 🏗️ Bước 3: Học enterprise patterns: Repository, Unit of Work, DI, CQRS (khi cần)
- 🧭 Bước 4: Học architectural patterns: Layered, Hexagonal/Clean

---

## 📝 Ghi chú của tôi
- 🎯 Dự án của tôi hay thay đổi nhất ở phần: …
- 🧩 Pattern mình muốn áp dụng đầu tiên: … (vì …)
- ⚠️ Over-engineering mình hay mắc: …
