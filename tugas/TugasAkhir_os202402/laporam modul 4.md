Berikut revisi laporan Anda agar lebih rapi, konsisten, dan profesional, tanpa mengubah makna isi:

---

# 📝 Laporan Tugas Akhir

**Mata Kuliah**: Sistem Operasi
**Semester**: Genap / Tahun Ajaran 2024–2025
**Nama**: Muhammad Pandu Dewanata Yaseh Hidayat
**NIM**: 240202841

## 🧪 Laporan Praktikum Sistem Operasi – Modul 4

---

## 📌 Deskripsi Singkat Tugas

**Modul 4 – Subsistem Kernel Alternatif (xv6-public)**
Modul ini berfokus pada modifikasi kernel xv6 dengan dua fitur utama:

1. Menambahkan system call baru `chmod(path, mode)` untuk mengatur mode akses file (read-only atau read-write).
2. Menambahkan pseudo-device `/dev/random` yang menghasilkan angka acak saat dibaca.

---

## 🛠️ Rincian Implementasi

### ✳️ System Call `chmod()`

* Menambahkan field `mode` pada `struct inode` di `fs.h`.
* Mendefinisikan system call baru di file: `syscall.h`, `user.h`, `usys.S`, `syscall.c`, dan `sysfile.c`.
* Memodifikasi `filewrite()` pada `file.c` agar menolak penulisan pada file read-only.
* Membuat program uji `chmodtest.c` untuk memastikan file read-only tidak dapat ditulis kembali.

### ✳️ Pseudo-Device `/dev/random`

* Membuat file `random.c` yang mengimplementasikan fungsi `randomread()` sebagai generator byte acak.
* Menambahkan entri driver pada array `devsw[]` di `file.c`.
* Membuat device node `/dev/random` di `init.c`.
* Membuat program uji `randomtest.c` untuk membaca 8 byte acak dari device tersebut.

---

## ✅ Uji Fungsionalitas

### Program Uji

* `chmodtest`: menguji pembatasan penulisan pada file read-only.
* `randomtest`: menguji keluaran byte acak dari `/dev/random`.

---

## 📷 Hasil Uji

### 📍 Output `chmodtest`:

```
Write blocked as expected
```

### 📍 Output `randomtest`:

```
48 67 38 65 32 47 86 21
```

---

## ⚠️ Kendala yang Dihadapi

* Penambahan field `mode` awalnya dilakukan pada disk layout, sehingga menyebabkan error saat mounting file system lama. Solusi: field hanya digunakan secara volatile di memori.
* Kesalahan indeks saat pendaftaran `devsw[]` menyebabkan device `/dev/random` tidak dapat diakses. Masalah diselesaikan dengan penyesuaian indeks driver.

---

## 📚 Referensi

* [xv6 book (rev11)](https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf)
* [GitHub xv6-public](https://github.com/mit-pdos/xv6-public)
* Modul Praktikum Sistem Operasi 2024 – Modul 4

---
