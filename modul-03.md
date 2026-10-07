# Modul 3: Dart Berorientasi Objek dan Asinkron

## Maksud dan Tujuan

1. Membuat class dan object dengan constructor, getter/setter, dan enkapsulasi.
2. Menerapkan inheritance, abstract class, polymorphism, interface, dan mixin.
3. Menggunakan Future, async, dan await serta menangani error pada proses asinkron.
4. Menggunakan Stream untuk memproses data yang datang berulang.
5. Menggabungkan OOP dan asinkron dalam program konsol sederhana.

## Alat, Bahan, dan Perangkat

1. **Perangkat Keras**
- Laptop/komputer dengan **RAM minimal 8 GB**.
- Ruang penyimpanan kosong **minimal 10 GB**.
- Koneksi internet.
2. **Perangkat Lunak**
- **Android Studio** – untuk pengembangan aplikasi Flutter.
- **Flutter SDK** – framework untuk membuat aplikasi.
- **Dart SDK** – bahasa pemrograman Flutter.
- **Flutter & Dart Plugin** – mendukung pengembangan Flutter di Android Studio.
- **Terminal** – menjalankan perintah Flutter dan Dart.

**Catatan:** Dart SDK sudah termasuk dalam Flutter SDK, sehingga tidak perlu diinstal secara terpisah.

## Dasar Teori

### 1. Class dan Object

Class adalah cetak biru yang berisi properti (data) dan method (perilaku). Object adalah wujud nyata (instance) dari class. Constructor menyiapkan object saat dibuat; Dart mendukung constructor biasa dan named constructor.

### 2. Enkapsulasi

Data disembunyikan dan diakses lewat getter/setter agar bisa divalidasi. Di Dart, anggota yang diawali `_` bersifat privat pada tingkat library (file), bukan tingkat class.

### 3. Inheritance, Abstract Class, Interface, Mixin, Polymorphism

| **Konsep** | **Kata kunci** | **Fungsi** |
|---|---|---|
| Pewarisan | extends | Class turunan mewarisi anggota class induk |
| Abstract class | abstract | Kerangka yang tidak bisa diinstansiasi; memuat method yang wajib di-override |
| Interface | implements | Class wajib mengimplementasikan seluruh anggota kontrak |
| Mixin | mixin, with | Memakai kemampuan tambahan tanpa pewarisan |
| Polymorphism | @override | Satu pemanggilan method menghasilkan perilaku berbeda sesuai tipe object |

### 4. Pemrograman Asinkron

Operasi yang lambat (jaringan, database, file) tidak boleh memblokir program. Dart menyediakan:

- **Future&lt;T&gt;**: satu nilai yang tersedia di masa depan.
- **async / await**: menulis kode asinkron dengan alur seperti kode biasa; await hanya boleh di dalam fungsi async.
- **Future.wait**: menjalankan beberapa Future bersamaan dan menunggu semuanya selesai.
- **try / catch / finally**: menangani error pada proses asinkron.

### 5. Stream

Stream&lt;T&gt; menghasilkan **banyak** nilai seiring waktu. Dapat dibuat dengan async\* + yield atau StreamController, dan dikonsumsi dengan await for atau listen().

|  | **Future** | **Stream** |
|---|---|---|
| Jumlah nilai | Satu | Banyak |
| Dikonsumsi dengan | await / then | await for / listen |
| Contoh | Ambil satu data dari server | Progres unduhan, data sensor |

## Pre-Test

1. Apa fungsi main() pada program Dart?
2. Jelaskan perbedaan final dan const.
3. Apa arti deklarasi String? Alamat:?
4. Apa yang Anda ketahui tentang class dan object? Berikan satu contoh dalam kehidupan sehari-hari.

## Praktikum

### Praktikum 1 - Class, Object, Constructor, dan Enkapsulasi

Tujuan: membuat class dan object, menggunakan constructor, getter/setter, dan properti privat.

Langkah pelaksanaan di Android Studio:

1. Klik kanan folder modul3 → New → Dart File → ketik p1\_class → Enter.
2. Salin kode di bawah ke editor.
3. Klik ▶ di samping void main() → Run 'p1\_class.dart'.
4. Amati panel Run dan cocokkan dengan hasil yang diharapkan. Perhatikan bahwa m2.ipk = 5.0 ditolak oleh setter.
5. Ubah nilai tersebut (mis. menjadi -1), jalankan ulang, lalu amati perubahannya.

