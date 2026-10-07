# Modul 7: Manajemen State

## Maksud dan Tujuan

1. Menjelaskan perbedaan *ephemeral state* (state lokal) dan *app state* (state aplikasi).
2. Memperbarui tampilan dengan setState() dan menerapkan teknik *lifting state up*.
3. Menjelaskan cara kerja InheritedWidget dan membuat sendiri pembawa data sederhana.
4. Membuat kelas model berbasis ChangeNotifier dan memanggil notifyListeners().
5. Menyediakan state dengan ChangeNotifierProvider serta membacanya dengan context.watch, context.read, context.select, dan Consumer.
6. Membangun aplikasi daftar tugas (to-do list) dengan state terpusat yang dipakai oleh lebih dari satu halaman.
7. Menulis unit test sederhana untuk kelas pengelola state.

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

### Apa itu State?

**State** adalah data yang dapat berubah selama aplikasi berjalan dan memengaruhi tampilan. Contohnya nilai counter, isi keranjang belanja, status login, atau daftar tugas. Flutter bersifat deklaratif: tampilan adalah hasil dari state.

```dart
  UI = f(state)
```

Artinya, ketika state berubah, kita tidak mengubah widget satu per satu. Kita mengubah state, lalu Flutter membangun ulang tampilan sesuai state yang baru.

State dibedakan menjadi dua jenis:

| **Jenis** | **Ciri** | **Contoh** | **Pendekatan yang cocok** |
|---|---|---|---|
| *Ephemeral state* (state lokal) | Hanya dipakai satu widget, tidak perlu dibagikan | Tab yang sedang aktif, isi kolom teks, animasi | setState() |
| *App state* (state aplikasi) | Dipakai banyak widget atau halaman, perlu dipertahankan | Daftar tugas, status login, keranjang belanja | InheritedWidget, Provider, Riverpod, dan sejenisnya |

### setState dan Lifting State Up

setState() memberi tahu Flutter bahwa state sebuah StatefulWidget telah berubah sehingga method build() perlu dipanggil ulang.

```dart
class _HalamanState extends State<Halaman> {
  int _jumlah = 0;
 
  void _tambah() {
    setState(() {
      _jumlah++;      // ubah state di dalam setState
    });
  }
 
  @override
  Widget build(BuildContext context) => Text('$_jumlah');
}
```

Hal penting tentang setState():

- Perubahan state harus dilakukan **di dalam** setState() (atau diikuti pemanggilannya). Tanpa itu, tampilan tidak diperbarui.
- setState() membangun ulang **seluruh** widget State tersebut beserta anak-anaknya.
- setState() hanya mengenai widget pemilik state. Widget lain yang tidak berada di bawahnya tidak ikut diperbarui.

**Lifting state up** adalah teknik memindahkan state ke *ancestor* (widget induk) terdekat yang menaungi semua widget yang membutuhkannya. Data dikirim ke bawah lewat parameter, dan perubahan dikirim ke atas lewat *callback*.

![](.gitbook/assets/modul07-01.png)

Kelemahan teknik ini muncul pada pohon widget yang dalam: data harus diteruskan melalui banyak lapisan widget yang sebenarnya tidak memerlukannya. Masalah ini dikenal sebagai *prop drilling*.

### InheritedWidget

InheritedWidget adalah widget bawaan Flutter yang membawa data ke seluruh widget di bawahnya tanpa harus meneruskannya lewat konstruktor. Banyak fitur Flutter dibangun di atasnya, misalnya Theme.of(context), MediaQuery.of(context), dan Navigator.of(context).

Cara kerja:

1. InheritedWidget ditaruh di atas pohon widget dan menyimpan data.
2. Widget di bawahnya mengambil data memakai context.dependOnInheritedWidgetOfExactType&lt;T&gt;(). Pemanggilan ini sekaligus mendaftarkan widget tersebut sebagai **dependen**.
3. Ketika InheritedWidget dibangun ulang dengan data baru, method updateShouldNotify() menentukan apakah dependen perlu dibangun ulang.

```dart
class CounterScope extends InheritedWidget {
  final int jumlah;
   const CounterScope({super.key, required this.jumlah, required super.child});
 
  static CounterScope of(BuildContext context) {
    final scope = context.dependOnInheritedWidgetOfExactType<CounterScope>();
    assert(scope != null, 'CounterScope tidak ditemukan');
    return scope!;
  }
   @override
  bool updateShouldNotify(CounterScope oldWidget) => jumlah != oldWidget.jumlah;
}
```

InheritedWidget bersifat **tidak dapat diubah** (*immutable*), sehingga tidak bisa menyimpan state yang berubah. Karena itu ia hampir selalu dipasangkan dengan StatefulWidget yang memegang state dan membuat ulang InheritedWidget dengan data terbaru.

