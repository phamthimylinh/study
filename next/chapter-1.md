# Tại sao không nên lạm dụng useEffect và Recoil trong page của react

## 1. useEffect
useEffect là một React Hook mạnh mẽ để xử lý side effects (như gọi API, cập nhật DOM, lắng nghe sự kiện, v.v.). Nhưng nếu lạm dụng, nó có thể gây ra nhiều vấn đề khó kiểm soát. Hãy tưởng tượng useEffect như một người đầu bếp phụ trong bếp: nếu bạn để họ tự do làm mọi thứ mà không có hướng dẫn rõ ràng, nhà bếp sẽ hỗn loạn.

### Lý do không nên lạm dụng useEffect:
- **Hiệu suất giảm**: useEffect chạy sau mỗi lần render nếu bạn không kiểm soát dependencies đúng cách. Nếu có quá nhiều useEffect không cần thiết, ứng dụng sẽ chậm vì phải xử lý lại nhiều lần
- **Code khó hiểu:** Khi một trang có quá nhiều useEffect, bạn sẽ khó biết cái nào chạy khi nào, phụ thuộc vào cái gì. Nó như một cuốn sách với quá nhiều ghi chú dán lung tung, bạn mất thời gian tìm hiểu.
- **Dễ gây lỗi vòng lặp vô hạn:** Nếu bạn cập nhật state trong useEffect mà không cẩn thận, state thay đổi sẽ trigger lại render, dẫn đến useEffect chạy lại, tạo vòng lặp vô tận.


### Ví dụ thực tế:
Hãy tưởng tượng bạn đang xây một trang hiển thị danh sách sản phẩm trong cửa hàng online. Bạn dùng useEffect để:
1. Gọi API lấy danh sách sản phẩm.
2. Cập nhật tiêu đề trang dựa trên số lượng sản phẩm.
3. Lắng nghe sự kiện cuộn trang để tải thêm sản phẩm.

Nếu bạn viết riêng từng useEffect mà không tối ưu:
```
function ProductList() {
  const [products, setProducts] = useState([]);
  const [pageTitle, setPageTitle] = useState("");
  const [page, setPage] = useState(1);

  // useEffect 1: Gọi API
  useEffect(() => {
    fetchProducts(page).then((data) => setProducts(data));
  }, [page]);

  // useEffect 2: Cập nhật tiêu đề
  useEffect(() => {
    setPageTitle(`Sản phẩm (${products.length})`);
  }, [products]);

  // useEffect 3: Lắng nghe cuộn trang
  useEffect(() => {
    const handleScroll = () => {
      if (window.scrollY > 1000) {
        setPage(page + 1); // Tăng page
      }
    };
    window.addEventListener("scroll", handleScroll);
    return () => window.removeEventListener("scroll", handleScroll);
  }, [page]);

  return <div>{/* Hiển thị sản phẩm */}</div>;
}
```

** Vấn đề **
- Có tới 3 useEffect, mỗi cái làm một việc nhỏ, nhưng chúng liên quan đến nhau (page → products → pageTitle). Điều này làm code khó theo dõi.
- Nếu không cẩn thận, setPage(page + 1) trong useEffect thứ 3 có thể gây ra vòng lặp vô hạn nếu API trả dữ liệu quá nhanh.
- Mỗi lần render, React phải kiểm tra và chạy lại các useEffect này, làm ứng dụng chậm hơn, đặc biệt khi danh sách sản phẩm dài.

** Cách cải thiện **
- Gộp logic liên quan vào một useEffect duy nhất nếu có thể.
- Sử dụng các công cụ khác như event handler hoặc useMemo để thay thế useEffect khi không cần side effect.

- Ví dụ 1
```
function ProductList() {
  const [products, setProducts] = useState([]);
  const [page, setPage] = useState(1);

  // Gộp logic vào 1 useEffect
  useEffect(() => {
    fetchProducts(page).then((data) => {
      setProducts(data);
      document.title = `Sản phẩm (${data.length})`; // Cập nhật tiêu đề trực tiếp
    });

    const handleScroll = () => {
      if (window.scrollY > 1000) {
        setPage((prev) => prev + 1);
      }
    };
    window.addEventListener("scroll", handleScroll);
    return () => window.removeEventListener("scroll", handleScroll);
  }, [page]);

  return <div>{/* Hiển thị sản phẩm */}</div>;
}
```

- Ví dụ 2
```
function ProductList() {
  const [products, setProducts] = useState([]);
  const [page, setPage] = useState(1);

  // Gọi API trong useEffect
  useEffect(() => {
    fetchProducts(page).then((data) => {
      setProducts((prev) => [...prev, ...data]);
      document.title = `Sản phẩm (${data.length})`;
    });
  }, [page]);

  // Event handler cho sự kiện cuộn
  const handleScroll = (event) => {
    const { scrollTop, scrollHeight, clientHeight } = event.target;
    if (scrollTop + clientHeight >= scrollHeight - 50) {
      setPage((prev) => prev + 1); // Tăng page khi cuộn gần cuối
    }
  };

  return (
    <div
      onScroll={handleScroll}
      style={{ height: "400px", overflowY: "auto" }}
    >
      {products.map((product) => (
        <div key={product.id}>{product.name}</div>
      ))}
    </div>
  );
}
```
