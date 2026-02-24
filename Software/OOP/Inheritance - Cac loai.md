# 🧬 Inheritance (Kế thừa) — Các loại & ví dụ

> 🎯 **Inheritance** là cơ chế OOP cho phép lớp con (subclass) **kế thừa** thuộc tính/hành vi của lớp cha (superclass) để tái sử dụng và mở rộng.

## 🧭 Mục lục
- 🧠 Kế thừa dùng để làm gì?
- 🧩 Các loại inheritance phổ biến
- 🔌 Interface inheritance (implements)
- ⚠️ Khi nào nên dùng kế thừa vs composition
- ✅ Checklist nhanh

---

## 🧠 1) Kế thừa dùng để làm gì?
- ♻️ **Reuse**: tái sử dụng code chung
- 🧱 **Polymorphism**: dùng kiểu cha để xử lý nhiều kiểu con
- 🧩 **Specialization**: lớp con là phiên bản “chuyên biệt” của lớp cha

📌 Quy tắc tư duy: subclass nên thỏa **is-a**
- ✅ `Dog` is-a `Animal`
- ❌ `Car` is-a `Engine` (đây là **has-a** → composition)

---

## 🧩 2) Các loại inheritance

## 2.1 🧍 Single Inheritance (kế thừa đơn)
**Định nghĩa**: 1 lớp con kế thừa từ **1 lớp cha**.

**Ví dụ (Java/C#/TypeScript style)**
```ts
class Animal {
  speak() { return '...'; }
}

class Dog extends Animal {
  speak() { return 'Woof'; }
}
```

✅ Dễ hiểu, phổ biến nhất.

---

## 2.2 🪜 Multilevel Inheritance (kế thừa nhiều tầng)
**Định nghĩa**: A → B → C (C kế thừa B, B kế thừa A).

```ts
class Vehicle {
  move() { /*...*/ }
}

class Car extends Vehicle {
  openTrunk() { /*...*/ }
}

class ElectricCar extends Car {
  charge() { /*...*/ }
}
```

⚠️ Cẩn thận: chuỗi kế thừa dài dễ khó kiểm soát (phụ thuộc ngầm).

---

## 2.3 🌳 Hierarchical Inheritance (kế thừa phân cấp)
**Định nghĩa**: 1 lớp cha có **nhiều lớp con**.

```ts
class Shape {
  area(): number { throw new Error('not implemented'); }
}

class Circle extends Shape {
  constructor(private r: number) { super(); }
  area() { return Math.PI * this.r * this.r; }
}

class Rectangle extends Shape {
  constructor(private w: number, private h: number) { super(); }
  area() { return this.w * this.h; }
}
```

✅ Rất hay dùng cho polymorphism (xử lý danh sách `Shape`).

---

## 2.4 🧩 Multiple Inheritance (đa kế thừa)
**Định nghĩa**: 1 lớp con kế thừa từ **nhiều lớp cha**.

- ✅ Hỗ trợ tốt trong **C++**
- ⚠️ **Java/C# không cho** đa kế thừa class (để tránh phức tạp)
- ✅ Thường thay bằng **interface** hoặc **composition**

**Ví dụ (C++)**
```cpp
class CanFly { public: void fly(); };
class CanSwim { public: void swim(); };

class Duck : public CanFly, public CanSwim {
  // Duck có cả fly() và swim()
};
```

⚠️ Bẫy nổi tiếng: **Diamond Problem**
- A là base, B và C kế thừa A, D kế thừa cả B và C → D có “2 bản A” nếu không xử lý.

---

## 2.5 🧬 Hybrid Inheritance (kế thừa lai)
**Định nghĩa**: kết hợp nhiều kiểu trên (ví dụ hierarchical + multiple + multilevel).

- ✅ Có trong C++
- ⚠️ Rất dễ phức tạp → ưu tiên thiết kế đơn giản, dùng interface/composition thay thế.

---

## 🔌 3) Interface inheritance (implements)
**Ý nghĩa**: lớp “kế thừa” **hợp đồng hành vi** thay vì code/field.

✅ Cách phổ biến trong Java/C#/TypeScript:
```ts
interface PaymentGateway {
  pay(amount: number): string;
}

class MomoGateway implements PaymentGateway {
  pay(amount: number) { return 'txn_momo_123'; }
}

class ZaloPayGateway implements PaymentGateway {
  pay(amount: number) { return 'txn_zlp_456'; }
}
```

🎯 Lợi ích:
- 🧩 Dễ thay thế implementation
- 🧪 Dễ test (mock interface)
- 🧱 Tránh “dính” vào base class lớn

📌 Lưu ý: một lớp có thể implements **nhiều interface**.

---

## ⚠️ 4) Khi nào nên dùng inheritance vs composition?

## ✅ Nên dùng inheritance khi
- ✔️ Quan hệ **is-a** thật sự đúng
- ✔️ Lớp cha ổn định, ít thay đổi
- ✔️ Muốn dùng polymorphism rõ ràng

## ✅ Nên dùng composition (has-a) khi
- 🧩 Bạn muốn “lắp ghép” hành vi linh hoạt (Strategy/Decorator)
- 🧯 Tránh base class phình to
- 🔁 Hành vi thay đổi theo runtime

**Ví dụ composition**
```ts
interface Engine { start(): void; }
class ElectricEngine implements Engine { start() {/*...*/} }

class Car {
  constructor(private engine: Engine) {}
  start() { this.engine.start(); }
}
```

---

## ✅ 5) Checklist nhanh
- 🔎 Subclass có thật sự **is-a** parent không?
- 🧱 Base class có nhỏ gọn, tập trung không? (tránh “God base class”)
- 🧪 Có test cho hành vi polymorphism không?
- 🧭 Cây kế thừa có quá sâu không? (thường > 3 tầng là nên xem lại)
- 🧩 Nếu cần nhiều “khả năng”, ưu tiên **interface + composition**

---

## 📝 Ghi chú của tôi
- 🎯 Trong dự án của tôi, chỗ nào đang dùng inheritance: …
- ⚠️ Có class cha nào quá to/khó sửa: …
- 🧩 Có chỗ nào nên chuyển sang composition: …
