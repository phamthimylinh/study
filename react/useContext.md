Trong lập trình frontend, cụ thể là với React (một thư viện JavaScript phổ biến), useContext là một React Hook được sử dụng để truy cập và sử dụng dữ liệu từ React Context mà không cần truyền props qua nhiều cấp component.

## 1. useContext là gì?
useContext là một Hook trong React cho phép một component chức năng (functional component) truy cập vào giá trị của một Context được tạo bởi React.createContext. Context là một cơ chế trong React giúp chia sẻ dữ liệu (state, functions, v.v.) giữa các component mà không cần truyền props qua từng cấp.

```
const value = useContext(MyContext);
```
