# Bab 3 — Proses

Bab ini mengikuti subbab 3.1–3.9 *Operating System Concepts* edisi ke-10. Penjelasan diselaraskan dengan `K3.pdf`, latihan K3, dan soal UTS 2022 dalam bahan kuliah Sensei. Gambar slide dipakai pada konsep yang lebih mudah dipahami secara visual. Bab ini terutama menuntut pembacaan alur dan pembedaan istilah; tidak ada rumus hitung utama.

**Peta belajar:** program menjadi proses → proses berganti keadaan → kernel menyimpan keadaan di PCB → penjadwal memilih proses → proses dibuat atau dihentikan → proses saling bertukar data.

## 3.1 Process Concept — Program yang sedang bekerja

### 3.1.1 The Process

Program adalah instruksi pasif yang tersimpan di disk. Proses adalah *satu pelaksanaan* program itu: ada instruksi yang sedang/akan dikerjakan, nilai register, lokasi instruksi berikutnya (*program counter*), dan data kerja. Dua kali membuka program yang sama dapat menghasilkan dua proses dengan keadaan berbeda. Jadi nama program saja tidak cukup untuk mengenali sebuah proses.

![Bagian memori sebuah proses dari K3, halaman 6](gambar/pertemuan-03/slide-006.jpg)

- **Text** memuat instruksi program. **Data** memuat variabel global atau statis.
- **Heap** memuat alokasi dinamis yang diminta saat program berjalan. **Stack** memuat bingkai pemanggilan fungsi, seperti parameter, variabel lokal, dan alamat kembali.
- Heap dan stack dapat berubah ukuran. Gambar menunjukkan susunan *konseptual* ruang alamat; letak fisik di RAM tidak wajib berurutan seperti gambar.

Contoh: editor teks yang dibuka dua kali dapat memakai instruksi aplikasi yang sama, tetapi setiap proses mempunyai dokumen dan posisi kerja sendiri. Jika satu proses berhenti, proses lain tidak otomatis ikut berhenti.

### 3.1.2 Process State

Proses tidak selalu memakai CPU. Ia dapat berada dalam **new** (sedang dibuat), **ready** (siap, menunggu CPU), **running** (instruksi sedang dieksekusi), **waiting** (menunggu kejadian seperti I/O), atau **terminated** (selesai).

![Diagram keadaan proses dari K3, halaman 9](gambar/pertemuan-03/slide-009.jpg)

```text
new → ready → running → terminated
          ↑       │
          │       ├─ jatah CPU habis / didahului proses lain → ready
          │       └─ meminta I/O → waiting
          └──────────── I/O selesai ────────┘
```

Perbedaan penting: proses **ready** sudah bisa berjalan tetapi belum mendapat CPU; proses **waiting** belum bisa maju karena menunggu peristiwa. Setelah I/O selesai, proses biasanya kembali ke **ready**, bukan langsung ke **running**; penjadwal masih harus memilihnya. Pada satu inti CPU, hanya satu proses berada dalam running pada suatu saat. Pada banyak inti, masing-masing inti dapat menjalankan proses.

### 3.1.3 Process Control Block (PCB)

Kernel perlu mengingat keadaan tiap proses agar bisa menghentikannya sementara lalu melanjutkannya. Catatan itu disebut **PCB**. Isinya antara lain identitas proses, state, program counter, register, informasi penjadwalan, pengelolaan memori, akuntansi, dan status I/O. Saat proses A diganti B, nilai penting A disimpan dan nilai B dipulihkan. PCB adalah catatan kernel mengenai proses, bukan seluruh isi program atau salinan lengkap RAM-nya.

![Isi PCB dari K3, halaman 10](gambar/pertemuan-03/slide-010.jpg)

**Contoh singkat:** A sedang menjalankan instruksi ke-20 lalu waktunya habis. Agar nanti tidak mulai lagi dari instruksi pertama, kernel menyimpan posisi dan register A di PCB. Ketika A mendapat CPU lagi, keadaan itu dipulihkan.

