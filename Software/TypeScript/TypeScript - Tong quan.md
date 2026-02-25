# 🟦 TypeScript — Học chi tiết (kèm ví dụ)

> 🎯 TypeScript (TS) là **JavaScript + kiểu dữ liệu (type system)**. TS giúp bắt lỗi sớm khi compile, code dễ đọc/dễ refactor, đặc biệt tốt cho dự án lớn và làm việc nhóm.

## 🧭 Mục lục
- 🔎 TypeScript là gì? Dùng để làm gì?
- ✨ Các tính năng cốt lõi (nhanh, có ví dụ)
- ➕ Các tính năng khác (rất hay dùng)
- ⚙️ Cài đặt & chạy nhanh (tsc/ts-node)
- 🧩 Kiểu dữ liệu cơ bản
- 🧠 Type inference (suy luận kiểu)
- 🧱 Interface vs Type alias
- 🧷 Union/Intersection + Narrowing (thu hẹp kiểu)
- 🧬 Generics (tổng quát hóa)
- 🧰 Utility types hay dùng
- 🧱 Function typing (overload, optional, default)
- 🧩 Class/Access modifiers/Abstract
- 📦 Modules (import/export) + path alias
- ⏳ Async/Promise typing
- ⚠️ Những bẫy hay gặp
- ✅ Best practices (thực dụng)
- 🧪 Mini ví dụ: API client + domain types

---

## ✨ Các tính năng cốt lõi (nhanh, có ví dụ)
Phần này tóm tắt đúng 5 “tính năng” bạn hỏi. Bạn có thể đọc mục này trước để nắm khung, rồi đọc các mục chi tiết bên dưới.

---

### 1) 🏷️ Type Annotations
### ✅ Là gì?
- Type annotations là việc **gắn kiểu** cho biến/hàm/params/return để TS kiểm tra.

### 🧪 Ví dụ
```ts
let age: number = 18;

function add(a: number, b: number): number {
  return a + b;
}

const names: string[] = ['An', 'Binh'];
```

📌 Tip: ưu tiên để TS tự suy luận kiểu, chỉ annotate khi cần rõ ràng hoặc public API.

---

### 2) 🧱 Interface, Type
### ✅ Dùng khi nào?
- `interface`: mô tả shape object, hay dùng cho “public contract”, dễ `extends`.
- `type`: linh hoạt cho union/intersection/mapped types.

### 🧪 Ví dụ
```ts
interface User {
  id: string;
  name: string;
}

type UserId = string;
type Admin = User & { role: 'admin' };

const a: Admin = { id: '1', name: 'A', role: 'admin' };
```

📌 Thực tế: team thường dùng cả 2; quan trọng là **nhất quán**.

---

### 3) 🧬 Generics
### ✅ Là gì?
- Generics cho phép viết hàm/class “tổng quát” nhưng vẫn giữ kiểu chính xác theo input.

### 🧪 Ví dụ
```ts
function wrap<T>(value: T) {
  return { value };
}

const a = wrap(123);     // { value: number }
const b = wrap('hello'); // { value: string }
```

### 🧩 Generic constraints (rất hay dùng)
```ts
function getProp<T extends object, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

getProp({ id: 'u1', age: 20 }, 'age'); // number
```

---

### 4) 🔀 Union Types
### ✅ Là gì?
- Union types cho phép một giá trị có thể là **một trong nhiều kiểu**.
- Khi kết hợp với “discriminated union” (có field phân biệt), TS sẽ tự narrow rất mạnh.

### 🧪 Ví dụ
```ts
type Status = 'NEW' | 'PAID' | 'CANCELLED';

type Result =
  | { ok: true; data: string }
  | { ok: false; error: string };

function handle(r: Result) {
  if (r.ok) {
    console.log(r.data);
  } else {
    console.error(r.error);
  }
}
```

---

### 5) 🧾 Enum
### ✅ Enum là gì?
- `enum` tạo một tập giá trị có tên (named constants).
- TS có **numeric enum** và **string enum**.

### 🧪 Ví dụ
```ts
enum OrderStatus {
  New = 'NEW',
  Paid = 'PAID',
  Cancelled = 'CANCELLED',
}

function setStatus(s: OrderStatus) {
  return s;
}

setStatus(OrderStatus.Paid);
```

### ⚠️ Lưu ý thực dụng
- `enum` là “tính năng runtime” (có sinh JS) → đôi khi team thích dùng union literal thay thế:
```ts
type OrderStatus2 = 'NEW' | 'PAID' | 'CANCELLED';
```
- Nếu cần enum nhưng muốn nhẹ hơn, bạn có thể dùng `as const`:
```ts
const OrderStatus3 = { New: 'NEW', Paid: 'PAID', Cancelled: 'CANCELLED' } as const;
type OrderStatus3 = (typeof OrderStatus3)[keyof typeof OrderStatus3];
```

