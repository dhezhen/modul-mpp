# Modul 8: REST API dan Pemrosesan JSON

## Maksud dan Tujuan

1. Memberikan pemahaman tentang cara aplikasi Flutter berkomunikasi dengan layanan web melalui REST API dan format JSON.
2. Memberikan pengalaman langsung mengambil, mengubah (parsing), dan menampilkan data dari API publik menggunakan paket http dan dio melalui satu studi kasus yang sama, yaitu Direktori Pengguna.
3. Melatih mahasiswa menampilkan status loading, error, dan data kosong dengan benar agar pengalaman pengguna tetap baik saat jaringan bermasalah.
4. Menjelaskan konsep REST API, metode HTTP, dan kode status.
5. Memakai paket http untuk permintaan GET serta paket dio untuk GET, POST, dan DELETE.
6. Mengubah JSON menjadi model Dart (fromJson, termasuk objek bersarang) dan sebaliknya (toJson).
7. Menggunakan FutureBuilder dengan benar: *loading*, *error*, data, dan coba lagi.
8. Memisahkan lapisan layanan (*service*) dari tampilan dan menangani error (timeout, tanpa koneksi, status error).
9. Membuat aplikasi Direktori Pengguna dari API publik, lengkap dengan halaman detail, data relasi, dan penambahan data.

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

### REST API dan HTTP

**REST API** adalah layanan web yang menyediakan data lewat protokol HTTP. Setiap data (*resource*) memiliki alamat (URL), dan operasi ditentukan oleh **metode HTTP**. Aplikasi (klien) mengirim *request*, server membalas *response* berisi kode status dan data, umumnya berformat **JSON**.

| **Metode** | **Fungsi** | **Contoh pada studi kasus** |
|---|---|---|
| GET | Mengambil data | GET /users (daftar), GET /posts?userId=1 (dengan query) |
| POST | Membuat data baru | POST /users dengan isi JSON pengguna baru |
| PUT / PATCH | Mengganti / mengubah sebagian data | PATCH /users/1 |
| DELETE | Menghapus data | DELETE /users/1 |

| **Kode status** | **Arti** | **Penanganan di aplikasi** |
|---|---|---|
| 200 OK / 201 Created | Berhasil (201: data baru dibuat) | Proses isi respons |
| 400 / 401 / 403 | Permintaan salah / belum login / dilarang | Tampilkan pesan sesuai kasus |
| 404 Not Found | Data atau alamat tidak ditemukan | Pesan “data tidak ditemukan” |
| 500 dan seterusnya | Kesalahan di sisi server | Pesan “kesalahan server”, sediakan tombol coba lagi |

### JSON dan tipe data Dart

**JSON** (*JavaScript Object Notation*) adalah teks terstruktur berisi objek {...} dan larik [...]. Fungsi jsonDecode mengubah teks JSON menjadi struktur Dart, dan jsonEncode melakukan sebaliknya.

| **JSON** | **Hasil jsonDecode di Dart** |
|---|---|
| objek { ... } | Map&lt;String, dynamic&gt; |
| larik [ ... ] | List&lt;dynamic&gt; |
| string, angka, true/false, null | String, int/double, bool, null |

Contoh JSON (dipersingkat) dari GET /users/1, yang memuat **objek bersarang**

```dart
  "name": "Leanne Graham",
  "username": "Bret",
  "email": "Sincere@april.biz",
  "address": { "street": "Kulas Light", "city": "Gwenborough" },
  "company": { "name": "Romaguera-Crona", "catchPhrase": "Multi-layered client-server neural-net" }
}
```

### Model dan serialisasi

Mengakses data lewat json['name'] rawan salah ketik dan tidak diperiksa tipenya saat kompilasi. Solusinya adalah **model**: class dengan properti bertipe jelas, dibuat lewat factory Kelas.fromJson(Map&lt;String, dynamic&gt; json). Arah sebaliknya memakai method toJson().

- Gunakan as untuk memastikan tipe, mis. json['id'] as int; bila bisa kosong, gunakan as String? dan nilai bawaan ?? ''.
- Objek bersarang diproses dengan fromJson milik class turunannya, mis. Alamat.fromJson(json['address'] as Map&lt;String, dynamic&gt;).
- Untuk proyek besar tersedia pembangkit kode (mis. json\_serializable); modul ini menulisnya manual agar prosesnya terlihat jelas.

