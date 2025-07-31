Berikut versi parafrase laporan kamu dengan bahasa yang lebih natural dan mengalir seperti gaya penulisan manusia:

---

# 📝 Laporan Tugas Akhir

**Mata Kuliah**: Sistem Operasi
**Semester**: Genap / Tahun Ajaran 2024–2025
**Nama**: Muhammad Pandu Dewanata Yaseh Hidayat
**NIM**: 240202841
**Modul yang Dikerjakan**:
`Modul 3 – Manajemen Memori Tingkat Lanjut`

---

## 📌 Deskripsi Tugas

Pada tugas modul ini, saya mengerjakan dua fitur lanjutan dalam pengelolaan memori pada sistem operasi *xv6*, yaitu:

1. **Copy-on-Write (CoW) Fork**
   Fitur ini bertujuan membuat proses `fork()` jadi lebih efisien. Alih-alih langsung menyalin seluruh halaman memori, proses parent dan child akan berbagi halaman yang sama dalam mode *read-only*. Penyalinan baru dilakukan jika salah satu proses mencoba menulis ke halaman tersebut.

2. **Shared Memory ala System V**
   Di sini, saya menambahkan dua system call baru yaitu `shmget()` dan `shmrelease()` untuk memungkinkan beberapa proses saling berbagi satu halaman memori. Mekanisme ini menggunakan sistem reference count agar memori dibagikan dengan aman dan efisien.

---

## 🛠️ Langkah-langkah Implementasi

### A. Copy-on-Write Fork (CoW)

* Saya membuat array `ref_count[]` di file `vm.c` sebagai penghitung jumlah proses yang memakai satu halaman fisik.
* Dua fungsi tambahan, `incref()` dan `decref()`, dibuat untuk menambah atau mengurangi ref\_count sesuai kebutuhan.
* Di file `mmu.h`, saya tambahkan flag `PTE_COW` untuk menandai halaman yang sedang dipakai mekanisme copy-on-write.
* Fungsi `copyuvm()` diubah menjadi `cowuvm()` yang kini digunakan di `fork()` pada `proc.c`.
* Untuk menangani penulisan ke halaman CoW, saya modifikasi `trap.c` agar bisa mendeteksi dan menyalin halaman secara dinamis saat diperlukan.

### B. Shared Memory ala System V

* Saya tambahkan struktur array `shmtab[]` di `vm.c` untuk menyimpan data shared memory, seperti key, alamat frame, dan ref\_count-nya.
* Saya juga membuat dua system call baru, `shmget()` dan `shmrelease()`, yang didefinisikan di `sysproc.c`.
* Agar system call ini bisa digunakan, saya daftarkan juga di beberapa file penting: `syscall.c`, `syscall.h`, `usys.S`, dan `user.h`.

---

## ✅ Pengujian

### Program yang Digunakan:

* **cowtest**: untuk menguji apakah `fork()` benar-benar menggunakan Copy-on-Write.
* **shmtest**: untuk memastikan dua proses bisa saling berbagi data lewat shared memory.

---

## 📷 Hasil Pengujian

### Output dari `cowtest`:

```
Child sees: Y
Parent sees: X
```

### Output dari `shmtest`:

```
Child reads: A
Parent reads: B
```

---

## ⚠️ Kendala Selama Pengerjaan

* Saat menangani page fault, sistem sempat crash karena halaman yang kena fault ternyata bukan halaman CoW, dan saya kurang tepat dalam validasinya.
* Saya sempat lupa menghapus flag `PTE_COW` setelah halaman disalin, yang membuat proses anak jadi mengakses memori secara ilegal.
* Di bagian `shmget()`, proses sering crash karena alamat virtual yang saya pakai tidak konsisten antar proses.
* Selain itu, ada masalah dengan `ref_count` yang tidak berkurang ketika proses selesai atau memanggil `shmrelease()`, yang menyebabkan kebocoran memori.

---

## 📚 Referensi

* [Buku xv6 MIT](https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf)
* [Repositori GitHub xv6](https://github.com/mit-pdos/xv6-public)

---
