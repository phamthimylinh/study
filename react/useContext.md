Trong lập trình fontend, cụ thể là react, useContext là một React Hook được sử dụng để truy cập và sử dụng dữ liệu từ React Context mà không cần truyền props qua nhiều cấp component.

## 1. useContext là gì?
`useContext` là một Hook trong react cho phép một function component truy cập vào giá trị của một Context được tạo bởi `React.createContext`. Context là một cơ chế trong React giúp chia sẻ dữ liệu (state, function,...) giữa các component mà không cần truyền props qua từng cấp.

Cú pháp: 
```typescript
const value = useContext(MyContext);
```
- `MyContext`: Là đôi tượng Context được tạo bởi `React.createContext`.
- `value`: Là giá trị hiện tại của Context, được cung cấp bởi component cha gần nhất sử dụng `MyContext.Provider`.

## 2. Tác dụng của `useContext`
`useContext` giúp đơn giản hoá việc quản lý và truy cập dữ liệu toàn cụ (global state) trong ứng dụng React. Các tác dụng chính bao gồm:
### **Tránh "prop drilling"**
- Prop drilling là tình trạng phải truyền props qua nhiều tầng component trung gian để đến được component cần sử dụng dữ liệu.
- Với `useContext`, bạn có thể truy cập dữ liệu trực tiếp từ Context ở bất kỳ component nào trong cây component, miễn là component đó nằm trong phạm vi của `Provider`.

### **Chia sẻ dữ liệu toàn cục**
- Context thường được sử dụng để lưu trữ các dữ liệu chung như:
    - Thông tin người dùng (use info, authentication status).
    - Theme (chủ đề giao diện: sáng/tối).
    - Ngôn ngữ (language setting).
    - Cấu hình ứng dụng.
- `useContext` cho phép các component truy cập dữ liệu này một cách dễ dàng.

### **Tăng tính tái sử dụng và bảo trì**
- Sử dụng `useContext` giúp mã nguồn gọn gàng hơn, dễ bảo trì hơn so với việc truyền props qua nhiều tầng.
- Nó cũng giúp tác biệt logic chia sẻ dữ liệu khỏi cấu trúc component.

## 3. Cách sử dụng `useContext`
Dưới đây là một ví dụ minh hoạ các dùng `useContext` để chia sẻ theme giữa các component:
**Bước 1: Tạo Context**
```javascript
import { crateContext, useContext, useState } from 'react';

// Tạo Context
const ThemeContext = createContext();
function App() {
    const [theme, setTheme] = useState('light');

    return (
        <ThemeContext.Provider value={{ theme, setTheme }}>
            <Toolbar />
        </ThemeContext.Provider>
    );
}
```

**Bước 2: Sử dụng `useContext` trong component**
```javascript
function Toolbar() {
    return (
        <div>
            <ThemedButton />
        </div>
    )
}

function ThemedButton() {
    const { theme, setTheme } = useContext(ThemeContext);

    return (
        <button 
            style={{
                background: theme === 'light' ? '#fff' : '#333',
                color: theme === 'light' ? '#000' : '#fff',
            }}
            onClick={() => setTheme(!theme)}
        >
            Toggle Theme
        </button>
    );
}
```

Giải thích:
- `ThemeContext.Provider` cung cấp giá trị `{theme, setTheme}` cho tất cả các component con.
- Component `ThemedButton` sử dụng `useContext(ThemeContext)` để lấy giá trị `theme` và `setTheme` mà không cần truyền props.

## 4. Khi nào nên dùng `useContext`?
- Khi bạn cần chia sẻ dữ liệu giữa nhiều component ở các cấp khác nhau.
- Khi dữ liệu mạng tính chất toàn cục hoặc được sử dụng nhiều bởi component (ví dụ: theme, use info)
- Khi muốn tránh việc truyền props qua nhiều tầng component.

## 5. Lưu ý khi sử dụng `useContext`
- Hiệu suất: Nếu giá trị Context thay đổi thường xuyên, tất cả các component sử dụng `useContext` sẽ re-render. Để tối ưu, bạn có thể dùng `useMemo` hoặc tách Context thành nhiều Context nhỏ hơn.
- Không lạm dụng: Context không phải là giải pháp thay thế cho tất cả các trường hợp quản lý state. Với các state cục bộ, hãy sử dụng `useState` hoặc `useReducer`.
- Chỉ dùng trong function component: `useContext` chỉ hoạt động trong các component chức năng hoặc custom hooks. Với class component, bạn phải dùng `Context.Consumer`.

## 6. So sánh với các phương pháp khác
- So với Redux: Context + `useContext` đơn giản hơn, phù hợp với các ứng dụng nhỏ đến trung bình. Redux mạnh hơn trong quản lý state phức tạp và có middleware hỗ trợ.
- So với prop drilling: `useContext` loại bỏ sự phức tạp của prop drilling, nhưng cần thiết kế tốt để tránh lạm dụng.

# Kết luận
`useContext` là một công cụ mạnh mẽ trong React để quản lý và chia sẻ dữ liệu toàn cục, giúp giảm bớt sự phức tạp khi truyền props và tính năng bảo trì của mã nguồn. Nó đặc biệt hữu ích trong các ứng dụng có nhiều component cần truy cập cùng một dữ liệu như theme, auth hoặc language settings.
