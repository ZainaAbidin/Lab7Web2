# Praktikum 1
### 1. Menentukan Aturan Main Visual (Penyusunan CSS)
Langkah pertama dalam narasi ini adalah membangun fondasi visual di dalam folder public. Di sini, kita membuat file style.css yang berfungsi sebagai "pemandu" bagi browser untuk menampilkan elemen-elemen HTML.
```Logika Flexbox:``` Kita menggunakan teknologi flexbox pada bagian .wrapper. Tujuannya adalah untuk membagi layar secara otomatis menjadi dua kolom: konten utama di kiri dan sidebar di kanan.
```Detail Widget:``` Kita memberikan sentuhan warna biru khusus pada header widget agar informasi penting di sisi kanan terlihat menonjol dan terpisah dari konten utama.

### 2. Membangun Kerangka Rumah (Modularitas Template)
Alih-alih menulis kode yang sama berulang kali di setiap halaman, kita memecah struktur website menjadi bagian-bagian kecil yang bisa digunakan kembali (reusable).
```Header:``` Kita membuat template/header.php yang berisi identitas situs dan menu navigasi. Dengan menaruh navigasi di sini, perubahan
pada satu menu akan otomatis berdampak pada seluruh halaman.
```Footer:``` Kita membuat template/footer.php yang bertugas sebagai penutup semua tag HTML. Ini memastikan bahwa 
setiap halaman "selesai" dengan sempurna tanpa ada kesalahan penutupan elemen.

### 3. Mengatur Alur Informasi (Logika Controller)
Setelah kerangka visual siap, kita berpindah ke "otak" aplikasi, yaitu file ```Controller/Page.php```.
1. Persiapan Paket Data: Setiap fungsi (seperti about atau contact) bertugas menyiapkan paket informasi berupa variabel $title dan $content.
2. Pengiriman Data ke View: Controller kemudian mengirimkan paket ini ke file View yang relevan. Tanpa langkah ini, halaman website akan kosong atau menampilkan pesan error karena tidak ada data yang bisa ditampilkan.

### 4. Merakit Isi Kamar (Implementasi View Halaman)
Langkah terakhir adalah menyusun konten spesifik untuk setiap menu, misalnya pada file page/about.php.
1. Sistem Include: Kita memanggil kerangka yang sudah dibuat di langkah ke-2 menggunakan perintah include('template/header') dan include('template/footer').
2. Penyajian Konten: Di tengah-tengah panggilan tersebut, kita cukup meletakkan variabel $title dan $content.
3. Pembersihan: Dengan metode ini, kita menghapus semua kode HTML berlebih yang sebelumnya menyebabkan teks muncul dua kali atau berantakan di bawah halaman. Hasil akhirnya adalah tampilan yang "clean", di mana struktur layout tetap konsisten namun isinya berubah sesuai menu yang diklik.

### 5. Memvalidasi Alamat (Routing)
Sebagai penutup alur, kita memastikan bahwa alamat URL yang diketik pengunjung ```(misalnya localhost:8080/about)``` terhubung ke fungsi yang benar di Controller. Proses ini memastikan bahwa semua link yang sudah kita buat di navigasi header benar-benar menampilkan konten dengan layout yang seragam dan rapi.


