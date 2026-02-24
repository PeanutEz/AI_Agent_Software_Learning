# 🧠 OOP — 4 thành phần (4 tính chất) cốt lõi

> 🎯 OOP (Object-Oriented Programming) thường được tóm tắt bằng **4 tính chất**: **Encapsulation, Abstraction, Inheritance, Polymorphism**.

## 🧭 Mục lục
- 🧱 1) Encapsulation (Đóng gói)
- 🎭 2) Abstraction (Trừu tượng hóa)
- 🧬 3) Inheritance (Kế thừa)
- 🌀 4) Polymorphism (Đa hình)
- ✅ Checklist áp dụng nhanh

---

## 🧱 1) Encapsulation (Đóng gói)
### ✅ Là gì?
- Gom **dữ liệu + hành vi** vào một đối tượng và **che giấu chi tiết bên trong**.
- Chỉ lộ ra các thao tác “hợp lệ” qua method.

### 🎯 Mục tiêu
- 🛡️ Bảo vệ trạng thái object (state) khỏi bị sửa bừa
- ✅ Đảm bảo luật (invariants) được giữ đúng

### 🧪 Ví dụ
```ts
class BankAccount {
  private balance: number = 0;

  deposit(amount: number) {
    if (amount <= 0) throw new Error('amount must be positive');
    this.balance += amount;
  }

  withdraw(amount: number) {
    if (amount <= 0) throw new Error('amount must be positive');
    if (amount > this.balance) throw new Error('insufficient funds');
    this.balance -= amount;
  }

  getBalance() {
    return this.balance;
  }
}
```

📌 Ý chính: không cho ai set `balance` trực tiếp.

---

## 🎭 2) Abstraction (Trừu tượng hóa)
### ✅ Là gì?
- Chỉ tập trung vào **cái cần dùng**, ẩn bớt chi tiết triển khai.
- Thường thể hiện qua **interface/abstract class**.

### 🎯 Mục tiêu
- 🧩 Giảm phụ thuộc vào chi tiết (loose coupling)
- 🔁 Dễ thay thế implementation (đổi DB, đổi cổng thanh toán…)

### 🧪 Ví dụ
```ts
interface PaymentGateway {
  pay(amount: number): string;
}

class MomoGateway implements PaymentGateway {
  pay(amount: number) { return 'txn_momo_123'; }
}

class CheckoutService {
  constructor(private gateway: PaymentGateway) {}

  checkout(amount: number) {
    return this.gateway.pay(amount);
  }
}
```

📌 Service phụ thuộc vào “hợp đồng” `PaymentGateway`, không phụ thuộc vendor cụ thể.

---

## 🧬 3) Inheritance (Kế thừa)
### ✅ Là gì?
- Lớp con kế thừa thuộc tính/hành vi từ lớp cha để **tái sử dụng** và **chuyên biệt hóa**.

### 🎯 Mục tiêu
- ♻️ Reuse code chung
- 🎭 Hỗ trợ polymorphism qua kiểu cha

### 🧪 Ví dụ
```ts
class Animal {
  speak() { return '...'; }
}

class Dog extends Animal {
  speak() { return 'Woof'; }
}
```

⚠️ Lưu ý thực dụng
- Chỉ dùng khi quan hệ **is-a** thật sự đúng.
- Nếu là **has-a** (có/bao gồm) thì thường nên dùng composition.

---

## 🌀 4) Polymorphism (Đa hình)
### ✅ Là gì?
- Cùng một lời gọi method nhưng hành vi khác nhau tùy object cụ thể.
- Thường gặp ở **override** (runtime polymorphism) và **overload** (compile-time, tùy ngôn ngữ).

### 🎯 Mục tiêu
- 🧩 Viết code tổng quát (làm việc qua interface/base class)
- ➕ Thêm loại mới ít sửa code cũ (Open/Closed)

### 🧪 Ví dụ (runtime polymorphism)
```ts
abstract class Shape {
  abstract area(): number;
}

class Circle extends Shape {
  constructor(private r: number) { super(); }
  area() { return Math.PI * this.r * this.r; }
}

class Rectangle extends Shape {
  constructor(private w: number, private h: number) { super(); }
  area() { return this.w * this.h; }
}

function totalArea(shapes: Shape[]) {
  return shapes.reduce((sum, s) => sum + s.area(), 0);
}
```

📌 `totalArea` không cần biết `Circle` hay `Rectangle`.

---

## ✅ Checklist áp dụng nhanh
- 🧱 Encapsulation: có chỗ nào cho sửa state trực tiếp không?
- 🎭 Abstraction: chỗ nào cần interface để đổi implementation?
- 🧬 Inheritance: có dùng sai is-a không? cây kế thừa có quá sâu không?
- 🌀 Polymorphism: có thể thay if-else bằng polymorphism không?

---

## 📝 Ghi chú của tôi
- 🧩 Ví dụ Encapsulation trong dự án: …
- 🎭 Interface quan trọng nhất của hệ thống: …
- 🧬 Chỗ nào đang lạm dụng inheritance: …
- 🌀 Chỗ nào đang if-else nhiều có thể polymorphic: …