## 3.2 Process Scheduling — Siapa mendapat CPU?

### 3.2.1 Scheduling Queues

Proses yang siap diletakkan di **ready queue**. Proses yang menunggu disk atau kejadian lain berada dalam antrean tunggu yang sesuai. Istilah *queue* menyatakan kumpulan kandidat; kebijakan memilihnya tidak selalu sekadar urutan masuk. Penjadwal memilih proses dari ready queue ketika CPU tersedia.

![Antrean ready dan wait dari K3, halaman 15](gambar/pertemuan-03/slide-015.jpg)

Ikuti satu contoh: A sedang running dan meminta baca disk. A masuk antrean tunggu disk. B dipilih dari ready queue dan berjalan. Saat pembacaan A selesai, A kembali ke ready queue; B boleh terus berjalan sampai ada alasan untuk berpindah. **Interrupt bukan selalu berarti A langsung memakai CPU.**

### 3.2.2 CPU Scheduling

Penjadwalan berusaha memakai CPU dengan baik dan menjaga respons aplikasi. Keputusan dapat terjadi saat proses menunggu I/O, selesai, atau didahului karena jatah waktu habis. Pemilihan proses berikutnya dan pemindahan CPU adalah dua langkah berbeda: **scheduler** memilih, **dispatcher** melaksanakan peralihan. Aturan rinci FCFS, SJF, dan Round Robin dibahas pada Bab 5.

### 3.2.3 Context Switch

**Context switch** menyimpan konteks proses yang berhenti dan memulihkan konteks proses berikutnya. Konteks minimal mencakup posisi instruksi dan register; rincian lain bergantung sistem. Selama perpindahan, CPU bekerja untuk kernel dan belum memajukan instruksi aplikasi. Karena itu, perpindahan punya biaya.

![Perpindahan konteks A ke B dari K3, halaman 16](gambar/pertemuan-03/slide-016.jpg)

Urutannya: timer atau peristiwa lain membawa kendali ke kernel → kernel menyimpan A → memilih B → memulihkan B → B berjalan. Jangan samakan **context switch**, **perpindahan user/kernel mode**, dan **pembuatan proses**. System call dapat masuk kernel lalu kembali ke proses yang sama tanpa mengganti proses.

## 3.3 Operations on Processes — Lahir dan berakhirnya proses

### 3.3.1 Process Creation

Suatu proses dapat membuat proses anak sehingga terbentuk hubungan induk–anak. Anak mendapat identitasnya sendiri. Sumber daya dapat diwariskan atau dibagikan menurut aturan sistem; jangan menganggap semua data otomatis dibagi. Di Unix, `fork()` membuat proses anak. Kedua alur melanjutkan eksekusi setelah `fork()`, tetapi nilai kembalinya berbeda: induk menerima identitas anak, anak menerima 0. Setelah itu, anak sering memanggil keluarga `exec()` untuk **mengganti program yang dijalankan dalam proses anak**. `exec()` tidak membuat proses baru.

![Pohon proses dari K3, halaman 21](gambar/pertemuan-03/slide-021.jpg)

```text
shell (induk)
  └─ fork() → proses anak
                └─ exec("program") → program baru berjalan dalam anak
shell dapat wait() untuk mengambil hasil anak
```

Jika `fork()` dipanggil sekali oleh satu proses dan berhasil, sesudahnya ada dua proses. Jika keduanya kembali memanggil `fork()`, jumlah dapat bertambah lagi. Hitung berdasarkan *proses mana yang mencapai panggilan*, bukan hanya jumlah baris kode. `fork()` juga dapat gagal, sehingga hitungan soal biasanya mengandaikan keberhasilan.

### 3.3.2 Process Termination

Proses selesai secara normal lewat `exit()` atau dihentikan karena galat/perintah. Pada Unix, induk memakai `wait()` atau sejenisnya untuk mengambil status akhir anak. **Zombie** ialah anak yang sudah selesai tetapi informasi akhirnya belum diambil induk. Ia tidak lagi menjalankan program, namun entri statusnya masih perlu dibereskan. **Orphan** ialah anak yang masih hidup setelah induknya berakhir; sistem akan mengalihkan pengasuhan menurut mekanismenya. Orphan dan zombie berbeda: yang satu soal induk yang hilang, yang lain soal status anak selesai yang belum diambil.

