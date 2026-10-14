# Pertemuan 2 — Layanan dan struktur sistem operasi, slide demi slide
Gambar mengikuti halaman 1–59 dari `K2.pdf` dalam paket Sensei. Nomor slide di bawah adalah halaman PDF asli. Pembahasan menekankan alur konsep; contoh hitungan di akhir memakai konvensi kisi-kisi.

**Fokus UTS:** Layanan SO, kategori system call, perpindahan mode, parameter, API, loader, pustaka dinamis, struktur kernel, policy dan mechanism.

## Pembahasan setiap slide

### Slide 001 — Chapter 2: Operating-System

![Slide 1: Chapter 2: Operating-System](gambar/pertemuan-02/slide-001.jpg)

Slide ini memetakan posisi topik dalam bab. Pakai judul-judulnya sebagai daftar cek; utamakan hubungan sebab-akibat dan contoh, bukan hafalan urutan.

### Slide 002 — Chapter 2: Operating-System Structures

![Slide 2: Chapter 2: Operating-System Structures](gambar/pertemuan-02/slide-002.jpg)

Slide ini memetakan posisi topik dalam bab. Pakai judul-judulnya sebagai daftar cek; utamakan hubungan sebab-akibat dan contoh, bukan hafalan urutan.

**Perhatikan pada gambar:** Operating System Services; User and Operating System-Interface

### Slide 003 — Objectives

![Slide 3: Objectives](gambar/pertemuan-02/slide-003.jpg)

Slide ini memetakan posisi topik dalam bab. Pakai judul-judulnya sebagai daftar cek; utamakan hubungan sebab-akibat dan contoh, bukan hafalan urutan.

### Slide 004 — Operating System Services

![Slide 4: Operating System Services](gambar/pertemuan-02/slide-004.jpg)

Sistem operasi menyediakan cara berinteraksi dan layanan menjalankan program, I/O, berkas, komunikasi, serta penanganan galat. Di belakangnya ada alokasi sumber daya, pencatatan penggunaan, dan perlindungan. Cocokkan permintaan pengguna dengan layanan yang benar.

**Perhatikan pada gambar:** Operating systems provide an environment for execution of programs; and services to programs and users

### Slide 005 — Operating System Services (Cont.)

![Slide 5: Operating System Services (Cont.)](gambar/pertemuan-02/slide-005.jpg)

Sistem operasi menyediakan cara berinteraksi dan layanan menjalankan program, I/O, berkas, komunikasi, serta penanganan galat. Di belakangnya ada alokasi sumber daya, pencatatan penggunaan, dan perlindungan. Cocokkan permintaan pengguna dengan layanan yang benar.

**Perhatikan pada gambar:** One set of operating-system services provides functions that are helpful to; File-system manipulation - The file system is of particular interest.

### Slide 006 — Operating System Services (Cont.)

![Slide 6: Operating System Services (Cont.)](gambar/pertemuan-02/slide-006.jpg)

Sistem operasi menyediakan cara berinteraksi dan layanan menjalankan program, I/O, berkas, komunikasi, serta penanganan galat. Di belakangnya ada alokasi sumber daya, pencatatan penggunaan, dan perlindungan. Cocokkan permintaan pengguna dengan layanan yang benar.

**Perhatikan pada gambar:** Another set of OS functions exists for ensuring the efficient operation of the; system itself via resource sharing

### Slide 007 — A View of Operating System Services

![Slide 7: A View of Operating System Services](gambar/pertemuan-02/slide-007.jpg)

Sistem operasi menyediakan cara berinteraksi dan layanan menjalankan program, I/O, berkas, komunikasi, serta penanganan galat. Di belakangnya ada alokasi sumber daya, pencatatan penggunaan, dan perlindungan. Cocokkan permintaan pengguna dengan layanan yang benar.

### Slide 008 — User Operating System Interface - CLI

![Slide 8: User Operating System Interface - CLI](gambar/pertemuan-02/slide-008.jpg)

