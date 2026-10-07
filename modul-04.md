# Modul 4: Widget Dasar dan Konsep Widget Tree

## Maksud dan Tujuan

1. Menjelaskan konsep widget dan widget tree pada Flutter.
2. Membedakan serta membuat StatelessWidget dan StatefulWidget (dengan setState).
3. Menggunakan widget Text, Icon, Image, dan Container.
4. Menyusun tata letak dengan Row, Column, dan Stack.
5. Membaca widget tree menggunakan Flutter Inspector di Android Studio.
6. Membuat kartu profil sederhana yang menggabungkan seluruh widget dasar tersebut.

## Alat, Bahan, dan Perangkat

Alat, bahan, perangkat lunak, perangkat keras yang digunakan adalah:

1. Komputer/Laptop: Intel Core i5 / AMD Ryzen 5, RAM 8GB (disarankan 16GB), SSD 20GB free.
2. Android Studio (versi terbaru, minimal Hedgehog 2023.1.1).
3. Flutter SDK (minimal 3.x) & Dart SDK.
4. JDK 17 (bundled dengan Android Studio).
5. Git dan akun GitHub.
6. Google Chrome / Microsoft Edge.
7. Koneksi internet
8. Kabel data USB & Perangkat Mobile Fisik (opsional).

## Dasar Teori

### 1. Widget dan Widget Tree

Di Flutter, hampir semuanya adalah widget: teks, gambar, tombol, tata letak, bahkan padding. Widget disusun bersarang (induk → anak) membentuk widget tree. Flutter menggambar layar dengan menelusuri pohon ini dari akar (`MaterialApp`) sampai ke daun (mis. `Text`).

Contoh kode dan pohonnya (*tree*):

| Scaffold(<br>body: Center(<br>child: Column(children: [Text('A'), Icon(Icons.star)]),<br>),<br>) | Scaffold<br>└─ Center<br>└─ Column<br>├─ Text<br>└─ Icon |
|---|---|

1. **StatelessWidget dan StatefulWidget**

Berikut perbedaan **StatelessWidget dan StatefulWidget**

|  | **StatelessWidget** | **StatefulWidget** |
|---|---|---|
| Sifat | Tampilan tetap, hanya bergantung pada data yang diberikan | Tampilan dapat berubah saat data (state) berubah |
| Struktur | 1 class, method `build()` | 2 class: widget + `State`, method `build()` ada di `State` |
| Memperbarui tampilan | Tidak ada | `setState(() { ... })` |
| Contoh | Judul, ikon, label | Penghitung, tombol suka/ikuti, formulir |

- build(*BuildContext context)* dipanggil Flutter untuk menyusun tampilan widget tersebut.
- setState() memberi tahu Flutter bahwa state berubah sehingga build() dipanggil ulang. Mengubah variabel **tanpa** setState() tidak memperbarui layar.
- const pada konstruktor widget membuat Flutter dapat memakai ulang objek yang sama sehingga lebih efisien.

### 3. Widget Dasar yang Dipelajari

| **Widget** | **Fungsi** | **Properti penting** |
|---|---|---|
| Text | Menampilkan teks | style (TextStyle), textAlign, maxLines, overflow |
| Icon | Menampilkan ikon bawaan (Icons.\*) | size, color |
| Image | Menampilkan gambar | Image.network, Image.asset, width, height, fit |
| Container | Kotak serbaguna (ukuran, jarak, dekorasi) | width, height, padding, margin, alignment, decoration |
| Row | Menyusun anak **horizontal** | mainAxisAlignment, crossAxisAlignment |
| Column | Menyusun anak **vertikal** | mainAxisAlignment, crossAxisAlignment |
| Stack | Menumpuk anak (anak terakhir paling atas) | alignment, Positioned, cl |

### 4. Sumbu pada Row dan Column

|  | **Main axis** | **Cross axis** |
|---|---|---|
| Row | Horizontal (kiri–kanan) | Vertikal |
| Column | Vertikal (atas–bawah) | Horizontal |

