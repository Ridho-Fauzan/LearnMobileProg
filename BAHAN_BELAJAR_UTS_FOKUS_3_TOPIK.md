# UTS Mobile Programming — Fokus 3 Topik

## Kisi-kisi
1. **RecyclerView** — sample code `MyApplication05c`
2. **Multiple Activity** — sample code `MyApplication02c`
3. **Themes** — sample code `MyApplication06a`

> Gunakan file ini untuk belajar H-1 UTS. Materi di luar tiga topik ini sengaja tidak dimasukkan.

---

# Cara belajar cepat

| Urutan | Waktu | Target |
|---|---:|---|
| RecyclerView | 45 menit | Paham komponen, 3 method Adapter, dan bisa merangkai kode dasar |
| Multiple Activity | 30 menit | Bisa pindah Activity, kirim data, dan tahu fungsi Manifest |
| Themes | 25 menit | Bisa bedakan Style vs Theme serta inheritance |
| Latihan | 30 menit | Kerjakan tanpa lihat jawaban, lalu cek kesalahan |

---

# 1. RecyclerView

## Inti konsep

**RecyclerView** adalah list yang bisa di-scroll untuk menampilkan banyak data secara efisien.

Contoh: daftar lagu, produk, mahasiswa, kontak, atau chat.

Kalau ada 100 lagu, RecyclerView tidak membuat 100 View sekaligus. Ia hanya membuat item yang sedang terlihat, lalu memakai ulang item yang keluar layar. Itu sebabnya disebut **Recycler**View.

## Komponen yang wajib hafal ⭐

| Komponen | Contoh di project 05c | Fungsi |
|---|---|---|
| **Data** | `ArrayList<Song>` | Menyimpan seluruh data |
| **Model / POJO** | `Song.java` | Bentuk satu data, yaitu title dan artist |
| **RecyclerView** | `rvSongs` | Wadah list yang tampil di layar |
| **Layout item** | `row_song.xml` | Tampilan untuk satu baris lagu |
| **Adapter** | `SongAdapter.java` | Jembatan antara data dan tampilan |
| **ViewHolder** | `SongViewHolder` | Menyimpan View pada satu item |
| **LayoutManager** | `LinearLayoutManager` | Mengatur susunan item |

Untuk jawaban paling aman, sebut semua tujuh komponen ini.

```
Data (ArrayList<Song>)
          ↓
      Adapter
          ↓
ViewHolder + row_song.xml
          ↓
RecyclerView + LayoutManager
          ↓
      tampil di layar
```

---

## Model: `Song.java`

Model adalah class yang menggambarkan satu data.

```java
public class Song {
    String title;
    String artist;

    public Song(String title, String artist) {
        this.title = title;
        this.artist = artist;
    }

    public String getTitle() { return title; }
    public String getArtist() { return artist; }
}
```

- **POJO** = *Plain Old Java Object*, class Java biasa yang menyimpan data.
- Constructor dipanggil saat objek dibuat: `new Song("Hati-Hati di Jalan", "Tulus")`.
- Getter mengambil nilai, misalnya `song.getTitle()`.

---

## Layout satu item: `row_song.xml`

`row_song.xml` adalah tampilan untuk **satu lagu**, bukan seluruh layar.

```xml
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <TextView
        android:id="@+id/tvSongTitle"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="20sp" />

    <TextView
        android:id="@+id/tvSongArtist"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="14sp" />
</LinearLayout>
```

**Jebakan:** tinggi root item harus `wrap_content`. Kalau memakai `match_parent`, satu baris dapat memenuhi satu layar.

---

## Adapter: tiga method wajib ⭐⭐⭐

