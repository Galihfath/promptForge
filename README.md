# PromptForge ⚡

**Website Prompt Builder** — aplikasi web stand-alone untuk menyusun prompt berkualitas tinggi
(plus file `CLAUDE.md`/`AGENTS.md`) untuk membangun website via **Claude Code**.

- 🧠 **Offline-first** — rule engine & template engine di sisi client; berfungsi penuh tanpa koneksi/API.
- ✨ **Mode AI opsional (gratis)** — tempel [Gemini API key gratis](https://aistudio.google.com/apikey) milikmu (BYOK): AI bisa **menganalisis brief → mengisi seluruh form** dan **memperkaya prompt** dengan ide konten & desain spesifik. Key tersimpan hanya di localStorage browser dan dikirim langsung ke Google, tanpa server perantara.
- 🪄 **Mulai Cepat** — ketik brief satu kalimat, form terisi otomatis (deteksi kata kunci: jenis, fitur, halaman, gaya, palet).
- 💡 **Saran Cerdas** — advisor heuristik mendeteksi kombinasi isian yang kurang optimal, dengan tombol "Terapkan" satu klik.
- 📊 **Skor Prompt** — nilai kelengkapan 0–100 real-time sebagai indikator kesiapan one-shot.
- 📦 **Satu file** — `index.html` berisi HTML + CSS + vanilla JS. Tanpa framework, build step, atau dependency.
- 🔌 **Full offline** — jalan langsung via `file://`, auto-save draft & preset ke `localStorage`.

Live: **https://prompt.galihupit.my.id**

## Cara Menjalankan Lokal

Double-click `index.html` — selesai. Tidak ada instalasi apa pun.

Kalau mau lewat server lokal (opsional):

```bash
npx serve .
# atau
python -m http.server 8000
```

## Cara Deploy ke Vercel

1. Push repo ini ke GitHub.
2. Di [vercel.com](https://vercel.com) → **Add New Project** → import repo ini.
3. Framework Preset: **Other**, tanpa build command, output directory: root (`./`). Deploy.
4. Tambahkan custom domain `prompt.galihupit.my.id` di **Settings → Domains**,
   lalu buat CNAME record di DNS domain kamu mengarah ke `cname.vercel-dns.com`.

> Catatan: meta tag `og:image` menunjuk ke `https://prompt.galihupit.my.id/og.png`.
> Tambahkan file `og.png` (1200×630) ke root repo bila ingin preview gambar saat link dibagikan.

## Struktur Kode (`index.html`)

Semua ada di satu file, dibagi per bagian dengan komentar header:

| Bagian | Isi |
|---|---|
| `<head>` | SEO meta, Open Graph, favicon SVG inline |
| `<style>` | Design tokens (CSS variables), layout 2 kolom, dark mode, responsive |
| `<body>` | Form 5 section (A–E), panel live preview, tab mobile, toast |
| `<script>` bagian 1–3 | Util, data statis, `collectData()` / `applyData()` |
| `<script>` bagian 4 | **Rule engine** (array `RULES`) |
| `<script>` bagian 4b | **Smart engine**: `SMART_*` (kamus keyword Mulai Cepat), `ADVISORS` (saran), `scorePrompt()` |
| `<script>` bagian 4c | **AI engine** (opsional): `callGemini()`, `aiAnalyzeBrief()` (structured output + sanitasi), `aiEnhancePrompt()` |
| `<script>` bagian 5 | Template builder: `buildPrompt()` & `buildClaudeMd()` |
| `<script>` bagian 6–9 | Render preview, aksi copy/download, preset, auto-save, init |

## Cara Menambah Rule Baru

Cari array `RULES` di `index.html`, lalu tambahkan **satu entry**:

```js
{
  id: 'form-kontak',                              // id unik
  label: 'Form Kontak',                           // nama rule
  when: d => d.fitur.includes('Form kontak'),     // kondisi aktif (d = data form)
  inject: {                                       // butir yang disuntik ke section prompt
    fitur: ['Validasi form di client, tampilkan pesan error inline per field.'],
  },
  criteria: ['Form menolak submit saat field wajib kosong.'],  // acceptance criteria
},
```

Key `inject` yang tersedia: `stack`, `desain`, `struktur`, `fitur`, `kualitas` — semuanya opsional.
