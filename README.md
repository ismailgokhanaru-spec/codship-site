# cod-ship.com — tanıtım sayfası

Tek dosya: `index.html`. Derleme yok, bağımlılık yok, dış kaynak yok.
Görseller `gorsel/` altında, `marka/vitrin/`'den kopyalandı.

## Dil

**Sayfanın HTML'i Türkçe yazıldı** — öncelik Türkiye. İngilizce karşılıklar
dosyanın sonundaki `EN` sözlüğünde duruyor; metinler `data-t="anahtar"`
özniteliğiyle eşleşiyor.

- Varsayılan: tarayıcı dili `tr` ile başlıyorsa Türkçe, değilse İngilizce.
- Kullanıcı TR/EN düğmesiyle seçerse tercih `localStorage`'a yazılır,
  ikinci ziyarette tekrar sorulmaz.
- **Türkçe metni değiştirirsen İngilizcesini de değiştir.** Türkçe doğrudan
  HTML'de, İngilizce sözlükte; ikisi ayrı yerde. Anahtarların tamamının
  karşılığı var mı diye bakmak için:
  ```
  node -e 'const s=require("fs").readFileSync("site/index.html","utf8");const k=[...s.matchAll(/data-t="([^"]+)"/g)].map(m=>m[1]);const en=s.slice(s.indexOf("const EN = {"),s.indexOf("const SERIT_TR"));const e=new Set([...en.matchAll(/"([a-z]+\.[A-Za-z0-9]+)"\s*:/g)].map(m=>m[1]));console.log([...new Set(k)].filter(x=>!e.has(x)))'
  ```
  Boş dizi basmalı.

**Görseller de dile göre değişiyor.** İki takım var, ikisi de 1600×900:

| Dil | Klasör | Üreten komut |
|---|---|---|
| Türkçe | `gorsel/tr/` | `node scripts/vitrin-gorsel.mjs --tr` |
| İngilizce | `gorsel/` | `node scripts/vitrin-gorsel.mjs` |

`<img data-gorsel="01-form.png">` özniteliği taşıyor; dil değişince `src`
ve `alt` birlikte güncelleniyor. Görsel ekler ya da adını değiştirirsen
**iki klasörde de** aynı dosya adı bulunmalı, yoksa dil değiştirince
kırık görsel çıkar.

Türkçe takımda tutarlar ₺ ve ondalık ayracı virgül (699,90 ₺); dolar
göstermek Türk mağazası anlatırken sahte duruyordu. Dil panosu
(`05-dil`) her iki takımda da Arapça ve dolar — anlatılan şey sağdan
sola yerleşim, oraya ₺ koymak anlamsız.

Kaynak dosyalar `marka/vitrin/` ve `marka/vitrin/tr/`; `site/gorsel/`
oradan kopya. Görselleri yeniden üretirsen kopyalamayı unutma.

## Hero'daki telefon

Sayfanın üstündeki telefon **görsel değil, canlı HTML**. Böylece dil
düğmesi formun içindeki yazıları da çeviriyor — ekran görüntüsü olsaydı
Türkçe sayfada İngilizce form kalırdı.

Form dolduruluyormuş gibi sırayla beliriyor; ad alanında daktilo
efekti var, eklenti kutusu kendi kendine işaretleniyor. Arkadaki
kıvrımlar SVG, `stroke-dashoffset` ile çiziliyor.

Telefon yüksekliği `aspect-ratio` ile sabit; metin uzayınca
(ör. İngilizce) içerik `margin-top:auto` sayesinde alta yaslanıyor.

## Yorumlar bölümü

`#yorumlar` bölümü yayında, ama içinde **gerçek yorum yok** — olmayan
mağaza adıyla referans yazmak sahteciliktir ve Shopify incelemesinde ret
sebebidir. Şu an görünen şey bunu açıkça söyleyen `.yorum-yok` bloğu.