```java
public class SongAdapter
        extends RecyclerView.Adapter<SongAdapter.SongViewHolder> {

    ArrayList<Song> songsInAdapter;

    public SongAdapter(ArrayList<Song> songs) {
        songsInAdapter = songs;
    }

    // 1. Membuat tampilan kosong dari row_song.xml
    @Override
    public SongViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.row_song, parent, false);
        return new SongViewHolder(view);
    }

    // 2. Mengisi tampilan dengan data sesuai position
    @Override
    public void onBindViewHolder(@NonNull SongViewHolder holder, int position) {
        Song song = songsInAdapter.get(position);
        holder.tvSongTitle.setText(song.getTitle());
        holder.tvSongArtist.setText(song.getArtist());
    }

    // 3. Memberi tahu jumlah semua data
    @Override
    public int getItemCount() {
        return songsInAdapter.size();
    }
}
```

| Method | Fungsi | Analogi formulir |
|---|---|---|
| `onCreateViewHolder()` | Mengubah `row_song.xml` menjadi View dan membuat ViewHolder | Mencetak formulir kosong |
| `onBindViewHolder()` | Mengisi View dengan data berdasarkan `position` | Mengisi formulir orang ke-sekian |
| `getItemCount()` | Mengembalikan jumlah data | Menghitung total formulir |

Poin penting:
- `position` dimulai dari `0`.
- `songsInAdapter.get(position)` mengambil Song sesuai posisi.
- `inflate(..., false)` berarti View belum dipasang ke parent karena RecyclerView yang memasangnya.

---

## ViewHolder

```java
public class SongViewHolder extends RecyclerView.ViewHolder {
    TextView tvSongTitle, tvSongArtist;

    public SongViewHolder(@NonNull View itemView) {
        super(itemView);
        tvSongTitle = itemView.findViewById(R.id.tvSongTitle);
        tvSongArtist = itemView.findViewById(R.id.tvSongArtist);
    }
}
```

ViewHolder menyimpan referensi View untuk satu baris. Jadi Android tidak perlu mencari `TextView` dari awal setiap list di-scroll.

**Jebakan:** di ViewHolder gunakan `itemView.findViewById(...)`, karena yang dicari adalah View di `row_song.xml`, bukan View milik seluruh Activity.

---

## Memasang RecyclerView di `MainActivity`

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    // 1. Siapkan data
    ArrayList<Song> songs = new ArrayList<>();
    for (int i = 0; i < 100; i++) {
        songs.add(new Song("Judul " + i, "Artis " + i));
    }

    // 2. Ambil RecyclerView dari XML
    RecyclerView rvSongs = findViewById(R.id.rvSongs);

    // 3. Pasang LayoutManager dan Adapter
    rvSongs.setLayoutManager(new LinearLayoutManager(this));
    rvSongs.setAdapter(new SongAdapter(songs));
}
```

**Urutan hafalan:**

```
Siapkan data → findViewById RecyclerView → setLayoutManager → setAdapter
```

| LayoutManager | Hasil |
|---|---|
| `LinearLayoutManager` | List vertikal atau horizontal |
| `GridLayoutManager(this, 2)` | Grid dua kolom |
| `StaggeredGridLayoutManager` | Grid tinggi item tidak seragam, seperti Pinterest |

Project dosen memakai `LinearLayoutManager`.

---

## Klik item

Adapter mengirim Song yang diklik ke Activity memakai interface listener.

```java
// Di Adapter
public interface OnItemClickListener {
    void onItemClick(Song song);
}

