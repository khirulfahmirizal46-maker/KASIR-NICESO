[README (1).md](https://github.com/user-attachments/files/34270492/README.1.md)
# Laporan Harian 3 Toko

Satu file (`index.html`) yang bisa di-hosting gratis di GitHub Pages. Agar data tersinkron antar perangkat, aplikasi memakai Firebase (paket gratis Spark). Tanpa Firebase, aplikasi tetap jalan dalam mode lokal: data hanya tersimpan di perangkat masing-masing.

## Bagian A. Hosting di GitHub Pages

1. Buka github.com, buat repository baru (misalnya `laporan-toko`). Pilih Public, karena GitHub Pages gratis untuk repository publik.
2. Tekan **Add file > Upload files**, unggah `index.html`, lalu **Commit changes**.
3. Buka **Settings > Pages**. Di bagian **Build and deployment**, pilih **Deploy from a branch**, branch `main`, folder `/ (root)`, lalu **Save**.
4. Tunggu 1 sampai 2 menit. Alamatnya: `https://USERNAME.github.io/laporan-toko/`

Catatan: isi repository publik bisa dilihat siapa saja, jadi jangan menaruh data laporan di dalamnya. Data laporan disimpan di Firebase, bukan di GitHub.

## Bagian B. Menyiapkan Firebase (sinkron antar perangkat)

1. Buka console.firebase.google.com, masuk dengan akun Google, lalu **Create a project**. Google Analytics boleh dimatikan.
2. **Build > Firestore Database > Create database**. Pilih lokasi terdekat (misalnya `asia-southeast2` Jakarta), mode **Production**.
3. **Build > Authentication > Get started > Sign-in method > Email/Password**, aktifkan. Lalu tab **Users > Add user** untuk membuat akun SPV dan tiap kasir (email dan kata sandi).
4. Tab **Settings** di Authentication, bagian **Authorized domains**: tambahkan `USERNAME.github.io`.
5. Kembali ke Firestore, tab **Rules**, ganti isinya dengan yang di bawah (ganti email dengan email akun Anda), lalu **Publish**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.auth != null
        && request.auth.token.email in [
          'spv@contoh.com',
          'kasir1@contoh.com',
          'kasir2@contoh.com'
        ];
    }
  }
}
```

Daftar email di aturan ini yang menentukan siapa boleh membaca dan menulis. Orang lain yang membuat akun sendiri lewat API Firebase tetap ditolak. Setiap ada kasir baru, tambahkan emailnya di sini dan di Authentication.

6. Ikon roda gigi di samping **Project Overview > Project settings**. Di bagian **Your apps**, tekan ikon web `</>`, beri nama, lalu **Register app**. Salin isi `firebaseConfig`.
7. Buka `index.html` di GitHub (ikon pensil), cari baris:

```js
const FIREBASE_CONFIG=null;
```

dan ganti dengan konfigurasi Anda, misalnya:

```js
const FIREBASE_CONFIG={apiKey:"...",authDomain:"...firebaseapp.com",projectId:"...",storageBucket:"...",messagingSenderId:"...",appId:"..."};
```

Commit. Nilai `apiKey` di sini bukan rahasia; yang melindungi data adalah login dan aturan di langkah 5.

## Bagian C. Memakai

1. Buka alamat GitHub Pages di tiap perangkat dan masuk dengan email dan kata sandi.
2. Di perangkat yang sudah berisi data lama, buka **Pengaturan > Unggah data perangkat ini ke penyimpanan bersama** satu kali.
3. Label di samping judul: **Tersinkron** berarti data sudah tersimpan untuk semua perangkat.

Batas paket gratis Firebase (50 ribu pembacaan dan 20 ribu penulisan per hari) jauh di atas pemakaian tiga toko.