Widget Expanded di dalam Row/Column membagi sisa ruang sesuai flex.

### 5. Hot Reload dan Hot Restart

- **Hot reload** menerapkan perubahan kode ke aplikasi yang berjalan **dengan mempertahankan state**.
- **Hot restart** memuat ulang aplikasi dari awal dan **mengatur ulang state**.

## Pre-Test

1. Apa itu Flutter dan bahasa pemrograman apa yang digunakannya?
2. Menurut Anda, apa yang harus terjadi pada tampilan aplikasi ketika data yang ditampilkannya berubah?
3. Apa perbedaan hot reload dan Hot Restart?

## Praktikum

Seluruh praktikum dikerjakan di Android Studio pada proyek Flutter dan dijalankan di emulator atau perangkat Android. Tiap praktikum berupa satu file di folder lib/ yang memiliki fungsi main() sendiri, sehingga dapat dijalankan terpisah.

### Praktikum 1

Membuat Projek Flutter ( cukup sekali pada modul ke 4 ini)

Berikut Langkah-langkahnya:

1. File → New → New Flutter Project.
2. Pilih Flutter pada panel kiri, pastikan Flutter SDK path terisi → Next.
3. Project name: modul4\_widget; atur Project location; pada Platforms cukup centang Android → Create.
4. Tunggu proses indexing dan unduhan selesai (indikator di kanan bawah).
5. Kemudian hidupkan emulator atau sambungkan perangkat smarphone Anda ke Android studio baik secara wireless (*wireless debugging)* maupun memakai kabel data.

### Praktikum 2 - StatelessWidget dan StatefulWidget

Langkah pelaksanaan di Android Studio:

1. Klik kanan folder lib → New → Dart File → ketik p1\_stateless\_stateful → Enter.
2. Salin kode di bawah ke editor.
3. Pastikan emulator menyala, lalu klik ▶ di samping void main() → Run 'p1\_stateless\_stateful.dart'.
4. Tekan tombol Tambah beberapa kali dan amati angka serta log Counter di-build ulang di panel Run.
5. Ubah teks 'Anda menekan tombol sebanyak:', lakukan hot reload, lalu amati bahwa angka tidak kembali ke 0.
6. Ubah int \_jumlah = 0; menjadi 10, lakukan hot reload (angka tidak berubah), lalu hot restart (angka mulai dari 10).
7. Buka Flutter Inspector dan temukan widget Salam dan Counter pada pohon.

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MyApp());

// StatelessWidget: tampilan tetap, hanya bergantung pada data dari luar
class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: Scaffold(
        appBar: AppBar(title: const Text('Stateless vs Stateful')),
        body: const Center(
          child: Column(
            mainAxisSize: MainAxisSize.min,
            children: [
              Salam(nama: 'Budi'),
              SizedBox(height: 24),
              Counter(),
            ],
          ),
        ),
      ),
    );
  }
}

// StatelessWidget dengan parameter: dapat dipakai ulang dengan data berbeda
class Salam extends StatelessWidget {
  final String nama;
  const Salam({super.key, required this.nama});

  @override
  Widget build(BuildContext context) {
    return Text('Halo, $nama!', style: const TextStyle(fontSize: 24));
  }
}

// StatefulWidget: terdiri dari widget + State
class Counter extends StatefulWidget {
  const Counter({super.key});

  @override
  State<Counter> createState() => _CounterState();
}


class _CounterState extends State<Counter> {
  int _jumlah = 0; // state

  void _tambah() {
    setState(() {
      _jumlah++; // tanpa setState, layar tidak diperbarui
    });
  }

  @override
  Widget build(BuildContext context) {
    debugPrint('Counter di-build ulang, jumlah = $_jumlah');
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        const Text('Anda menekan tombol sebanyak:'),
        Text('$_jumlah', style: const TextStyle(fontSize: 48)),
        ElevatedButton(onPressed: _tambah, child: const Text('Tambah')),
      ],
    );
  }
}
```

File: **Praktikum\_stateless\_stateful.dart**

Jalankan file tersebut di emulator atau di browser chrome dan amati hasilnya

```dart
class _CounterState extends State<Counter> {
  int _jumlah = 0; // state

