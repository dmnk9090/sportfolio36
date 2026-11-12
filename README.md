# SportFolio — Portal Digital Kebugaran Jasmani & Asesmen PJOK
SMP Negeri 36 Pekanbaru · Dikembangkan oleh Heru Prananda, S.Pd., Gr
(Dikembangkan dengan bantuan asisten AI Claude; lihat halaman "Tentang, Atribusi & Privasi" di aplikasi.)

## Berkas yang diunggah ke GitHub (satu folder)
`index.html` · `sw.js` · `manifest.webmanifest` · `icon-192.png` · `icon-512.png` · `icon-maskable-512.png` · `apple-touch-icon.png`

## Struktur `index.html`
1. **Inti aplikasi** — data, penyimpanan lokal, tampilan siswa & guru, `Util` (alat bantu), `App` (bus kejadian).
2. **Modul fitur** (skrip terpisah, saling bebas): Fitur Siswa & Alat Latihan · Perbaikan Admin · Peringkat Nilai ·
   Sumber Belajar · Game · Privasi/Panduan/Juri · Statistik/Survei/Bank Soal.
3. **Modul sinkronisasi** (`type="module"`) — Firebase Realtime Database & Authentication.

Modul terhubung lewat `App.on('namaKejadian', fn)` dan `registerStudentTab(...)`.
Kejadian: `dataSaved · studentLoaded · adminListRendered · tabChanged · welcomeShown · adminEntered · adminLeft`.

## Memperbarui aplikasi
1. Unggah `index.html` baru (Add file → Upload files, nama sama) lalu Commit.
2. Naikkan angka `VERSION` di `sw.js` (mis. `sportfolio-v3`) agar HP memakai versi terbaru.
3. Tunggu 2–3 menit, muat ulang dengan Ctrl+F5.

## Firebase
- **Authentication**: aktifkan Email/Password; satu pengguna guru (`heruprananda@gmail.com`).
- **Realtime Database → Rules**:
```json
{
  "rules": {
    ".read": false, ".write": false,
    "students": {
      ".read": "auth != null && auth.token.email == 'heruprananda@gmail.com'",
      ".write": "auth != null && auth.token.email == 'heruprananda@gmail.com'",
      "$id": { ".read": true, ".write": "newData.exists()" }
    },
    "scores": {
      ".read": true,
      ".write": "auth != null && auth.token.email == 'heruprananda@gmail.com'",
      "$id": { ".write": "newData.exists()" }
    },
    "resources": {
      ".read": true,
      ".write": "auth != null && auth.token.email == 'heruprananda@gmail.com'"
    },
    "game": {
      "scores": {
        ".read": true,
        ".write": "auth != null && auth.token.email == 'heruprananda@gmail.com'",
        "$id": { ".write": "newData.exists()" }
      },
      "questions": {
        ".read": true,
        ".write": "auth != null && auth.token.email == 'heruprananda@gmail.com'"
      }
    }
  }
}
```

## Jalur data di Firebase
`students/{NISN}` data lengkap siswa · `scores/{NISN}` ringkasan peringkat (inisial) ·
`resources` sumber belajar · `game/scores/{NISN}` skor game · `game/questions` bank soal game.

## Catatan privasi
Kode tidak memuat data siswa. Data asli hanya di Firebase; guru memasukkan lewat "Impor Daftar" atau "Tambah Siswa Baru".
Mode Juri memakai data contoh yang hanya ada di memori halaman dan tidak menulis ke server.
Risiko tersisa: data satu siswa dapat diakses oleh orang yang mengetahui NISN-nya (siswa belum punya akun Firebase sendiri).