![Pembuatan dan akhir proses dari K3, halaman 27](gambar/pertemuan-03/slide-027.jpg)

## 3.4 Interprocess Communication — Mengapa proses perlu bicara?

Proses independen tidak saling memengaruhi; **proses kooperatif** sengaja berbagi hasil. Contohnya peramban mengirim permintaan ke proses layanan, atau penghasil data menyerahkannya ke pengolah data. Alasan bekerja sama meliputi berbagi informasi, mempercepat pekerjaan yang dapat dipisah, dan menyusun sistem menjadi bagian-bagian kecil. Dua model utama ialah **shared memory** dan **message passing**.

![Dua model komunikasi dari K3, halaman 37](gambar/pertemuan-03/slide-037.jpg)

Pada shared memory, kedua proses memakai wilayah memori bersama dan perlu sepakat kapan membaca atau menulis. Pada message passing, proses memakai operasi kirim dan terima; kernel atau sarana komunikasi mengurus perpindahannya. Keduanya dapat dipakai di satu sistem. Pilihan bergantung ukuran data, biaya pertukaran, kebutuhan sinkronisasi, dan apakah proses berada di mesin yang sama.

## 3.5 IPC in Shared-Memory Systems

Misalkan proses *producer* membuat data dan *consumer* memakainya. Keduanya dapat mengakses buffer bersama. Producer menaruh item pada slot berikutnya; consumer mengambil item dari slot berikutnya. Jika buffer berkapasitas `N`, indeks melingkar diperbarui dengan `(indeks + 1) mod N` agar kembali ke slot pertama setelah slot terakhir.

![Producer dan consumer pada buffer bersama dari K3, halaman 41](gambar/pertemuan-03/slide-041.jpg)

Dengan rancangan satu slot sengaja dibiarkan kosong, `in == out` berarti kosong dan `(in + 1) mod N == out` berarti penuh; kapasitas efektifnya `N − 1`. **Ini sifat rancangan satu slot kosong**, bukan aturan semua buffer. Rancangan lain memakai penghitung sehingga dapat memanfaatkan semua `N` slot, tetapi penghitung dan data bersama perlu dilindungi dari pembaruan bersamaan. Tanpa sinkronisasi, consumer bisa membaca sebelum producer selesai menulis, atau producer menimpa data yang belum dibaca. Bab 7 membahas alat sinkronisasinya.

**Contoh `N = 4`:** mula-mula `in = out = 0`. Setelah producer menaruh dua item, `in = 2` dan `out = 0`. Ia boleh menaruh satu item lagi sehingga `in = 3`. Sesudah itu slot efektif penuh karena `(3 + 1) mod 4 = 0 = out`; slot keempat sengaja tidak diisi agar keadaan penuh tidak rancu dengan kosong.

## 3.6 IPC in Message-Passing Systems

### 3.6.1 Naming

Komunikasi memerlukan hubungan antara pengirim dan penerima. Pada **direct communication**, proses menyebut tujuan secara langsung. Pada **indirect communication**, keduanya memakai *mailbox* atau port; pengirim mengirim ke wadah itu, penerima mengambil darinya. Cara kedua memudahkan pergantian pihak tanpa harus mengetahui identitas proses lain, tetapi aturan kepemilikan dan penerima pesan perlu jelas.

### 3.6.2 Synchronization

Operasi kirim dan terima bisa **blocking** atau **nonblocking**. Kirim blocking menunggu sampai syarat pengiriman terpenuhi; terima blocking menunggu pesan tersedia. Operasi nonblocking segera kembali, mungkin dengan hasil “belum ada pesan”. Jangan menyamakan blocking dengan “selalu buruk”: menunggu dapat menyederhanakan urutan kerja. Namun dua pihak yang sama-sama menunggu satu sama lain bisa terhenti, sehingga alur komunikasi harus dirancang.

