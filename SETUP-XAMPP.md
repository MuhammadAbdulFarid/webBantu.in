# Setup Database dengan XAMPP

## 📋 Langkah-Langkah Setup

### 1. Install dan Jalankan XAMPP

1. Download XAMPP dari https://www.apachefriends.org/
2. Install XAMPP
3. Buka **XAMPP Control Panel**
4. Start **Apache** dan **MySQL**

### 2. Buka phpMyAdmin

1. Setelah MySQL running, buka browser
2. Akses: `http://localhost/phpmyadmin`
3. Login dengan:
   - Username: `root`
   - Password: (kosong - default XAMPP)

### 3. Import Database

#### Cara 1: Via phpMyAdmin (RECOMMENDED)

1. Di phpMyAdmin, klik tab **"SQL"** di menu atas
2. Copy semua isi file `database/schema.sql`
3. Paste ke SQL editor
4. Klik **"Go"** atau **"Kirim"**
5. Database `bantuin_db` akan otomatis dibuat dengan semua tabel dan sample data

#### Cara 2: Via Command Line

```powershell
# Masuk ke folder XAMPP MySQL
cd C:\xampp\mysql\bin

# Import database
.\mysql.exe -u root -e "source d:\rpl&struktur_data\database\schema.sql"
```

### 4. Verifikasi Database

1. Di phpMyAdmin, klik `bantuin_db` di sidebar kiri
2. Pastikan ada 8 tabel:
   - users
   - jobs
   - talents
   - saved_jobs
   - saved_talents
   - job_applications
   - sessions
   - error_logs
3. Klik tabel `users` → tab "Browse" → harus ada 3 sample users

## 🔧 Konfigurasi Backend

File `.env` sudah diupdate untuk XAMPP:

```
DB_HOST=localhost
DB_PORT=3306          # Default XAMPP MySQL port
DB_USER=root
DB_PASSWORD=          # Kosong (default XAMPP)
DB_NAME=bantuin_db
```

## 🚀 Jalankan Backend

```powershell
cd backend
npm start
```

Jika koneksi berhasil, akan muncul:

```
✅ Database connected successfully
🚀 Server running on http://localhost:3000
```

## 📝 Troubleshooting

### Error: "connect ECONNREFUSED"

**Penyebab:** MySQL belum running
**Solusi:**

1. Buka XAMPP Control Panel
2. Klik "Start" pada MySQL
3. Tunggu hingga muncul "Running" dengan background hijau

### Error: "ER_ACCESS_DENIED_ERROR"

**Penyebab:** Username/password salah
**Solusi:**

1. XAMPP default: user=`root`, password=**kosong**
2. Jika sudah set password, update di `.env` file

### Error: "Unknown database 'bantuin_db'"

**Penyebab:** Database belum diimport
**Solusi:** Ulangi langkah 3 (Import Database)

## ✅ Test Koneksi

### Via Browser

Buka: `http://localhost:3000`

### Via PowerShell

```powershell
Invoke-RestMethod -Uri "http://localhost:3000" | ConvertTo-Json
```

Harus return:

```json
{
  "success": true,
  "message": "Bantu.in API is running"
}
```

## 🎯 Next Steps

1. ✅ XAMPP MySQL running
2. ✅ Database imported
3. ✅ Backend connected
4. ▶️ Test API endpoints: `http://localhost:8000/test-api.html`
5. ▶️ Buka website: `http://localhost:8000/beranda.html`