Penulisan kode yang berulang inilah yang melatarbelakangi lahirnya paket seperti Provider: Provider pada dasarnya membungkus InheritedWidget agar lebih mudah dipakai.

### Provider

**Provider** adalah paket yang dikembangkan komunitas dan didokumentasikan secara luas oleh tim Flutter sebagai cara sederhana untuk mengelola app state. Tiga komponen utamanya:

| **Komponen** | **Peran** |
|---|---|
| ChangeNotifier | Kelas yang menyimpan state dan logika, serta memberi tahu pendengar lewat notifyListeners() |
| ChangeNotifierProvider | Menyediakan satu objek ChangeNotifier ke seluruh widget di bawahnya |
| context.watch, context.read, context.select, Consumer | Cara widget mengambil objek tersebut |

Alur kerjanya:

![](.gitbook/assets/modul07-02.png)

Contoh model:

```dart
class CounterModel extends ChangeNotifier {
  int _jumlah = 0;
  int get jumlah => _jumlah;     // UI hanya boleh MEMBACA
 
  void tambah() {
    _jumlah++;
    notifyListeners();           // beri tahu semua pendengar
  }
}
```

Cara mengambil objek dari Provider:

| **Cara** | **Mendengarkan perubahan?** | **Dipakai di mana** |
|---|---|---|
| `context.watch<T>()` | Ya, widget dibangun ulang setiap `notifyListeners()` | Di dalam `build()` |
| `context.read<T>()` | Tidak | Di dalam *callback* seperti `onPressed` |
| `context.select<T, R>((m) => ...)` | Ya, tetapi hanya jika nilai yang dipilih berubah | Di dalam `build()`, untuk menghemat pembangunan ulang |
| `Consumer<T>` | Ya, hanya bagian di dalam `builder` yang dibangun ulang | Di dalam `build()`, membatasi area pembangunan ulang |

{% hint style="info" %}
**Aturan praktis:** `watch` atau `select` di dalam `build()`, `read` di dalam callback. Memakai `context.watch` di dalam `onPressed` akan menghasilkan error.
{% endhint %}

Beberapa prinsip penting:

- Letakkan `ChangeNotifierProvider` **di atas** semua widget yang membutuhkannya. Agar bisa dipakai lintas halaman, letakkan di atas `MaterialApp`.
- State dibuat **privat** (`_daftar`) dan hanya diubah lewat method di model. UI tidak boleh mengubah data secara langsung.
- Setiap kali state berubah, panggil `notifyListeners()`. Lupa memanggilnya adalah kesalahan paling umum.

Pisahkan **model data** (misalnya `Tugas`), **pengelola state** (`TugasProvider`), dan **tampilan** (halaman dan widget).

## Pre-Test

1. Apa yang dimaksud dengan *state* pada aplikasi Flutter? Berikan dua contoh.
2. Apa perbedaan *ephemeral state* dan *app state*?
3. Apa yang terjadi jika kita mengubah variabel state tanpa memanggil setState()?
4. Apa itu *lifting state up* dan masalah apa yang muncul bila pohon widget sangat dalam?

## Praktikum

### Praktikum 1: setState dan Lifting State Up

**Tujuan:** memahami bahwa setState() membangun ulang seluruh widget pemilik state, serta menerapkan *lifting state up*.

1. **Langkah 1.** Buat proyek Flutter baru dengan **Project name** praktikum7\_setstate, **Project type** Application, dan platform **Android** dicentang. Tunggu hingga *indexing* dan *pub get* selesai.
2. **Langkah 2. Ganti isi lib/main.dart.** kosongkan isinya, lalu tempel kode berikut. State \_jumlah berada di HalamanCounter. Widget TampilanAngka dan PanelTombol tidak memiliki state; data dikirim ke bawah lewat parameter dan perubahan dikirim ke atas lewat callback.

