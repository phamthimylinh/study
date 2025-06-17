## 1. Eloquent Model Conventions
- Model được tạo ra bởi lệnh `make:model` sẽ được đặt trong thư mục **app/Models**, hãy cùng xem một model basic class và thảo luận một số quy ước chính của Eloquent
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    // ...
}
```
### 1.1 Table name
- Sau khi xem ví dụ trên, bạn có thể nhận thấy rằng chúng tôi đã không nói eloquent cái mà bảng trong csdl nào tương ứng với `Flight` model
- Theo quy ước, tên "snack case", số nhiều của của class sẽ được sử dụng làm tên bảng trừ khi có tên khác được chỉ định rõ ràng. Vì vậy, trong trường hợp này, eloquent sẽ cho rằng `Flight` model lưu trữ các bản ghi trong bảng "flights".
Trong khi một model `AirTrafficController` model sẽ lưu trữ các bản ghi trong bảng **air_traffic_controllers**

- Nếu bảng trong csdl tương ứng của model không phù hợp với quy ước này, bạn có thể chỉ định thủ công tên bảng của model bằng cách xác định thuộc tính bảng trên model
```php 
 <?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * The table associated with the model.
     *
     * @var string
     */
    protected $table = 'my_flights';
}
```
### 1.2 Primary key
- Eloquent cũng sẽ giả định rằng mỗi bảng trong csdl tương ứng của model đều có một cột khóa chính có một khóa chính có tên là `id`.
- Nếu cần thiết, bạn có thể xác định thuộc tính `$primaryKey` để chỉ định một cột khác đóng vai trò là khóa chính của primary key
```php
 <?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * The primary key associated with the table.
     *
     * @var string
     */
    protected $primaryKey = 'flight_id';
}
```
- Ngoài ra, Eloquent cho rằng khóa chính là một giá trị số nguyên tăng dần, nghĩa là Eloquent sẽ tự động chuyển đổi khóa chính thành một số nguyên
- Nếu bạn muốn sử dụng khóa chính không tăng dần hoặc không phải là số, bạn phải xác định thuộc tính $incrementing công khai trên model của mình được đặt thành false
```php
<?php

class Flight extends Model
{
    /**
     * Indicates if the model's ID is auto-incrementing.
     *
     * @var bool
     */
    public $incrementing = false;
}
```
- Nếu khóa chính của model không phải là số nguyên, bạn nên xác định thuộc tính **$keyType** được bảo vệ model của bạn. Thuộc tính này phải có giá trị là chuỗi
```php
<?php

class Flight extends Model
{
    /**
     * The data type of the primary key ID.
     *
     * @var string
     */
    protected $keyType = 'string';
}
```
### 1.3 "composite" primary keys
- Eloquent yêu cầu mỗi model phải có ít nhất 1 "ID" nhận dạng duy nhất có thể làm khóa chính. Các model eloquent không hỗ trợ khóa chính `composite`
- Tuy nhiên, bạn có thể tự do thêm trong csdl

## 2. UUID and ULID key


