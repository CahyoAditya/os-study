# Rangkuman kisi-kisi UTS Sistem Operasi

Sumber: `os-sumber/OS/Kisi kisi.pdf` dan materi K1–K7. Kisi-kisi memuat 33 soal dalam 100 menit. Saat ujian, hanya catatan *tulisan tangan bertinta basah* di kertas Letter/A4/F4 yang boleh dibawa.

## 1. Peran dan proteksi SO (K1)

- SO membagi CPU, memori, penyimpanan, dan I/O; mengawasi program agar tidak saling merusak.
- *User mode* untuk aplikasi; *kernel mode* untuk instruksi istimewa. Alur layanan: aplikasi → system call/trap → kernel memeriksa izin dan parameter → kembali ke aplikasi.
- Interrupt perangkat/timer dan exception mengembalikan kendali ke kernel. Timer mencegah satu program menguasai CPU terus-menerus.
- Multiprogramming mengisi waktu CPU ketika proses lain menunggu I/O; time sharing memberi giliran CPU pendek agar aplikasi terasa berjalan bersama.
- Boot: firmware → boot loader → kernel → layanan. Hypervisor menjalankan beberapa SO tamu di satu mesin fisik.
- Soal skenario: tentukan sumber daya, pihak yang berwenang, jalur permintaan, dan perlindungannya.

## 2. Layanan dan struktur SO (K2)

- Layanan: eksekusi program, I/O, berkas, komunikasi, deteksi error, alokasi, pencatatan pemakaian, proteksi, keamanan.
- API ialah antarmuka fungsi bagi pemrogram; system call ialah pintu ke kernel. Tidak semua fungsi API memanggil kernel.
- Kategori system call: proses, berkas, perangkat, informasi, komunikasi, proteksi. Parameter bisa lewat register, blok memori, atau stack; kernel tetap memeriksanya.
- Loader menyiapkan program; pustaka dinamis menyediakan kode bersama. Keduanya bukan system call.
- Monolitik: jalur cepat, risiko bug kernel luas. Berlapis: batas jelas, tetapi lintas lapisan bisa mahal. Microkernel: isolasi lebih baik, pesan dan context switch bisa mahal. Modul kernel: mudah ditambah, tetap berhak tinggi.
- *Policy* menentukan pilihan; *mechanism* menjalankannya. Model thread: many-to-one tidak paralel di banyak inti; one-to-one bisa paralel tetapi lebih mahal; many-to-many lentur tetapi rumit.

## 3. State proses dan trace kode (K3)

- Program = instruksi pasif; proses = program yang berjalan. PCB menyimpan state, register, program counter, penjadwalan, memori, dan I/O. Context switch menyimpan state lama lalu memulihkan state baru.
- Alur state: `new → ready → running → terminated`; `running → waiting` saat menunggu I/O; `waiting → ready` ketika I/O selesai; `running → ready` saat dipreempt. *Ready* berarti siap tetapi belum mendapat CPU.
- Ruang alamat: kode, data, heap, stack. `fork()` menghasilkan 0 di anak, PID anak di induk, atau −1 saat gagal. Variabel induk dan anak terpisah; urutan cetaknya tidak pasti.
- `exec()` yang berhasil mengganti program pada proses yang sama; baris lama sesudahnya tidak berjalan. `wait()` mengambil status anak; `exit()` mengakhiri proses.
- Trace: gambar pohon → pecah setiap cabang yang mencapai `fork()` → catat nilai tiap cabang → hentikan setelah `exec()` berhasil/`exit()` → hitung keluaran, jangan tebak urutan.
- Zombie: anak selesai tetapi status belum diambil. Orphan: induk selesai saat anak masih hidup. IPC: shared memory cepat tetapi butuh sinkronisasi; message passing lebih terisolasi; pipe mengalirkan byte.

## 4. Thread dan speedup (K4)

- Thread satu proses berbagi kode, data, berkas; masing-masing punya register, program counter, stack. Konkurensi = maju bergantian; paralelisme = berjalan bersamaan.
- Data parallelism membagi data; task parallelism membagi tugas. Thread pool memakai ulang pekerja.
- Pthreads: `void *worker(void *arg)`; `pthread_create(&tid, NULL, worker, arg)` membuat; `pthread_join(tid, NULL)` menunggu. Pastikan argumen masih hidup dan akses data bersama disinkronkan.
- Kerja serial `W`, `n` thread, overhead/thread `o`: pembentukan paralel `T=W/n+o`; pembentukan serial `T=W/n+n·o`; `speedup=W/T`. Pilih rumus sesuai cerita soal.
- Amdahl: `speedup(N)=1/[s+(1−s)/N]`; `s` bagian serial, `N` pemroses. Batas saat `N` sangat besar: `1/s` bila `s>0`.