Altında `.yorumlar` ızgarası `hidden` ile kapalı duruyor; içinde tek bir
**yer tutucu kart** var. İlk gerçek yorumlar gelince:

1. `.yorum-yok` bloğunu sil
2. `.yorumlar`dan `hidden`i kaldır
3. Kartları Shopify App Store'daki yorumlardan **olduğu gibi** doldur —
   mağaza adı, ülke, puan, metin. Kırpıp güzelleştirme.

> **Dikkat:** `.yorumlar{display:grid}` tarayıcının kendi `[hidden]`
> kuralını eziyor; `hidden` yazmak tek başına yetmiyor, yer tutucu kart
> sayfada görünüyor. Bunun için `.yorumlar[hidden]{display:none}` kuralı
> kondu. Izgaraya benzer bir bölüm daha eklersen aynı tuzağa dikkat.

## Hareket

Belirme animasyonları `IntersectionObserver` ile; şerit saf CSS.
`prefers-reduced-motion: reduce` diyen ziyaretçide **hepsi kapanır**,
sayfa sabit durur. JavaScript kapalıysa `<noscript>` bloğu içeriği
görünür kılar — animasyon yüzünden metin kaybolmaz.

## Yerelde bakmak

```
cd site && python3 -m http.server 8765
```

Sonra `http://127.0.0.1:8765/`.

> `index.html`'i çift tıklayıp `file://` ile açma — görseller yüklenir ama
> bazı tarayıcılarda göreli yollar ve `prefers-color-scheme` farklı
> davranıyor. Sunucuyla bak.

## Neden Remix uygulamasının içinde değil

Uygulama (`app/`) Shopify incelemesine girecek. Tanıtım sayfasının her
değişikliği uygulamanın yeni sürümünü gerektirmesin diye ayrı tutuldu.
Uygulamanın kendi kök sayfası (`app/routes/_index/`) duruyor; orası
mağaza alan adı girilen teknik giriş sayfası, pazarlama sayfası değil.

## Yayına alırken

Statik barındırmanın herhangi biri yeter (Cloudflare Pages, Netlify,
GoDaddy). `cod-ship.com` GoDaddy'de; DNS'te **MX/SPF/DKIM kayıtlarına
dokunma** — mail oradan geçiyor, bkz. `[[codship-mail-bahngo-diger-adi]]`.
Sadece A/CNAME eklenecek.

## Henüz gerçek olmayan bağlantılar

| Yer | Şu an | Olması gereken |
|---|---|---|
| "Start free" / "Install on Shopify" düğmeleri | `#fiyat`'a gidiyor | App Store listeleme adresi — uygulama **yayımlanınca** |
| Yasal bağlantılar | `kapida-odeme-production-817b.up.railway.app` | Kendi alan adına taşınırsa güncellenecek |

Uygulama App Store'da yayımlanmadan önce düğmeleri gerçek adrese
bağlama — kırık bağlantı, olmayan bir yere giden düğmeden iyidir.

## Bilerek yapmadıklarımız

- **Müşteri yorumu yok.** Henüz kullanıcımız yok; uydurma referans
  koymadık. Rakip (Releasit) sayfasının yarısı yorum. Bizde `#yorumlar`
  bölümü var ama içinde **"henüz yorum yok" diyen dürüst bir blok**
  duruyor — bkz. aşağıdaki "Yorumlar bölümü".
- **"115.000 mağaza", "dönüşümü %X artırır" gibi rakam yok.** Kanıtlanamaz
  iddia hem yanlış hem de Shopify incelemesinde sorun çıkarıyor.
- **Rakip metni kopyalanmadı.** Yapı benzer (hero → özellik → fiyat →
  SSS) çünkü kategori standardı bu; cümleler bize ait.
- **Analytics/çerez yok.** Çerez yoksa çerez bandı da gerekmiyor.
  (Dil tercihi `localStorage`'da tutuluyor; bu izleme değil, kullanıcının
  kendi seçimi — onay bandı gerektirmiyor.)
