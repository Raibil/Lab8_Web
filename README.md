<img width="811" height="526" alt="image" src="https://github.com/user-attachments/assets/6d83fb48-3351-4dfa-a536-d8ff548bf91f" /># Lab8Web - Praktikum 8: PHP dan Database MySQL

| | |
|---|---|
| **Nama** | Raihan Nabil Mas'ud |
| **NIM** | 312510094 |
| **Kelas** | I.25.1A |
| **Mata Kuliah** | Pemrograman Web |

## Tujuan

1. Mahasiswa mampu memahami konsep dasar Database.
2. Mahasiswa mampu memahami konsep dasar CRUD menggunakan PHP.
3. Mahasiswa mampu membuat program CRUD sederhana menggunakan PHP.

## Struktur Folder

```
lab8_php_database/
├── gambar/          # folder penyimpanan gambar barang
├── koneksi.php      # koneksi ke database
├── index.php        # Read   - menampilkan data
├── tambah.php       # Create - menambah data
├── ubah.php         # Update - mengubah data
├── hapus.php        # Delete - menghapus data
└── style.css        # tampilan halaman
```

[Screenshot]

*Gambar: Struktur folder proyek di VSCode / File Explorer*

---

## 1. Persiapan

Alat yang digunakan: VSCode, XAMPP (Apache, MySQL/MariaDB, PHP), dan browser. Folder `lab8_php_database` dibuat di dalam `htdocs` pada direktori XAMPP.

### 1.1 Menjalankan MySQL Server

Buka **XAMPP Control Panel**, lalu klik **Start** pada modul **Apache** dan **MySQL** sampai statusnya hijau (running).

<img width="811" height="526" alt="image" src="https://github.com/user-attachments/assets/a3eb8f4b-2518-41bc-8d59-4d991e62f202" />

*Gambar 1. XAMPP Control Panel dengan Apache dan MySQL berjalan*

### 1.2 Mengakses MySQL Client menggunakan phpMyAdmin

Setelah Apache dan MySQL berjalan, buka browser dan akses `http://localhost/phpmyadmin/`.

<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/8bad0a5a-2488-4b62-8d76-5fe5cd15d4af" />

*Gambar 2. Halaman phpMyAdmin*

---

## 2. Membuat Database: Studi Kasus Data Barang

Struktur tabel `data_barang`:

| Nama Field | Tipe Data | Ukuran | Keterangan |
|---|---|---|---|
| id_barang | INT | 10 | Primary Key, Auto Increment |
| kategori | VARCHAR | 30 | |
| nama | VARCHAR | 50 | |
| gambar | VARCHAR | 100 | |
| harga_beli | DECIMAL | 10,0 | |
| harga_jual | DECIMAL | 10,0 | |
| stok | INT | 4 | |

### 2.1 Membuat Database

Dijalankan melalui tab **SQL** di phpMyAdmin:

```sql
CREATE DATABASE latihan1;
```

<img width="444" height="431" alt="image" src="https://github.com/user-attachments/assets/6b05f139-521d-491c-80e6-c0c2308b82f7" />

*Gambar 3. Database `latihan1` berhasil dibuat*

### 2.2 Membuat Tabel

Pilih database `latihan1`, lalu jalankan:

```sql
CREATE TABLE data_barang (
  id_barang int(10) auto_increment Primary Key,
  kategori varchar(30),
  nama varchar(50),
  gambar varchar(100),
  harga_beli decimal(10,0),
  harga_jual decimal(10,0),
  stok int(4)
);
```

Kolom `id_barang` menjadi primary key dengan auto increment sehingga nilainya terisi otomatis.

<img width="653" height="555" alt="image" src="https://github.com/user-attachments/assets/40bf5976-f1da-43b3-90ce-22ed071bfc46" />

*Gambar 4. Perintah CREATE TABLE di phpMyAdmin*

<img width="940" height="511" alt="image" src="https://github.com/user-attachments/assets/917396e0-6a51-44fb-abe6-05d17987be7a" />

*Gambar 5. Struktur tabel `data_barang`*

### 2.3 Menambahkan Data

```sql
INSERT INTO data_barang (kategori, nama, gambar, harga_beli, harga_jual, stok)
VALUES ('Elektronik', 'HP Samsung Android', 'hp_samsung.jpg', 2000000, 2400000, 5),
('Elektronik', 'HP Xiaomi Android', 'hp_xiaomi.jpg', 1000000, 1400000, 5),
('Elektronik', 'HP OPPO Android', 'hp_oppo.jpg', 1800000, 2300000, 5);
```

<img width="940" height="201" alt="image" src="https://github.com/user-attachments/assets/b641a545-892c-4817-9a30-1ac00d572c95" />

*Gambar 6. Data barang pada tabel `data_barang`*

---

## 3. Membuat Program CRUD

Folder proyek diakses melalui `http://localhost/lab8_php_database/`.

### 3.1 File Koneksi Database (`koneksi.php`)

File ini berisi pengaturan koneksi ke MySQL (host, user, password, nama database) menggunakan `mysqli_connect()`. Jika koneksi gagal, pesan error ditampilkan dan program dihentikan dengan `die()`. File ini di-include oleh file PHP lainnya.

```php
<?php
$host = "localhost";
$user = "root";
$pass = "";
$db   = "latihan1";

$conn = mysqli_connect($host, $user, $pass, $db);
if ($conn == false)
{
    echo "Koneksi ke server gagal.";
    die();
} #else echo "Koneksi berhasil";
?>
```

Untuk menguji koneksi, tanda komentar pada `echo "Koneksi berhasil"` dihapus sementara lalu file dibuka di browser.

