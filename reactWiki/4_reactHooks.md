# React Hooks Cheatsheet: useState & useEffect 🎣

Hooks là các hàm đặc biệt bắt đầu bằng từ khóa `use`. Chúng giúp Functional Component "móc" (hook) vào các tính năng cốt lõi của React.

---

## 1. `useState` - Quản lý Bộ nhớ (State)
Giúp component ghi nhớ thông tin (biến số) giữa các lần render.

### Cú pháp
```javascript
const [name, setName] = useState("Giá trị mặc định");
// name: Biến chứa giá trị hiện tại
// setName: Hàm để cập nhật giá trị (gây render lại)
```

### 3 Lưu ý sống còn với `useState`:
1.  **Không thay đổi trực tiếp:** `count = 5` (Sai) $\rightarrow$ `setCount(5)` (Đúng).
2.  **Cập nhật dựa trên giá trị cũ:** Nếu giá trị mới phụ thuộc vào giá trị cũ (ví dụ: tăng đếm), hãy dùng callback.
    ```javascript
    // ❌ Dễ lỗi nếu click nhanh nhiều lần
    setCount(count + 1);
    
    // ✅ An toàn tuyệt đối (Lấy state mới nhất)
    setCount((prevCount) => prevCount + 1);
    ```
3.  **Với Object/Array:** Phải copy lại state cũ trước khi sửa (Dùng Spread Operator `...`).
    ```javascript
    const [user, setUser] = useState({ name: "Nam", age: 20 });
    
    // Muốn sửa age, phải giữ lại name
    setUser({ ...user, age: 21 });
    ```

---

## 2. `useEffect` - Xử lý Tác vụ phụ (Side Effects)
Dùng để xử lý những việc nằm **ngoài luồng render** của React như:
* Gọi API (Fetch Data).
* Hẹn giờ (`setTimeout`, `setInterval`).
* Tương tác trực tiếp với DOM (`document.title`).

### Tư duy "Dependency Array" `[]` (Mảng phụ thuộc)
`useEffect` nhận vào 2 tham số: `useEffect(callback, [deps])`. Cái mảng thứ 2 quyết định **KHI NÀO** effect chạy.

| Mảng Dependency | Ý nghĩa (Khi nào chạy?) | Tương đương Lifecycle cũ |
| :--- | :--- | :--- |
| **Không truyền** | Chạy **MỖI LẦN** component render. (Ít dùng, dễ gây loop). | `componentDidUpdate` (all) |
| **Mảng rỗng `[]`** | Chạy **DUY NHẤT 1 LẦN** sau khi component hiện lên. (Dùng gọi API lần đầu). | `componentDidMount` |
| **Có biến `[count]`** | Chạy lần đầu + Chạy lại mỗi khi biến `count` thay đổi. | `componentDidUpdate` (specific) |

### Ví dụ 1: Gọi API (Chạy 1 lần)
```jsx
useEffect(() => {
  console.log("Chỉ chạy 1 lần khi mở trang web");
  
  // Giả lập gọi API
  fetchData();
}, []); // <--- Quan trọng: Mảng rỗng
```

### Ví dụ 2: Theo dõi biến đổi (Chạy khi biến thay đổi)
```jsx
useEffect(() => {
  console.log("Chạy mỗi khi user bấm nút thay đổi count");
  document.title = `Bạn đã bấm ${count} lần`;
}, [count]); // <--- Chạy lại khi 'count' đổi
```

### Ví dụ 3: Cleanup Function (Dọn dẹp)
Cực quan trọng khi dùng `setInterval` hoặc `addEventListener` để tránh rò rỉ bộ nhớ (Memory Leak).

```jsx
useEffect(() => {
  const timer = setInterval(() => {
    console.log("Đang chạy...");
  }, 1000);

  // Hàm return này sẽ chạy khi Component bị hủy (Unmount)
  return () => {
    clearInterval(timer); // Dọn dẹp timer cũ
    console.log("Component đã bị xóa, dừng timer!");
  };
}, []);
```

---

## 3. Luật bất di bất dịch của Hooks (Rules of Hooks)
Nếu phạm luật, React sẽ báo lỗi màn hình đỏ ngay lập tức.

1.  **Chỉ gọi Hooks ở trên cùng (Top Level):**
    * ❌ Không gọi trong vòng lặp `for`, `if`, `else`, hay function lồng nhau.
    * ✅ Phải gọi ngay đầu thân hàm Component.
2.  **Chỉ gọi Hooks từ React Functions:**
    * Chỉ dùng trong Functional Component hoặc Custom Hooks.

---

## 🎯 Bài tập thực chiến: Đồng hồ đếm ngược & Gọi API
Ví dụ này kết hợp cả `useState` (lưu thời gian) và `useEffect` (tạo bộ đếm).

```jsx
import { useState, useEffect } from "react";

const CountdownTimer = () => {
  const [timeLeft, setTimeLeft] = useState(10); // Bắt đầu từ 10s

  useEffect(() => {
    // 1. Logic chạy (Effect)
    if (timeLeft === 0) return;

    const timerId = setInterval(() => {
      setTimeLeft((prev) => prev - 1);
    }, 1000);

    // 2. Logic dọn dẹp (Cleanup)
    return () => clearInterval(timerId);
    
  }, [timeLeft]); // Chạy lại mỗi khi timeLeft thay đổi

  return (
    <div>
      <h1>Đếm ngược: {timeLeft}s</h1>
      {timeLeft === 0 && <p style={{ color: 'red' }}>Hết giờ! 💥</p>}
    </div>
  );
};

export default CountdownTimer;
```