### Paket http dan dio

| **Aspek** | **http** | **dio** |
|---|---|---|
| Sifat | Ringkas, resmi dari tim Dart | Lengkap, populer untuk aplikasi besar |
| Decode JSON | Manual (jsonDecode) | Otomatis (response.data) |
| Konfigurasi bersama | Dibuat sendiri | BaseOptions (baseUrl, timeout, header) |
| Interceptor (log, token) | Tidak ada bawaan | Ada (InterceptorsWrapper) |
| Error status non-2xx | Tidak melempar error; periksa statusCode | Melempar DioException (badResponse) |
| Fitur lain | Dasar | Pembatalan, unggah/unduh, progres |

### Lapisan layanan (service)

Kode jaringan dipisah dari tampilan ke class **service/repository** (mis. PenggunaService). Widget hanya memanggil method seperti ambilSemua() dan menampilkan hasilnya. Keuntungannya: kode UI lebih bersih, mudah diganti (http ke dio), dan mudah diuji.

### FutureBuilder

FutureBuilder&lt;T&gt; membangun tampilan berdasarkan status sebuah Future:

| **Bagian** | **Keterangan** |
|---|---|
| future | Future yang ditunggu. **Simpan di state** (mis. di initState), jangan dibuat di dalam build() |
| snapshot.connectionState | waiting saat menunggu; done setelah selesai |
| snapshot.hasError / snapshot.error | Ada tidaknya error dan isinya |
| snapshot.hasData / snapshot.data | Ada tidaknya data dan isinya |

**Urutan pengecekan yang dianjurkan:** (1) *loading* → (2) *error* → (3) data (periksa juga data kosong). Untuk memuat ulang, ganti nilai Future di dalam setState.

**Jebakan:** menulis future: layanan.ambilSemua() langsung di build() membuat permintaan jaringan diulang **setiap widget dibangun ulang** (mis. saat ukuran jendela berubah).

### Loading, error, dan data kosong

- **Loading:** tampilkan indikator (CircularProgressIndicator) agar pengguna tahu proses berjalan.
- **Error:** tampilkan pesan yang **ramah** (bukan teks mentah dari exception) beserta tombol **Coba lagi**.
- **Data kosong:** tampilkan keterangan, bukan layar kosong.
- Pasang **timeout** agar aplikasi tidak menunggu tanpa batas, dan petakan error (timeout, tanpa koneksi, status 404/500) ke pesan yang jelas.

### Izin dan keamanan jaringan

- **Android:** build *release* memerlukan izin &lt;uses-permission android:name="android.permission.INTERNET"/&gt; pada android/app/src/main/AndroidManifest.xml (pada mode debug izin ini sudah tersedia).
- **Web:** peramban menerapkan **CORS**; server harus mengizinkan asal permintaan. JSONPlaceholder mengizinkannya, sebagian API lain tidak.
- **HTTPS:** gunakan alamat https. Android 9 ke atas memblokir HTTP biasa (*cleartext*).
- **macOS:** aplikasi desktop memerlukan entitlement com.apple.security.network.client.
- Jangan menyimpan kunci API rahasia di dalam kode aplikasi.

## Pre-Test

1. Apa yang dimaksud dengan REST API?
2. Apa fungsi JSON?
3. Apa fungsi package http pada Flutter?
4. Apa perbedaan async dan await?
5. Apa fungsi FutureBuilder?

## Praktikum

### Praktikum: Studi kasus membuat aplikasi cuaca

1. **Langkah 1 –** Membuat Project di Android Studio dan setup dependency
      - Buatlah aplikasi flutter baru dengan nama weather\_app
      - Buka file pubspec.yaml dan tambahkan dependencies http dan cupertino icon seperti dibawah ini:

```yaml
dependencies:
  flutter:
    sdk: flutter

  cupertino_icons: ^1.0.0
  http: ^1.5.0
```

klik dibagian atas Pu Get

