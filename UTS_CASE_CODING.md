# UTS Mobile Programming: Persiapan Case Coding

**Materi:** RecyclerView, Multiple Activity, Themes
**Jenis soal (konfirmasi dosen):** case coding, tidak ada teori.

> Bentuk soal persisnya belum diketahui. Perkiraanku ada dua kemungkinan: (1) membuat aplikasi atau fitur dari soal cerita, atau (2) memperbaiki kode yang error. File ini menyiapkan keduanya.

## Cara pakai file ini

| Bagian | Isi | Yang harus kamu lakukan |
|---|---|---|
| **A. Setup** | Membuat project, shortcut, jebakan template | Baca sekali (5 menit) |
| **B. Studi kasus lengkap** | Satu aplikasi yang memakai ketiga topik | **Ketik ulang sendiri di Android Studio sampai jalan.** Ini bagian terpenting |
| **C. Latihan variasi** | 5 soal yang mirip soal UTS | Kerjakan tanpa melihat kunci, baru dicek |
| **D. Debug cepat** | Daftar gejala error, penyebab, solusinya | Pakai saat aplikasimu crash |
| **E. Checklist ujian** | Urutan kerja saat UTS | Baca sebelum masuk ruangan |

**Target:** bisa mengetik ulang Studi Kasus B dari nol dalam 30 sampai 40 menit tanpa melihat catatan.

---

# A. Setup Project

## A.1 Membuat project baru

**File → New → New Project → Empty Views Activity**
- Language: **Java**
- Minimum SDK: biarkan default (project dosen memakai API 24)

> ⚠️ Pilih **Empty Views Activity**, bukan **Empty Activity**. Template "Empty Activity" memakai Jetpack Compose dan Kotlin, bukan XML dan Java seperti di kelas.

## A.2 Kode bawaan template (EdgeToEdge)

Activity baru dari template berisi kode ini:

```java
EdgeToEdge.enable(this);
setContentView(R.layout.activity_main);
ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {
    Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
    v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
    return insets;
});
```

Ada dua pilihan:
1. **Hapus** baris `EdgeToEdge.enable(this);` dan blok `ViewCompat...` sampai `});`, sisakan `setContentView(...)`. Ini yang dilakukan dosen di semua sample code, dan yang dipakai di file ini.
2. **Biarkan.** Kalau dibiarkan, root layout **wajib** tetap punya `android:id="@+id/main"`. Kalau id itu terhapus, aplikasi langsung crash saat dibuka (`NullPointerException`).

> Kalau theme kamu `NoActionBar` dan kode ini dihapus, teks paling atas bisa tertutup status bar di HP Android 15 ke atas. Di studi kasus ini theme-nya memakai Action Bar, jadi aman dihapus.

## A.3 Membuat Activity kedua

Klik kanan folder package (`java/com.example.namaapp`) → **New → Activity → Empty Views Activity** → isi nama, misalnya `DetailActivity`.

Wizard ini otomatis membuat file `.java`, file layout `.xml`, **dan mendaftarkannya di `AndroidManifest.xml`**. Kalau kamu membuat class Java secara manual (New → Java Class), kamu harus menambahkan `<activity>` ke manifest sendiri.

## A.4 Membuat `styles.xml`

Project baru tidak punya `styles.xml`. Klik kanan `res/values` → **New → Values Resource File** → nama: `styles`. (Style juga boleh ditulis langsung di `themes.xml`, hasilnya sama.)

## A.5 RecyclerView tidak perlu tambah dependency

Project dosen tidak menambahkan dependency RecyclerView di `build.gradle`, karena RecyclerView sudah ikut terbawa library `material` yang ada di template. Langsung pakai tag `androidx.recyclerview.widget.RecyclerView` di XML.

## A.6 Shortcut Android Studio (Windows)

| Shortcut | Fungsi |
|---|---|
| **Alt+Enter** | Import otomatis dan quick fix. Contoh: di baris `extends RecyclerView.Adapter<...>` yang merah, pilih **Implement methods** untuk membuat 3 method Adapter otomatis |
| **Alt+Insert** | Generate **Constructor** dan **Getter** (sangat berguna untuk class model) |
| **Ctrl+O** | Override method (misalnya `onSupportNavigateUp`) |
| **Ctrl+Space** | Autocomplete |
| **Ctrl+Alt+L** | Merapikan indentasi kode |
| **Shift+F10** | Run aplikasi |

> ⚠️ **Hati-hati saat Alt+Enter memberi beberapa pilihan import:**
> - `ActionBar` → pilih **`androidx.appcompat.app.ActionBar`**, bukan `android.app.ActionBar`. Yang salah akan error "incompatible types" di `getSupportActionBar()`.
> - `View` → `android.view.View`. `Toast` → `android.widget.Toast`. `Intent` → `android.content.Intent`.

---

# B. Studi Kasus Lengkap: Aplikasi "Tokoku"

## Soal

> Buat aplikasi daftar produk kafe.
> 1. Halaman utama menampilkan judul dan **daftar produk** (nama dan harga) memakai **RecyclerView**.
> 2. Saat produk diklik, buka **halaman detail** yang menampilkan nama, harga, dan deskripsi produk tersebut.
> 3. Halaman detail punya tombol **Kembali** dan tombol **Cari di Google** (membuka browser).
> 4. Gunakan **warna, style, dan theme custom**.

