# React Basics Cheatsheet: JSX, Props, State, Events ⚛️

Đây là 4 khái niệm nền tảng để xây dựng bất kỳ ứng dụng React nào.

---

## 1. JSX (JavaScript XML)
JSX cho phép viết mã giống HTML ngay trong file JavaScript.

### Quy tắc "Vàng" của JSX:
1.  **Thẻ đóng:** Mọi thẻ phải được đóng (VD: `<img />`, `<br />`).
2.  **Parent Wrapper:** Component chỉ được trả về **1 phần tử cha duy nhất**.
    * *Mẹo:* Dùng `<> ... </>` (Fragment) để bọc nếu không muốn sinh ra thẻ `div` thừa.
3.  **CamelCase:** Các thuộc tính HTML phải viết kiểu lạc đà.
    * `class` $\rightarrow$ `className`
    * `onclick` $\rightarrow$ `onClick`
    * `for` $\rightarrow$ `htmlFor`
4.  **Nhúng JS:** Dùng cặp ngoặc nhọn `{ }` để viết code JS bên trong JSX.

```jsx
const user = "Gemini";
const imageUrl = "[https://example.com/avatar.jpg](https://example.com/avatar.jpg)";

// ✅ Đúng
const element = (
  <>
    <h1 className="title">Hello, {user}</h1>
    <img src={imageUrl} alt="Avatar" />
  </>
);
```

---

## 2. Components (Thành phần)
React chia nhỏ giao diện thành các mảnh ghép độc lập gọi là Component. Hiện nay **Functional Component** là chuẩn.

```jsx
// Tạo Component (Viết hoa chữ cái đầu)
const MyButton = () => {
  return <button>Click me</button>;
};

// Sử dụng Component
const App = () => {
  return (
    <div>
      <h1>Welcome</h1>
      <MyButton /> {/* Gọi Component như thẻ HTML */}
    </div>
  );
};
```

---

## 3. Props (Properties - Tài sản)
Dùng để **truyền dữ liệu từ Cha xuống Con**.
* **Nguyên tắc:** Props là "Bất biến" (Read-only). Con không được sửa Props của Cha.

```jsx
// 1. Component Con nhận Props (Dùng Destructuring ES6)
const UserCard = ({ name, age }) => {
  return (
    <div className="card">
      <h3>Tên: {name}</h3>
      <p>Tuổi: {age}</p>
    </div>
  );
};

// 2. Component Cha truyền Props
const App = () => {
  return (
    <div>
      <UserCard name="Nam" age={20} />
      <UserCard name="Lan" age={22} />
    </div>
  );
};
```

---

## 4. State (Trạng thái - Trí nhớ)
Props là tĩnh, **State** là động. State là dữ liệu nội tại giúp Component "ghi nhớ" và tự render lại khi thay đổi.

* Dùng Hook: `useState`.
* **Tuyệt đối không** gán trực tiếp (`count = 5`). Phải dùng hàm `setCount`.

```jsx
import { useState } from 'react';

const Counter = () => {
  // Khai báo: [giá_trị_hiện_tại, hàm_cập_nhật] = useState(giá_trị_gốc)
  const [count, setCount] = useState(0);

  const tangLen = () => {
    // setCount(count + 1); // Cách cơ bản
    setCount((prev) => prev + 1); // ✅ Cách chuẩn (Best Practice)
  };

  return (
    <div>
      <h1>Số đếm: {count}</h1>
      <button onClick={tangLen}>Tăng +1</button>
    </div>
  );
};
```

---

## 5. Events (Sự kiện)
Cách React xử lý tương tác người dùng (Click, Input, Submit).

* Sự kiện viết dạng **camelCase** (`onClick`, `onChange`).
* Truyền **function** vào, không phải gọi function.

### Xử lý Click & Input Form
```jsx
const FormLogin = () => {
  const [email, setEmail] = useState("");

  // 1. Handle Input Change
  const handleChange = (event) => {
    setEmail(event.target.value); // Lấy giá trị từ ô input
  };

  // 2. Handle Submit
  const handleSubmit = () => {
    alert(`Đăng nhập với email: ${email}`);
  };

  return (
    <div>
      {/* ❌ Sai: onClick={handleSubmit()} - Nó sẽ chạy ngay lập tức */}
      {/* ✅ Đúng: onClick={handleSubmit} - Chờ click mới chạy */}
      
      <input 
        type="text" 
        value={email} 
        onChange={handleChange} 
        placeholder="Nhập email..." 
      />
      
      <button onClick={handleSubmit}>Login</button>
    </div>
  );
};
```

---

## 🎯 Ví dụ tổng hợp: Ứng dụng "Like Ảnh"
Kết hợp cả 4 kiến thức trên vào một component thực tế.

```jsx
import { useState } from 'react';

// 1. Component hiển thị ảnh (Nhận Props)
const ImageCard = ({ src, title }) => {
  // 2. State quản lý lượt like
  const [likes, setLikes] = useState(0);
  const [isLiked, setIsLiked] = useState(false);

  // 3. Event xử lý click
  const handleLike = () => {
    if (isLiked) {
      setLikes(likes - 1);
    } else {
      setLikes(likes + 1);
    }
    setIsLiked(!isLiked); // Đảo ngược trạng thái true/false
  };

  return (
    <div style={{ border: '1px solid #ddd', padding: '10px', width: '200px' }}>
      {/* JSX hiển thị ảnh */}
      <img src={src} alt={title} style={{ width: '100%' }} />
      <h3>{title}</h3>
      
      <div style={{ display: 'flex', justifyContent: 'space-between' }}>
        <span>❤️ {likes} lượt thích</span>
        
        {/* Render có điều kiện (Conditional Rendering) */}
        <button 
          onClick={handleLike}
          style={{ background: isLiked ? 'red' : 'gray', color: 'white' }}
        >
          {isLiked ? 'Bỏ thích' : 'Thích'}
        </button>
      </div>
    </div>
  );
};

// Component Cha
const App = () => {
  return (
    <div style={{ display: 'flex', gap: '20px' }}>
      <ImageCard src="[https://via.placeholder.com/150](https://via.placeholder.com/150)" title="Ảnh phong cảnh" />
      <ImageCard src="[https://via.placeholder.com/150](https://via.placeholder.com/150)" title="Ảnh chân dung" />
    </div>
  );
};

export default App;
```