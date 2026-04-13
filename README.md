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
