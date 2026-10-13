# Bài 5: Chẩn đoán và khắc phục 3 sự cố Pipeline CI/CD

## 1. Lỗi quyền thực thi Gradle Wrapper

**Triệu chứng:** GitHub Actions báo `./gradlew: Permission denied`, exit code 126.

**Nguyên nhân:** Script `gradlew` chưa có quyền thực thi trên runner Linux.

**Cách khắc phục:**

```bash
chmod +x gradlew
./gradlew test
```

**Kết quả:** Gradle test chạy thành công.

## 2. Lỗi xác thực Docker Registry

**Triệu chứng:** Bước `docker/login-action@v3` báo `Password required`.

**Nguyên nhân:** Tên Secret trong workflow bị viết sai, hoặc repository chưa được cấu hình Secret tương ứng.

**Cách khắc phục:**

```yaml
password: ${{ secrets.REGISTRY_TOKEN }}
```

Tạo repository Secret `REGISTRY_TOKEN` chứa token có quyền đẩy package lên GitHub Container Registry.

**Kết quả:** Đăng nhập Registry thành công.

## 3. Lỗi cấu hình Docker build

**Triệu chứng:** Build Docker thất bại do tag image chứa chữ hoa và đường dẫn Dockerfile/build context không đúng.

**Nguyên nhân:** Tên image Docker phải viết thường; Dockerfile của dự án nằm ở thư mục gốc.

**Cách khắc phục:**

```yaml
context: .
file: ./Dockerfile
tags: ghcr.io/thanhit2006/bt5:latest
```

Tạo JAR trước khi build image:

```bash
./gradlew bootJar
```

**Kết quả:** Docker image được build và đẩy lên GitHub Container Registry thành công.
