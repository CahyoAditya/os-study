# Cheatsheet UTS Sistem Operasi — menurut kemampuan yang diuji

Sumber utama: `os-sumber/OS/Kisi kisi.pdf` (11 Oktober 2026), materi K1–K7, dan bab terkait di repositori ini. Kisi-kisi menyebut 33 soal dalam 100 menit. **Saat ujian, hanya cheatsheet ukuran Letter/A4/F4 yang ditulis tangan dengan tinta basah yang boleh dipakai.** Dokumen ini adalah bahan untuk memilih dan menyalin bagian penting ke lembar tersebut, bukan lembar cetak yang boleh langsung dibawa.

## 1. Menjelaskan peran sistem operasi dan mekanisme proteksinya (pertemuan 1)

**Peran.** Sistem operasi (SO) adalah perantara program/pengguna dengan perangkat keras. Sebagai *resource allocator*, SO memutuskan pembagian CPU, memori, penyimpanan, dan perangkat I/O ketika permintaan bersaing. Sebagai *control program*, SO mengawasi eksekusi agar satu program tidak merusak program atau sistem lain. Tujuan praktisnya: nyaman dipakai, efisien, dan aman.

**Dual-mode.** CPU membedakan *user mode* (program biasa) dari *kernel mode* (SO). Instruksi istimewa, misalnya mengatur perangkat I/O, tabel memori, atau timer, hanya sah di kernel mode. Permintaan sah dari program melewati *system call*: program memicu trap → CPU masuk kernel mode → kernel memeriksa permintaan dan hak akses → kernel bekerja → kembali ke user mode. Interrupt perangkat dan exception juga dapat membawa kendali ke kernel. Mode saja belum cukup: kernel juga harus memeriksa alamat, izin, dan parameter.

**Timer** diatur kernel untuk memicu interrupt setelah waktu tertentu. Dengan demikian, program yang berputar terus tidak memegang CPU selamanya; kernel dapat mengambil kendali dan melakukan penjadwalan. *Multiprogramming* menyimpan beberapa pekerjaan di memori agar CPU dapat mengerjakan pekerjaan lain saat satu pekerjaan menunggu I/O. *Multitasking/time sharing* memberi giliran CPU cukup cepat sehingga beberapa aplikasi terasa berjalan bersama. Keduanya tidak mensyaratkan banyak CPU.

**Saat menyala:** firmware memulai *boot loader* → boot loader memuat kernel → kernel menginisialisasi sistem dan memulai layanan. **Hypervisor** menyediakan mesin virtual sehingga beberapa sistem operasi tamu dapat berbagi satu perangkat fisik; setiap tamu tetap memerlukan pengaturan dan isolasi sumber daya.

**Cara menjawab skenario:** tentukan sumber daya yang diperebutkan, siapa yang berwenang mengubahnya, bagaimana program meminta layanan, lalu mekanisme yang mencegah penyalahgunaan. Contoh: aplikasi ingin menulis langsung ke pengendali disk → instruksi itu tidak boleh dijalankan di user mode; aplikasi harus meminta layanan kernel. Aplikasi CPU-bound berjalan tak berakhir → timer memberi kesempatan kernel mengambil alih.

## 2. Mengaitkan skenario dengan layanan SO dan menilai desainnya (pertemuan 2)

| Skenario | Layanan atau konsep yang dicari |
|---|---|
| Menjalankan berkas program | *Program execution*: loader memuat program, menyiapkan memori dan titik awal, lalu menjalankannya. |
| Membaca keyboard, mengirim ke printer | Operasi I/O melalui SO dan driver. |
| Membuat, membaca, menghapus berkas | Manipulasi sistem berkas. |
| Dua proses bertukar data | Komunikasi/IPC. |
| Gagal baca disk atau instruksi tidak sah | Deteksi dan penanganan error. |
| Banyak proses meminta CPU/memori | Alokasi sumber daya; pemakaian dapat dicatat (*accounting*). |
| Pembatasan akses data | Protection dan security. |

**System call** adalah pintu masuk ke layanan kernel; **API** adalah antarmuka fungsi untuk pemrogram. Pemanggilan API dapat membungkus satu atau beberapa system call, tetapi tidak semua fungsi API perlu masuk kernel. Kategori system call: kendali proses, pengelolaan berkas, pengelolaan perangkat, pemeliharaan informasi, komunikasi, dan proteksi. Parameter dapat dikirim lewat register, blok/tabel di memori yang alamatnya diteruskan, atau stack; yang penting kernel tetap memeriksa parameter dari user space.