## Peta file

| No | File | Topik |
|---|---|---|
| 0 | Buat `DetailActivity` lewat wizard (A.3) | Multiple Activity |
| 1 | `res/values/colors.xml` | Themes |
| 2 | `res/values/styles.xml` | Themes |
| 3 | `res/values/themes.xml` dan `res/values-night/themes.xml` | Themes |
| 4 | `Produk.java` | RecyclerView (model) |
| 5 | `res/layout/item_produk.xml` | RecyclerView (layout satu item) |
| 6 | `ProdukAdapter.java` | RecyclerView (Adapter + ViewHolder) |
| 7 | `res/layout/activity_main.xml` | RecyclerView |
| 8 | `MainActivity.java` | RecyclerView + Multiple Activity |
| 9 | `res/layout/activity_detail.xml` | Multiple Activity + Themes |
| 10 | `DetailActivity.java` | Multiple Activity |
| 11 | `AndroidManifest.xml` | Multiple Activity + Themes |

> Baris `package com.example.tokoku;` di setiap file Java **disesuaikan** dengan package project kamu. Biarkan baris `package` yang dibuat Android Studio, jangan diganti.

---

## Langkah 1: `colors.xml`

```xml
<!-- File: res/values/colors.xml -->
<resources>
    <color name="black">#FF000000</color>
    <color name="white">#FFFFFFFF</color>
    <color name="coklat_tua">#FF5D4037</color>
    <color name="oranye">#FFE65100</color>
    <color name="krem">#FFFFF8E1</color>
</resources>
```

`black` dan `white` sudah ada dari template. Tambahkan warna sendiri dengan format `#AARRGGBB`.

---

## Langkah 2: `styles.xml`

```xml
<!-- File: res/values/styles.xml -->
<resources>
    <!-- Style dasar: mewarisi style bawaan dengan parent -->
    <style name="JudulHalaman" parent="Widget.AppCompat.TextView">
        <item name="android:textSize">24sp</item>
        <item name="android:textStyle">bold</item>
        <item name="android:textColor">@color/coklat_tua</item>
    </style>

    <!-- Mewarisi style sendiri dengan titik: tetap bold dan coklat, ukurannya diganti -->
    <style name="JudulHalaman.Item">
        <item name="android:textSize">18sp</item>
    </style>

    <style name="HargaText" parent="Widget.AppCompat.TextView">
        <item name="android:textSize">16sp</item>
        <item name="android:textColor">@color/oranye</item>
    </style>
</resources>
```

Dua cara pewarisan sama persis dengan project 06a dosen (`MyJumboText` dan `MyJumboText.blue`):
- Mewarisi style **bawaan** → `parent="..."`.
- Mewarisi style **buatan sendiri** → nama diawali nama induk + titik. `JudulHalaman.Item` hasilnya **18sp, bold, coklat**.

---

## Langkah 3: `themes.xml`

```xml
<!-- File: res/values/themes.xml -->
<resources>
    <style name="Base.Theme.Tokoku" parent="Theme.Material3.Light">
        <item name="colorPrimary">@color/coklat_tua</item>
        <item name="android:windowBackground">@color/krem</item>
    </style>

    <style name="Theme.Tokoku" parent="Base.Theme.Tokoku" />
</resources>
```

- Nama `Base.Theme.Tokoku` dan `Theme.Tokoku` mengikuti nama project. Di project kamu namanya `Base.Theme.NamaProjectKamu`. **Ubah isinya saja, jangan ganti namanya**, karena nama itu sudah dipakai di manifest.
- Template aslinya memakai parent `Theme.Material3.DayNight.NoActionBar`, jadi **tidak ada Action Bar**. Di sini diganti `Theme.Material3.Light` supaya ada Action Bar (judul di atas layar) dan tampilannya selalu terang.
- `colorPrimary` → warna utama. Di Material3, warna ini dipakai sebagai warna background **Button**.
- `android:windowBackground` → warna background semua layar.

> ⚠️ **Jebakan dark mode** (sama seperti project 06a): template juga punya `res/values-night/themes.xml` yang dipakai saat HP dalam mode gelap. Isinya masih `NoActionBar`. Kalau tidak diubah, saat dark mode Action Bar hilang dan warnamu tidak berlaku. **Solusi:** samakan isinya dengan `values/themes.xml`, atau hapus file `values-night/themes.xml`.

```xml
<!-- File: res/values-night/themes.xml -->
<resources>
    <style name="Base.Theme.Tokoku" parent="Theme.Material3.Light">
        <item name="colorPrimary">@color/coklat_tua</item>
        <item name="android:windowBackground">@color/krem</item>
    </style>
</resources>
```

---

## Langkah 4: Model `Produk.java`

```java
// File: Produk.java
package com.example.tokoku;

public class Produk {
    String nama;
    int harga;
    String deskripsi;

    public Produk(String nama, int harga, String deskripsi) {
        this.nama = nama;
        this.harga = harga;
        this.deskripsi = deskripsi;
    }

    public String getNama() {
        return nama;
    }

    public int getHarga() {
        return harga;
    }

    public String getDeskripsi() {
        return deskripsi;
    }
}
```

Cara cepat: tulis 3 atributnya saja, lalu **Alt+Insert → Constructor** (pilih semua) dan **Alt+Insert → Getter** (pilih semua).

---