  void _tambah() {
    setState(() {
      _jumlah++; // tanpa setState, layar tidak diperbarui
    });
  }

  @override
  Widget build(BuildContext context) {
    debugPrint('Counter di-build ulang, jumlah = $_jumlah');
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        const Text('Anda menekan tombol sebanyak:'),
        Text('$_jumlah', style: const TextStyle(fontSize: 48)),
        ElevatedButton(onPressed: _tambah, child: const Text('Tambah')),
      ],
    );
  }
}
```

![](.gitbook/assets/modul04-01.png)

Berikut hasil dari program tersebut:

Praktikum 2 — Text dan Icon

Tujuan: menata teks dengan TextStyle dan menampilkan ikon.

Langkah pelaksanaan di Android Studio:

1. Buat file Praktikum\_text\_icon.dart di folder lib (New → Dart File).
2. Salin kode di bawah ke editor.
3. Klik ▶ di samping void main() → Run 'p2\_text\_icon.dart'.
4. Ubah fontSize, color, dan fontWeight pada teks bergaya, lalu amati hasilnya dengan hot reload.
5. Hapus maxLines: 2 dan amati perubahan pada teks panjang; kembalikan lagi.

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MyApp());
class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: Scaffold(
        appBar: AppBar(title: const Text('Text dan Icon')),
        body: Padding(
          padding: const EdgeInsets.all(16),
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              const Text('Teks biasa'),
              const Text(
                'Teks dengan gaya',
                style: TextStyle(
                  fontSize: 24,
                  fontWeight: FontWeight.bold,
                  color: Colors.indigo,
                  letterSpacing: 1.5,
                ),
              ),
              const Text(
                'Teks miring bergaris bawah',
                style: TextStyle(
                  fontStyle: FontStyle.italic,
                  decoration: TextDecoration.underline,
                ),
              ),
              const SizedBox(height: 12),
              const Text(
    'Teks panjang ini dibatasi dua baris saja.Sisanya dipotong'
    'dan diganti tanda elipsis agar tampilan tetap rapi pada '
                'layar yang kecil.',
                maxLines: 2,
                overflow: TextOverflow.ellipsis,
              ),





              const SizedBox(height: 12),
              // Beberapa gaya dalam satu paragraf
              const Text.rich(
                TextSpan(
                  text: 'Halo, ',
                  children: [
                    TextSpan(
                      text: 'Flutter',
                      style: TextStyle(
                        fontWeight: FontWeight.bold,
                        color: Colors.blue,
                      ),
                    ),
                    TextSpan(text: '!'),
                  ],
                ),
              ),
              const Divider(height: 32),
              const Text('Icon:'),
              const SizedBox(height: 8),
              const Row(
                mainAxisAlignment: MainAxisAlignment.spaceAround,
                children: [
                  Icon(Icons.favorite, color: Colors.red, size: 40),
                  Icon(Icons.star, color: Colors.amber, size: 40),
                  Icon(Icons.email, color: Colors.blue, size: 40),
                  Icon(Icons.phone, color: Colors.green, size: 40),
                ],
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

6. Ubah Icons.favorite menjadi ikon lain (ketik Icons. lalu pilih dari daftar saran).

![](.gitbook/assets/modul04-02.png)

Jalankan file tersebut di emulator atau web browser, jika berhasil berikut tampilannya:

```dart
              const SizedBox(height: 12),
              // Beberapa gaya dalam satu paragraf
              const Text.rich(
                TextSpan(
                  text: 'Halo, ',
                  children: [
                    TextSpan(
                      text: 'Flutter',
                      style: TextStyle(
                        fontWeight: FontWeight.bold,
                        color: Colors.blue,
                      ),
                    ),
                    TextSpan(text: '!'),
                  ],
                ),
              ),
              const Divider(height: 32),
              const Text('Icon:'),
              const SizedBox(height: 8),
              const Row(
                mainAxisAlignment: MainAxisAlignment.spaceAround,
                children: [
                Icon(Icons.favorite, color: Colors.red, size: 40),
                 Icon(Icons.star, color: Colors.amber, size: 40),
                 Icon(Icons.email, color: Colors.blue, size: 40),
                 Icon(Icons.phone, color: Colors.green, size: 40),
                ],
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

### Praktikum 3 -  Image dan Container

**Tujuan:** menampilkan gambar dari jaringan dan dari aset, serta membuat kotak berdekorasi dengan Container.

**Langkah pelaksanaan di Android Studio:**

1. **Siapkan aset gambar:** klik kanan folder proyek modul4\_widget → **New → Directory** → ketik assets/images. Salin file foto Anda ke folder tersebut dan beri nama foto.jpg (drag and drop ke panel Project, atau klik kanan → *Paste*).
2. **Daftarkan aset:** buka pubspec.yaml, pada bagian flutter: tambahkan seperti berikut (perhatikan **2 spasi** per tingkat, jangan memakai Tab):
3. flutter:
4. uses-material-design: true
5. assets:
6. - assets/images/
7. Klik tulisan **Pub get** pada pita di atas editor (atau jalankan flutter pub get di Terminal).
8. Buat file p3\_image\_container.dart di folder lib (**New → Dart File**) dan salin kode di bawah.
9. **Hentikan** aplikasi sebelumnya (■), lalu klik **▶** di samping void main() → **Run 'p3\_image\_container.dart'**. Perubahan aset membutuhkan *run* ulang penuh, bukan hot reload.
10. Ubah fit: BoxFit.cover menjadi BoxFit.contain dan BoxFit.fill, lalu amati perbedaannya.
11. Pada Container berdekorasi, tambahkan color: Colors.red, di luar decoration. Amati error pada editor/panel Run, lalu hapus kembali.

**File:** praktikum\_image\_container.dart

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MyApp());

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: Scaffold(
        appBar: AppBar(title: const Text('Image dan Container')),
        body: SingleChildScrollView(
          padding: const EdgeInsets.all(16),
          child: Column(
            children: [
              const Text('1. Image.network'),
              const SizedBox(height: 8),
              Image.network(
                'https://picsum.photos/id/237/200/200',
                width: 150,
                height: 150,
                fit: BoxFit.cover,
                errorBuilder: (context, error, stackTrace) =>
                    const Icon(Icons.broken_image, size: 64),
              ),
              const SizedBox(height: 16),
              const Text('2. Image.asset dengan ClipOval'),
              const SizedBox(height: 8),
              ClipOval(
                child: Image.asset(
                  'assets/images/foto.jpg',
                  width: 120,
                  height: 120,
                  fit: BoxFit.cover,
                  errorBuilder: (context, error, stackTrace) =>
                      const Icon(Icons.person, size: 64),
                ),
              ),
              const SizedBox(height: 16),
              const Text('3. Container dengan dekorasi'),
              Container(
                width: 220,
                height: 100,
                margin: const EdgeInsets.symmetric(vertical: 8),
                padding: const EdgeInsets.all(12),
                alignment: Alignment.center,
                decoration: BoxDecoration(
                  color: Colors.blue.shade50,
                  borderRadius: BorderRadius.circular(16),
                  border: Border.all(color: Colors.blue, width: 2),
                  boxShadow: const [
                    BoxShadow(
                      color: Colors.black26,
                      blurRadius: 6,
                      offset: Offset(2, 4),
                    ),
                  ],
                ),
                child: const Text('Container', style: TextStyle(fontSize: 20)),
              ),
              const Text('4. Container sederhana (hanya color)'),
              const SizedBox(height: 8),
              Container(
                width: 220,
                height: 50,
                color: Colors.amber,
                child: const Center(child: Text('Hanya color')),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

Jalankan file tersebut dan amati hasilnya, jika berhasil berikut tampilannya:

```dart
                height: 150,
                fit: BoxFit.cover,
                errorBuilder: (context, error, stackTrace) =>
                    const Icon(Icons.broken_image, size: 64),
              ),
              const SizedBox(height: 16),
              const Text('2. Image.asset dengan ClipOval'),
              const SizedBox(height: 8),
              ClipOval(
                child: Image.asset(
                  'assets/images/foto.jpg',
                  width: 120,
                  height: 120,
                  fit: BoxFit.cover,
                  errorBuilder: (context, error, stackTrace) =>
                      const Icon(Icons.person, size: 64),
                ),
              ),
              const SizedBox(height: 16),
              const Text('3. Container dengan dekorasi'),
              Container(
                width: 220,
                height: 100,
                margin: const EdgeInsets.symmetric(vertical: 8),
                padding: const EdgeInsets.all(12),
                alignment: Alignment.center,
                decoration: BoxDecoration(
                  color: Colors.blue.shade50,
                  borderRadius: BorderRadius.circular(16),
                  border: Border.all(color: Colors.blue, width: 2),
                  boxShadow: const [
                    BoxShadow(
                      color: Colors.black26,
                      blurRadius: 6,
                      offset: Offset(2, 4),
                    ),
                  ],
                ),
     child: const Text('Container', style: TextStyle(fontSize: 20)),
              ),
              const Text('4. Container sederhana (hanya color)'),
              const SizedBox(height: 8),
              Container(
                width: 220,
                height: 50,
                color: Colors.amber,
                child: const Center(child: Text('Hanya color')),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

![](.gitbook/assets/modul04-03.png)

### Praktikum 4 - Row dan Column

**Tujuan:** menyusun widget secara horizontal dan vertikal serta mengatur perataannya.

**Langkah pelaksanaan di Android Studio:**

1. Buat file p4\_row\_column.dart di folder lib (**New → Dart File**).
2. Salin kode di bawah ke editor.
3. Klik **▶** di samping void main() → **Run 'p4\_row\_column.dart'**.
4. Pada bagian 1, ganti MainAxisAlignment.spaceBetween secara bergantian dengan start, center, end, spaceAround, spaceEvenly; lakukan **hot reload** setiap kali dan amati perbedaannya.
5. Pada bagian 2, ubah nilai flex (mis. 1 : 1 : 3) dan amati pembagian lebar.
6. Pada bagian 3, ubah crossAxisAlignment menjadi CrossAxisAlignment.center dan amati posisi teks.
7. Latihan tool: letakkan kursor pada Row di bagian 3, tekan Alt+Enter → **Wrap with Padding**, lalu amati perubahannya di **Flutter Inspector**.

**File:** p4\_row\_column.dart

```dart
import 'package:flutter/material.dart';
void main() => runApp(const MyApp());
class MyApp extends StatelessWidget {
  const MyApp({super.key});
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: Scaffold(
        appBar: AppBar(title: const Text('Row dan Column')),
        body: const Padding(
          padding: EdgeInsets.all(16),
          child: ContohLayout(),
        ),
      ),
    );
  }
}

// Widget kecil yang dipakai berulang
class Kotak extends StatelessWidget {
  final Color warna;
  final String label;
  const Kotak({super.key, required this.warna, required this.label});

  @override
  Widget build(BuildContext context) {
    return Container(
      width: 70,
      height: 70,
      color: warna,
      alignment: Alignment.center,
      child: Text(
        label,
        style: const TextStyle(color: Colors.white, fontSize: 20),
      ),
    );
  }
}

class ContohLayout extends StatelessWidget {
  const ContohLayout({super.key});

  @override
  Widget build(BuildContext context) {
    return const Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text('1. Row - mainAxisAlignment: spaceBetween'),
        SizedBox(height: 8),
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          children: [
            Kotak(warna: Colors.red, label: 'A'),
            Kotak(warna: Colors.green, label: 'B'),
            Kotak(warna: Colors.blue, label: 'C'),
          ],
        ),
        SizedBox(height: 24),
        Text('2. Row - Expanded (flex 1 : 2 : 1)'),
        SizedBox(height: 8),
        Row(
          children: [
            Expanded(
              flex: 1,
              child: ColoredBox(color: Colors.red, child: SizedBox(height: 50)),
            ),
            Expanded(
              flex: 2,
              child:
                  ColoredBox(color: Colors.green, child: SizedBox(height: 50)),
            ),
            Expanded(
              flex: 1,
              child:
                  ColoredBox(color: Colors.blue, child: SizedBox(height: 50)),
            ),
          ],
        ),
        SizedBox(height: 24),
        Text('3. Row berisi Icon dan Column'),
        SizedBox(height: 8),
        Row(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Icon(Icons.person, size: 48),
            SizedBox(width: 12),
            Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Text(
                  'Budi Santoso',
                  style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
                ),
                Text('Mahasiswa Teknik Informatika'),
              ],
            ),
          ],
        ),
      ],
    );
  }
}
```

```dart
    );
  }
}