---

## ➕ Các tính năng khác (rất hay dùng)
Đây là những tính năng TS bạn sẽ gặp thường xuyên trong dự án thực tế (ngoài 5 mục ở trên).

---

### 🧠 Type inference (tự suy luận kiểu)
```ts
const n = 1; // number
const arr = ['a', 'b']; // string[]
```

---

### 🧷 Intersection types (`&`)
```ts
type HasId = { id: string };
type HasCreatedAt = { createdAt: Date };
type Entity = HasId & HasCreatedAt;

const e: Entity = { id: '1', createdAt: new Date() };
```

---

### 🧊 Literal types + `as const`
Giúp TS giữ nguyên literal (không bị “widen” thành string/number).
```ts
const status = 'PAID'; // type: "PAID" (thường là string nếu không const context)

const OrderStatus = {
  New: 'NEW',
  Paid: 'PAID',
  Cancelled: 'CANCELLED',
} as const;

type OrderStatus = (typeof OrderStatus)[keyof typeof OrderStatus];
```

---

### ✅ `satisfies` (kiểm tra shape nhưng không làm mất kiểu)
Rất hữu ích khi muốn object “đúng cấu trúc” nhưng vẫn giữ literal types.
```ts
type Routes = Record<string, { method: 'GET' | 'POST'; path: `/${string}` }>;

const routes = {
  health: { method: 'GET', path: '/health' },
  login: { method: 'POST', path: '/login' },
} satisfies Routes;
```

---

### 🔀 Declaration merging (merge type)
TS có cơ chế **declaration merging**: nhiều “khai báo” cùng tên sẽ được TS **gộp** lại.

✅ Thường merge được
- `interface` (phổ biến nhất)
- `namespace` (và có thể merge namespace với function/class cùng tên theo một số kiểu)
- `enum` (có thể “merge” qua namespace để thêm static members)

❌ Không merge được
- `type` alias **không** merge. Khai báo lại sẽ bị lỗi “Duplicate identifier”.

#### 🧩 Ví dụ: `interface` merge
```ts
interface User {
  id: string;
}

interface User {
  name: string;
}

// Sau merge: User có cả id + name
const u: User = { id: '1', name: 'A' };
```

#### 🧱 Type alias không merge → dùng intersection để “ghép”
```ts
type UserA = { id: string };
type UserB = { name: string };
type User = UserA & UserB;

const u: User = { id: '1', name: 'A' };
```

#### 🧩 Module augmentation (mở rộng type của thư viện)
Hay dùng để “bổ sung type” cho thư viện (ví dụ thêm field vào Request của Express, thêm method vào một module).
```ts
// ví dụ minh họa (tên module chỉ là ví dụ)
declare module 'my-lib' {
  interface Options {
    timeoutMs?: number;
  }
}
```

⚠️ Lưu ý thực dụng
- Merge mạnh nhưng cũng dễ gây “type ẩn” (khó lần theo), nên chỉ dùng khi có lý do rõ (đặc biệt là module augmentation).

---

### 🔍 Type narrowing (thu hẹp kiểu) bằng `typeof` / `in`
```ts
function f(v: string | number) {
  if (typeof v === 'string') {
    return v.toUpperCase();
  }
  return v + 1;
}

type A = { kind: 'a'; a: number };
type B = { kind: 'b'; b: string };
type AB = A | B;

function g(x: AB) {
  if ('a' in x) return x.a;
  return x.b;
}
```

---

### 🧪 Type guards (tự định nghĩa)
```ts
function isNonEmptyString(v: unknown): v is string {
  return typeof v === 'string' && v.trim().length > 0;
}
```

---

### 🧭 `keyof`, `typeof`, index access types
```ts
const cfg = { retry: 3, timeoutMs: 5000 };
type Config = typeof cfg;
type ConfigKey = keyof Config; // 'retry' | 'timeoutMs'
type TimeoutType = Config['timeoutMs']; // number
```

---

### 🧩 Mapped types (tự tạo type từ type khác)
```ts
type Flags<T> = {
  [K in keyof T]: boolean;
};

type User = { id: string; name: string };
type UserFlags = Flags<User>; // { id: boolean; name: boolean }
```

---

### 🧠 Conditional types (type “nếu…thì…”) — nâng cao
```ts
type ElementType<T> = T extends (infer U)[] ? U : T;

type A1 = ElementType<string[]>; // string
type A2 = ElementType<number>;   // number
```

---

### 🧵 Template literal types — nâng cao
```ts
type HttpMethod = 'GET' | 'POST';
type Route = `/${string}`;
type Endpoint = `${HttpMethod} ${Route}`;

const e: Endpoint = 'GET /users';
```

