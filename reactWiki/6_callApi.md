# Call API Cheatsheet: Axios vs TanStack Query 📡

Nhiệm vụ: Lấy dữ liệu từ Backend (Server) về hiển thị lên Frontend.

---

## 1. Cách cơ bản: Axios + useEffect
Đây là cách "thủ công". Bạn phải tự tay quản lý 3 trạng thái: Đang tải (Loading), Có lỗi (Error), và Có dữ liệu (Data).

### Cài đặt
```bash
npm install axios
```

### Code mẫu chuẩn
```jsx
import { useState, useEffect } from "react";
import axios from "axios";

const UserList = () => {
  // 1. Khai báo 3 biến state bắt buộc phải có
  const [users, setUsers] = useState([]);         // Chứa dữ liệu
  const [loading, setLoading] = useState(true);   // Trạng thái loading
  const [error, setError] = useState(null);       // Trạng thái lỗi

  useEffect(() => {
    // Hàm gọi API
    const fetchData = async () => {
      try {
        setLoading(true); // Bắt đầu tải -> Bật loading
        
        // Gọi API (Thay URL của bạn vào đây)
        const response = await axios.get("[https://jsonplaceholder.typicode.com/users](https://jsonplaceholder.typicode.com/users)");
        
        setUsers(response.data); // Lưu dữ liệu vào state
      } catch (err) {
        setError(err.message); // Lưu lỗi nếu có
      } finally {
        setLoading(false); // Dù thành công hay thất bại cũng tắt loading
      }
    };

    fetchData();
  }, []); // [] -> Chạy 1 lần khi mount

  // 2. Render giao diện dựa trên 3 trạng thái
  if (loading) return <p>⏳ Đang tải dữ liệu...</p>;
  if (error) return <p>❌ Lỗi rồi: {error}</p>;

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
};

export default UserList;
```

---

## 2. Cách chuyên nghiệp: TanStack Query (React Query)
Tại sao dùng cái này? Vì cách trên quá dài dòng! React Query giúp bạn xóa bỏ 90% code quản lý state ở trên. Nó tự động cache, tự động gọi lại khi mạng lag, tự động deduping.

### Cài đặt
```bash
npm install @tanstack/react-query axios
```

### Bước 1: Cấu hình (Bọc App lại)
Làm ở file `main.jsx` (hoặc `App.jsx`).

```jsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import App from './App';

// Tạo client
const queryClient = new QueryClient();

ReactDOM.createRoot(document.getElementById('root')).render(
  // Cung cấp QueryClient cho toàn bộ app
  <QueryClientProvider client={queryClient}>
    <App />
  </QueryClientProvider>
);
```

### Bước 2: Sử dụng trong Component (`useQuery`)
Code ngắn hơn, sạch hơn và mạnh hơn gấp 10 lần.

```jsx
import { useQuery } from '@tanstack/react-query';
import axios from 'axios';

// Hàm call API tách riêng ra cho gọn
const fetchUsers = async () => {
  const res = await axios.get("[https://jsonplaceholder.typicode.com/users](https://jsonplaceholder.typicode.com/users)");
  return res.data;
};

const UserListPro = () => {
  // useQuery tự trả về data, isLoading, error, isError
  const { data, isLoading, isError, error } = useQuery({
    queryKey: ['users'], // Key định danh (để cache dữ liệu)
    queryFn: fetchUsers, // Hàm gọi API
    staleTime: 60000,    // (Tùy chọn) Giữ cache trong 60s không gọi lại
  });

  if (isLoading) return <p>⏳ Đang tải...</p>;
  if (isError) return <p>❌ Lỗi: {error.message}</p>;

  return (
    <ul>
      {/* React Query trả dữ liệu trong biến 'data' */}
      {data.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
};
```

---

## 3. So sánh nhanh

| Tính năng | Axios + useEffect (Thủ công) | React Query (Tự động) |
| :--- | :--- | :--- |
| **Code** | Dài dòng, lặp lại nhiều. | Ngắn gọn, tập trung logic. |
| **Loading/Error** | Phải tự tạo state `useState`. | Có sẵn biến `isLoading`, `isError`. |
| **Caching** | Không có (Mỗi lần vào lại trang là gọi lại API). | **Có sẵn** (Lưu lại data, vào lại trang hiển thị ngay lập tức). |
| **Background Update** | Không có. | Tự động cập nhật ngầm khi dữ liệu cũ. |
| **Window Focus** | Không có. | Tự gọi lại API khi người dùng chuyển tab quay lại. |

---

## 🎯 Khi nào dùng cái nào?

1.  **Dự án học tập / Bài tập nhỏ:** Dùng **Cách 1 (Axios + useEffect)** để hiểu luồng chạy của dữ liệu.
2.  **Dự án tốt nghiệp / Đi làm:** Bắt buộc dùng **React Query** (hoặc SWR). Sếp sẽ đánh giá rất cao nếu bạn biết dùng cái này vì nó tối ưu trải nghiệm người dùng cực tốt.