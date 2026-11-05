# TOEIC Reading Mini-Test 2026 · Ms. Ngọc Huyền

31 bài Mini Test Reading (Part 5, Part 6, Part 7 Mini Test 01–10 + Part 7 Full Test), độ khó bám sát YBM 2026, xếp thành lộ trình 3 chặng.
Danh sách học viên **riêng**, không dính tới lớp TOEIC Online 500+.

- `index.html`: trang lộ trình. Học viên đăng nhập Google bằng Gmail cô đã kích hoạt.
- `mt/`: 31 bài Mini Test. Thêm `?thi=1` sau link để vào **chế độ thi thật** (bấm giờ, ẩn đáp án tới khi nộp, tự lưu điểm).
- `quan-ly.html`: cô thêm, khoá, gia hạn, xoá học viên và xem điểm thi thật. Chỉ tài khoản giáo viên của My English Workspace đăng nhập được.
- `mt.js`: lưu điểm thi thật lên Firebase (`mt26_progress`).

Nội dung bài được mã hoá: ai mở file trên GitHub cũng chỉ thấy chuỗi ký tự.

## Cài đặt một lần

### 1. Thêm Rules trên Firebase

Firebase Console → dự án **my-english-workspace-cf8d7** → Firestore Database → **Rules**.
Dán đoạn dưới vào **bên trong** khối `match /databases/{database}/documents { ... }` (cạnh phần của lớp 500+ và Bứt Phá), rồi bấm **Publish**.

```
    // ===== TOEIC Reading Mini-Test 2026 =====
    function mt26Teacher() {
      return request.auth != null
        && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'teacher';
    }
    function mt26Active() {
      return request.auth != null
        && exists(/databases/$(database)/documents/mt26_students/$(request.auth.token.email.lower()))
        && get(/databases/$(database)/documents/mt26_students/$(request.auth.token.email.lower())).data.locked != true
        && get(/databases/$(database)/documents/mt26_students/$(request.auth.token.email.lower())).data.until > request.time;
    }
    match /mt26_students/{email} {
      allow read: if mt26Teacher()
        || (request.auth != null && request.auth.token.email.lower() == email);
      allow write: if mt26Teacher();
    }
    match /mt26_secret/{doc} {
      allow read: if mt26Teacher() || mt26Active();
      allow write: if mt26Teacher();
    }
    match /mt26_progress/{email} {
      allow read: if mt26Teacher()
        || (request.auth != null && request.auth.token.email.lower() == email);
      allow create, update: if request.auth != null
        && request.auth.token.email.lower() == email && mt26Active();
      allow delete: if mt26Teacher();
    }
```

### 2. Cài mã giải mã

Mở `quan-ly.html` → đăng nhập tài khoản giáo viên → **Cài đặt** → **Nhập / đổi mã giải mã** → dán mã em Claude gửi → **Lưu mã**.
Mã không nằm trong repo này. Không chia sẻ mã cho ai.

### 3. Thêm học viên

Ở `quan-ly.html`, nhập Gmail và họ tên → **Thêm & kích hoạt**. Học viên mở link → **Đăng nhập bằng Google** → chọn đúng Gmail đó.
Muốn chặn một học viên, bấm **Khoá** (chậm nhất sau 20 phút em đó không vào được nữa).

### 4. Bật GitHub Pages

Repo → Settings → Pages → Source: **Deploy from a branch**, Branch: **main**, thư mục **/ (root)** → Save.

© Ms. Ngọc Huyền (Phạm Thị Ngọc Huyền). Tài liệu tự biên soạn, giữ bản quyền.