**Loader vs pustaka dinamis.** Loader menyiapkan program untuk dieksekusi. *Dynamically linked library* (DLL/shared library) adalah kode pustaka yang dihubungkan saat pemuatan atau saat program berjalan, sehingga kode pustaka dapat dipakai bersama; jangan samakan dengan system call.

| Desain | Kelebihan yang lazim | Harga/risiko yang dinilai |
|---|---|---|
| Monolitik | Komponen kernel dapat saling memanggil langsung; jalur pendek. | Komponen saling erat; bug di kernel berdampak besar. |
| Berlapis (*layered*) | Batas antarlapis memudahkan penalaran dan pengujian. | Menentukan batas lapisan tidak selalu mudah; melewati banyak lapisan dapat menambah biaya. |
| Microkernel | Layanan dipindah ke proses user; isolasi dan pemeliharaan lebih mudah. | Pesan antarproses dan perpindahan konteks dapat menambah biaya. |
| *Loadable kernel modules* | Fitur/driver dapat ditambahkan atau dilepas tanpa membangun seluruh kernel. | Modul tetap berjalan dengan hak kernel; kualitasnya penting. |

**Policy vs mechanism:** policy menentukan *apa* yang dipilih (misalnya siapa diprioritaskan); mechanism menyediakan *cara* menjalankan pilihan itu (misalnya timer dan context switch). **Model thread:** *many-to-one* murah di level pengguna tetapi satu panggilan blocking dapat menahan semua thread dan tidak memanfaatkan banyak inti; *one-to-one* memungkinkan paralelisme dan blocking terpisah dengan biaya thread kernel lebih besar; *many-to-many* memetakan banyak thread pengguna ke sejumlah thread kernel dan lebih rumit dikelola. **Quantum RR:** terlalu kecil → terlalu banyak context switch; terlalu besar → RR mendekati FCFS dan respons interaktif memburuk. Pilih menurut tujuan, bukan menyebut satu desain selalu terbaik.

## 3. Menentukan state proses, menelusuri `fork()`, dan menganalisis kode (pertemuan 3)

**Program** adalah instruksi pasif di penyimpanan; **proses** adalah program yang sedang dieksekusi beserta state dan sumber dayanya. Ruang alamat proses biasanya memuat text/kode, data global, heap (alokasi dinamis), dan stack (pemanggilan fungsi/variabel lokal). **PCB** mencatat identitas, state, program counter, register, informasi penjadwalan, memori, I/O, dan akuntansi. Saat **context switch**, SO menyimpan keadaan proses lama di PCB lalu memulihkan keadaan proses berikutnya; pergantian ini punya biaya.

```text
new --diterima--> ready --dipilih CPU--> running --selesai--> terminated
                     ^                 |  
                     |--preemption-----|   \--menunggu I/O/event--> waiting
                     ^                                      |
                     +------------- I/O/event selesai -----+
```

**Tes state cepat:** siap memakai CPU tetapi belum mendapat giliran = **ready**; sedang mendapat CPU = **running**; tidak bisa lanjut sebelum I/O/event selesai = **waiting**. Proses yang di-*preempt* kembali ke **ready**, bukan otomatis waiting. Selesainya I/O memindahkan waiting → ready, bukan langsung running.

**`fork()`** membuat proses anak. Setelah berhasil, pemanggilan yang sama kembali **0 di anak** dan **PID anak (>0) di induk**; gagal menghasilkan −1 di induk tanpa anak baru. Anak dan induk lanjut dari baris setelah `fork()` dengan salinan state yang terpisah. Jangan menganggap urutan cetak anak–induk pasti jika tidak ada sinkronisasi. **`exec()`** mengganti citra program dalam proses yang memanggilnya; jika berhasil, baris sesudahnya pada citra lama tidak dijalankan. **`wait()`** menunggu/mengambil status anak; **`exit()`** mengakhiri proses.