```dart
import 'package:flutter/material.dart';
 
void main() => runApp(const MyApp());
 
class MyApp extends StatelessWidget {
  const MyApp({super.key});
 
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Praktikum setState',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(colorSchemeSeed: Colors.indigo, useMaterial3: true),
      home: const HalamanCounter(),
    );
  }
}
 
class HalamanCounter extends StatefulWidget {
  const HalamanCounter({super.key});
 
  @override
  State<HalamanCounter> createState() => _HalamanCounterState();
}
 
class _HalamanCounterState extends State<HalamanCounter> {
  // State milik halaman ini
  int _jumlah = 0;
 
  void _tambah() {
    setState(() => _jumlah++);
  }
 
  void _kurang() {
    if (_jumlah == 0) return;
    setState(() => _jumlah--);
  }
 
  void _reset() {
    setState(() => _jumlah = 0);
  }
 
  @override
  Widget build(BuildContext context) {
    debugPrint('build: HalamanCounter');
    return Scaffold(
      appBar: AppBar(title: const Text('Counter dengan setState')),
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            TampilanAngka(jumlah: _jumlah),
            const SizedBox(height: 24),
            PanelTombol(
              onTambah: _tambah,
              onKurang: _kurang,
              onReset: _reset,
            ),
          ],
        ),
      ),
    );
  }
}
 
class TampilanAngka extends StatelessWidget {
  final int jumlah;
 
  const TampilanAngka({super.key, required this.jumlah});
 
  @override
  Widget build(BuildContext context) {
    debugPrint('build: TampilanAngka');
    return Text(
      '$jumlah',
      style: const TextStyle(fontSize: 72, fontWeight: FontWeight.bold),
    );
  }
}
 
class PanelTombol extends StatelessWidget {
  final VoidCallback onTambah;
  final VoidCallback onKurang;
  final VoidCallback onReset;
 
  const PanelTombol({
    super.key,
    required this.onTambah,
    required this.onKurang,
    required this.onReset,
  });
 
  @override
  Widget build(BuildContext context) {
    debugPrint('build: PanelTombol');
    return Row(
      mainAxisSize: MainAxisSize.min,
      children: [
        IconButton.filled(
          onPressed: onKurang,
          icon: const Icon(Icons.remove),
        ),
        const SizedBox(width: 12),
        IconButton.filledTonal(
          onPressed: onReset,
          icon: const Icon(Icons.refresh),
        ),
        const SizedBox(width: 12),
        IconButton.filled(
          onPressed: onTambah,
          icon: const Icon(Icons.add),
        ),
      ],
    );
  }
}
```

3. **Langkah 3. Jalankan aplikasi.** Pilih emulator pada device selector, lalu klik tombol **Run**. Tekan tombol **+**, **-**, dan **reset**, lalu amati angka di layar.
4. **Langkah 4. Amati pembangunan ulang widget.**
1. Buka panel **Run** di bagian bawah Android Studio.
2. Tekan tombol **+** satu kali.
3. Catat baris `build: ...` yang muncul pada panel Run.

Seluruh widget (`HalamanCounter`, `TampilanAngka`, dan `PanelTombol`) dibangun ulang, meskipun `PanelTombol` sebenarnya tidak berubah. Itulah sifat `setState()`: membangun ulang widget pemilik state beserta seluruh anaknya.

### Praktikum 2: Membuat InheritedWidget Sendiri

**Tujuan:** memahami cara InheritedWidget membagikan data ke widget yang jauh di bawahnya dan ke halaman lain, tanpa meneruskan lewat konstruktor.

1. **Langkah 1.**  Buat proyek Flutter baru dengan **Project name** praktikum7\_inherited, Application, platform Android. Tunggu hingga proses awal selesai.
2. **Langkah 2. Ganti isi lib/main.dart: bagian pembawa data dan pemegang state.** Kosongkan main.dart, lalu tempel kode berikut. Perhatikan bahwa CounterHolder (pemegang state) ditempatkan **di atas** MyApp, sehingga semua halaman dapat mengaksesnya.

```dart
import 'package:flutter/material.dart';
 
void main() => runApp(const CounterHolder(child: MyApp()));
 
// ---------- 1) InheritedWidget: pembawa data ----------
class CounterScope extends InheritedWidget {
  final int jumlah;
  final VoidCallback tambah;
 
  const CounterScope({
    super.key,
    required this.jumlah,
    required this.tambah,
    required super.child,
  });
 
  static CounterScope of(BuildContext context) {
    final scope = context.dependOnInheritedWidgetOfExactType<CounterScope>();
    assert(scope != null, 'CounterScope tidak ditemukan di atas widget ini');
    return scope!;
  }
 
  // Dependen hanya dibangun ulang jika nilai jumlah berubah
  @override
  bool updateShouldNotify(CounterScope oldWidget) =>
      jumlah != oldWidget.jumlah;
}
 
// ---------- 2) StatefulWidget: pemegang state ----------
class CounterHolder extends StatefulWidget {
  final Widget child;
 
  const CounterHolder({super.key, required this.child});
 
  @override
  State<CounterHolder> createState() => _CounterHolderState();
}
 
class _CounterHolderState extends State<CounterHolder> {
  int _jumlah = 0;
 
  void _tambah() => setState(() => _jumlah++);
 
  @override
  Widget build(BuildContext context) {
    return CounterScope(
      jumlah: _jumlah,
      tambah: _tambah,
      child: widget.child,
    );
  }
}
```

3. **Langkah 3. Tambahkan aplikasi dan dua halaman** di bawah kode sebelumnya, pada file yang sama.