File: p1\_class.dart

```dart
class Mahasiswa {
  final String nim;
  String nama;
  double _ipk; // diawali _ = privat (per file/library)

  // Constructor biasa
  Mahasiswa(this.nim, this.nama, this._ipk);

  // Named constructor
  Mahasiswa.baru(this.nim, this.nama) : _ipk = 0.0;

  // Getter
  double get ipk => _ipk;

  // Setter dengan validasi
  set ipk(double nilai) {
    if (nilai >= 0 && nilai <= 4) {
      _ipk = nilai;
    } else {
      print('IPK tidak valid: $nilai');
    }
  }

  void perkenalan() {
    print('Saya $nama (NIM $nim), IPK ${_ipk.toStringAsFixed(2)}');
  }

  @override
  String toString() => 'Mahasiswa($nim, $nama)';
}

void main() {
  var m1 = Mahasiswa('2021001', 'Budi', 3.5);
  var m2 = Mahasiswa.baru('2021002', 'Siti');

  m1.perkenalan();
  m2.perkenalan();

  m2.ipk = 3.8;   // memakai setter
  m2.ipk = 5.0;   // ditolak validasi
  print(m2.ipk);  // memakai getter
  print(m2);      // memakai toString()
}
```

Jalankan programnya dan amati hasilnya.

```dart
  @override
  String toString() => 'Mahasiswa($nim, $nama)';
}

void main() {
  var m1 = Mahasiswa('2021001', 'Budi', 3.5);
  var m2 = Mahasiswa.baru('2021002', 'Siti');

  m1.perkenalan();
  m2.perkenalan();

  m2.ipk = 3.8;   // memakai setter
  m2.ipk = 5.0;   // ditolak validasi
  print(m2.ipk);  // memakai getter
  print(m2);      // memakai toString()
}
```

### Praktikum 2 - Inheritance, Abstract Class, Polymorphism, Interface, Mixin

**Tujuan: menerapkan pewarisan, abstraksi, polimorfisme, interface, dan mixin.**

Langkah pelaksanaan di Android Studio:

1. Buat file p2\_pewarisan.dart di folder modul3 (New → Dart File).
2. Salin kode di bawah ke editor.
3. Klik ▶ di samping void main() → Run 'p2\_pewarisan.dart'.
4. Amati urutan output info() pada Dosen dan Mahasiswa. Pada Mahasiswa, baris tambahan muncul karena method di-*override*.
5. Tambahkan var x = Pengguna('A'); di dalam main(). Perhatikan garis bawah merah yang muncul di editor sebelum program dijalankan, lalu hapus baris tersebut.

```dart
// Abstract class: tidak bisa diinstansiasi
abstract class Pengguna {
  final String nama;
  Pengguna(this.nama);

  String peran(); // wajib di-override
  void info() => print('$nama - ${peran()}');
}
// Mixin: kemampuan tambahan yang bisa dipakai banyak class
mixin Login {
  bool _masuk = false;

  void login() {
    _masuk = true;
    print('Login berhasil');
  }

  bool get sudahLogin => _masuk;
}

// Interface (setiap class di Dart bisa jadi interface)
abstract class Notifikasi {
  void kirim(String pesan);
}

// Inheritance
class Dosen extends Pengguna with Login {
  final String nidn;
  Dosen(super.nama, this.nidn);

  @override
  String peran() => 'Dosen';
}

class Mahasiswa extends Pengguna with Login implements Notifikasi {
  Mahasiswa(super.nama);

  @override
  String peran() => 'Mahasiswa';

  @override
  void kirim(String pesan) => print('Notifikasi untuk $nama: $pesan');

  @override
  void info() {
    super.info(); // memanggil method induk
    print('(status mahasiswa aktif)');
  }
}

void main() {
  List<Pengguna> daftar = [Dosen('Dr. Andi', '0412345'), Mahasiswa('Budi')];

  // Polymorphism: satu pemanggilan, perilaku berbeda
  for (var p in daftar) {
    p.info();
  }

  var mhs = Mahasiswa('Siti');
  mhs.login();
  print('Sudah login: ${mhs.sudahLogin}');
  mhs.kirim('Jadwal praktikum diubah');

  // Pengecekan tipe
  for (var p in daftar) {
    if (p is Dosen) {
      print('${p.nama} memiliki NIDN ${p.nidn}');
    }
  }
}
```