Sistem operasi menyediakan cara berinteraksi dan layanan menjalankan program, I/O, berkas, komunikasi, serta penanganan galat. Di belakangnya ada alokasi sumber daya, pencatatan penggunaan, dan perlindungan. Cocokkan permintaan pengguna dengan layanan yang benar.

**Perhatikan pada gambar:** CLI or command interpreter allows direct command entry; Sometimes implemented in kernel, sometimes by systems

### Slide 009 — Bourne Shell Command Interpreter

![Slide 9: Bourne Shell Command Interpreter](gambar/pertemuan-02/slide-009.jpg)

Sistem operasi menyediakan cara berinteraksi dan layanan menjalankan program, I/O, berkas, komunikasi, serta penanganan galat. Di belakangnya ada alokasi sumber daya, pencatatan penggunaan, dan perlindungan. Cocokkan permintaan pengguna dengan layanan yang benar.

### Slide 010 — User Operating System Interface - GUI

![Slide 10: User Operating System Interface - GUI](gambar/pertemuan-02/slide-010.jpg)

Sistem operasi menyediakan cara berinteraksi dan layanan menjalankan program, I/O, berkas, komunikasi, serta penanganan galat. Di belakangnya ada alokasi sumber daya, pencatatan penggunaan, dan perlindungan. Cocokkan permintaan pengguna dengan layanan yang benar.

**Perhatikan pada gambar:** User-friendly desktop metaphor interface; Usually mouse, keyboard, and monitor

### Slide 011 — Touchscreen Interfaces

![Slide 11: Touchscreen Interfaces](gambar/pertemuan-02/slide-011.jpg)

Sistem operasi menyediakan cara berinteraksi dan layanan menjalankan program, I/O, berkas, komunikasi, serta penanganan galat. Di belakangnya ada alokasi sumber daya, pencatatan penggunaan, dan perlindungan. Cocokkan permintaan pengguna dengan layanan yang benar.

**Perhatikan pada gambar:** Touchscreen devices require new; Mouse not possible or not desired

### Slide 012 — The Mac OS X GUI

![Slide 12: The Mac OS X GUI](gambar/pertemuan-02/slide-012.jpg)

Sistem operasi menyediakan cara berinteraksi dan layanan menjalankan program, I/O, berkas, komunikasi, serta penanganan galat. Di belakangnya ada alokasi sumber daya, pencatatan penggunaan, dan perlindungan. Cocokkan permintaan pengguna dengan layanan yang benar.

### Slide 013 — System Calls

![Slide 13: System Calls](gambar/pertemuan-02/slide-013.jpg)

Aplikasi meminta kerja kernel lewat system call. CPU berpindah ke kernel mode untuk menjalankan layanan yang butuh hak istimewa, lalu kembali ke user mode; API adalah antarmuka pemrograman yang sering membungkus panggilan itu. Parameter dapat lewat register, blok memori, atau stack.

**Perhatikan pada gambar:** Programming interface to the services provided by the OS; Typically written in a high-level language (C or C++)

### Slide 014 — Example of System Calls

![Slide 14: Example of System Calls](gambar/pertemuan-02/slide-014.jpg)

Aplikasi meminta kerja kernel lewat system call. CPU berpindah ke kernel mode untuk menjalankan layanan yang butuh hak istimewa, lalu kembali ke user mode; API adalah antarmuka pemrograman yang sering membungkus panggilan itu. Parameter dapat lewat register, blok memori, atau stack.

**Perhatikan pada gambar:** System call sequence to copy the contents of one file to another file

### Slide 015 — Example of Standard API

![Slide 15: Example of Standard API](gambar/pertemuan-02/slide-015.jpg)

Aplikasi meminta kerja kernel lewat system call. CPU berpindah ke kernel mode untuk menjalankan layanan yang butuh hak istimewa, lalu kembali ke user mode; API adalah antarmuka pemrograman yang sering membungkus panggilan itu. Parameter dapat lewat register, blok memori, atau stack.

### Slide 016 — System Call Implementation

![Slide 16: System Call Implementation](gambar/pertemuan-02/slide-016.jpg)