```dart
class MyApp extends StatelessWidget {
  const MyApp({super.key});
 
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Praktikum InheritedWidget',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(colorSchemeSeed: Colors.teal, useMaterial3: true),
      home: const HalamanA(),
    );
  }
}
 
class HalamanA extends StatelessWidget {
  const HalamanA({super.key});
 
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Halaman A')),
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            const Text('Nilai counter bersama:'),
            const TampilJumlah(),
            const SizedBox(height: 16),
            ElevatedButton(
              onPressed: () => Navigator.push(
                context,
                MaterialPageRoute(builder: (_) => const HalamanB()),
              ),
              child: const Text('Ke Halaman B'),
            ),
          ],
        ),
      ),
      floatingActionButton: const TombolTambah(),
    );
  }
}
 
class HalamanB extends StatelessWidget {
  const HalamanB({super.key});
 
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Halaman B')),
      body: const Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Text('Halaman B membaca data yang sama:'),
            TampilJumlah(),
          ],
        ),
      ),
      floatingActionButton: const TombolTambah(),
    );
  }
}
 
// Widget kecil ini TIDAK menerima parameter apa pun,
// data diambil langsung dari InheritedWidget
class TampilJumlah extends StatelessWidget {
  const TampilJumlah({super.key});
 
  @override
  Widget build(BuildContext context) {
    debugPrint('build: TampilJumlah');
    final jumlah = CounterScope.of(context).jumlah;
    return Text(
      '$jumlah',
      style: const TextStyle(fontSize: 64, fontWeight: FontWeight.bold),
    );
  }
}
 
class TombolTambah extends StatelessWidget {
  const TombolTambah({super.key});
 
  @override
  Widget build(BuildContext context) {
    return FloatingActionButton(
      onPressed: CounterScope.of(context).tambah,
      child: const Icon(Icons.add),
    );
  }
}
```

4. **Langkah 4. Jalankan dan uji.**
1. Tekan tombol **+** di Halaman A beberapa kali.
2. Tekan **Ke Halaman B**. Apakah nilainya sama?
3. Tekan **+** di Halaman B, lalu kembali ke Halaman A. Apakah nilainya ikut berubah?
4. Perhatikan panel **Run**: widget apa saja yang dicetak build: ketika tombol ditekan?

**Langkah 5. Eksperimen: salah menempatkan InheritedWidget.**

1. Ubah baris main() menjadi:

```dart
void main() => runApp(const MyApp());
```

2. Pada MyApp, ubah home: const HalamanA() menjadi:

```dart
      home: const CounterHolder(child: HalamanA()),
```

3. Lakukan **Hot Restart**, lalu buka Halaman B. Pesan apa yang muncul, dan mengapa? (Petunjuk: halaman yang dibuka dengan Navigator.push berada di luar CounterHolder.)
4. Kembalikan kode ke bentuk semula.

### Praktikum 3: Proyek Aplikasi Daftar Tugas (To-Do List) dengan State Terpusat

**Tujuan:** membangun aplikasi to-do list yang seluruh datanya dikelola satu TugasProvider dan dipakai oleh lebih dari satu halaman.

**Fitur aplikasi:**

- menambah tugas (lewat *bottom sheet* berisi form dengan validasi),
- menandai tugas selesai atau belum,
- menghapus tugas dengan geser, dilengkapi tombol **Urungkan**,
- menyaring daftar: Semua, Aktif, Selesai,
- menghapus semua tugas yang sudah selesai,
- halaman statistik yang membaca state yang sama.

**Arsitektur:**

```text
  lib/
   ├─ main.dart                     (menyediakan TugasProvider)
   ├─ models/
   │   └─ tugas.dart                (model data, immutable)
   ├─ providers/
   │   └─ tugas_provider.dart       (state terpusat + logika)
   ├─ pages/
   │   ├─ daftar_tugas_page.dart    (halaman utama)
   │   └─ statistik_page.dart       (halaman kedua)
   └─ widgets/
       ├─ tugas_tile.dart           (satu baris tugas)
       └─ form_tambah_tugas.dart    (form tambah tugas)
```

1. **Langkah 1. Buat proyek baru dan pasang paket di Android Studio.**
- Buat proyek Flutter baru dengan **Project name** todo\_app, Application, platform Android.
- Tambahkan provider pada pubspec.yaml seperti pada Praktikum 3 Langkah 2, lalu klik **Pub get**.
- Pada panel Project, buka folder test, klik kanan file widget\_test.dart, pilih **Delete**. File bawaan ini mengacu pada MyApp yang tidak kita pakai dan akan menimbulkan error.
2. **Langkah 2. Siapkan struktur folder.**
- Klik kanan folder lib &gt; **New &gt; Directory**, buat empat folder: models, providers, pages, dan widgets.
3. **Langkah 3. Buat model lib/models/tugas.dart.**
- Klik kanan folder models &gt; **New &gt; Dart File** &gt; tugas. Model dibuat *immutable*: untuk mengubah nilai, kita membuat salinan baru dengan copyWith.

