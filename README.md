# StrukturData-Q1-2501010063-Rio-Teguh-Priyo-Utomo-C

1. 1. Karakteristik Memori dan Akses Data
Mengapa Array = O(1) dan Singly Linked List = O(n)?
Array menyimpan elemen secara kontinu di memori — artinya, semua elemen berjejer rapat di alamat memori yang berurutan. Karena itu, CPU bisa langsung menghitung alamat elemen ke-i dengan rumus:
alamat[i] = base_address + (i × ukuran_tipe_data)
Tidak peduli indeks berapa yang diminta, kalkulasi ini selalu selesai dalam satu langkah → O(1).
Sebaliknya, Singly Linked List menyimpan node secara non-kontinu — setiap node tersebar di sembarang lokasi memori, dan hanya terhubung lewat pointer next. Tidak ada cara untuk "melompat" ke node ke-5 tanpa melewati node 1 → 2 → 3 → 4 → 5 terlebih dahulu. Dalam kasus terburuk, kita harus traversal seluruh list → O(n).
Array:    [10][20][30][40][50]   ← berurutan, lompat langsung ke indeks manapun
           100 104 108 112 116   ← alamat memori (misal int 4 byte)

LinkedList: 10→  20→  30→  40→  50→NULL
            100  340  612  89   450   ← alamat acak, harus ikuti pointer

2. Analisis Efisiensi Operasi Manipulasi
Linked List lebih unggul dari Array untuk insertion dan deletion dalam kondisi berikut:
a. Penyisipan/penghapusan di tengah atau awal struktur data
Pada Array, menyisipkan elemen di posisi ke-i mengharuskan semua elemen dari posisi i hingga akhir digeser satu per satu → O(n). Linked List hanya perlu mengubah dua pointer (prev dan next) tanpa menggeser data apapun → O(1) jika posisi node sudah diketahui.
b. Ukuran data sering berubah dan tidak bisa diprediksi
Array statis punya kapasitas tetap. Jika penuh, harus dialokasikan ulang. Linked List bersifat dinamis by nature — node baru cukup di-malloc/new dan disambungkan, tanpa perlu realokasi massal.
c. Data berukuran besar yang sering disisipkan/dihapus di awal
Menghapus elemen pertama Array = menggeser semua elemen → O(n). Di Linked List, cukup pindahkan pointer head ke node berikutnya → O(1).

Catatan penting: Keunggulan ini berlaku penuh hanya jika posisi node sudah diketahui (misal sudah punya pointer ke node tersebut). Jika harus mencari posisinya dulu, tetap ada biaya O(n) untuk traversal.


3. Konsep Doubly Linked List
Anatomi node Doubly Linked List:
┌──────────┬──────────┬──────────┐
│  *prev   │   data   │  *next   │
└──────────┴──────────┴──────────┘
     ↑                     ↑
pointer ke               pointer ke
node sebelumnya          node berikutnya
Setiap node menyimpan tiga komponen: pointer prev (menunjuk ke node sebelumnya), data (nilai yang disimpan), dan pointer next (menunjuk ke node berikutnya). Node pertama memiliki prev = NULL, node terakhir memiliki next = NULL.
Dampak terhadap memori:
Setiap node membutuhkan ruang ekstra untuk satu pointer tambahan (prev). Jika ukuran pointer = 8 byte (64-bit), maka setiap node Doubly LL mengonsumsi 8 byte lebih banyak dibanding Singly LL. Untuk n node, total overhead tambahan = 8n byte.
Dampak terhadap fleksibilitas:
KemampuanSingly LLDoubly LLTraversal maju✅✅Traversal mundur❌✅Hapus node tanpa pointer prevButuh node sebelumnyaLangsung O(1)Implementasi DequeSulitMudah
Doubly LL memungkinkan navigasi dua arah, sehingga cocok untuk struktur seperti browser history (back/forward) atau teks editor dengan undo/redo.

4. Mekanisme Circular Linked List
Perbedaan teoritis:
Pada Linked List biasa, node terakhir menunjuk ke NULL sebagai penanda akhir. Pada Circular Linked List, node terakhir menunjuk kembali ke node pertama (head), membentuk siklus tanpa ujung.
Biasa:    head → [A] → [B] → [C] → NULL

Circular: head → [A] → [B] → [C] ──┐
               └─────────────────┘
Karena tidak ada NULL, traversal harus dihentikan secara eksplisit (biasanya dengan mendeteksi kembali ke node awal).
Use case: Sistem Antrian Round-Robin (Penjadwalan CPU)
Sistem operasi menggunakan Circular Linked List untuk menjadwalkan proses. Setiap proses mendapat jatah waktu CPU (time slice). Setelah proses terakhir selesai giliran, scheduler langsung kembali ke proses pertama — tanpa perlu reset pointer atau pengecekan NULL. Struktur melingkar ini secara alami merepresentasikan siklus yang terus berputar, membuat implementasi menjadi lebih efisien dan bersih.

5. Array Dinamis di Python
Mekanisme "di balik layar" saat Python list kehabisan kapasitas:
Python list bukan array biasa — ia adalah Dynamic Array yang dikelola secara otomatis. Berikut yang terjadi saat append() dipanggil dan kapasitas penuh:
Kondisi awal:
list = [1, 2, 3, 4]  ← kapasitas = 4, terisi = 4 (PENUH)

append(5) dipanggil → OVERFLOW!
Langkah-langkah yang terjadi:

Deteksi overflow — Python mendeteksi len == capacity
Alokasi memori baru — Python mengalokasikan blok memori baru yang lebih besar. Python menggunakan faktor pertumbuhan sekitar 1.125× hingga 2× (bukan persis 2×, tergantung ukuran saat ini)
Copy semua elemen — Seluruh elemen lama disalin ke lokasi memori baru → ini operasi O(n)
Bebaskan memori lama — Blok memori lama di-dealokasi
Sisipkan elemen baru — Elemen baru ditambahkan di posisi berikutnya

Setelah resize:
[1, 2, 3, 4, 5, _, _, _]  ← kapasitas baru = 8, terisi = 5
Kenapa tidak resize setiap kali?
Karena meskipun satu operasi resize = O(n), jika dihitung secara amortized analysis, biaya rata-rata per operasi append tetap O(1). Setiap elemen paling banyak "dipindahkan" beberapa kali sepanjang hidupnya, sehingga total biaya n operasi append = O(n), bukan O(n²).