**Langkah trace:** (1) gambar pohon proses; (2) pada setiap `fork()` yang *dicapai*, pecah cabang induk/anak; (3) catat nilai balik dan variabel per cabang; (4) coret jalur setelah `exec()` berhasil atau `exit()`; (5) hitung jumlah keluaran, kemudian pisahkan jumlah dari urutan. Contoh:

```c
int x = 1;
pid_t p = fork();
if (p == 0) x += 2; else x += 1;
printf("%d\n", x);
```

Hasilnya dua baris, **3 dan 2**, dalam urutan yang tidak dijamin. Dua `fork()` tanpa kondisi yang membatasi cabang menghasilkan paling banyak 4 proses total bila semuanya berhasil, tetapi hitung titik yang benar-benar dicapai.

**Zombie** = anak sudah selesai, tetapi induk belum mengambil statusnya dengan `wait()`; entri proses masih tertahan. **Orphan** = induk selesai lebih dulu saat anak masih hidup; anak kemudian diadopsi oleh proses pengasuh sistem. **IPC:** *shared memory* cepat untuk data bersama tetapi perlu sinkronisasi; *message passing* memindahkan pesan melalui mekanisme SO dan memudahkan isolasi; *pipe* berupa aliran byte antarproses, lazimnya satu arah per pipe. Pilih berdasar hubungan proses, kebutuhan kecepatan, dan sinkronisasi.

## 4. Melengkapi Pthreads, menghitung speedup, dan menilai paralelisme (pertemuan 4)

**Thread** berbagi kode, data, dan berkas dalam satu proses, tetapi punya register, program counter, dan stack sendiri. Manfaat: responsif, berbagi sumber daya, pembuatan/pergantian lebih ringan daripada proses, dan dapat memanfaatkan banyak inti. **Konkurensi** berarti tugas dapat maju bergantian; **paralelisme** berarti eksekusi benar-benar bersamaan pada lebih dari satu unit kerja. *Data parallelism* membagi data serupa ke pekerja; *task parallelism* membagi jenis pekerjaan berbeda. *Thread pool* membuat pekerja lebih awal dan memakai ulang pekerja untuk menghindari biaya membuat thread setiap tugas; *implicit threading* menyerahkan pengelolaan thread ke runtime/pustaka.

**Kerangka Pthreads yang harus dikenali:**

```c
#include <pthread.h>
void *worker(void *arg) { /* pekerjaan */ return NULL; }

pthread_t tid;
pthread_create(&tid, NULL, worker, arg); /* membuat thread */
pthread_join(tid, NULL);                 /* menunggu thread selesai */
```

`worker` menerima `void *` dan mengembalikan `void *`; `pthread_create` menerima alamat `pthread_t`, atribut, fungsi, dan argumennya. Pastikan argumen masih hidup ketika dibaca pekerja. `join` diperlukan sebelum memakai hasil yang harus sudah selesai. Jangan membaca nilai bersama tanpa sinkronisasi.

**Overhead pembentukan thread menurut kisi-kisi:** jika pekerjaan serial murni memakan waktu `W`, dibagi rata ke `n` thread, dan overhead tiap thread `o`:

| Cara pembentukan | Waktu total menurut soal | Speedup terhadap `W` |
|---|---:|---:|
| Paralel/bersamaan | `W/n + o` | `W/(W/n+o)` |
| Serial/satu per satu | `W/n + n·o` | `W/(W/n+n·o)` |

Contoh `W=120 ms, n=4, o=5 ms`: paralel = `30+5=35 ms`, speedup ≈ `3,43`; serial = `30+20=50 ms`, speedup = `2,4`. Periksa dulu apakah soal berkata pembuatan paralel atau serial. Rumus ini mengabaikan biaya lain kecuali soal menyebutnya.

**Hukum Amdahl:** jika fraksi `s` dari pekerjaan harus serial dan sisanya dapat diparalelkan pada `N` pemroses, `Speedup(N) = 1 / [s + (1-s)/N]`. Batas saat `N` sangat besar adalah `1/s` untuk `s>0`. Contoh `s=0,2` dan `N=4`: `1/(0,2+0,8/4)=2,5`, bukan 4. Jika diminta memilih jumlah thread, bandingkan kenaikan waktu yang benar-benar didapat dengan overhead dan batas serialnya.

## 5. Menyusun jadwal CPU dan menghitung waktu tunggu (pertemuan 5)