holder.itemView.setOnClickListener(v -> {
    if (listener != null) {
        listener.onItemClick(song);
    }
});
```

```java
// Di MainActivity
SongAdapter adapter = new SongAdapter(songs, clickedSong -> {
    Toast.makeText(this,
            clickedSong.getTitle() + " is clicked",
            Toast.LENGTH_LONG).show();
});
```

- `holder.itemView` = seluruh baris lagu.
- `clickedSong -> { ... }` = lambda, cara singkat membuat isi interface.

---

## Error RecyclerView

| Gejala | Penyebab | Solusi |
|---|---|---|
| List kosong | Lupa LayoutManager | `rvSongs.setLayoutManager(new LinearLayoutManager(this));` |
| List kosong | Lupa Adapter | `rvSongs.setAdapter(adapter);` |
| Satu item memenuhi layar | Tinggi root item `match_parent` | Ubah menjadi `wrap_content` |
| App crash / TextView null | ID XML dan ViewHolder berbeda | Samakan ID dan pakai `itemView.findViewById` |
| Data salah saat scroll | Tidak memakai `position` | Ambil data dengan `data.get(position)` |

---

# 2. Multiple Activity

## Inti konsep

**Activity** biasanya mewakili satu layar atau satu tugas terfokus yang dilakukan user.

Di project 02c:

```
MainActivity                    MainActivity2
halaman login       →          halaman "Halo [username]"
```

- `MainActivity.java` berisi logika layar.
- `activity_main.xml` berisi tampilannya.
- `MainActivity2.java` dan `activity_main2.xml` adalah layar kedua.

Beberapa Activity yang dirangkai untuk menyelesaikan sebuah pekerjaan disebut **task**.

---

## Manifest: Activity wajib didaftarkan ⭐

Setiap Activity wajib terdaftar di `AndroidManifest.xml`.

```xml
<application ...>
    <activity
        android:name=".MainActivity"
        android:exported="true">
        <intent-filter>
            <action android:name="android.intent.action.MAIN" />
            <category android:name="android.intent.category.LAUNCHER" />
        </intent-filter>
    </activity>

    <activity
        android:name=".MainActivity2"
        android:exported="false" />
</application>
```

| Bagian | Arti |
|---|---|
| `<activity>` | Mendaftarkan sebuah Activity |
| `android:name` | Nama class Activity. Titik berarti package di depan tidak ditulis ulang |
| `MAIN` + `LAUNCHER` | Activity pertama yang dibuka ketika ikon aplikasi ditekan |
| `exported="true"` | Dapat dibuka dari luar aplikasi. Activity utama perlu ini |
| `exported="false"` | Hanya digunakan di aplikasi sendiri |

Kalau `MainActivity2` tidak terdaftar, pemanggilan Activity bisa gagal dengan `ActivityNotFoundException`.

---

## Intent: pesan untuk melakukan sesuatu ⭐

Intent adalah objek/pesan yang meminta Android melakukan suatu tindakan.

| | Explicit Intent | Implicit Intent |
|---|---|---|
| Tujuan | Nama class tujuan disebut jelas | Hanya aksi yang diminta |
| Contoh | Pindah ke `MainActivity2` | Membuka URL di browser |
| Yang menentukan aplikasi tujuan | Programmer | Android mencari aplikasi yang cocok |

---

## Explicit Intent: pindah antar Activity

```java
Intent niatPindahHalaman = new Intent(this, MainActivity2.class);
startActivity(niatPindahHalaman);
```

- `this` = Activity yang sekarang, yaitu `MainActivity`.
- `MainActivity2.class` = tujuan.
- `startActivity(...)` = mulai membuka Activity tujuan.

**Rumus hafalan:**

```java
Intent intent = new Intent(this, ActivityTujuan.class);
startActivity(intent);
```

---

## Kirim data dengan `putExtra` ⭐⭐⭐

### Pengirim: `MainActivity`

```java
String strUsername = etUsername.getText().toString();

Intent intent = new Intent(this, MainActivity2.class);
intent.putExtra("keyUsername", strUsername);
startActivity(intent);
```

### Penerima: `MainActivity2`

```java
String username = getIntent().getStringExtra("keyUsername");
tvGreetings.setText("Halo " + username);
```

| Kode | Fungsi |
|---|---|
| `getText().toString()` | Mengambil teks EditText sebagai String |
| `putExtra(key, value)` | Menitipkan data ke Intent |
| `getIntent()` | Mengambil Intent yang membuka Activity sekarang |
| `getStringExtra(key)` | Mengambil data String dari Intent |

**Aturan nomor satu:** key harus **sama persis**.

```java
// Benar
intent.putExtra("keyUsername", "Budi");
getIntent().getStringExtra("keyUsername");

// Salah: hasilnya null
getIntent().getStringExtra("username");
```

Tipe lain:

```java
intent.putExtra("umur", 20);
intent.putExtra("isLogin", true);

