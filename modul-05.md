# Modul 5: Layout, Styling, dan Desain Responsif

## Maksud dan Tujuan

1. Membekali mahasiswa dengan teknik menyusun antarmuka Flutter yang satu kode, tampil baik di ponsel, tablet, web, dan desktop.
2. Memberikan pengalaman langsung menampilkan data berulang dan membagi ruang tata letak secara fleksibel.
3. Melatih mahasiswa menerapkan tema Material 3 dan mode gelap yang konsisten pada seluruh aplikasi. Menampilkan data berulang dengan ListView dan GridView (termasuk versi builder).
4. Membagi ruang dengan Expanded dan Flexible.
5. Membaca ukuran layar dan ruang yang tersedia dengan MediaQuery dan LayoutBuilder.
6. Membuat tata letak adaptif berdasarkan breakpoint lebar.
7. Menerapkan Material 3, ThemeData, ColorScheme, dan mode gelap (ThemeMode).
8. Membuat katalog produk responsif yang berubah bentuk menurut lebar layar.

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

### 1. ListView dan GridView

**ListView** adalah widget Flutter yang digunakan untuk menampilkan sekumpulan widget atau data dalam bentuk daftar yang tersusun secara vertikal atau horizontal dan dapat digulir (scroll). ListView cocok digunakan untuk membuat tampilan seperti daftar mahasiswa, daftar menu, daftar berita, atau daftar pesan.

**GridView** adalah widget Flutter yang digunakan untuk menampilkan sekumpulan widget atau data dalam bentuk grid atau susunan baris dan kolom yang dapat digulir. GridView cocok digunakan untuk membuat tampilan seperti katalog produk, galeri foto, menu aplikasi, atau dashboard.

Perbedaan utama:

ListView lebih sesuai untuk tampilan daftar, sedangkan GridView lebih sesuai untuk tampilan kotak-kotak dalam beberapa kolom.

| **Widget** | **Kegunaan** | **Catatan** |
|---|---|---|
| ListView(children: [...]) | Daftar pendek dan tetap | Semua anak dibuat sekaligus |
| ListView.builder | Daftar panjang/dinamis | Item dibuat **malas** (*lazy*), hanya yang terlihat |
| ListView.separated | Daftar dengan pemisah antar-item | Memakai separatorBuilder |
| GridView.count | Grid dengan **jumlah kolom tetap** | crossAxisCount |
| GridView.builder | Grid panjang/dinamis | Memakai gridDelegate |
| SliverGridDelegateWithFixedCrossAxisCount | Jumlah kolom tetap | crossAxisCount |
| SliverGridDelegateWithMaxCrossAxisExtent | Lebar item maksimum; **jumlah kolom menyesuaikan lebar layar** | maxCrossAxisExtent |

### 2. Expanded dan Flexible

Keduanya hanya valid sebagai anak langsung Row, Column, atau Flex.

| **Widget** | **Perilaku** |
|---|---|
| Expanded | child **dipaksa mengisi** jatah ruangnya (sama dengan Flexible(fit: FlexFit.tight)) |
| Flexible (default FlexFit.loose) | Child **boleh lebih kecil** dari jatahnya |
| flex | Perbandingan pembagian sisa ruang (mis. flex: 1 dan flex: 2 → 1 : 2) |

### 3. Desain Responsif dan Adaptif

- **Responsif:** tampilan menyesuaikan ukuran ruang yang tersedia.
- **Adaptif:** tampilan menyesuaikan jenis perangkat/platform (mis. menu samping di desktop, daftar tunggal di ponsel).

**Kelas ukuran jendela (Material 3):**

| **Kelas** | **Lebar** | **Perangkat umum** |
|---|---|---|
| Compact | &lt; 600 | Ponsel (potret) |
| Medium | 600 – 839 | Tablet kecil, ponsel lipat, ponsel (lanskap) |
| Expanded | ≥ 840 | Tablet besar, web, desktop |

### 4. Material 3, ThemeData, dan Mode Gelap

- **Material 3** adalah sistem desain terbaru Google (bawaan Flutter terbaru; useMaterial3: true).
- ColorScheme.fromSeed(seedColor: ...) menghasilkan seluruh palet warna harmonis dari **satu warna benih**, dengan peran seperti primary, onPrimary, primaryContainer, secondary, tertiary, surface, error.
- ThemeData menyimpan tema: warna, tipografi (textTheme), dan tema komponen (mis. appBarTheme, elevatedButtonTheme).
- Menerapkan tema: MaterialApp(theme: ..., darkTheme: ..., themeMode: ...). Mengambil tema: Theme.of(context).
- ThemeMode: system (mengikuti perangkat), light, dark.
- Agar mode gelap benar, **ambil warna dari Theme.of(context).colorScheme**, bukan menulis Colors.white/Colors.black secara langsung.

## Pre-Test

1. Jelaskan perbedaan StatelessWidget dan StatefulWidget.
1. ……………………..
2. Apa yang dimaksud widget tree?
3. Apa masalah yang mungkin muncul jika sebuah aplikasi ditulis dengan ukuran tetap (mis. width: 400) lalu dibuka di layar tablet atau desktop?

## Praktikum

Seluruh praktikum dikerjakan di Android Studio pada proyek Flutter dan dijalankan di Chrome (web); emulator Android dipakai sebagai pembanding. Tiap praktikum berupa satu file di folder lib/ yang memiliki main() sendiri.

### Praktikum 1 – Membuat Projek baru di modul 5

1. File → New → New Flutter Project → pilih Flutter → Next.
2. Project name: modul5\_responsif. Pada Platforms centang Android dan Web (tambahkan Windows/macOS/Linux bila ingin uji desktop) → Create.
3. Jika proyek sudah ada tetapi belum memiliki target web, jalankan di Terminal: flutter create . --platforms=web.

### Praktikum 2 -  ListView

**Tujuan:** menampilkan daftar statis, daftar panjang dengan builder, dan daftar berpemisah.

**Langkah pelaksanaan di Android Studio:**

1. Buat file praktikum\_listview.dart di folder lib (**New → Dart File**) dan salin kode di bawah.
2. Pilih **Chrome (web)** di toolbar, klik **▶** di samping void main() → **Run 'p1\_listview.dart'**.
3. Pindah antar tab *children*, *builder*, *separated*; gulir tiap daftar.
4. Pada tab *builder*, gulir ke bawah dan amati log build item ... di panel **Run**: hanya item yang terlihat yang dibuat.
5. Pada tab *children*, hapus Expanded yang membungkus ListView vertikal, lakukan **hot restart**, dan amati error *Vertical viewport was given unbounded height*. Kembalikan kodenya.
6. Pada tab *separated*, ganti Divider(height: 1) dengan SizedBox(height: 8) dan amati perubahannya.