// Widget kecil yang dipakai berulang
class Kotak extends StatelessWidget {
  final Color warna;
  final String label;
  const Kotak({super.key, required this.warna, required this.label});

  @override
  Widget build(BuildContext context) {
    return Container(
      width: 70,
      height: 70,
      color: warna,
      alignment: Alignment.center,
      child: Text(
        label,
        style: const TextStyle(color: Colors.white, fontSize: 20),
      ),
    );
  }
}

class ContohLayout extends StatelessWidget {
  const ContohLayout({super.key});

  @override
  Widget build(BuildContext context) {
    return const Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text('1. Row - mainAxisAlignment: spaceBetween'),
        SizedBox(height: 8),
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          children: [
            Kotak(warna: Colors.red, label: 'A'),
            Kotak(warna: Colors.green, label: 'B'),
            Kotak(warna: Colors.blue, label: 'C'),
          ],
        ),
        SizedBox(height: 24),
        Text('2. Row - Expanded (flex 1 : 2 : 1)'),
        SizedBox(height: 8),
        Row(
          children: [
            Expanded(
              flex: 1,
  child: ColoredBox(color: Colors.red, child: SizedBox(height: 50)),
            ),Expanded( flex: 2,
  child:ColoredBox(color: Colors.green, child: SizedBox(height: 50)),
            ),
            Expanded(
              flex: 1,
   child:ColoredBox(color: Colors.blue, child: SizedBox(height: 50)),
            ),
          ],
        ),