**Konvensi kisi-kisi:** semua tiba di waktu 0; antrean awal P1, P2, … kecuali disebut lain. SJF **non-preemptive**. RR: proses yang jatahnya habis dan belum selesai masuk **belakang** antrean. Buat Gantt chart lebih dulu, lalu tabel selesai/tunggu.

Untuk proses `i`: `turnaround_i = completion_i - arrival_i`; `waiting_i = turnaround_i - burst_i`; `response_i = first_start_i - arrival_i`. Rata-rata waktu tunggu = `Σ waiting_i / jumlah proses`. Jika tidak ada I/O dan semua tiba 0, rumus ini aman bahkan untuk RR yang terpotong-potong. Jangan menghitung hanya awal pertama proses RR sebagai total waktu tunggu.

| Algoritma | Prosedur | Kekuatan dan risiko |
|---|---|---|
| FCFS | Urut kedatangan; satu proses berjalan sampai burst selesai. | Sederhana; proses panjang di depan menyebabkan *convoy effect*. |
| SJF | Pilih burst terpendek di antara yang sudah tiba; non-preemptive menurut kisi-kisi. | Rata-rata tunggu kecil jika burst diketahui; perkiraan burst bisa salah dan pekerjaan panjang dapat menunggu lama. |
| RR | Jalan maksimum `q`, lalu bila belum selesai pindah ke belakang antrean. | Respons lebih baik untuk interaktif; `q` kecil menambah context switch, `q` sangat besar mendekati FCFS. |

**Contoh tunggal:** burst P1=5, P2=3, P3=1; semua tiba 0; RR `q=2`.

| Algoritma | Gantt chart (waktu: proses) | Tunggu P1,P2,P3 | Rata-rata |
|---|---|---|---:|
| FCFS | `0–5 P1 | 5–8 P2 | 8–9 P3` | `0,5,8` | `13/3 ≈ 4,33` |
| SJF | `0–1 P3 | 1–4 P2 | 4–9 P1` | `4,1,0` | `5/3 ≈ 1,67` |
| RR | `0–2 P1 | 2–4 P2 | 4–5 P3 | 5–7 P1 | 7–8 P2 | 8–9 P1` | `4,5,4` | `13/3 ≈ 4,33` |

Pada RR, P1 selesai di 9, sehingga tunggunya `9−0−5=4`. Nilai tunggu rata-rata RR pada contoh ini kebetulan sama dengan FCFS; itu bukan aturan umum. Saat menilai algoritma, sebut tujuan: rata-rata tunggu, respons interaktif, keadilan, deadline, atau biaya context switch.

## 6. Menyusun timeline RM/EDF dan menemukan deadline miss (pertemuan 5–6)

Proses periodik `Pi` mempunyai waktu eksekusi `t_i` dan periode `p_i`. Instance ke-`k` tiba pada `k·p_i`, dengan deadline `(k+1)·p_i`, sesuai kisi-kisi. **RM**: periode lebih pendek mendapat prioritas statis lebih tinggi, preemptive. **EDF**: pilih instance dengan deadline absolut terdekat; jika deadline sama, lanjutkan proses yang sedang berjalan. Pada soal ini, instance yang melewati deadline **tetap dijalankan sampai selesai**.

**Langkah tabel:** tulis semua waktu rilis dan deadline → pada setiap rilis, penyelesaian, atau titik pergantian, perbarui sisa kerja → pilih proses menurut RM/EDF → tulis *satu segmen eksekusi per baris* dengan waktu mulai `tawal`; jika tidak ada yang siap tulis `___`. Untuk RM, berhenti saat instance **pertama P2** selesai, lalu nyatakan waktu deadline miss dan selesai P2. Untuk EDF, lanjutkan sampai **KPK kedua periode**, yaitu satu hiperperiode.

**Utilisasi:** `U = Σ(t_i/p_i)`. Untuk `n` tugas periodik independen dengan deadline sama dengan periode, batas cukup Liu–Layland untuk RM: `U ≤ n(2^(1/n)−1)` menjamin dapat dijadwalkan oleh RM dalam model ideal. Untuk dua tugas, batasnya `≈0,828`. Jika `U` lebih besar, **belum otomatis gagal**: periksa timeline. EDF pada satu CPU ideal mempunyai uji `U ≤ 1` untuk model tugas periodik yang memenuhi asumsi tersebut; tetap patuhi konvensi dan cek deadline pada soal.

