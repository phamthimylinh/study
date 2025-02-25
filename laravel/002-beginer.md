# Mục A. Học lập trình cơ bản
## 🔥 1. Khái niệm về lập trình: Biến, kiểu dữ liệu, toán tử
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

## 🔥 2. Cấu trúc điều kiện, vòng lặp
### 📌 2.1. Cấu trúc điều kiện (`if-else`, `switch`)
Điều kiện giúp chương trình quyết định nên chạy đoạn code nào.
#### ✅ Ví dụ if-else
```php
<?php
$age = 18;

if ($age >= 18) {
    echo "Bạn đủ tuổi lái xe\n";
} else {
    echo "Bạn chưa đủ tuổi\n";
}
?>
```
#### ✅ Ví dụ `switch`
```php
<?php
$day = "Monday";

switch ($day) {
    case "Monday":
        echo "Hôm nay là thứ Hai\n";
        break;
    case "Friday":
        echo "Hôm nay là thứ Sáu\n";
        break;
    default:
        echo "Không rõ hôm nay là ngày nào\n";
}
?>
```
### 2.2. Vòng lặp (`for`, `while`, `foreach`)
Vòng lặp giúp chạy một đoạn code nhiều lần.
#### ✅ Ví dụ vòng lặp `for`
```php
<?php
for ($i = 1; $i <= 5; $i++) {
    echo "Lần lặp thứ $i\n";
}
?>
```
#### ✅ Ví dụ vòng lặp `while`
```php
<?php
$x = 1;
while ($x <= 3) {
    echo "Số: $x\n";
    $x++;
}
?>
```
#### ✅ Ví dụ vòng lặp `foreach`
```php
<?php
$fruits = ["Táo", "Chuối", "Cam"];

foreach ($fruits as $fruit) {
    echo "Trái cây: $fruit\n";
}
?>
```

## 🔥 3. Hàm và phạm vi biến
### 📌 3.1. Định nghĩa và gọi hàm
Hàm giúp tái sử dụng code
#### ✅ Ví dụ định nghĩa hàm
```php
<?php
function sayHello($name) {
    return "Xin chào, $name!";
}

echo sayHello("John");
?>
```
#### ✅ Ví dụ phạm vi biến
```php
<?php
$x = 10; // Biến global

function test() {
    global $x; // Truy cập biến toàn cục
    echo "Giá trị của x: $x\n";
}

test();
?>
```

## 🔥 4. Làm việc với mảng, chuỗi
### 📌 4.1. Mảng
Mảng giúp lưu nhiều giá trị trong 1 biến.
#### ✅ Ví dụ mảng
```php
<?php
$names = ["An", "Bình", "Cường"];
echo "Tên đầu tiên: " . $names[0];
?>
```
### 📌 4.2. Chuỗi
PHP có nhiều hàm xử lý chuỗi.
#### ✅ Ví dụ xử lý chuỗi
```php
<?php
$str = "Hello PHP";
echo strtoupper($str); // Viết hoa
echo strlen($str); // Độ dài chuỗi
?>
```

# B. Làm quen với PHP 
## 🔥1. Cấu trúc file PHP
PHP là một ngôn ngữ server-side, tức là mã PHP chạy trên máy chủ và tạo ra HTML để gửi về trình duyệt.
Một file PHP thường có phần mở đầu bằng `<?php` và kết thúc (có thể không cần) `?>`.
#### ✅ Ví dụ cấu trúc file PHP
```php
<?php
echo "Hello World!";
?>
```
👉 Khi chạy trên trình duyệt, nó sẽ hiển thị:
```
Hello World!
```
#### ✅ Cách tổ chức file PHP trong dự án
1. **index.php** - File chính của trang web.
2. **config.php** - Chứa các thông tin cấu hình như kết nối database.
3. **functions.php** - Chứa các hàm dùng chung.
4. **header.php, footer.php** - Chia giao diện thành phần để tái sử dụng.

##### 💡 Lưu ý:
- PHP không hiển thị lỗi nếu cấu trúc file sai. .Hãy luôn bật chế độ báo lỗi để debug dễ dàng:
```php
error_reporting(E_ALL);
ini_set('display_errors', 1);
```
## 🔥2.Xử lý form & dữ liệu từ người dùng
Khi làm web, bạn cần lấy dữ liệu từ form. PHP hỗ trợ lấy dữ liệu thông qua `$_GET` và `$_POST`.
#### ✅ Ví dụ: Form nhập tên
```php
<!DOCTYPE html>
<html>
<head>
    <title>Form nhập tên</title>
</head>
<body>
    <form action="welcome.php" method="POST">
        Nhập tên: <input type="text" name="username">
        <input type="submit" value="Gửi">
    </form>
</body>
</html>
```
👉 Khi người dùng nhập tên và nhấn gửi, trình duyệt sẽ chuyển sang `welcome.php` với dữ liệu.
#### welcome.php (Xử lý dữ liệu nhận được)
```php
<?php
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $name = htmlspecialchars($_POST['username']); // Chống XSS
    echo "Xin chào, " . $name;
}
?>
```
#### 💡 Lưu ý bảo mật:
- `htmlspecialchars()` tránh lỗ hổng XSS (Cross-Site Scripting).
- Kiểm tra `$_SERVER["REQUEST_METHOD"]` để đảm bảo chắc chắn dữ liệu được gửi qua method POST.
## 🔥3. Làm việc với file trong PHP
PHP hỗ trợ đọc, ghi, upload file rất dễ dàng.
#### Đọc file:
```php
<?php
$filename = "example.txt";
$content = file_get_contents($filename);
echo nl2br($content);
?>
```
#### Ghi file:
```php
<?php
$filename = "example.txt";
file_put_contents($filename, "Dữ liệu mới được ghi vào file\n", FILE_APPEND);
?>
```
👉 Dữ liệu mới sẽ được ghi vào cuối file.

#### Upload file:
Tạo một form để upload file:
```php
<form action="upload.php" method="POST" enctype="multipart/form-data">
    Chọn file: <input type="file" name="uploadedFile">
    <input type="submit" value="Upload">
</form>
```
**Xử lý upload** trong `upload.php`:
```php
<?php
if ($_FILES['uploadedFile']['error'] == 0) {
    move_uploaded_file($_FILES['uploadedFile']['tmp_name'], 'uploads/' . $_FILES['uploadedFile']['name']);
    echo "Upload thành công!";
} else {
    echo "Lỗi upload file!";
}
?>
```
#### 💡 Lưu ý bảo mật:
- Kiểm tra loại file (`$_FILES['uploadedFile']['type']`).
- Chỉ cho phép các định dạng an toàn (JPG, PNG, etc.).

## 🔥4. Xử lý lỗi và debug trong PHP