Aplikasi meminta kerja kernel lewat system call. CPU berpindah ke kernel mode untuk menjalankan layanan yang butuh hak istimewa, lalu kembali ke user mode; API adalah antarmuka pemrograman yang sering membungkus panggilan itu. Parameter dapat lewat register, blok memori, atau stack.

**Perhatikan pada gambar:** Typically, a number associated with each system call; System-call interface maintains a table indexed according to

### Slide 017 — API – System Call – OS Relationship

![Slide 17: API – System Call – OS Relationship](gambar/pertemuan-02/slide-017.jpg)

Aplikasi meminta kerja kernel lewat system call. CPU berpindah ke kernel mode untuk menjalankan layanan yang butuh hak istimewa, lalu kembali ke user mode; API adalah antarmuka pemrograman yang sering membungkus panggilan itu. Parameter dapat lewat register, blok memori, atau stack.

### Slide 018 — System Call Parameter Passing

![Slide 18: System Call Parameter Passing](gambar/pertemuan-02/slide-018.jpg)

Aplikasi meminta kerja kernel lewat system call. CPU berpindah ke kernel mode untuk menjalankan layanan yang butuh hak istimewa, lalu kembali ke user mode; API adalah antarmuka pemrograman yang sering membungkus panggilan itu. Parameter dapat lewat register, blok memori, atau stack.

**Perhatikan pada gambar:** Often, more information is required than simply identity of desired; Exact type and amount of information vary according to OS

### Slide 019 — Parameter Passing via Table

![Slide 19: Parameter Passing via Table](gambar/pertemuan-02/slide-019.jpg)

Aplikasi meminta kerja kernel lewat system call. CPU berpindah ke kernel mode untuk menjalankan layanan yang butuh hak istimewa, lalu kembali ke user mode; API adalah antarmuka pemrograman yang sering membungkus panggilan itu. Parameter dapat lewat register, blok memori, atau stack.

### Slide 020 — Types of System Calls

![Slide 20: Types of System Calls](gambar/pertemuan-02/slide-020.jpg)

Aplikasi meminta kerja kernel lewat system call. CPU berpindah ke kernel mode untuk menjalankan layanan yang butuh hak istimewa, lalu kembali ke user mode; API adalah antarmuka pemrograman yang sering membungkus panggilan itu. Parameter dapat lewat register, blok memori, atau stack.

**Perhatikan pada gambar:** create process, terminate process; get process attributes, set process attributes

### Slide 021 — Types of System Calls (cont.)

![Slide 21: Types of System Calls (cont.)](gambar/pertemuan-02/slide-021.jpg)

Aplikasi meminta kerja kernel lewat system call. CPU berpindah ke kernel mode untuk menjalankan layanan yang butuh hak istimewa, lalu kembali ke user mode; API adalah antarmuka pemrograman yang sering membungkus panggilan itu. Parameter dapat lewat register, blok memori, atau stack.

**Perhatikan pada gambar:** create file, delete file; read, write, reposition

### Slide 022 — Types of System Calls (Cont.)

![Slide 22: Types of System Calls (Cont.)](gambar/pertemuan-02/slide-022.jpg)

Aplikasi meminta kerja kernel lewat system call. CPU berpindah ke kernel mode untuk menjalankan layanan yang butuh hak istimewa, lalu kembali ke user mode; API adalah antarmuka pemrograman yang sering membungkus panggilan itu. Parameter dapat lewat register, blok memori, atau stack.

**Perhatikan pada gambar:** Information maintenance; get time or date, set time or date

### Slide 023 — Types of System Calls (Cont.)

![Slide 23: Types of System Calls (Cont.)](gambar/pertemuan-02/slide-023.jpg)

Aplikasi meminta kerja kernel lewat system call. CPU berpindah ke kernel mode untuk menjalankan layanan yang butuh hak istimewa, lalu kembali ke user mode; API adalah antarmuka pemrograman yang sering membungkus panggilan itu. Parameter dapat lewat register, blok memori, atau stack.

**Perhatikan pada gambar:** Control access to resources; Get and set permissions

### Slide 024 — Examples of Windows and Unix System Calls