## Langkah 5: Layout satu item `item_produk.xml`

Buat file: klik kanan `res/layout` → **New → Layout Resource File** → nama `item_produk`, root element `LinearLayout`.

```xml
<!-- File: res/layout/item_produk.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:padding="16dp">

    <TextView
        android:id="@+id/tvNamaProduk"
        style="@style/JudulHalaman.Item"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Nama produk" />

    <TextView
        android:id="@+id/tvHargaProduk"
        style="@style/HargaText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Rp 0" />
</LinearLayout>
```

> ⚠️ `android:layout_height` root item **harus `wrap_content`**. Kalau `match_parent`, satu produk memenuhi satu layar penuh.

---

## Langkah 6: `ProdukAdapter.java`

```java
// File: ProdukAdapter.java
package com.example.tokoku;

import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;

import androidx.annotation.NonNull;
import androidx.recyclerview.widget.RecyclerView;

import java.util.ArrayList;

public class ProdukAdapter extends RecyclerView.Adapter<ProdukAdapter.ProdukViewHolder> {

    ArrayList<Produk> listProduk;
    OnItemClickListener listener;

    // "Kontrak" supaya MainActivity yang menentukan aksi saat item diklik
    public interface OnItemClickListener {
        void onItemClick(Produk produk);
    }

    public ProdukAdapter(ArrayList<Produk> listProduk, OnItemClickListener listener) {
        this.listProduk = listProduk;
        this.listener = listener;
    }

    // 1. Membuat tampilan satu item dari item_produk.xml
    @NonNull
    @Override
    public ProdukViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.item_produk, parent, false);
        return new ProdukViewHolder(view);
    }

    // 2. Mengisi data produk ke tampilan sesuai posisinya
    @Override
    public void onBindViewHolder(@NonNull ProdukViewHolder holder, int position) {
        Produk produk = listProduk.get(position);
        holder.tvNamaProduk.setText(produk.getNama());
        holder.tvHargaProduk.setText("Rp " + produk.getHarga());

        holder.itemView.setOnClickListener(v -> {
            if (listener != null) {
                listener.onItemClick(produk);
            }
        });
    }

    // 3. Jumlah data
    @Override
    public int getItemCount() {
        return listProduk.size();
    }

    public class ProdukViewHolder extends RecyclerView.ViewHolder {
        TextView tvNamaProduk, tvHargaProduk;

        public ProdukViewHolder(@NonNull View itemView) {
            super(itemView);
            tvNamaProduk = itemView.findViewById(R.id.tvNamaProduk);
            tvHargaProduk = itemView.findViewById(R.id.tvHargaProduk);
        }
    }
}
```

Urutan mengetik yang paling cepat:
1. Tulis class `ProdukViewHolder` di bagian bawah dulu.
2. Tulis `extends RecyclerView.Adapter<ProdukAdapter.ProdukViewHolder>` di baris class. Baris ini akan merah.
3. Klik baris merah itu → **Alt+Enter → Implement methods** → 3 method dibuat otomatis, tinggal diisi.

> ⚠️ `setText("Rp " + produk.getHarga())` aman karena hasilnya String. **`setText(produk.getHarga())` (int saja) membuat crash**, karena `setText(int)` dianggap ID resource string (`Resources$NotFoundException`). Selalu ubah angka jadi String: `"Rp " + harga` atau `String.valueOf(harga)`.

---

## Langkah 7: `activity_main.xml`

```xml
<!-- File: res/layout/activity_main.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/tvJudul"
        style="@style/JudulHalaman"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp"
        android:text="Menu Tokoku" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rvProduk"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1" />
</LinearLayout>
```

`layout_height="0dp"` + `layout_weight="1"` membuat RecyclerView mengisi **sisa ruang** di bawah judul. Kalau ditulis `match_parent`, RecyclerView setinggi layar penuh dan item paling bawah bisa terpotong sebesar tinggi judul.

---

## Langkah 8: `MainActivity.java`

```java
// File: MainActivity.java
package com.example.tokoku;

import android.content.Intent;
import android.os.Bundle;

import androidx.appcompat.app.AppCompatActivity;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {

    RecyclerView rvProduk;
    ArrayList<Produk> listProduk;
    ProdukAdapter adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 1. Siapkan data
        listProduk = new ArrayList<>();
        listProduk.add(new Produk("Kopi Susu", 18000, "Espresso dengan susu segar dan gula aren."));
        listProduk.add(new Produk("Teh Tarik", 15000, "Teh hitam dengan susu kental manis."));
        listProduk.add(new Produk("Roti Bakar", 20000, "Roti bakar isi cokelat dan keju."));
        listProduk.add(new Produk("Matcha Latte", 22000, "Bubuk matcha Jepang dengan susu."));

        // 2. Ambil RecyclerView, pasang LayoutManager dan Adapter
        rvProduk = findViewById(R.id.rvProduk);
        rvProduk.setLayoutManager(new LinearLayoutManager(this));

        adapter = new ProdukAdapter(listProduk, produk -> {
            // 3. Item diklik: pindah ke DetailActivity sambil membawa data
            Intent intent = new Intent(this, DetailActivity.class);
            intent.putExtra(DetailActivity.KEY_NAMA, produk.getNama());
            intent.putExtra(DetailActivity.KEY_HARGA, produk.getHarga());
            intent.putExtra(DetailActivity.KEY_DESKRIPSI, produk.getDeskripsi());
            startActivity(intent);
        });
        rvProduk.setAdapter(adapter);
    }
}
```