**File:** praktikum\_listview.dart

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MyApp());

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      theme: ThemeData(colorSchemeSeed: Colors.indigo),
      home: const DaftarPage(),
    );
  }
}

class DaftarPage extends StatelessWidget {
  const DaftarPage({super.key});

  @override
  Widget build(BuildContext context) {
    return DefaultTabController(
      length: 3,
      child: Scaffold(
        appBar: AppBar(
          title: const Text('ListView'),
          bottom: const TabBar(
            tabs: [
              Tab(text: 'children'),
              Tab(text: 'builder'),
              Tab(text: 'separated'),
            ],
          ),
        ),
        body: const TabBarView(
          children: [TabStatis(), TabBuilder(), TabSeparated()],
        ),
      ),
    );
  }
}

// 1. ListView biasa + ListView horizontal
class TabStatis extends StatelessWidget {
  const TabStatis({super.key});

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        SizedBox(
          height: 64,
          child: ListView(
            scrollDirection: Axis.horizontal,
            padding: const EdgeInsets.all(12),
            children: [
              for (final k in ['Semua', 'Makanan', 'Minuman', 'Snack', 'Dessert'])
                Padding(
                  padding: const EdgeInsets.only(right: 8),
                  child: Chip(label: Text(k)),
                ),
            ],
          ),
        ),
        const Divider(height: 1),
        // Tanpa Expanded, ListView vertikal di dalam Column akan error
        const Expanded(
          child: ListView(
            children: [
              ListTile(
                leading: Icon(Icons.person),
                title: Text('Profil'),
                subtitle: Text('Ubah data diri'),
                trailing: Icon(Icons.chevron_right),
              ),
              ListTile(
                leading: Icon(Icons.notifications),
                title: Text('Notifikasi'),
                subtitle: Text('Atur pemberitahuan'),
                trailing: Icon(Icons.chevron_right),
              ),
              ListTile(
                leading: Icon(Icons.lock),
                title: Text('Privasi'),
                subtitle: Text('Kata sandi dan keamanan'),
                trailing: Icon(Icons.chevron_right),
              ),
              ListTile(
                leading: Icon(Icons.help),
                title: Text('Bantuan'),
                trailing: Icon(Icons.chevron_right),
              ),
            ],
          ),
        ),
      ],
    );
  }
}

// 2. ListView.builder: item dibuat saat terlihat saja
class TabBuilder extends StatelessWidget {
  const TabBuilder({super.key});

  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: 1000,
      itemBuilder: (context, index) {
        debugPrint('build item $index');
        return ListTile(
          leading: CircleAvatar(child: Text('${index + 1}')),
          title: Text('Item ke-${index + 1}'),
          subtitle: const Text('Dibuat saat terlihat di layar'),
        );
      },
    );
  }
}

// 3. ListView.separated: ada pemisah antar-item
class TabSeparated extends StatelessWidget {
  const TabSeparated({super.key});

  @override
  Widget build(BuildContext context) {
    final nama = List.generate(15, (i) => 'Mahasiswa ${i + 1}');
    return ListView.separated(
      itemCount: nama.length,
      separatorBuilder: (context, index) => const Divider(height: 1),
      itemBuilder: (context, index) => ListTile(
        leading: const Icon(Icons.person_outline),
        title: Text(nama[index]),
        trailing: const Icon(Icons.chevron_right),
        onTap: () {
          ScaffoldMessenger.of(context).showSnackBar(
            SnackBar(content: Text('Dipilih: ${nama[index]}')),
          );
        },
      ),
    );
  }
}
```

```dart
        body: const TabBarView(
          children: [TabStatis(), TabBuilder(), TabSeparated()],
        ),
      ),
    );
  }
}

// 1. ListView biasa + ListView horizontal
class TabStatis extends StatelessWidget {
  const TabStatis({super.key});

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        SizedBox(
          height: 64,
          child: ListView(
            scrollDirection: Axis.horizontal,
            padding: const EdgeInsets.all(12),
            children: [
              for (final k in ['Semua', 'Makanan', 'Minuman', 'Snack', 'Dessert'])
                Padding(
                  padding: const EdgeInsets.only(right: 8),
                  child: Chip(label: Text(k)),
                ),
            ],
          ),
        ),
        const Divider(height: 1),
        // Tanpa Expanded, ListView vertikal di dalam Column akan error
        const Expanded(
          child: ListView(
            children: [
              ListTile(
                leading: Icon(Icons.person),
                title: Text('Profil'),
                subtitle: Text('Ubah data diri'),
                trailing: Icon(Icons.chevron_right),
              ),
              ListTile(
                leading: Icon(Icons.notifications),
                title: Text('Notifikasi'),
                subtitle: Text('Atur pemberitahuan'),
                trailing: Icon(Icons.chevron_right),
              ),
              ListTile(
                leading: Icon(Icons.lock),
                title: Text('Privasi'),
                subtitle: Text('Kata sandi dan keamanan'),
                trailing: Icon(Icons.chevron_right),
              ),
              ListTile(
                leading: Icon(Icons.help),
                title: Text('Bantuan'),
                trailing: Icon(Icons.chevron_right),
              ),
            ],
          ),
        ),
      ],
    );
  }
}

// 2. ListView.builder: item dibuat saat terlihat saja
class TabBuilder extends StatelessWidget {
  const TabBuilder({super.key});

  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: 1000,
      itemBuilder: (context, index) {
        debugPrint('build item $index');
        return ListTile(
          leading: CircleAvatar(child: Text('${index + 1}')),
          title: Text('Item ke-${index + 1}'),
          subtitle: const Text('Dibuat saat terlihat di layar'),
        );
      },
    );
  }
}

// 3. ListView.separated: ada pemisah antar-item
class TabSeparated extends StatelessWidget {
  const TabSeparated({super.key});

  @override
  Widget build(BuildContext context) {
    final nama = List.generate(15, (i) => 'Mahasiswa ${i + 1}');
    return ListView.separated(
      itemCount: nama.length,
      separatorBuilder: (context, index) => const Divider(height: 1),
      itemBuilder: (context, index) => ListTile(
        leading: const Icon(Icons.person_outline),
        title: Text(nama[index]),
        trailing: const Icon(Icons.chevron_right),
        onTap: () {
          ScaffoldMessenger.of(context).showSnackBar(
            SnackBar(content: Text('Dipilih: ${nama[index]}')),
          );
        },
      ),
    );
  }
}
```

```dart
              ),
            ],
          ),
        ),
      ],
    );
  }
}

