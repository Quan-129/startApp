# Global State Cheatsheet: Context API vs Zustand 🗄️

Giúp quản lý dữ liệu toàn cục (Global State) mà không cần truyền Props qua nhiều cấp.

---

## 1. Vấn đề: "Prop Drilling" (Khoan lỗ)
Trước khi dùng Global State, hãy nhìn nỗi đau này:

```text
App (Có dữ liệu User)
 └─ Layout
     └─ Header
         └─ UserMenu
             └─ Avatar (Cần dữ liệu User) -> Phải truyền qua 4 tầng! 😫
```

Giải pháp: Đưa `User` vào một "Kho chung". `Avatar` kết nối trực tiếp vào kho đó.

---

## 2. Context API (Hàng chính chủ React)
Có sẵn trong React, không cần cài đặt.

* **Ưu điểm:** Không tốn dung lượng bundle, chuẩn React.
* **Nhược điểm:** Cấu hình hơi dài dòng (Cần Provider), hiệu năng không tốt bằng Zustand nếu dữ liệu thay đổi liên tục (gây re-render thừa).
* **Khi nào dùng:** Theme (Sáng/Tối), Ngôn ngữ (Anh/Việt), Auth (Lưu user đăng nhập).

### Code mẫu: Chức năng đổi Theme (Dark Mode)

**Bước 1: Tạo Context và Provider**
Tạo file `context/ThemeContext.jsx`:

```jsx
import { createContext, useState, useContext } from 'react';

// 1. Khởi tạo Context
const ThemeContext = createContext();

// 2. Tạo Provider (Cái thùng chứa dữ liệu)
export const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState("light"); // 'light' hoặc 'dark'

  const toggleTheme = () => {
    setTheme((curr) => (curr === "light" ? "dark" : "light"));
  };

  return (
    // Truyền biến 'theme' và hàm 'toggleTheme' xuống cho tất cả con cháu
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

// 3. Custom Hook để các component con gọi cho lẹ (Optional nhưng nên làm)
export const useTheme = () => useContext(ThemeContext);
```

**Bước 2: Bọc ứng dụng (Trong `main.jsx`)**

```jsx
import { ThemeProvider } from './context/ThemeContext';

// ...
<ThemeProvider>
    <App />
</ThemeProvider>
// ...
```

**Bước 3: Sử dụng ở bất kỳ đâu**

```jsx
import { useTheme } from '../context/ThemeContext';

const Header = () => {
  const { theme, toggleTheme } = useTheme(); // Lấy dữ liệu từ kho

  return (
    <div style={{ background: theme === 'light' ? '#fff' : '#333' }}>
      <h1>Chế độ hiện tại: {theme}</h1>
      <button onClick={toggleTheme}>Đổi màu</button>
    </div>
  );
};
```

---

## 3. Zustand (Hàng "quốc dân" hiện nay)
Thư viện quản lý state nhỏ nhất, dễ dùng nhất và mạnh nhất hiện nay. Quên Redux cồng kềnh đi.

* **Cài đặt:** `npm install zustand`
* **Ưu điểm:** Code siêu ngắn, **KHÔNG CẦN PROVIDER** bao bọc, hiệu năng cực cao (chỉ render lại component nào dùng dữ liệu).
* **Khi nào dùng:** Giỏ hàng, Danh sách dữ liệu phức tạp, Trạng thái App.

### Code mẫu: Giỏ hàng (Shopping Cart)

**Bước 1: Tạo Store (`store/useCartStore.js`)**

```jsx
import { create } from 'zustand';

// set: hàm dùng để cập nhật state
const useCartStore = create((set) => ({
  // State ban đầu
  cartCount: 0,
  cartItems: [],

  // Action: Thêm vào giỏ
  addToCart: () => set((state) => ({ cartCount: state.cartCount + 1 })),
  
  // Action: Xóa sạch giỏ (Reset)
  clearCart: () => set({ cartCount: 0, cartItems: [] }),
}));

export default useCartStore;
```

**Bước 2: Sử dụng (Không cần bọc Provider gì cả!)**

```jsx
import useCartStore from '../store/useCartStore';

const ProductPage = () => {
  // Lấy hàm addToCart từ store
  const addToCart = useCartStore((state) => state.addToCart);

  return <button onClick={addToCart}>Thêm vào giỏ</button>;
};

const CartIcon = () => {
  // Lấy số lượng từ store (Chỉ component này re-render khi số lượng đổi)
  const cartCount = useCartStore((state) => state.cartCount);

  return <div>🛒 Giỏ hàng: {cartCount}</div>;
};
```

---

## 4. Bảng so sánh "Chốt đơn"

| Tiêu chí | Context API | Zustand |
| :--- | :--- | :--- |
| **Cài đặt** | Có sẵn trong React. | Phải cài `npm install zustand`. |
| **Provider** | **Bắt buộc** (Phải bọc `<Provider>` ở root). | **Không cần** (Import là dùng luôn). |
| **Boilerplate** | Khá dài dòng. | Cực ngắn gọn. |
| **Hiệu năng** | Render lại tất cả con cháu nếu không tối ưu kỹ. | Tối ưu tự động (Chỉ render component dùng state). |
| **Dùng tốt nhất cho** | Theme, Auth, Ngôn ngữ (Ít thay đổi). | Giỏ hàng, Form data, App State (Thay đổi nhiều). |

---

## 🎯 Lời khuyên thực tế

1.  **Dự án nhỏ / Bài tập:** Dùng **Context API** cho quen tư duy React.
2.  **Dự án Đi làm / Thực tế:**
    * Dùng **Context API** cho: Authentication (Lưu user), Theme.
    * Dùng **Zustand** cho: Tất cả các state còn lại (Giỏ hàng, Filter, Modal manager...).