Hal penting:
- **Key memakai konstanta** (`DetailActivity.KEY_NAMA`), bukan string yang diketik dua kali. Kalau salah ketik, Android Studio langsung memberi error merah. Dengan string biasa, salah ketik baru ketahuan saat layar detail menampilkan `null`.
- Di dalam lambda (`produk -> { ... }`), `this` tetap berarti `MainActivity`. Kalau kamu memakai anonymous class (`new ProdukAdapter.OnItemClickListener() { ... }`), `this` berubah arti menjadi listener tersebut. Di situ harus ditulis **`MainActivity.this`**.

**▶ Checkpoint:** Run (Shift+F10). List produk harus tampil, dan mengklik produk membuka halaman detail (masih kosong).

---

## Langkah 9: `activity_detail.xml`

```xml
<!-- File: res/layout/activity_detail.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="24dp"
    tools:context=".DetailActivity">

    <TextView
        android:id="@+id/tvNamaDetail"
        style="@style/JudulHalaman"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Nama" />

    <TextView
        android:id="@+id/tvHargaDetail"
        style="@style/HargaText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:text="Rp 0" />

    <TextView
        android:id="@+id/tvDeskripsiDetail"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="Deskripsi" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:orientation="horizontal">

        <Button
            android:id="@+id/btnKembali"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Kembali" />

        <Button
            android:id="@+id/btnCariGoogle"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginStart="8dp"
            android:layout_weight="1"
            android:text="Cari di Google" />
    </LinearLayout>
</LinearLayout>
```

Dua tombol memakai `layout_width="0dp"` + `layout_weight="1"`, jadi lebarnya persis setengah-setengah. Warnanya otomatis coklat dari `colorPrimary` di theme.

---

## Langkah 10: `DetailActivity.java`

```java
// File: DetailActivity.java
package com.example.tokoku;

import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.widget.Button;
import android.widget.TextView;

import androidx.appcompat.app.ActionBar;
import androidx.appcompat.app.AppCompatActivity;

public class DetailActivity extends AppCompatActivity {

    // Key untuk Intent, dipakai oleh MainActivity (pengirim) dan DetailActivity (penerima)
    public static final String KEY_NAMA = "keyNama";
    public static final String KEY_HARGA = "keyHarga";
    public static final String KEY_DESKRIPSI = "keyDeskripsi";

    TextView tvNamaDetail, tvHargaDetail, tvDeskripsiDetail;
    Button btnKembali, btnCariGoogle;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_detail);

        tvNamaDetail = findViewById(R.id.tvNamaDetail);
        tvHargaDetail = findViewById(R.id.tvHargaDetail);
        tvDeskripsiDetail = findViewById(R.id.tvDeskripsiDetail);
        btnKembali = findViewById(R.id.btnKembali);
        btnCariGoogle = findViewById(R.id.btnCariGoogle);

        // Ambil data yang dikirim MainActivity
        Intent intent = getIntent();
        String nama = intent.getStringExtra(KEY_NAMA);
        int harga = intent.getIntExtra(KEY_HARGA, 0);
        String deskripsi = intent.getStringExtra(KEY_DESKRIPSI);

        tvNamaDetail.setText(nama);
        tvHargaDetail.setText("Rp " + harga);
        tvDeskripsiDetail.setText(deskripsi);

        // Judul dan tombol panah back di Action Bar (dicek null karena theme bisa NoActionBar)
        ActionBar actionBar = getSupportActionBar();
        if (actionBar != null) {
            actionBar.setTitle(nama);
            actionBar.setDisplayHomeAsUpEnabled(true);
        }

        // Tombol Kembali: tutup Activity ini, kembali ke MainActivity
        btnKembali.setOnClickListener(v -> finish());

        // Implicit Intent: buka browser
        btnCariGoogle.setOnClickListener(v -> {
            Uri uri = Uri.parse("https://www.google.com/search?q=" + Uri.encode(nama));
            Intent intentBrowser = new Intent(Intent.ACTION_VIEW, uri);
            startActivity(intentBrowser);
        });
    }

    // Tombol panah back di Action Bar
    @Override
    public boolean onSupportNavigateUp() {
        finish();
        return true;
    }
}
```

Hal penting:
- `getIntExtra(KEY_HARGA, 0)`: angka `0` adalah nilai default kalau data tidak ditemukan. `getStringExtra` tidak punya nilai default, dan hasilnya `null` kalau key salah.
- Tipe pengirim dan penerima **harus sama**. Harga dikirim sebagai `int`, jadi dibaca dengan `getIntExtra`. Kalau dibaca dengan `getStringExtra`, hasilnya `null`.
- `finish()` menutup Activity sekarang. Jangan memakai `startActivity(new Intent(this, MainActivity.class))` untuk "kembali", karena itu membuat MainActivity **baru** dan menumpuk layar.
- `Uri.encode(nama)` mengubah spasi dan karakter khusus supaya aman dipakai di URL.

---

## Langkah 11: `AndroidManifest.xml`

Kalau `DetailActivity` dibuat lewat wizard, `<activity>`-nya sudah ada. Tambahkan `android:label` kalau ingin.