2. **Langkah 2 – Membuat Struktur Folder**
- Di dalam folder lib, buat struktur:
      - lib/
      - main.dart
      - models/
         - weather.dart
      - services/
         - weather\_service.dart

Keterangan:

- models   → menyimpan struktur data
- services   → menangani komunikasi dengan API
- main.dart    → menampilkan tampilan aplikasi
3. **Langkah 3 – Membuat Model Weather**
   - Buka folder lib/model, kemudia buat file dart bernama **weather,** kemudian masukan kode kode berikut:

```dart
class Weather {
  final String city;
  final String country;
  final double temperature;
  final int humidity;
  final double windSpeed;
  final int weatherCode;
  final String description;

  Weather({
    required this.city,
    required this.country,
    required this.temperature,
    required this.humidity,
    required this.windSpeed,
    required this.weatherCode,
    required this.description,
  });
}
```

Model tersebut digunakan untuk menyimpan informasi cuaca yang akan ditampilkan.

4. **Langkah 4 – Membuat Weather Service**
   - Buka folder **lib/services**, kemudia buat file dart bernama **weather\_service,** kemudian masukan kode kode berikut:

```dart
import 'dart:convert';
import 'package:http/http.dart' as http;
import '../models/weather.dart';

class WeatherService {
  static Future<Weather> getWeather(String city) async {

    // 1. Mencari koordinat kota
    final locationUrl = Uri.https(
      'geocoding-api.open-meteo.com',
      '/v1/search',
      {
        'name': city,
        'count': '1',
        'language': 'id',
        'format': 'json',
      },
    );

    final locationResponse =
        await http.get(locationUrl);

    if (locationResponse.statusCode != 200) {
      throw Exception('Gagal mencari lokasi');
    }

    final locationData =
        jsonDecode(locationResponse.body);

    if (locationData['results'] == null ||
        locationData['results'].isEmpty) {
      throw Exception('Kota tidak ditemukan');
    }

    final location =
        locationData['results'][0];

    final latitude =
        location['latitude'];

    final longitude =
        location['longitude'];

    final cityName =
        location['name'];

    final country =
        location['country'] ?? '';

    // 2. Mengambil data cuaca
    final weatherUrl = Uri.https(
      'api.open-meteo.com',
      '/v1/forecast',
      {
        'latitude': latitude.toString(),
        'longitude': longitude.toString(),
        'current':
            'temperature_2m,relative_humidity_2m,weather_code,wind_speed_10m',
        'timezone': 'auto',
      },
    );

    final weatherResponse =
        await http.get(weatherUrl);

    if (weatherResponse.statusCode != 200) {
      throw Exception('Gagal mengambil data cuaca');
    }

    final weatherData =
        jsonDecode(weatherResponse.body);

    final current =
        weatherData['current'];

    final weatherCode =
        current['weather_code'];

    return Weather(
      city: cityName,
      country: country,
      temperature:
          (current['temperature_2m'] as num).toDouble(),
      humidity:
          current['relative_humidity_2m'],
      windSpeed:
          (current['wind_speed_10m'] as num).toDouble(),
      weatherCode: weatherCode,
      description:
          weatherDescription(weatherCode),
    );
  }

  static String weatherDescription(int code) {

    if (code == 0) {
      return 'Cerah';
    }

    if (code >= 1 && code <= 3) {
      return 'Berawan';
    }

    if (code >= 51 && code <= 67) {
      return 'Hujan';
    }

    if (code >= 71 && code <= 77) {
      return 'Salju';
    }

    if (code >= 80 && code <= 82) {
      return 'Hujan deras';
    }

    if (code >= 95) {
      return 'Badai petir';
    }

    return 'Kondisi tidak diketahui';
  }
}
```

5. **Langkah 5 – Membuat Tampilan Utama**
   - Buka folder **lib/main.dart**, kemudian update seluruh kode dengan kode berikut:

