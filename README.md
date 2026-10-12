# Báo cáo kết quả Bài 5: Nối Job tự động Build & Push Docker Image sau khi Test Gradle xanh

## 1. Mục tiêu bài toán

- Tách luồng CI thành 2 Job riêng biệt: Job `test` (chạy Gradle test & đóng gói JAR) và Job `docker-build` (đóng gói Docker Image).
- Sử dụng từ khóa `needs: test` để thiết lập cơ chế phụ thuộc, đảm bảo job `docker-build` chỉ chạy khi job `test` hoàn tất thành công[cite: 1].

## 2. Mô phỏng Sơ đồ Workflow (Tab Actions)

### Trường hợp 1: Test Gradle thành công (Pipeline chạy toàn bộ)

- Biểu đồ hiển thị mũi tên nối tuần tự: `test` ➔ `docker-build`[cite: 1].
- Cả hai Job đều hoàn tất với trạng thái **Success** (màu xanh).

### Trường hợp 2: Test Gradle bị lỗi (Lỗi kiểm thử)

- Job `test` thất bại (Failed)[cite: 1].
- Job `docker-build` tự động chuyển sang trạng thái **Skipped** (Bị bỏ qua), ngăn chặn việc đẩy Docker Image lỗi lên Registry[cite: 1].

## 3. Nhật ký Log thực thi mẫu

```text
==================================================
Job 1: Run Gradle Tests & Package (test)
==================================================
- Checkout repository successfully
- Setup Java JDK 17 with Temurin distribution
- Executing Gradle Wrapper: ./gradlew build
- BUILD SUCCESSFUL in 12s
➔ Job status: SUCCESS

==================================================
Job 2: Build and Push Docker Image (docker-build)
==================================================
- Triggered by needs: test condition
- Logging in to GitHub Container Registry (ghcr.io)...
- Login Succeeded
- Building and pushing image: ghcr.io/username/repository:latest
- Successfully pushed image to Registry
➔ Job status: SUCCESS
```

## 4. Kết luận

Cấu hình thành công cơ chế tuần tự giữa các Job trong GitHub Actions, giúp kiểm soát chất lượng mã nguồn tự động trước khi tiến hành đóng gói và phân phối Container Image[cite: 1].