## 5. Penjadwalan CPU (K5)

- Konvensi kisi-kisi: semua tiba waktu 0, antrean awal P1, P2, … kecuali disebut lain. SJF *non-preemptive*.
- FCFS: urut kedatangan; proses panjang dapat menahan yang pendek. SJF: pilih burst terpendek dari yang sudah tiba. RR: jalankan maksimal quantum `q`; proses belum selesai masuk belakang antrean.
- Buat Gantt chart dahulu. `turnaround=completion−arrival`; `waiting=turnaround−burst`; `response=first_start−arrival`; rata-rata tunggu = jumlah waiting / jumlah proses.
- Pada RR, waiting mencakup semua jeda, bukan hanya sebelum giliran pertama. Quantum kecil menambah context switch; quantum besar mendekati FCFS.

## 6. Penjadwalan RM dan EDF (K5–K6)

- Tugas periodik `Pi`: kerja `t_i`, periode `p_i`; instance ke-`k` rilis `k·p_i`, deadline `(k+1)·p_i` menurut kisi-kisi.
- RM: periode lebih pendek lebih tinggi prioritasnya, preemptive. EDF: deadline absolut terdekat; saat sama, teruskan yang sedang berjalan. Dalam soal ini, instance yang terlambat tetap dikerjakan hingga selesai.
- Timeline: tulis rilis/deadline → perbarui sisa kerja pada setiap peristiwa → pilih proses → catat segmen dan deadline miss. RM berhenti setelah instance pertama P2 selesai; EDF berhenti pada `KPK(p1,p2)`.
- Utilisasi `U=Σ(t_i/p_i)`. Batas *cukup* RM: `U≤n(2^(1/n)−1)` untuk `n` tugas ideal; dua tugas ≈ `0,828`. Di atas batas ini belum tentu gagal. EDF satu CPU ideal: `U≤1` pada asumsi model tersebut.
- Deadline miss bila waktu selesai melewati deadline absolut; periksa timeline, jangan simpulkan hanya dari batas RM.

## 7. Sinkronisasi dan deadlock (K7)

- Race condition: hasil bergantung pada urutan akses data bersama. Critical section perlu mutual exclusion, progress, dan bounded waiting.
- Semaphore `wait(S)` mengambil izin atau menunggu; `signal(S)` mengembalikan izin/membangunkan penunggu. Saat trace, catat nilai awal, operasi, siapa tertahan, siapa dibangunkan. Proses tertahan belum melewati `wait`.
- Mutex awal `1`: `wait(mutex) → bagian kritis → signal(mutex)`.
- Bounded buffer kapasitas `N`: awal `empty=N, full=0, mutex=1`. Produsen: `wait(empty) → wait(mutex) → isi → signal(mutex) → signal(full)`. Konsumen: `wait(full) → wait(mutex) → ambil → signal(mutex) → signal(empty)`.
- Empat syarat deadlock: mutual exclusion, hold and wait, no preemption, circular wait. Pencegahan memutus salah satunya; avoidance memeriksa keamanan sebelum memberi sumber daya; detection mencari deadlock yang sudah mungkin terjadi.
- RAG: `P→R` = meminta, `R→P` = dialokasikan. Tanpa siklus = tidak deadlock. Siklus dengan satu instance tiap jenis = deadlock; dengan beberapa instance = kemungkinan saja.
- Banker's: `Need=Max−Allocation`; mulai `Work=Available`. Cari proses belum selesai dengan `Need≤Work` pada *setiap jenis*; anggap selesai, lalu `Work+=Allocation`. Ulangi. Semua selesai = aman; macet = tidak aman, belum tentu sudah deadlock.
- Permintaan baru: cek `Request≤Need` dan `Request≤Available`, alokasikan sementara, lalu uji keadaan aman. Kabuli hanya jika tetap aman.

## Prioritas untuk disalin tangan

1. Rumus waiting, turnaround, response, speedup, Amdahl, utilisasi, dan Banker's.
2. Aturan antrean RR, prioritas RM/EDF, deadline, dan batas cukup RM.
3. State proses, hasil `fork()`, urutan semaphore, kondisi deadlock, serta arah panah RAG.

Ini rangkuman dan cara mengerjakan, bukan salinan soal UTS.