<img width="772" height="156" alt="image" src="https://github.com/user-attachments/assets/62226d4a-aba4-4e32-8803-ed0568fbdf76" />

*Gambar 7. Hasil tes koneksi: "Koneksi berhasil"*

### 3.2 Menampilkan Data / Read (`index.php`)

`index.php` menjalankan query `SELECT * FROM data_barang`, lalu menampilkan semua baris dalam tabel HTML dengan perulangan `while` dan `mysqli_fetch_array()`. Setiap baris memiliki link **Ubah** dan **Hapus** yang membawa parameter `id`. Jika data kosong, ditampilkan teks "Belum ada data".

Potongan kode utama:

```php
<?php
include("koneksi.php");

$sql = 'SELECT * FROM data_barang';
$result = mysqli_query($conn, $sql);
?>
...
<?php while ($row = mysqli_fetch_array($result)): ?>
<tr>
    <td><img src="gambar/<?= htmlspecialchars($row['gambar']); ?>" width="80"></td>
    <td><?= htmlspecialchars($row['nama']); ?></td>
    ...
    <td>
        <a href="ubah.php?id=<?= $row['id_barang']; ?>">Ubah</a>
        <a href="hapus.php?id=<?= $row['id_barang']; ?>">Hapus</a>
    </td>
</tr>
<?php endwhile; ?>
```

<img width="940" height="249" alt="image" src="https://github.com/user-attachments/assets/ecb1cadb-b2bc-49cf-a2ad-bbc86882f89b" />

*Gambar 8. Tampilan halaman index (daftar data barang)*

### 3.3 Menambah Data / Create (`tambah.php`)

`tambah.php` berisi form input barang (nama, kategori, harga jual, harga beli, stok, file gambar) dengan `enctype="multipart/form-data"` agar bisa mengunggah file. Saat tombol **Simpan** ditekan:

1. Data dikirim dengan method `POST`.
2. File gambar dipindahkan ke folder `gambar/` dengan `move_uploaded_file()`.
3. Data disimpan ke database dengan query `INSERT` (prepared statement).
4. Pengguna diarahkan kembali ke `index.php`.

Potongan kode utama:

```php
$stmt = mysqli_prepare($conn, "INSERT INTO data_barang (nama, kategori, harga_jual, harga_beli, stok, gambar) VALUES (?, ?, ?, ?, ?, ?)");
mysqli_stmt_bind_param($stmt, "ssiiis", $nama, $kategori, $harga_jual, $harga_beli, $stok, $gambar);
mysqli_stmt_execute($stmt);

header('location: index.php');
```

<img width="940" height="267" alt="image" src="https://github.com/user-attachments/assets/25647741-4942-4719-ad17-bb80462a506a" />

*Gambar 9. Form Tambah Barang*

<img width="940" height="274" alt="image" src="https://github.com/user-attachments/assets/d444098d-d253-4bf7-a3bc-f37abf8cb737" />

*Gambar 10. Data baru tampil di halaman index*

### 3.4 Mengubah Data / Update (`ubah.php`)

`ubah.php` mengambil data berdasarkan `id` dari URL (`$_GET['id']`) dan menampilkannya pada form yang sudah terisi. `id` disimpan dalam input `hidden`. Saat disimpan, query `UPDATE` dijalankan; kolom gambar hanya diperbarui jika pengguna memilih file gambar baru. Fungsi `is_select()` menandai pilihan kategori yang sesuai dengan data.

Potongan kode utama:

```php
$stmt = mysqli_prepare($conn, "UPDATE data_barang SET nama=?, kategori=?, harga_jual=?, harga_beli=?, stok=? WHERE id_barang=?");
mysqli_stmt_bind_param($stmt, "ssiiii", $nama, $kategori, $harga_jual, $harga_beli, $stok, $id);
mysqli_stmt_execute($stmt);

header('location: index.php');
```

<img width="940" height="257" alt="image" src="https://github.com/user-attachments/assets/5b414f23-fc45-4160-8047-21eb88ee3a62" />

*Gambar 11. Form Ubah Barang*

<img width="940" height="268" alt="image" src="https://github.com/user-attachments/assets/2646f148-32d2-4055-8324-de125efaac41" />

*Gambar 12. Data berubah di halaman index setelah diedit*

### 3.5 Menghapus Data / Delete (`hapus.php`)

`hapus.php` menerima `id` lewat URL, menjalankan query `DELETE`, lalu mengarahkan kembali ke `index.php`. Pada halaman index ditambahkan konfirmasi (`confirm`) sebelum data dihapus.

```php
<?php
include_once 'koneksi.php';

$id = $_GET['id'];
$stmt = mysqli_prepare($conn, "DELETE FROM data_barang WHERE id_barang = ?");
mysqli_stmt_bind_param($stmt, "i", $id);
mysqli_stmt_execute($stmt);

header('location: index.php');
exit;
?>
```

<img width="940" height="272" alt="image" src="https://github.com/user-attachments/assets/8912f881-1238-417e-ba0e-27cce71b3b58" />

*Gambar 13. Konfirmasi sebelum menghapus data*

<img width="940" height="230" alt="image" src="https://github.com/user-attachments/assets/cdc37331-4843-49ee-9107-0542382f11be" />

*Gambar 14. Halaman index setelah data dihapus*

---

## Kesimpulan

Dari praktikum ini dapat disimpulkan bahwa PHP dapat terhubung ke database MySQL menggunakan ekstensi MySQLi. Operasi dasar pengelolaan data (CRUD: Create, Read, Update, Delete) berhasil dibuat melalui `tambah.php`, `index.php`, `ubah.php`, dan `hapus.php`. Penggunaan prepared statement membantu mencegah SQL injection, dan pemisahan file koneksi memudahkan pemeliharaan program.