        SizedBox(height: 24),
        Text('3. Row berisi Icon dan Column'),
        SizedBox(height: 8),
        Row(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Icon(Icons.person, size: 48),
            SizedBox(width: 12),
            Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Text(
                  'Budi Santoso',
                  style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
                ),
                Text('Mahasiswa Teknik Informatika'),
              ],
            ),
          ],
        ),
      ],
    );
  }
}
```

![](.gitbook/assets/modul04-04.png)

Jalankan file tersebut dan amati hasilnya, jika berhasil berikut tampilannya:
**Praktikum 5 — Stack**

```dart
       SizedBox(height: 24),
        Text('3. Row berisi Icon dan Column'),
        SizedBox(height: 8),
        Row(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Icon(Icons.person, size: 48),
            SizedBox(width: 12),
            Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Text(
                  'Budi Santoso',
                  style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
                ),
                Text('Mahasiswa Teknik Informatika'),
              ],
            ),
          ],
        ),
      ],
    );
  }
}
```

**Tujuan:** menumpuk widget dan memposisikannya dengan Positioned.

**Langkah pelaksanaan di Android Studio:**

1. Buat file p5\_stack.dart di folder lib (**New → Dart File**).
2. Salin kode di bawah ke editor.
3. Klik **▶** di samping void main() → **Run 'p5\_stack.dart'**.
4. Ubah nilai left dan top pada Positioned avatar, lalu **hot reload** dan amati perpindahannya.
5. Hapus clipBehavior: Clip.none dan amati bagian avatar yang melewati batas banner (terpotong); kembalikan lagi.
6. Pada tumpukan tiga kotak, tukar urutan kotak merah dan biru di dalam children. Amati bahwa **widget terakhir berada paling atas**.
7. Buka **Flutter Inspector** dan temukan widget Stack beserta anak-anaknya.

**File:** p5\_stack.dart

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MyApp());

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: Scaffold(
        appBar: AppBar(title: const Text('Stack')),
        body: SingleChildScrollView(
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              // Banner + avatar yang menumpuk
              Stack(
                clipBehavior: Clip.none,
                children: [
                  Container(
                    height: 140,
                    width: double.infinity,
                    color: Colors.indigo,
                  ),
                  const Positioned(
                    top: 12,
                    right: 12,
                child: Icon(Icons.settings, color: Colors.white),
                  ),
                  const Positioned(
                    left: 24,
                    top: 90,
                    child: CircleAvatar(
                      radius: 48,
                      backgroundColor: Colors.white,
                      child: CircleAvatar(
                        radius: 42,
                        backgroundColor: Colors.orange,
         child: Icon(Icons.person, size: 48, color: Colors.white),
                      ),
                    ),
                  ),
                ],
              ),
              const SizedBox(height: 60),
              const Padding(
                padding: EdgeInsets.symmetric(horizontal: 24),
                child: Text(
                  'Budi Santoso',
                  style: TextStyle(fontSize: 22, fontWeight: FontWeight.bold),
                ),
              ),
              const Divider(height: 40),
              const Padding(
                padding: EdgeInsets.only(left: 24),
                child: Text('Urutan tumpukan (terakhir = paling atas)'),
              ),
              const SizedBox(height: 8),
              Center(
                child: SizedBox(
                  width: 140,
                  height: 140,
                  child: Stack(
                    alignment: Alignment.center,
                    children: [
                      Container(width: 140, height: 140, color: Colors.red),
                      Container(width: 90, height: 90, color: Colors.green),
                      Container(width: 40, height: 40, color: Colors.blue),
                    ],
                  ),
                ),
              ),
              const SizedBox(height: 24),
            ],
          ),
        ),
      ),
    );
  }
}
```

