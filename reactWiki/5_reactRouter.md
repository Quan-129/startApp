# React Router DOM Cheatsheet (v6) 🚦

React là SPA (Single Page Application). Nghĩa là thực chất chỉ có **1 file HTML duy nhất**. React Router giúp ta giả lập việc chuyển trang bằng cách tráo đổi các Component dựa trên đường dẫn URL.

---

## 1. Cài đặt
Mở terminal tại thư mục dự án và chạy:

```bash
npm install react-router-dom
```

---

## 2. Thiết lập (Setup)
Để Router hoạt động, bạn phải bao bọc toàn bộ ứng dụng bằng `BrowserRouter`. Thường làm ở file `main.jsx` (hoặc `index.js`).

```jsx
// file: main.jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import { BrowserRouter } from 'react-router-dom' // <--- Import cái này
import App from './App'

ReactDOM.createRoot(document.getElementById('root')).render(
  <BrowserRouter> {/* Bao bọc App lại */}
    <App />
  </BrowserRouter>
)
```

---

## 3. Cấu hình Tuyến đường (Defining Routes)
Tại `App.jsx`, chúng ta định nghĩa bản đồ: "Đường dẫn nào thì hiện Component nào".

* **`Routes`**: Là cái thùng chứa tất cả các tuyến đường.
* **`Route`**: Định nghĩa từng tuyến đường cụ thể.

```jsx
// file: App.jsx
import { Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import About from './pages/About';
import NotFound from './pages/NotFound';

function App() {
  return (
    <Routes>
      {/* 1. Trang chủ (path="/") */}
      <Route path="/" element={<Home />} />
      
      {/* 2. Trang giới thiệu */}
      <Route path="/about" element={<About />} />
      
      {/* 3. Trang 404 (Dấu * đại diện cho tất cả link không tồn tại) */}
      <Route path="*" element={<NotFound />} />
    </Routes>
  );
}
```

---

## 4. Điều hướng (Navigation) - Link vs thẻ `<a>`
Đây là điểm khác biệt quan trọng nhất.

* **Thẻ `<a>` (HTML thường):** Gửi request lên server $\rightarrow$ Tải lại cả trang (Trắng trang rồi mới hiện) $\rightarrow$ **CẤM DÙNG** trong React Router (trừ khi link ra web ngoài như Google/Facebook).
* **Thẻ `<Link>` (React Router):** Chỉ đổi URL và tráo Component $\rightarrow$ Mượt mà, không load lại trang.

```jsx
import { Link, NavLink } from 'react-router-dom';

const Navbar = () => {
  return (
    <nav>
      {/* Dùng Link thường */}
      <Link to="/">Trang chủ</Link>

      {/* Dùng NavLink (Tự động thêm class 'active' nếu đang ở trang đó) */}
      {/* Rất tiện để làm Menu đổi màu khi được chọn */}
      <NavLink 
        to="/about" 
        className={({ isActive }) => isActive ? "red-text" : "black-text"}
      >
        Giới thiệu
      </NavLink>
    </nav>
  );
};
```

---

## 5. Dynamic Route (Đường dẫn động) & `useParams`
Làm sao để vào trang chi tiết sản phẩm như `/product/123` hay `/product/999`? Ta dùng tham số động (Dynamic Params).

### Bước 1: Định nghĩa Route có tham số (Dấu hai chấm `:`)
```jsx
// Trong App.jsx
<Route path="/product/:id" element={<ProductDetail />} />
```

### Bước 2: Lấy tham số ở Component con (`useParams`)
```jsx
// Trong ProductDetail.jsx
import { useParams } from 'react-router-dom';

const ProductDetail = () => {
  const { id } = useParams(); // Lấy cái :id trên URL xuống
  
  return <h1>Đang xem sản phẩm có ID là: {id}</h1>;
};
```

---

## 6. Điều hướng bằng Code (`useNavigate`)
Đôi khi bạn muốn chuyển trang tự động sau khi xử lý xong việc gì đó (ví dụ: Login thành công thì chuyển về trang chủ). Không thể dùng thẻ `<Link>` click bằng tay được.

Ta dùng Hook `useNavigate`.

```jsx
import { useNavigate } from 'react-router-dom';

const LoginPage = () => {
  const navigate = useNavigate();

  const handleLogin = () => {
    // 1. Xử lý login...
    alert("Đăng nhập thành công!");
    
    // 2. Tự động chuyển hướng về trang chủ
    navigate("/"); 
    
    // Mẹo: navigate(-1) để quay lại trang trước đó (Back)
  };

  return <button onClick={handleLogin}>Đăng nhập ngay</button>;
};
```

---

## 🎯 Tóm tắt quy trình chuẩn
1.  **Cài đặt:** `npm i react-router-dom`.
2.  **Bọc App:** Dùng `BrowserRouter` trong `main.jsx`.
3.  **Vẽ bản đồ:** Dùng `Routes` và `Route` trong `App.jsx`.
4.  **Tạo Menu:** Dùng `Link` hoặc `NavLink` thay cho thẻ `a`.
5.  **Chuyển trang tự động:** Dùng `useNavigate`.
6.  **Lấy ID trên URL:** Dùng `useParams`.