![Slide 24: Examples of Windows and Unix System Calls](gambar/pertemuan-02/slide-024.jpg)

Aplikasi meminta kerja kernel lewat system call. CPU berpindah ke kernel mode untuk menjalankan layanan yang butuh hak istimewa, lalu kembali ke user mode; API adalah antarmuka pemrograman yang sering membungkus panggilan itu. Parameter dapat lewat register, blok memori, atau stack.

### Slide 025 — Standard C Library Example

![Slide 25: Standard C Library Example](gambar/pertemuan-02/slide-025.jpg)

Linker menyatukan modul dan rujukan simbol menjadi program yang dapat dimuat. Loader menempatkan program ke memori dan menyiapkannya untuk berjalan. Pustaka dinamis dihubungkan saat pemuatan atau saat dibutuhkan, sehingga kode pustaka dapat dipakai bersama.

**Perhatikan pada gambar:** C program invoking printf() library call, which calls write() system call

### Slide 026 — Example: Arduino

![Slide 26: Example: Arduino](gambar/pertemuan-02/slide-026.jpg)

Slide ini memberi contoh tambahan untuk bagian tersebut. Perhatikan urutan pada gambar: identifikasi pelaku, sumber daya yang dipakai, lalu perubahan keadaan setelah setiap langkah. Hubungkan dengan slide sebelumnya sebelum menghafal istilahnya.

**Perhatikan pada gambar:** Programs (sketch) loaded via; Boot loader loads program

### Slide 027 — Example: FreeBSD

![Slide 27: Example: FreeBSD](gambar/pertemuan-02/slide-027.jpg)

Slide ini memberi contoh tambahan untuk bagian tersebut. Perhatikan urutan pada gambar: identifikasi pelaku, sumber daya yang dipakai, lalu perubahan keadaan setelah setiap langkah. Hubungkan dengan slide sebelumnya sebelum menghafal istilahnya.

**Perhatikan pada gambar:** User login -> invoke user’s choice of; Shell executes fork() system call to create

### Slide 028 — System Services

![Slide 28: System Services](gambar/pertemuan-02/slide-028.jpg)

Sistem operasi menyediakan cara berinteraksi dan layanan menjalankan program, I/O, berkas, komunikasi, serta penanganan galat. Di belakangnya ada alokasi sumber daya, pencatatan penggunaan, dan perlindungan. Cocokkan permintaan pengguna dengan layanan yang benar.

**Perhatikan pada gambar:** System programs provide a convenient environment for program; development and execution. They can be divided into:

### Slide 029 — System Services (cont.)

![Slide 29: System Services (cont.)](gambar/pertemuan-02/slide-029.jpg)

Sistem operasi menyediakan cara berinteraksi dan layanan menjalankan program, I/O, berkas, komunikasi, serta penanganan galat. Di belakangnya ada alokasi sumber daya, pencatatan penggunaan, dan perlindungan. Cocokkan permintaan pengguna dengan layanan yang benar.

**Perhatikan pada gambar:** Provide a convenient environment for program development and; Some of them are simply user interfaces to system calls; others

### Slide 030 — System Services (Cont.)

![Slide 30: System Services (Cont.)](gambar/pertemuan-02/slide-030.jpg)

Sistem operasi menyediakan cara berinteraksi dan layanan menjalankan program, I/O, berkas, komunikasi, serta penanganan galat. Di belakangnya ada alokasi sumber daya, pencatatan penggunaan, dan perlindungan. Cocokkan permintaan pengguna dengan layanan yang benar.

**Perhatikan pada gambar:** Text editors to create and modify files; Special commands to search contents of files or perform

### Slide 031 — System Services (Cont.)

![Slide 31: System Services (Cont.)](gambar/pertemuan-02/slide-031.jpg)

Sistem operasi menyediakan cara berinteraksi dan layanan menjalankan program, I/O, berkas, komunikasi, serta penanganan galat. Di belakangnya ada alokasi sumber daya, pencatatan penggunaan, dan perlindungan. Cocokkan permintaan pengguna dengan layanan yang benar.