### 3.6.3 Buffering

Pesan dapat ditampung dalam antrean berkapasitas **nol**, **terbatas**, atau **tak terbatas secara konsep**. Kapasitas nol membutuhkan pengirim dan penerima bertemu pada waktu yang sesuai (*rendezvous*). Kapasitas terbatas membuat pengirim menunggu ketika penuh. Kapasitas tak terbatas berarti pengirim tidak menunggu karena kapasitas menurut model, tetapi sistem nyata tetap dibatasi memori. Kapasitas buffer dan blocking adalah dua hal yang saling berkaitan, bukan istilah yang sama.

![Model antrean pesan dari K3, halaman 48](gambar/pertemuan-03/slide-048.jpg)

## 3.7 Examples of IPC Systems — Bentuk nyata

### 3.7.1 POSIX Shared Memory

POSIX menyediakan cara membuat objek memori bersama, mengatur ukurannya, dan memetakannya ke ruang alamat proses. Setelah pemetaan, proses membaca atau menulis area yang sama melalui alamat memorinya. Pembuatan dan pemetaan memerlukan layanan kernel; akses data biasa sesudahnya tidak perlu mengirim setiap nilai sebagai pesan. Proses tetap perlu mengatur urutan akses.

### 3.7.2 Mach Message Passing

Mach menonjolkan pertukaran pesan melalui **port**. Proses mengirim ke port dan penerima mengambil dari port. Contoh ini menunjukkan bahwa operasi yang terlihat sederhana—kirim dan terima—tetap membutuhkan aturan identitas tujuan, hak akses, serta penanganan pesan ketika penerima belum siap.

### 3.7.3 Windows IPC

Windows menggunakan mekanisme pesan dalam beberapa layanan sistemnya. Contoh pada buku menunjukkan variasi implementasi IPC; untuk UTS, pahami model kirim–terima dan kapan data disalin atau dibagikan. Nama layanan internal dapat berbeda antarversi dan bukan inti konsep.

### 3.7.4 Pipes

**Pipe** adalah saluran aliran byte. Pada *ordinary pipe*, satu ujung dipakai membaca dan ujung lain menulis; biasanya dipakai oleh proses yang memiliki hubungan, misalnya setelah `fork()`. Pipe biasa bersifat sementara. **Named pipe** mempunyai nama yang dapat dibuka proses yang tidak memiliki hubungan induk–anak. Satu pipe lazimnya satu arah; komunikasi dua arah mungkin membutuhkan dua saluran atau mekanisme lain.

![Aliran pipe dari K3, halaman 60](gambar/pertemuan-03/slide-060.jpg)

Contoh perintah `penghasil | penyaring`: keluaran penghasil masuk sebagai masukan penyaring. Pipe mengalirkan byte; batas “satu objek data” tidak otomatis dijaga seperti sebuah pesan utuh. Jika pembaca lambat dan buffer penuh, penulis dapat menunggu.

## 3.8 Communication in Client–Server Systems

### 3.8.1 Sockets

**Socket** adalah titik ujung komunikasi. Dalam jaringan, pasangan alamat dan port membantu menemukan layanan; protokol menentukan cara data dikirim. Client menghubungi server, lalu keduanya membaca dan menulis lewat socket. TCP menyajikan aliran byte yang berurutan dan andal menurut model protokolnya, sedangkan UDP mengirim datagram tanpa jaminan pengantaran atau urutan. Pilihan protokol memengaruhi cara aplikasi menangani pesan yang hilang dan batas pesan.

![Client dan server lewat socket dari K3, halaman 65](gambar/pertemuan-03/slide-065.jpg)

### 3.8.2 Remote Procedure Calls (RPC)

RPC membuat pemanggilan layanan di mesin lain tampak seperti pemanggilan fungsi: client menyiapkan argumen, *stub* mengubahnya menjadi pesan, jaringan mengirim pesan, server menjalankan layanan, lalu hasil dikirim kembali. Tampilan yang mirip fungsi lokal tidak menghapus kemungkinan jaringan lambat, pesan hilang, atau server gagal. Karena itu, hasil yang tidak diterima belum tentu berarti operasi tidak dijalankan.

