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


# Ví dụ về các lưu ý
## 1. Hiệu suất kém:
Một context chữa một state lớn và thay đổi thường xuyên dẫn đến re-render không cần thiết cho tất cả component dùng Context
```
import { createContext, useContext, useState } from 'react';

// Tạo Context
const AppContext = createContext();

function App() {
  const [state, setState] = useState({
    user: { name: 'John', age: 30 },
    theme: 'light',
    counter: 0,
  });

  // Cập nhật counter mỗi giây
  setInterval(() => {
    setState((prev) => ({ ...prev, counter: prev.counter + 1 }));
  }, 1000);

  return (
    <AppContext.Provider value={state}>
      <UserProfile />
      <ThemeDisplay />
    </AppContext.Provider>
  );
}

function UserProfile() {
  const { user } = useContext(AppContext); // Re-render mỗi giây dù không cần
  return <div>User: {user.name}</div>;
}

function ThemeDisplay() {
  const { theme } = useContext(AppContext); // Re-render mỗi giây dù không cần
  return <div>Theme: {theme}</div>;
}
```
**Vấn đề**:
- Context chứa nhiều dữ liệu (user, theme, counter) trong 1 object lớn.
- Mỗi khi counter thay đổi mỗi giây, toàn bộ state được cập nhật, khiến tất các component re-render, dù một số component chỉ cần các user hoặc theme thay đổi

**good exam**
Tách context thành các context nhỏ hơn và sử dụng `useMemo` để tránh re-render không cần thiết:
```
import { createContext, useContext, useState, useMemo } from 'react';

// Tách thành các Context riêng
const UserContext = createContext();
const ThemeContext = createContext();
const CounterContext = createContext();

function App() {
  const [user, setUser] = useState({ name: 'John', age: 30 });
  const [theme, setTheme] = useState('light');
  const [counter, setCounter] = useState(0);

  // Cập nhật counter mỗi giây
  setInterval(() => {
    setCounter((prev) => prev + 1);
  }, 1000);

  // Sử dụng useMemo để đảm bảo giá trị Context không thay đổi trừ khi cần
  const userValue = useMemo(() => ({ user, setUser }), [user]);
  const themeValue = useMemo(() => ({ theme, setTheme }), [theme]);

  return (
    <UserContext.Provider value={userValue}>
      <ThemeContext.Provider value={themeValue}>
        <CounterContext.Provider value={counter}>
          <UserProfile />
          <ThemeDisplay />
          <CounterDisplay />
        </CounterContext.Provider>
      </ThemeContext.Provider>
    </UserContext.Provider>
  );
}

function UserProfile() {
  const { user } = useContext(UserContext); // Không re-render khi counter đổi
  return <div>User: {user.name}</div>;
}

function ThemeDisplay() {
  const { theme } = useContext(ThemeContext); // Không re-render khi counter đổi
  return <div>Theme: {theme}</div>;
}

function CounterDisplay() {
  const counter = useContext(CounterContext); // Chỉ re-render khi counter đổi
  return <div>Counter: {counter}</div>;
}
```
- Tách Context: mỗi loại dữ liệu (user, counter, theme) có Context riêng, nên thay đồi ở 1 context không ảnh hưởng đến context khác, 
- Sử dụng `userMemo` đảm bảo giá trị context (userValue, themeValue) không thay đổi trừ khi user hoặc theme thực sự thay đổi, giảm re-render không cần thiết.

## 2. Không lạm dụng
- Sử dụng context cho mọi state, kể cả state cụ bộ, dẫn đến mã phức tạp và khó bảo trì
```javascript
import { createContext, useContext, useState } from 'react';

const FormContext = createContext();

function App() {
  const [inputValue, setInputValue] = useState('');

  return (
    <FormContext.Provider value={{ inputValue, setInputValue }}>
      <Form />
    </FormContext.Provider>
  );
}

function Form() {
  return (
    <div>
      <InputField />
      <SubmitButton />
    </div>
  );
}

function InputField() {
  const { inputValue, setInputValue } = useContext(FormContext);
  return (
    <input
      value={inputValue}
      onChange={(e) => setInputValue(e.target.value)}
      placeholder="Enter text"
    />
  );
}

function SubmitButton() {
  const { inputValue } = useContext(FormContext);
  return <button disabled={!inputValue}>Submit</button>;
}
```

**Vấn đề**
- inputValue là state cục bộ, chỉ liên quan đến component Form và các component con trực tiếp.
- Sử dụng Context cho một state cục bộ như thế này là không cần thiết, làm mã phức tạp hơn và khó mở rộng (nếu có nhiều form, context sẽ gây xung đột)
- Context nên dành cho dữ liệu toàn cục, không phải state cục bộ

**Good example**
Sử dụng state cục bộ trong component và truyền props khi cần.
```
import { useState } from 'react';

function App() {
  return <Form />;
}

function Form() {
  const [inputValue, setInputValue] = useState('');

  return (
    <div>
      <InputField inputValue={inputValue} setInputValue={setInputValue} />
      <SubmitButton inputValue={inputValue} />
    </div>
  );
}

function InputField({ inputValue, setInputValue }) {
  return (
    <input
      value={inputValue}
      onChange={(e) => setInputValue(e.target.value)}
      placeholder="Enter text"
    />
  );
}

function SubmitButton({ inputValue }) {
  return <button disabled={!inputValue}>Submit</button>;
}
```
- inputValue là state cục bộ, được quản lý trong `Form` và truyền qua prop đến `InputFild` và `SubmitButton`.
- Không sử dụng Context, mã đơn giản, dễ hiểu và tránh được overhead không cần thiết.
- Context chỉ nên dùng khi dữ liệu cần chia sẻ qua nhiều tầng component hoặc giữa component không liên quan trực tiếp.