```dart
  final String id;
  final String judul;
  final bool selesai;
  final DateTime dibuat;
 
  const Tugas({
    required this.id,
    required this.judul,
    this.selesai = false,
    required this.dibuat,
  });
 
  Tugas copyWith({String? judul, bool? selesai}) {
    return Tugas(
      id: id,
      judul: judul ?? this.judul,
      selesai: selesai ?? this.selesai,
      dibuat: dibuat,
    );
  }
}
```

4. **Langkah 4. Buat pengelola state lib/providers/tugas\_provider.dart.**
- Klik kanan folder providers &gt; **New &gt; Dart File** &gt; tugas\_provider. Seluruh logika aplikasi berada di sini. Perhatikan bahwa daftar \_daftar bersifat privat dan UI hanya dapat membacanya lewat getter.

```dart
import 'dart:collection';
 
import 'package:flutter/foundation.dart';
 
import '../models/tugas.dart';
 
enum FilterTugas { semua, aktif, selesai }
 
class TugasProvider extends ChangeNotifier {
  // State PRIVAT: hanya bisa diubah lewat method di kelas ini
  final List<Tugas> _daftar = [
    Tugas(id: '1', judul: 'Baca Modul 7 sampai selesai', dibuat: DateTime.now()),
    Tugas(id: '2', judul: 'Kerjakan Praktikum 4', dibuat: DateTime.now()),
    Tugas(id: '3', judul: 'Kumpulkan laporan', dibuat: DateTime.now()),
  ];
 
  FilterTugas _filter = FilterTugas.semua;
  Tugas? _terakhirDihapus;
  int _indeksTerakhirDihapus = 0;
 
  // ---------- Getter (data turunan, hanya baca) ----------
  UnmodifiableListView<Tugas> get semuaTugas => UnmodifiableListView(_daftar);
 
  FilterTugas get filter => _filter;
 
  List<Tugas> get tugasTampil {
    switch (_filter) {
      case FilterTugas.semua:
        return List.unmodifiable(_daftar);
      case FilterTugas.aktif:
        return _daftar.where((t) => !t.selesai).toList();
      case FilterTugas.selesai:
        return _daftar.where((t) => t.selesai).toList();
    }
  }
 
  int get total => _daftar.length;
  int get jumlahSelesai => _daftar.where((t) => t.selesai).length;
  int get jumlahAktif => total - jumlahSelesai;
  double get persenSelesai => total == 0 ? 0.0 : jumlahSelesai / total;
 
  // ---------- Aksi (mengubah state) ----------
  void tambah(String judul) {
    final bersih = judul.trim();
    if (bersih.isEmpty) return;
 
    _daftar.insert(
      0,
      Tugas(
        id: DateTime.now().microsecondsSinceEpoch.toString(),
        judul: bersih,
        dibuat: DateTime.now(),
      ),
    );
    notifyListeners();
  }
 
  void ubahStatus(String id) {
    final i = _daftar.indexWhere((t) => t.id == id);
    if (i == -1) return;
 
    _daftar[i] = _daftar[i].copyWith(selesai: !_daftar[i].selesai);
    notifyListeners();
  }
 
  void hapus(String id) {
    final i = _daftar.indexWhere((t) => t.id == id);
    if (i == -1) return;
 
    _terakhirDihapus = _daftar[i];
    _indeksTerakhirDihapus = i;
    _daftar.removeAt(i);
    notifyListeners();
  }
 
  void urungkanHapus() {
    final tugas = _terakhirDihapus;
    if (tugas == null) return;
 
    final posisi = _indeksTerakhirDihapus > _daftar.length
        ? _daftar.length
        : _indeksTerakhirDihapus;
    _daftar.insert(posisi, tugas);
    _terakhirDihapus = null;
    notifyListeners();
  }
 
  void hapusSelesai() {
    _daftar.removeWhere((t) => t.selesai);
    notifyListeners();
  }
 
  void setFilter(FilterTugas filterBaru) {
    if (_filter == filterBaru) return;
    _filter = filterBaru;
    notifyListeners();
  }
}
```

5. **Langkah 5. Ganti isi lib/main.dart.**

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
 
import 'pages/daftar_tugas_page.dart';
import 'providers/tugas_provider.dart';
 
void main() {
  runApp(
    ChangeNotifierProvider(
      create: (_) => TugasProvider(),
      child: const TodoApp(),
    ),
  );
}
 
