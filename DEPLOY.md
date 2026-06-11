# Deploy lên Vercel

App này là 1 file HTML tĩnh (`index.html` ~120KB, ~3270 dòng, không cần build step). Deploy lên Vercel cực nhanh — không cần Vercel CLI, không cần framework config.

## Chuẩn bị (đã xong)

✅ Local git repo initialized tại `D:/CLAUDE/POKER/POKER/` (branch `main`)
✅ `.gitignore` loại `.claude/`, `.build/`, OS junk
✅ Initial commit: 3 files, 3909 lines

Còn lại bạn tự làm: tạo GitHub repo + kết nối Vercel.

---

## Phương án 1: GitHub + Vercel Web UI (khuyến nghị — auto deploy)

**Bước 1. Tạo GitHub repo**

1. Mở https://github.com/new
2. Điền:
   - **Repository name**: `poker-equity` (hoặc tên bạn thích)
   - **Description**: "Poker equity calculator — Neon Cosmic UI, tiếng Việt"
   - **Visibility**: Public hoặc Private đều OK
   - **KHÔNG** check "Add a README file", "Add .gitignore", "Choose a license" (vì local repo đã có)
3. Click **Create repository**
4. GitHub sẽ hiện trang hướng dẫn push. BỎ QUA — làm theo bước 2.

**Bước 2. Push local repo lên GitHub**

GitHub sẽ cho bạn URL dạng `https://github.com/<username>/poker-equity.git`. Chạy trong PowerShell/Git Bash tại `D:/CLAUDE/POKER/POKER`:

```bash
git remote add origin https://github.com/<username>/poker-equity.git
git branch -M main
git push -u origin main
```

> Nếu dùng SSH: thay URL thành `git@github.com:<username>/poker-equity.git`
> Nếu 2FA: GitHub sẽ hỏi password — dùng Personal Access Token thay password (Settings → Developer settings → Personal access tokens → Generate new token, scope `repo`).

**Bước 3. Kết nối Vercel với GitHub**

1. Mở https://vercel.com → **Sign Up** (hoặc Login nếu đã có)
2. Chọn **Continue with GitHub** → authorize Vercel truy cập GitHub
3. Sau khi vào dashboard, click **Add New… → Project**
4. Tìm repo `poker-equity` trong danh sách "Import Git Repository"
5. Click **Import**

**Bước 4. Cấu hình project (rất đơn giản)**

Vercel tự detect project type. App này là static HTML, không cần framework:

| Field | Value |
|---|---|
| **Project Name** | `poker-equity` (auto-fill từ repo) |
| **Framework Preset** | **Other** ← QUAN TRỌNG (KHÔNG chọn Vite/Next.js gì hết) |
| **Root Directory** | `./` (mặc định) |
| **Build Command** | (để trống) |
| **Output Directory** | (để trống — sẽ dùng root) |
| **Install Command** | (để trống) |

6. Click **Deploy**
7. Đợi 30-60s. Vercel sẽ show "🎉 Your project has been successfully deployed"
8. Click vào screenshot preview → bạn sẽ thấy app chạy tại URL dạng `https://poker-equity-<hash>.vercel.app`

**Bước 5. (Optional) Custom domain**

Trong Vercel project → **Settings → Domains** → thêm domain bạn sở hữu. Vercel tự cấp SSL.

**Bước 6. Auto-deploy setup**

✅ Đã setup sẵn! Mỗi lần bạn push code lên GitHub:
```bash
git add . && git commit -m "msg" && git push
```
Vercel tự detect, build, deploy trong ~30s. URL production không đổi. Check tab **Deployments** để xem history.

---

## Phương án 2: Vercel CLI (nhanh hơn, cần terminal)

**Bước 1. Cài Vercel CLI**

```bash
npm install -g vercel
```

**Bước 2. Login + deploy**

Trong `D:/CLAUDE/POKER/POKER`:
```bash
vercel login        # Mở browser, xác thực
vercel              # First deploy — hỏi vài câu:
                    #   - Set up and deploy? Y
                    #   - Which scope? <your account>
                    #   - Link to existing project? N
                    #   - Project name? poker-equity
                    #   - In which directory is your code located? ./
                    #   - Override settings? N
```

