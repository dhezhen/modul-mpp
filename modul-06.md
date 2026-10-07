# Modul 6: Navigasi, Routing, dan Form

## Maksud dan Tujuan

1. Membangun aplikasi multi-halaman dengan struktur navigasi yang terorganisasi.
2. Mengimplementasikan navigasi menggunakan Navigator.push, Navigator.pop, named routes, dan onGenerateRoute.
3. Menerapkan routing deklaratif menggunakan go\_router, termasuk parameter path dan query.
4. Membuat input teks menggunakan TextField dan TextEditingController.
5. Membangun form menggunakan Form, TextFormField, dan GlobalKey&lt;FormState&gt;.
6. Menerapkan validasi input seperti data wajib, format email, dan validasi kata sandi.
7. Mengirim dan menerima data antarhalaman melalui konstruktor, arguments, parameter path, dan extra.
8. Merancang alur navigasi dan form yang terstruktur, aman, dan mudah dipelihara.
9. Mengembangkan mini aplikasi pendaftaran dan login sebagai penerapan navigasi dan pengelolaan form.
10. Mempersiapkan dasar pengembangan untuk materi manajemen state dan REST API

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

1. **Konsep Route dan Navigasi**

Di Flutter, setiap "halaman" (layar) disebut **route**. Route pada dasarnya adalah widget yang menempati seluruh layar. Flutter mengelola route dalam bentuk **tumpukan (stack)**:

- **Push()**: menaruh halaman baru di atas tumpukan, halaman lama tetap berada di bawahnya.
- **Pop()**: membuang halaman teratas sehingga pengguna kembali ke halaman sebelumnya.

Ada tiga pendekatan yang dipelajari pada modul ini:

| **Pendekatan** | **Ciri** | **Cocok untuk** |
|---|---|---|
| ***Navigator.push (imperatif langsung)*** | Membuat route saat dipanggil | Aplikasi kecil, belajar konsep dasar |
| ***Named routes*** | Route diberi nama string, didaftarkan di MaterialApp | Aplikasi menengah |
| ***go\_router*** | Routing deklaratif berbasis URL, mendukung parameter dan deep link | Aplikasi nyata, termasuk web |

![](.gitbook/assets/modul06-01.png)

Berikut ilustrasinya

Contoh kode:

```dart
// Membuka halaman Detail
Navigator.push(
  context,
  MaterialPageRoute(
    builder: (context) => const HalamanDetail(),
  ),
);

//Untuk kembali:

Navigator.pop(context);
```

2. **go\_router**

go\_router digunakan untuk mengatur navigasi dengan pendekatan berbasis **path**, seperti /profil atau /produk/1. Pendekatan ini membantu mengelola navigasi pada aplikasi yang memiliki banyak halaman. Perintah go() digunakan untuk berpindah berdasarkan path, push() untuk menambahkan halaman baru, dan pop() untuk kembali ke halaman sebelumnya.

```dart
final router = GoRouter(
  routes: [
    GoRoute(
      path: '/',
      builder: (context, state) => const Beranda(),
    ),
    GoRoute(
      path: '/profil',
      builder: (context, state) => const Profil(),
    ),
  ],
);


//Navigasi ke halaman Profil:
context.go('/profil');

//Kembali ke halaman sebelumnya:
context.pop();
```

3. **TextField dan TextEditingController**

TextField digunakan untuk menerima **input teks dari pengguna**, misalnya nama, email, atau nomor telepon. Jika aplikasi perlu membaca isi input tersebut, digunakan TextEditingController. Isi dari TextField dapat diambil melalui properti .text.

Contoh kode:

```dart
final namaController = TextEditingController();

TextField(
  controller: namaController,
  decoration: const InputDecoration(
    labelText: 'Nama',
    border: OutlineInputBorder(),
  ),
);
//untuk membaca isi input:
String nama = namaController.text;
// Jika controller digunakan dalam StatefulWidget, controller sebaiknya dibersihkan menggunakan dispose() ketika widget tidak lagi digunakan
@override
void dispose() {
  namaController.dispose();
  super.dispose();
}.
```

4. **Form dan Validasi**

Form digunakan untuk mengelompokkan beberapa input dan melakukan **validasi data** sebelum data diproses. Input yang berada di dalam Form biasanya menggunakan TextFormField dan memiliki validator. Jika data tidak sesuai aturan, validator mengembalikan pesan kesalahan. Sebaliknya, return null menunjukkan bahwa data sudah valid.

**Contoh kode:**

```dart
class DetailPage extends StatelessWidget {
  final String nama;

  const DetailPage({
    super.key,
    required this.nama,
  });

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Text('Halo, $nama'),
      ),
    );
  }
}
```

Mengirim data:

```dart
Navigator.push(
  context,
  MaterialPageRoute(
    builder: (context) => const DetailPage(
      nama: 'Budi',
    ),
  ),
);

//hasil yang ditampilkan
Halo, Budi
```

## Pre-Test

1. Apa yang dimaksud dengan route pada Flutter?
2. Jelaskan perbedaan Navigator.push dan Navigator.pop.
3. Mengapa Navigator.push dapat di-await? Apa isi nilai yang dikembalikan?
4. Apa fungsi TextEditingController? Apa risikonya jika tidak di-dispose()?

## Praktikum

### Praktikum 1: Navigator Dasar (push, pop, kirim dan terima data)

**Tujuan:** berpindah antarhalaman, mengirim data ke halaman tujuan, dan menerima hasil dari halaman tujuan.

**Berikut langkah-langkahnya:**