# Praktikum 2
### STEP 1: MEMBUAT LAYOUT UTAMA
buat folder baru bernama layout di dalam `app/Views`. Di dalam folder tersebut, buat file main.php. File ini 
akan menjadi kerangka utama untuk semua halaman website.
Di dalam file main.php, buat struktur HTML lengkap seperti header, navigasi menu, sidebar, dan footer. 
Bagian yang paling penting adalah kode `<?= $this->renderSection('content') ?>`. Kode ini berfungsi sebagai 
tempat penyisipan konten dari halaman yang menggunakan layout ini. Jadi apapun yang ditulis di halaman child akan muncul tepat di posisi ini.
```
<!-- app/Views/layout/main.php -->
<section id="main">
    <?= $this->renderSection('content') ?>
</section>
```
### STEP2: MODIFIKASI FILE VIEW MENGGUNAKAN LAYOUT
Setelah layout utama selesai, semua file view yang sudah ada harus diubah agar menggunakan layout tersebut. Caranya, hapus semua kode HTML yang berulang seperti header, footer, dan sidebar. Ganti dengan tiga baris `kode: extend()` untuk memanggil layout, lalu `section()` dan `endSection()` untuk membungkus konten unik halaman tersebut.
Dengan cara ini, setiap halaman hanya fokus pada kontennya saja. Contohnya halaman home cukup menampilkan judul dan deskripsi, tanpa perlu menulis ulang kode header dan footer.
```
<!-- app/Views/home.php -->
<?= $this->extend('layout/main') ?>

<?= $this->section('content') ?>
<h1><?= $title; ?></h1>
<hr>
<p><?= $content; ?></p>
<?= $this->endSection() ?>
```
### STEP3: MEMBUAT VIEW CELL
View Cell adalah fitur CodeIgniter 4 yang memungkinkan kita membuat komponen kecil yang bisa dipanggil di mana saja. Cocok untuk widget yang muncul di banyak halaman, seperti daftar artikel terbaru di sidebar.

Langkah membuat View Cell:

**1**, buat folder Cells di dalam app. Lalu buat file `ArtikelTerkini.php` di dalam folder Cells. File ini berisi class yang 
akan mengambil data dari database.
Di dalam class tersebut, buat method `render()`. Method ini akan memanggil model ArtikelModel, mengambil 5 artikel 
terbaru, lalu menampilkannya ke view component.
```
// app/Cells/ArtikelTerkini.php
namespace App\Cells;

use CodeIgniter\View\Cell;
use App\Models\ArtikelModel;

class ArtikelTerkini extends Cell
{
    public function render()
    {
        $model = new ArtikelModel();
        $artikel = $model->orderBy('id', 'DESC')->limit(5)->findAll();
        return view('components/artikel_terkini', ['artikel' => $artikel]);
    }
}
```
**2**, buat folder components di dalam `app/Views`. Buat file `artikel_terkini`.php untuk menampilkan 
data artikel dalam bentuk list link.
```
<!-- app/Views/components/artikel_terkini.php -->
<h3>Artikel Terkini</h3>
<ul>
<?php foreach ($artikel as $row): ?>
    <li><a href="<?= base_url('/artikel/' . $row['slug']) ?>">
        <?= $row['judul'] ?>
    </a></li>
<?php endforeach; ?>
</ul>
```
 **3**, panggil `View Cell` di layout main.php pada bagian sidebar menggunakan perintah `view_cell()`.
 ```
<!-- app/Views/layout/main.php (bagian sidebar) -->
<aside id="sidebar">
    <?= view_cell('App\\Cells\\ArtikelTerkini::render') ?>
</aside>
```

# Praktikum 3
### 1. Penyesuaian Database dan Program
Untuk tugas ini, kolom created_at dengan tipe TIMESTAMP telah berhasil ditambahkan ke tabel artikel. Kode 
pada `ArtikelTerkini.php` juga sudah diperbarui menggunakan perintah `$model->orderBy('created_at', 'DESC')`. Hal ini 
memastikan aplikasi selalu mengambil data artikel yang paling baru diinput berdasarkan waktu sistem, bukan 
sekadar urutan ID. Seluruh langkah praktikum mulai dari pembuatan `Layout, penulisan CSS Flexbox`, hingga 
implementasi View Cell telah diselesaikan dengan improvisasi pada bagian penanganan error class.
### 2. Manfaat Utama View Layout
Manfaat utamanya adalah efisiensi dan konsistensi. Dengan View Layout, kita cukup menulis struktur dasar HTML 
(seperti header, navigasi, dan footer) satu kali saja. Halaman lain hanya perlu "mengisi" bagian kontennya. 
Ini memudahkan pemeliharaan kode; jika ada perubahan menu navigasi, kita hanya perlu mengubah satu 
file layout saja, dan seluruh halaman website akan otomatis ikut terupdate.
### 3. Perbedaan View Cell dan View Biasa
```View Biasa:``` Bersifat pasif dan sangat bergantung pada Controller. 
Data harus dikirimkan oleh Controller saat fungsi return view() dipanggil. 
Jika data tersebut dibutuhkan di banyak halaman (seperti sidebar), maka 
setiap Controller harus mengambil data yang sama secara berulang-ulang.
```View Cell:``` Bersifat mandiri dan modular. View Cell memiliki logic atau 
"Controller kecil" sendiri di dalamnya. Ia bisa dipanggil di mana saja (di dalam view manapun) 
tanpa harus melibatkan Controller utama halaman tersebut. Ini sangat cocok untuk komponen 
yang muncul di banyak tempat seperti widget berita, profil user, atau keranjang belanja.
### 4. Improvisasi View Cell dengan Kategori
Untuk membatasi tampilan hanya pada kategori tertentu, kita bisa menambahkan parameter pada fungsi render() dan menggunakan klausa where pada query database.

