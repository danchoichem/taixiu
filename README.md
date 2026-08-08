# Luyện Tiếng Trung — Next.js + Auth.js + Prisma

Scaffold ban đầu cho luyentiengtrung.com, viết lại bằng Next.js (App Router),
có sẵn đăng ký / đăng nhập bằng email-mật khẩu, lưu user trong Postgres qua Prisma.

## Cấu trúc chính

```
src/
  auth.ts                      # cấu hình Auth.js (NextAuth) + Credentials provider
  middleware.ts                # bảo vệ route /hoc/*
  lib/prisma.ts                # Prisma client singleton
  app/
    page.tsx                   # trang chủ
    dang-ky/page.tsx           # form đăng ký
    dang-nhap/page.tsx         # form đăng nhập
    hoc/page.tsx                # trang học (yêu cầu đăng nhập) — chỗ để bạn chuyển
                                 # nội dung cũ (TTS, sidebar, vocab...) vào
    api/dang-ky/route.ts       # API tạo user mới (hash mật khẩu bằng bcrypt)
    api/auth/[...nextauth]/route.ts  # API route của Auth.js
prisma/
  schema.prisma                # models: User, Account, Session, VocabProgress...
```

## 1. Setup database (Vercel Postgres)

1. Vào **Vercel Dashboard** → project của bạn → tab **Storage** → **Create Database** → chọn **Postgres** (Neon).
2. Sau khi tạo xong, vào tab **.env.local** của database đó, copy 2 biến:
   - `POSTGRES_PRISMA_URL`
   - `POSTGRES_URL_NON_POOLING`
3. Dán vào file `.env` ở local (copy từ `.env.example`).

## 2. Setup biến môi trường

```bash
cp .env.example .env
```

Tạo `AUTH_SECRET` bằng lệnh:

```bash
npx auth secret
```

Lệnh này tự động ghi giá trị vào `.env`.

## 3. Cài đặt & migrate database

```bash
npm install
npx prisma migrate dev --name init
```

Lệnh `prisma migrate dev` sẽ tạo các bảng `users`, `accounts`, `sessions`,
`vocab_progress`... trong database Postgres của bạn.

## 4. Chạy thử local

```bash
npm run dev
```

Mở http://localhost:3000 → thử **Đăng ký** → sẽ tự đăng nhập và chuyển vào `/hoc`.

## 5. Đẩy code lên GitHub

```bash
git init
git add .
git commit -m "Khoi tao site Next.js voi dang ky/dang nhap"
git branch -M main
git remote add origin <URL repo GitHub cua ban>
git push -u origin main
```

## 6. Deploy lên Vercel

1. Vào https://vercel.com/new → chọn **Import Git Repository** → chọn repo vừa push.
2. Ở phần **Environment Variables**, thêm đúng 3 biến trong `.env`:
   - `POSTGRES_PRISMA_URL`
   - `POSTGRES_URL_NON_POOLING`
   - `AUTH_SECRET`
   (Nếu database tạo trực tiếp trong Vercel Dashboard ở bước 1, 2 biến Postgres
   thường được Vercel tự thêm sẵn khi bạn link database vào project.)
3. Bấm **Deploy**.

Sau khi deploy xong, domain cũ `luyentiengtrung.com` có thể trỏ (add domain)
trực tiếp trong Vercel Dashboard → Settings → Domains.

## Việc cần làm tiếp theo

- [ ] Chuyển nội dung học (từ vựng, TTS, sidebar Chữ Hán...) từ site cũ vào `src/app/hoc/`
- [ ] Nối `VocabProgress` với giao diện học để lưu tiến độ theo từng user
- [ ] (Tuỳ chọn) Thêm đăng nhập bằng Google/GitHub OAuth trong `src/auth.ts`
- [ ] (Tuỳ chọn) Thêm trang "quên mật khẩu" (gửi email reset)
