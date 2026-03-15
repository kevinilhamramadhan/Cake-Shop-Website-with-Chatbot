# 🎂 Cake Shop Website with Chatbot

Aplikasi Point of Sale (PoS) toko kue berbasis web dengan fitur AI chatbot. Dibangun dengan arsitektur microservices menggunakan Node.js, Python (FastAPI), dan React.

## Arsitektur

```
Cloudflare Tunnel
      │
   Nginx (reverse proxy)
      ├── /api/      → Backend (Node.js)
      ├── /chatbot/  → Chatbot (Python/FastAPI)
      └── /          → Frontend (React/Vite)
         
Backend → PostgreSQL
```

## Prasyarat

Pastikan sudah terinstall di mesin lokal:
- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/) (v2+)
- [Ollama](https://ollama.com/download) (hanya jika ingin menjalankan fitur chatbot)

## Struktur Folder

```
.
├── backend/        # REST API (Node.js/Express)
├── chatbot/        # AI Chatbot (Python/FastAPI + LangChain)
├── frontend/       # UI (React/Vite)
├── nginx/          # Konfigurasi reverse proxy
├── docker-compose.yml
└── .env.example
```

## Setup & Menjalankan Lokal

### 1. Clone repository

```bash
git clone https://github.com/username/Cake-Shop-Website-with-Chatbot.git
cd Cake-Shop-Website-with-Chatbot
```

### 2. Buat file `.env`

```bash
cp .env.example .env
```

Lalu isi nilai variabel di file `.env` sesuai environment lokal kamu (lihat bagian [Environment Variables](#environment-variables)).

### 3. Jalankan

**Tanpa chatbot** (tidak perlu Ollama):
```bash
docker compose up -d nginx
```

**Dengan chatbot** (membutuhkan Ollama):

Pastikan Ollama sudah berjalan, lalu pull model yang dibutuhkan:
```bash
ollama pull gemma3
ollama pull functiongemma
```

Pastikan `OLLAMA_BASE_URL` di file `.env` sudah diisi:
```
OLLAMA_BASE_URL=http://host.docker.internal:11434
```

> **Catatan:** Gunakan `host.docker.internal` agar container Docker bisa mengakses Ollama yang berjalan di host. Di Linux, jika `host.docker.internal` tidak berfungsi, gunakan IP host kamu (cek dengan `ip route | grep docker | awk '{print $9}'`).

Lalu jalankan semua service:
```bash
docker compose up -d
```

Docker akan otomatis pull image dari Docker Hub dan menjalankan semua service.

### 4. Akses aplikasi

| Service  | URL                        |
|----------|----------------------------|
| Frontend | http://localhost            |
| Backend  | http://localhost/api/       |
| Chatbot  | http://localhost/chatbot/   |

### Menghentikan aplikasi

```bash
docker compose down
```

> **Catatan:** Data database tidak akan hilang saat `docker compose down`. Gunakan `docker compose down -v` hanya jika ingin menghapus data juga.

## Environment Variables

Salin `.env.example` menjadi `.env` dan isi nilai berikut:

| Variabel          | Deskripsi                                    | Contoh                             |
|-------------------|----------------------------------------------|------------------------------------|
| `DB_NAME`         | Nama database PostgreSQL                     | `cakeshop`                         |
| `DB_USER`         | Username database PostgreSQL                 | `postgres`                         |
| `DB_PASSWORD`     | Password database PostgreSQL                 | `postgres`                         |
| `JWT_SECRET`      | Secret key untuk JWT (random string panjang) | `ganti_dengan_string_acak_panjang` |
| `OLLAMA_BASE_URL` | URL Ollama server                            | `http://host.docker.internal:11434`|

## Cara Berkontribusi

### Branching

Semua contributor langsung push ke branch `main`:

```bash
git checkout main
git pull
# ... kerjakan perubahan ...
git add .
git commit -m "feat: deskripsi perubahan"
git push origin main
```

> **Catatan:** Setiap push ke `main` akan otomatis trigger CI dan build ulang image ke Docker Hub.

### Kontribusi per Role

**Backend Developer**
- Kerjakan di folder `backend/`
- Pastikan `package.json` dan `package-lock.json` selalu ter-update
- Buat migration script untuk setiap perubahan schema database

**Chatbot Developer**
- Kerjakan di folder `chatbot/`
- Setelah menambah dependency baru jalankan `uv add <package>` dan commit `pyproject.toml` serta `uv.lock`

**Frontend Developer**
- Kerjakan di folder `frontend/`
- Untuk development frontend saja, cukup jalankan `docker compose up db backend` lalu jalankan frontend secara lokal dengan `npm run dev`

## CI (Continuous Integration)

Setiap push ke branch `main` akan otomatis:
1. Build Docker image untuk ketiga service
2. Push image ke Docker Hub dengan tag `latest` dan tag commit SHA

Workflow dapat juga dijalankan manual melalui tab **Actions** di GitHub → **Run workflow**.
