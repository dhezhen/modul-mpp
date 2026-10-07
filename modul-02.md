# Modul 2: Dasar Bahasa Dart

## Maksud dan Tujuan

1. Menjelaskan fungsi Dart dalam pengembangan aplikasi Flutter serta membuat program Dart sederhana.
2. Menggunakan variabel, konstanta, tipe data, dan null safety dalam program Dart.
3. Menggunakan operator dan percabangan untuk mengatur proses dalam program.
4. Menggunakan perulangan dan fungsi untuk membuat program yang lebih terstruktur.
5. Menggunakan List, Set, dan Map serta menerapkannya dalam pembuatan program konsol sederhana.

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

### 1. Mengenal Dart

Dart adalah bahasa pemrograman yang dikembangkan oleh Google dan digunakan dalam pengembangan aplikasi Flutter. Dengan Dart dan Flutter, satu kode program dapat digunakan untuk membuat aplikasi di berbagai platform, seperti Android, iOS, Web, Windows, Linux, dan macOS.

Sebelum mempelajari Flutter, mahasiswa perlu memahami dasar-dasar bahasa Dart.

### 2. Program Dart Sederhana

Program Dart sederhana dapat dibuat seperti berikut:

```dart
void main() {
  print('Hello Dart!');
}
```

## Pre-Test

1. Perbedaan var, final, const?
1. Apa itu Null Safety?
1. Perbedaan Future & Stream?

## Praktikum

Subbab ini berisi materi praktikum berupa langkah-langkah atau prosedur yang perlu dilakukan oleh praktikan.

### Persiapan Proyek

1. Buka Android Studio → **New Flutter Project** → pilih *Flutter* → beri nama praktikum\_dart → Finish.
2. Klik kanan folder proyek → **New → Directory** → beri nama praktikum.
3. Setiap praktikum dibuat sebagai file baru di folder praktikum/ (klik kanan → **New → Dart File**), misalnya p1\_hello.dart.
4. Menjalankan: klik kanan pada file → **Run 'p1\_hello.dart'** (hasil tampil di panel *Run*).

Alternatif: *File → New → Project → Dart* (Console Application), jika Dart SDK sudah diatur di *Settings → Languages & Frameworks → Dart*.

### Praktikum 1 — Fungsi Dart dan Program Sederhana

**File:** p1\_hello.dart

```dart
import 'dart:io';
void main() {
  print('Halo, Dart!');

  stdout.write('Masukkan nama Anda: ');
  String? nama = stdin.readLineSync();

  print('Selamat datang, $nama');
}
```

**Latihan:**

1. Tambahkan input NIM dan program studi, lalu tampilkan ketiganya.
2. Ubah print menggunakan interpolasi ${nama.toUpperCase()}.

### Praktikum 2 - Variabel, Konstanta, Tipe Data, dan Null Safety

Buat sebuah file bernama  di projek yang sama dan ketikan variabel.dart

```dart
void main() {
  // Variabel dan tipe data
  String nama = 'Budi';
  int umur = 20;
  double ipk = 3.75;
  bool aktif = true;
  var kota = 'Kuningan'; // tipe disimpulkan otomatis (String)

  // Konstanta
  final DateTime waktu = DateTime.now(); // ditentukan saat runtime
  const double pi = 3.14159;             // ditentukan saat kompilasi

  print('$nama, $umur tahun, IPK $ipk, aktif: $aktif, kota: $kota');
  print('Waktu: $waktu | Pi: $pi');

  // Null safety
  String? alamat;               // boleh null
  print(alamat);                // null
  print(alamat?.length);        // null (aman)
  print(alamat ?? 'Belum diisi'); // nilai default

  alamat = 'Jl. Tol Cipali';
  print(alamat.length);         // aman setelah diisi

  // late: diisi kemudian, tidak boleh null saat dipakai
  late String hobi;
  hobi = 'Membaca';
  print(hobi);
}
```

### Praktikum 3 -  Operator dan Percabangan

**File:** p3 \_percabangan.dart