// 2. ListView.builder: item dibuat saat terlihat saja
class TabBuilder extends StatelessWidget {
  const TabBuilder({super.key});

  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: 1000,
      itemBuilder: (context, index) {
        debugPrint('build item $index');
        return ListTile(
          leading: CircleAvatar(child: Text('${index + 1}')),
          title: Text('Item ke-${index + 1}'),
          subtitle: const Text('Dibuat saat terlihat di layar'),
        );
      },
    );
  }
}

// 3. ListView.separated: ada pemisah antar-item
class TabSeparated extends StatelessWidget {
  const TabSeparated({super.key});

  @override
  Widget build(BuildContext context) {
    final nama = List.generate(15, (i) => 'Mahasiswa ${i + 1}');
    return ListView.separated(
      itemCount: nama.length,
      separatorBuilder: (context, index) => const Divider(height: 1),
      itemBuilder: (context, index) => ListTile(
        leading: const Icon(Icons.person_outline),
        title: Text(nama[index]),
        trailing: const Icon(Icons.chevron_right),
        onTap: () {
          ScaffoldMessenger.of(context).showSnackBar(
            SnackBar(content: Text('Dipilih: ${nama[index]}')),
          );
        },
      ),
    );
  }
}
```

Simpan file tersebut, jalankan dan amati hasilnya.

**Hasil yang diharapkan:**

![](.gitbook/assets/modul05-01.png)

- Tab *children*: deretan *chip* yang dapat digeser ke samping, di bawahnya empat menu pengaturan.
- Tab *builder*: daftar 1000 item; log build item N hanya muncul untuk item yang sedang atau akan terlihat.
- Tab *separated*: 15 nama dengan garis pemisah; mengetuk item memunculkan *snackbar* "Dipilih: ...".

### Praktikum 3 - GridView

**Tujuan:** membuat grid dengan jumlah kolom tetap dan grid yang jumlah kolomnya menyesuaikan lebar layar.

**Langkah pelaksanaan di Android Studio:**

1. Buat file praktikum\_gridview.dart di folder lib dan salin kode di bawah.
2. Klik **▶** di samping void main() → **Run 'p2\_gridview.dart'** (target Chrome).
3. **Ubah lebar jendela Chrome** dari sempit ke lebar pada tiap tab. Catat jumlah kolom pada masing-masing tab.
4. Pada tab *count*, ubah crossAxisCount menjadi 3 dan 4, lalu **hot reload**.
5. Pada tab *builder*, ubah childAspectRatio (mis. 0.8 dan 1.6) dan amati bentuk kotak.
6. Pada tab *extent*, ubah maxCrossAxisExtent (mis. 100 dan 250) dan amati perubahan jumlah kolom.

**File:** praktikum\_gridview.dart

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MyApp());
class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      theme: ThemeData(colorSchemeSeed: Colors.teal),
      home: const GridPage(),
    );
  }
}

class GridPage extends StatelessWidget {
  const GridPage({super.key});

  @override
  Widget build(BuildContext context) {
    return DefaultTabController(
      length: 3,
      child: Scaffold(
        appBar: AppBar(
          title: const Text('GridView'),
          bottom: const TabBar(
            tabs: [
              Tab(text: 'count'),
              Tab(text: 'builder'),
              Tab(text: 'extent'),
            ],
          ),
        ),
        body: const TabBarView(
          children: [GridCount(), GridBuilder(), GridExtent()],
        ),
      ),
    );
  }
}

class KotakGrid extends StatelessWidget {
  final int nomor;
  const KotakGrid({super.key, required this.nomor});

  @override
  Widget build(BuildContext context) {
    final warna = Colors.primaries[nomor % Colors.primaries.length];
    return Container(
      decoration: BoxDecoration(
        color: warna,
        borderRadius: BorderRadius.circular(12),
      ),
      alignment: Alignment.center,
      child: Text(
        '$nomor',
        style: const TextStyle(color: Colors.white, fontSize: 24),
      ),
    );
  }
}

// 1. GridView.count: jumlah kolom tetap
class GridCount extends StatelessWidget {
  const GridCount({super.key});

  @override
  Widget build(BuildContext context) {
    return GridView.count(
      crossAxisCount: 2,
      crossAxisSpacing: 8,
      mainAxisSpacing: 8,
      padding: const EdgeInsets.all(8),
      children: List.generate(12, (i) => KotakGrid(nomor: i + 1)),
    );
  }
}

// 2. GridView.builder + FixedCrossAxisCount
class GridBuilder extends StatelessWidget {
  const GridBuilder({super.key});

  @override
  Widget build(BuildContext context) {
    return GridView.builder(
      padding: const EdgeInsets.all(8),
      gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 3,
        crossAxisSpacing: 8,
        mainAxisSpacing: 8,
        childAspectRatio: 1.2,
      ),
      itemCount: 60,
      itemBuilder: (context, index) => KotakGrid(nomor: index + 1),
    );
  }
}

// 3. GridView.builder + MaxCrossAxisExtent: kolom menyesuaikan lebar
class GridExtent extends StatelessWidget {
  const GridExtent({super.key});

  @override
  Widget build(BuildContext context) {
    return GridView.builder(
      padding: const EdgeInsets.all(8),
      gridDelegate: const SliverGridDelegateWithMaxCrossAxisExtent(
        maxCrossAxisExtent: 150,
        crossAxisSpacing: 8,
        mainAxisSpacing: 8,
      ),
      itemCount: 60,
      itemBuilder: (context, index) => KotakGrid(nomor: index + 1),
    );
  }
}
```

