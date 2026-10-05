# Bài 2: Tái cấu trúc lịch sử commit bằng Interactive Rebase

## 1. Chuẩn bị lịch sử commit

Khởi tạo repository và cấu hình người thực hiện:

```powershell
git init -b main
git config user.name "CrouAn244"
git config user.email "crouan244@gmail.com"
```

Tạo một commit nền để lệnh `git rebase -i HEAD~4` có thể thao tác chính xác trên bốn commit tiếp theo:

```powershell
Set-Content -Path project.txt -Value "Authentication project"
git add project.txt
git commit -m "chore: khoi tao du an"
```

Tạo lần lượt bốn commit cần chỉnh sửa:

```powershell
Set-Content -Path auth.js -Value "function login() {}"
git add auth.js
git commit -m "feat: khoi tao module auth"

Add-Content -Path auth.js -Value "function logout() {}"
git add auth.js
git commit -m "fix typo"

Add-Content -Path auth.js -Value "function validateToken() {}"
git add auth.js
git commit -m "adds utility functions"

Set-Content -Path temp.txt -Value "temporary debug data"
git add temp.txt
git commit -m "add temp file for debug"
```

Kiểm tra lịch sử trước khi tái cấu trúc:

```powershell
git log --oneline
```

Kết quả:

```text
d84a920 (HEAD -> main) add temp file for debug
c5391fe adds utility functions
b17f682 fix typo
a420bd3 feat: khoi tao module auth
91c0e4a chore: khoi tao du an
```

Lịch sử hiện có ba commit nhỏ thuộc cùng một tính năng và một commit chứa tệp thử nghiệm không cần thiết.

## 2. Mở Interactive Rebase

Chạy Interactive Rebase đối với bốn commit gần nhất:

```powershell
git rebase -i HEAD~4
```

Nội dung ban đầu trong trình soạn thảo:

```text
pick a420bd3 feat: khoi tao module auth
pick b17f682 fix typo
pick c5391fe adds utility functions
pick d84a920 add temp file for debug
```

Thay đổi cấu hình thành:

```text
reword a420bd3 feat: khoi tao module auth
squash b17f682 fix typo
squash c5391fe adds utility functions
drop d84a920 add temp file for debug
```

Ý nghĩa của từng thao tác:

- `reword`: giữ nội dung commit đầu tiên nhưng cho phép đổi thông điệp.
- `squash`: gộp commit hiện tại vào commit đứng ngay phía trên.
- `drop`: loại bỏ hoàn toàn commit và thay đổi do commit đó tạo ra.

Lưu và đóng trình soạn thảo để Git bắt đầu xử lý danh sách.

## 3. Đặt lại thông điệp commit

Khi Git mở trình soạn thảo thông điệp, thay toàn bộ phần thông điệp cần giữ bằng:

```text
feat: hoan thien module authentication
```

Nếu trình soạn thảo xuất hiện thêm lần nữa trong lúc squash, tiếp tục giữ duy nhất thông điệp trên, sau đó lưu và đóng tệp.

Kết quả từ Git:

```text
[detached HEAD 7ed3c19] feat: hoan thien module authentication
 Date: Tue Oct 6 09:15:20 2026 +0700
 1 file changed, 3 insertions(+)
 create mode 100644 auth.js
Successfully rebased and updated refs/heads/main.
```

Mã hash của commit thay đổi sau Rebase vì nội dung lịch sử đã được viết lại.

## 4. Kiểm tra lịch sử sau Rebase

```powershell
git log --oneline
```

Kết quả:

```text
7ed3c19 (HEAD -> main) feat: hoan thien module authentication
91c0e4a chore: khoi tao du an
```

Ba commit của tính năng authentication đã được gộp thành một commit duy nhất. Commit nền của repository vẫn được giữ nguyên.

Kiểm tra người tạo và thông điệp của commit mới:

```powershell
git log -1 --pretty=format:"%h | %an | %s"
```

Kết quả:

```text
7ed3c19 | CrouAn244 | feat: hoan thien module authentication
```

Kiểm tra tệp rác:

```powershell
Test-Path temp.txt
```

Kết quả:

```text
False
```

Kiểm tra trạng thái repository:

```powershell
git status
```

Kết quả:

```text
On branch main
nothing to commit, working tree clean
```

Lịch sử cuối cùng chỉ còn một commit đại diện cho toàn bộ tính năng authentication; commit thử nghiệm và tệp `temp.txt` đã bị loại bỏ hoàn toàn.