```dart
import 'dart:io';
void main() {
  // Operator
  int a = 10, b = 3;
  print('a + b = ${a + b}');
  print('a / b = ${a / b}');   // hasil double
  print('a ~/ b = ${a ~/ b}'); // pembagian bulat
  print('a % b = ${a % b}');
  print('a > b && b > 0 = ${a > b && b > 0}');
  // if - else if - else
  stdout.write('Masukkan nilai (0-100): ');
  int nilai = int.parse(stdin.readLineSync()!);

  String grade;
  if (nilai >= 85) {
    grade = 'A';
  } else if (nilai >= 70) {
    grade = 'B';
  } else if (nilai >= 55) {
    grade = 'C';
  } else {
    grade = 'D';
  }
  print('Grade: $grade');
  // Operator ternary
  print(nilai >= 60 ? 'Lulus' : 'Tidak lulus');
  // switch
  switch (grade) {
    case 'A':
      print('Sangat baik');
      break;
    case 'B':
      print('Baik');
      break;
    default:
      print('Perlu ditingkatkan');
  }
}
```

### Praktikum 4 — Perulangan dan Fungsi

**Tujuan:** membuat program terstruktur dengan perulangan dan fungsi.

**File:** p4\_loop\_fungsi.dart

```dart
// Fungsi dengan parameter dan return
int kuadrat(int x) {
  return x * x;
}

// Arrow function
int tambah(int a, int b) => a + b;

// Named parameter (sering dipakai pada widget Flutter)
void sapa({required String nama, String sapaan = 'Halo'}) {
  print('$sapaan, $nama!');
}

// Fungsi dengan perulangan
int faktorial(int n) {
  int hasil = 1;
  for (int i = 2; i <= n; i++) {
    hasil *= i;
  }
  return hasil;
}

void main() {
  // for
  for (int i = 1; i <= 5; i++) {
    print('Kuadrat $i = ${kuadrat(i)}');
  }

  // while
  int n = 3;
  while (n > 0) {
    print('Hitung mundur: $n');
    n--;
  }

  // do-while (minimal dijalankan sekali)
  int x = 0;
  do {
    print('x = $x');
    x++;
  } while (x < 2);

  // for-in
  for (var buah in ['Apel', 'Jeruk', 'Mangga']) {
    print(buah);
  }

  print('3 + 4 = ${tambah(3, 4)}');
  sapa(nama: 'Dede');
  sapa(nama: 'Siti', sapaan: 'Selamat pagi');
  print('5! = ${faktorial(5)}');
}
```

```dart
  do {
    print('x = $x');
    x++;
  } while (x < 2);

  // for-in
  for (var buah in ['Apel', 'Jeruk', 'Mangga']) {
    print(buah);
  }

  print('3 + 4 = ${tambah(3, 4)}');
  sapa(nama: 'Dede');
  sapa(nama: 'Siti', sapaan: 'Selamat pagi');
  print('5! = ${faktorial(5)}');
}
```

### Praktikum 5 -  List, Set, Map dan Program Konsol

### Dasar koleksi

- **File:** p5a\_koleksi.dart

```dart
void main() {
  // List: berurutan, boleh duplikat
  List<String> mk = ['AI', 'Basis Data', 'Flutter'];
  mk.add('Jaringan');
  mk.remove('Basis Data');
  print(mk);
  print('Jumlah: ${mk.length}, pertama: ${mk[0]}');

  // Set: unik, tanpa duplikat
  Set<int> angka = {1, 2, 3, 3, 2};
  angka.add(4);
  print(angka); // {1, 2, 3, 4}
  print(angka.contains(2));

  // Map: pasangan key-value
  Map<String, int> nilai = {'Budi': 80, 'Siti': 90};
  nilai['Andi'] = 75;
  nilai.remove('Budi');
  print(nilai);
  nilai.forEach((nama, n) => print('$nama -> $n'));

  // Operasi umum
  var daftar = [5, 3, 8, 1];
  daftar.sort();
  print(daftar);
  print(daftar.where((e) => e > 3).toList());
  print(daftar.map((e) => e * 2).toList());
}
```

```dart
  // Operasi umum
  var daftar = [5, 3, 8, 1];
  daftar.sort();
  print(daftar);
  print(daftar.where((e) => e > 3).toList());
  print(daftar.map((e) => e * 2).toList());
}
```

## Post-Test

1. Buatlah program Dart sederhana yang menyimpan nama dan nilai mahasiswa, kemudian menampilkan keduanya ke terminal.
2. Buatlah program Dart yang menggunakan percabangan if-else untuk menentukan apakah seorang mahasiswa dinyatakan lulus atau tidak lulus berdasarkan nilai minimal 70.
3. Buatlah program Dart yang menggunakan perulangan untuk menampilkan angka 1 sampai 5 ke terminal.

## Latihan/Tugas

Buat program penentu kategori BMI (kurus/normal/gemuk) dari input berat dan tinggi badan.