```dart
  @override
  Widget build(BuildContext context) {
    return DefaultTabController(
      length: 3,
      child: Scaffold(
        appBar: AppBar(
          title: const Text('GridView'),
          bottom: const TabBar(
            tabs: [
              Tab(text: 'count'),
              Tab(text: 'builder'),
              Tab(text: 'extent'),
            ],
          ),
        ),
        body: const TabBarView(
          children: [GridCount(), GridBuilder(), GridExtent()],
        ),
      ),
    );
  }
}

class KotakGrid extends StatelessWidget {
  final int nomor;
  const KotakGrid({super.key, required this.nomor});

  @override
  Widget build(BuildContext context) {
    final warna = Colors.primaries[nomor % Colors.primaries.length];
    return Container(
      decoration: BoxDecoration(
        color: warna,
        borderRadius: BorderRadius.circular(12),
      ),
      alignment: Alignment.center,
      child: Text(
        '$nomor',
        style: const TextStyle(color: Colors.white, fontSize: 24),
      ),
    );
  }
}
// 1. GridView.count: jumlah kolom tetap
class GridCount extends StatelessWidget {
  const GridCount({super.key});

  @override
  Widget build(BuildContext context) {
    return GridView.count(
      crossAxisCount: 2,
      crossAxisSpacing: 8,
      mainAxisSpacing: 8,
      padding: const EdgeInsets.all(8),
      children: List.generate(12, (i) => KotakGrid(nomor: i + 1)),
    );
  }
}

// 2. GridView.builder + FixedCrossAxisCount
class GridBuilder extends StatelessWidget {
  const GridBuilder({super.key});

  @override
  Widget build(BuildContext context) {
    return GridView.builder(
      padding: const EdgeInsets.all(8),
      gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 3,
        crossAxisSpacing: 8,
        mainAxisSpacing: 8,
        childAspectRatio: 1.2,
      ),
      itemCount: 60,
      itemBuilder: (context, index) => KotakGrid(nomor: index + 1),
    );
  }
}

// 3. GridView.builder + MaxCrossAxisExtent: kolom menyesuaikan lebar
class GridExtent extends StatelessWidget {
  const GridExtent({super.key});

  @override
  Widget build(BuildContext context) {
    return GridView.builder(
      padding: const EdgeInsets.all(8),
      gridDelegate: const SliverGridDelegateWithMaxCrossAxisExtent(
        maxCrossAxisExtent: 150,
        crossAxisSpacing: 8,
        mainAxisSpacing: 8,
      ),
      itemCount: 60,
      itemBuilder: (context, index) => KotakGrid(nomor: index + 1),
    );
  }
}
```

```dart
// 2. GridView.builder + FixedCrossAxisCount
class GridBuilder extends StatelessWidget {
  const GridBuilder({super.key});

  @override
  Widget build(BuildContext context) {
    return GridView.builder(
      padding: const EdgeInsets.all(8),
      gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 3,
        crossAxisSpacing: 8,
        mainAxisSpacing: 8,
        childAspectRatio: 1.2,
      ),
      itemCount: 60,
      itemBuilder: (context, index) => KotakGrid(nomor: index + 1),
    );
  }
}

// 3. GridView.builder + MaxCrossAxisExtent: kolom menyesuaikan lebar
class GridExtent extends StatelessWidget {
  const GridExtent({super.key});

  @override
  Widget build(BuildContext context) {
    return GridView.builder(
      padding: const EdgeInsets.all(8),
      gridDelegate: const SliverGridDelegateWithMaxCrossAxisExtent(
        maxCrossAxisExtent: 150,
        crossAxisSpacing: 8,
        mainAxisSpacing: 8,
      ),
      itemCount: 60,
      itemBuilder: (context, index) => KotakGrid(nomor: index + 1),
    );
  }
}
```

![](.gitbook/assets/modul05-02.png)

Simpan dan jalankan file tersebut, lalu amati hasilnya.

**Hasil yang diharapkan:**

- Tab *count*: selalu 2 kolom, selebar apa pun jendela.
- Tab *builder*: selalu 3 kolom dengan kotak agak melebar (rasio 1.2).
- Tab *extent*: jumlah kolom **bertambah** saat jendela dilebarkan dan **berkurang** saat disempitkan (lebar tiap kotak maksimum 150).

### Praktikum 4 - MediaQuery dan LayoutBuilder

**Tujuan:** membaca ukuran layar dan ruang induk, lalu mengubah tata letak menurut breakpoint.

**Langkah pelaksanaan di Android Studio:**

1. Buat file praktikum\_mediaquery\_layoutbuilder.dart di folder lib dan salin kode di bawah.
2. Klik **▶** di samping void main() → **Run 'p4\_mediaquery\_layoutbuilder.dart'** (Chrome).
3. Ubah lebar jendela Chrome sambil mengamati kartu **MediaQuery**; perhatikan lebar, tinggi, dan orientasi ikut berubah.
4. Bandingkan dua kotak **LayoutBuilder**: pada kotak 300 px nilai maxWidth tetap 300 walau jendela diubah, sedangkan MediaQuery mengikuti jendela.
5. Perhatikan **panel adaptif**: Column (tersusun ke bawah) saat lebar &lt; 600 dan Row (berdampingan) saat lebar ≥ 600.
6. Ubah batas 600 pada panel adaptif menjadi 900, **hot reload**, dan amati kapan perubahan terjadi.
7. (Opsional, emulator) putar emulator ke lanskap dan amati nilai orientasi.

**File:** praktikum\_mediaquery\_layoutbuilder.dart

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MyApp());

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      theme: ThemeData(colorSchemeSeed: Colors.indigo),
      home: Scaffold(
        appBar: AppBar(title: const Text('MediaQuery dan LayoutBuilder')),
        body: ListView(
          padding: const EdgeInsets.all(16),
          children: [
            const InfoMediaQuery(),
            const SizedBox(height: 16),
            const Text('LayoutBuilder pada lebar penuh'),
            const SizedBox(height: 6),
            const PetaKelas(),
            const SizedBox(height: 16),
            const Text('LayoutBuilder pada kotak 300 px'),
            const SizedBox(height: 6),
            const SizedBox(width: 300, child: PetaKelas()),
            const SizedBox(height: 16),
            const Text('Panel adaptif (Column < 600, Row >= 600)'),
            const SizedBox(height: 6),
            const PanelAdaptif(),
          ],
        ),
      ),
    );
  }
}

// MediaQuery: informasi layar/jendela dan perangkat
class InfoMediaQuery extends StatelessWidget {
  const InfoMediaQuery({super.key});

  @override
  Widget build(BuildContext context) {
    final ukuran = MediaQuery.sizeOf(context);
    final orientasi = MediaQuery.orientationOf(context);
    final dpr = MediaQuery.devicePixelRatioOf(context);
    final skalaTeks = MediaQuery.textScalerOf(context).scale(1);
    final kecerahan = MediaQuery.platformBrightnessOf(context);

    return Card(
      child: Padding(
        padding: const EdgeInsets.all(16),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text('MediaQuery', style: Theme.of(context).textTheme.titleMedium),
            const SizedBox(height: 8),
            Text('Lebar          : ${ukuran.width.toStringAsFixed(0)}'),
            Text('Tinggi         : ${ukuran.height.toStringAsFixed(0)}'),
            Text('Orientasi      : ${orientasi.name}'),
            Text('Pixel ratio    : ${dpr.toStringAsFixed(2)}'),
            Text('Skala teks     : ${skalaTeks.toStringAsFixed(2)}'),
            Text('Tema perangkat : ${kecerahan.name}'),
          ],
        ),
      ),
    );
  }
}

