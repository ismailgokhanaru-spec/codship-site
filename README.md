# cod-ship.com — tanıtım sayfası

İki sayfa, derleme yok, dış kaynak yok (CDN font/JS yok):

| Adres | Dosya | Dil | Nasıl yazılır |
|---|---|---|---|
| `cod-ship.com/` | `index.html` | **İngilizce** (ana dil) | elle — tek kaynak bu |
| `cod-ship.com/tr/` | `tr/index.html` | Türkçe | **üretilir**: `node scripts/site-tr-sayfa.mjs` |

Görseller `gorsel/` (İngilizce takım) ve `gorsel/tr/` (Türkçe takım) altında.
Tasarımın rakip incelemesi: `docs/site-rakip-inceleme.md` (24.11.2026).

## Dil (24.11.2026'dan beri ana dil İngilizce)

Yurt dışı reklamı için ana dil İngilizce: **`index.html`'in HTML metni İngilizce**,
Türkçe karşılıklar dosyanın sonundaki `const TR = { … }` sözlüğünde. Metinler
`data-t="anahtar"` özniteliğiyle eşleşir. Reklam ve arama botları JS çalıştırmadan
HTML'i okuduğu için Türkçe sayfanın da metni HTML'de olmalı; bu yüzden `tr/index.html`
üretilir (Türkçe HTML + `lang="tr"` + İngilizce metin `EN_HAZIR` içinde gömülü).

- **İngilizce metni değiştirirsen TR sözlüğünü de değiştir**, sonra
  `node scripts/site-tr-sayfa.mjs` çalıştır. `tr/index.html`'i elle düzenleme.
  Güncellik denetimi: `node scripts/site-tr-sayfa.mjs --kontrol` (güncel değilse 1 ile çıkar).
- Anahtarların tamamının Türkçesi var mı (boş dizi basmalı):
  ```
  node -e 'const s=require("fs").readFileSync("site/index.html","utf8");const k=[...s.matchAll(/data-(?:t|alt)="([^"]+)"/g)].map(m=>m[1]);const tr=s.slice(s.indexOf("const TR = {"),s.indexOf("const SERIT_EN"));const e=new Set([...tr.matchAll(/"([a-z]+\.[A-Za-z0-9]+)"\s*:/g)].map(m=>m[1]));console.log([...new Set(k)].filter(x=>!e.has(x)))'
  ```
  Üretici de eksik anahtarda durur.
- Kök sayfada açılış dili: ziyaretçinin önceki seçimi (`localStorage` `codship-dil`, yalnız
  TR/EN düğmesine basınca yazılır), yoksa tarayıcı dili `tr` ile başlıyorsa Türkçe, değilse
  İngilizce. `tr/` adresi her zaman Türkçe açılır. TR/EN düğmesi iki sayfada da yerinde çevirir.
- `<head>`: iki sayfada `canonical` (kendi adresi) ve `hreflang` en / tr / x-default=en.
  Türkçe `title`/`description`/`og` metinleri üreticideki `META` nesnesinde.
- Dili değişen başka şeyler: App Store bağlantıları (`a.magaza`, `a.magaza-yorum`;
  Türkçede `?locale=tr`), demo tutarları (`TUTAR`: Türkçede ₺ ve virgül, İngilizcede $),
  şerit (`SERIT_EN` / `TR.serit`), yüzde yazımı (25% / %25).
- Yorum metinleri (`<p lang="tr">`) `data-t` taşımaz: iki dilde de olduğu gibi kalır.

**Görseller de dile göre değişir.** `<img data-gorsel="s-blok.webp" data-alt="alt.blok">`:
dil değişince `src` `gorsel/` ↔ `gorsel/tr/` arasında, `alt` sözlükten değişir. Görsel
eklersen **iki klasörde de** aynı dosya adı bulunmalı.