`Contoh Logic pada ArtikelTerkini.php:`
```
PHP
public function render(string $kategori = 'berita'): string
{
    $model = new \App\Models\ArtikelModel();
    // Mengambil artikel yang hanya memiliki kategori tertentu
    $artikel = $model->where('kategori', $kategori)
                     ->orderBy('created_at', 'DESC')
                     ->limit(5)
                     ->findAll();

    return view('components/artikel_terkini', ['artikel' => $artikel]);
}
```

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ca452061-cbac-46db-9905-51eec01e7509" />


# Praktikun 4
Proses dimulai dengan mempersiapkan infrastruktur data, yaitu membuat tabel user pada `MySQL` untuk menyimpan informasi akun. 
Setelah tabel siap, dibuat sebuah UserModel yang berfungsi sebagai penghubung logika aplikasi dengan database agar data 
pengguna bisa dikelola. Langkah selanjutnya adalah membangun `User Controller` untuk menangani alur masuk dan keluar pengguna, 
di mana sistem akan mengambil input email dan password, lalu memverifikasinya dengan data di database menggunakan fungsi keamanan 
hash.Agar sistem bisa diuji, digunakan fitur Seeder untuk memasukkan data akun admin secara otomatis ke dalam database 
tanpa harus menginputnya secara manual lewat form. Untuk menjaga keamanan halaman admin, dibuat sebuah Auth Filter yang 
bertindak sebagai penjaga pintu; filter ini akan mencegat setiap permintaan akses dan memastikan hanya pengguna yang sudah 
login yang bisa masuk, sementara yang belum `login` akan diarahkan kembali ke halaman masuk. Terakhir, semua komponen
tersebut diintegrasikan melalui konfigurasi rute dan filter agar halaman dashboard admin benar-benar terlindungi secara otomatis oleh sistem autentikasi yang telah dibuat.
### Halaman Login
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/651cbf95-6385-4471-adeb-6b64fe4dac78" />

### Halaman Setelah Login
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4b1a2f9a-d3fa-4c9a-bbf8-c158af5957db" />


# Praktikun 5
Proses dimulai dengan memodifikasi logika pada Controller Artikel untuk menerapkan sistem Pagination, di mana daftar 
artikel yang sebelumnya ditampilkan secara keseluruhan kini dibatasi (misalnya 10 data per halaman) menggunakan library 
pager bawaan `CodeIgniter 4`. Bersamaan dengan itu, dikembangkan fitur Pencarian dengan menangkap variabel kata kunci dari
URL dan mengintegrasikannya ke dalam query database menggunakan perintah like, sehingga sistem dapat menyaring artikel 
berdasarkan judul yang spesifik.Pada bagian antarmuka `(View)`, sebuah form pencarian ditambahkan di atas tabel untuk memfasilitasi
input pengguna, serta navigasi nomor halaman diletakkan di bawah tabel agar pengguna dapat berpindah antar halaman data. 
Untuk meningkatkan pengalaman pengguna, dilakukan penyesuaian pada logika penomoran di dalam tabel agar angka yang muncul 
adalah nomor urut baris yang konsisten `(1, 2, 3...)`, bukan lagi menampilkan ID unik dari database yang biasanya tidak 
berurutan. Terakhir, konfigurasi pagination dipastikan tetap membawa parameter pencarian saat berpindah halaman agar hasil filter data tidak hilang atau ter-reset.
<img width="1920" height="1080" alt="Screenshot (276)" src="https://github.com/user-attachments/assets/08e21360-cac9-4462-a143-5a17f8f6b04c" />


