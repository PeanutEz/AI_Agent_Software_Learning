# Git — Lệnh thường dùng & chuẩn viết commit

> Ngày cập nhật: 2026-02-25

## 0) Git và GitHub là gì (rất ngắn)

### Git là gì?
- **Git** là hệ thống quản lý phiên bản (Version Control System — VCS) theo mô hình **phân tán**.
- Git lưu lịch sử thay đổi dưới dạng các **commit**; giúp nhiều người làm chung, quay lại phiên bản cũ, tách nhánh (branch) để phát triển song song.

Luồng làm việc cơ bản trong Git:
- Bạn sửa trong **working tree** → đưa thay đổi vào **staging area (index)** → tạo **commit** trong **local repository** → đồng bộ lên **remote**.

### GitHub là gì?
- **GitHub** là nền tảng **lưu trữ và cộng tác** cho các repository Git trên cloud.
- Ngoài việc “host” repo Git, GitHub còn cung cấp các tính năng để làm việc nhóm như: Pull Request (PR), review, Issues, Actions (CI/CD), wiki, releases…
- GitHub **không phải là Git**: Git là công cụ VCS; GitHub là dịch vụ/hệ sinh thái dùng Git.

Từ khóa:
- **Working tree**: file bạn đang sửa.
- **Stage/Index**: “danh sách” thay đổi sẽ được commit.
- **Commit**: một snapshot có message, author, timestamp.
- **Branch**: con trỏ tới chuỗi commit.
- **Remote**: repo trên server.

---

## 1) Thiết lập ban đầu (cực quan trọng)

### 1.1 Kiểm tra phiên bản
```powershell
git --version
```

### 1.2 Cấu hình tên và email (gắn vào commit)
```powershell
git config --global user.name "Nguyen Van A"
git config --global user.email "a@example.com"
```

Xem lại:
```powershell
git config --global --list
```

### 1.3 Chọn editor và cấu hình line ending (Windows)
Chọn editor (ví dụ VS Code):
```powershell
git config --global core.editor "code --wait"
```

Line endings (khuyến nghị cho Windows để tránh lỗi CRLF/LF):
```powershell
git config --global core.autocrlf true
```
- `true`: checkout ra CRLF, commit lên LF.
- Nếu team yêu cầu khác, phải theo chuẩn dự án.

---

## 2) Các lệnh Git phổ biến nhất (ngắn gọn)

### 2.1 Bắt đầu repo
```powershell
git init
git clone <url>
```

### 2.2 Xem trạng thái & lịch sử
```powershell
git status -sb
git log --oneline --decorate --graph --all
```

### 2.3 Xem thay đổi
```powershell
git diff
git diff --staged
```

### 2.4 Stage / unstage / bỏ thay đổi
```powershell
git add .
git add <file>
git add -p

git restore --staged <file>
git restore <file>
```

### 2.5 Commit (và sửa commit gần nhất)
```powershell
git commit -m "type(scope): subject"
git commit              # mở editor để viết body

git commit --amend      # sửa commit gần nhất (chỉ nên dùng khi chưa push)
```

### 2.6 Branch (tạo/chuyển/merge)
```powershell
git branch
git switch -c feature/login
git switch main
git merge feature/login
```
Nếu conflict: sửa file → `git add .` → `git commit`.

### 2.7 Đồng bộ với remote
```powershell
git remote -v
git fetch
git pull --rebase
git push
git push -u origin <branch>
```

### 2.8 Cất tạm thay đổi (khi cần đổi branch gấp)
```powershell
git stash push -m "wip"
git stash list
git stash pop
```

### 2.9 Hoàn tác an toàn khi đã push
```powershell
git revert <commit>
```

### 2.10 `.gitignore` (rất hay dùng)
Ví dụ `.gitignore`:
```gitignore
node_modules/
dist/
.env
*.log
```
Nếu lỡ track rồi thì untrack:
```powershell
git rm -r --cached <path>
```

> Ghi chú ngắn: `git reset --hard` rất nguy hiểm vì có thể làm mất thay đổi local. Nếu bạn muốn học `reset/rebase/cherry-pick/bisect` mình có thể làm thêm một trang “nâng cao”, tách riêng để khỏi rối.

---

## 3) Chuẩn viết commit (Conventional Commits)

### 3.1 Format
Conventional Commits có dạng:

$$
<type>(<scope>): <subject>

<body>

<footer>
$$

Trong đó:
- `type`: loại thay đổi (feat/fix/docs…)
- `scope` (tùy chọn): phạm vi (auth, api, ui…)
- `subject`: mô tả ngắn, rõ, ở thể mệnh lệnh (imperative)
- `body` (tùy chọn): lý do/chi tiết/ảnh hưởng
- `footer` (tùy chọn): breaking change, issue link…

### 3.2 Các `type` hay dùng (nhóm phổ biến)
- `feat`: thêm tính năng
- `fix`: sửa bug
- `docs`: tài liệu
- `refactor`: refactor
- `test`: thêm/sửa test
- `chore`: việc lặt vặt (tooling, cleanup)

Các type khác tùy dự án: `style`, `perf`, `build`, `ci`, `revert`.

### 3.3 Quy tắc viết `subject` (rất quan trọng)
- Dùng **thể mệnh lệnh**: “add”, “update”, “remove”, “fix”…
- Không chấm `.` ở cuối
- Ngắn gọn (thường ≤ 50 ký tự nếu team theo quy ước 50/72)
- Nói “làm gì” hơn là “đã làm gì”

Ví dụ tốt:
- `feat(auth): add refresh token rotation`
- `fix(api): handle null userId in profile endpoint`
- `docs(readme): add local setup steps`

Ví dụ không tốt:
- `update code`
- `fix bug`
- `Added new feature for login` (quá dài + không imperative chuẩn)

### 3.4 Khi nào cần body?
Viết body khi cần giải thích **why** (lý do), trade-off, hoặc hướng dẫn migration.

### 3.5 Breaking changes
Nếu có breaking change:
- thêm `!` sau scope/type
- và/hoặc footer `BREAKING CHANGE:`

Ví dụ:
```text
feat(api)!: rename /v1/users/me to /v1/me

BREAKING CHANGE: Clients must call /v1/me instead of /v1/users/me.
```

### 3.6 Liên kết issue/ticket
Tùy hệ thống, footer thường dùng:
- `Refs: #123`
- `Closes: #123`
- `Fixes: JIRA-456`

Ví dụ:
```text
fix(ui): prevent double submit on checkout

Closes: #128
```

### 3.7 Thói quen giúp commit “đẹp” và dễ review
- Commit nhỏ, “atomic”: mỗi commit giải quyết 1 ý chính
- Tránh commit lẫn lộn refactor + feature + formatting
- Dùng `git add -p` để tách phần thay đổi
- Trước khi push, xem lại:
```powershell
git status -sb
git diff --staged
git log --oneline -5
```

---

## 4) Workflow mẫu (ngắn)
```powershell
git switch main
git pull --rebase

git switch -c feature/login
git add -p
git commit -m "feat(auth): add login endpoint"
git push -u origin feature/login
```