// LayoutBuilder: ruang yang diberikan induk
class PetaKelas extends StatelessWidget {
  const PetaKelas({super.key});

  String _kelas(double lebar) {
    if (lebar < 600) return 'Compact (ponsel)';
    if (lebar < 840) return 'Medium (tablet kecil)';
    return 'Expanded (tablet besar/desktop)';
  }

  Color _warna(double lebar) {
    if (lebar < 600) return Colors.orange.shade100;
    if (lebar < 840) return Colors.green.shade100;
    return Colors.blue.shade100;
  }

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        final lebar = constraints.maxWidth;
        return Container(
          padding: const EdgeInsets.all(16),
          decoration: BoxDecoration(
            color: _warna(lebar),
            borderRadius: BorderRadius.circular(12),
          ),
          child: Text(
            'maxWidth = ${lebar.toStringAsFixed(0)}\nKelas: ${_kelas(lebar)}',
            style: const TextStyle(color: Colors.black87),
          ),
        );
      },
    );
  }
}

// Tata letak adaptif dengan LayoutBuilder
class PanelAdaptif extends StatelessWidget {
  const PanelAdaptif({super.key});

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        final panel1 = Container(
          height: 120,
          color: Colors.indigo.shade200,
          alignment: Alignment.center,
          child: const Text('Panel 1'),
        );
        final panel2 = Container(
          height: 120,
          color: Colors.teal.shade200,
          alignment: Alignment.center,
          child: const Text('Panel 2'),
        );

        if (constraints.maxWidth < 600) {
          return Column(children: [panel1, const SizedBox(height: 8), panel2]);
        }
        return Row(
          children: [
            Expanded(child: panel1),
            const SizedBox(width: 8),
            Expanded(child: panel2),
          ],
        );
      },
    );
  }
}
```

```dart
            const SizedBox(height: 16),
            const Text('Panel adaptif (Column < 600, Row >= 600)'),
            const SizedBox(height: 6),
            const PanelAdaptif(),
          ],
        ),
      ),
    );
  }
}

// MediaQuery: informasi layar/jendela dan perangkat
class InfoMediaQuery extends StatelessWidget {
  const InfoMediaQuery({super.key});

  @override
  Widget build(BuildContext context) {
    final ukuran = MediaQuery.sizeOf(context);
    final orientasi = MediaQuery.orientationOf(context);
    final dpr = MediaQuery.devicePixelRatioOf(context);
    final skalaTeks = MediaQuery.textScalerOf(context).scale(1);
    final kecerahan = MediaQuery.platformBrightnessOf(context);

    return Card(
      child: Padding(
        padding: const EdgeInsets.all(16),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text('MediaQuery', style: Theme.of(context).textTheme.titleMedium),
            const SizedBox(height: 8),
            Text('Lebar          : ${ukuran.width.toStringAsFixed(0)}'),
            Text('Tinggi         : ${ukuran.height.toStringAsFixed(0)}'),
            Text('Orientasi      : ${orientasi.name}'),
            Text('Pixel ratio    : ${dpr.toStringAsFixed(2)}'),
            Text('Skala teks     : ${skalaTeks.toStringAsFixed(2)}'),
            Text('Tema perangkat : ${kecerahan.name}'),
          ],
        ),
      ),
    );
  }
}

// LayoutBuilder: ruang yang diberikan induk
class PetaKelas extends StatelessWidget {
  const PetaKelas({super.key});

  String _kelas(double lebar) {
    if (lebar < 600) return 'Compact (ponsel)';
    if (lebar < 840) return 'Medium (tablet kecil)';
    return 'Expanded (tablet besar/desktop)';
  }

  Color _warna(double lebar) {
    if (lebar < 600) return Colors.orange.shade100;
    if (lebar < 840) return Colors.green.shade100;
    return Colors.blue.shade100;
  }

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        final lebar = constraints.maxWidth;
        return Container(
          padding: const EdgeInsets.all(16),
          decoration: BoxDecoration(
            color: _warna(lebar),
            borderRadius: BorderRadius.circular(12),
          ),
          child: Text(
            'maxWidth = ${lebar.toStringAsFixed(0)}\nKelas: ${_kelas(lebar)}',
            style: const TextStyle(color: Colors.black87),
          ),
        );
      },
    );
  }
}

// Tata letak adaptif dengan LayoutBuilder
class PanelAdaptif extends StatelessWidget {
  const PanelAdaptif({super.key});

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        final panel1 = Container(
          height: 120,
          color: Colors.indigo.shade200,
          alignment: Alignment.center,
          child: const Text('Panel 1'),
        );
        final panel2 = Container(
          height: 120,
          color: Colors.teal.shade200,
          alignment: Alignment.center,
          child: const Text('Panel 2'),
        );

        if (constraints.maxWidth < 600) {
          return Column(children: [panel1, const SizedBox(height: 8), panel2]);
        }
        return Row(
          children: [
            Expanded(child: panel1),
            const SizedBox(width: 8),
            Expanded(child: panel2),
          ],
        );
      },
    );
  }
}
```

simpan dan jalankan file tersebut, lalu amati hasilnya.

```dart
  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        final lebar = constraints.maxWidth;
        return Container(
          padding: const EdgeInsets.all(16),
          decoration: BoxDecoration(
            color: _warna(lebar),
            borderRadius: BorderRadius.circular(12),
          ),
          child: Text(
            'maxWidth = ${lebar.toStringAsFixed(0)}\nKelas: ${_kelas(lebar)}',
            style: const TextStyle(color: Colors.black87),
          ),
        );
      },
    );
  }
}

// Tata letak adaptif dengan LayoutBuilder
class PanelAdaptif extends StatelessWidget {
  const PanelAdaptif({super.key});

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        final panel1 = Container(
          height: 120,
          color: Colors.indigo.shade200,
          alignment: Alignment.center,
          child: const Text('Panel 1'),
        );
        final panel2 = Container(
          height: 120,
          color: Colors.teal.shade200,
          alignment: Alignment.center,
          child: const Text('Panel 2'),
        );