**Perhatikan pada gambar:** Provide facilities like disk checking, process scheduling, error; Run in user context not kernel context

### Slide 032 — Linkers and Loaders

![Slide 32: Linkers and Loaders](gambar/pertemuan-02/slide-032.jpg)

Linker menyatukan modul dan rujukan simbol menjadi program yang dapat dimuat. Loader menempatkan program ke memori dan menyiapkannya untuk berjalan. Pustaka dinamis dihubungkan saat pemuatan atau saat dibutuhkan, sehingga kode pustaka dapat dipakai bersama.

**Perhatikan pada gambar:** Source code compiled into object files designed to be loaded into any; physical memory location – relocatable object file

### Slide 033 — The Role of the Linker and Loader

![Slide 33: The Role of the Linker and Loader](gambar/pertemuan-02/slide-033.jpg)

Linker menyatukan modul dan rujukan simbol menjadi program yang dapat dimuat. Loader menempatkan program ke memori dan menyiapkannya untuk berjalan. Pustaka dinamis dihubungkan saat pemuatan atau saat dibutuhkan, sehingga kode pustaka dapat dipakai bersama.

### Slide 034 — Why Applications are Operating System Specific

![Slide 34: Why Applications are Operating System Specific](gambar/pertemuan-02/slide-034.jpg)

Slide ini memberi contoh tambahan untuk bagian tersebut. Perhatikan urutan pada gambar: identifikasi pelaku, sumber daya yang dipakai, lalu perubahan keadaan setelah setiap langkah. Hubungkan dengan slide sebelumnya sebelum menghafal istilahnya.

**Perhatikan pada gambar:** Apps compiled on one system usually not executable on other; Each operating system provides its own unique system calls

### Slide 035 — Operating System Design and Implementation

![Slide 35: Operating System Design and Implementation](gambar/pertemuan-02/slide-035.jpg)

Mekanisme adalah cara sistem melakukan sesuatu; kebijakan menentukan pilihan mana yang diambil. Contohnya, mekanisme timer memberi kesempatan menjadwalkan ulang, sedangkan kebijakan penjadwal memilih proses berikutnya.

**Perhatikan pada gambar:** Design and Implementation of OS not “solvable”, but some; approaches have proven successful

### Slide 036 — Operating System Design and Implementation (Cont.)

![Slide 36: Operating System Design and Implementation (Cont.)](gambar/pertemuan-02/slide-036.jpg)

Mekanisme adalah cara sistem melakukan sesuatu; kebijakan menentukan pilihan mana yang diambil. Contohnya, mekanisme timer memberi kesempatan menjadwalkan ulang, sedangkan kebijakan penjadwal memilih proses berikutnya.

**Perhatikan pada gambar:** Important principle to separate; Policy: What will be done?

### Slide 037 — Implementation

![Slide 37: Implementation](gambar/pertemuan-02/slide-037.jpg)

Mekanisme adalah cara sistem melakukan sesuatu; kebijakan menentukan pilihan mana yang diambil. Contohnya, mekanisme timer memberi kesempatan menjadwalkan ulang, sedangkan kebijakan penjadwal memilih proses berikutnya.

**Perhatikan pada gambar:** Early OSes in assembly language; Then system programming languages like Algol, PL/1

### Slide 038 — Operating System Structure

![Slide 38: Operating System Structure](gambar/pertemuan-02/slide-038.jpg)

Struktur monolitik menaruh banyak layanan di kernel sehingga komunikasi antarkomponen cepat tetapi batas kegagalannya luas. Struktur berlapis memisahkan tanggung jawab; microkernel memindahkan lebih banyak layanan ke luar kernel; modul dapat dimuat saat perlu. Bandingkan fleksibilitas, isolasi, dan biaya komunikasi.

**Perhatikan pada gambar:** General-purpose OS is very large program; Various ways to structure ones

### Slide 039 — Monolithic Structure – Original UNIX

![Slide 39: Monolithic Structure – Original UNIX](gambar/pertemuan-02/slide-039.jpg)

