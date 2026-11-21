Praktikum 8: PHP dan Database MySQL

Nama: Lingga Chezya Poetra

NIM: 312510169

Kelas: Teknik Informatika

Mata Kuliah: Pemrograman Web

Tujuan Praktikum

Memahami konsep dasar pengelolaan Database menggunakan MySQL.

Memahami konsep dasar operasi CRUD (Create, Read, Update, Delete) pada aplikasi web berbasis PHP.

Mampu membangun aplikasi web CRUD sederhana yang terintegrasi dengan database MySQL dan fitur unggah file (file upload).

Langkah-Langkah & Penjelasan Praktikum

1. Persiapan Server & Pembuatan Database MySQL

Penjelasan:

Sebelum membuat program PHP, server lokal harus dijalankan terlebih dahulu menggunakan XAMPP dengan mengaktifkan modul Apache (sebagai web server) dan MySQL (sebagai database server). Pengelolaan database dilakukan melalui antarmuka phpMyAdmin (http://localhost/phpmyadmin/).

Membuat Database (latihan1):

Perintah CREATE DATABASE digunakan untuk membuat wadah penyimpanan data baru.

CREATE DATABASE latihan1;


Membuat Tabel (data_barang):

Tabel data_barang dibuat dengan kolom id_barang sebagai Primary Key dengan fitur AUTO_INCREMENT agar nomor ID terisi otomatis secara berurutan setiap kali ada data baru yang masuk.

CREATE TABLE data_barang (
    id_barang int(10) auto_increment Primary Key,
    kategori varchar(30),
    nama varchar(30),
    gambar varchar(100),
    harga_beli decimal(10,0),
    harga_jual decimal(10,0),
    stok int(4)
);


Menambahkan Data Awal:

Perintah INSERT INTO digunakan untuk mengisi 3 data awal sebagai data uji coba sebelum aplikasi PHP dibuat.

INSERT INTO data_barang (kategori, nama, gambar, harga_beli, harga_jual, stok)
VALUES 
('Elektronik', 'HP Samsung Android', 'hp_samsung.jpg', 2000000, 2400000, 5),
('Elektronik', 'HP Xiaomi Android', 'hp_xiaomi.jpg', 1000000, 1400000, 5),
('Elektronik', 'HP OPPO Android', 'hp_oppo.jpg', 1800000, 2300000, 5);


Screenshot Database & Tabel:


2. Membuat Koneksi Database (koneksi.php)

Penjelasan:

Agar skrip PHP dapat membaca dan memanipulasi data di MySQL, diperlukan file jembatan bernama koneksi.php di dalam folder proyek htdocs/lab8_php_database/.

Variabel $host, $user, $pass, dan $db menyimpan konfigurasi server lokal XAMPP (localhost, user root, tanpa password, dan database latihan1).

Fungsi mysqli_connect() digunakan untuk membuka koneksi ke server MySQL. Jika koneksi gagal ($conn == false), skrip akan menghentikan proses (die()) dan menampilkan pesan error.

Screenshot Tes Koneksi:


3. Menampilkan Data (index.php - Operasi Read)

Penjelasan:

File index.php berfungsi sebagai halaman utama untuk menampilkan seluruh daftar barang dari database ke dalam tabel HTML.

Perintah include("koneksi.php"); memanggil konfigurasi koneksi database yang sudah dibuat sebelumnya.

Query SELECT * FROM data_barang dijalankan melalui mysqli_query() untuk mengambil seluruh baris data.

Perulangan while($row = mysqli_fetch_array($result)) digunakan untuk memecah hasil query menjadi array dan mencetaknya satu per satu ke dalam baris tabel (<tr>).

Pada kolom Aksi, ditambahkan tautan menuju ubah.php?id=... dan hapus.php?id=... dengan mengirimkan nilai id_barang melalui metode GET agar sistem tahu baris data mana yang dipilih pengguna.

Screenshot Halaman Utama (index.php):


4. Menambah Data (tambah.php - Operasi Create)

Penjelasan:

File tambah.php digunakan untuk menambahkan data barang baru beserta file gambarnya.

Tag <form> menggunakan atribut method="post" untuk mengirim data secara aman dan enctype="multipart/form-data" agar form mendukung pengiriman berkas/gambar ($_FILES).

Saat tombol Simpan ditekan (isset($_POST['submit'])), file gambar yang diunggah dipindahkan dari penyimpanan sementara (tmp_name) ke folder lokal gambar/ menggunakan fungsi move_uploaded_file().

Selanjutnya, query INSERT INTO data_barang ... dijalankan untuk menyimpan teks inputan dan nama file gambar ke database, lalu fungsi header('location: index.php'); mengarahkan halaman kembali ke halaman utama.

Screenshot Tambah Barang (tambah.php):


5. Mengubah Data (ubah.php - Operasi Update)

Penjelasan:

File ubah.php bertugas memperbarui data barang yang sudah tersimpan sebelumnya.

Pertama, skrip menangkap id_barang dari URL menggunakan $_GET['id'], lalu menjalankan query SELECT * FROM data_barang WHERE id_barang = ... untuk menampilkan data lama ke dalam atribut value pada masing-masing kolom input form.

Fungsi bantuan is_select() digunakan untuk mencocokkan kategori lama barang agar otomatis terpilih (selected) pada menu dropdown <select>.

Setelah data diedit dan tombol Simpan ditekan, query UPDATE data_barang SET ... WHERE id_barang = ... dijalankan. Jika pengguna tidak mengunggah gambar baru, kolom gambar pada database tetap mempertahankan foto yang lama.

Screenshot Ubah Barang (ubah.php):


6. Menghapus Data (hapus.php - Operasi Delete)

Penjelasan:

File hapus.php berfungsi menghapus data barang tertentu dari tabel database.

Skrip mengambil parameter id barang yang dikirim saat pengguna mengklik tautan Hapus di halaman utama menggunakan $_GET['id'].

Perintah SQL DELETE FROM data_barang WHERE id_barang = '{$id}' dieksekusi untuk menghapus baris spesifik tersebut agar tidak menghapus seluruh isi tabel.

Setelah terhapus, header('location: index.php'); langsung me-refresh tampilan kembali ke halaman daftar barang.