```xml
<!-- File: AndroidManifest.xml -->
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="Tokoku"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Tokoku">

        <activity
            android:name=".DetailActivity"
            android:exported="false"
            android:label="Detail Produk" />

        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```

- `android:theme` di `<application>` berlaku untuk **semua** Activity.
- `intent-filter` MAIN + LAUNCHER menentukan Activity yang dibuka pertama kali. Hanya satu Activity yang memilikinya.

**▶ Checkpoint akhir:** Run. Klik produk → detail tampil dengan data yang benar → tombol Kembali dan panah back kembali ke list → tombol Cari di Google membuka browser.

---

# C. Latihan Variasi Soal

Kerjakan dengan memodifikasi project Tokoku, atau buat project baru. Coba dulu tanpa membuka kunci.

## Latihan 1: Data mahasiswa dalam grid 2 kolom

**Soal:** Tampilkan daftar mahasiswa (nama, NIM, jurusan) dalam **grid 2 kolom**. Klik mahasiswa → halaman detail menampilkan ketiga datanya.

**Kunci jawaban:**

Polanya sama dengan Tokoku. Yang berubah:
1. Model `Mahasiswa` dengan atribut `nama`, `nim`, `jurusan` (ketiganya String).
2. Layout item berisi 3 TextView.
3. Di Adapter, ganti semua `Produk` menjadi `Mahasiswa`.
4. LayoutManager diganti:

```java
rvMahasiswa.setLayoutManager(new GridLayoutManager(this, 2));
```

Import: `androidx.recyclerview.widget.GridLayoutManager`.

5. Kirim tiga String dengan `putExtra`, baca dengan tiga `getStringExtra`.


## Latihan 2: Halaman login sebelum daftar produk

**Soal:** Tambahkan `LoginActivity` sebagai halaman pertama. User mengisi username lalu menekan **Masuk**. Kalau username kosong, tampilkan Toast "Username wajib diisi". Kalau terisi, buka `MainActivity` dan tampilkan "Halo, [username]" di judulnya.

**Kunci jawaban:**

**1. Buat `LoginActivity` lewat wizard**, lalu layout-nya:

```xml
<!-- File: res/layout/activity_login.xml (Latihan 2) -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    android:padding="24dp">

    <EditText
        android:id="@+id/etUsername"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Username" />

    <Button
        android:id="@+id/btnMasuk"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="Masuk" />
</LinearLayout>
```

**2. `LoginActivity.java`:**

```java
// File: LoginActivity.java (Latihan 2)
package com.example.tokoku;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

public class LoginActivity extends AppCompatActivity {

    public static final String KEY_USERNAME = "keyUsername";

    EditText etUsername;
    Button btnMasuk;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_login);

        etUsername = findViewById(R.id.etUsername);
        btnMasuk = findViewById(R.id.btnMasuk);

        btnMasuk.setOnClickListener(v -> {
            String username = etUsername.getText().toString().trim();
            if (username.isEmpty()) {
                Toast.makeText(this, "Username wajib diisi", Toast.LENGTH_SHORT).show();
                return;
            }
            Intent intent = new Intent(this, MainActivity.class);
            intent.putExtra(KEY_USERNAME, username);
            startActivity(intent);
        });
    }
}
```

**3. Di `MainActivity.onCreate`**, setelah `setContentView`:

```java
TextView tvJudul = findViewById(R.id.tvJudul);
String username = getIntent().getStringExtra(LoginActivity.KEY_USERNAME);
tvJudul.setText("Halo, " + username);
```

**4. Pindahkan `intent-filter` di manifest** dari MainActivity ke LoginActivity, supaya LoginActivity yang dibuka pertama:

```xml
<!-- File: AndroidManifest.xml (Latihan 2) -->
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
    <application
        android:icon="@mipmap/ic_launcher"
        android:label="Tokoku"
        android:theme="@style/Theme.Tokoku">

        <activity
            android:name=".LoginActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <activity
            android:name=".MainActivity"
            android:exported="false" />

        <activity
            android:name=".DetailActivity"
            android:exported="false" />
    </application>
</manifest>
```

> Bonus: kalau user menekan Back di MainActivity, dia kembali ke halaman login. Kalau soal meminta login **tidak bisa** dibuka lagi dengan Back, tambahkan `finish();` setelah `startActivity(intent);` di LoginActivity.


## Latihan 3: Tambah dan hapus produk

**Soal:** Di halaman utama tambahkan input nama, input harga, dan tombol **Tambah**. Produk baru langsung muncul di list. Validasi: kalau ada input kosong, tampilkan Toast.
**Variasi:** mengklik produk akan **menghapus** produk itu dari list (gantikan pindah ke detail).

**Kunci jawaban:**

**1. Tambahkan di `activity_main.xml`**, di antara judul dan RecyclerView:

```xml
<!-- File: res/layout/activity_main.xml (Latihan 3) -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:id="@+id/tvJudul"
        style="@style/JudulHalaman"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp"
        android:text="Menu Tokoku" />

    <EditText
        android:id="@+id/etNama"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Nama produk" />

    <EditText
        android:id="@+id/etHarga"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Harga"
        android:inputType="number" />

    <Button
        android:id="@+id/btnTambah"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Tambah" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rvProduk"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1" />
</LinearLayout>
```

`android:inputType="number"` membuat keyboard hanya angka, jadi `Integer.parseInt` aman.