class TodoApp extends StatelessWidget {
  const TodoApp({super.key});
 
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'To-Do List',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(colorSchemeSeed: Colors.indigo, useMaterial3: true),
      home: const DaftarTugasPage(),
    );
  }
}
```

6. **Langkah 6. Buat widget satu baris tugas lib/widgets/tugas\_tile.dart.**

Klik kanan folder widgets &gt; **New &gt; Dart File** &gt; tugas\_tile. Widget ini hanya menerima satu objek Tugas dan memanggil aksi lewat context.read.

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
 
import '../models/tugas.dart';
import '../providers/tugas_provider.dart';
 
class TugasTile extends StatelessWidget {
  final Tugas tugas;
 
  const TugasTile({super.key, required this.tugas});
 
  String _format(DateTime d) {
    final hari = d.day.toString().padLeft(2, '0');
    final bulan = d.month.toString().padLeft(2, '0');
    return '$hari/$bulan/${d.year}';
  }
 
  @override
  Widget build(BuildContext context) {
    return Dismissible(
      key: ValueKey('hapus_${tugas.id}'),
      direction: DismissDirection.endToStart,
      background: Container(
        color: Theme.of(context).colorScheme.errorContainer,
        alignment: Alignment.centerRight,
        padding: const EdgeInsets.only(right: 20),
        child: const Icon(Icons.delete),
      ),
      onDismissed: (_) {
        final provider = context.read<TugasProvider>();
        provider.hapus(tugas.id);
 
        ScaffoldMessenger.of(context)
          ..hideCurrentSnackBar()
          ..showSnackBar(
            SnackBar(
              content: Text('"${tugas.judul}" dihapus'),
              action: SnackBarAction(
                label: 'URUNGKAN',
                onPressed: provider.urungkanHapus,
              ),
            ),
          );
      },
      child: CheckboxListTile(
        value: tugas.selesai,
        onChanged: (_) => context.read<TugasProvider>().ubahStatus(tugas.id),
        controlAffinity: ListTileControlAffinity.leading,
        title: Text(
          tugas.judul,
          style: TextStyle(
            decoration: tugas.selesai ? TextDecoration.lineThrough : null,
            color: tugas.selesai ? Colors.grey : null,
          ),
        ),
        subtitle: Text('Dibuat ${_format(tugas.dibuat)}'),
      ),
    );
  }
}
```

7. **Langkah 7. Buat form tambah tugas lib/widgets/form\_tambah\_tugas.dart.**

Klik kanan folder widgets &gt; **New &gt; Dart File** &gt; form\_tambah\_tugas. Form ini memakai Form, TextFormField, dan validasi seperti pada Bab 6.

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
 
import '../providers/tugas_provider.dart';
 
Future<void> tampilkanFormTambah(BuildContext context) {
  return showModalBottomSheet<void>(
    context: context,
    isScrollControlled: true,
    builder: (_) => const FormTambahTugas(),
  );
}
 
class FormTambahTugas extends StatefulWidget {
  const FormTambahTugas({super.key});
 
  @override
  State<FormTambahTugas> createState() => _FormTambahTugasState();
}
 
class _FormTambahTugasState extends State<FormTambahTugas> {
  final _formKey = GlobalKey<FormState>();
  final _judulController = TextEditingController();
 
  @override
  void dispose() {
    _judulController.dispose();
    super.dispose();
  }
 
  void _simpan() {
    if (!_formKey.currentState!.validate()) return;
 
    context.read<TugasProvider>().tambah(_judulController.text);
    Navigator.pop(context);
  }
 
