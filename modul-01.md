# Modul 1: Pengantar Multiplatform & Setup Android Studio

## Maksud dan Tujuan

1. Mahasiswa mampu menjelaskan konsep single codebase dan keunggulan Flutter sebagai framework multiplatform.
2. Mahasiswa mampu membedakan rendering engine Skia dan Impeller pada Flutter.
3. Mahasiswa mampu melakukan instalasi Android Studio dan konfigurasi Flutter SDK.
4. Mahasiswa mampu membuat proyek Flutter pertama menggunakan Android Studio.
5. Mahasiswa mampu menjalankan aplikasi Flutter di emulator Android, Chrome (Web), dan Desktop.

## Alat, Bahan, dan Perangkat

1. Komputer/Laptop: Intel Core i5 / AMD Ryzen 5, RAM 8GB (disarankan 16GB), SSD 20GB free.
2. Android Studio (versi terbaru, minimal Hedgehog 2023.1.1).
3. Flutter SDK (minimal 3.x) & Dart SDK.
4. JDK 17 (bundled dengan Android Studio).
5. Git dan akun GitHub.
6. Google Chrome / Microsoft Edge.
7. Kabel data USB & Perangkat Mobile Fisik (opsional).

## Dasar Teori

### 1. Konsep Single Codebase

Single codebase merupakan pendekatan pengembangan aplikasi dengan menggunakan satu basis kode yang dapat dikompilasi dan dijalankan pada berbagai platform, seperti mobile, web, dan desktop. Pendekatan ini memberikan beberapa keuntungan, antara lain menghemat waktu dan biaya pengembangan, menjaga konsistensi tampilan serta pengalaman pengguna (UI/UX) di berbagai platform, mempermudah proses pemeliharaan dan pengembangan aplikasi, serta memungkinkan aplikasi dirilis ke pengguna dalam waktu yang lebih cepat (time-to-market).

### 2. Rendering Engine: Skia vs Impeller

| Aspek | Skia | Impeller |
|---|---|---|
| Platform | Semua platform | iOS (default), Android (eksperimental) |
| Pendekatan | Immediate mode | Retained mode + pre-compiled shaders |
| Shader Compilation | Runtime | AOT |
| Status | Default di sebagian besar platform | Default di iOS sejak Flutter 3.10 |

### 3. Arsitektur Flutter

Arsitektur Flutter terdiri dari tiga bagian utama, yaitu Framework, Engine, dan Embedder. Framework yang menggunakan bahasa pemrograman Dart menyediakan berbagai komponen untuk membangun aplikasi, seperti widget, proses rendering tampilan, animasi, dan pengelolaan gesture. Di bawahnya terdapat Engine yang dibangun menggunakan C/C++ dan berfungsi menjalankan proses inti Flutter, termasuk rendering menggunakan Skia/Impeller, Dart VM, serta komunikasi dengan platform melalui *platform channels*. Sementara itu, Embedder berperan menghubungkan Flutter Engine dengan sistem operasi (*OS*) yang menjadi target aplikasi, seperti Android, iOS, Windows, macOS, atau Linux.

### 4. Mengapa Menggunakan Android Studio?

Android Studio menjadi salah satu pilihan yang sesuai untuk pengembangan aplikasi Flutter karena menyediakan berbagai fitur yang mendukung proses pemrograman. Android Studio dapat menggunakan Flutter Plugin dan Dart Plugin untuk membantu penulisan kode, analisis program, *debugging*, serta pengembangan aplikasi. Selain itu, Android Studio telah terintegrasi dengan Android SDK Manager untuk mengelola kebutuhan SDK dan AVD Manager untuk membuat serta menjalankan emulator Android. Pengembang juga dapat memanfaatkan fitur seperti Logcat, Layout Inspector, dan Performance Profiler untuk memantau serta menganalisis aplikasi. Ditambah dengan terminal terintegrasi, berbagai kebutuhan pengembangan Flutter dapat dilakukan langsung dalam satu lingkungan kerja.

## Pre-Test

1. Apa yang dimaksud dengan single codebase?
2. Jelaskan perbedaan Skia dan Impeller!
3. Mengapa Android Studio direkomendasikan untuk Flutter?

## Praktikum

### Praktikum 1: Instalasi Android Studio