```dart
import 'package:flutter/cupertino.dart';
import 'package:flutter/material.dart';

import 'models/weather.dart';
import 'services/weather_service.dart';

void main() {
  runApp(const WeatherApp());
}

class WeatherApp extends StatelessWidget {
  const WeatherApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Weather App',

      theme: ThemeData(
        useMaterial3: true,
        colorSchemeSeed: Colors.blue,
      ),

      home: const WeatherPage(),
    );
  }
}

class WeatherPage extends StatefulWidget {
  const WeatherPage({super.key});

  @override
  State<WeatherPage> createState() =>
      _WeatherPageState();
}

class _WeatherPageState
    extends State<WeatherPage> {

  final TextEditingController cityController =
      TextEditingController(text: 'Kuningan');

  late Future<Weather> weatherFuture;

  @override
  void initState() {
    super.initState();

    weatherFuture =
        WeatherService.getWeather('Kuningan');
  }

  void searchWeather() {

    final city =
        cityController.text.trim();

    if (city.isEmpty) {
      return;
    }
    setState(() {
      weatherFuture =
          WeatherService.getWeather(city);
    });
  }

  @override
  Widget build(BuildContext context) {

    return Scaffold(
      appBar: AppBar(
        title: const Text(
          'Weather App',
          style: TextStyle(
            fontWeight: FontWeight.bold,
          ),
        ),

        centerTitle: true,
        actions: [
          IconButton(
            onPressed: searchWeather,
            icon: const Icon(
              CupertinoIcons.refresh,
            ),
          ),
        ],
      ),

      body: Padding(
        padding: const EdgeInsets.all(20),
        child: Column(
          children: [

            // SEARCH
            Row(
              children: [
                Expanded(
                  child: TextField(
                    controller: cityController,

                    decoration: InputDecoration(
                      hintText: 'Masukkan nama kota',
                      prefixIcon: const Icon(
                        CupertinoIcons.search,
                      ),

                      border: OutlineInputBorder(
                        borderRadius:
                            BorderRadius.circular(16),
                      ),
                    ),
                    onSubmitted: (_) {
                      searchWeather();
                    },
                  ),
                ),

                const SizedBox(width: 10),
                IconButton(
                  onPressed: searchWeather,

                  icon: const Icon(
                    CupertinoIcons.arrow_right_circle_fill,
                    size: 40,
                  ),
                ),
              ],
            ),

            const SizedBox(height: 25),
            Expanded(
              child: FutureBuilder<Weather>(
                future: weatherFuture,
                builder:
                    (context, snapshot) {
                  // LOADING
                  if (snapshot.connectionState ==
                      ConnectionState.waiting) {
                    return const Center(
                      child: Column(
                        mainAxisAlignment:
                            MainAxisAlignment.center,
                        children: [
                          CircularProgressIndicator(),

                          SizedBox(height: 15),

                          Text(
                            'Mengambil data cuaca...',
                          ),
                        ],
                      ),
                    );
                  }

                  // ERROR
                  if (snapshot.hasError) {

                    return Center(
                      child: Column(
                        mainAxisAlignment:
                            MainAxisAlignment.center,

                        children: [

                          const Icon(
                            CupertinoIcons
                                .exclamationmark_triangle,
                            size: 60,
                          ),
                          const SizedBox(height: 15),
                          Text(
                            snapshot.error.toString(),
                            textAlign: TextAlign.center,
                          ),
                        ],
                      ),
                    );
                  }
                  // DATA
                  final weather =
                      snapshot.data!;

                  return WeatherContent(
                    weather: weather,
                  );
                },
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

6. **Langkah 6 – Membuat Widget Weather Content**
- pada file main.dart tambahkan class WeatherPageState

```dart
class WeatherContent extends StatelessWidget {
  final Weather weather;
  const WeatherContent({
    super.key,
    required this.weather,
  });
  @override
  Widget build(BuildContext context) {
    return SingleChildScrollView(
      child: Column(
        children: [
          // LOCATION
          const Icon(
            CupertinoIcons.location_fill,
            size: 22,
          ),

          const SizedBox(height: 8),

          Text(
            '${weather.city}, ${weather.country}',
            style: const TextStyle(
              fontSize: 22,
              fontWeight: FontWeight.bold,
            ),
          ),
          const SizedBox(height: 25),
          // WEATHER CARD
          Container(
            width: double.infinity,
            padding: const EdgeInsets.all(25),
            decoration: BoxDecoration(
              borderRadius:
                  BorderRadius.circular(28),
              gradient: const LinearGradient(
                begin: Alignment.topLeft,
                end: Alignment.bottomRight,

                colors: [
                  Color(0xff4facfe),
                  Color(0xff00c6fb),
                ],
              ),
            ),

            child: Column(
              children: [
                Icon(
                  weatherIcon(
                    weather.weatherCode,
                  ),
                  size: 85,
                  color: Colors.white,
                ),
                const SizedBox(height: 15),
                Text(
                  '${weather.temperature.toStringAsFixed(1)}°C',

                  style: const TextStyle(
                    fontSize: 50,
                    fontWeight: FontWeight.bold,
                    color: Colors.white,
                  ),
                ),
                const SizedBox(height: 5),

                Text(
                  weather.description,
                  style: const TextStyle(
                    fontSize: 20,
                    color: Colors.white,
                  ),
                ),
              ],
            ),
          ),

          const SizedBox(height: 20),

          // INFORMATION
          Row(
            children: [

              Expanded(
                child: WeatherInfoCard(
                  icon: CupertinoIcons.drop_fill,
                  title: 'Kelembapan',
                  value:
                      '${weather.humidity}%',
                ),
              ),
              const SizedBox(width: 12),

              Expanded(
                child: WeatherInfoCard(
                  icon: CupertinoIcons.wind,
                  title: 'Angin',
                  value:
                      '${weather.windSpeed.toStringAsFixed(1)} km/h',
                ),
              ),
            ],
          ),
        ],
      ),
    );
  }}
