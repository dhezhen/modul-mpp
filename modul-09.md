# Modul 9: Penyimpanan Data Lokal dan Fitur Platform

## Maksud dan Tujuan

1. Memberikan pemahaman bahwa data di memori hilang saat aplikasi ditutup, sehingga aplikasi nyata perlu menyimpan data secara lokal dengan cara yang sesuai kebutuhan.
2. Memberikan pengalaman langsung memakai shared\_preferences, SQLite (sqflite), file, serta plugin kamera/galeri dan lokasi melalui satu studi kasus yang sama, yaitu Catatan Lapangan.
3. Memperkenalkan cara Flutter berinteraksi dengan platform: platform channel dan penyesuaian perilaku per platform.
4. Memilih mekanisme penyimpanan lokal yang tepat (shared\_preferences, file, SQLite, NoSQL) sesuai jenis data.
5. Menyimpan dan membaca pengaturan dengan shared\_preferences.
6. Membuat tabel dan melakukan CRUD pada SQLite dengan sqflite, termasuk migrasi skema database.
7. Mengambil foto dari kamera atau galeri dengan image\_picker dan menyimpannya sebagai file lewat path\_provider.
8. Mengambil lokasi dengan geolocator dan menangani izin runtime (diizinkan, ditolak, ditolak permanen).

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

### 1. Mengapa perlu penyimpanan lokal?

Variabel dan state hanya hidup di memori; saat aplikasi ditutup, datanya hilang. Penyimpanan lokal membuat data **persisten** (bertahan) dan dapat dipakai tanpa internet. Pilihan mekanismenya bergantung pada jenis dan jumlah data:

| **Mekanisme** | **Cocok untuk** | **Catatan** |
|---|---|---|
| shared\_preferences | Pengaturan kecil berpasangan kunci–nilai (tema, nama, status) | Hanya tipe sederhana; tidak terenkripsi; bukan untuk data besar atau sensitif |
| File (path\_provider) | Foto, dokumen, berkas besar | Simpan berkasnya di file, simpan **path**-nya di database |
| SQLite (sqflite, drift) | Data terstruktur dan relasional, perlu pencarian dan pengurutan | sqflite memakai SQL langsung; drift bertipe aman dengan pembangkit kode dan mendukung stream |
| NoSQL (Hive, Isar) | Objek/dokumen tanpa skema tabel, baca-tulis cepat | Status pemeliharaan paket berubah-ubah; periksa versi dan aktivitas terbaru sebelum dipakai |
| flutter\_secure\_storage | Data sensitif (token, kata sandi) | Memakai penyimpanan aman milik sistem operasi |

### 2. shared\_preferences

- Menyimpan bool, int, double, String, dan List&lt;String&gt; dengan kunci berupa teks: setBool, getString, dan seterusnya. Kunci yang belum ada menghasilkan null, sehingga beri nilai bawaan (?? false).
- Operasi tulis bersifat asinkron (await prefs.setString(...)). Hapus satu kunci dengan remove, hapus semua dengan clear.
- Jika dipakai sebelum runApp(), panggil WidgetsFlutterBinding.ensureInitialized() terlebih dahulu karena plugin memerlukan binding Flutter.
- Versi paket yang lebih baru menyediakan API SharedPreferencesAsync dan SharedPreferencesWithCache; API SharedPreferences.getInstance() yang dipakai modul ini dapat menampilkan petunjuk *deprecated*, tetapi konsepnya sama.

### 3. SQLite dan sqflite

**SQLite** adalah database relasional yang tertanam dalam satu berkas. Data disimpan dalam **tabel** (kolom dan baris); setiap baris diidentifikasi **primary key**. Tipe kolom utama: INTEGER, TEXT, REAL, BLOB. Tipe lain disesuaikan: DateTime disimpan sebagai TEXT (ISO 8601), bool sebagai INTEGER (0/1).

| **Operasi** | **SQL** | **Method sqflite** |
|---|---|---|
| Membuat tabel | CREATE TABLE ... | db.execute(...) |
| Menambah baris | INSERT INTO ... | db.insert(tabel, map) |
| Membaca | SELECT ... WHERE ... ORDER BY ... | db.query(tabel, where:, whereArgs:, orderBy:) |
| Mengubah | UPDATE ... SET ... WHERE ... | db.update(tabel, map, where:, whereArgs:) |
| Menghapus | DELETE FROM ... WHERE ... | db.delete(tabel, where:, whereArgs:) |