![Urutan RPC dari K3, halaman 70](gambar/pertemuan-03/slide-070.jpg)

Jika client mengulang permintaan setelah waktu tunggu habis, operasi seperti “tambah saldo” bisa terjadi dua kali bila server sebenarnya sudah menjalankan permintaan pertama. Sistem perlu identitas permintaan atau operasi yang aman diulang untuk mengendalikan risiko ini. **Android RPC** dalam buku adalah salah satu contoh komunikasi antarkomponen; konsep utamanya tetap pengemasan argumen, pengiriman, pelaksanaan, dan pengembalian hasil.

## 3.9 Summary — Rangkaian inti

Program aktif mempunyai keadaan dan PCB. Saat menunggu I/O, proses meninggalkan CPU; saat siap, ia bersaing lewat ready queue. Kernel menyimpan konteks sebelum memindahkan CPU. Proses dapat membuat anak, mengganti program anak, serta menunggu hasilnya. Proses yang bekerja sama dapat berbagi memori atau mengirim pesan; keduanya membutuhkan aturan urutan dan penanganan kegagalan.

| Jika soal menyebut… | Periksa konsep ini |
|---|---|
| “Siap tetapi belum berjalan” | Ready, bukan waiting. |
| “Menunggu disk” | Waiting; setelah selesai kembali ke ready. |
| “Melanjutkan instruksi terakhir” | Program counter dan register dalam PCB. |
| “Membuat anak lalu menjalankan program lain” | `fork()` lalu `exec()`. |
| “Anak selesai, induk belum mengambil hasil” | Zombie. |
| “Data bersama dibaca terlalu cepat” | Sinkronisasi shared memory. |
| “Client memanggil layanan jauh” | RPC dan kemungkinan kegagalan jaringan. |

## Contoh soal dan pembahasan

### Soal 1 — State dan queue (adaptasi UTS Sistem Operasi 2022)

**Soal.** A sedang berjalan lalu meminta baca disk. B siap berjalan. Ketika disk selesai, apakah A langsung berjalan? Gambarkan jalur A dan hubungkan state dengan antreannya.

**Pembahasan.** A berpindah `running → waiting` dan masuk antrean tunggu disk. Penjadwal dapat memilih B dari ready queue. Ketika disk selesai, A pindah `waiting → ready` dan masuk ready queue. A baru `ready → running` jika dipilih. Jika B masih berjalan, A tidak otomatis merebut CPU. Dengan begitu, state menunjukkan *kesiapan*, sedangkan queue menyimpan proses yang menunggu sumber daya atau giliran.

### Soal 2 — Shell membuat proses (adaptasi UTS Sistem Operasi 2022)

**Soal.** Sebutkan peran `fork()`, `exec()`, dan `wait()` ketika shell menjalankan program.

**Pembahasan.** Shell memanggil `fork()` untuk membuat anak. Anak memanggil `exec()` agar program yang diminta menggantikan citra program anak. Induk boleh memanggil `wait()` untuk menunggu akhir anak dan mengambil statusnya. `exec()` tidak menciptakan anak baru; proses anak tetap punya identitas proses yang sama setelah citranya diganti.

### Soal 3 — Program, proses, dan PCB (adaptasi latihan K3 nomor 61–63)

**Soal.** Satu program editor dibuka dua kali. Apa yang sama dan apa yang berbeda? Mengapa PCB diperlukan?

**Pembahasan.** Kedua proses berasal dari program yang sama, tetapi masing-masing punya identitas, data kerja, dan posisi eksekusi. Kernel membutuhkan PCB untuk menyimpan state, program counter, register, serta informasi lain agar setiap proses dapat dijadwalkan dan dilanjutkan dengan benar. Berkas program di disk saja tidak menyimpan keadaan dua jendela yang sedang berjalan.

### Soal 4 — Ready atau waiting? (adaptasi latihan K3 nomor 64–68)

