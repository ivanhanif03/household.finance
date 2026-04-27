# 🏠 Household Finance

PWA (Progressive Web App) untuk pencatatan keuangan rumah tangga. Gratis, open source, dan bisa di-deploy sendiri di GitHub Pages tanpa biaya server.

![Preview](assets/preview.png)

## ✨ Fitur

- 💰 Catat pendapatan & pengeluaran
- 🔄 Transfer/pindah dana antar dompet
- 📊 Budget bulanan dengan peringatan otomatis
- 🔔 Push notification (via OneSignal, opsional)
- 📱 PWA — bisa di-install di HP seperti aplikasi native
- 📤 Export data ke CSV
- ⚙️ Konfigurasi kepemilikan & dompet yang fleksibel
- 🔒 Password login (validasi di server)
- ⚡ Cache-first loading (instan meski sinyal lemah)

## 🗂️ Struktur File

```
household-finance/
├── index.html              # Halaman utama
├── app.js                  # Logic aplikasi
├── config.js               # Konfigurasi default
├── style.css               # Tampilan
├── manifest.json           # PWA manifest
├── sw.js                   # Service Worker
├── OneSignalSDKWorker.js   # Push notification worker
├── Code.gs                 # Google Apps Script (backend)
└── assets/
    └── icons/
        ├── icon-192.png
        └── icon-512.png
```

---

## 🚀 Tutorial Deploy

### Prasyarat
- Akun Google (untuk Google Sheets + Apps Script)
- Akun GitHub (untuk hosting via GitHub Pages)
- Akun OneSignal (opsional, untuk push notification background)

---

### Langkah 1 — Setup Google Sheets