int umur = getIntent().getIntExtra("umur", 0);
boolean isLogin = getIntent().getBooleanExtra("isLogin", false);
```

---

## Implicit Intent: membuka browser

Project 02c:

```java
Uri uri = Uri.parse("https://www.instagram.com");
Intent niatBrowser = new Intent(Intent.ACTION_VIEW, uri);
startActivity(niatBrowser);
```

- `Uri.parse(...)` mengubah alamat String menjadi objek URI.
- `Intent.ACTION_VIEW` berarti tampilkan data tersebut.
- Android mencari browser atau aplikasi yang dapat membuka URL.

Perbandingan kode:

```java
// Explicit: class tujuan disebut langsung
new Intent(this, MainActivity2.class);

// Implicit: aksi disebut, Android mencari aplikasi yang cocok
new Intent(Intent.ACTION_VIEW, Uri.parse("https://www.google.com"));
```

---

## Menangani klik tombol

Project 02c memakai satu listener untuk beberapa tombol.

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        btnLogin = findViewById(R.id.btnLogin);
        btnRegister = findViewById(R.id.btnRegister);
        btnLogin.setOnClickListener(this);
        btnRegister.setOnClickListener(this);
    }

    @Override
    public void onClick(View view) {
        if (view.getId() == R.id.btnLogin) {
            // pindah Activity
        } else if (view.getId() == R.id.btnRegister) {
            // buka browser
        }
    }
}
```

Alur:

```
ID di XML → findViewById → setOnClickListener → cek view.getId()
```

**Jebakan:** `setContentView` harus dipanggil sebelum `findViewById`. Kalau tidak, hasil `findViewById` dapat `null` dan aplikasi crash saat View dipakai.

---

## Error Multiple Activity

| Gejala | Penyebab | Solusi |
|---|---|---|
| `ActivityNotFoundException` | Activity tujuan belum ada di Manifest | Tambah `<activity android:name=".MainActivity2" />` |
| Layar kedua menulis `Halo null` | Key pengirim dan penerima berbeda | Samakan key `putExtra` dan `getStringExtra` |
| Tombol tidak merespons | Listener belum dipasang | Panggil `setOnClickListener` |
| App crash saat tombol dipakai | `findViewById` sebelum `setContentView` / ID salah | Pasang layout dulu dan cek ID |

---

# 3. Themes

## Style vs Theme ⭐⭐⭐

| | Style | Theme |
|---|---|---|
| Diterapkan ke | Satu View | Satu Activity atau seluruh aplikasi |
| Contoh | Satu TextView biru, 48sp, bold | Semua layar memakai warna/background/action bar yang sama |
| Cara pakai | `style="@style/NamaStyle"` di layout XML | `android:theme="@style/NamaTheme"` di Manifest |

**Style** adalah kumpulan atribut tampilan, misalnya warna teks, ukuran teks, font, padding, dan background.

**Theme** adalah Style yang diterapkan ke seluruh Activity atau aplikasi.

Analogi: Style = baju satu orang. Theme = aturan dress code seluruh acara.

---

## Membuat dan memakai Style

Di `res/values/styles.xml`:

```xml
<style name="MyJumboText" parent="Widget.AppCompat.TextView">
    <item name="android:textColor">@color/red</item>
    <item name="android:textSize">48sp</item>
    <item name="android:textStyle">bold</item>
</style>
```

Memakainya pada satu TextView:

```xml
<TextView
    style="@style/MyJumboText"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Hello World!" />
```

Kenapa memakai Style?
- Tampilan dipisahkan dari konten, mirip CSS.
- Kode layout lebih rapi.
- Warna/ukuran bisa diubah di satu tempat.
- Tampilan antar View lebih konsisten.

---

## Inheritance / pewarisan Style ⭐⭐⭐

### 1. Mewarisi Style bawaan atau library: gunakan `parent`

```xml
<style name="MyJumboText" parent="Widget.AppCompat.TextView">
    <item name="android:textColor">@color/red</item>
    <item name="android:textSize">48sp</item>
    <item name="android:textStyle">bold</item>
</style>
```