**2. `MainActivity.java`:**

```java
// File: MainActivity.java (Latihan 3)
package com.example.tokoku;

import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {

    RecyclerView rvProduk;
    ArrayList<Produk> listProduk;
    ProdukAdapter adapter;
    EditText etNama, etHarga;
    Button btnTambah;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        etNama = findViewById(R.id.etNama);
        etHarga = findViewById(R.id.etHarga);
        btnTambah = findViewById(R.id.btnTambah);

        listProduk = new ArrayList<>();
        listProduk.add(new Produk("Kopi Susu", 18000, "Espresso dengan susu."));

        rvProduk = findViewById(R.id.rvProduk);
        rvProduk.setLayoutManager(new LinearLayoutManager(this));

        // Variasi: klik item = hapus item
        adapter = new ProdukAdapter(listProduk, produk -> {
            int index = listProduk.indexOf(produk);
            listProduk.remove(index);
            adapter.notifyItemRemoved(index);
            Toast.makeText(this, produk.getNama() + " dihapus", Toast.LENGTH_SHORT).show();
        });
        rvProduk.setAdapter(adapter);

        btnTambah.setOnClickListener(v -> {
            String nama = etNama.getText().toString().trim();
            String hargaText = etHarga.getText().toString().trim();

            if (nama.isEmpty() || hargaText.isEmpty()) {
                Toast.makeText(this, "Nama dan harga wajib diisi", Toast.LENGTH_SHORT).show();
                return;
            }

            int harga = Integer.parseInt(hargaText);
            listProduk.add(new Produk(nama, harga, "Produk baru"));
            adapter.notifyItemInserted(listProduk.size() - 1);

            etNama.setText("");
            etHarga.setText("");
        });
    }
}
```

Hal penting:
- **Setelah ArrayList diubah, Adapter wajib diberi tahu.** Tanpa `notifyItemInserted` / `notifyItemRemoved`, data di ArrayList berubah tapi tampilan tidak. Kalau bingung harus pakai yang mana, `adapter.notifyDataSetChanged();` selalu bisa dipakai (menggambar ulang seluruh list).
- `adapter` dijadikan **field** (ditulis di luar `onCreate`). Kalau `adapter` adalah variabel lokal, `adapter.notifyItemRemoved(...)` di dalam lambda-nya sendiri error "variable might not have been initialized".


## Latihan 4: Theme berbeda untuk halaman detail

**Soal:**
1. Halaman detail memakai **background putih** dan warna tombol **oranye**, sedangkan halaman utama tetap krem dan coklat.
2. Buat style `JudulHalaman.Merah` yang sama dengan `JudulHalaman` tetapi berwarna merah, lalu pakai untuk nama produk di halaman detail.

**Kunci jawaban:**

**1. Theme khusus detail di `themes.xml`.** Mewarisi theme aplikasi dengan titik, lalu menimpa dua item:

```xml
<!-- File: res/values/themes.xml (Latihan 4) -->
<resources>
    <style name="Base.Theme.Tokoku" parent="Theme.Material3.Light">
        <item name="colorPrimary">@color/coklat_tua</item>
        <item name="android:windowBackground">@color/krem</item>
    </style>

    <style name="Theme.Tokoku" parent="Base.Theme.Tokoku" />

    <style name="Theme.Tokoku.Detail">
        <item name="colorPrimary">@color/oranye</item>
        <item name="android:windowBackground">@color/white</item>
    </style>
</resources>
```

**2. Pasang di `<activity>` DetailActivity di manifest.** Theme di `<activity>` mengalahkan theme di `<application>`:

```xml
<!-- File: AndroidManifest.xml (Latihan 4) -->
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
    <application
        android:icon="@mipmap/ic_launcher"
        android:label="Tokoku"
        android:theme="@style/Theme.Tokoku">

        <activity
            android:name=".DetailActivity"
            android:exported="false"
            android:theme="@style/Theme.Tokoku.Detail" />

        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```

**3. Style baru di `styles.xml`:**

```xml
<!-- File: res/values/styles.xml (Latihan 4) -->
<resources>
    <style name="JudulHalaman" parent="Widget.AppCompat.TextView">
        <item name="android:textSize">24sp</item>
        <item name="android:textStyle">bold</item>
        <item name="android:textColor">@color/coklat_tua</item>
    </style>

    <style name="JudulHalaman.Item">
        <item name="android:textSize">18sp</item>
    </style>

    <style name="JudulHalaman.Merah">
        <item name="android:textColor">#FFD32F2F</item>
    </style>

    <style name="HargaText" parent="Widget.AppCompat.TextView">
        <item name="android:textSize">16sp</item>
        <item name="android:textColor">@color/oranye</item>
    </style>
</resources>
```

**4. Di `activity_detail.xml`**, ganti style TextView nama:

```xml
style="@style/JudulHalaman.Merah"
```

Hasilnya 24sp, bold (diwarisi), merah (ditimpa).

> ⚠️ Kalau soal meminta halaman **tanpa Action Bar**, pakai parent `Theme.Material3.Light.NoActionBar`. Di Activity itu `getSupportActionBar()` menghasilkan `null`, jadi wajib dicek `if (actionBar != null)` seperti di DetailActivity.


## Latihan 5: Perbaiki kode (debugging)