        if (constraints.maxWidth < 600) {
          return Column(children: [panel1, const SizedBox(height: 8), panel2]);
        }
        return Row(
          children: [
            Expanded(child: panel1),
            const SizedBox(width: 8),
            Expanded(child: panel2),
          ],
        );
      },
    );
  }
}
```

**Hasil yang diharapkan:**

- Kartu **MediaQuery** menampilkan lebar, tinggi, orientasi, *pixel ratio*, skala teks, dan tema perangkat; angkanya berubah saat jendela diubah.
- Kotak **LayoutBuilder** penuh berganti warna (oranye → hijau → biru) dan label kelasnya mengikuti lebar jendela; kotak 300 px **selalu oranye** dengan maxWidth = 300.
- Panel adaptif tersusun vertikal pada layar sempit dan berdampingan pada layar lebar.

![](.gitbook/assets/modul05-03.png)

![](.gitbook/assets/modul05-04.png)

Praktikum 5 - Material 3, ThemeData, dan Mode Gelap

Tujuan: membangun tema dari satu warna benih, menerapkan tema terang/gelap, dan memakai warna dari ColorScheme.

Langkah pelaksanaan di Android Studio:

1. Buat file praktikum\_tema.dart di folder lib dan salin kode di bawah.
2. Klik ▶ di samping void main() → Run 'praktikum\_tema.dart' (Chrome).
3. Pilih mode Terang, Gelap, dan Sistem pada tombol segmen; amati perubahan seluruh warna halaman.
4. Klik tiap warna benih dan amati perubahan palet pada bagian *Peran warna ColorScheme* serta komponen.
5. Ubah Colors.indigo pada \_seed menjadi warna lain, lalu lakukan hot restart.
6. Pada fungsi bangunTema, ubah centerTitle: true menjadi false, atau ubah radius tombol dari 12 menjadi 30, lalu hot reload.
7. Pada widget KotakWarna, ganti salah satu warna dengan nilai tetap Colors.white dan amati bahwa warna tersebut tidak ikut berubah pada mode gelap.

**File:** praktikum\_tema.dart

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MyApp());

// Satu fungsi membangun tema terang maupun gelap dari warna benih
ThemeData bangunTema(Color seed, Brightness kecerahan) {
  final skema = ColorScheme.fromSeed(seedColor: seed, brightness: kecerahan);
  return ThemeData(
    useMaterial3: true,
    colorScheme: skema,
    appBarTheme: AppBarTheme(
      backgroundColor: skema.primaryContainer,
      foregroundColor: skema.onPrimaryContainer,
      centerTitle: true,
    ),
    textTheme: const TextTheme(
      headlineMedium: TextStyle(fontWeight: FontWeight.bold),
    ),
    elevatedButtonTheme: ElevatedButtonThemeData(
      style: ElevatedButton.styleFrom(
        shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(12),
        ),
      ),
    ),
  );
}

class MyApp extends StatefulWidget {
  const MyApp({super.key});

  @override
  State<MyApp> createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  ThemeMode _mode = ThemeMode.system;
  Color _seed = Colors.indigo;

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Tema',
      theme: bangunTema(_seed, Brightness.light),
      darkTheme: bangunTema(_seed, Brightness.dark),
      themeMode: _mode,
      home: HalamanTema(
        mode: _mode,
        seed: _seed,
        onMode: (m) => setState(() => _mode = m),
        onSeed: (c) => setState(() => _seed = c),
      ),
    );
  }
}

class KotakWarna extends StatelessWidget {
  final String label;
  final Color latar;
  final Color teks;
  const KotakWarna({
    super.key,
    required this.label,
    required this.latar,
    required this.teks,
  });

  @override
  Widget build(BuildContext context) {
    return Container(
      width: 140,
      padding: const EdgeInsets.symmetric(vertical: 14),
      alignment: Alignment.center,
      decoration: BoxDecoration(
        color: latar,
        borderRadius: BorderRadius.circular(8),
      ),
      child: Text(label, style: TextStyle(color: teks)),
    );
  }
}

class HalamanTema extends StatelessWidget {
  final ThemeMode mode;
  final Color seed;
  final ValueChanged<ThemeMode> onMode;
  final ValueChanged<Color> onSeed;

  const HalamanTema({
    super.key,
    required this.mode,
    required this.seed,
    required this.onMode,
    required this.onSeed,
  });

  @override
  Widget build(BuildContext context) {
    final tema = Theme.of(context);
    final skema = tema.colorScheme;
    const pilihanWarna = [
      Colors.indigo,
      Colors.teal,
      Colors.deepOrange,
      Colors.pink,
    ];

    return Scaffold(
      appBar: AppBar(title: const Text('Material 3 dan Tema')),
      floatingActionButton: FloatingActionButton(
        onPressed: () {},
        child: const Icon(Icons.add),
      ),
      body: ListView(
        padding: const EdgeInsets.all(16),
        children: [
          Text('Tema aplikasi', style: tema.textTheme.headlineMedium),
          const SizedBox(height: 16),
          Text('Mode tampilan', style: tema.textTheme.titleMedium),
          const SizedBox(height: 8),
          SegmentedButton<ThemeMode>(
            showSelectedIcon: false,
            segments: const <ButtonSegment<ThemeMode>>[
              ButtonSegment(value: ThemeMode.system, label: Text('Sistem')),
              ButtonSegment(value: ThemeMode.light, label: Text('Terang')),
              ButtonSegment(value: ThemeMode.dark, label: Text('Gelap')),
            ],
            selected: {mode},
            onSelectionChanged: (pilihan) => onMode(pilihan.first),
          ),
          const SizedBox(height: 16),
          Text('Warna benih (seed)', style: tema.textTheme.titleMedium),
          const SizedBox(height: 8),
          Wrap(
            spacing: 12,
            children: [
              for (final c in pilihanWarna)
                InkWell(
                  customBorder: const CircleBorder(),
                  onTap: () => onSeed(c),
                  child: CircleAvatar(
                    backgroundColor: c,
                    child: seed == c
                        ? const Icon(Icons.check, color: Colors.white)
                        : null,
                  ),
                ),
            ],
          ),
          const Divider(height: 32),
          Text('Peran warna ColorScheme', style: tema.textTheme.titleMedium),
          const SizedBox(height: 8),
          Wrap(
            spacing: 8,
            runSpacing: 8,
            children: [
              KotakWarna(
                label: 'primary',
                latar: skema.primary,
                teks: skema.onPrimary,
              ),
              KotakWarna(
                label: 'primaryContainer',
                latar: skema.primaryContainer,
                teks: skema.onPrimaryContainer,
              ),
              KotakWarna(
                label: 'secondary',
                latar: skema.secondary,
                teks: skema.onSecondary,
              ),
              KotakWarna(
                label: 'tertiary',
                latar: skema.tertiary,
                teks: skema.onTertiary,
              ),
              KotakWarna(
                label: 'error',
                latar: skema.error,
                teks: skema.onError,
              ),
              KotakWarna(
                label: 'surface',
                latar: skema.surfaceContainerHighest,
                teks: skema.onSurface,
              ),
            ],
          ),
          const Divider(height: 32),
          Text('Komponen', style: tema.textTheme.titleMedium),
          const SizedBox(height: 8),
          Wrap(
            spacing: 8,
            runSpacing: 8,
            children: [
              ElevatedButton(onPressed: () {}, child: const Text('Elevated')),
              FilledButton(onPressed: () {}, child: const Text('Filled')),
              OutlinedButton(onPressed: () {}, child: const Text('Outlined')),
              TextButton(onPressed: () {}, child: const Text('Text')),
            ],
          ),
          const SizedBox(height: 16),
          const TextField(
            decoration: InputDecoration(
              border: OutlineInputBorder(),
              labelText: 'Nama',
              prefixIcon: Icon(Icons.person),
            ),
          ),
          const SizedBox(height: 16),
          Card(
            child: ListTile(
              leading: const Icon(Icons.dark_mode),
              title: const Text('Card dan ListTile'),
              subtitle: Text('Mode saat ini: ${mode.name}'),
            ),
          ),
        ],
      ),
    );
  }
}
```