1. **Langkah 1. Buat proyek baru di Android Studio.**
- Buat proyek Flutter baru mengikuti butir a. Membuat proyek Flutter baru pada Panduan Dasar, dengan Project name praktikum6\_navigator, Project type Application, dan platform Android dicentang. Tunggu hingga proses *indexing* dan *pub get* selesai, kemudian pastikan file lib/main.dart terbuka di editor.
2. **Langkah 2. Ganti isi lib/main.dart.**
- Pada panel Project buka lib &gt; main.dart (klik dua kali), kosongkan isinya (butir d pada Panduan Dasar), lalu tempel kode berikut.

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Praktikum Navigator',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(
        colorSchemeSeed: Colors.indigo,
        useMaterial3: true,
      ),
      home: const HalamanBeranda(),
    );
  }
}
```

3. **Langkah 3. Buat halaman beranda (tambahkan di bawah kode sebelumnya, pada file yang sama).**

```dart
class HalamanBeranda extends StatelessWidget {
  const HalamanBeranda({super.key});

  Future<void> _bukaDetail(BuildContext context) async {
    // Kirim data lewat konstruktor, lalu TUNGGU hasil dari halaman tujuan
    final hasil = await Navigator.push<String>(
      context,
      MaterialPageRoute(
        builder: (context) => const HalamanDetail(nama: 'Budi Santoso'),
      ),
    );

    // Pastikan widget masih terpasang sebelum memakai context setelah await
    if (!context.mounted) return;

    if (hasil != null) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('Hasil dari halaman detail: $hasil')),
      );
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Beranda')),
      body: Center(
        child: ElevatedButton(
          onPressed: () => _bukaDetail(context),
          child: const Text('Buka Halaman Detail'),
        ),
      ),
    );
  }
}
```

**Langkah 4. Buat halaman detail** yang menerima data lewat konstruktor dan mengem balikan hasil.

```dart
class HalamanDetail extends StatelessWidget {
  final String nama;

  const HalamanDetail({super.key, required this.nama});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Detail')),
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Text('Halo, $nama!', style: const TextStyle(fontSize: 22)),
            const SizedBox(height: 24),
            ElevatedButton(
              onPressed: () => Navigator.pop(context, 'Disetujui'),
              child: const Text('Setujui dan Kembali'),
            ),
            const SizedBox(height: 8),
            OutlinedButton(
              onPressed: () => Navigator.pop(context, 'Ditolak'),
              child: const Text('Tolak dan Kembali'),
            ),
          ],
        ),
      ),
    );
  }
}
```

**Langkah 5. Jalankan dan amati.**

Lakukan pengujian berikut:

1. Tekan **Buka Halaman Detail**. Perhatikan nama yang tampil.
2. Tekan **Setujui dan Kembali**. Perhatikan SnackBar di beranda.
3. Ulangi dengan **Tolak dan Kembali**.
4. Tekan tombol kembali (panah di AppBar atau tombol back sistem). Apa yang tampil di SnackBar? (Jawab: tidak ada, karena nilai yang dikembalikan null.)

### Praktikum 2: Named Routes dan Arguments

**Tujuan:** memakai routes, initialRoute, onGenerateRoute, dan arguments.

1. **Langkah 1. Buat proyek baru di Android Studio.** Beri nama **praktikum6\_named\_routes**, **Project type** Application, dan platform **Android & Web** dicentang. Tunggu hingga proses *indexing* dan *pub get* selesai, kemudian pastikan file lib/main.dart terbuka di editor.
2. **Langkah 2. Buat struktur file.** Klik kanan folder lib pada panel Project, pilih **New &gt; Dart File**, lalu ketik nama file tanpa  Buat tiga file baru berikut (main.dart sudah ada):

lib/

      - main.dart
      - halaman\_beranda.dart
      - halaman\_profil.dart
      - halaman\_produk.dart
3. **Isi halaman\_beranda.dart.** dengan kode dibawah ini

```dart
import 'package:flutter/material.dart';
class HalamanBeranda extends StatelessWidget {
  const HalamanBeranda({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Beranda')),
      body: Padding(
        padding: const EdgeInsets.all(16),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.stretch,
          children: [
            ElevatedButton(
              onPressed: () => Navigator.pushNamed(context, '/profil'),
              child: const Text('Ke Profil (tanpa data)'),
            ),
            const SizedBox(height: 12),
            ElevatedButton(
              onPressed: () => Navigator.pushNamed(
                context,
                '/produk',
                arguments: {'id': 7, 'nama': 'Laptop Gaming'},
              ),
              child: const Text('Ke Produk (dengan arguments)'),
            ),
          ],
        ),
      ),
    );
  }
}
```

4. **Isi halaman\_profil.dart.**

```dart
import 'package:flutter/material.dart';
class HalamanProfil extends StatelessWidget {
  const HalamanProfil({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Profil')),
      body: Center(
        child: ElevatedButton(
          onPressed: () => Navigator.pop(context),
          child: const Text('Kembali'),
        ),
      ),
    );
  }
}
```

5. **Isi halaman\_produk.dart.** Halaman ini membaca arguments yang dikirim.

```dart
import 'package:flutter/material.dart';

class HalamanProduk extends StatelessWidget {
  final int id;
  final String nama;

  const HalamanProduk({super.key, required this.id, required this.nama});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Produk #$id')),
      body: Center(
        child: Text(nama, style: const TextStyle(fontSize: 24)),
      ),
    );
  }
}
```

6. **Langkah 6. Isi main.dart.** Daftarkan route statis lewat routes dan route berargumen lewat onGenerateRoute.

```dart
import 'package:flutter/material.dart';
import 'halaman_beranda.dart';
import 'halaman_produk.dart';
import 'halaman_profil.dart';