  @override
  Widget build(BuildContext context) {
    return Padding(
      // Agar form naik di atas keyboard
      padding: EdgeInsets.fromLTRB(
        16,
        16,
        16,
        16 + MediaQuery.of(context).viewInsets.bottom,
      ),
      child: Form(
        key: _formKey,
        child: Column(
          mainAxisSize: MainAxisSize.min,
          crossAxisAlignment: CrossAxisAlignment.stretch,
          children: [
            Text('Tugas Baru', style: Theme.of(context).textTheme.titleLarge),
            const SizedBox(height: 16),
            TextFormField(
              controller: _judulController,
              autofocus: true,
              textInputAction: TextInputAction.done,
              onFieldSubmitted: (_) => _simpan(),
              decoration: const InputDecoration(
                labelText: 'Judul tugas',
                border: OutlineInputBorder(),
              ),
              validator: (value) {
                if (value == null || value.trim().isEmpty) {
                  return 'Judul tugas wajib diisi';
                }
                if (value.trim().length < 3) {
                  return 'Judul minimal 3 karakter';
                }
                return null;
              },
            ),
            const SizedBox(height: 16),
            FilledButton(
              onPressed: _simpan,
              child: const Text('Simpan'),
            ),
          ],
        ),
      ),
    );
  }
}
```

8. **Langkah 8. Buat halaman utama lib/pages/daftar\_tugas\_page.dart.**
   - Klik kanan folder pages &gt; **New &gt; Dart File** &gt; daftar\_tugas\_page. Halaman dipecah menjadi tiga widget kecil (\_Ringkasan, \_FilterBar, \_DaftarView) yang masing-masing hanya mendengarkan bagian state yang dibutuhkan.

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
 
import '../providers/tugas_provider.dart';
import '../widgets/form_tambah_tugas.dart';
import '../widgets/tugas_tile.dart';
import 'statistik_page.dart';
 
class DaftarTugasPage extends StatelessWidget {
  const DaftarTugasPage({super.key});
 
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Daftar Tugas'),
        actions: [
          IconButton(
            icon: const Icon(Icons.bar_chart),
            tooltip: 'Statistik',
            onPressed: () => Navigator.push(
              context,
              MaterialPageRoute(builder: (_) => const StatistikPage()),
            ),
          ),
          IconButton(
            icon: const Icon(Icons.delete_sweep),
            tooltip: 'Hapus yang selesai',
            onPressed: () => context.read<TugasProvider>().hapusSelesai(),
          ),
        ],
      ),
      body: const Column(
        children: [
          _Ringkasan(),
          _FilterBar(),
          Expanded(child: _DaftarView()),
        ],
      ),
      floatingActionButton: FloatingActionButton.extended(
        onPressed: () => tampilkanFormTambah(context),
        icon: const Icon(Icons.add),
        label: const Text('Tugas'),
      ),
    );
  }
}
 
class _Ringkasan extends StatelessWidget {
  const _Ringkasan();
 
  @override
  Widget build(BuildContext context) {
    // select: dibangun ulang hanya jika nilai yang dipilih berubah
    final aktif = context.select<TugasProvider, int>((p) => p.jumlahAktif);
    final selesai = context.select<TugasProvider, int>((p) => p.jumlahSelesai);
 
    return Padding(
      padding: const EdgeInsets.fromLTRB(16, 12, 16, 0),
      child: Text(
        '$aktif tugas aktif, $selesai selesai',
        style: Theme.of(context).textTheme.titleMedium,
      ),
    );
  }
}
 
class _FilterBar extends StatelessWidget {
  const _FilterBar();
 
  @override
  Widget build(BuildContext context) {
    final filter = context.select<TugasProvider, FilterTugas>((p) => p.filter);
 
    return Padding(
      padding: const EdgeInsets.symmetric(horizontal: 16, vertical: 8),
      child: SegmentedButton<FilterTugas>(
        segments: const [
          ButtonSegment(value: FilterTugas.semua, label: Text('Semua')),
          ButtonSegment(value: FilterTugas.aktif, label: Text('Aktif')),
          ButtonSegment(value: FilterTugas.selesai, label: Text('Selesai')),
        ],
        selected: {filter},
        onSelectionChanged: (pilihan) =>
            context.read<TugasProvider>().setFilter(pilihan.first),
      ),
    );
  }
}
 
class _DaftarView extends StatelessWidget {
  const _DaftarView();
 
  @override
  Widget build(BuildContext context) {
    final daftar = context.watch<TugasProvider>().tugasTampil;
 
    if (daftar.isEmpty) {
      return const Center(child: Text('Tidak ada tugas'));
    }
 
    return ListView.builder(
      padding: const EdgeInsets.only(bottom: 88),
      itemCount: daftar.length,
      itemBuilder: (context, i) =>
          TugasTile(key: ValueKey(daftar[i].id), tugas: daftar[i]),
    );
  }
}
```

**Langkah 9. Buat halaman statistik** `lib/pages/statistik_page.dart`**.**

Klik kanan folder `pages` &gt; **New &gt; Dart File** &gt; `statistik_page`. Halaman ini tidak menerima data lewat konstruktor sama sekali; ia membaca `TugasProvider` yang sama dengan halaman utama.

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
 
import '../providers/tugas_provider.dart';
 
class StatistikPage extends StatelessWidget {
  const StatistikPage({super.key});
 
