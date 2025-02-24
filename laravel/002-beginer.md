# 🔥 1. Khái niệm về lập trình: Biến, kiểu dữ liệu, toán tử
### 📌 1.1. Biến là gì?
- **Biến** là một vùng nhớ dùng để lưu trữ giá trị và có thể thay đổi trong quá trình chạy chương trình.
- Trong PHP, biến được khai báo bằng dấu `$` trước tên biến.
#### ✅ Ví dụ về biến trong PHP
```php
<?php
$name = "John Doe";  // Biến lưu trữ chuỗi
$age = 25;           // Biến lưu số nguyên
$isDeveloper = true; // Biến lưu kiểu boolean

echo "Tên: " . $name . "\n";
echo "Tuổi: " . $age . "\n";
echo "Là lập trình viên? " . ($isDeveloper ? "Có" : "Không") . "\n";
?>
```
#### ⚠️ Lưu ý khi đặt tên biến
- Tên biến chỉ được chứa chữ cái, số, dấu `_` (không có dấu cách).
- Không được bắt đầu bằng số ($1var ❌).
- PHP **phân biệt họa thường** (`$name` khác `$Name`).

### 📌 1.2. Kiểu dữ liệu trong PHP
- PHP là một ngôn ngữ **yếu kiểu** (loosely typed), nghĩa là biến có thể thay đổi kiểu dữ liệu động.


| Kiểu dữ liệu| Ví dụ |
|-----------|------|
| String (Chuỗi)  | `$name = "Hello PHP"`   |
| Integer (Số nguyên)  | `$age = 30`   |
| Float (Số thực)  | `$price = 9.99`   |
| Boolean (Đúng/Sai)  | `$isActive = true`   |
| Array (Mảng)  | `$fruits = ["Apple", "Banana"]`   |
| Object (Đối tượng)  | `$user = new User()`   |
| NULL (Không có giá trị)  | `$data = null`   |

#### ✅ Ví dụ: Kiểm tra kiểu dữ liệu
```php
<?php
$var1 = "Hello";
$var2 = 123;
$var3 = 3.14;
$var4 = true;

var_dump($var1, $var2, $var3, $var4);
?>
```
⏩ `var_dump()` giúp kiểm tra kiểu dữ liệu và giá trị của biến.

### 📌 1.3. Toán tử trong PHP
- PHP có nhiều toán tử, phổ biến gồm:

| Loại toán tử| Ví dụ |
|-----------|------|
|Toán tử số học|`+ - * / %`|
|Toán tử so sánh|`== != < > <= >=`|
|Toán tử logic|`&& !`|
|Toán tử gán|`&& != += -= *= /=`|
|Toán tử nối chuỗi|`"Hello " . "World"`|

#### ✅ Ví dụ toán tử
```php
<?php
$a = 10;
$b = 5;
echo "Tổng: " . ($a + $b) . "\n";
echo "Hiệu: " . ($a - $b) . "\n";
echo "Tích: " . ($a * $b) . "\n";
echo "Lũy thừa: " . ($a ** $b) . "\n"; // 10^5
?>

```