void main() => runApp(const MyApp());

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Named Routes',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(colorSchemeSeed: Colors.teal, useMaterial3: true),
      initialRoute: '/',
      routes: {
        '/': (context) => const HalamanBeranda(),
        '/profil': (context) => const HalamanProfil(),
      },
      onGenerateRoute: (settings) {
        if (settings.name == '/produk') {
          final args = settings.arguments as Map<String, dynamic>;
          return MaterialPageRoute(
            builder: (context) => HalamanProduk(
              id: args['id'] as int,
              nama: args['nama'] as String,
            ),
          );
        }
        return null; // route tidak dikenal
      },
    );
  }
}
```

7. **Langkah 7. Jalankan aplikasi tersebyt lalu uji kedua tombol.**
8. **Langkah 8. Eksperimen (wajib).** Ubah '/profil' pada tombol menjadi '/profile' (salah ketik), lalu lakukan Hot Restart. Catat pesan error yang muncul (lihat panel Run di bagian bawah Android Studio), kemudian kembalikan seperti semula. Dari sini terlihat kelemahan named routes: nama berupa string sehingga rawan salah ketik, dan itu salah satu alasan go\_router direkomendasikan.

### Praktikum 3: Routing dengan go\_router

**Tujuan:** memasang go\_router, membuat rute dengan path parameter, query parameter, dan extra.

1. **Langkah 1.**  Buat proyek Flutter baru mengikuti butir  dengan **Project name** praktikum6\_go\_router, **Project type** Application, dan platform **Android** dicentang. Tunggu hingga proses *indexing* dan *pub get* selesai, kemudian pastikan file lib/main.dart terbuka di editor.
2. **Langkah 2. Pasang paket go\_router.**
   - Buka file **pubspec.yaml** pada panel Project (berada di bagian paling bawah daftar file).
   - Pada bagian dependencies:, tambahkan go\_router tepat di bawah flutter: (dengan sdk: flutter). Indentasi harus 2 spasi dan sejajar dengan **flutter:**

```yaml
flutter:
    sdk: flutter
  go_router: ^18.0.2
```

   - Klik **Pub get** pada banner di atas editor.
3. Ganti isi `lib/main.dart`.

```dart
import 'package:flutter/material.dart';
import 'package:go_router/go_router.dart';

void main() => runApp(const MyApp());

// Konfigurasi router
final GoRouter _router = GoRouter(
  initialLocation: '/',
  routes: [
    GoRoute(
      path: '/',
      name: 'beranda',
      builder: (context, state) => const BerandaPage(),
    ),
    GoRoute(
      path: '/produk/:id',
      name: 'produk',
      builder: (context, state) {
        final id = state.pathParameters['id']!;
        return ProdukPage(id: id);
      },
    ),
```

Perhatikan bahwa MaterialApp diganti menjadi **MaterialApp.router** dan memakai routerConfig.

```dart
GoRoute(
      path: '/cari',
      name: 'cari',
      builder: (context, state) {
        final kata = state.uri.queryParameters['q'] ?? '(kosong)';
        return CariPage(kataKunci: kata);
      },
    ),
    GoRoute(
      path: '/kontak',
      name: 'kontak',
      builder: (context, state) {
        final nama = state.extra as String? ?? 'Tanpa nama';
        return KontakPage(nama: nama);
      },
    ),
  ],
);

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp.router(
      title: 'Praktikum go_router',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(colorSchemeSeed: Colors.deepOrange, useMaterial3: true),
      routerConfig: _router,
    );
  }
}
```

4. **Tambahkan halaman beranda** di bawah kode tadi.

```dart
class BerandaPage extends StatelessWidget {
  const BerandaPage({super.key});
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Beranda go_router')),
      body: Padding(
        padding: const EdgeInsets.all(16),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.stretch,
          children: [


            ElevatedButton(
              // path parameter
              onPressed: () => context.push('/produk/42'),
              child: const Text('push: /produk/42'),
            ),
            const SizedBox(height: 12),
            ElevatedButton(
              // query parameter
              onPressed: () => context.push('/cari?q=flutter'),
              child: const Text('push: /cari?q=flutter'),
            ),
            const SizedBox(height: 12),
            ElevatedButton(
              // memakai nama route + parameter
              onPressed: () => context.pushNamed(
                'produk',
                pathParameters: {'id': '99'},
              ),
              child: const Text('pushNamed: produk id 99'),
            ),
            const SizedBox(height: 12),
            ElevatedButton(
              // objek lewat extra
              onPressed: () => context.push('/kontak', extra: 'Siti Aminah'),
              child: const Text('push dengan extra'),
            ),
            const SizedBox(height: 12),
            OutlinedButton(
              // go: mengganti tumpukan (tidak ada tombol back)
              onPressed: () => context.go('/produk/1'),
              child: const Text('go: /produk/1 (ganti tumpukan)'),
            ),
          ],
        ),
      ),
    );
  }
}
```

```dart
            ElevatedButton(
              // path parameter
              onPressed: () => context.push('/produk/42'),
              child: const Text('push: /produk/42'),
            ),
            const SizedBox(height: 12),
            ElevatedButton(
              // query parameter
              onPressed: () => context.push('/cari?q=flutter'),
              child: const Text('push: /cari?q=flutter'),
            ),
            const SizedBox(height: 12),
            ElevatedButton(
              // memakai nama route + parameter
              onPressed: () => context.pushNamed(
                'produk',
                pathParameters: {'id': '99'},
              ),
              child: const Text('pushNamed: produk id 99'),
            ),
            const SizedBox(height: 12),
            ElevatedButton(
              // objek lewat extra
              onPressed: () => context.push('/kontak', extra: 'Siti Aminah'),
              child: const Text('push dengan extra'),
            ),
            const SizedBox(height: 12),
            OutlinedButton(
              // go: mengganti tumpukan (tidak ada tombol back)
              onPressed: () => context.go('/produk/1'),
              child: const Text('go: /produk/1 (ganti tumpukan)'),
            ),
          ],
        ),
      ),
    );
  }
}
```

5. **Tambahkan tiga halaman tujuan.**

```dart
class ProdukPage extends StatelessWidget {
  final String id;
  const ProdukPage({super.key, required this.id});
```

```dart
@override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Produk $id')),
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
          Text('ID produk: $id', style: const TextStyle(fontSize: 22)),
            const SizedBox(height: 16),
            ElevatedButton(
              onPressed: () => context.go('/'),
              child: const Text('Kembali ke Beranda (go)'),
            ),    ],         ),        ),     );  } }