**Contoh sesuai pola soal:** P1: `p1=50, t1=25`; P2: `p2=75, t2=30`. `U=25/50+30/75=0,9`; di atas batas cukup RM dua tugas, tetapi di bawah 1.

| RM, sampai P2 pertama selesai | Penjelasan |
|---|---|
| `0–25 P1` | P1 berprioritas lebih tinggi. |
| `25–50 P2` | P2 masih butuh 5. |
| `50–75 P1` | P1 baru datang, P2 terhenti. |
| `75–80 P2` | P2 pertama melewati deadline pada `75`, selesai pada `80`. |

| EDF, sampai KPK(50,75)=150 | Penjelasan |
|---|---|
| `0–25 P1; 25–55 P2` | P2 berdeadline 75 tetap berjalan saat P1 baru tiba pada 50 (deadline P1 baru 100). |
| `55–80 P1; 80–110 P2` | P2 kedua berdeadline 150; saat 100 P1 baru juga berdeadline 150, sehingga P2 yang sedang berjalan dilanjutkan. |
| `110–135 P1; 135–150 ___` | Semua instance sebelum 150 selesai sesuai deadline. |

**Jebakan:** tingkat utilisasi bukan bukti tunggal miss RM saat di atas batas cukup; bandingkan setiap waktu selesai dengan deadline absolut instance. Hiperperiode adalah KPK periode, bukan jumlahnya. Jika ada dua instance satu proses yang menumpuk, jangan hilangkan pekerjaan lama.

## 7. Menelusuri semaphore, membaca RAG, dan menentukan safe state (pertemuan 7)

**Race condition** terjadi bila hasil bergantung pada urutan akses bersamaan ke data bersama. *Critical section* ialah bagian yang mengakses data tersebut. Solusi yang benar memenuhi (1) **mutual exclusion**: paling banyak satu masuk; (2) **progress**: jika kosong, keputusan siapa masuk tidak ditunda tanpa alasan oleh pihak di luar; (3) **bounded waiting**: penunggu tidak disalip selamanya. Mutex memberi kepemilikan eksklusif; spinlock menunggu dengan *busy waiting*, masuk akal hanya untuk tunggu sangat singkat dalam kondisi sesuai.

**Semaphore:** `wait(S)`/`P`/`down` mengambil satu izin; jika tak tersedia, pemanggil menunggu. `signal(S)`/`V`/`up` mengembalikan satu izin atau membangunkan penunggu. Operasi ini atomik. Untuk trace, catat **nilai awal, tiap operasi, siapa yang tertahan, dan siapa yang dibangunkan**. Nilai yang tampak ketika ada penunggu bergantung pada definisi implementasi; gunakan konvensi pseudocode soal. Jangan menganggap `wait` yang tertahan sudah melewati baris berikutnya.

| Masalah | Nilai awal dan urutan penting |
|---|---|
| Mutual exclusion | `mutex=1`; `wait(mutex)` → critical section → `signal(mutex)`. |
| Bounded buffer kapasitas `N` | `empty=N`, `full=0`, `mutex=1`; produsen `wait(empty) → wait(mutex) → isi → signal(mutex) → signal(full)`; konsumen `wait(full) → wait(mutex) → ambil → signal(mutex) → signal(empty)`. |
| Dining philosophers | Semua mengambil garpu kiri lalu menunggu kanan dapat membentuk siklus. Batasi pemakai meja ke `N−1`, tetapkan urutan total garpu, atau ubah urutan ambil salah satu filsuf. |

**Trace mini:** `S=1`; A menjalankan `wait(S)` → S=0, A lanjut; B menjalankan `wait(S)` → B tertahan; A menjalankan `signal(S)` → B dapat lanjut. Jangan menaikkan S menjadi izin bebas **dan** sekaligus memberi izin yang sama kepada B.

**Saat membaca kode sinkronisasi:** cocokkan setiap `wait` dengan `signal`, lihat apakah kunci selalu dilepas juga pada cabang `return`/error, dan periksa urutan mengambil dua kunci. Misalnya A mengambil X lalu menunggu Y, sedangkan B mengambil Y lalu menunggu X: bila keduanya telanjur memegang kunci pertama, keduanya dapat deadlock. Urutan pengambilan X lalu Y yang sama untuk semua pekerja memutus kemungkinan siklus itu. Menaruh `wait(empty)` saat masih memegang `mutex` pada bounded buffer juga berbahaya: konsumen mungkin perlu `mutex` untuk mengosongkan buffer, tetapi produsen tidak melepaskannya.