Vercel tạo project + deploy. URL preview: `https://poker-equity-<hash>-<user>.vercel.app`

**Bước 3. Production deploy**

```bash
vercel --prod       # Promote lên production
```

**Bước 4. (Optional) Kết nối GitHub để auto-deploy**

Sau khi deploy bằng CLI, vào https://vercel.com/dashboard → project → **Settings → Git** → **Connect Git Repository** → chọn GitHub repo. Từ đó về sau mỗi push tự deploy.

---

## Phương án 3: Drag & Drop (nhanh nhất, không cần GitHub)

1. Vào https://vercel.com/new
2. **KHÔNG** click "Import Git Repository"
3. Kéo thả folder `D:/CLAUDE/POKER/POKER/` vào ô "Drag and drop your folder"
4. Vercel upload + deploy trong 10s
5. URL: `https://poker-equity-<hash>.vercel.app`

⚠️ **Nhược điểm**: mỗi lần update phải upload lại. Không có version control.

---

## Kiểm tra sau deploy

Sau khi có URL production, test:

1. ✅ App load, hiện "Poker Equity — Máy Tính Texas Hold'em"
2. ✅ Chọn 2 lá bài → equity donut render
3. ✅ Chọn 3+ board cards → Outs analysis + Best hand hiện
4. ✅ Đổi vị trí (BTN/UTG/BB) → recommendation thay đổi
5. ✅ Reload page → state restored (localStorage)
6. ✅ Mobile: test swipe gesture, haptic (nếu có device)

## Lưu ý kỹ thuật

- App dùng **localStorage** cho state persistence. Nếu dùng incognito hoặc clear data → state sẽ reset.
- **HTTPS bắt buộc** cho clipboard API (`navigator.clipboard.writeText`) — Vercel tự cấp SSL.
- **Service Worker / PWA**: chưa có. Có thể thêm sau nếu muốn offline.
- **Environment variables**: app không dùng. Không cần config.

## Troubleshooting

**Build failed: "No framework detected"**
- Đảm bảo Framework Preset = **Other**, KHÔNG chọn framework nào.

**Push rejected: "Permission denied"**
- Dùng HTTPS URL + Personal Access Token (không phải password).
- Hoặc setup SSH key.

**App load nhưng JS không chạy**
- Check browser console (F12). Nếu có CSP error → Vercel cần custom headers. Tạo `vercel.json`:

```json
{
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        { "key": "Content-Security-Policy", "value": "default-src 'self' 'unsafe-inline' 'unsafe-eval' data: blob:" }
      ]
    }
  ]
}
```

**Custom domain không hoạt động**
- DNS propagation mất 24-48h. Check tại https://dnschecker.org

---

## Files đã chuẩn bị

- `D:/CLAUDE/POKER/POKER/index.html` — App (3,200+ dòng, self-contained)
- `D:/CLAUDE/POKER/POKER/HANDOFF.md` — Documentation (cho AI/dev tiếp theo)
- `D:/CLAUDE/POKER/POKER/.gitignore` — Loại `.claude/`, `.build/`
- Git: initialized, branch `main`, 1 commit

## Cấu trúc sau deploy

```
D:/CLAUDE/POKER/POKER/
├── .git/              ← Local git history
├── .gitignore
├── HANDOFF.md         ← Project docs
└── index.html         ← Single-file app (Vercel serves as-is)
```

Sau khi push lên GitHub + connect Vercel, mọi thay đổi chỉ cần `git push` — Vercel tự build + deploy.

---

## Liên hệ / Next steps

Sau khi deploy, nếu muốn:
- Custom domain → Settings → Domains
- Analytics → Settings → Analytics (free tier có 100K events/tháng)
- Password protect → Settings → Security → Password Protection
- PWA / offline support → cần thêm `manifest.json` + service worker
- Branch previews → tự động có sẵn (mỗi PR tạo 1 preview URL)