class CariPage extends StatelessWidget {
  final String kataKunci;
  const CariPage({super.key, required this.kataKunci});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Pencarian')),
      body: Center(
        child: Text('Kata kunci: $kataKunci',
            style: const TextStyle(fontSize: 22)),
      ),     );   } }

class KontakPage extends StatelessWidget {
  final String nama;
  const KontakPage({super.key, required this.nama});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Kontak')),
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Text('Nama: $nama', style: const TextStyle(fontSize: 22)),
            const SizedBox(height: 16),
            ElevatedButton(
              onPressed: () => context.pop(),
              child: const Text('Kembali (pop)'),
            ),
          ],
        ),
      ),
    );
  }
}
```

6. Jalankan dan amati perbedaan go dan push.
1. Tekan tombol push: /produk/42. Muncul tombol back di AppBar, tumpukan bertambah.
2. Kembali, lalu tekan go: /produk/1. Perhatikan: tidak ada tombol back otomatis karena tumpukan diganti.
3. Jika dijalankan di Chrome (pilih Chrome (web) pada device selector, lalu klik Run), perhatikan perubahan alamat URL pada bilah alamat untuk setiap halaman.

### Praktikum 6: Proyek Mini, Aplikasi Pendaftaran dan Login (Tampilan Saja)

**Tujuan:** menggabungkan go\_router, Form, validasi, dan pengiriman data antarhalaman. Aplikasi ini **tidak terhubung ke server** dan hanya menampilkan alur antarmuka.

1. **Langkah 1. Buat proyek dan pasang paket di Android Studio.**
   - Buat proyek Flutter baru, dengan **Project name** mini\_auth\_app.
   - Pasang paket go\_router dengan cara yang sama seperti Praktikum 3 (edit pubspec.yaml, lalu klik **Pub get**).
2. **Langkah 2. Siapkan struktur folder.** Klik kanan lib, pilih **New &gt; Directory**, ketik pages. Selanjutnya buat file Dart: router, validators di dalam lib, serta login\_page, register\_page, welcome\_page di dalam folder pages (klik kanan folder pages). Hasil akhirnya seperti ini:

```text
lib/
 ├─ main.dart
 ├─ router.dart
 ├─ validators.dart
 └─ pages/
     ├─ login_page.dart
     ├─ register_page.dart
     └─ welcome_page.dart
```

3. **Langkah 3. Isi lib/validators.dart.**

```dart
class Validators {
  /// Kolom wajib diisi
  static String? wajib(String? value, [String label = 'Kolom ini']) {
    if (value == null || value.trim().isEmpty) {
      return '$label wajib diisi';
    }
    return null;
  }

  /// Format email sederhana
  static String? email(String? value) {
    final kosong = wajib(value, 'Email');
    if (kosong != null) return kosong;


    final pola = RegExp(r'^[\w\.\-]+@([\w\-]+\.)+[\w\-]{2,}$');
    if (!pola.hasMatch(value!.trim())) {
      return 'Format email tidak valid';
    }
    return null;
  }

  /// Minimal 8 karakter dan mengandung angka
  static String? kataSandi(String? value) {
    final kosong = wajib(value, 'Kata sandi');
    if (kosong != null) return kosong;

    if (value!.length < 8) {
      return 'Kata sandi minimal 8 karakter';
    }
    if (!RegExp(r'\d').hasMatch(value)) {
      return 'Kata sandi harus mengandung minimal 1 angka';
    }
    return null;
  }

  /// Konfirmasi harus sama dengan kata sandi
  static String? konfirmasi(String? value, String sandiAsli) {
    final kosong = wajib(value, 'Konfirmasi kata sandi');
    if (kosong != null) return kosong;

    if (value != sandiAsli) {
      return 'Konfirmasi kata sandi tidak cocok';
    }
    return null;
  }
}
```

**Langkah 4. Isi lib/router.dart.** Pada file ini seluruh rute aplikasi didefinisikan.

```dart
    final pola = RegExp(r'^[\w\.\-]+@([\w\-]+\.)+[\w\-]{2,}$');
    if (!pola.hasMatch(value!.trim())) {
      return 'Format email tidak valid';
    }
    return null;
  }

  /// Minimal 8 karakter dan mengandung angka
  static String? kataSandi(String? value) {
    final kosong = wajib(value, 'Kata sandi');
    if (kosong != null) return kosong;

    if (value!.length < 8) {
      return 'Kata sandi minimal 8 karakter';
    }
    if (!RegExp(r'\d').hasMatch(value)) {
      return 'Kata sandi harus mengandung minimal 1 angka';
    }
    return null;
  }

  /// Konfirmasi harus sama dengan kata sandi
  static String? konfirmasi(String? value, String sandiAsli) {
    final kosong = wajib(value, 'Konfirmasi kata sandi');
    if (kosong != null) return kosong;

    if (value != sandiAsli) {
      return 'Konfirmasi kata sandi tidak cocok';
    }
    return null;
  }
}
```

```dart
import 'package:go_router/go_router.dart';
import 'pages/login_page.dart';
import 'pages/register_page.dart';
import 'pages/welcome_page.dart';