Jalankan file tersbut dan amati hasilnya.

```dart
  void login() {
    _masuk = true;
    print('Login berhasil');   }
  bool get sudahLogin => _masuk;
}

// Interface (setiap class di Dart bisa jadi interface)
abstract class Notifikasi {
  void kirim(String pesan); }
// Inheritance
class Dosen extends Pengguna with Login {
  final String nidn;
  Dosen(super.nama, this.nidn);

  @override
  String peran() => 'Dosen'; }

class Mahasiswa extends Pengguna with Login implements Notifikasi {
  Mahasiswa(super.nama);

  @override
  String peran() => 'Mahasiswa';
  @override
  void kirim(String pesan) => print('Notifikasi untuk $nama: $pesan');
  @override
  void info() {
    super.info(); // memanggil method induk
    print('(status mahasiswa aktif)');
  } }

void main() {
  List<Pengguna> daftar = [Dosen('Dr. Andi', '0412345'), Mahasiswa('Budi')];

  // Polymorphism: satu pemanggilan, perilaku berbeda
  for (var p in daftar) {
    p.info();
  }
  var mhs = Mahasiswa('Siti');
  mhs.login();
  print('Sudah login: ${mhs.sudahLogin}');
  mhs.kirim('Jadwal praktikum diubah');

  // Pengecekan tipe
  for (var p in daftar) {
    if (p is Dosen) {
      print('${p.nama} memiliki NIDN ${p.nidn}');
    }
  }
}
```

### Praktikum 3 — Future, async, dan await

**Tujuan:** memahami pemrograman asinkron dengan Future, async/await, dan penanganan error.

**Langkah pelaksanaan di Android Studio:**

1. Buat file p3\_future.dart di folder modul3 (**New → Dart File**).
2. Salin kode di bawah ke editor.
3. Klik **▶** di samping void main() → **Run 'p3\_future.dart'**. Program berjalan sekitar 8 detik, jangan dihentikan.
4. Perhatikan **jeda waktu** antarbaris di panel **Run**. Baris C tampil lebih dulu daripada B karena Future berjalan di latar.
5. (Opsional) Pasang *breakpoint* pada baris print('A. Mulai');, jalankan dengan **Debug**, lalu tekan F8 berulang untuk melihat alur eksekusi.

**File:** p3\_future.dart

```dart
Future<String> ambilData() async {
  await Future.delayed(const Duration(seconds: 2));
  return 'Data berhasil diambil';
}

Future<int> bagi(int a, int b) async {
  if (b == 0) throw Exception('Pembagi tidak boleh nol');
  return a ~/ b;
}

Future<void> tugas(String nama, int detik) async {
  await Future.delayed(Duration(seconds: detik));
  print('Tugas $nama selesai');
}

void main() async {
  // 1. Tanpa await: urutan cetak berbeda
  print('A. Mulai');
  ambilData().then((v) => print('B. $v (via then)'));
  print('C. Baris ini tercetak lebih dulu');

  // 2. Dengan await: berurutan
  await Future.delayed(const Duration(seconds: 3));
  String hasil = await ambilData();
  print('D. $hasil');




  // 3. Penanganan error
  try {
    print(await bagi(10, 2));
    print(await bagi(10, 0));
  } catch (e) {
    print('Terjadi error: $e');
  } finally {
    print('Blok finally selalu dijalankan');
  }

  // 4. Menjalankan beberapa Future bersamaan
  var waktuMulai = DateTime.now();
  await Future.wait([tugas('X', 2), tugas('Y', 3)]);
  var durasi = DateTime.now().difference(waktuMulai).inSeconds;
  print('Total waktu: $durasi detik (bukan 5)');
}
```

Jalankan file tersebut dan amati hasilnya.