- **Pemetaan model:** hasil query berupa List&lt;Map&lt;String, Object?&gt;&gt;; ubah ke objek dengan Model.fromMap, dan sebaliknya dengan toMap (serupa fromJson/toJson pada Modul 8).
- **Parameter aman:** gunakan tanda ? dan whereArgs, **jangan** menyusun SQL dengan interpolasi string agar terhindar dari *SQL injection*.
- **Versi dan migrasi:** openDatabase(version: n, onCreate:, onUpgrade:). onCreate dipanggil saat database baru dibuat; onUpgrade dipanggil bila versi di berkas lebih kecil dari version, dan dipakai untuk mengubah skema (mis. ALTER TABLE ... ADD COLUMN) tanpa menghapus data lama.
- **Satu instance:** buka database sekali dan pakai ulang (pola *singleton*).
- **Dukungan platform:** sqflite mendukung Android, iOS, dan macOS. Web, Windows, dan Linux memerlukan paket pendukung lain (mis. varian FFI/web) atau pilihan seperti drift.

### 4. File dan path\_provider

path\_provider memberi lokasi direktori aplikasi, misalnya getApplicationDocumentsDirectory() (permanen) dan getTemporaryDirectory() (cache; dapat dibersihkan sistem). Paket path membantu menyusun path (join, extension). Foto dari image\_picker berada di cache, sehingga **harus disalin** ke direktori dokumen jika ingin dipertahankan.

### 5. Plugin fitur perangkat dan izin runtime

**Plugin** adalah paket yang berisi kode Dart sekaligus kode native (Kotlin/Swift) untuk mengakses fitur perangkat. Fitur sensitif (lokasi, kamera) memerlukan **izin** dari pengguna.

- **Alur izin lokasi:** (1) periksa layanan lokasi aktif (isLocationServiceEnabled), (2) periksa izin (checkPermission), (3) minta izin bila denied (requestPermission), (4) bila deniedForever, arahkan pengguna ke pengaturan aplikasi, (5) baru ambil posisi (getCurrentPosition).
- **Android:** izin dideklarasikan pada AndroidManifest.xml (mis. ACCESS\_FINE\_LOCATION) dan diminta saat aplikasi berjalan.
- image\_picker memakai aplikasi kamera dan pemilih foto milik sistem sehingga pada Android umumnya tidak memerlukan izin kamera tambahan.
- Minta izin **saat dibutuhkan**, jelaskan alasannya, dan sediakan jalur alternatif bila ditolak.
- Setelah menambah plugin, **jalankan ulang aplikasi sepenuhnya**: hot reload dan hot restart tidak memuat kode native baru.

## Pre-Test

1. Mengapa data pada variabel atau state hilang ketika aplikasi ditutup? Apa yang dimaksud data persisten?
2. Apa yang dimaksud pasangan kunci–nilai (key–value)? Berikan satu contoh.
3. Apa yang dimaksud tabel, baris, kolom, dan primary key pada database?
4. Tuliskan perintah SQL untuk mengambil seluruh data dari tabel mahasiswa.
5. Mengapa operasi baca/tulis ke penyimpanan dibuat asinkron? Apa fungsi async dan await?

## Praktikum

### Praktikum: Membuat Aplikasi My Profile

### Langkah 1 – membuat projek baru di Android Studio

   - Buatlah aplikasi flutter baru dengan nama **praktikum9\_storage**

### Langkah 2 – Menambahkan Package.

### Buka file pubspec.yaml dan tambahkan dependencies http dan cupertino icon seperti dibawah ini:

```yaml
dependencies:
  flutter:
    sdk: flutter

  cupertino_icons: ^1.0.0
  shared_preferences: ^2.5.3
  image_picker: ^1.2.0
```

   - ketik di terminal comand `Flutter Pub get`
1. Langkah 3, buat strukttur folder dan file seperti dibawah ini:
   - lib/
   - main.dart
   - services/
      - storage\_service.dart
   - pages/
      - profile\_page.dart
2. Langkah 4 – Membuat Storage Service
   - Buka: **lib/services/storage\_service.dart**  kemudian ketikan kode dibawah ini:

```dart
import 'package:shared_preferences/shared_preferences.dart';

class StorageService {
  static const String nameKey = 'user_name';
  static Future<void> saveName(String name) async {
    final prefs =
        await SharedPreferences.getInstance();
    await prefs.setString(nameKey, name);
  }
  static Future<String> getName() async {
    final prefs =
        await SharedPreferences.getInstance();
    return prefs.getString(nameKey) ?? '';
  }
  static Future<void> deleteName() async {
    final prefs =
        await SharedPreferences.getInstance();
    await prefs.remove(nameKey);
  }
}
```

Class ini digunakan untuk menangani proses penyimpanan dan pengambilan data.

Dengan demikian kode shared\_preferences tidak perlu ditulis berulang-ulang pada halaman utama.

3. **Langkah 5 – Membuat Halaman Profile**
   - Buka: **lib/pages/profile\_page.dart** kemudian ketikan kode dibawah ini:

```dart
import 'dart:io';

import 'package:flutter/cupertino.dart';
import 'package:flutter/material.dart';
import 'package:image_picker/image_picker.dart';

import '../services/storage_service.dart';

class ProfilePage extends StatefulWidget {
  const ProfilePage({super.key});

  @override
  State<ProfilePage> createState() =>
      _ProfilePageState();
}

class _ProfilePageState
    extends State<ProfilePage> {

  final TextEditingController nameController =
      TextEditingController();

  File? selectedImage;

  String platformName = '';

  @override
  void initState() {
    super.initState();

    loadData();
  }

  Future<void> loadData() async {

    final name =
        await StorageService.getName();

    setState(() {
      nameController.text = name;

      platformName = 'Android';
    });
  }

  Future<void> saveName() async {
    final name =
        nameController.text.trim();

    if (name.isEmpty) {
      return;
    }
    await StorageService.saveName(name);
    if (!mounted) return;
    ScaffoldMessenger.of(context)
        .showSnackBar(
      const SnackBar(
        content: Text(
          'Nama berhasil disimpan',
        ),
      ),
    );
  }

  Future<void> pickImage() async {

    final picker = ImagePicker();

    final image =
        await picker.pickImage(
      source: ImageSource.gallery,
    );
    if (image == null) {
      return;
    }
    setState(() {
      selectedImage =
          File(image.path);
    });
  }
  Future<void> deleteName() async {
    await StorageService.deleteName();
    setState(() {
      nameController.clear();
    });

    if (!mounted) return;

    ScaffoldMessenger.of(context)
        .showSnackBar(
      const SnackBar(
        content: Text(
          'Data berhasil dihapus',
        ),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {

    return Scaffold(

      appBar: AppBar(
        title: const Text(
          'My Profile',
          style: TextStyle(
            fontWeight: FontWeight.bold,
          ),
        ),
        centerTitle: true,
      ),

      body: SingleChildScrollView(
        padding: const EdgeInsets.all(20),
        child: Column(
          children: [
            const SizedBox(height: 20),
            // FOTO PROFIL
            CircleAvatar(
              radius: 60,
              backgroundImage:
                  selectedImage != null
                      ? FileImage(
                          selectedImage!,
                        )
                      : null,
              child: selectedImage == null
                  ? const Icon(
                      CupertinoIcons.person_fill,
                      size: 55,
                    )
                  : null,
            ),
            const SizedBox(height: 15),

            // PILIH FOTO
            OutlinedButton.icon(
              onPressed: pickImage,

              icon: const Icon(
                CupertinoIcons.photo,
              ),

              label: const Text(
                'Pilih Foto',
              ),
            ),

            const SizedBox(height: 30),

            // NAMA
            TextField(
              controller: nameController,

              decoration: InputDecoration(
                labelText: 'Nama Pengguna',

                prefixIcon: const Icon(
                  CupertinoIcons.person,
                ),

                border: OutlineInputBorder(
                  borderRadius:
                      BorderRadius.circular(15),
                ),
              ),
            ),

            const SizedBox(height: 15),

            // SIMPAN
            SizedBox(
              width: double.infinity,

              child: ElevatedButton.icon(
                onPressed: saveName,

                icon: const Icon(
                  CupertinoIcons
                      .checkmark_circle,
                ),

                label: const Text(
                  'Simpan Nama',
                ),
              ),
            ),

            const SizedBox(height: 15),

            // PLATFORM
            Card(
              child: ListTile(

                leading: const Icon(
                  CupertinoIcons.device_phone_portrait,
                ),

                title: const Text(
                  'Platform',
                ),

                subtitle: Text(
                  platformName,
                ),
              ),
            ),

            const SizedBox(height: 15),

            // HAPUS
            TextButton.icon(
              onPressed: deleteName,

              icon: const Icon(
                CupertinoIcons.trash,
              ),

              label: const Text(
                'Hapus Data',
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

4. **Langkah 6 – Update file main.dart**
   - Buka: **lib/main.dart** kemudian ketikan kode dibawah ini:

```dart
import 'package:flutter/material.dart';

import 'pages/profile_page.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {

  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {

    return MaterialApp(
      debugShowCheckedModeBanner: false,

      title: 'My Profile',

      theme: ThemeData(
        useMaterial3: true,
        colorSchemeSeed: Colors.blue,
      ),

      home: const ProfilePage(),
    );
  }
}
```

5. **Langkah 7 – Menjalankan Aplikasi**
   - Jalankan aplikasi pada emulator Android atau web, dan coba isikan nama Anda, serta ubah foto profilnya, amati hasilnya.
   - Lakukan percobaan

## Post-Test

1. Apa fungsi penyimpanan data lokal?
2. Apa fungsi shared\_preferences?
3. Kapan sebaiknya menggunakan SQLite?

## Latihan/Tugas

Kembangkan aplikasi My Profile dengan ketentuan berikut.

Tambahkan fitur-fitur dibawah ini:

1. Email pengguna
2. Nomor telepon
3. Dark mode
4. Foto dari kamera
5. Lokasi pengguna
6. Riwayat data
7. Database SQLite
8. Halaman edit profil