final GoRouter appRouter = GoRouter(
  initialLocation: '/login',
  routes: [
    GoRoute(
      path: '/login',
      name: 'login',
      builder: (context, state) => const LoginPage(),
    ),
    GoRoute(
      path: '/register',
      name: 'register',
      builder: (context, state) => const RegisterPage(),
    ),
    GoRoute(
      path: '/welcome',
      name: 'welcome',
      builder: (context, state) {
        // Data dikirim lewat extra; beri nilai cadangan bila kosong
        final nama = state.extra as String? ?? 'Pengguna';
        return WelcomePage(nama: nama);
      },
    ),
  ],
);
```

```dart
    GoRoute(
      path: '/welcome',
      name: 'welcome',
      builder: (context, state) {
        // Data dikirim lewat extra; beri nilai cadangan bila kosong
        final nama = state.extra as String? ?? 'Pengguna';
        return WelcomePage(nama: nama);
      },
    ),
  ],
);
```

4. **Langkah 5. Isi lib/main.dart.**

```dart
import 'package:flutter/material.dart';

import 'router.dart';

void main() => runApp(const MiniAuthApp());

class MiniAuthApp extends StatelessWidget {
  const MiniAuthApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp.router(
      title: 'Mini Auth App',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(
        colorSchemeSeed: Colors.indigo,
        useMaterial3: true,
      ),
      routerConfig: appRouter,
    );
  }
}
```

5. **Langkah 6. Isi lib/pages/login\_page.dart.**

```dart
import 'package:flutter/material.dart';
import 'package:go_router/go_router.dart';

import '../validators.dart';

class LoginPage extends StatefulWidget {
  const LoginPage({super.key});

  @override
  State<LoginPage> createState() => _LoginPageState();
}

class _LoginPageState extends State<LoginPage> {
  final _formKey = GlobalKey<FormState>();
  final _emailController = TextEditingController();
  final _sandiController = TextEditingController();
  bool _sembunyikanSandi = true;

  @override
  void dispose() {
    _emailController.dispose();
    _sandiController.dispose();
    super.dispose();   }

  void _masuk() {
    if (!_formKey.currentState!.validate()) return;

    // Tampilan saja: nama diambil dari bagian depan email
    final email = _emailController.text.trim();
    final nama = email.split('@').first;

    context.go('/welcome', extra: nama);   }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
        child: Center(
          child: SingleChildScrollView(
            padding: const EdgeInsets.all(24),
            child: ConstrainedBox(
              constraints: const BoxConstraints(maxWidth: 420),
              child: Form(
                key: _formKey,
                child: Column(
                  mainAxisSize: MainAxisSize.min,
                  crossAxisAlignment: CrossAxisAlignment.stretch,
                  children: [
                    const Icon(Icons.lock_open_rounded,
                        size: 72, color: Colors.indigo),
                    const SizedBox(height: 12),
                    Text(
                      'Selamat Datang',
                      textAlign: TextAlign.center,
                      style: Theme.of(context).textTheme.headlineMedium,
                    ),
                    const SizedBox(height: 4),
                    const Text(
                      'Masuk untuk melanjutkan',
                      textAlign: TextAlign.center,
                    ),
                    const SizedBox(height: 32),
                    TextFormField(
                      controller: _emailController,
                      keyboardType: TextInputType.emailAddress,
                      textInputAction: TextInputAction.next,
                      decoration: const InputDecoration(
                        labelText: 'Email',
                        prefixIcon: Icon(Icons.email_outlined),
                        border: OutlineInputBorder(),
                      ),
                      validator: Validators.email,
                    ),
                    const SizedBox(height: 16),
                    TextFormField(
                      controller: _sandiController,
                      obscureText: _sembunyikanSandi,
                      textInputAction: TextInputAction.done,
                      onFieldSubmitted: (_) => _masuk(),
                      decoration: InputDecoration(
                        labelText: 'Kata sandi',
                        prefixIcon: const Icon(Icons.lock_outline),
                        border: const OutlineInputBorder(),
                        suffixIcon: IconButton(
                          icon: Icon(_sembunyikanSandi
                              ? Icons.visibility
                              : Icons.visibility_off),
                          onPressed: () => setState(
                              () => _sembunyikanSandi = !_sembunyikanSandi),
                        ),
                      ),
                      // Pada login cukup periksa tidak kosong
                      validator: (v) => Validators.wajib(v, 'Kata sandi'),
                    ),
                    const SizedBox(height: 24),
                    FilledButton(
                      onPressed: _masuk,
                      child: const Padding(
                        padding: EdgeInsets.symmetric(vertical: 12),
                        child: Text('Masuk'),
                      ),
                    ),
                    const SizedBox(height: 12),
                    Row(
                      mainAxisAlignment: MainAxisAlignment.center,
                      children: [
                        const Text('Belum punya akun?'),
                        TextButton(
                          onPressed: () => context.push('/register'),
                          child: const Text('Daftar'),
                        ),
                      ],
                    ),
                  ],
                ),
              ),
            ),
          ),
        ),
      ),
    );
  }
}
```

```dart
                      textAlign: TextAlign.center,
                      style: Theme.of(context).textTheme.headlineMedium,
                    ),
                    const SizedBox(height: 4),
                    const Text(
                      'Masuk untuk melanjutkan',
                      textAlign: TextAlign.center,
                    ),
                    const SizedBox(height: 32),
                    TextFormField(
                      controller: _emailController,
                      keyboardType: TextInputType.emailAddress,
                      textInputAction: TextInputAction.next,
                      decoration: const InputDecoration(
                        labelText: 'Email',
                        prefixIcon: Icon(Icons.email_outlined),
                        border: OutlineInputBorder(),
                      ),
                      validator: Validators.email,
                    ),
                    const SizedBox(height: 16),
                    TextFormField(
                      controller: _sandiController,
                      obscureText: _sembunyikanSandi,
                      textInputAction: TextInputAction.done,
                      onFieldSubmitted: (_) => _masuk(),
                      decoration: InputDecoration(
                        labelText: 'Kata sandi',
                        prefixIcon: const Icon(Icons.lock_outline),
                        border: const OutlineInputBorder(),
                        suffixIcon: IconButton(
                          icon: Icon(_sembunyikanSandi
                              ? Icons.visibility
                              : Icons.visibility_off),
                          onPressed: () => setState(
                          () => _sembunyikanSandi = !_sembunyikanSandi),
                        ),
                      ),
                 // Pada login cukup periksa tidak kosong
                   validator: (v) => Validators.wajib(v, 'Kata sandi'),
                    ),
                    const SizedBox(height: 24),
                    FilledButton(
                      onPressed: _masuk,
                      child: const Padding(
                        padding: EdgeInsets.symmetric(vertical: 12),
                        child: Text('Masuk'),
                      ),
                    ),
                    const SizedBox(height: 12),
                    Row(
                      mainAxisAlignment: MainAxisAlignment.center,
                      children: [
                        const Text('Belum punya akun?'),
                        TextButton(
                          onPressed: () => context.push('/register'),
                          child: const Text('Daftar'),
                        ),
                      ],
                    ),
                  ],
                ),
              ),
            ),
          ),
        ),
      ),
    );
  }
}
```

```dart
                        const Text('Belum punya akun?'),
                        TextButton(
                          onPressed: () => context.push('/register'),
                          child: const Text('Daftar'),
                        ),
                      ],
                    ),
                  ],
                ),
              ),
            ),
          ),
        ),
      ),
    );
  }
}
```

6. **Langkah 7. Isi lib/pages/register\_page.dart.**

```dart
import 'package:flutter/material.dart';
import 'package:go_router/go_router.dart';