---

### 🧯 `never` để kiểm tra exhaustive (bắt thiếu case)
```ts
type Status = 'NEW' | 'PAID' | 'CANCELLED';

function assertNever(x: never): never {
  throw new Error(`Unhandled: ${x}`);
}

function render(s: Status) {
  switch (s) {
    case 'NEW': return 'New';
    case 'PAID': return 'Paid';
    case 'CANCELLED': return 'Cancelled';
    default: return assertNever(s);
  }
}
```

---

## 🔎 1) TypeScript là gì? Dùng để làm gì?
### ✅ TS giải quyết gì?
- 🧯 Bắt lỗi *trước khi chạy*: sai kiểu, gọi hàm thiếu tham số, null/undefined…
- 🧠 IDE thông minh: auto-complete, jump to definition, refactor an toàn
- 🧩 Thiết kế API rõ ràng hơn: bạn “định nghĩa hợp đồng” bằng type

### ❗ TS không làm được
- TS không tự biến code chạy nhanh hơn.
- TS kiểm tra kiểu ở compile-time; runtime vẫn là JS (nên vẫn cần validate dữ liệu input).

---

## ⚙️ 2) Cài đặt & chạy nhanh
### 🧰 Tool phổ biến
- `typescript` (tsc): compiler
- `ts-node`: chạy TS trực tiếp (dev)
- Bundler: Vite/tsup/esbuild (tùy mục tiêu)

### 🧾 tsconfig (tư duy)
- TS hoạt động tốt nhất khi bật strict:
  - ✅ `"strict": true`
  - ✅ `"noUncheckedIndexedAccess": true` (tùy dự án)
  - ✅ `"exactOptionalPropertyTypes": true` (tùy dự án)

---

## 🧩 3) Kiểu dữ liệu cơ bản
```ts
let n: number = 10;
let s: string = 'hello';
let ok: boolean = true;
let ids: number[] = [1, 2, 3];
let pair: [string, number] = ['a', 1];

// object typing
let user: { id: string; name: string } = { id: 'u1', name: 'An' };

// any (né type checking) — dùng hạn chế
let x: any = 123;

// unknown (an toàn hơn any) — phải kiểm tra trước khi dùng
let u: unknown = JSON.parse('{"a":1}');
```

📌 `unknown` > `any` vì bắt bạn kiểm tra kiểu.

---

## 🧠 4) Type inference (suy luận kiểu)
TS thường tự đoán kiểu:
```ts
const count = 1; // inferred: number
const name = 'Minh'; // inferred: string

const tags = ['a', 'b']; // inferred: string[]
```

🎯 Tip: ưu tiên inference để code gọn, chỉ annotate khi cần.

---

## 🧱 5) Interface vs Type alias
### ✅ Giống nhau
- Cả hai đều mô tả shape của object.

### 🧠 Khác nhau (thực dụng)
- `interface` dễ mở rộng bằng `extends` và declaration merging.
- `type` mạnh hơn khi cần union/intersection, mapped type.

```ts
interface User {
  id: string;
  name: string;
}

type Admin = User & { role: 'admin' };

const a: Admin = { id: '1', name: 'A', role: 'admin' };
```

---

## 🧷 6) Union/Intersection + Narrowing
### 6.1 🔀 Union types
```ts
type PaymentStatus = 'new' | 'paid' | 'failed';

function setStatus(s: PaymentStatus) {
  // ...
}
```

### 6.2 🔍 Narrowing (thu hẹp kiểu)
```ts
type ApiResult = { ok: true; data: string } | { ok: false; error: string };

function handle(r: ApiResult) {
  if (r.ok) {
    // r is { ok: true; data: string }
    console.log(r.data);
  } else {
    // r is { ok: false; error: string }
    console.error(r.error);
  }
}
```

### 6.3 🧪 Type guards
```ts
function isString(v: unknown): v is string {
  return typeof v === 'string';
}

function print(v: unknown) {
  if (isString(v)) {
    console.log(v.toUpperCase());
  }
}
```

---

## 🧬 7) Generics (tổng quát hóa)
Generics giúp bạn viết code tái sử dụng mà vẫn “giữ kiểu”.

```ts
function first<T>(arr: T[]): T | undefined {
  return arr[0];
}

const n = first([1, 2, 3]);      // n: number | undefined
const s = first(['a', 'b']);     // s: string | undefined
```

### 🧩 Generic constraints
```ts
function pluck<T extends object, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const u = { id: 'u1', age: 20 };
const id = pluck(u, 'id');   // string
const age = pluck(u, 'age'); // number
```

---