### 2. Mewarisi Style buatan sendiri: gunakan titik (`.`)

```xml
<style name="MyJumboText.blue">
    <item name="android:textColor">@color/blue</item>
</style>
```

`MyJumboText.blue` otomatis mewarisi `MyJumboText`.

| Atribut pada `MyJumboText.blue` | Nilai akhir | Alasannya |
|---|---|---|
| `textColor` | biru | Ditimpa oleh Style anak |
| `textSize` | 48sp | Diwarisi dari `MyJumboText` |
| `textStyle` | bold | Diwarisi dari `MyJumboText` |

Jadi TextView dengan `style="@style/MyJumboText.blue"` akan berwarna **biru**, ukuran **48sp**, dan **bold**.

---

## Colors sebagai resource

`res/values/colors.xml`:

```xml
<resources>
    <color name="blue">#FF0000FF</color>
    <color name="red">#FFFF0000</color>
</resources>
```

Digunakan dalam Style:

```xml
<item name="android:textColor">@color/blue</item>
```

Format warna: `#AARRGGBB`

| Bagian | Arti |
|---|---|
| `AA` | Alpha/transparansi. `FF` = tidak transparan |
| `RR` | Merah |
| `GG` | Hijau |
| `BB` | Biru |

---

## Menerapkan Theme melalui Manifest ⭐

### Theme untuk seluruh aplikasi

```xml
<application
    android:theme="@style/Theme.MyApplication06a"
    ... >
```

### Theme untuk satu Activity

```xml
<activity
    android:name=".SettingsActivity"
    android:theme="@style/Theme.Material3.DayNight.NoActionBar" />
```

Kalau Theme dipasang di `<application>`, semua Activity menggunakannya kecuali Activity tersebut punya Theme sendiri.

---

## `themes.xml` dan dark mode

Project 06a punya dua file:

```text
res/values/themes.xml          → mode terang
res/values-night/themes.xml    → mode gelap
```

Android memilih salah satunya otomatis sesuai dark mode perangkat.

Di project dosen, Theme dark mode memakai:

```xml
parent="Theme.Material3.DayNight.NoActionBar"
```

`NoActionBar` menghilangkan Action Bar. Karena Options Menu berada di Action Bar, menu Help, Settings, dan Logout juga tidak terlihat saat dark mode.

Kode dosen aman karena memeriksa null:

```java
ActionBar actionBar = getSupportActionBar();
if (actionBar != null) {
    actionBar.setDisplayHomeAsUpEnabled(true);
}
```

Tanpa pengecekan ini, `NoActionBar` membuat `getSupportActionBar()` menghasilkan `null`, lalu aplikasi berpotensi crash dengan `NullPointerException`.

---

## Error Themes

| Gejala | Penyebab | Solusi |
|---|---|---|
| Style tidak terlihat | Nama Style/reference salah | Gunakan `style="@style/NamaStyle"` |
| Warna tidak ditemukan | Warna belum ada / salah nama di `colors.xml` | Tambah `<color name="...">...</color>` lalu pakai `@color/...` |
| Menu hilang | Theme memakai `NoActionBar` | Gunakan Theme tanpa `NoActionBar` bila menu diperlukan |
| App crash pada ActionBar | `getSupportActionBar()` bernilai null | Cek `if (actionBar != null)` |
| Inheritance tidak jalan | Nama child / parent salah | Bawaan pakai `parent`; Style sendiri pakai `NamaInduk.NamaAnak` |

---

# Cheat Sheet 5 Menit

## RecyclerView

```java
rvSongs = findViewById(R.id.rvSongs);
rvSongs.setLayoutManager(new LinearLayoutManager(this));
rvSongs.setAdapter(songAdapter);
```

```java
onCreateViewHolder()  // buat ViewHolder dari XML item
onBindViewHolder()    // isi data sesuai position
getItemCount()        // jumlah data
```

```
Data → Adapter → ViewHolder / row_item.xml → RecyclerView + LayoutManager
```

