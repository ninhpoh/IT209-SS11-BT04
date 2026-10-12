# Bài 4: Khai báo GitHub Secrets và đăng nhập GitHub Container Registry (GHCR)

## Mục tiêu
- Lưu Personal Access Token an toàn bằng Repository Secret `REGISTRY_TOKEN`.
- Tự động đăng nhập GHCR trong workflow bằng `docker/login-action@v3`.

## Các bước thực hiện

### 1. Chuẩn bị repo
Dùng repo GitHub dùng để nộp bài (chưa có thì tạo: GitHub -> **+** -> New repository), đẩy code lên nhánh `main`.

### 2. Tạo Personal Access Token (classic)
GitHub -> Settings -> Developer settings -> Personal access tokens -> Tokens (classic) -> Generate new token (classic).
- Tick quyền `write:packages`
- Generate và copy token `ghp_...` ngay (chỉ hiện một lần)

### 3. Tạo Repository Secret
Repo -> Settings -> Secrets and variables -> Actions -> New repository secret.
- Name: `REGISTRY_TOKEN`
- Secret: dán token
- Add secret

### 4. Khai báo bước đăng nhập trong `.github/workflows/ci.yml`
```yaml
- name: Đăng nhập vào GitHub Container Registry
  uses: docker/login-action@v3
  with:
    registry: ghcr.io
    username: ${{ github.actor }}
    password: ${{ secrets.REGISTRY_TOKEN }}
```

### 5. Push code và kiểm tra
```bash
git add .
git commit -m "ci: login to ghcr"
git push
```
Repo -> tab Actions -> mở run mới nhất -> mở bước "Đăng nhập vào GitHub Container Registry" -> xác nhận có dòng `Login Succeeded!`.

## Lỗi thường gặp
| Lỗi | Cách xử lý |
|---|---|
| `Password required` | Tên secret sai, phải đúng `REGISTRY_TOKEN` (phân biệt hoa thường) |
| `unauthorized` | Token thiếu `write:packages` hoặc đã hết hạn, tạo lại và Update secret |
| Actions không chạy | `ci.yml` không nằm ở `.github/workflows/` ở gốc repo, hoặc tên nhánh không khớp `on.push.branches` |

## Lưu ý bảo mật
Không commit token vào code. Chỉ lưu trong GitHub Secrets.