# Praktikum 6
### 1. Persiapan Relasi pada Struktur Database
Langkah pertama dimulai dari fondasi paling dasar, yaitu tabel di database. Kita memastikan bahwa tabel artikel memiliki 
kolom khusus bernama id_kategori yang berfungsi sebagai "kunci penghubung". Di sini, kita tidak lagi menuliskan nama kategori 
secara langsung di tabel artikel, melainkan hanya menyimpan angka ID-nya saja. Dengan cara ini, tabel artikel dan tabel kategoris 
menjadi saling terhubung satu sama lain, menciptakan sebuah relasi yang memungkinkan satu label kategori digunakan oleh banyak artikel tanpa terjadi duplikasi data yang berantakan.

### 2. Pengaturan Izin dan Logika Penggabungan pada Model
Langkah kedua berlanjut ke bagian Model, yang bertugas sebagai penjaga pintu database. Kita wajib mendaftarkan id_kategori 
ke dalam daftar bidang yang diizinkan agar sistem CodeIgniter mau memproses dan menyimpan data tersebut saat dikirim dari 
form. Selain itu, Model dibekali dengan perintah penggabungan atau Join. Perintah ini bekerja secara otomatis untuk menarik 
nama asli kategori dari tabel kategoris saat kita menampilkan daftar artikel, sehingga di layar kamu muncul tulisan seperti "Teknologi" atau "Edukasi" dan bukannya sekadar angka ID yang sulit dimengerti.

### 3. Pengaturan Logika Operasional pada Controller
Langkah ketiga adalah mengatur Controller yang berperan sebagai otak dari aplikasi ini. Controller bertugas menjembatani 
antara data di database dan apa yang tampil di layar pengguna. Saat kamu membuka halaman tambah atau ubah, Controller 
memerintahkan sistem untuk mengambil seluruh daftar kategori yang tersedia agar bisa ditampilkan sebagai pilihan. Begitu 
kamu menekan tombol simpan, Controller akan menangkap data tersebut, membuat slug otomatis untuk alamat URL, dan memastikan 
semua variabel—termasuk variabel pencarian—sudah siap digunakan agar aplikasi tidak mengalami error saat memuat halaman.

### 4. Pembuatan Antarmuka Dinamis pada View
Langkah keempat berfokus pada tampilan atau View yang kamu lihat di browser. Di sini, kita membuat dropdown kategori 
yang sifatnya dinamis menggunakan perulangan. Artinya, pilihan kategori yang muncul di layar akan selalu mengikuti d
ata terbaru yang ada di database. Khusus pada bagian edit artikel, View ini juga memiliki kecerdasan untuk mendeteksi
kategori mana yang sudah dipilih sebelumnya. Jika sebuah artikel terdaftar di bawah kategori `"Lifestyle"`, maka saat 
tombol ubah diklik, pilihan tersebut akan langsung muncul secara otomatis di kotak dropdown tanpa perlu kamu cari lagi.

### 5. Sinkronisasi Final dan Eksekusi Perubahan
Langkah terakhir adalah memastikan semua file tersebut saling berkomunikasi dengan benar. Masalah yang sebelumnya kamu 
hadapi, di mana data tidak berubah atau dropdown kosong, diselesaikan dengan menyatukan nama variabel dan nama tabel di
semua file. Dengan atribut form yang sudah memiliki tujuan alamat yang jelas dan Model yang sudah diberi izin akses, 
sekarang setiap kali kamu memilih kategori dan menekan tombol kirim, data tersebut akan mengalir lancar menuju database 
dan tersimpan secara permanen. Akhirnya, daftar artikel kamu kini menampilkan label kategori yang sesuai dan fitur 
hapus maupun ubah berfungsi dengan sempurna.
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/88314884-3be9-46e7-8e96-567a913e33a6" />