Struktur monolitik menaruh banyak layanan di kernel sehingga komunikasi antarkomponen cepat tetapi batas kegagalannya luas. Struktur berlapis memisahkan tanggung jawab; microkernel memindahkan lebih banyak layanan ke luar kernel; modul dapat dimuat saat perlu. Bandingkan fleksibilitas, isolasi, dan biaya komunikasi.

**Perhatikan pada gambar:** UNIX – limited by hardware functionality, the original UNIX; operating system had limited structuring. The UNIX OS

### Slide 040 — Traditional UNIX System Structure

![Slide 40: Traditional UNIX System Structure](gambar/pertemuan-02/slide-040.jpg)

Struktur monolitik menaruh banyak layanan di kernel sehingga komunikasi antarkomponen cepat tetapi batas kegagalannya luas. Struktur berlapis memisahkan tanggung jawab; microkernel memindahkan lebih banyak layanan ke luar kernel; modul dapat dimuat saat perlu. Bandingkan fleksibilitas, isolasi, dan biaya komunikasi.

**Perhatikan pada gambar:** Beyond simple but not fully layered

### Slide 041 — Linux System Structure

![Slide 41: Linux System Structure](gambar/pertemuan-02/slide-041.jpg)

Struktur monolitik menaruh banyak layanan di kernel sehingga komunikasi antarkomponen cepat tetapi batas kegagalannya luas. Struktur berlapis memisahkan tanggung jawab; microkernel memindahkan lebih banyak layanan ke luar kernel; modul dapat dimuat saat perlu. Bandingkan fleksibilitas, isolasi, dan biaya komunikasi.

**Perhatikan pada gambar:** Monolithic plus modular design

### Slide 042 — Layered Approach

![Slide 42: Layered Approach](gambar/pertemuan-02/slide-042.jpg)

Struktur monolitik menaruh banyak layanan di kernel sehingga komunikasi antarkomponen cepat tetapi batas kegagalannya luas. Struktur berlapis memisahkan tanggung jawab; microkernel memindahkan lebih banyak layanan ke luar kernel; modul dapat dimuat saat perlu. Bandingkan fleksibilitas, isolasi, dan biaya komunikasi.

**Perhatikan pada gambar:** The operating system is divided; into a number of layers (levels),

### Slide 043 — Microkernels

![Slide 43: Microkernels](gambar/pertemuan-02/slide-043.jpg)

Struktur monolitik menaruh banyak layanan di kernel sehingga komunikasi antarkomponen cepat tetapi batas kegagalannya luas. Struktur berlapis memisahkan tanggung jawab; microkernel memindahkan lebih banyak layanan ke luar kernel; modul dapat dimuat saat perlu. Bandingkan fleksibilitas, isolasi, dan biaya komunikasi.

**Perhatikan pada gambar:** Moves as much from the kernel into user space; Mach example of microkernel

### Slide 044 — Microkernel System Structure

![Slide 44: Microkernel System Structure](gambar/pertemuan-02/slide-044.jpg)

Struktur monolitik menaruh banyak layanan di kernel sehingga komunikasi antarkomponen cepat tetapi batas kegagalannya luas. Struktur berlapis memisahkan tanggung jawab; microkernel memindahkan lebih banyak layanan ke luar kernel; modul dapat dimuat saat perlu. Bandingkan fleksibilitas, isolasi, dan biaya komunikasi.

### Slide 045 — Modules

![Slide 45: Modules](gambar/pertemuan-02/slide-045.jpg)

Struktur monolitik menaruh banyak layanan di kernel sehingga komunikasi antarkomponen cepat tetapi batas kegagalannya luas. Struktur berlapis memisahkan tanggung jawab; microkernel memindahkan lebih banyak layanan ke luar kernel; modul dapat dimuat saat perlu. Bandingkan fleksibilitas, isolasi, dan biaya komunikasi.

**Perhatikan pada gambar:** Many modern operating systems implement loadable kernel; Uses object-oriented approach

### Slide 046 — Hybrid Systems

![Slide 46: Hybrid Systems](gambar/pertemuan-02/slide-046.jpg)

