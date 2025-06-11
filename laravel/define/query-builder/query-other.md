# 1. Pessimistic locking
- Query builder cũng bao gồm các chức năng giúp bạn đạt được "khóa bi quan" khi thực hiện các câu lệnh truy vấn `select`.
- Để thực thi một lệnh với "share lock", bạn có thể gọi phương thức `shareLock`
- ShareLock ngăn cho các hàng đã chọn bị sửa đổi đến khi transaction được xác nhận
```php
DB::table('users')
    ->where('votes', '>', 100)
    ->sharedLock()
    ->get();
```
- Ngoài ra, bạn cũng có thể  sử dụng phương thức `lockForUpdate`. Khóa "for update" ngăn không cho các bản ghi được chọn bị sửa đổi hoặc được chọn bằng share look khác.
```php
DB::table('users')
    ->where('votes', '>', 100)
    ->lockForUpdate()
    ->get();
``` 
- Mặc dù không bắt buộc, nhưng nên bao bọc pessimistic trong transaction. Điều này đảm bảo dữ liệu được truy vấn vẫn không thay đổi trong csdl cho đến khi toàn bộ hoạt động hoàn tất.
- Trong trường hợp xảy ra lỗi, transaction sẽ hoàn lại toàn bộ mọi thay đổi và tự động giải phóng khóa
```php
DB::transaction(function () {
    $sender = DB::table('users')
        ->lockForUpdate()
        ->find(1);

    $receiver = DB::table('users')
        ->lockForUpdate()
        ->find(2);

    if ($sender->balance < 100) {
        throw new RuntimeException('Balance too low.');
    }

    DB::table('users')
        ->where('id', $sender->id)
        ->update([
            'balance' => $sender->balance - 100
        ]);

    DB::table('users')
        ->where('id', $receiver->id)
        ->update([
            'balance' => $receiver->balance + 100
        ]);
});
```

# 2. Reuseable query components
- Nếu bạn lặp lại logic truy vấn trong toàn bộ ứng dụng, bạn có thể trích xuất logic thành các đối tượng có thể tái sử dụng bằng cách sử dụng phương thức `tap` và `pipe`
- Hãy tưởng tượng, bạn có hai truy vấn khác nhau trong ứng dụng của mình
```php
use Illuminate\Database\Query\Builder;
use Illuminate\Support\Facades\DB;

$destination = $request->query('destination');

DB::table('flights')
    ->when($destination, function (Builder $query, string $destination) {
        $query->where('destination', $destination);
    })
    ->orderByDesc('price')
    ->get();

// ...

$destination = $request->query('destination');

DB::table('flights')
    ->when($destination, function (Builder $query, string $destination) {
        $query->where('destination', $destination);
    })
    ->where('user', $request->user()->id)
    ->orderBy('destination')
    ->get();
```
- Bạn có thể muốn trích xuất bộ lọc chung giữa các truy vấn thành một đối tượng để tái sử dụng
```php
<?php

namespace App\Scopes;

use Illuminate\Database\Query\Builder;

class DestinationFilter
{
    public function __construct(
        private ?string $destination,
    ) {
        //
    }

    public function __invoke(Builder $query): void
    {
        $query->when($this->destination, function (Builder $query) {
            $query->where('destination', $this->destination);
        });
    }
}
```
- Sau đó, bạn có thể sử dụng các phương thức `tap` của query builder để áp dụng logic của đối tượng vào truy vấn 
```php
use App\Scopes\DestinationFilter;
use Illuminate\Database\Query\Builder;
use Illuminate\Support\Facades\DB;

DB::table('flights')
    ->tap(new DestinationFilter($destination))
    ->orderByDesc('price')
    ->get();

// ...

DB::table('flights')
    ->tap(new DestinationFilter($destination))
    ->where('user', $request->user()->id)
    ->orderBy('destination')
    ->get();
```

**Query Pipes**
- Phương thức `tab` sẽ luôn trả về query builder. Nếu bạn muốn trích xuất một đối tượng thực thi truy vấn và trả về một giá trị khác, bạn có thể sử dụng phương thức `pipe` thay thế
- Hãy xem xét đối tượng truy vấn sau đây chứa logic phân trang được share trong toàn bộ ứng dụng
- Không giống như **DestinationFilter**, cái mà áp dụng các điểu kiện truy vấn cho truy vấn, đối tượng `Paginate` thực thi query và trả về một paginator instance:
```php
<?php

namespace App\Scopes;

use Illuminate\Contracts\Pagination\LengthAwarePaginator;
use Illuminate\Database\Query\Builder;

class Paginate
{
    public function __construct(
        private string $sortBy = 'timestamp',
        private string $sortDirection = 'desc',
        private string $perPage = 25,
    ) {
        //
    }

    public function __invoke(Builder $query): LengthAwarePaginator
    {
        return $query->orderBy($this->sortBy, $this->sortDirection)
            ->paginate($this->perPage, pageName: 'p');
    }
}
```
- Sử dụng phương thức `pipe` chúng ta có thể tận dụng tối tượng này để áp dụng logic phân trang được chia sẻ
```php
$flights = DB::table('flights')
    ->tap(new DestinationFilter($destination))
    ->pipe(new Paginate);
```
# 3. Debugging
- Bạn có thể sử dụng phương thức `dd` và `dump` trong khi xây dựng query để dump các ràng buộc truy vấn hiện tại và SQL. 
- Phương thức `dd` sẽ hiển thị thông tin debug và sau đó sẽ dừng thực hiện yêu cầu.
-Phương thức `dump` sẽ hiển thị thông tin debug nhưng cho phép yêu cầu tiếp tục thực hiện
```php
DB::table('users')->where('votes', '>', 100)->dd();

DB::table('users')->where('votes', '>', 100)->dump();
```
- Các phương thức `dumpRawSql` và `ddRawSql` có thể được gọi trên 1 query để dump SQL query với tất cả các ràng buộc tham số được thay thế đúng cách
```php
DB::table('users')->where('votes', '>', 100)->dumpRawSql();

DB::table('users')->where('votes', '>', 100)->ddRawSql();
```