```dart
class KotakWarna extends StatelessWidget {
  final String label;
  final Color latar;
  final Color teks;
  const KotakWarna({
    super.key,
    required this.label,
    required this.latar,
    required this.teks,
  });

  @override
  Widget build(BuildContext context) {
    return Container(
      width: 140,
      padding: const EdgeInsets.symmetric(vertical: 14),
      alignment: Alignment.center,
      decoration: BoxDecoration(
        color: latar,
        borderRadius: BorderRadius.circular(8),
      ),
      child: Text(label, style: TextStyle(color: teks)),
    );
  }
}

class HalamanTema extends StatelessWidget {
  final ThemeMode mode;
  final Color seed;
  final ValueChanged<ThemeMode> onMode;
  final ValueChanged<Color> onSeed;

  const HalamanTema({
    super.key,
    required this.mode,
    required this.seed,
    required this.onMode,
    required this.onSeed,
  });

  @override
  Widget build(BuildContext context) {
    final tema = Theme.of(context);
    final skema = tema.colorScheme;
    const pilihanWarna = [
      Colors.indigo,
      Colors.teal,
      Colors.deepOrange,
      Colors.pink,
    ];

    return Scaffold(
      appBar: AppBar(title: const Text('Material 3 dan Tema')),
      floatingActionButton: FloatingActionButton(
        onPressed: () {},
        child: const Icon(Icons.add),
      ),
      body: ListView(
        padding: const EdgeInsets.all(16),
        children: [
          Text('Tema aplikasi', style: tema.textTheme.headlineMedium),
          const SizedBox(height: 16),
          Text('Mode tampilan', style: tema.textTheme.titleMedium),
          const SizedBox(height: 8),
          SegmentedButton<ThemeMode>(
            showSelectedIcon: false,
            segments: const <ButtonSegment<ThemeMode>>[
              ButtonSegment(value: ThemeMode.system, label: Text('Sistem')),
              ButtonSegment(value: ThemeMode.light, label: Text('Terang')),
              ButtonSegment(value: ThemeMode.dark, label: Text('Gelap')),
            ],
            selected: {mode},
            onSelectionChanged: (pilihan) => onMode(pilihan.first),
          ),
          const SizedBox(height: 16),
          Text('Warna benih (seed)', style: tema.textTheme.titleMedium),
          const SizedBox(height: 8),
          Wrap(
            spacing: 12,
            children: [
              for (final c in pilihanWarna)
                InkWell(
                  customBorder: const CircleBorder(),
                  onTap: () => onSeed(c),
                  child: CircleAvatar(
                    backgroundColor: c,
                    child: seed == c
                        ? const Icon(Icons.check, color: Colors.white)
                        : null,
                  ),
                ),
            ],
          ),
          const Divider(height: 32),
          Text('Peran warna ColorScheme', style: tema.textTheme.titleMedium),
          const SizedBox(height: 8),
          Wrap(
            spacing: 8,
            runSpacing: 8,
            children: [
              KotakWarna(
                label: 'primary',
                latar: skema.primary,
                teks: skema.onPrimary,
              ),
              KotakWarna(
                label: 'primaryContainer',
                latar: skema.primaryContainer,
                teks: skema.onPrimaryContainer,
              ),
              KotakWarna(
                label: 'secondary',
                latar: skema.secondary,
                teks: skema.onSecondary,
              ),
              KotakWarna(
                label: 'tertiary',
                latar: skema.tertiary,
                teks: skema.onTertiary,
              ),
              KotakWarna(
                label: 'error',
                latar: skema.error,
                teks: skema.onError,
              ),
              KotakWarna(
                label: 'surface',
                latar: skema.surfaceContainerHighest,
                teks: skema.onSurface,
              ),
            ],
          ),
          const Divider(height: 32),
          Text('Komponen', style: tema.textTheme.titleMedium),
          const SizedBox(height: 8),
          Wrap(
            spacing: 8,
            runSpacing: 8,
            children: [
              ElevatedButton(onPressed: () {}, child: const Text('Elevated')),
              FilledButton(onPressed: () {}, child: const Text('Filled')),
              OutlinedButton(onPressed: () {}, child: const Text('Outlined')),
              TextButton(onPressed: () {}, child: const Text('Text')),
            ],
          ),
          const SizedBox(height: 16),
          const TextField(
            decoration: InputDecoration(
              border: OutlineInputBorder(),
              labelText: 'Nama',
              prefixIcon: Icon(Icons.person),
            ),
          ),
          const SizedBox(height: 16),
          Card(
            child: ListTile(
              leading: const Icon(Icons.dark_mode),
              title: const Text('Card dan ListTile'),
              subtitle: Text('Mode saat ini: ${mode.name}'),
            ),
          ),
        ],
      ),
    );
  }
}
```

