# I. Kiến thức nền tảng
## 1. Cơ chế hoạt đọng của git
- Git là hệ thống quản lý nhiều phên bản phân tán (distributed)
- Git không lưu "file snapshot", mà lưu theo huowsg **content-addressable file system** với **hash SHA-1**
- Hiểu mô hình DAG (Directed Acyclic graph)
- Vai trò của .git/ (HEAD, objects, refs, config,..)

## 2. Các vùng àm việc:
- Working directory
- Staging Area (index)
- Local Repository
- Remote Repository

## 3. Git object Model
- Các loại object: `blob`, `tree`, `commit`, `tag`
- Cấu trúc và liên kết giữa chúng
- Vai trò của SHA-1 hash (content-based addressing)
- `git cat-file`, `git hash-object`, `git ls-tree`, `git rev-parse` (đọc sâu cấu trúc object)

# II. Sử dụng git thành thạo
## 1. Các lệnh cơ bản
- `git init` , `git clone`
- `git add` , `git commit`
- `git status`, `git diff`, `git log`, `git show`
- `git push` , `git pull`, `git fetch` (khác biệt)
- `.gitignore`, `.gitattributes`

## 2. Branching - hiểu về pointer, commit graph, merge base
- `git branch`, `git switch`, `git checkout`
- `git merger`:fast-forward, 3-wqy merge
- `git rebase` + `interactive base`
- `git cherry-pick`, `git stash`
- `git tag` (annotated vs lightweight)

# III. Làm việc nhóm với remote repository
## 1. remote
- `git remote`, git `remote add/set-url`, `origin/main` là gì
- remote tracking branch là gì (`origin/main` vs `main`)
- `git fetch` vs `git pull` vs `pull --rebase`
- Xung đội khi push/pull và cách xử lý

## 2. Git workflow
- git flow, github flow, trunk-based development
- Feature branch, release branch, hotfix
- Cách tổ chức commit: squash, atomic, descriptive commit message

# IV. Phân tích lịch sử, gỡ lỗi, khôi phục
## 1. Lịch sử và điều hướng
- `git log --graph`, `git blame`, `git show`
- `HEAD`, `HEAD^`, `HEAD~2`, `origin/HEAD` là gì

## 2. Gỡ lỗi và phục hồi
- `git reflog` - cứu commit bị mất
- `git reset` (soft, mixed, hard) vs `git revert`
- `git clean -fd` để dọn untraked file
- `git bisect` để  tìm commit gây lỗi (binary search)

# V. Git nâng cao cho quy mô lớn CI/CD
## 1. Tối ưu hiệu suất
- Shallow clone (`--depth=1`)
- Sparse-checkout (`git sparse-checkout`)
- `git gc`, `git fsck`, `git repack`, 
- Hiểu `packfile` và ảnh hưởng đến hiệu suất clone/pull

## 2. Large file support (Git LFS)
- Cách hoạt động
- Khi nào cần, khi nào không

## 3. Submodule vs SubTree
- Khác nhau về quản lý code đa repo
- Ưu nhược điểm từng phương pháp

# VI. Git trong quy trình DevOps / CI / Team quản lý
## 1. Git hooks:
- `pre commit`, `commit-msg`, `pre-push`
- Dùng để enforce rule, lint, test trước khi commit

## 2. Bảo vệ branch & chính sách làm việc nhóm
- Protected branch, force push policy
- Pull request (review/merge), auto rebase/squash
- Conventional commit, semantic versioning từ Git

## 3. Tích hợp CI/CD
- Trigger theo git events (push, PR, tag,..)
- Auto deploy theo tag/version/branch

## 4. Các lệnh ít dùng nhưng hữu ích
- `git shortlog`, `git describe`, `git archive`, `git worktree`
- `git rerere` (reuse recorded resolution)
- `git replace`