  @override
  Widget build(BuildContext context) {
    final p = context.watch<TugasProvider>();
    final persen = (p.persenSelesai * 100).round();
 
    return Scaffold(
      appBar: AppBar(title: const Text('Statistik Tugas')),
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            SizedBox(
              width: 160,
              height: 160,
              child: Stack(
                fit: StackFit.expand,
                children: [
                  CircularProgressIndicator(
                    value: p.persenSelesai,
                    strokeWidth: 12,
                  ),
                  Center(
                    child: Text(
                      '$persen%',
                      style: Theme.of(context).textTheme.headlineLarge,
                    ),
                  ),
                ],
              ),
            ),
            const SizedBox(height: 32),
            Text('Total tugas: ${p.total}'),
            Text('Aktif: ${p.jumlahAktif}'),
            Text('Selesai: ${p.jumlahSelesai}'),
            const SizedBox(height: 24),
            FilledButton.tonal(
              onPressed: () => Navigator.pop(context),
              child: const Text('Kembali'),
            ),
          ],
        ),
      ),
    );
  }
}
```

**Langkah 10. Jalankan aplikasi.**

Pilih emulator pada device selector, lalu klik **Run**. Jika ada garis merah pada kode sebelum dijalankan, perbaiki dengan **Alt+Enter** (*Import library*) atau periksa kembali penulisan nama file dan folder.

**Langkah 11. Uji alur lengkap** dan centang bila berhasil:

| **No** | **Skenario** | **Hasil yang diharapkan** | **Berhasil** |
|---|---|---|---|
| 1 | Buka aplikasi | Tiga tugas awal tampil, ringkasan "3 tugas aktif, 0 selesai" | ☐ |
| 2 | Centang satu tugas | Judul tercoret, ringkasan menjadi "2 tugas aktif, 1 selesai" | ☐ |
| 3 | Tekan **+ Tugas**, simpan tanpa mengetik | Pesan "Judul tugas wajib diisi" | ☐ |
| 4 | Ketik "ab" lalu simpan | Pesan "Judul minimal 3 karakter" | ☐ |
| 5 | Ketik judul valid lalu simpan | Tugas baru muncul di urutan paling atas | ☐ |
| 6 | Pilih filter **Aktif**, lalu **Selesai** | Daftar hanya menampilkan tugas sesuai filter | ☐ |
| 7 | Geser satu tugas ke kiri | Tugas hilang, muncul SnackBar dengan tombol URUNGKAN | ☐ |
| 8 | Tekan **URUNGKAN** | Tugas kembali ke posisi semula | ☐ |
| 9 | Buka ikon statistik | Persentase dan jumlah sesuai kondisi daftar | ☐ |
| 10 | Ubah status tugas, buka statistik lagi | Angka statistik ikut berubah tanpa mengirim data lewat konstruktor | ☐ |
| 11 | Tekan ikon sapu (hapus yang selesai) | Semua tugas yang tercentang hilang | ☐ |

**Langkah 12. Amati pembangunan ulang widget.**

1. Tambahkan baris debugPrint('build: \_Ringkasan'); sebagai baris pertama di dalam build() milik \_Ringkasan. Lakukan hal serupa untuk \_FilterBar, \_DaftarView, dan DaftarTugasPage (ganti teksnya sesuai nama kelas).
2. Lakukan **Hot Restart**, lalu buka panel **Run**.
3. Lakukan setiap aksi pada tabel berikut dan catat widget yang dibangun ulang:

| **Aksi** | **DaftarTugasPage** | **\_Ringkasan** | **\_FilterBar** | **\_DaftarView** |
|---|---|---|---|---|
| Centang satu tugas | ... | ... | ... | ... |
| Ganti filter | ... | ... | ... | ... |
| Tambah tugas | ... | ... | ... | ... |

## Post-Test

1. Apa Fungsi dari  `setState()`?
2. Method `notifyListeners()` pada `ChangeNotifier` berfungsi untuk?
3. Agar ChangeNotifierProvider dapat diakses semua halaman yang dibuka dengan Navigator.push, ia sebaiknya diletakkan dimana?

## Latihan/Tugas

### Tugas 1: Pengembangan Aplikasi To-Do (Wajib)

Kembangkan aplikasi todo\_app dengan ketentuan berikut. Semua logika baru harus ditambahkan pada TugasProvider atau model, bukan di dalam widget.

1. **Ubah judul tugas.** Tambahkan method ubahJudul(String id, String judulBaru) pada TugasProvider. Di UI, tekan lama (*long press*) pada satu tugas untuk membuka dialog berisi TextFormField dengan validasi yang sama seperti form tambah.
2. **Prioritas.** Tambahkan enum Prioritas { rendah, sedang, tinggi } pada model Tugas. Form tambah menampilkan DropdownButtonFormField untuk memilih prioritas, dan setiap baris tugas menampilkan penanda prioritas (warna atau ikon).
3. **Tenggat waktu.** Tambahkan kolom tenggat (opsional) memakai showDatePicker. Tugas yang melewati tenggat dan belum selesai ditampilkan dengan warna peringatan.
4. **Pencarian.** Tambahkan kolom pencarian pada halaman utama. Hasil pencarian dikelola oleh TugasProvider (misalnya properti kataKunci dan getter tugasTampil yang menggabungkan filter dan pencarian).
5. **Tandai semua selesai.** Tambahkan tombol pada halaman statistik yang memanggil method baru tandaiSemuaSelesai(), lalu pastikan halaman utama ikut berubah ketika Anda kembali.
