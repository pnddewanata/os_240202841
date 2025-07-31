Berikut adalah laporan akhir dengan gaya **bahasa manusia** tanpa mengubah isi teknis atau struktur penting dari laporan aslinya:

---

# 📝 Laporan Tugas Akhir

**Mata Kuliah**: Sistem Operasi
**Semester**: Genap / Tahun Ajaran 2024–2025
**Nama**: Muhammad Pandu Dewanata Yaseh Hidayat
**NIM**: 240202841
**Modul yang Dikerjakan**:

# 🧪 Modul 2 — Penjadwalan CPU Lanjutan (Priority Scheduling Non-Preemptive)

---

## 🎯 Tujuan

Tujuan utama dari tugas ini adalah memodifikasi sistem penjadwalan proses pada `xv6-public`, dari yang semula menggunakan algoritma **Round Robin** menjadi **Non-Preemptive Priority Scheduling**.
Beberapa langkah penting yang dilakukan meliputi:

* Menambahkan field `priority` pada setiap proses.
* Membuat syscall baru yaitu `set_priority(int)` yang digunakan untuk mengatur nilai prioritas suatu proses.
* Memodifikasi fungsi scheduler agar proses yang dijalankan adalah proses dengan status RUNNABLE dan memiliki prioritas tertinggi.

---

## 🗂️ File yang Diubah

Berikut daftar file yang mengalami perubahan beserta deskripsi singkatnya:

| File        | Perubahan                                       |
| ----------- | ----------------------------------------------- |
| `proc.h`    | Menambahkan field `priority` pada struct proc   |
| `proc.c`    | Mengubah isi fungsi `scheduler()`               |
| `sysproc.c` | Menambahkan implementasi syscall `set_priority` |
| `syscall.h` | Menambahkan nomor syscall baru                  |
| `syscall.c` | Registrasi syscall baru                         |
| `user.h`    | Menambahkan deklarasi syscall                   |
| `usys.S`    | Menambahkan entri syscall                       |
| `Makefile`  | Menambahkan program pengujian `ptest.c`         |

---

## 🧩 Rincian Implementasi

### 🔹 1. Menambahkan Field `priority`

Di dalam `struct proc`, ditambahkan satu field baru:

```c
int priority; // nilai prioritas (0 = tertinggi, 100 = terendah)
```

### 🔹 2. Inisialisasi Nilai Prioritas

Pada fungsi `allocproc()`, setiap proses baru secara default diberikan nilai prioritas:

```c
p->priority = 60; // nilai default
```

### 🔹 3. Pembuatan syscall `set_priority(int)`

Agar pengguna bisa mengatur prioritas proses, dibuatlah syscall baru dengan rincian sebagai berikut:

#### a. `syscall.h`

```c
#define SYS_set_priority 24
```

#### b. `user.h`

```c
int set_priority(int priority);
```

#### c. `usys.S`

```asm
SYSCALL(set_priority)
```

#### d. `syscall.c`

```c
extern int sys_set_priority(void);
[SYS_set_priority] sys_set_priority,
```

#### e. `sysproc.c`

```c
int sys_set_priority(void) {
  int prio;
  if (argint(0, &prio) < 0 || prio < 0 || prio > 100)
    return -1;
  myproc()->priority = prio;
  return 0;
}
```

### 🔹 4. Modifikasi Scheduler

Scheduler diubah agar dapat memilih proses RUNNABLE dengan prioritas paling tinggi (angka prioritas paling kecil). Berikut cuplikan modifikasi fungsi `scheduler()`:

```c
void scheduler(void) {
  struct proc *p;
  struct proc *highest = 0;

  for(;;){
    sti();
    acquire(&ptable.lock);
    highest = 0;
    for(p = ptable.proc; p < &ptable.proc[NPROC]; p++){
      if(p->state != RUNNABLE)
        continue;
      if(highest == 0 || p->priority < highest->priority)
        highest = p;
    }

    if(highest != 0){
      p = highest;
      proc = p;
      switchuvm(p);
      p->state = RUNNING;
      swtch(&cpu->scheduler, proc->context);
      switchkvm();
      proc = 0;
    }

    release(&ptable.lock);
  }
}
```

### 🔹 5. Program Uji (`ptest.c`)

Untuk membuktikan bahwa penjadwalan berjalan sesuai prioritas, dibuatlah program sederhana yang menjalankan dua proses anak dengan prioritas berbeda.

```c
#include "types.h"
#include "stat.h"
#include "user.h"

void busy() {
  for (volatile int i = 0; i < 100000000; i++);
}

int main() {
  int pid1 = fork();
  if (pid1 == 0) {
    set_priority(90); // prioritas rendah
    busy();
    printf(1, "Child 1 selesai\n");
    exit();
  }

  int pid2 = fork();
  if (pid2 == 0) {
    set_priority(10); // prioritas tinggi
    busy();
    printf(1, "Child 2 selesai\n");
    exit();
  }

  wait(); wait();
  printf(1, "Parent selesai\n");
  exit();
}
```

### 🔹 6. Menambahkan `ptest` ke Makefile

Agar program uji bisa dikompilasi secara otomatis:

```make
  _ptest\
```

---

## ✅ Validasi & Hasil Uji

Program diuji langsung di shell `xv6` dan hasilnya menunjukkan bahwa proses dengan prioritas lebih tinggi dijalankan lebih dahulu:

```bash
$ ptest
Child 2 selesai
Child 1 selesai
Parent selesai
```

## 📚 Referensi

* [MIT xv6 Book (x86)](https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf)
* [xv6-public GitHub](https://github.com/mit-pdos/xv6-public)

---

## 📝 Kesimpulan

Melalui implementasi ini, sistem `xv6` berhasil dimodifikasi untuk menggunakan algoritma **Non-Preemptive Priority Scheduling**. Proses yang memiliki angka prioritas lebih kecil akan dijalankan terlebih dahulu.

Pendekatan ini memberikan penjadwalan yang lebih deterministik, namun bisa menjadi tidak adil jika proses dengan prioritas rendah terus-menerus ditunda. Oleh karena itu, di dunia nyata biasanya mekanisme seperti **aging** digunakan untuk menjaga keseimbangan.

---