![](.gitbook/assets/modul04-05.png)

Jalankan dan amati hasilnya, berikut tampilan jika berhasil:

```dart
FontWeight.bold),
                ),
              ),
              const Divider(height: 40),
              const Padding(
                padding: EdgeInsets.only(left: 24),
                child: Text('Urutan tumpukan (terakhir = paling atas)'),
              ),
              const SizedBox(height: 8),
              Center(
                child: SizedBox(
                  width: 140,
                  height: 140,
                  child: Stack(
                    alignment: Alignment.center,
                    children: [
             Container(width: 140, height: 140, color: Colors.red),
             Container(width: 90, height: 90, color: Colors.green),
             Container(width: 40, height: 40, color: Colors.blue),
                    ],
                  ),
                ),
              ),
              const SizedBox(height: 24),
            ],
          ),
        ),
      ),
    );
  }
}
```

## Post-Test

1. Jelaskan perbedaan StatelessWidget dan StatefulWidget, beserta satu contoh penggunaan masing-masing?
2. Jelaskan perbedaan main axis dan cross axis pada Row dan Column.
3. Apa fungsi widget Expanded di dalam Row? Apa arti flex: 2?

## Latihan/Tugas

Buat aplikasi **"Kartu Nama Digital"** berisi data diri Anda sendiri dengan ketentuan:

1. Foto Anda dari **aset** (Image.asset) berbentuk lingkaran.
2. Menggunakan minimal satu **StatelessWidget buatan sendiri** yang dapat dipakai ulang dengan parameter.
3. Menggunakan minimal satu **StatefulWidget** (mis. tombol suka atau ganti tema) dengan setState.
4. Menggunakan Stack, Row, Column, Container berdekorasi, Icon, dan Text dengan beragam gaya.
5. Memuat nama, NIM, program studi, minimal tiga kontak, dan satu kutipan/motto.
6. Tampilan rapi tanpa *overflow* (garis kuning-hitam) pada emulator.
7. Kumpulkan pengerjaan di edlink, dan satukan dengan laporan praktikum