## Multiple Activity

```java
Intent intent = new Intent(this, MainActivity2.class);
intent.putExtra("keyUsername", username);
startActivity(intent);
```

```java
String username = getIntent().getStringExtra("keyUsername");
```

```java
new Intent(Intent.ACTION_VIEW, Uri.parse("https://www.google.com"));
```

## Themes

```xml
<!-- Style untuk satu View -->
<TextView style="@style/MyJumboText.blue" />

<!-- Theme untuk seluruh aplikasi -->
<application android:theme="@style/Theme.MyApplication06a" />

<!-- Inheritance bawaan / library -->
<style name="MyStyle" parent="Widget.AppCompat.TextView" />

<!-- Inheritance Style buatan sendiri -->
<style name="MyStyle.blue" />
```

---

# Latihan UTS

## Pilihan Ganda

1. Bagian RecyclerView yang menjadi perantara antara data dan tampilan adalah...
   a. LayoutManager  b. Adapter  c. ViewHolder  d. LinearLayout

2. Method Adapter yang mengisi data ke View berdasarkan `position` adalah...
   a. `onCreateViewHolder`  b. `onBindViewHolder`  c. `getItemCount`  d. `onCreate`

3. Jika data dan Adapter sudah ada tetapi RecyclerView kosong, penyebab paling mungkin adalah...
   a. Lupa `setLayoutManager`  b. Lupa `getIntent`  c. Lupa `putExtra`  d. Lupa Manifest

4. `onCreateViewHolder()` bertugas...
   a. Menghitung data  b. Mengisi data  c. Membuat View item dari XML  d. Membuka Activity

5. Activity A ingin membuka Activity B dalam aplikasi sendiri. Intent yang tepat adalah...
   a. `new Intent(Intent.ACTION_VIEW, uri)`
   b. `new Intent(this, ActivityB.class)`
   c. `new Intent(Intent.ACTION_SEND)`
   d. `new Intent(ActivityB.class, this)`

6. Key pada `putExtra` dan `getStringExtra` harus...
   a. Berbeda  b. Sama persis  c. Berupa angka  d. Kosong

7. Agar Activity tujuan dapat dibuka, class Activity itu harus...
   a. Didaftarkan dalam `AndroidManifest.xml`
   b. Dimasukkan ke ArrayList
   c. Didaftarkan dalam `colors.xml`
   d. Dibuat sebagai Adapter

8. Intent `new Intent(Intent.ACTION_VIEW, Uri.parse("https://google.com"))` adalah...
   a. Explicit Intent  b. Implicit Intent  c. Broadcast  d. Service Intent

9. Style diterapkan pada..., sedangkan Theme diterapkan pada...
   a. Satu View; Activity/aplikasi
   b. Activity; satu View
   c. Database; RecyclerView
   d. Manifest; Adapter

10. `MyJumboText.blue` mewarisi `MyJumboText` dan hanya menulis ulang `textColor`. Maka `textSize` dan `textStyle`...
    a. Hilang  b. Diwarisi dari parent  c. Menjadi null  d. Mengikuti warna child

11. Theme `NoActionBar` dapat menyebabkan...
    a. RecyclerView hilang  b. Action Bar dan menu hilang  c. Database hilang  d. Intent gagal

12. Warna `#FF0000FF` adalah...
    a. Merah  b. Hijau  c. Biru  d. Transparan

## Lengkapi Kode

13. Adapter:

```java
@Override
public int getItemCount() {
    return ____________________;
}
```

14. Pindah dari `MainActivity` ke `ProfileActivity`:

```java
Intent intent = new Intent(__________, __________.class);
startActivity(__________);
```

15. Kirim dan terima nama:

```java
// Pengirim
intent.putExtra("__________", nama);

// Penerima
String nama = getIntent().getStringExtra("__________");
```

16. Pasang RecyclerView:

```java
rvSongs = findViewById(R.id.rvSongs);
rvSongs.__________________(new LinearLayoutManager(this));
rvSongs.__________________(songAdapter);
```

