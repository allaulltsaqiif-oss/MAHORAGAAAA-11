# 🏫 PantauSiswa

> **"Pantau Perkembangan & Kepulangan Anak — Cukup Satu Aplikasi."**

PantauSiswa adalah aplikasi web kolaboratif yang menghubungkan guru dan orang tua untuk memantau perkembangan belajar (4C) dan keamanan kepulangan siswa secara real-time. Dirancang khusus untuk sekolah Indonesia dengan kondisi internet tidak stabil dan guru yang sibuk.

---

## 🎯 Mengapa PantauSiswa?

| Masalah Nyata | Solusi |
|---|---|
| Guru kewalahan pantau 40 siswa sekaligus | Input 4C cepat — hanya ubah yang berubah dari minggu lalu |
| Siswa tertinggal tidak terdeteksi dini | Deteksi otomatis: 2× merah di kompetensi yang sama = peringatan |
| Orang tua cemas anak tidak langsung pulang | Notifikasi real-time saat guru catat kepulangan |
| Internet sekolah tidak stabil | Offline-first: data tersimpan lokal, sinkron otomatis saat online |
| Laporan perkembangan jarang & tidak terstruktur | Laporan mingguan otomatis, bisa kirim via WhatsApp |

---

## 🚀 Fitur Utama

### Untuk Guru
- **Dashboard Prioritas** — Langsung tahu siapa yang butuh perhatian hari ini
- **Input 4C Cerdas** — Tampilkan nilai minggu lalu sebagai acuan; guru hanya ubah yang berubah
- **Catat Kepulangan Cepat** — Centang per siswa atau "Semua Pulang" dalam satu klik
- **Mode Hari Libur** — Tandai libur/agenda khusus agar orang tua tidak menunggu notifikasi
- **Laporan Otomatis** — Generate teks laporan mingguan, kirim via WhatsApp atau salin

### Untuk Orang Tua
- **Dashboard Sederhana** — Satu layar, satu informasi: sudah/belum pulang + jam
- **Notifikasi Real-Time** — Push notification browser saat anak dicatat pulang
- **Konfirmasi Tiba di Rumah** *(opsional)* — Orang tua bisa konfirmasi anak sudah sampai rumah
- **Riwayat & Laporan** — Kepulangan 7 hari terakhir + laporan perkembangan 4C

### Keamanan & Privasi
- **Kode orang tua 8 karakter alfanumerik** acak (contoh: `X7K2-9MPQ`) — tidak bisa ditebak
- **Rate limiting login** — Maksimal 5 percobaan gagal per IP per jam
- **Row-Level Security** — Setiap pengguna hanya bisa melihat data miliknya sendiri
- **Data anak tidak pernah dijual** ke pihak ketiga

---

## 🏗️ Tech Stack

| Layer | Teknologi | Alasan |
|---|---|---|
| Frontend | Next.js 14 + TypeScript | App Router, Server Actions, deploy gratis di Vercel |
| Database | Supabase (PostgreSQL) | RLS built-in, Realtime WebSocket, free tier cukup untuk MVP |
| Auth | Supabase Auth | JWT, session management, aman |
| Offline | PWA + IndexedDB (idb) | Background sync untuk sekolah dengan internet tidak stabil |
| Styling | Tailwind CSS | Cepat, responsif, mobile-first |
| Validasi | Zod + React Hook Form | Type-safe, UX form yang baik |
| Notifikasi | Web Push API + Supabase Realtime | Real-time tanpa server tambahan |

---

## 📁 Struktur Proyek

```
pantau-siswa/
├── app/
│   ├── (auth)/
│   │   ├── login/
│   │   └── register/
│   ├── (guru)/
│   │   ├── page.tsx              # Dashboard guru
│   │   ├── perkembangan/         # Input 4C mingguan
│   │   ├── kepulangan/           # Catat kepulangan harian
│   │   ├── kalender/             # Atur hari libur / agenda
│   │   └── laporan/              # Generate & kirim laporan
│   ├── (ortu)/
│   │   ├── page.tsx              # Dashboard orang tua (1 layar utama)
│   │   ├── riwayat/              # Riwayat kepulangan
│   │   └── laporan/              # Laporan perkembangan 4C
│   ├── api/
│   │   ├── auth/
│   │   └── laporan/
│   └── layout.tsx
├── components/
│   ├── guru/
│   ├── ortu/
│   └── shared/
├── hooks/
│   ├── useRealtimeKepulangan.ts
│   ├── useOfflineSync.ts
│   └── useNotifikasi.ts
├── lib/
│   ├── supabase/
│   ├── detection.ts              # Logika deteksi siswa tertinggal
│   └── laporan.ts                # Generator teks laporan
├── public/
│   ├── manifest.json             # PWA manifest
│   └── sw.js                     # Service Worker
├── supabase/
│   └── migrations/
├── .env.local
└── package.json
```

---

## ⚙️ Cara Instalasi & Menjalankan