**Empat kondisi Coffman** harus ada bersama agar deadlock mungkin: (1) mutual exclusion, (2) hold and wait, (3) no preemption, (4) circular wait. *Prevention* merancang aturan yang menghilangkan setidaknya satu kondisi (contoh: urutan total pengambilan resource menghilangkan circular wait). *Avoidance* memeriksa permintaan terhadap keadaan aman sebelum mengabulkannya (contoh: Banker's algorithm). *Detection* membiarkan kemungkinan deadlock lalu mencarinya; *recovery* dapat membatalkan proses atau mencabut sumber daya bila aman. Starvation adalah satu proses menunggu tanpa batas meskipun sistem lain terus maju; berbeda dari deadlock yang membuat kelompok saling menunggu.

**Resource-allocation graph (RAG):** `Pi → Rj` berarti Pi *meminta* Rj; `Rj → Pi` berarti satu instance Rj *dialokasikan* ke Pi. Tanpa siklus → tidak deadlock. Bila setiap jenis sumber daya hanya punya **satu instance**, siklus → deadlock. Bila suatu jenis punya **beberapa instance**, siklus hanya menunjukkan kemungkinan; perlu pemeriksaan lanjutan. Hitung titik/instance sumber daya dan arah panah sebelum menyimpulkan.

**Banker's algorithm:** semua angka vektor per jenis resource. `Need = Max − Allocation`.

1. Hitung `Need` untuk setiap proses; salin `Available` ke `Work`; tandai semua `Finish=false`.
2. Cari proses belum selesai dengan `Need_i ≤ Work` pada **setiap komponen**. Jika ada, anggap proses itu selesai: `Work = Work + Allocation_i`, lalu `Finish_i=true`. Catat urutannya.
3. Ulangi. Semua `Finish=true` → ada *safe sequence*, state aman. Bila masih ada proses tetapi tak ada yang memenuhi syarat → state tidak aman; **tidak aman tidak selalu berarti sudah deadlock**.
4. Untuk permintaan `Request_i`: pastikan `Request_i ≤ Need_i` dan `Request_i ≤ Available`; lakukan alokasi **sementara**, hitung ulang `Available`, `Allocation_i`, `Need_i`, lalu jalankan safety check. Kabuli hanya jika hasilnya aman.

**Contoh kecil (resource A,B):** `Available=(1,1)`. P1: `Allocation=(1,0), Need=(0,1)`; P2: `Allocation=(0,1), Need=(2,0)`; P3: `Allocation=(0,0), Need=(1,1)`. Mulai `Work=(1,1)`: P1 memenuhi, selesai → `(2,1)`; P2 memenuhi → `(2,2)`; P3 memenuhi → `(2,2)`. Salah satu safe sequence adalah **P1 → P2 → P3**. Urutan aman bisa lebih dari satu. Pemeriksaan dilakukan komponen demi komponen; total keseluruhan resource tidak cukup sebagai pengganti vektor.

## Ringkasan yang diprioritaskan untuk disalin tangan

1. Diagram state proses, aturan `fork()/exec()/wait()`, dan langkah trace.
2. `waiting=completion−arrival−burst`; FCFS/SJF/RR beserta aturan antrean.
3. RM/EDF: rilis, deadline absolut, aturan tie EDF, `U`, batas cukup RM, dan aturan akhir tabel.
4. `W/n+o`, `W/n+n·o`, `Speedup=1/[s+(1−s)/N]`.
5. `wait/signal`, bounded buffer, empat kondisi deadlock, arah panah RAG, dan langkah safety Banker's.
6. Peta singkat layanan SO, dual-mode, serta pilihan struktur SO.

**Batas sumber:** kisi-kisi menjelaskan topik, kemampuan, dan konvensi; contoh hitungan serta trace dalam dokumen ini adalah latihan buatan untuk menerapkan aturan tersebut, bukan salinan soal UTS. Jawaban isian mengikuti nama fungsi dan tanda desimal yang diminta soal.
