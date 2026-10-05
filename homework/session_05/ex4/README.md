
## 1. Mục tiêu

Mô phỏng quy trình xử lý một lỗi nghiêm trọng phát hiện trên môi trường Production bằng Gitflow.

Kịch bản:

* `main` đang ở phiên bản ổn định `v1.0.0`.
* `develop` chứa các chức năng đang phát triển cho phiên bản tiếp theo.
* Phát hiện lỗi nghiêm trọng liên quan đến việc lộ dữ liệu người dùng trên Production.
* Tạo nhánh Hotfix trực tiếp từ `main`.
* Sửa lỗi và phát hành phiên bản `v1.0.1`.
* Đồng bộ bản sửa lỗi trở lại `develop`.

---

## 2. Cấu trúc các nhánh

### `main`

Nhánh chứa phiên bản ổn định đang được sử dụng trên Production.

Phiên bản ban đầu:

```text
v1.0.0
```

Sau khi xử lý lỗi:

```text
v1.0.1
```

### `develop`

Nhánh phát triển các chức năng mới cho phiên bản tiếp theo.

Trong bài này, `develop` có thêm chức năng:

```text
feat: add customer notification feature
```

### `hotfix/v1.0.1`

Nhánh được tạo trực tiếp từ `main` để xử lý lỗi nghiêm trọng trên Production.

Commit sửa lỗi:

```text
fix: patch critical user data exposure
```

---

## 3. Quy trình thực hiện

### Bước 1: Trạng thái ban đầu

Repository có hai nhánh chính:

```text
main
develop
```

`main` đang ở phiên bản:

```text
v1.0.0
```

---

### Bước 2: Phát triển chức năng mới trên `develop`

Tạo chức năng mới trên nhánh `develop`:

```text
feat: add customer notification feature
```

Chức năng này chưa được đưa vào Production.

---

### Bước 3: Phát hiện lỗi nghiêm trọng trên Production

Do lỗi xảy ra trên phiên bản Production, tạo nhánh Hotfix trực tiếp từ `main`:

```bash
git checkout main
git checkout -b hotfix/v1.0.1
```

---

### Bước 4: Sửa lỗi bảo mật

Thực hiện sửa lỗi liên quan đến việc lộ dữ liệu người dùng:

```text
fix: patch critical user data exposure
```

Sau đó push nhánh Hotfix lên GitHub.

---

### Bước 5: Merge Hotfix vào `main`

Sau khi kiểm tra bản sửa lỗi, merge Hotfix vào `main`:

```bash
git checkout main
git merge --no-ff hotfix/v1.0.1 -m "merge: hotfix v1.0.1 into main"
```

Kết quả tạo merge commit:

```text
8d64961 merge: hotfix v1.0.1 into main
```

---

### Bước 6: Tạo phiên bản `v1.0.1`

Tạo tag cho phiên bản phát hành:

```bash
git tag -a v1.0.1 -m "Release Hotfix 1.0.1"
```

Tag `v1.0.1` trỏ tới commit:

```text
8d64961
```

Sau đó push tag lên GitHub:

```bash
git push origin v1.0.1
```

---

### Bước 7: Merge Hotfix trở lại `develop`

Để đảm bảo lỗi đã được sửa trên Production cũng được áp dụng cho phiên bản đang phát triển, merge Hotfix vào `develop`:

```bash
git checkout develop
git merge --no-ff hotfix/v1.0.1 -m "merge: hotfix v1.0.1 into develop"
```

Kết quả tạo commit:

```text
8ee4ff7 merge: hotfix v1.0.1 into develop
```

---

## 4. Sơ đồ Gitflow

```text
                         hotfix/v1.0.1
                              |
                              | fix: patch critical user data exposure
                              v
v1.0.0 --------------------> FIX
  |                           |
  |                           | merge
  |                           v
  |                       main v1.0.1
  |                           |
  |                      tag: v1.0.1
  |
  +----> develop
            |
            +---- feat: add customer notification feature
            |
            +---- merge hotfix v1.0.1
```

---

## 5. Commit Graph thực tế

Kết quả kiểm tra bằng:

```bash
git log --graph --oneline --decorate --all
```

```text
*   8ee4ff7 (HEAD -> develop, origin/develop) merge: hotfix v1.0.1 into develop
|\
* | 1acba92 feat: add customer notification feature
| | * 8d64961 (tag: v1.0.1, origin/main, main) merge: hotfix v1.0.1 into main
| |/|
|/|/
| * f7d6f66 (origin/hotfix/v1.0.1, hotfix/v1.0.1)
| | fix: patch critical user data exposure
|/
* 22acb0b (tag: v1.0.0) chore: initial project setup
```

---

## 6. Kết quả kiểm tra

### Danh sách branch

```bash
git branch -a
```

Kết quả:

```text
develop
hotfix/v1.0.1
main
remotes/origin/develop
remotes/origin/hotfix/v1.0.1
remotes/origin/main
```

![Danh sách branch](images/01-branches.png)

---

### Danh sách tag

```bash
git tag
```

Kết quả:

```text
v1.0.0
v1.0.1
```

![Danh sách tag](images/02-tags.png)

---

### Commit graph

```bash
git log --graph --oneline --decorate --all
```

![Git commit graph](images/03-git-graph.png)

---

### Repository trên GitHub

![GitHub repository](images/04-github.png)

---

## 7. Kết luận

Bài thực hành đã mô phỏng thành công quy trình xử lý Hotfix trong Gitflow.

Kết quả đạt được:

* Tạo `hotfix/v1.0.1` trực tiếp từ `main`.
* Sửa lỗi nghiêm trọng liên quan đến việc lộ dữ liệu người dùng.
* Merge Hotfix vào `main`.
* Tạo tag phát hành `v1.0.1`.
* Push phiên bản mới lên GitHub.
* Merge Hotfix trở lại `develop`.
* Đảm bảo bản sửa lỗi được đồng bộ giữa Production và nhánh phát triển.
* Kiểm tra và lưu lại Git branch, tag và commit graph để phục vụ việc đánh giá bài thực hành.