17. Lengkapi Style child:

```xml
<style name="MyJumboText.__________">
    <item name="android:textColor">@color/blue</item>
</style>
```

## Esai / Cari Error

18. Jelaskan alur `ArrayList<Song>` sampai judul lagu tampil dalam RecyclerView.

19. Jelaskan beda Explicit Intent dan Implicit Intent, beserta contoh masing-masing dari project 02c.

20. Kenapa key pada `putExtra("keyUsername", value)` harus sama dengan key pada `getStringExtra("keyUsername")`?

21. Jelaskan beda Style dan Theme. Jelaskan juga dua cara inheritance Style.

22. Mengapa kode berikut membuat list kosong?

```java
rvSongs = findViewById(R.id.rvSongs);
rvSongs.setAdapter(songAdapter);
```

23. Apa hasil kode berikut, dan kenapa?

```java
// Pengirim
intent.putExtra("keyUsername", "Budi");

// Penerima
String nama = getIntent().getStringExtra("username");
tvGreetings.setText("Halo " + nama);
```

24. Mengapa kode berikut dapat crash?

```xml
<activity android:theme="@style/Theme.Material3.DayNight.NoActionBar" />
```

```java
getSupportActionBar().setDisplayHomeAsUpEnabled(true);
```

---

# Kunci Jawaban

## Pilihan Ganda

1. **b. Adapter**
2. **b. `onBindViewHolder`**
3. **a. Lupa `setLayoutManager`**
4. **c. Membuat View item dari XML**
5. **b. `new Intent(this, ActivityB.class)`**
6. **b. Sama persis**
7. **a. Didaftarkan dalam `AndroidManifest.xml`**
8. **b. Implicit Intent**
9. **a. Satu View; Activity/aplikasi**
10. **b. Diwarisi dari parent**
11. **b. Action Bar dan menu hilang**
12. **c. Biru**

## Lengkapi Kode

13.
```java
return songsInAdapter.size();
```

14.
```java
Intent intent = new Intent(this, ProfileActivity.class);
startActivity(intent);
```

15. Kedua key harus sama. Contoh:
```java
intent.putExtra("keyNama", nama);
String nama = getIntent().getStringExtra("keyNama");
```

16.
```java
rvSongs.setLayoutManager(new LinearLayoutManager(this));
rvSongs.setAdapter(songAdapter);
```

17.
```xml
<style name="MyJumboText.blue">
```

## Esai / Cari Error

18. ArrayList menyimpan Song. Adapter mengambil data itu. `onCreateViewHolder` mengubah XML item menjadi View. ViewHolder menyimpan TextView judul/artis. `onBindViewHolder` mengambil Song menurut `position` lalu mengisi TextView. RecyclerView dan LayoutManager menampilkan itemnya sebagai list.

19. Explicit Intent menyebut class tujuan: `new Intent(this, MainActivity2.class)`. Implicit Intent menyebut aksi: `new Intent(Intent.ACTION_VIEW, Uri.parse("https://www.instagram.com"))`, lalu Android mencari browser yang cocok.

20. Key adalah label data dalam Intent. Kalau label pengirim dan penerima berbeda, data tidak ditemukan sehingga hasilnya `null`.

21. Style diterapkan pada satu View. Theme diterapkan pada Activity/aplikasi. Style bawaan/library diwarisi dengan atribut `parent`. Style buatan sendiri diwarisi lewat pola `NamaParent.NamaChild`.

22. LayoutManager belum dipasang. Tambahkan:
```java
rvSongs.setLayoutManager(new LinearLayoutManager(this));
```

23. Hasilnya `Halo null`. Pengirim memakai key `keyUsername`, tetapi penerima mencari `username`.

24. `NoActionBar` membuat `getSupportActionBar()` menghasilkan `null`. Memanggil method dari `null` menyebabkan `NullPointerException`. Gunakan pengecekan:
```java
ActionBar actionBar = getSupportActionBar();
if (actionBar != null) {
    actionBar.setDisplayHomeAsUpEnabled(true);
}
```