```dart
      body: ListView(
        padding: const EdgeInsets.all(16),
        children: [
          Text('Tema aplikasi', style: tema.textTheme.headlineMedium),
          const SizedBox(height: 16),
          Text('Mode tampilan', style: tema.textTheme.titleMedium),
          const SizedBox(height: 8),
          SegmentedButton<ThemeMode>(
            showSelectedIcon: false,
            segments: const <ButtonSegment<ThemeMode>>[
      ButtonSegment(value: ThemeMode.system, label: Text('Sistem')),
      ButtonSegment(value: ThemeMode.light, label: Text('Terang')),
      ButtonSegment(value: ThemeMode.dark, label: Text('Gelap')),
            ],
            selected: {mode},
            onSelectionChanged: (pilihan) => onMode(pilihan.first),
          ),
          const SizedBox(height: 16),
      Text('Warna benih (seed)', style: tema.textTheme.titleMedium),
          const SizedBox(height: 8),
          Wrap(
            spacing: 12,
            children: [
              for (final c in pilihanWarna)
                InkWell(
                  customBorder: const CircleBorder(),
                  onTap: () => onSeed(c),
                  child: CircleAvatar(
                    backgroundColor: c,
                    child: seed == c
                     ? const Icon(Icons.check, color: Colors.white)
                        : null,
                  ),
                ),
            ],
          ),
          const Divider(height: 32),
 Text('Peran warna ColorScheme', style: tema.textTheme.titleMedium),
          const SizedBox(height: 8),
          Wrap(
            spacing: 8,
            runSpacing: 8,
            children: [
              KotakWarna(
                label: 'primary',
                latar: skema.primary,
                teks: skema.onPrimary,
              ),
              KotakWarna(
                label: 'primaryContainer',
                latar: skema.primaryContainer,
                teks: skema.onPrimaryContainer,
              ),
              KotakWarna(
                label: 'secondary',
                latar: skema.secondary,
                teks: skema.onSecondary,
              ),
              KotakWarna(
                label: 'tertiary',
                latar: skema.tertiary,
                teks: skema.onTertiary,
              ),
              KotakWarna(
                label: 'error',
                latar: skema.error,
                teks: skema.onError,
              ),
              KotakWarna(
                label: 'surface',
                latar: skema.surfaceContainerHighest,
                teks: skema.onSurface,
              ),
            ],
          ),
          const Divider(height: 32),
          Text('Komponen', style: tema.textTheme.titleMedium),
          const SizedBox(height: 8),
          Wrap(
            spacing: 8,
            runSpacing: 8,
            children: [
              ElevatedButton(onPressed: () {}, child: const Text('Elevated')),
              FilledButton(onPressed: () {}, child: const Text('Filled')),
              OutlinedButton(onPressed: () {}, child: const Text('Outlined')),
              TextButton(onPressed: () {}, child: const Text('Text')),
            ],
          ),
          const SizedBox(height: 16),
          const TextField(
            decoration: InputDecoration(
              border: OutlineInputBorder(),
              labelText: 'Nama',
              prefixIcon: Icon(Icons.person),
            ),
          ),
          const SizedBox(height: 16),
          Card(
            child: ListTile(
              leading: const Icon(Icons.dark_mode),
              title: const Text('Card dan ListTile'),
              subtitle: Text('Mode saat ini: ${mode.name}'),
            ),
          ),
        ],
      ),
    );
  }
}
```

```dart
              KotakWarna(
                label: 'secondary',
                latar: skema.secondary,
                teks: skema.onSecondary,
              ),
              KotakWarna(
                label: 'tertiary',
                latar: skema.tertiary,
                teks: skema.onTertiary,
              ),
              KotakWarna(
                label: 'error',
                latar: skema.error,
                teks: skema.onError,
              ),
              KotakWarna(
                label: 'surface',
                latar: skema.surfaceContainerHighest,
                teks: skema.onSurface,
              ),
            ],
          ),
          const Divider(height: 32),
          Text('Komponen', style: tema.textTheme.titleMedium),
          const SizedBox(height: 8),
          Wrap(
            spacing: 8,
            runSpacing: 8,
            children: [
     ElevatedButton(onPressed: () {}, child: const Text('Elevated')),
     FilledButton(onPressed: () {}, child: const Text('Filled')),
     OutlinedButton(onPressed: () {}, child: const Text('Outlined')),
     TextButton(onPressed: () {}, child: const Text('Text')),
            ],
          ),
          const SizedBox(height: 16),
          const TextField(
            decoration: InputDecoration(
              border: OutlineInputBorder(),
              labelText: 'Nama',
              prefixIcon: Icon(Icons.person),
            ),
          ),
          const SizedBox(height: 16),
          Card(
            child: ListTile(
              leading: const Icon(Icons.dark_mode),
              title: const Text('Card dan ListTile'),
              subtitle: Text('Mode saat ini: ${mode.name}'),
            ),
          ),
        ],
      ), ); }   }
```

Simpan file tersebut, jalankan dan amati hasilnya.

Hasil yang diharapkan:

- Halaman berganti terang/gelap saat mode dipilih; mode *Sistem* mengikuti tema perangkat/peramban.
- Memilih warna benih mengubah seluruh palet (AppBar, tombol, kotak peran warna) secara serasi.
- Kotak peran warna selalu terbaca karena pasangan warna xxx dan onXxx dari ColorScheme.

![](.gitbook/assets/modul05-05.png)

![](.gitbook/assets/modul05-06.png)

## Post-Test

1. Jelaskan perbedaan ListView(children: [...]) dan ListView.builder. Kapan sebaiknya memakai builder?
2. Mengapa ListView di dalam Column dapat menimbulkan error unbounded height? Sebutkan cara memperbaikinya.
3. Jelaskan perbedaan SliverGridDelegateWithFixedCrossAxisCount dan SliverGridDelegateWithMaxCrossAxisExtent. Mana yang lebih cocok untuk tampilan responsif?

## Latihan/Tugas

Buat aplikasi "Katalog Responsif" bertema pilihan sendiri (mis. katalog buku, tempat wisata, jadwal kuliah, atau daftar dosen) dengan ketentuan:

1. Minimal 12 data yang dimodelkan dengan class.
2. Tiga layout berdasarkan lebar: &lt; 600 (ListView), 600–839 (GridView), dan ≥ 840 (menu samping + GridView memakai Expanded).
3. Memakai LayoutBuilder untuk memilih layout dan MediaQuery minimal untuk satu keperluan (mis. menampilkan lebar atau menentukan jumlah kolom).
4. Tema Material 3 buatan sendiri dengan ColorScheme.fromSeed, minimal satu *component theme* (mis. appBarTheme), dan mode gelap dengan tombol ganti mode.
5. Seluruh warna komponen diambil dari colorScheme (tanpa Colors.white/Colors.black pada komponen utama).
6. Tidak ada *overflow* (garis kuning-hitam) pada lebar ±400, ±700, dan ±1200 piksel.