## 🧰 8) Utility types hay dùng
### 🔧 `Partial`, `Required`, `Readonly`
```ts
type User = { id: string; name: string; email?: string };

type UserPatch = Partial<User>;       // mọi field optional
type UserFull = Required<User>;       // mọi field required
type UserRO = Readonly<User>;         // readonly
```

### 🧰 `Pick`, `Omit`
```ts
type UserDTO = Pick<User, 'id' | 'name'>;
type UserPrivate = Omit<User, 'email'>;
```

### 🧠 `Record`
```ts
type Role = 'admin' | 'user';
const perms: Record<Role, string[]> = {
  admin: ['*'],
  user: ['read'],
};
```

### 🧲 `ReturnType`, `Parameters`
```ts
function makeUser(id: string) { return { id, createdAt: new Date() }; }

type MakeUserReturn = ReturnType<typeof makeUser>; // { id: string; createdAt: Date }
```

---

## 🧱 9) Function typing
### 9.1 Optional & default
```ts
function greet(name: string, title?: string) {
  return title ? `${title} ${name}` : name;
}

function sum(a: number, b: number = 0) {
  return a + b;
}
```

### 9.2 Function overload (khi cần API “đổi kiểu theo input”)
```ts
function toArray(v: string): string[];
function toArray(v: number): number[];
function toArray(v: string | number) {
  return [v];
}

const a = toArray('x'); // string[]
const b = toArray(1);   // number[]
```

---

## 🧩 10) Class/Access modifiers/Abstract
```ts
abstract class PaymentGateway {
  abstract pay(amount: number): Promise<string>;
}

class MomoGateway extends PaymentGateway {
  async pay(amount: number) {
    return `txn_${amount}`;
  }
}
```

📌 TS có `public` (default), `private`, `protected`, `readonly`.

---

## 📦 11) Modules (import/export)
```ts
// user.ts
export type User = { id: string; name: string };

// app.ts
import type { User } from './user';
```

### 🧭 Path alias (gợi ý)
- Dùng alias như `@/domain/...` để import gọn (cần config tsconfig + bundler).

---

## ⏳ 12) Async/Promise typing
```ts
type User = { id: string; name: string };

async function fetchUser(id: string): Promise<User> {
  const res = await fetch(`/api/users/${id}`);
  if (!res.ok) throw new Error('request failed');
  return (await res.json()) as User; // ⚠️ cast: cần validate nếu dữ liệu không tin cậy
}
```

📌 Nếu dữ liệu từ API/DB có thể “bẩn”, nên dùng schema validation (zod/yup) để an toàn runtime.

---

## ⚠️ 13) Những bẫy hay gặp
- 🧨 Lạm dụng `any` → mất lợi ích TS
- 🧨 `as SomeType` (type assertion) quá nhiều → che lỗi
- 🧨 Không bật strict → TS yếu đi rất nhiều
- 🧨 Confuse `null`/`undefined` (bật `strictNullChecks` để TS bắt)
- 🧨 Tưởng TS “validate runtime” → không, TS chỉ compile-time

---

## ✅ 14) Best practices (thực dụng)
- ✅ Bật `strict` ngay từ đầu
- ✅ Ưu tiên `unknown` thay `any`
- ✅ Dùng union + discriminated union để model state
- ✅ Dùng `type` cho union/mapped type; dùng `interface` cho public API object (tùy team)
- ✅ Giữ type gần domain (ví dụ: `MoneyCents`, `OrderId`)

---

## 🧪 15) Mini ví dụ: Domain types + service
### 🧩 Model domain rõ ràng
```ts
type OrderStatus = 'NEW' | 'PAID' | 'CANCELLED';

type Order = {
  id: string;
  status: OrderStatus;
  totalCents: number;
};

type PayResult =
  | { ok: true; txnId: string }
  | { ok: false; reason: 'INSUFFICIENT_FUNDS' | 'GATEWAY_ERROR' };

async function payOrder(order: Order): Promise<PayResult> {
  if (order.status !== 'NEW') {
    return { ok: false, reason: 'GATEWAY_ERROR' };
  }

  // giả lập gọi cổng thanh toán
  return { ok: true, txnId: 'txn_001' };
}

async function handlePay(order: Order) {
  const r = await payOrder(order);

  if (r.ok) {
    console.log('Paid, txn=', r.txnId);
  } else {
    console.log('Pay failed:', r.reason);
  }
}
```

🎯 Điểm đáng học:
- Dùng union có `ok` để TS tự narrow.
- Trạng thái domain được model bằng literal union.

---

## 📝 Ghi chú của tôi
- 🎯 Mục tiêu học TS của tôi (frontend/backend/fullstack): …
- 🧩 Phần mình hay sai nhất (generics/narrowing/tsconfig): …
- 🧪 Mini project muốn làm bằng TS: …
