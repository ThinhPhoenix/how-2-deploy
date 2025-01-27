<img src="https://github.com/user-attachments/assets/cedfcf13-3634-4134-be70-430cb0904ee6" width="10%"><br/>
### 🚀 Hướng Dẫn Triển Khai Vite App Lên GitHub Pages  

GitHub Pages chỉ hỗ trợ **static web** (HTML, CSS, JS), vì vậy bạn có thể deploy các dự án **React, Vue, Svelte...** miễn là đã **build ra file tĩnh**.  

Dưới đây là cách deploy một **Vite project** lên GitHub Pages bằng **GitHub Actions**.  

---

### 🛠️ Bước 1: Cấu Hình `vite.config.ts`  
Cập nhật **base URL** sao cho trùng với **tên repository** trên GitHub.  

```ts
// vite.config.ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  base: "/tên-repo/", // ⚡ Đổi "tên-repo" thành tên repo của bạn
});
```

---

### ⚙️ Bước 2: Tạo GitHub Actions Workflow  
Tạo file `.github/workflows/deploy.yml` trong thư mục gốc của project.  

📌 **Nếu thư mục `.github` chưa tồn tại, hãy tự tạo mới.**  

Dán đoạn code sau vào `deploy.yml`:  

```yaml
name: Deploy Vite App to GitHub Pages

on:
  push:
    branches:
      - main # 🔄 Mỗi lần push lên main, workflow sẽ chạy

jobs:
  build:
    name: Build Project 🔨
    runs-on: ubuntu-latest

    steps:
      - name: 📥 Clone Repository
        uses: actions/checkout@v3

      - name: 📦 Setup Node.js
        uses: actions/setup-node@v3

      - name: 🛠️ Install Dependencies
        uses: bahmutov/npm-install@v1

      - name: 🏗️ Build Project
        run: npm run build

      - name: 📤 Upload Build Artifacts
        uses: actions/upload-artifact@v3
        with:
          name: production-files
          path: ./dist

  deploy:
    name: 🚀 Deploy to GitHub Pages
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'

    steps:
      - name: 📥 Download Build Artifacts
        uses: actions/download-artifact@v3
        with:
          name: production-files
          path: ./dist

      - name: 🌐 Deploy to gh-pages Branch
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

---

### ✅ Bước 3: Cấu Hình GitHub  
Sau khi commit và push lên GitHub, bạn cần thiết lập một số cài đặt:  

1. **Vào repo trên GitHub** → **Settings** → **Actions** → **General**  
2. **Tìm mục Workflow permissions** → Chọn **Read and Write** → **Save**  
3. **Vào tab Actions**, nếu job bị failed, nhấn **Re-run failed jobs**  
4. **Vào Settings** → **Pages** → **Chọn "Deploy from branch"** → **Branch: `gh-pages`** → **Save**  
---

### 🌍 Bước 4: Truy Cập Website  
Sau khi quá trình deploy hoàn tất, website của bạn sẽ có đường dẫn:  

```
https://<username_github>.github.io/<tên-repo>/
```

📌 Nếu thấy lỗi **404**, hãy thử thêm `index.html` vào đường dẫn:  

```
https://<username_github>.github.io/<tên-repo>/index.html
```

---

### 🎯 Lưu Ý  
- **Mỗi repository chỉ có thể deploy một trang web**  
- Nếu dùng **Custom Domain**, hãy thêm file `CNAME` vào thư mục `public/`  
- Nếu gặp lỗi **404 khi refresh trang**, cần thêm file `404.html` để hỗ trợ `SPA`  

Chúc bạn deploy thành công! 🚀
