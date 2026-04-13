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
