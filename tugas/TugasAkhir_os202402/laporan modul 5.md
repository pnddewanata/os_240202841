Berikut adalah versi **parafrase dengan bahasa yang lebih natural dan manusiawi** dari laporan tugas akhir yang kamu buat. Tetap sopan, ringkas, dan mudah dipahami:

---

# 📝 Laporan Tugas Akhir

**Mata Kuliah**: Sistem Operasi
**Semester**: Genap / Tahun Ajaran 2024–2025
**Nama**: Muhammad Pandu Dewanata Yaseh Hidayat
**NIM**: 240202841
**Modul**: Modul 5 – Audit dan Keamanan Sistem

---

## 📌 Deskripsi Singkat Tugas

Tugas pada modul ini berfokus pada penambahan fitur audit di dalam sistem. Tujuannya adalah supaya semua system call yang dilakukan oleh proses bisa direkam dan tersimpan dalam bentuk log. Namun, agar aman, hanya proses tertentu (PID 1) yang diizinkan untuk mengakses log tersebut. Dengan ini, mahasiswa belajar bagaimana cara mengintegrasikan fitur keamanan langsung dari sisi kernel.

---

## 🛠️ Apa yang Saya Lakukan

### A. Audit System Call

* Saya menambahkan struktur data `audit_entry` dan array `audit_log[]` di file `syscall.c` untuk menyimpan catatan system call.
* Di dalam fungsi `syscall()`, saya memasukkan kode untuk mencatat setiap system call yang dijalankan proses, lengkap dengan PID, nomor syscall, dan waktu (tick-nya).
* Saya membuat syscall baru bernama `get_audit_log(void *buf, int max)` di `sysproc.c`. System call ini hanya bisa dipakai oleh proses dengan PID 1.
* Selain itu, saya juga mendeklarasikan syscall tersebut di file `user.h`, `usys.S`, `syscall.h`, dan juga mendaftarkannya di `syscall.c`.

### B. Program Uji & Validasi Akses

* Saya membuat program `audit.c` untuk mengambil log menggunakan `get_audit_log()` lalu mencetaknya ke layar.
* Program `audit` saya tambahkan ke dalam `Makefile` supaya bisa langsung dibangun dengan `make`.
* Supaya `audit` bisa berjalan sebagai proses dengan PID 1, saya mengedit file `init.c` dan mengganti shell default dengan `exec("audit", argv)`.

---

## ✅ Pengujian

Program yang digunakan untuk uji coba:
**audit** – menjalankan `get_audit_log()` dan menampilkan hasil log di terminal.

---

## 📷 Hasil Pengujian

### 📍 Saat dijalankan oleh proses biasa:

```
Access denied or error.
```

### 📍 Saat dijalankan oleh PID 1:

```
=== Audit Log ===
[0] PID=1 SYSCALL=5 TICK=12  
[1] PID=1 SYSCALL=6 TICK=13  
[2] PID=1 SYSCALL=22 TICK=14  
...
```

---

## ⚠️ Kendala Selama Mengerjakan

* Awalnya saya lupa menambahkan pengecekan PID di `get_audit_log()`, sehingga proses selain PID 1 sempat bisa mengakses log.
* Ukuran buffer di `argptr()` harus disesuaikan dengan jumlah maksimal data (`MAX_AUDIT`), kalau tidak bisa error.
* Karena XV6 tidak menyimpan data secara permanen, semua log hanya ada di memori dan akan hilang setelah restart.
* Proses mengganti shell dengan program `audit` agak tricky. Kadang `audit` belum sempat ter-compile saat `init` mencoba menjalankannya.

---

## 📚 Sumber Referensi

* Buku resmi XV6 – [https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf](https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf)
* Repositori XV6 Public – [https://github.com/mit-pdos/xv6-public](https://github.com/mit-pdos/xv6-public)
* Forum diskusi, Stack Overflow, dan GitHub Issues

---
