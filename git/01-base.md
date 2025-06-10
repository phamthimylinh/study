# 1. Cơ chế hoạt động của Git
Git là một hệ thống quản lý phiên bản phân tán (distributed version control system - DVCS). Để hiểu bản chất của Git, chúng ta cần nắm rõ 4 khía cạnh chính:
1. Git là hệ thống phân tán.
2. Git lưu dữ liệu theo content-addressable file system với SHA-1
3. Git sử dụng mô hình DAG (Directed Acyclic Graph)
4. Vai trò của thư mục .git

## 1.1 Git là hệ thống quản lý phiên bản phân tán (Distributed)
- Ý nghĩa "phân tán"
    - Khác với các hệ thống quản lý phiên bản tập trung (như SVN), Git không phụ thuộc vào máy chủ trung tâm duy nhất. Mỗi developer có một bản sao đầy đủ của repository (bao gồm toàn bộ lịch sử commit) trên máy local.
    - Điều này cho phép bạn làm việc offline (commit, branch, merge,..) mà không cần kết nối với server. Khi cần, bạn chỉ cần đồng bộ với remote repository (qua push hoặc pull)

- Lợi ích:
    - Tăng tốc độ: Các thao tác như commit, diff, merge được thực hiện trên local
    - An toàn: Mỗi máy local là một bản sao đầy đủ, nên nếu server gặp sự cố, bạn vẫn có toàn bộ dữ liệu
    - Linh hoạt: Dễ dàng làm việc nhóm với nhiều remote repository (github, gitlab, bitbucket,...)

## 1.2 Git không lưu "file snapshot", mà lưu theo content-addressable file system hash SHA-1
- Git không lưu toàn bộ file
    - Nhiều người lầm tưởng rằng Git lưu toàn bộ file mỗi khi bạn commit. Thay vào đó, Git chỉ lưu **những thay đổi** (deltas) và tổ chức dữ liệu một cách thông minh.
    - Git sử dụng một hệ thống gọi là content-addressable file system, nghĩa là dữ liệu được lưu dựa trên nội dung của nó, không phải tên file hay vị trí

- Hash SHA-1 là gì?
    - Mỗi đối tượng (file, thư mục, commit) trong Git được băm (hash) bằng thuật tuoán SHA-1, tạo ra 1 chuỗi 40 ký tự duy nhất (ví dụ `a12adg356d...`)
    - Chuỗi hash này là "địa chỉ" của đối tượng, dựa trên **nội dung** của nó. Nếu nội dung thay đổi dủ chỉ 1 ký tự, hash sẽ hoàn toàn khác.
    - Ví dụ: bạn sửa file `index.js`, Git sẽ tạo 1 hash mới cho nội dung mới của file đó, nhưng các file không đổi vẫn giữ nguyên hash cũ

- Lợi ích
    - Tiết kiệm không gian: Các nội dung giống nhau chỉ được lưu 1 lần (dù xuất hiện ở nhiều commit)
    - Đảm bảo toàn vẹn dữ liệu: Hash SHA-1  giúp phát hiện nếu dữ liệu bị thay đổi ngoài ý muốn 

- Hình dung: Hãy nghĩ Git như 1 "kho lưu trữ" nơi mỗi món đồ (file, thư mục, commit) được dán nhãn bằng 1 mã vạch duy nhất (hash SHA-1). Nếu món đồ thay đổi, mã vạch cũng thay đổi

## 1.3 Hiểu mô hình DAG (Directed Acyclic grahp)
