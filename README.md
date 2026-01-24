# Tổng quan GitHub Actions: "Trợ lý ảo" miễn phí cho quy trình CI/CD


## 1. GitHub Actions là gì?

GitHub Actions là một nền tảng **CI/CD (Continuous Integration/Continuous Deployment)** được tích hợp sẵn trong GitHub.

Hiểu đơn giản: Nó là một "con robot" ngồi chờ. Hễ bạn làm một hành động gì đó (như push code), nó sẽ thức dậy và thực hiện một danh sách các công việc bạn đã giao (như chạy test, đóng gói build, deploy lên server).

---

## 2. Cấu trúc của github action

Để sử dụng GitHub Actions, bạn cần hiểu 5 thành phần cốt lõi này:

1.  **Workflow (Quy trình):** Là file cấu hình `.yml` chứa toàn bộ quy trình. Một repo có thể có nhiều workflow (ví dụ: 1 cái để test, 1 cái để deploy).
2.  **Events (Sự kiện):** sự kiện để kích hoạt workflow.
    * Ví dụ: `on: push` (khi push code), `on: pull_request` (khi tạo PR), `on: schedule` (hẹn giờ chạy).
3.  **Jobs (Công việc):** Một workflow gồm nhiều Jobs. Mặc định các Job chạy song song (nếu muốn chạy tuần tự, ví dụ Build xong mới Deploy, phải cấu hình `needs`).
4.  **Runners (Người chạy):** Là máy chủ (server) thực thi lệnh.
    * **GitHub-hosted:** GitHub cung cấp sẵn (Ubuntu, Windows, macOS).
    * **Self-hosted:** Bạn dùng máy chủ riêng của mình để chạy.
5.  **Steps & Actions:**
    * **Step:** Một dòng lệnh đơn lẻ (vd: `npm install`).
    * **Action:** Một đoạn script được đóng gói sẵn để tái sử dụng (vd: `actions/checkout` để lấy code về).

---

## 3. Thực hành: Workflow đầu tiên

Mọi workflow đều phải nằm trong thư mục `.github/workflows` của dự án.

Dưới đây là ví dụ file `ci.yml`. Workflow này sẽ tự động chạy test mỗi khi có code mới được đẩy lên nhánh `main`.

```yaml
# 1. Tên hiển thị trên giao diện GitHub
name: Node.js CI

# 2. Event: Chỉ chạy khi push vào nhánh main
on:
  push:
    branches: [ "main" ]

# 3. Định nghĩa các công việc
jobs:
  build-and-test:
    # 4. Runner: Mượn một máy Ubuntu mới tinh của GitHub
    runs-on: ubuntu-latest

    # 5. Các bước thực hiện
    steps:
      # Bước 1: Lấy code từ repo về máy Ubuntu này
      - name: Checkout code
        uses: actions/checkout@v4

      # Bước 2: Cài đặt Node.js version 20
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      # Bước 3: Cài library
      - name: Install dependencies
        run: npm install

      # Bước 4: Chạy test
      - name: Run Test
        run: npm test
```
---
## 4. Một số điều thú vị với GitHub Actions

Không chỉ dùng để CI/CD khô khan, cộng đồng Developer đã sáng tạo ra rất nhiều Actions thú vị để "làm đẹp" và biến Profile GitHub thành một CV xịn xò.

Dưới đây là các Actions phổ biến nhất:

### 1. 📊 GitHub Metrics
Hiển thị các chỉ số chi tiết về thói quen code của bạn: thời gian code trong ngày, ngôn ngữ sử dụng nhiều nhất, số dòng code đã xóa/thêm...
* **Repo:** `lowlighter/metrics`
* **Demo:** https://github.com/lowlighter/metrics/

### 2. 🐍 Snake Game (Rắn săn mồi)
Biến biểu đồ Contribution (mấy ô xanh xanh) của bạn thành bản đồ cho con rắn chạy ăn điểm. Nó sẽ tạo ra ảnh động (SVG/GIF) cập nhật mỗi ngày.
* **Repo:** `Platane/snk`
* **Demo:** https://github.com/platane/snk
    ![Snake animation](https://github.com/platane/snk/raw/output/github-contribution-grid-snake.svg)

### 3. 🏆 GitHub Trophies (Cúp thành tích)
Phân tích lịch sử hoạt động của bạn và trao các loại "Cúp" (Rank S, A, B...) giống như chơi game RPG (Multi-language, Long-time user, Stars...).
* **Repo:** `ryo-ma/github-profile-trophy`
* **Demo:** https://github.com/ryo-ma/github-profile-trophy

### 4. 🔐 Security: Quét lộ API Key/Password (Gitleaks)
Sợ nhất là lỡ tay commit file `.env` hoặc hardcode AWS Key lên GitHub. Action này sẽ quét toàn bộ code, nếu phát hiện chuỗi ký tự giống password/key, nó sẽ báo lỗi ngay lập tức.
* **Tên:** `gitleaks/gitleaks-action`
* **Ứng dụng:** Bảo vệ túi tiền và server của bạn trước khi quá muộn.

### 5. 📱 Notifications: Báo tin nhắn về Telegram/Discord/Slack
Thay vì phải F5 liên tục để xem build xong chưa, Action này sẽ gửi tin nhắn về điện thoại cho bạn ngay khi build xong (hoặc khi build lỗi).
* **Tên:** `slack-send-to-slack`
* **Link:** https://github.com/marketplace/actions/slack-send-to-slack
* **Ứng dụng:**
    > "Sếp ơi, code đã deploy xong, bot báo về điện thoại em rồi nè!"

---