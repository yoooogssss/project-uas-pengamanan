# UAS Keamanan Komputer

## Identitas
- Nama: Yoga Dwi Anggoro Moekti
- NIM: 221080200128
- Kelas: 6B1
- Repo GitHub: https://github.com/yoooogssss/project-uas-pengamanan

---
# JWT CRUD API UAS Starter

Silakan lanjutkan pengembangan API dan perbaiki bug yang ditemukan.
## Bagian A – Bug Fixing JWT REST API

### Bug 1: Token tetap aktif setelah logout
**Penjelasan:**  
JWT tidak otomatis kadaluarsa saat logout, karena tidak disimpan server-side. Akibatnya, token lama bisa tetap digunakan hingga expired.

**Solusi:**  
Implementasi sistem **blacklist token**:
- Simpan token yang sudah logout dalam Redis/database.
- Middleware memeriksa apakah token termasuk dalam daftar blacklist.
    php
    // Contoh middleware token blacklist (pseudocode)
    if (in_array($token, $blacklist)) {
        return response()->json(['message' => 'Token sudah tidak aktif'], 401);
    }
---

## Bagian B – Simulasi Serangan dan Solusi

### Jenis Serangan: Broken Access Control  
**Simulasi Postman:**  
1. Login sebagai user biasa.
2. Kirim request:

http
PUT /api/users/update
Content-Type: application/json

{
  "user_id": 1,
  "name": "Hacked Admin"
}


Jika berhasil diubah, maka terjadi Broken Access Control.

**Solusi Implementasi:**  
* Validasi *role/permission* pada setiap endpoint sensitif.
* Cek auth()->user()->id dengan user_id pada request body. Jangan percaya data dari klien.

php
// Middleware / Controller
if (auth()->user()->role !== 'admin') {
   return response()->json(['error' => 'Unauthorized'], 403);
}
Cek ID user agar hanya bisa edit miliknya sendiri (untuk user biasa):

php
if (auth()->user()->id !== $request->user_id && auth()->user()->role !== 'admin') {
    return response()->json(['error' => 'Forbidden'], 403);
}
---

## Bagian C – Refleksi Teori & Etika

### 1. CIA Triad dalam Keamanan Informasi  
Confidentiality: Melindungi informasi dari akses tidak sah.
Integrity: Menjaga agar data tidak dimodifikasi tanpa izin.
Availability: Menjamin data bisa diakses saat dibutuhkan.


### 2. UU ITE yang relevan  
Pasal 30 Ayat (1): Akses sistem elektronik tanpa izin = ilegal.
Pasal 32: Pengubahan atau penghilangan data = tindak pidana.

### 3. Pandangan Al-Qur'an  
- Surah Al-Baqarah: 205  
Dan apabila ia berpaling (dari kamu), ia berjalan di bumi untuk mengadakan kerusakan padanya, dan merusak tanam-tanaman dan binatang ternak, dan Allah tidak menyukai kebinasaan


### 4. Etika Cyber dan Kejujuran  
Etika cyber mencakup kejujuran, tanggung jawab, dan tidak merugikan orang lain.
Amanah dalam menjaga akses dan data.
Developer harus bertanggung jawab terhadap sistem yang dibuat.