IconData weatherIcon(int code) {

  if (code == 0) {
    return CupertinoIcons.sun_max_fill;
  }

  if (code >= 1 && code <= 3) {
    return CupertinoIcons.cloud_sun_fill;
  }

  if (code >= 51 && code <= 67) {
    return CupertinoIcons.cloud_rain_fill;
  }

  if (code >= 71 && code <= 77) {
    return CupertinoIcons.snow;
  }

  if (code >= 80 && code <= 82) {
    return CupertinoIcons.cloud_heavyrain_fill;
  }

  if (code >= 95) {
    return CupertinoIcons.cloud_bolt_rain_fill;
  }

  return CupertinoIcons.cloud_fill;
}
```

7. **Langkah 7 – Membuat Weather Info Card**
   - Terakhir, pada file main.dart, tambahkan **class wheaterInfoCard**

```dart
class WeatherInfoCard extends StatelessWidget {

  final IconData icon;
  final String title;
  final String value;

  const WeatherInfoCard({
    super.key,
    required this.icon,
    required this.title,
    required this.value,
  });

  @override
  Widget build(BuildContext context) {

    return Card(
      elevation: 1,

      shape: RoundedRectangleBorder(
        borderRadius:
            BorderRadius.circular(20),
      ),

      child: Padding(
        padding: const EdgeInsets.all(18),
        child: Column(
          children: [
            Icon(
              icon,
              size: 30,
            ),
            const SizedBox(height: 10),
            Text(
              title,
              style: const TextStyle(
                fontSize: 13,
              ),
            ),
            const SizedBox(height: 5),
            Text(
              value,
              style: const TextStyle(
                fontSize: 16,
                fontWeight: FontWeight.bold,
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

Terakhir, simpan dan jalankan aplikasi tersebut. Berikut tampilan jika berhasil

![](.gitbook/assets/modul08-01.png)

## Post-Test

6. Apa fungsi Open-Meteo API pada aplikasi?
7. Mengapa aplikasi membutuhkan Geocoding API?
8. Mengapa latitude dan longitude diperlukan untuk mengambil data cuaca?
9. Apa fungsi package http?
10. Apa fungsi jsonDecode()?

## Latihan/Tugas

### Tugas: Pengembangan Weather App

### Kembangkan aplikasi Weather App yang telah dibuat dengan ketentuan:

Tambahkan minimal 2 fitur dari pilihan berikut:

Prakiraan cuaca 5 hari

- Dark mode
- Tombol refresh
- Riwayat pencarian kota
- Kota favorit
- Menampilkan sunrise dan sunset
- Menampilkan kemungkinan hujan
- Menggunakan LayoutBuilder agar tampilan responsif