import '../validators.dart';

class RegisterPage extends StatefulWidget {
  const RegisterPage({super.key});

  @override
  State<RegisterPage> createState() => _RegisterPageState();
}

class _RegisterPageState extends State<RegisterPage> {
  final _formKey = GlobalKey<FormState>();
  final _namaController = TextEditingController();
  final _emailController = TextEditingController();
  final _sandiController = TextEditingController();
  final _konfirmasiController = TextEditingController();

  bool _sembunyikanSandi = true;
  bool _setuju = false;

  @override
  void dispose() {
    _namaController.dispose();
    _emailController.dispose();
    _sandiController.dispose();
    _konfirmasiController.dispose();
    super.dispose();
  }

  void _daftar() {
    if (!_formKey.currentState!.validate()) return;

    // Checkbox bukan bagian dari validator TextFormField,
    // jadi diperiksa secara terpisah
    if (!_setuju) {
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(
          content: Text('Anda harus menyetujui syarat dan ketentuan'),
        ),
      );
      return;
    }

    // Kirim nama ke halaman selamat datang
    context.go('/welcome', extra: _namaController.text.trim());
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Daftar Akun')),
      body: SafeArea(
        child: Center(
          child: SingleChildScrollView(
            padding: const EdgeInsets.all(24),
            child: ConstrainedBox(
              constraints: const BoxConstraints(maxWidth: 420),
              child: Form(
                key: _formKey,
                autovalidateMode: AutovalidateMode.onUserInteraction,
                child: Column(
                  crossAxisAlignment: CrossAxisAlignment.stretch,
                  children: [
                    TextFormField(
                      controller: _namaController,
                      textInputAction: TextInputAction.next,
                      textCapitalization: TextCapitalization.words,
                      decoration: const InputDecoration(
                        labelText: 'Nama lengkap',
                        prefixIcon: Icon(Icons.person_outline),
                        border: OutlineInputBorder(),
                      ),
                      validator: (v) => Validators.wajib(v, 'Nama'),
                    ),
                    const SizedBox(height: 16),
                    TextFormField(
                      controller: _emailController,
                      keyboardType: TextInputType.emailAddress,
                      textInputAction: TextInputAction.next,
                      decoration: const InputDecoration(
                        labelText: 'Email',
                        prefixIcon: Icon(Icons.email_outlined),
                        border: OutlineInputBorder(),
                      ),
                      validator: Validators.email,
                    ),
                    const SizedBox(height: 16),
                    TextFormField(
                      controller: _sandiController,
                      obscureText: _sembunyikanSandi,
                      textInputAction: TextInputAction.next,
                      decoration: InputDecoration(
                        labelText: 'Kata sandi',
                        helperText: 'Minimal 8 karakter dan ada angka',
                        prefixIcon: const Icon(Icons.lock_outline),
                        border: const OutlineInputBorder(),
                        suffixIcon: IconButton(
                          icon: Icon(_sembunyikanSandi
                              ? Icons.visibility
                              : Icons.visibility_off),
                          onPressed: () => setState(
                              () => _sembunyikanSandi = !_sembunyikanSandi),
                        ),
                      ),
                      validator: Validators.kataSandi,
                    ),
                    const SizedBox(height: 16),
                    TextFormField(
                      controller: _konfirmasiController,
                      obscureText: _sembunyikanSandi,
                      decoration: const InputDecoration(
                        labelText: 'Konfirmasi kata sandi',
                        prefixIcon: Icon(Icons.lock_reset),
                        border: OutlineInputBorder(),
                      ),
                      validator: (v) =>
                          Validators.konfirmasi(v, _sandiController.text),
                    ),
                    const SizedBox(height: 8),
                    CheckboxListTile(
                      value: _setuju,
                      onChanged: (v) => setState(() => _setuju = v ?? false),
                      controlAffinity: ListTileControlAffinity.leading,
                      contentPadding: EdgeInsets.zero,
                      title: const Text('Saya menyetujui syarat dan ketentuan'),
                    ),
                    const SizedBox(height: 16),
                    FilledButton(
                      onPressed: _daftar,
                      child: const Padding(
                        padding: EdgeInsets.symmetric(vertical: 12),
                        child: Text('Daftar'),
                      ),
                    ),
                    TextButton(
                      onPressed: () => context.pop(),
                      child: const Text('Sudah punya akun? Masuk'),
                    ),
                  ],
                ),
              ),
            ),
          ),
        ),
      ),
    );
  }
}
```

```dart
  void _daftar() {
    if (!_formKey.currentState!.validate()) return;

    // Checkbox bukan bagian dari validator TextFormField,
    // jadi diperiksa secara terpisah
    if (!_setuju) {
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(
          content: Text('Anda harus menyetujui syarat dan ketentuan'),
        ),
      );
      return;
    }

    // Kirim nama ke halaman selamat datang
    context.go('/welcome', extra: _namaController.text.trim());
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Daftar Akun')),
      body: SafeArea(
        child: Center(
          child: SingleChildScrollView(
            padding: const EdgeInsets.all(24),
            child: ConstrainedBox(
              constraints: const BoxConstraints(maxWidth: 420),
              child: Form(
                key: _formKey,
                autovalidateMode: AutovalidateMode.onUserInteraction,
                child: Column(
                  crossAxisAlignment: CrossAxisAlignment.stretch,
                  children: [
                    TextFormField(
                      controller: _namaController,
                      textInputAction: TextInputAction.next,
                      textCapitalization: TextCapitalization.words,
                      decoration: const InputDecoration(
                        labelText: 'Nama lengkap',
                        prefixIcon: Icon(Icons.person_outline),
                        border: OutlineInputBorder(),
                      ),
                      validator: (v) => Validators.wajib(v, 'Nama'),
                    ),
                    const SizedBox(height: 16),
                    TextFormField(
                      controller: _emailController,
                      keyboardType: TextInputType.emailAddress,
                      textInputAction: TextInputAction.next,
                      decoration: const InputDecoration(
                        labelText: 'Email',
                        prefixIcon: Icon(Icons.email_outlined),
                        border: OutlineInputBorder(),
                      ),
                      validator: Validators.email,
                    ),
                    const SizedBox(height: 16),
                    TextFormField(
                      controller: _sandiController,
                      obscureText: _sembunyikanSandi,
                      textInputAction: TextInputAction.next,
                      decoration: InputDecoration(
                        labelText: 'Kata sandi',
                        helperText: 'Minimal 8 karakter dan ada angka',
                        prefixIcon: const Icon(Icons.lock_outline),
                        border: const OutlineInputBorder(),
                        suffixIcon: IconButton(
                          icon: Icon(_sembunyikanSandi
                              ? Icons.visibility
                              : Icons.visibility_off),
                          onPressed: () => setState(
                              () => _sembunyikanSandi = !_sembunyikanSandi),
                        ),
                      ),
                      validator: Validators.kataSandi,
                    ),
                    const SizedBox(height: 16),
                    TextFormField(
                      controller: _konfirmasiController,
                      obscureText: _sembunyikanSandi,
                      decoration: const InputDecoration(
                        labelText: 'Konfirmasi kata sandi',
                        prefixIcon: Icon(Icons.lock_reset),
                        border: OutlineInputBorder(),
                      ),
                      validator: (v) =>
                          Validators.konfirmasi(v, _sandiController.text),
                    ),
                    const SizedBox(height: 8),
                    CheckboxListTile(
                      value: _setuju,
                      onChanged: (v) => setState(() => _setuju = v ?? false),
                      controlAffinity: ListTileControlAffinity.leading,
                      contentPadding: EdgeInsets.zero,
                      title: const Text('Saya menyetujui syarat dan ketentuan'),
                    ),
                    const SizedBox(height: 16),
                    FilledButton(
                      onPressed: _daftar,
                      child: const Padding(
                        padding: EdgeInsets.symmetric(vertical: 12),
                        child: Text('Daftar'),
                      ),
                    ),
                    TextButton(
                      onPressed: () => context.pop(),
                      child: const Text('Sudah punya akun? Masuk'),
                    ),
                  ],
                ),
              ),
            ),
          ),
        ),
      ),
    );
  }
}
```

```dart
                      ),
                      validator: Validators.email,
                    ),
                    const SizedBox(height: 16),
                    TextFormField(
                      controller: _sandiController,
                      obscureText: _sembunyikanSandi,
                      textInputAction: TextInputAction.next,
                      decoration: InputDecoration(
                        labelText: 'Kata sandi',
                        helperText: 'Minimal 8 karakter dan ada angka',
                        prefixIcon: const Icon(Icons.lock_outline),
                        border: const OutlineInputBorder(),
                        suffixIcon: IconButton(
                          icon: Icon(_sembunyikanSandi
                              ? Icons.visibility
                              : Icons.visibility_off),
                          onPressed: () => setState(
                              () => _sembunyikanSandi = !_sembunyikanSandi),
                        ),
                      ),
                      validator: Validators.kataSandi,
                    ),
                    const SizedBox(height: 16),
                    TextFormField(
                      controller: _konfirmasiController,
                      obscureText: _sembunyikanSandi,
                      decoration: const InputDecoration(
                        labelText: 'Konfirmasi kata sandi',
                        prefixIcon: Icon(Icons.lock_reset),
                        border: OutlineInputBorder(),
                      ),
                      validator: (v) =>
                          Validators.konfirmasi(v, _sandiController.text),
                    ),
                    const SizedBox(height: 8),
                    CheckboxListTile(
                      value: _setuju,
                      onChanged: (v) => setState(() => _setuju = v ?? false),
                      controlAffinity: ListTileControlAffinity.leading,
                      contentPadding: EdgeInsets.zero,
                      title: const Text('Saya menyetujui syarat dan ketentuan'),
                    ),
                    const SizedBox(height: 16),
                    FilledButton(
                      onPressed: _daftar,
                      child: const Padding(
                        padding: EdgeInsets.symmetric(vertical: 12),
                        child: Text('Daftar'),
                      ),
                    ),
                    TextButton(
                      onPressed: () => context.pop(),
                      child: const Text('Sudah punya akun? Masuk'),
                    ),
                  ],
                ),
              ),
            ),
          ),
        ),
      ),
    );
  }
}
```

```dart
                      ),
                    ),
                    TextButton(
                      onPressed: () => context.pop(),
                      child: const Text('Sudah punya akun? Masuk'),
                    ), ], ), ),), ), ), ), ); }}
