# Harfiye

Gazete bulmacası görünümünde, Türkçe 5 harfli kelime oyunu. Gizli kelimeyi 6 denemede bul; kutuların rengi harflerin ne kadar yakın olduğunu söyler.

- **Yeşil:** harf kelimede var ve doğru yerde
- **Sarı:** harf kelimede var ama başka bir yerde
- **Kırmızı:** harf kelimede yok

## Özellikler

- **Günlük bulmaca:** Herkese aynı gün aynı kelime (Türkiye saatine göre), günde bir kez. Seri sayacı ve kaçırılan günü kapatan seri koruma.
- **Serbest mod:** İstediğin kadar oyna, kelimeler bitene kadar tekrar etmez.
- **TDK sözlüğü:** Tahminler TDK Güncel Türkçe Sözlük'teki 5 harfli kelimelerle denetlenir (5.440 kelime).
- **313 cevap kelimesi:** Bilişim, bilim ve günlük hayattan; her birinin kısa açıklaması oyun sonunda gösterilir.
- **Mürekkep ve dükkân:** Bulunan kelimeler mürekkep kazandırır. 5, 15, 30, 50, 100 ve 200. kelimelerde hediye açılır. Mürekkeple 5 tema ve maskot için 6 kostüm alınır.
- **Kitap kurdu:** Oyuna tepki veren maskot.
- **Grup yarışması:** Her grup tek telefondan oynar, tüm gruplara aynı kelime gelir, en hızlı bulan kazanır. Yönetici masasında canlı sıralama, ipucu gönderme ve kelimeyi değiştirme var.

## Çalıştırma

Tek dosyadan oluşur: `index.html`. Bir tarayıcıda açman yeterli. Herhangi bir statik barındırma hizmetine (Netlify, GitHub Pages, Vercel) olduğu gibi yüklenebilir.

Tek kişilik oyun internet ya da sunucu gerektirmez. İlerleme ve mürekkep tarayıcının yerel deposunda (`localStorage`) tutulur.

## Grup yarışması için Firebase

Grup yarışması Firebase Firestore kullanır.

1. [Firebase konsolunda](https://console.firebase.google.com) bir proje aç ve Firestore veritabanı oluştur.
2. Bir web uygulaması kaydet ve `firebaseConfig` nesnesini `index.html` içindeki `FIREBASE_CONFIG` değişkenine koy.
3. Firestore kurallarına `firestore.rules` dosyasındaki kuralı ekle.

`FIREBASE_CONFIG` değeri `null` bırakılırsa yarışma düğmesi gizlenir ve oyun yalnızca tek kişilik çalışır.

> Kurallar `kelime_odalar` koleksiyonunu herkese açar. Kimlik doğrulama yoktur; yarışma kodunu bilen herkes katılabilir.

## Yönetici şifresini değiştirme

Kodda şifrenin kendisi değil SHA-256 özeti (`ADMIN_HASH`) durur. Yeni bir özet üretmek için tarayıcı konsolunda:

```js
const s = "yeni-sifren";
const h = await crypto.subtle.digest("SHA-256", new TextEncoder().encode(s));
console.log([...new Uint8Array(h)].map(b => b.toString(16).padStart(2, "0")).join(""));
```

Çıkan değeri `index.html` içindeki `ADMIN_HASH` ile değiştir. Depo herkese açıksa kısa şifrelerin özeti kolayca kırılabilir; en az 12 karakterli bir şifre kullan.

## Kelime ekleme

Cevap kelimeleri `index.html` içindeki `WORDS` dizisindedir: `["KELİME", "kategori", "açıklama"]`. Kategoriler: `bil` (Bilişim), `fen` (Bilim), `gen` (Genel). Kelime 5 harfli olmalı ve TDK listesinde (`TDK5`) bulunmalı.

Günlük bulmacanın sırası bu diziden üretilir. Yayına girdikten sonra kelime eklemek ya da sırayı değiştirmek, gelecek günlerin kelimelerini değiştirir.

## Kaynak

Geçerli tahmin listesi, Türk Dil Kurumu Güncel Türkçe Sözlük madde başlarından derlenmiştir.
