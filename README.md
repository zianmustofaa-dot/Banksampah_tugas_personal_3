# Bank Sampah Digital

Tugas personal 3 — sistem informasi bank sampah berbasis web.

| Bagian | Lokasi |
|---|---|
| Aplikasi Next.js | [`bank-sampah-web/`](bank-sampah-web/) |
| Laporan & aset tugas | [`laporan-tugas3/`](laporan-tugas3/) |
| Skrip SQL dokumentasi | `01_ddl.sql` … `05_upgrade_prod.sql` |
| PDF laporan | `Laporan_BankSampah_*.pdf` |

## Menjalankan lokal

```bash
cd bank-sampah-web
npm install
cp .env.example .env.local   # isi DATABASE_URL
npm run db:init              # sekali saja: buat tabel + seed
npm run dev
```

Buka http://localhost:3000.

## Deploy ke Vercel

1. Import repo ini di [vercel.com/new](https://vercel.com/new).
2. **Wajib:** pada **Root Directory**, pilih `bank-sampah-web`
   (Project Settings → General → Root Directory jika project sudah terlanjur dibuat).
3. Tambahkan Environment Variable `DATABASE_URL` (connection string Neon/Postgres,
   gunakan host pooler + `sslmode=require`).
4. Deploy. Setelah itu jalankan `npm run db:init` sekali dari lokal
   (lihat [`bank-sampah-web/README.md`](bank-sampah-web/README.md)).
5. Cek `https://<domain-vercel>/api/health` → harus `status: ok`.

Detail lengkap + troubleshooting: [`bank-sampah-web/README.md`](bank-sampah-web/README.md).

## Push ke GitHub

Repo ini sudah disiapkan untuk remote:

```bash
git remote set-url origin https://github.com/zianmustofaa-dot/Banksampah_tugas_personal_3.git
git branch -M main
git push -u origin main
```

> File `.env.local`, `.vercel/`, `node_modules/`, dan `.next/` tidak ikut ter-commit
> karena sudah masuk `.gitignore`. Jangan pernah commit kredensial database.