Struktur monolitik menaruh banyak layanan di kernel sehingga komunikasi antarkomponen cepat tetapi batas kegagalannya luas. Struktur berlapis memisahkan tanggung jawab; microkernel memindahkan lebih banyak layanan ke luar kernel; modul dapat dimuat saat perlu. Bandingkan fleksibilitas, isolasi, dan biaya komunikasi.

**Perhatikan pada gambar:** Most modern operating systems are actually not one pure model; Hybrid combines multiple approaches to address

### Slide 047 — macOS and iOS Structure

![Slide 47: macOS and iOS Structure](gambar/pertemuan-02/slide-047.jpg)

Struktur monolitik menaruh banyak layanan di kernel sehingga komunikasi antarkomponen cepat tetapi batas kegagalannya luas. Struktur berlapis memisahkan tanggung jawab; microkernel memindahkan lebih banyak layanan ke luar kernel; modul dapat dimuat saat perlu. Bandingkan fleksibilitas, isolasi, dan biaya komunikasi.

### Slide 048 — Darwin

![Slide 48: Darwin](gambar/pertemuan-02/slide-048.jpg)

Slide ini memberi contoh tambahan untuk bagian tersebut. Perhatikan urutan pada gambar: identifikasi pelaku, sumber daya yang dipakai, lalu perubahan keadaan setelah setiap langkah. Hubungkan dengan slide sebelumnya sebelum menghafal istilahnya.

### Slide 049 — Android

![Slide 49: Android](gambar/pertemuan-02/slide-049.jpg)

Slide ini memberi contoh tambahan untuk bagian tersebut. Perhatikan urutan pada gambar: identifikasi pelaku, sumber daya yang dipakai, lalu perubahan keadaan setelah setiap langkah. Hubungkan dengan slide sebelumnya sebelum menghafal istilahnya.

**Perhatikan pada gambar:** Developed by Open Handset Alliance (mostly Google); Based on Linux kernel but modified

### Slide 050 — Android Architecture

![Slide 50: Android Architecture](gambar/pertemuan-02/slide-050.jpg)

Slide ini memberi contoh tambahan untuk bagian tersebut. Perhatikan urutan pada gambar: identifikasi pelaku, sumber daya yang dipakai, lalu perubahan keadaan setelah setiap langkah. Hubungkan dengan slide sebelumnya sebelum menghafal istilahnya.

### Slide 051 — Building and Booting an Operating System

![Slide 51: Building and Booting an Operating System](gambar/pertemuan-02/slide-051.jpg)

Saat komputer menyala, firmware memulai boot loader. Boot loader memuat kernel; kernel menyiapkan memori, perangkat, dan layanan awal. Bedakan tahap pemuatan sistem ini dari menjalankan aplikasi biasa.

**Perhatikan pada gambar:** Operating systems generally designed to run on a class of systems; with variety of perpherals

### Slide 052 — Building and Booting Linux

![Slide 52: Building and Booting Linux](gambar/pertemuan-02/slide-052.jpg)

Saat komputer menyala, firmware memulai boot loader. Boot loader memuat kernel; kernel menyiapkan memori, perangkat, dan layanan awal. Bedakan tahap pemuatan sistem ini dari menjalankan aplikasi biasa.