### Praktikum 2 – Instalasi Android Studio

1. Download IDE Android Studio terbaru (Android Studio Quail 4) melalui laman resminya di  https://developer.android.com/studio

![](.gitbook/assets/modul01-01.png)

![](.gitbook/assets/modul01-02.png)

2. Setelah itu doubel klik file instalasi Android studio, ikuti instalasinya  hinga selesai
3. Klik next pada gambar kiri untuk melanjutkan instalasi. Pada gambar sebelah kana ceklis *Android Virtual Device* kemudian klik next

![](.gitbook/assets/modul01-03.png)

![](.gitbook/assets/modul01-04.png)

4. Pada gambar sebelah kiri tentukan file instalasi kemudian klik next, selanjutnya klik tombol install untuk melakukan instalasi.

![](.gitbook/assets/modul01-05.png)

![](.gitbook/assets/modul01-06.png)

5. Jika proses instalasi telah selesai klik next, dan klik finish. Instalasi selesai dan Anda bisa langsung membuka IDE Android Studio

### Praktikum 2 – Instalasi Flutter SDK

1. Unduh dari https://docs.flutter.dev/get-started/install
1. Ekstrak ke C:\src\flutter (Windows) atau ~/development/flutter (macOS/Linux).
1. Tambahkan flutter\bin ke PATH di enviroment variabel .

![](.gitbook/assets/modul01-07.png)

1. Verifikasi: di CMD

```bash
flutter --version
```

### Praktikum 3 – Instalasi Plugin Flutter & Dart

1. Buka Android Studio → Plugins.
1. Cari 'Flutter' → Install (Dart otomatis terinstall).
1. Restart Android Studio.

### Praktikum 4 – Konfigurasi Flutter SDK

2. File → Settings → Languages & Frameworks → Flutter.
1. Set Flutter SDK path ke folder instalasi.
1. Jalankan di Terminal:

```bash
flutter doctor
flutter doctor --android-licenses
```

### Praktikum 5 – Membuat Proyek Flutter Pertama

1. File → New → New Flutter Project → Flutter.
1. Isi:
- Project name: hello\_multiplatform
- Organization: ti.fkom.uniku
- Android language: Kotlin
- Platforms: Android, Web, Windows
1. Klik Create → tunggu pub get & Gradle sync.

### Praktikum 7 – Menjalankan di Berbagai Platform

3. Android: Pilih emulator di Device Selector → ▶ Run (Shift+F10).
1. Web: Pilih Chrome (web) → ▶ Run.
1. Windows: Pilih Windows (desktop) → ▶ Run.

### Praktikum 8 – Modifikasi Kode & Hot Reload

Ubah lib/main.dart:

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MyApp());

class MyApp extends StatelessWidget {
  const MyApp({super.key});
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Hello Multiplatform',
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.deepPurple),
        useMaterial3: true,
      ),
      home: const MyHomePage(title: 'Hello Multiplatform'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  const MyHomePage({super.key, required this.title});
  final String title;
  @override
  State<MyHomePage> createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;
  void _incrementCounter() => setState(() => _counter++);

  @override
  Widget build(BuildContext context) {
    final platform = Theme.of(context).platform.name;
    return Scaffold(
      appBar: AppBar(
        backgroundColor: Theme.of(context).colorScheme.inversePrimary,
        title: Text(widget.title),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            const Text('Anda menekan tombol sebanyak:'),
            Text('$_counter',
                style: Theme.of(context).textTheme.headlineMedium),
            const SizedBox(height: 20),
            const Text('Aplikasi ini berjalan di:'),
            Text(platform,
                style: const TextStyle(fontWeight: FontWeight.bold)),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        child: const Icon(Icons.add),
      ),
    );
  }
}
```

Simpan (Ctrl+S) → Hot Reload otomatis.

## Post-Test

1. Jelaskan Langkah setup Flutter SDK di Android Studio!
2. Apa fungsi Flutter Doctor?
3. Perbedaan Hot Reload dan Hot Restart?

## Latihan/Tugas

Buat aplikasi Flutter yang menampilkan Nama, NIM, Prodi, dan Platform yang digunakan. Jalankan di minimal 2 platform. Screenshot & kumpulkan via LMS.
