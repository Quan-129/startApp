# React + Vite + Tailwind CSS Starter Template

Đây là bộ Source Code mẫu (Boilerplate) đã được cấu hình chuẩn "Industry Standard". Dùng để khởi tạo nhanh các dự án ReactJS mà không cần setup lại từ đầu.

---

## 1. Danh sách công nghệ (Tech Stack)

| Tên Thư viện | Phiên bản | Công dụng chính |
| :--- | :--- | :--- |
| **Vite** | Latest | Công cụ Build tool siêu tốc (thay thế Create-react-app). |
| **ReactJS** | 18+ | Thư viện xây dựng giao diện người dùng (UI). |
| **Tailwind CSS** | v3.4.17 | Framework CSS giúp style giao diện cực nhanh bằng class. |
| **React Router DOM** | v6+ | Quản lý chuyển trang (Routing). |
| **Axios** | Latest | Thư viện gọi API chuyên nghiệp. |
| **React Icons** | Latest | Kho icon khổng lồ. |
| **Clsx & Tailwind-merge**| Latest | Hỗ trợ gộp class Tailwind động (cho Components). |

---

## 2. Cấu trúc thư mục chuẩn (Folder Structure)

```text
src/
├── assets/          # Chứa ảnh, font, global css
├── components/      # Các thành phần UI nhỏ (Button, Input, Card)
├── layouts/         # Bố cục trang (Header, Footer, Sidebar)
├── pages/           # Các màn hình chính (HomePage, LoginPage)
├── routes/          # Cấu hình đường dẫn (AppRoutes)
├── services/        # Cấu hình gọi API (Axios instance)
├── hooks/           # Các logic dùng lại (Custom Hooks)
├── utils/           # Các hàm tiện ích (Format tiền, ngày tháng)
├── App.jsx          # File gốc chứa Router
└── main.jsx         # Entry point
```

## 3. Hướng dẫn tạo dự án mới từ Template này

Khi bắt đầu một bài tập lớn hoặc dự án mới, hãy làm theo quy trình 3 bước sau:

### Cách 1: Dùng dòng lệnh (Nhanh nhất - Khuyên dùng)

Sử dụng `degit` để tải code về mà không dính lịch sử git cũ.

**Bước 1: Tải code về thư mục dự án mới**

```bash
npx degit <github-username>/<repo-template-name> <tên-dự-án-mới>
# Ví dụ: npx degit Quan-129/startApp du-an-ban-hang
```

**Bước 2: Cài đặt thư viện**

```bash
cd <tên-dự-án-mới>
npm install
```

**Bước 3: Chạy dự án**

```bash
npm run dev
```

### Cách 2: Dùng giao diện GitHub

1. Vào Repo Template này trên GitHub.
2. Bấm nút **Use this template** > **Create a new repository**.
3. Đặt tên dự án mới và tạo.
4. Clone repo mới đó về máy tính (`git clone ...`).
5. Chạy `npm install` và `npm run dev` như bình thường.

## 4. Các lưu ý quan trọng

- **Đổi tên dự án:** Sau khi tải về, hãy vào file `package.json`, tìm dòng `"name"` và sửa thành tên dự án mới của bạn.
- **File .gitkeep:** Các thư mục rỗng có chứa file `.gitkeep` để giữ chỗ trên GitHub. Bạn có thể xóa chúng đi khi đã thêm file code vào.
- **Cấu hình Tailwind:** Dự án sử dụng file config thủ công để tương thích tốt nhất với Windows/WSL.