### Prasyarat
- Node.js 18+
- Akun [Supabase](https://supabase.com) (gratis)
- Akun [Vercel](https://vercel.com) (gratis, untuk deploy)

### 1. Clone repositori

```bash
git clone https://github.com/username/pantau-siswa.git
cd pantau-siswa
npm install
```

### 2. Setup Supabase

1. Buat project baru di [supabase.com](https://supabase.com)
2. Buka **SQL Editor**, jalankan seluruh migration dari folder `supabase/migrations/` secara berurutan
3. Aktifkan **Realtime** untuk tabel `kepulangan` di menu Database → Replication

### 3. Konfigurasi environment

Salin file contoh dan isi dengan nilai dari Supabase:

```bash
cp .env.local.example .env.local
```

```env
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
NEXT_PUBLIC_APP_NAME=PantauSiswa
NEXT_PUBLIC_APP_VERSION=1.0.0
```

### 4. Jalankan secara lokal

```bash
npm run dev
```

Buka [http://localhost:3000](http://localhost:3000)

### 5. Deploy ke Vercel

```bash
npx vercel --prod
```

Atau hubungkan repositori GitHub ke Vercel untuk auto-deploy setiap push ke `main`.

---

## 👥 Alur Pengguna

### Guru
```
Login → Dashboard (lihat prioritas hari ini)
  → [Harian] Catat Kepulangan → centang siswa → notifikasi terkirim
  → [Mingguan] Input Perkembangan 4C → nilai minggu lalu tampil sebagai acuan
  → [Otomatis] Sistem deteksi siswa 2× merah → peringatan + rekomendasi
  → [Mingguan] Generate Laporan → salin / kirim via WhatsApp
```

### Orang Tua
```
Login dengan kode anak → Dashboard (status pulang hari ini)
  → Terima notifikasi browser saat anak dicatat pulang
  → [Opsional] Konfirmasi "Anak sudah tiba di rumah"
  → Lihat riwayat kepulangan & laporan perkembangan
```

---

## 🔐 Keamanan Data

- **RLS (Row-Level Security)** aktif di semua tabel — data terisolasi per pengguna
- **Kode orang tua**: 8 karakter alfanumerik acak, tidak bisa ditebak
- **Rate limiting**: Maksimal 5 percobaan login gagal per IP per jam
- **Service role key** tidak pernah diekspose ke frontend
- **Data siswa** tidak dijual atau dibagikan ke pihak ketiga (lihat [Kebijakan Privasi](#))

---

## 📶 Dukungan Offline

PantauSiswa adalah **Progressive Web App (PWA)** dengan dukungan offline penuh:

- Data kepulangan & perkembangan tersimpan di **IndexedDB** saat offline
- **Background Sync** otomatis mengirim data ke server saat koneksi kembali
- Guru mendapat notifikasi: *"Anda sedang offline. Data akan tersinkron otomatis."*
- Orang tua tetap bisa melihat riwayat kepulangan dari cache lokal

---

## 🧪 Testing

```bash
# Unit tests
npm run test

# Integration tests (Playwright)
npm run test:e2e

# Type checking
npm run type-check
```

---

## 📦 Dependencies Utama

```json
{
  "next": "14.2.4",
  "react": "^18.3.1",
  "@supabase/supabase-js": "^2.44.1",
  "@supabase/ssr": "^0.3.0",
  "tailwindcss": "^3.4.4",
  "zod": "^3.23.8",
  "react-hook-form": "^7.52.0",
  "idb": "^8.0.0",
  "workbox-window": "^7.1.0",
  "papaparse": "^5.4.1",
  "date-fns": "^3.6.0",
  "lucide-react": "^0.394.0"
}
```

---

## 🗺️ Roadmap

### MVP (Minggu 1-4)
- [x] Autentikasi guru & orang tua
- [x] Manajemen siswa (CRUD + impor CSV)
- [x] Input perkembangan 4C dengan referensi minggu lalu
- [x] Catat kepulangan + notifikasi real-time
- [x] Dashboard guru dengan deteksi siswa tertinggal
- [x] Dashboard orang tua (satu layar sederhana)
- [x] Mode hari libur / agenda sekolah
- [x] Offline-first + background sync
- [x] Laporan mingguan otomatis (teks + WhatsApp)

### Fase 2 (Bulan 1-3)
- [ ] Dashboard Kepala Sekolah
- [ ] Ekspor laporan ke PDF
- [ ] Grafik perkembangan per siswa
- [ ] Import dari Excel (XLSX)
- [ ] Dark mode

### Fase 3 (Bulan 4-12)
- [ ] Integrasi WhatsApp Business API (kirim otomatis)
- [ ] Fitur chat guru ↔ orang tua
- [ ] Multi-bahasa (Indonesia, Inggris)
- [ ] Export ke Dapodik (Kemendikbud)

---

## 🤝 Kontribusi

Pull request dan issue sangat disambut! Untuk perubahan besar, buka issue terlebih dahulu untuk mendiskusikan apa yang ingin diubah.

---

## 📄 Lisensi

MIT License — lihat [LICENSE](LICENSE) untuk detail.

---

## 📬 Kontak

Tim PantauSiswa — [email@pantaususwa.id](mailto:email@pantaususwa.id)

---

> *PantauSiswa dibangun dengan semangat membantu guru Indonesia yang berdedikasi, dan memberi ketenangan bagi orang tua yang peduli.*