**Perhatikan pada gambar:** Download Linux source code (http://www.kernel.org); Configure kernel via “make menuconfig”

### Slide 053 — System Boot

![Slide 53: System Boot](gambar/pertemuan-02/slide-053.jpg)

Saat komputer menyala, firmware memulai boot loader. Boot loader memuat kernel; kernel menyiapkan memori, perangkat, dan layanan awal. Bedakan tahap pemuatan sistem ini dari menjalankan aplikasi biasa.

**Perhatikan pada gambar:** When power initialized on system, execution starts at a fixed memory; Operating system must be made available to hardware so hardware can

### Slide 054 — Operating-System Debugging

![Slide 54: Operating-System Debugging](gambar/pertemuan-02/slide-054.jpg)

Pengukuran kinerja menunjukkan bagian yang benar-benar lambat. Tracing mencatat peristiwa saat sistem berjalan; debugging mencari penyebab galat. Data hasil ukur lebih kuat daripada menebak dari gejala.

**Perhatikan pada gambar:** Debugging is finding and fixing errors, or bugs; Also performance tuning

### Slide 055 — Performance Tuning

![Slide 55: Performance Tuning](gambar/pertemuan-02/slide-055.jpg)

Pengukuran kinerja menunjukkan bagian yang benar-benar lambat. Tracing mencatat peristiwa saat sistem berjalan; debugging mencari penyebab galat. Data hasil ukur lebih kuat daripada menebak dari gejala.

**Perhatikan pada gambar:** Improve performance by removing bottlenecks; OS must provide means of computing and displaying measures of system

### Slide 056 — Tracing

![Slide 56: Tracing](gambar/pertemuan-02/slide-056.jpg)

Pengukuran kinerja menunjukkan bagian yang benar-benar lambat. Tracing mencatat peristiwa saat sistem berjalan; debugging mencari penyebab galat. Data hasil ukur lebih kuat daripada menebak dari gejala.

**Perhatikan pada gambar:** Collects data for a specific event, such as steps involved in a system; strace – trace system calls invoked by a process

### Slide 057 — BCC

![Slide 57: BCC](gambar/pertemuan-02/slide-057.jpg)

Pengukuran kinerja menunjukkan bagian yang benar-benar lambat. Tracing mencatat peristiwa saat sistem berjalan; debugging mencari penyebab galat. Data hasil ukur lebih kuat daripada menebak dari gejala.

**Perhatikan pada gambar:** Debugging interactions between user-level and kernel code nearly; impossible without toolset that understands both and an instrument

### Slide 058 — Linux bcc/BPF Tracing Tools

![Slide 58: Linux bcc/BPF Tracing Tools](gambar/pertemuan-02/slide-058.jpg)

Pengukuran kinerja menunjukkan bagian yang benar-benar lambat. Tracing mencatat peristiwa saat sistem berjalan; debugging mencari penyebab galat. Data hasil ukur lebih kuat daripada menebak dari gejala.

### Slide 059 — End of Chapter 2

![Slide 59: End of Chapter 2](gambar/pertemuan-02/slide-059.jpg)

Slide ini memetakan posisi topik dalam bab. Pakai judul-judulnya sebagai daftar cek; utamakan hubungan sebab-akibat dan contoh, bukan hafalan urutan.

## Contoh soal dan pembahasan

### Contoh 1 — Membuat proses dari shell (adaptasi UTS 2018/2022)
**Soal.** Sebutkan panggilan yang diperlukan ketika shell menjalankan sebuah perintah dan menunggu hasilnya.

**Pembahasan.** Shell memanggil `fork()` untuk membuat anak. Pada cabang anak, `exec()` mengganti program anak dengan perintah yang diminta. Pada cabang induk, `wait()` menunggu dan mengambil status keluarnya anak. `exec()` yang berhasil tidak kembali ke kode program lama.

### Contoh 2 — Memilih desain kernel (latihan dari kisi-kisi)
**Soal.** Layanan driver mudah diganti dan kesalahan driver diharapkan lebih terisolasi. Desain apa yang paling sesuai?

**Pembahasan.** Microkernel menempatkan lebih banyak layanan di proses pengguna sehingga isolasi lebih kuat, tetapi komunikasi antarproses menambah biaya. Jika hanya butuh memasang fitur kernel saat diperlukan, modul kernel lebih langsung. Pilihan bergantung pada prioritas isolasi dan performa.

## Rujukan

- Slide kuliah `K2.pdf` halaman 1–59 dari paket Sensei.

- *Operating System Concepts*, edisi ke-10, Silberschatz, Galvin, dan Gagne, sebagaimana tercantum pada slide.

- `Kisi kisi.pdf` dan kumpulan contoh UTS 2018, 2019, 2021, 2022 dalam paket Sensei. Soal yang diberi label "adaptasi" mengikuti jenis/topik soal terdahulu; angka dan redaksinya tidak diklaim sama dengan soal asli.