**Soal:** Kode di bawah dimaksudkan menampilkan list dan membuka detail saat item diklik. Ada **6 kesalahan**. Temukan dan perbaiki semuanya.

```java
// MainActivity.java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    rvProduk = findViewById(R.id.rvProduk);
    setContentView(R.layout.activity_main);

    listProduk = new ArrayList<>();
    listProduk.add(new Produk("Kopi Susu", 18000, "Enak"));

    adapter = new ProdukAdapter(listProduk, produk -> {
        Intent intent = new Intent(this, DetailActivity.class);
        intent.putExtra("keyNama", produk.getNama());
        intent.putExtra("keyHarga", produk.getHarga());
        startActivity(intent);
    });
    rvProduk.setAdapter(adapter);
}
```

```java
// ProdukAdapter.java (sebagian)
@Override
public void onBindViewHolder(@NonNull ProdukViewHolder holder, int position) {
    Produk produk = listProduk.get(position);
    holder.tvNamaProduk.setText(produk.getNama());
    holder.tvHargaProduk.setText(produk.getHarga());
    holder.itemView.setOnClickListener(v -> listener.onItemClick(produk));
}

@Override
public int getItemCount() {
    return 0;
}
```

```java
// DetailActivity.java (sebagian)
String nama = getIntent().getStringExtra("namaProduk");
int harga = getIntent().getIntExtra("keyHarga", 0);
```

```xml
<!-- AndroidManifest.xml: DetailActivity tidak terdaftar -->
<application android:theme="@style/Theme.Tokoku">
    <activity android:name=".MainActivity" android:exported="true">
        <intent-filter>
            <action android:name="android.intent.action.MAIN" />
            <category android:name="android.intent.category.LAUNCHER" />
        </intent-filter>
    </activity>
</application>
```

**Kunci jawaban:**

| No | Kesalahan | Akibat | Perbaikan |
|---|---|---|---|
| 1 | `findViewById` dipanggil sebelum `setContentView` | `rvProduk` bernilai null, crash `NullPointerException` saat `setAdapter` | Pindahkan `setContentView` tepat setelah `super.onCreate` |
| 2 | Tidak ada `setLayoutManager` | List tidak tampil | Tambah `rvProduk.setLayoutManager(new LinearLayoutManager(this));` |
| 3 | `setText(produk.getHarga())` dengan int | Crash `Resources$NotFoundException` | `setText("Rp " + produk.getHarga())` |
| 4 | `getItemCount()` mengembalikan `0` | List kosong | `return listProduk.size();` |
| 5 | Key `"namaProduk"` ≠ `"keyNama"` | Nama di detail `null` | Samakan key, sebaiknya pakai konstanta |
| 6 | DetailActivity tidak terdaftar di manifest | Crash `ActivityNotFoundException` saat item diklik | Tambah `<activity android:name=".DetailActivity" android:exported="false" />` |


---

# D. Debug Cepat

## Cara membaca error saat aplikasi crash

1. Buka tab **Logcat** di bagian bawah Android Studio.
2. Ubah filter level menjadi **Error**, atau ketik `FATAL EXCEPTION` di kotak pencarian.
3. Cari baris **`Caused by:`**. Itu penyebab aslinya.
4. Di bawahnya cari baris yang menyebut file kamu, misalnya `at com.example.tokoku.DetailActivity.onCreate(DetailActivity.java:42)`. Klik tulisan biru itu untuk langsung lompat ke baris yang bermasalah.

## Gejala → penyebab → solusi

| Gejala | Penyebab paling mungkin | Solusi |
|---|---|---|
| Crash saat aplikasi dibuka, `NullPointerException` | `findViewById` sebelum `setContentView`, atau id di Java tidak ada di layout yang dipasang | Panggil `setContentView` dulu. Cek id di XML |
| Crash saat dibuka, error menyebut `R.id.main` / `setOnApplyWindowInsetsListener` | Kode EdgeToEdge template masih ada, tapi id `main` di root layout terhapus | Hapus kode EdgeToEdge, atau kembalikan `android:id="@+id/main"` |
| `ActivityNotFoundException` | Activity tujuan tidak ada di manifest | Tambahkan `<activity android:name=".NamaActivity" />` |
| `Resources$NotFoundException` | `setText(angka)` | `setText(String.valueOf(angka))` atau `"Rp " + angka` |
| `NumberFormatException` | `Integer.parseInt` pada teks kosong atau huruf | Cek `isEmpty()` dulu, pakai `inputType="number"` |
| RecyclerView kosong | Lupa `setLayoutManager`, lupa `setAdapter`, `getItemCount()` salah, atau ArrayList kosong | Cek keempatnya |
| Data baru tidak muncul di list | Lupa `notifyItemInserted` / `notifyDataSetChanged` | Panggil setelah `listProduk.add(...)` |
| Satu item memenuhi layar | Tinggi root layout item `match_parent` | Ubah jadi `wrap_content` |
| Item paling bawah terpotong | RecyclerView `match_parent` di bawah View lain dalam LinearLayout | `layout_height="0dp"` + `layout_weight="1"` |
| Detail menampilkan `null` | Key `putExtra` ≠ key `getStringExtra`, atau tipe beda (kirim int, baca String) | Samakan key dan tipe |
| Detail menampilkan `0` padahal harga ada | Key salah, sehingga `getIntExtra` memakai nilai default | Samakan key |
| Klik item tidak terjadi apa-apa | `setOnClickListener` belum dipasang di `holder.itemView`, atau listener tidak dipanggil | Cek `onBindViewHolder` |
| Error "incompatible types" di `getSupportActionBar()` | Salah import `android.app.ActionBar` | Ganti ke `androidx.appcompat.app.ActionBar` |
| Crash di `getSupportActionBar().setTitle(...)` | Theme `NoActionBar`, ActionBar bernilai null | Cek `if (actionBar != null)`, atau pakai theme dengan Action Bar |
| Action Bar / menu hilang | Theme `NoActionBar`, atau `values-night/themes.xml` masih `NoActionBar` saat dark mode | Ganti parent theme di **kedua** file |
| Warna theme tidak berubah | Yang diedit `values/themes.xml`, tapi HP dalam dark mode memakai `values-night` | Edit juga `values-night/themes.xml`, atau hapus file itu |
| Style tidak berlaku | Salah tulis nama, atau ditulis `android:style` | Tulis `style="@style/NamaStyle"` (tanpa `android:`) |
| "Kembali" membuat layar menumpuk | Kembali memakai `startActivity` ke MainActivity | Pakai `finish()` |
| "variable might not have been initialized" | `adapter` lokal dipakai di dalam lambda-nya sendiri | Jadikan `adapter` field |