**Soal.** Proses P menunggu jawaban disk; Q siap tetapi CPU dipakai R. Tentukan state P dan Q, lalu state P setelah disk selesai.

**Pembahasan.** P = waiting karena ada kejadian yang belum selesai. Q = ready karena tidak terhalang selain giliran CPU. Sesudah disk selesai, P = ready. P belum tentu running karena R mungkin masih memakai CPU atau Q dipilih lebih dulu.

### Soal 5 — Zombie dan orphan (latihan berdasarkan subbab 3.3)

**Soal.** Anak sudah `exit()`, tetapi induk belum `wait()`. Pada kasus lain, induk berakhir lebih dahulu sementara anak masih bekerja. Sebutkan keduanya.

**Pembahasan.** Kasus pertama adalah zombie: anak selesai, status akhirnya masih perlu diambil. Kasus kedua adalah orphan: anak masih hidup tanpa induk asal. Menyebut keduanya “proses mati” menghilangkan perbedaan penting.

### Soal 6 — Buffer melingkar (latihan berdasarkan subbab 3.5)

**Soal.** Buffer memakai empat slot dan aturan satu slot kosong. Jika `in = 2` dan `out = 3`, apakah buffer penuh? Berapa item di dalamnya?

**Pembahasan.** Posisi sesudah `in` adalah `(2 + 1) mod 4 = 3`, sama dengan `out`, jadi buffer penuh menurut aturan ini. Jumlah item efektifnya `N − 1 = 3`. Jawaban “empat item” salah karena satu slot sengaja dikosongkan sebagai penanda.

### Soal 7 — Memilih cara komunikasi (latihan berdasarkan kisi-kisi IPC)

**Soal.** Dua proses di satu komputer bertukar blok data besar berulang kali. Pilih shared memory atau message passing, dan sebutkan masalah yang masih harus diselesaikan.

**Pembahasan.** Shared memory masuk akal karena setelah pemetaan, data dapat diakses tanpa mengirim ulang tiap blok sebagai pesan. Namun kedua proses tetap harus menyepakati kapan data selesai ditulis dan aman dibaca; diperlukan sinkronisasi. Message passing juga mungkin, terutama bila kemudahan pengaturan lebih penting daripada biaya penyalinan. Pilihan bukan aturan mutlak.

## Uji pemahaman

1. Mengapa proses yang menunggu I/O tidak disebut ready?
2. Apa yang perlu disimpan agar proses melanjutkan instruksi yang tepat setelah context switch?
3. Mengapa `exec()` sendiri tidak menambah jumlah proses?
4. Apa risiko mengulang RPC setelah waktu tunggu habis?
5. Kapan ordinary pipe kurang sesuai dibanding named pipe atau socket?

**Jawaban ringkas:** (1) kejadian yang dibutuhkan belum selesai; (2) setidaknya program counter dan register, disimpan melalui konteks proses/PCB; (3) ia mengganti citra program proses pemanggil; (4) operasi bisa terulang padahal permintaan pertama telah dijalankan; (5) ketika proses tidak memiliki hubungan yang memudahkan pewarisan pipe atau perlu komunikasi jaringan.

## Sumber dan batas cakupan

- Silberschatz, Galvin, dan Gagne, *Operating System Concepts*, edisi ke-10, Bab 3 subbab 3.1–3.9.
- `K3.pdf`, terutama diagram keadaan, PCB, antrean, pembuatan proses, IPC, pipe, socket, dan RPC.
- `Contoh Soal/UTS Sistem Operasi 2022.pdf` untuk soal adaptasi 1–2; `Contoh Soal/Latihan Soal K3 - Proses.pdf` untuk soal adaptasi 3–4. Soal lain adalah latihan yang disusun dari konsep bab dan kisi-kisi, bukan salinan UTS.
- Penjadwalan rinci dan sinkronisasi formal sengaja dirujuk ke bab kuliah berikutnya. Tidak ada kontrak kuliah terpisah dalam paket bahan yang diperiksa.