```

7. **Langkah 8. Isi lib/pages/welcome\_page.dart.**

```dart
import 'package:flutter/material.dart';
import 'package:go_router/go_router.dart';
class WelcomePage extends StatelessWidget {
  final String nama;

  const WelcomePage({super.key, required this.nama});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Beranda'),
        automaticallyImplyLeading: false,
      ),
      body: Center(
        child: Padding(
          padding: const EdgeInsets.all(24),
          child: Column(
            mainAxisSize: MainAxisSize.min,
            children: [
         const Icon(Icons.check_circle, size: 96, color: Colors.green),
              const SizedBox(height: 16),
              Text(
                'Halo, $nama!',
                style: Theme.of(context).textTheme.headlineMedium,
                textAlign: TextAlign.center,
              ),
              const SizedBox(height: 8),
              const Text('Anda berhasil masuk (tampilan saja).'),
              const SizedBox(height: 32),
              OutlinedButton.icon(
                onPressed: () => context.go('/login'),
                icon: const Icon(Icons.logout),
                label: const Text('Keluar'),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

8. **Langkah 9. Jalankan aplikasi.**

Pilih emulator (atau Chrome) pada device selector, lalu klik tombol Run. Jika ada garis merah pada kode sebelum dijalankan, perbaiki dengan Alt+

9. **Langkah 10. Uji alur lengkap dan centang bila berhasil:**

| No | Skenario | Hasil yang diharapkan | Berhasil |
|---|---|---|---|
| 1 | Login dengan kolom kosong | Pesan error muncul di kedua kolom | [ ] |
| 2 | Login dengan email abc | "Format email tidak valid" | [ ] |
| 3 | Login dengan budi@kampus.ac.id dan sandi apa saja | Pindah ke halaman Selamat Datang, tertulis "Halo, budi!" | [ ] |
| 4 | Tekan Daftar di halaman login | Pindah ke halaman Daftar, ada tombol back | [ ] |
| 5 | Daftar tanpa mencentang syarat | SnackBar peringatan | [ ] |
| 6 | Daftar dengan data benar dan centang | Halaman Selamat Datang menampilkan nama yang diketik | [ ] |
| 7 | Tekan Keluar | Kembali ke halaman Login | [ ] |

## Post-Test

1. Apa yang terjadi ketika kode Navigator.pop(context, 'ok') dijalankan?
2. Dalam go\_router, apa yang dimaksud dengan :id pada pola path /produk/:id?
3. Widget apakah yang digunakan untuk mengelompokkan beberapa input atau kolom agar dapat divalidasi secara bersamaan?

## Latihan/Tugas

Tugas 1: Pengembangan Proyek Mini (Wajib)

Kembangkan aplikasi **mini\_auth\_app** dengan ketentuan berikut:

1. Tambahkan kolom Nomor HP pada form pendaftaran dengan validasi: hanya angka, panjang 10 sampai 13 digit, diawali 08.
2. Tambahkan DropdownButtonFormField untuk memilih Program Studi (minimal 4 pilihan) dengan validasi wajib dipilih.
3. Tambahkan halaman baru Ringkasan Pendaftaran (/ringkasan) yang menampilkan seluruh data yang diisi (nama, email, nomor HP, program studi) sebelum menuju halaman Selamat Datang. Kirim data menggunakan sebuah kelas model (misalnya DataPendaftar) melalui extra, bukan sekadar String.
4. Pada halaman Ringkasan sediakan dua tombol: Ubah (kembali ke form dengan pop) dan Konfirmasi (lanjut ke Selamat Datang dengan go).
5. Tambahkan halaman Lupa Kata Sandi (/lupa-sandi) berisi satu kolom email dengan validasi format. Setelah valid, tampilkan SnackBar dan kembali ke halaman Login.