---

# E. Checklist Saat Ujian

**Sebelum ngoding (3 menit)**
- [ ] Baca soal sampai habis. Coret-coret: ada berapa layar, data apa saja per item, tombol apa saja.
- [ ] Tentukan nama class model dan atributnya (String atau int).

**Urutan kerja**
1. [ ] Buat project: **Empty Views Activity, Java**.
2. [ ] Buat semua Activity tambahan lewat **wizard** (otomatis masuk manifest).
3. [ ] Hapus kode EdgeToEdge di setiap Activity (atau pastikan root layout punya `android:id="@+id/main"`).
4. [ ] Theme: ubah parent di `values/themes.xml` **dan** `values-night/themes.xml`. Tambah warna di `colors.xml`, style di `styles.xml`.
5. [ ] Model → layout item → Adapter → `activity_main.xml` → `MainActivity`.
6. [ ] **Run.** Pastikan list tampil sebelum lanjut.
7. [ ] Klik item → `putExtra` → Activity detail → `getXxxExtra`.
8. [ ] **Run lagi.** Uji setiap tombol.

**Sebelum dikumpulkan**
- [ ] Semua Activity ada di manifest.
- [ ] Key `putExtra` sama dengan key `getXxxExtra`, dan tipenya sama.
- [ ] Tidak ada `setText(int)`.
- [ ] Ada `setLayoutManager` dan `setAdapter`.
- [ ] Root layout item `wrap_content`.
- [ ] Coba aplikasinya sekali lagi dari awal sampai akhir.

---

# Template Kosong untuk Dihafal

Ganti `Xxx` dengan nama datamu.

```java
// Adapter
public class XxxAdapter extends RecyclerView.Adapter<XxxAdapter.XxxViewHolder> {
    ArrayList<Xxx> list;
    OnItemClickListener listener;

    public interface OnItemClickListener { void onItemClick(Xxx data); }

    public XxxAdapter(ArrayList<Xxx> list, OnItemClickListener listener) {
        this.list = list;
        this.listener = listener;
    }

    @NonNull
    @Override
    public XxxViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_xxx, parent, false);
        return new XxxViewHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull XxxViewHolder holder, int position) {
        Xxx data = list.get(position);
        holder.tvSesuatu.setText(data.getSesuatu());
        holder.itemView.setOnClickListener(v -> listener.onItemClick(data));
    }

    @Override
    public int getItemCount() { return list.size(); }

    public class XxxViewHolder extends RecyclerView.ViewHolder {
        TextView tvSesuatu;
        public XxxViewHolder(@NonNull View itemView) {
            super(itemView);
            tvSesuatu = itemView.findViewById(R.id.tvSesuatu);
        }
    }
}
```

```java
// Di Activity
rv = findViewById(R.id.rvXxx);
rv.setLayoutManager(new LinearLayoutManager(this));   // atau new GridLayoutManager(this, 2)
rv.setAdapter(new XxxAdapter(list, data -> {
    Intent intent = new Intent(this, DetailActivity.class);
    intent.putExtra("keySesuatu", data.getSesuatu());
    startActivity(intent);
}));
```

```java
// Di DetailActivity
String sesuatu = getIntent().getStringExtra("keySesuatu");
int angka = getIntent().getIntExtra("keyAngka", 0);
```

```java
// Implicit Intent (browser)
startActivity(new Intent(Intent.ACTION_VIEW, Uri.parse("https://www.google.com")));
```

```xml
<!-- Style -->
<style name="Induk" parent="Widget.AppCompat.TextView"> ... </style>
<style name="Induk.Anak"> ... </style>

<!-- Theme -->
<style name="Base.Theme.App" parent="Theme.Material3.Light">
    <item name="colorPrimary">@color/warnaku</item>
    <item name="android:windowBackground">@color/warnaBg</item>
</style>

<!-- Manifest -->
<application android:theme="@style/Theme.App"> ... </application>
<activity android:name=".DetailActivity" android:theme="@style/Theme.App.Detail" />
```

Semangat UTS-nya! 💪