```dart
  // 3. Penanganan error
  try {
    print(await bagi(10, 2));
    print(await bagi(10, 0));
  } catch (e) {
    print('Terjadi error: $e');
  } finally {
    print('Blok finally selalu dijalankan');
  }

  // 4. Menjalankan beberapa Future bersamaan
  var waktuMulai = DateTime.now();
  await Future.wait([tugas('X', 2), tugas('Y', 3)]);
  var durasi = DateTime.now().difference(waktuMulai).inSeconds;
  print('Total waktu: $durasi detik (bukan 5)');
}
```

### Praktikum 4 — Stream

**Tujuan:** menggunakan Stream untuk data yang datang berkali-kali.

**Perbedaan:** Future menghasilkan **satu** nilai; Stream menghasilkan **banyak** nilai seiring waktu.

**Langkah pelaksanaan di Android Studio:**

1. Buat file p4\_stream.dart di folder modul3 (**New → Dart File**).
2. Salin kode di bawah ke editor. Jika StreamController bergaris merah, letakkan kursor di sana dan tekan Alt+Enter untuk menambahkan import 'dart:async';.
3. Klik **▶** di samping void main() → **Run 'p4\_stream.dart'**.
4. Amati angka hitung mundur yang muncul satu per satu setiap detik pada panel **Run**.
5. Amati urutan pesan dari StreamController, termasuk pesan error dan Stream ditutup.

**File:** p4\_stream.dart

```dart
import 'dart:async';
// Stream dengan async* dan yield
Stream<int> hitungMundur(int n) async* {
  for (int i = n; i > 0; i--) {
    await Future.delayed(const Duration(seconds: 1));
    yield i;
  }
}
void main() async {
  // 1. await for
  print('--- await for ---');
  await for (var angka in hitungMundur(3)) {
    print('Hitung: $angka');
  }
  print('Selesai!');

  // 2. StreamController + listen
  print('--- StreamController ---');
  final controller = StreamController<String>();

  controller.stream.listen(
    (pesan) => print('Terima: $pesan'),
    onError: (e) => print('Error: $e'),
    onDone: () => print('Stream ditutup'),
  );

  controller.add('Pesan 1');
  controller.add('Pesan 2');
  controller.addError('Contoh error');
  controller.add('Pesan 3');
  await controller.close();

  // 3. Transformasi stream
  print('--- map & where ---');
  var genap = Stream.fromIterable([1, 2, 3, 4, 5, 6])
      .where((e) => e % 2 == 0)
      .map((e) => e * 10);
  await for (var x in genap) {
    print(x);
  }
}
```

Jalankan dan amati hasilnya

```dart
  print('Selesai!');

  // 2. StreamController + listen
  print('--- StreamController ---');
  final controller = StreamController<String>();

  controller.stream.listen(
    (pesan) => print('Terima: $pesan'),
    onError: (e) => print('Error: $e'),
    onDone: () => print('Stream ditutup'),
  );

  controller.add('Pesan 1');
  controller.add('Pesan 2');
  controller.addError('Contoh error');
  controller.add('Pesan 3');
  await controller.close();

  // 3. Transformasi stream
  print('--- map & where ---');
  var genap = Stream.fromIterable([1, 2, 3, 4, 5, 6])
      .where((e) => e % 2 == 0)
      .map((e) => e * 10);
  await for (var x in genap) {
    print(x);
  }
}
```

## Post-Test

1. Kata kunci apa yang digunakan agar suatu class mewarisi class lain?
2. Jelaskan perbedaan Future dan Stream.
3. Apa akibatnya jika lupa menulis await sebelum memanggil fungsi yang mengembalikan Future?

## Latihan/Tugas

### Tugas Mandiri

Buat program konsol **"Sistem Perpustakaan Sederhana"** dengan ketentuan:

1. Class Buku (judul, penulis, status dipinjam) dengan enkapsulasi.
2. Abstract class Anggota dan turunan Mahasiswa serta Dosen dengan batas pinjam berbeda (polymorphism).
3. Abstract class/interface BukuRepository beserta implementasinya yang memakai Future dan jeda simulasi.
4. Fitur pinjam dan kembali buku dengan penanganan error (buku sedang dipinjam, buku tidak ditemukan).
5. Stream yang menampilkan progres impor 5 data buku awal.
6. Menu interaktif dengan stdin.