1. Buka [Google Sheets](https://sheets.google.com) → buat spreadsheet baru
2. Beri nama spreadsheet, misalnya **"Household Finance"**
3. Buat **2 sheet** dengan nama persis seperti ini:
   - `Transaksi`
   - `Budget`

4. Di sheet **Transaksi**, buat header di baris 1:
   ```
   A: Tanggal | B: Jenis | C: Kategori | D: Deskripsi | E: Nominal | F: Dompet | G: Detail | H: Kepemilikan
   ```

5. Di sheet **Budget**, buat header di baris 1:
   ```
   A: PeriodeKey | B: PeriodeLabel | C: Budget | D: Realisasi
   ```

---

### Langkah 2 — Setup Google Apps Script (GAS)

1. Di Google Sheets, klik menu **Extensions → Apps Script**
2. Hapus semua kode yang ada
3. Copy semua isi file `Code.gs` dari repo ini → paste ke editor GAS
4. Klik **Save** (ikon 💾)

#### Set Password

Ada 2 cara set password:

**Cara A — Script Properties (lebih aman, direkomendasikan):**
1. Di GAS → klik ikon ⚙️ **Project Settings** (sidebar kiri)
2. Scroll ke bawah → **Script Properties** → klik **Add script property**
3. Tambahkan:
   ```
   Key: APP_PASSWORD
   Value: password_kamu_disini
   ```
4. Klik **Save script properties**

**Cara B — Edit langsung di Code.gs:**
```javascript
function getPassword() {
  return "password_kamu_disini"; // ganti ini
}
```

#### Setup OneSignal di GAS (jika pakai notifikasi)

Di **Script Properties**, tambahkan:
```
ONESIGNAL_APP_ID   → App ID dari dashboard OneSignal
ONESIGNAL_REST_KEY → REST API Key dari dashboard OneSignal  
ONESIGNAL_URL      → URL GitHub Pages kamu (contoh: https://username.github.io)
```

#### Deploy GAS

1. Klik **Deploy → New deployment**
2. Klik ikon ⚙️ di samping "Select type" → pilih **Web app**
3. Isi konfigurasi:
   - Description: `Household Finance API`
   - Execute as: **Me**
   - Who has access: **Anyone**
4. Klik **Deploy**
5. **Copy URL** yang muncul — ini adalah `SCRIPT_URL` yang akan dipakai di app

> ⚠️ Setiap kali update Code.gs, wajib deploy ulang dengan **New Version** (bukan new deployment).

---

### Langkah 3 — Setup GitHub Pages

1. **Fork** repo ini atau clone ke komputer kamu:
   ```bash
   git clone https://github.com/username/household-finance.git
   cd household-finance
   ```

2. Edit file `config.js` — isi `scriptUrl` dengan URL GAS dari langkah 2:
   ```javascript
   const APP_CONFIG = {
     appName:   "Household Finance",   // nama app kamu
     appEmoji:  "🏠",                  // emoji ikon
     scriptUrl: "https://script.google.com/macros/s/XXXX/exec", // URL GAS
     oneSignalAppId: "",               // isi jika pakai OneSignal
     // ...
   };
   ```

3. Push ke GitHub:
   ```bash
   git add .
   git commit -m "Initial setup"
   git push origin main
   ```

4. Di GitHub repo → **Settings → Pages**:
   - Source: **Deploy from a branch**
   - Branch: **main** → **/ (root)**
   - Klik **Save**

5. Tunggu beberapa menit → akses di `https://username.github.io/nama-repo`

> 💡 Alternatif: rename repo menjadi `username.github.io` agar URL menjadi `https://username.github.io` (diperlukan jika pakai OneSignal free plan)

---

### Langkah 4 — Setup OneSignal (Opsional)

OneSignal memungkinkan push notification masuk ke HP meskipun app tertutup.

1. Daftar di [onesignal.com](https://onesignal.com) → buat app baru
2. Pilih platform **Web**
3. Isi konfigurasi:
   - **Site Name**: nama app kamu
   - **Site URL**: `https://username.github.io` (harus root domain, bukan subdirectory jika pakai free plan)
4. Download **OneSignalSDKWorker.js** dari dashboard OneSignal
5. Taruh file tersebut di **root folder** repo (sama level dengan `index.html`)
6. Catat **App ID** dan **REST API Key** dari **Settings → Keys & IDs**
7. Masukkan ke Script Properties GAS (lihat langkah 2) dan ke `config.js`

---

### Langkah 5 — Konfigurasi via Settings App

Setelah deploy, buka app → klik **⚙️ Setup** di halaman login:

1. **Nama Aplikasi** — nama yang tampil di app
2. **Script URL** — URL GAS dari langkah 2 (wajib diisi)
3. **OneSignal App ID** — kosongkan jika tidak pakai
4. **Kepemilikan** — daftar nama anggota, pisahkan dengan koma
   ```
   Contoh: Ayah, Ibu, Anak
   ```
5. **Konfigurasi Dompet** — format JSON:
   ```json
   {
     "Cash": ["Cash"],
     "M-Banking": ["BCA", "Mandiri", "BRI"],
     "E-Wallet": ["GoPay", "Dana", "OVO"]
   }
   ```
6. Klik **Simpan Pengaturan**

---

### Langkah 6 — Install sebagai PWA di HP

**Android (Chrome):**
1. Buka URL app di Chrome
2. Tap menu ⋮ → **Add to Home Screen**
3. Tap **Add**

**iPhone (Safari):**
1. Buka URL app di Safari
2. Tap ikon Share 📤 → **Add to Home Screen**
3. Tap **Add**

---

## 🔄 Update App

Jika kamu mengubah kode:

```bash
git add .
git commit -m "Update: deskripsi perubahan"
git push origin main
```

GitHub Pages akan otomatis update dalam 1-2 menit.

Jika update `Code.gs`:
1. Edit di GAS Editor
2. **Deploy → Manage Deployments → Edit (ikon pensil) → Version: New version → Deploy**

---

## ⚙️ Konfigurasi Lanjutan

### Ubah Password
Di GAS → Project Settings → Script Properties → ubah nilai `APP_PASSWORD`

### Ubah Periode Budget
Default periode budget adalah tanggal **25 s/d 24** bulan berikutnya (cocok untuk gajian tanggal 25). Untuk mengubah, edit fungsi `getPeriodeBudget()` di `app.js`.

### Tambah Tipe Dompet Baru
Di Settings app → Konfigurasi Dompet, tambahkan key baru:
```json
{
  "Cash": ["Cash"],
  "M-Banking": ["BCA", "Mandiri"],
  "E-Wallet": ["GoPay", "Dana"],
  "Investasi": ["Saham", "Reksa Dana"]
}
```

---

## 🛠️ Troubleshooting

| Masalah | Solusi |
|---|---|
| Tidak bisa login | Pastikan Script URL sudah diisi di Settings dan GAS sudah di-deploy |
| Data tidak muncul | Cek nama sheet di Spreadsheet harus persis: `Transaksi` dan `Budget` |
| Push notif tidak masuk | Cek Script Properties GAS sudah diisi, cek Audience → Subscriptions di OneSignal |
| PWA tidak update | Clear cache browser: Settings → Clear site data |
| GAS error 403 | Pastikan deployment GAS "Who has access" = **Anyone** |

---

## 📋 FAQ

**Q: Apakah data aman?**
A: Data tersimpan di Google Sheets milik kamu sendiri. Tidak ada pihak ketiga yang bisa mengakses kecuali kamu share spreadsheet-nya.

**Q: Berapa biayanya?**
A: Gratis. Google Sheets, Apps Script, dan GitHub Pages semuanya gratis. OneSignal juga punya free plan.

**Q: Bisa dipakai lebih dari 1 HP?**
A: Bisa. Semua HP yang login ke app yang sama akan berbagi data yang sama dari spreadsheet.

**Q: Bagaimana cara backup data?**
A: Data ada di Google Sheets, otomatis ter-backup. Bisa juga export ke CSV dari app.

---

## 📄 Lisensi

MIT License — bebas digunakan, dimodifikasi, dan didistribusikan.

---

## 🙏 Kontribusi

Pull request dan issue sangat disambut! Silakan fork dan kembangkan sesuai kebutuhan.