| Dosya (`s-*.webp`) | Kaynak (`docs/app-store-gorseller/yeni-2026-11-22/kaynak/`) |
|---|---|
| s-blok | blok-{tr,en}-secili.png (620 genişlik) |
| s-upsell, s-hediye | upsell-pencere-*, hediye-* |
| s-tesekkur | tesekkur-teklif-* |
| s-ucret, s-ucret-onizleme | ucret-*, ucret-onizleme-* |
| s-kurallar, s-kaynaklar | kurallar-*, kaynaklar-* |
| s-analitik | analitik-* (üstten 1400 px kırpıldı) |
| s-abtest, s-pano | abtest-*, pano-* |
| s-whatsapp | whatsapp-* |
| og-kapak.jpg | ../{tr,en}-0-kapak.png |

Dönüştürme: `cwebp -q 82 -m 6 -resize GENISLIK 0`. Panel ekranlarındaki rakamlar örnek veridir
(App Store görsel takımıyla aynı); sayfa metninde kullanım rakamı yok.

Eski görseller (`00-kapak.png`, `01-blok.png`, `03-teklif.png`, `04-kasa.png`, `05-dil.png`,
iki klasörde de) artık sayfada kullanılmıyor; silinmedi.

## Bölümler ve hareketler

1. **Hero** — solda başlık, App Store rozeti (puan ve değerlendirme sayısı App Store'dan),
   iki CTA; sağda **canlı demo telefonu** (aşağıda).
2. **Şerit** — özellik adları kayar (saf CSS), üstüne gelince durur.
3. **Gerçekler** — sayaçlar: 22 (blok dili, `extensions/cod-form/locales`), 21 (panel dili,
   `app/lib/i18n`), 60 (ücretsiz plan, `PLAN_ORDER_QUOTA`), %25 (yıllık indirim). Kullanım rakamı değil.
4. **Nasıl çalışır** — 3 adım; bağlayıcı çizgi görününce çizilir (telefonda dikey).
5. **Özellik vitrini** — 7 sekme, her birinde gerçek ekran (tarayıcı/telefon çerçevesi CSS).
   Görünür olunca kendiliğinden ilerler (sekmenin altında dolan çubuk), üstüne gelince durur,
   tıklanınca otomatik geçiş kapanır, ok tuşlarıyla gezilir. Uzun ekranlar çerçevede yavaşça kayar.
6. **Panel** — eğik (3B) panel görüntüsü kaydırınca düzleşir + A/B testi kartı; altında 3 kart.
7. **Entegrasyonlar** — yalnız gerçekten olanlar: tema düzenleyici (uygulama bloğu), Shopify
   ödeme sayfası (kurallar: `extensions/cod-limit`), teşekkür sayfası (`extensions/cod-thankyou`),
   Meta ve TikTok sunucu olayları (`app/lib/capi.server.ts`, Growth), WhatsApp. Marka logosu yok.
8. **Yorumlar** — App Store'daki yorumların tamamı (aşağıda).
9. **Fiyat** — aylık/yıllık düğmesi (kayan seçici). Kaynak `docs/app-store-tr.md` + `app/lib/plan.ts`.
   Ücretsiz deneme yok (K-267); SSS'de de böyle yazıyor.
10. **SSS**, **kapanış CTA**, alt bilgi.

Belirme animasyonları `IntersectionObserver` ile. `prefers-reduced-motion: reduce` diyen
ziyaretçide **hepsi kapanır**: demo telefonu okunur bir karede (2 adet + ek ürün seçili) durur,
sekmeler kendiliğinden geçmez. JavaScript kapalıysa `<noscript>` içeriği görünür kılar.
Üst barda kaydırma ilerleme çizgisi var.

## Hero'daki demo telefonu

Görsel değil, **canlı HTML** — dil düğmesi telefonun içini de çevirir. Yazılar gerçek blok ve
teşekkür sayfası metinleri (`extensions/cod-form/locales/*.json`, `extensions/cod-thankyou/locales/*.json`).
Akış (`kareler` dizisi, ~20 sn döngü): kampanya geri sayımı → 2 adet kartı seçilir (ara toplam
sayarak artar, tasarruf özeti açılır) → ek ürün işaretlenir (sepet tutarına göre hediye çubuğu
dolar) → "Continue to checkout / Sipariş Ver" → upsell penceresi (geri sayım akar) → "Yes, add it"
→ **Shopify ödeme sayfası** (müşteri bilgisi alanları bilerek boş çubuk: o form Shopify'ın, K-242)
→ kapıda ödeme seçilir → teşekkür sayfası (kapıda ödeme ücreti + WhatsApp onay düğmesi).
Telefon ekranda değilken ya da sekme gizliyken durur. Telefonun çevresindeki etiketler ve alttaki
adım çubuğu o anki adımda yanar (etiketler 1100 px altında gizli).

## Yorumlar bölümü (AÇIK, 24.11.2026)

Shopify App Store'daki yorumların **tamamı** (3 adet, hepsi 5 yıldız): Kupart (Türkiye, 16 Kasım 2026),
Dua Hanem (Birleşik Krallık, 14 Kasım 2026), SenTasarla (Türkiye, 16 Kasım 2026). Metinler
`apps.shopify.com/codship-cod-form-upsells/reviews?locale=tr` sayfasından **olduğu gibi** alındı
(yazım hataları dahil; kırpma, düzeltme, çeviri yok). İngilizce sayfada da Türkçe asıllarıyla
durur, altında "Türkçe yazıldı" notu var.

Yeni yorum gelince: aynı `<article class="yorum">` kalıbıyla ekle, hero rozetindeki ve bölüm
başlığındaki "5.0 · 3 reviews" sayısını (EN HTML + TR sözlük: `hero.rating`, `rev.score`) güncelle,
`tr/` sayfasını yeniden üret. Built for Shopify rozeti **yok**; koyma.

## Bağlantılar

| Yer | Adres |
|---|---|
| Kurulum düğmeleri | `https://apps.shopify.com/codship-cod-form-upsells` (TR: `?locale=tr`) |
| Yorum bağlantıları | `…/codship-cod-form-upsells/reviews` (TR: `?locale=tr`) |
| Yasal | `https://app.cod-ship.com/{gizlilik,dpa,veri-isleme,veri-talebi}` (24.11'de 200) |

App Store'daki demo mağaza bağlantısı süreli şifre-atlama jetonu taşıdığı için siteye konmadı.

## Yerelde bakmak

```
cd site && python3 -m http.server 8765
```

Sonra `http://127.0.0.1:8765/` ve `http://127.0.0.1:8765/tr/`. `file://` ile açma (göreli yollar).
Önizleme görüntüleri: `docs/site-yeni-onizleme/`.

## Neden Remix uygulamasının içinde değil

Uygulama (`app/`) Shopify incelemesine girer. Tanıtım sayfasının her değişikliği uygulamanın yeni
sürümünü gerektirmesin diye ayrı tutuldu.

## Yayına alırken

Statik barındırmanın herhangi biri yeter. `tr/` klasörü de yüklenmeli. `cod-ship.com` GoDaddy'de;
DNS'te **MX/SPF/DKIM kayıtlarına dokunma** — mail oradan geçiyor. Yayın Gökhan onayıyla.

## Bilerek yapmadıklarımız

- **"115.000 mağaza", "dönüşümü %X artırır" gibi rakam yok.** Kanıtlanamaz iddia hem yanlış hem
  Shopify incelemesinde sorun. Sayaçlar yalnız ürün gerçeği.
- **Uygulama form toplamaz** (K-242): sitede "kapıda ödeme formu", "müşteri bilgisi toplar" yok.
- **Destek dili iddiası İngilizcede yok** (App Store listelemesinde destek dili yazmıyor);
  Türkçede eskiden beri "Türkçe yanıtlanır" duruyor.
- **Rakip metni kopyalanmadı**; yapı kategori standardı.
- **Analytics/çerez yok**, çerez bandı da gerekmiyor (dil tercihi kullanıcının kendi seçimi).
