# Eminevim — Satış Yönetici Özeti

Satış raporlamasının yönetici katmanı için hazırlanan tek dosyalık ekran şablonu.
Amaç iki yönlü: hem sunumda gösterilecek somut bir görsel, hem de ilgili birimlerden
istenecek verinin listesini netleştiren bir gereksinim aracı.

**Son güncelleme:** 30 Eylül 2026

## Dosyalar

| Dosya | Ne işe yarar |
|---|---|
| `yonetici-ozeti.html` | **Güncel prototip.** Tek dosya (~150 KB), HTML + CSS + JS, dış bağımlılık yok. Çift tıklayıp tarayıcıda açılır. |
| `Yonetici-Ozeti-Proje-Notlari.md` | Metodoloji: MTD hedef ağırlıklandırma, gün içi projeksiyon formülü, iki katmanlı hesaplama mimarisi. |
| `Satis-Raporlama-Arastirma-ve-HTML-Degerlendirme.md` | Satış raporlaması araştırması ve kaynaklar. 11. bölüm artık silinmiş olan eski `index.html`'i tarif eder. |

## Çalışma kuralları — ÖNCE BUNU OKU

Bu dosya üstünde Claude ve Codex dönüşümlü çalışıyor. Aşağıdakiler tahmin
değil, hepsi bu ekranda bir şeyi bozduğu için yazıldı.

### Ölç, göze güvenme

- **Her değişiklikten sonra beş sayfa × dört dönem taraması** (Genel · Saha ·
  Bölge · Şube · Personel × Gün · Hafta · Ay · Yıl): konsol hatası, boş
  bölüm, `NaN`/`undefined`/`Infinity`, boş SVG. İki kanalla da (Tüm Satış /
  Alternatif) geçilmeli.
- Ayrıca her turda ölçülmesi gerekenler: **pano ölçeği** (`.wrap`
  `data-olcek`), **hero boyu**, **saha kartı boyu**, **kart hizası** (ilk
  kartın tepesi soldaki ilk bloğun tepesiyle, son kartın altı son bloğun
  altıyla — ikisi de 0 px olmalı).
- "Düzelttim" demeden önce sayıyı göster. Bu dosyadaki her düzeltme ölçülmüş
  bir öncesi/sonrası taşıyor.

### Hizalama

- **Kutu değil metin hizalanır.** Yer tutucusu olan (`min-height`) ya da
  içinde dikey yaslama olan kutuların tepesi, yazının tepesi değildir.
  `Range.selectNodeContents(el).getBoundingClientRect()` ile yazının kendi
  kutusu ölçülür. Örnek: `kapsamHizala()`.
- **İki sütun arasındaki hizayı CSS ifade edemiyorsa JS ile ölç ve
  değişkene yaz.** Hizalanacak öğe akıştan çıkarılır (`position:absolute`)
  ki yazmak yerleşimi değiştirmesin.
- Aynı anlam düzeyindeki iki yazı **aynı puntoda** olmalı (kapsam adı ile
  gerçekleşen tutar: ikisi de `clamp(34px,4.5vw,48px)`).
- Yan yana duran panellerin başlıkları aynı hizada bitmeli — yeni bir panel
  eklenirse başlığı diğerleriyle aynı `h3` biçimini almalı.

### Ölçek (tek ekran sığdırma) — en sık kırılan yer

- Pano `.wrap`'e `transform:scale()` uygulanarak ekrana sığdırılıyor. Bunun
  iki sonucu var ve ikisi de tekrar tekrar hata üretti:
  1. **`getBoundingClientRect` ölçeklenmiş değer verir.** Yerleşim pikseli
     gerekiyorsa `offsetWidth/offsetHeight/offsetLeft/offsetTop` kullan, ya
     da `.wrap` `data-olcek`'e böl. Kart boyu çivileme ve harita balonu tam
     bu yüzden bozulmuştu.
  2. **Bir blok tek bir dönemde uzarsa BÜTÜN EKRAN o dönemde küçülür.**
     Dönemler arası boy sabitliği artık yalnız hizalama değil, ölçek
     meselesi. En geniş hâle göre yer ayrılır (örn. `.btablo` Hafta'daki üç
     çubuğa göre).
- Yeni bir şey eklerken **önce/sonra ölçeği karşılaştır**. Ölçek düştüyse
  ekrandaki her şey küçülmüştür; kazancın bedelini söyle.

### Tekrar eden tuzaklar

- `:not(#id)` seçicinin ağırlığını iki kimliğe çıkarır; aynı öğeyi hedefleyen
  ikinci kural aynı şekli taşımazsa sessizce geçersiz kalır.
- `:not(body.x) .y` yazma — ara katmanlar da body değildir, kural her zaman
  tutar. Doğrusu `body:not(.x) .y`.
- `grid-auto-columns:max-content` ızgarayı kutusundan taşırır; komşu panelin
  üstüne biner. Kutuya uymak için `minmax(0,1fr)`.
- Esnek kutu içinde `width:auto` + `max-height` verilen SVG sıfıra iner
  (döngüsel ölçüm). Boyu açıkça ver, eni viewBox oranından çıksın.
- **SVG'de kesik deseni her alt yolda baştan başlar** — tek `<path>` ile
  kesintisiz dolaşan ışık yapılamaz, blok başına ayrı path gerekir.
- **Çizim fonksiyonu kendi `viewBox`'ını kurmalı.** `cizSpark` kurmadığı için
  Gün grafiği ilk yüklemede yarısı kesik çiziliyordu.
- `display:flex` verilen kapta `[hidden]` ezilir; `display` verirken
  `[hidden]{display:none}` kuralını da yaz.
- CSS'e **hedefli** dokun. İki yorum satırı arasını komple değiştirme; o
  aralıkta başka sayfaya ait kurallar olabiliyor.
- **Rastgele akışa dokunma.** Ana `PERSONEL.forEach` döngüsüne yeni bir
  `rnd()` çağrısı eklemek bütün diziyi kaydırır ve daha önce konuşulmuş her
  rakam değişir. Yeni seri ayrı tohumlu ayrı geçişte üretilir.
- Dört KPI kutusunun **sonuncusunu `cizRandevu` yazar**; kutulara topluca bir
  şey basılacaksa onun sonunda basılmalı.

## Ekranın yapısı

Üç eksen bağımsız çalışır:

- **Kanal:** Tüm Satış · Alternatif Satış Kanalı (SPY)
- **Kırılım:** Genel · Saha · Bölge · Şube · Personel
- **Dönem:** Gün · Hafta · Ay · Yıl (ileri/geri adımlanabilir)

| Sayfa | İçerik | Filtre |
|---|---|---|
| **Genel** | Ciro kartı (hedefe göre, önceki döneme göre, hedefe kalan, randevu, tahmini kapanış) · seyir (Gün'de saatlik, diğerlerinde dönem içi) · Türkiye haritası + kanal payı halkası · sağ sütunda 4 saha kartı | Yok |
| **Saha** | **Sahalar alt alta**, her blok Genel'in minyatürü: ciro + ilerleme çubuğu + tahmini kapanış + 4 KPI + dönem seyri + kanal payı halkası | Yok |
| **Bölge** | Durum şeridi · hedefe göre dağılım · liste | Saha |
| **Şube** | Öne çıkanlar · liste | Saha · Bölge |
| **Personel** | Öne çıkanlar · tablo | Saha · Bölge · Şube |

Genel ve Saha her zaman şirket geneline bakar. Listelerde arama ve sütun
sıralama vardır.

**Genel sayfası iki sütunludur:** solda ciro kartı → seyir → harita + kanal payı,
sağda dar bir sütunda dört saha kartı. Sağ sütun soldakiyle **yatayda hizalıdır**:
ilk kart ciro kartıyla başlar, son kart kanal payıyla biter. Kart içeriği bunun
için sol sütunun boyuna göre ölçeklenir (`kartSigdir`, `--ko` değişkeni); ölçek
bir kez bulunur, pencere boyutu değişene kadar korunur, böylece dönem
değiştikçe boy oynamaz.

Ciro kartının yanındaki dört KPI kutusunun (`.pair`) puntosu 30 Eylül'de bir
kez daha büyütüldü: başlık `0,92vw → 1,04vw`, değer `1,8vw → 2,02vw`
(ölçülen 28 → 31,6 px), alt satır `0,85vw → 0,96vw`. `.pair`
`flex:1 1 auto` ile hero'nun boyuna esnediği ve içinde pay olduğu için
**ciro bloğunun boyu değişmedi** (496 px, önce ve sonra).

Kart içindeki KPI satırlarının (hedefe kalan · randevu · önceki dönem) puntosu
büyütüldü: `clamp(11px,0.9vw,14px)` → `clamp(12px,1.08vw,17px)`. `kartSigdir`
kartları sol sütuna sığdırdığı için `--ko` buna karşılık 0,91'den 0,85'e
düştü; net sonuç KPI satırlarında **+%13**, kartın diğer öğelerinde **−%7**.
Yani kartın içindeki denge KPI satırları lehine değişti.

Kartların **arka yüzü** vardır (`.kart .arka`): gösterinin son aşamasında kart
döner ve o sahanın bölgeleri dönem cirosuyla listelenir. Ön yüz `.kart.arkada`
sınıfıyla gizlenir; iskelet bozulmadığı için `cizSahaKartlar` değer güncellemesi
etkilenmez.

## Gösteri (play) tuşu

Gezinme şeridinde, dönem adımlayıcısının sağında. Yalnız **Genel** kırılımında
görünür — ciro kartı ile saha kartlarını kullanıyor. Basılınca:

1. Ciro kartının sağındaki dört KPI kutusu sırayla (sol üst · sağ üst · sol alt
   · sağ alt, 620 ms arayla) yatay eksende dönerek organizasyonun adetlerini
   gösterir: **4 saha · 22 bölge · 200 şube · 2.500 personel.** İçerik kutu yan
   dönmüşken, animasyonun tam ortasında değişir. Personel adedi kanala duyarlı:
   Alternatif Satış Kanalı seçiliyken 250 yazar.
2. 1,8 sn durur, sonra aynı sırayla KPI değerlerine geri döner.
3. Saha kartları **tek tek** dolaşılır; her kart kendi içinde üç adım —
   sıra "önce sahayı tanı, sonra büyüklüğünü, sonra içini":
   - **a) Sahaya gelinir** (`sahaVurgula`): haritada o sahanın illeri öne
     çıkar, üstteki bloklar o sahaya filtrelenir ve **KPI kutularında
     sahanın kendi KPI'ları okunur** (hedefe göre · önceki döneme göre ·
     hedefe kalan · randevu). Rakamlar akarak geçer, **kutu dönmez**.
     1,5 sn durur.
   - **b) Kutular dönüp adetleri gösterir** (`gosteriAdetCevir`):
     bölge · şube · personel · il. Kökte dördüncü kutu "saha"dır; bir
     sahanın altında saha olmadığı için yerine o sahanın kapsadığı il
     sayısı geliyor — haritayla da bağ kuruyor. Dördü birden, 0,44 sn.
     1,5 sn durur.
   - **c) Kartın kendisi arka yüzüne döner** (`kartCevir`): o sahanın
     bölgeleri, dönem cirosuna göre sıralı. Dönüş 0,55 sn, içerik kart tam
     yan dönmüşken (270 ms) değişir; 1,7 sn durur.

   Kart **ön yüzüne dönmeden** sıra öbür sahaya geçer; dönmüş kartlar
   birikir. Kart başına ~5,3 sn.
4. Turun sonunda dört kart 90 ms arayla ön yüzlerine döner, kapsam
   Türkiye geneline çıkar, sonra başa sarar. Tuşa yeniden basılınca durur;
   dönen kutular, çevrilmiş kartlar, çivilenmiş kart boyları ve saha
   vurgusu temizlenir.

Bir tur yaklaşık 31 saniye (ölçülen: kart 1 → 7,2 sn · kart 4 → 23,2 sn ·
kartlar öne 28,8 sn · başa sarma 31,2 sn).

**Kart boyu dönüşte değişmez.** Arka yüz ön yüzden kısa; `#sahaKartlar`
ızgarası `align-content:stretch` ile boşluğu satırlara dağıttığı için bir
kart arkaya dönünce dördü birden oynuyordu. İlk dönüşte `kartlariSabitle()`
her kartın o anki boyunu piksel olarak çiviliyor, gösteri bitince
`kartlariCoz()` bırakıyor (ölçülen: dönüş öncesi ve sonrası 201 px; ara
değerler yalnız `rotateX` animasyonunun kendisi).

**Sıranın geçmişi.** Önce dört evre vardı (dört kart dolaşılır → dördü
birden arkaya döner); sonra kart kart yapıldı ama vurgu ile adetler aynı
anda geliyordu, sahanın kendi KPI'ları hiç görünmüyordu. Şimdiki üç adım
bunu ayırıyor. Bunun bir sonucu: **kutu dönüşü artık `sahaVurgula`'nın
içinde değil** — vurgulama her zaman (elle de, gösteride de) yalnız değer
yazıp rakamları akıtıyor, dönüş gösterinin ayrı bir adımı. `GOSTERI.turda`
bayrağı bu yüzden kaldırıldı.

**Sıra bilinçli:** saha turunda `sahaVurgula()` doğrudan `cizCiro()` çağırıyor,
yani kutulardaki adetleri ezerdi. Bu yüzden adetler önce gösterilip kutular
geri döndürülüyor, tur ondan sonra başlıyor.

Gösteri sırasında herhangi bir yeniden çizim (`ciz()`) gösteriyi durdurur —
kırılım, dönem, kanal, arama ya da pencere boyutu değişimi. Yarıda kalan
kutular KPI değerlerine geri yazılır, saha vurgusu kaldırılır.

## Sayfa genişliği

`--sayfa-en` tek yerden ayarlanır (`:root`), `.wrap` · `.top-in` · `.navbar-in`
üçü de ona bağlı. Eskiden 1440 px'lik tavan vardı; genel müdürlükteki geniş
ekranda iki yanda boşuna boşluk kaldığı için kaldırıldı, şimdi `100%`.
Çok geniş ekranda tekrar bir tavan istenirse değiştirilecek tek yer orası.

Genişlik yayılınca haritanın sütunu da genişledi: harita 660 px'lik kendi
tavanında kalıyor, sağında boşluk oluşuyor. Harita satırının düzeni (harita ·
kanal payı) bu yüzden yeniden ele alınacak — bkz. "Sıradaki adımlar".

## Dönem içi seyir grafiği

- **Dikey eksen yoktur.** Tutar etiketleri ve ızgara kaldırıldı; değerler
  doğrudan noktaların üstünde yazılı. Yalnız taban çizgisi ve alttaki dönem
  etiketleri kalır.
- Etiketler sığdığı kadar yazılır (`etiketAdimi`), kalanı atlanır. **Son nokta
  her zaman yazılır** — bakılan rakam odur; tahmin etiketiyle çakışmasın diye
  çizginin altına alınır.
- **Hafta ve Ay birikimlidir**: dönem başından o ana kadarki toplam, artı
  bugünden dönem sonuna uzanan kesikli projeksiyon. Projeksiyonun bittiği
  nokta, karttaki **tahmini kapanış** rakamıdır.
- **Dilimleme:** Yıl → ay ay · **Ay → hafta hafta** · Hafta → gün gün ·
  Gün → saat saat. Ay bir süre gün gün bakılıyordu (ayın nerede kırıldığı
  okunsun diye); 30 nokta eğriyi gereksiz ayrıntıya boğduğu ve tutarların
  çoğu sığmadığı için atlandığı için yedişer günlük öbeğe döndü
  (`1–7`, `8–14`, … son öbek ay kaç güne bitiyorsa orada kesilir). Ciro ile
  kayıt seyri artık dört dönemde de **aynı dilimlemeyi** kullanıyor, iki
  grafik aynı x eksenini okuyor.
- Birikimli grafikte **dilim sütunu yoktur**. Günlük ciro ile birikimli toplam
  iki farklı büyüklük; tek eksene bindirilince ikisi de okunmuyordu.
- **Birikimli hedef eğrisi de gösterilmez.** Hedef, ciro kartındaki ilerleme
  çubuğunda ve yüzdelerde zaten var. (Hedef ağırlıkları hesapta kalır:
  projeksiyonun kalan günlere dağıtımı `gunlukHedef` paylarına göre yapılır.)
- **Yıl** görünümünde sütun asıldır (aylık ciro + geçen yıl çizgisi):
  mevsimsellik dilim bazında okunur, birikimlide kaybolur.
- **Gün** görünümünde büyük grafik yoktur; ciro kartı içindeki saatlik birikim
  çizgisi kullanılır.
- **Seyir tek bir alandır ve dört dönemde de aynı görünür.** Ciro kartının
  altında, zeminsiz ve kenarlıksız; küçük başlık (Gün'de "Saatlik seyir",
  diğerlerinde "Dönem içi seyir") ve sağda tek satır açıklama. Ayrı bölüm
  (`secTr`) ve zeminli `.chart` kutusu kaldırıldı.
- Ortak ölçü `SEYIR` sabitinde: 1000×120, L=R=46, T=B=24. Dönem değişince
  grafik alanı yerinden oynamaz.
- Lejant yerine sağ üstte tek satır not kullanılır ("kesik çizgi: tahmini
  kapanış" / "kesik çizgi: geçen yıl"). Saha bloklarında lejant kalır —
  çizim fonksiyonu `el.leg` verilmezse lejantı atlar.
- Çizgi rengi dönemin tamamına ait **tek bir yargıdır** (hedefte / izleme /
  risk). Saha bloklarında ise sahanın kendi kimlik rengi kullanılır — çizim
  fonksiyonu hedef SVG'yi ve rengi parametre olarak alır.

## Harita satırı: harita solda, bölgeler sağında

Harita kendi sütununda **sola dayalı**; sağında kalan yere **"Bölgeler"**
başlığı ve bölge listesi girdi (ad + H/G veya dönem cirosu). Liste hedef
olan dönemlerde H/G'ye, Yıl'da ciroya göre sıralıdır.
Başlık "Dönüşüm oranı" ve "Toplam kanal dağılımı" ile aynı biçimde ve aynı
hizada (ölçülen fark 0 px). Harita yükseklik sınırına
göre ölçeklendiği için yanında her zaman epey boşluk kalıyordu.

- Genel kapsamda 22 bölge, saha önizlemesinde o sahanın bölgeleri görünür.
  Bölge satırının üstüne gelince ya da satıra tıklayınca üst metrikler o
  bölgeye geçer; liste yerinde kalır ki başka bölge seçilebilsin.
- **Satır boyu sıkı (20 px):** 11 satır + başlık haritanın boyuna sığmalı.
  Gevşek bırakıldığında harita satırı 323 → **369 px**'e çıkıyor ve tek-ekran
  ölçeği bütün panoyu küçültüyordu (0,864 → 0,824).
- **Sütun KAYMAZ, yalnız dönüş tuşu sağa dayanır.** Başlık da satırlar da
  her zaman haritanın hemen sağındaki aynı yerde başlar; ilk sütun dibe
  kadar iner, fazlası sağdaki sütundan devam eder.

  > Önce `margin-left:auto` ile bütün sütun sağa yapıştırılmıştı. Hata:
  > az bölgeli bir saha seçilince (tek sütun) "Bölgeler" başlığı da onunla
  > sağa kaçıyordu. Doğrusu, tuşun konumlanma bağlamını sütundan KARTA
  > taşımak: `position:relative` `.bolge-kol`'dan alınıp `.hrt-kol`'a
  > verildi. Ölçülen sonuç — 22 bölgede ve 6 bölgeli sahada başlığın sol
  > kenarı ikisinde de haritadan 16,5 px, tuş ikisinde de kart kenarından
  > 6 px, harita 490 px'i koruyor.
- **Sığmayan isimler sağa yapışmakla çözülmüyor** — darboğaz listenin yeri
  değil satırın içi. Ölçülen (1600×900, ölçeksiz px): satır 165, isim kutusu
  **64** (H/G yüzü) / 72 (ciro yüzü), değer kutusu 96 / 88, aradaki boşluk 5.
  En uzun ad ("Muğla–Batı Akdeniz") 130 px istiyordu; 22 addan 16'sı
  kırpılıyordu. Tam sığması için satırın 231 px olması gerekiyordu, yani sütun
  başına +66 px. İki kolla çözüldü, ikisi de düzenden feragat istemiyor:
  - **H/G çubuğu 96 -> 60 px** (`.hg-iz`). Boşalan 36 px isme gitti:
    **isim kutusu 64 -> 100 px**, kırpılan ad 16 -> 10. Yüzde yazısının
    gezinme payı daralmasın diye kenar payı da 17 -> 13 px yapıldı.
  - **Kısa ad tablosu** (`BOLGE_KISA`, ağaçta `b.kisa`). Yalnız sığmayan 11 ad
    için: "İstanbul Anadolu 1" -> "İst. Anadolu 1", "Muğla–Batı Akdeniz" ->
    "Muğla–B. Akd." gibi. **TAM ad korunuyor**: bütün öteki sayfalarda, satırın
    `title`ında ve `aria-label`ında tam hâliyle duruyor — kısa ad yalnız bu
    dar listenin gösterdiği metin. Tabloda olmayan ad kendi tam hâliyle yazılır.

  Ölçülen sonuç: **H/G yüzünde hiç kırpılma yok** (0/22).
- **Ciro yüzü hâlâ kırpıyor** (13/22): orada darboğaz sabit çubuk değil, tam
  tutar yazısı. "₺28.848.453" `white-space:nowrap` ile 88 px istiyor ve isme
  72 px kalıyor. Çözmek için tek kol var: bu listede kısa para biçimi
  ("₺28,8 M"), ~45 px kazandırır. Bir biçim/kesinlik kararı olduğu için
  yapılmadı; tam ad zaten `title`da.
- **Sütunlar dengelenmiyor: ilk sütun dibe kadar iner, fazlası yana geçer.**
  Satır sayısı sabit değil, `bolgeSatirSayisi()` ile ölçülen kutu boyundan
  çıkar (`floor(boş ÷ satır boyu)`). Eskiden sabit `BOLGE_SATIR = 11`'di ve
  22 bölge her zaman düzgün iki sütuna bölünüyordu; kutunun boyu haritaya
  bağlı olduğu için o 11 satır dibe varmıyor, altta boşluk bırakıyordu.
  Sabit artık yalnız ilk çizimdeki tahmin — o anda ölçülecek satır yok.
  Ölçülen: 1600×900'de 15 + 7, 1366×768'de 14 + 8; taşma yok.
- **Sütun geni tavanlı** (`minmax(0,clamp(120px,10.3vw,172px))`), `1fr`
  değil. `1fr` listeye verilen bütün genişliği bölüşüyor, ölçülen 1600×900'de
  sütun 185 px çıkıyor ve ad ile değer arasında gereksiz boşluk kalıyordu.
  Boşalan genişlik haritaya geçti (`.harita-sar max-width` 52% → 58%):
  **liste 389 → 299 px, harita 439 → 490 px (+%11,5)**, aradaki boşluk
  16,5 px. Harita genişlerken UZAMIYOR — boyu `height:clamp()` ile açıkça
  verili, genişlik yalnız kutunun içindeki letterbox boşluğunu yiyor; satır
  boyu 248,8 px'te sabit kaldığı için **tek-ekran ölçeği değişmedi**.

> **İki tuzak, ikisi de ölçümle yakalandı.** (1) Liste `flex:1 1 auto` iken
> kendi içeriği sütunun "varsayılan boyu" oluyor, `align-items:stretch`
> satırı ona göre uzatıyor, uzayan kutuya daha çok satır sığıyor ve ölçüm
> kendi kuyruğunu kovalıyordu: satır 248,8 → 349,4 px, ölçek 0,859 → 0,822,
> yani bütün pano küçülüyordu. Çözüm `flex:1 1 0` + `min-height:0` +
> `overflow:hidden` — kutunun boyunu harita belirliyor, liste ona uyuyor.
> (2) Satır boyu `getBoundingClientRect()` ile okunuyordu; pano tek ekrana
> bir transform ile sığdırıldığı için rect ölçeklenmiş boy veriyor, üstelik
> değer dönüşü (`.btl.cevir`) satırı `scaleY` ile kısaltıyor — dönüşün
> ortasında 18 satır seçilip 3 satır kırpılıyordu (taşma 46 px).
> `offsetHeight` / `clientHeight` düzen boyunu verir, transformu saymaz ve
> ikisi aynı uzayda kalır.
- Rakamlar akarak geçiyor (`iskelet` + `sayiAk`), liste değişmedikçe
  yeniden kurulmuyor.
- **H/G yüzü:** çubuk yüzdeyle aynı satırda; doluluk `H/G ÷ 100` ve en çok
  %100. Yazı gerçek değeri gösterir, %100'ü aşsa da çubuk taşmaz. Renk
  yalnız açık–koyu yeşil tonlarıyla 0–1 aralığında değişir.
- **Ciro yüzü:** çubuk yoktur. Görünen bölgelerin ciroları kendi arasında
  doğrusal olarak kıyaslanır; düşük ciro kırmızı, orta sarı, yüksek yeşildir.
  Bu renk hedef başarısını değil yalnız göreli ciro büyüklüğünü anlatır.

**İki tuzak:**

1. Esnek kutu içinde `width:auto` + `max-height` verilen SVG sıfıra iniyor
   (döngüsel ölçüm). Haritanın **boyu açıkça** veriliyor, eni viewBox
   oranından çıkıyor.
2. `grid-auto-columns:max-content` ızgarayı kutusundan taşırıyor. Dar
   ekranda liste sağdaki "Dönüşüm oranı" panelinin üstüne biniyordu —
   ölçülen 1600×900'de **183 px**, 1366×768'de **114 px**. Sütunlar artık
   `minmax(0,1fr)`, yani kutuya göre boyutlanıyor; ad sığmazsa üç noktayla
   kısalıyor. Harita da sütunun yarısını geçmiyor (`max-width:52%`) ki
   listeye her zaman yer kalsın, ve `preserveAspectRatio="xMinYMid meet"`
   ile kutusu daralınca sola yaslı kalıyor.

Ölçülen (çakışma yok, ad kısalması yok): 1920×940 harita 686 / liste 636 ·
1600×900 harita 515 / liste 456 · 1366×768 harita 462 / liste 410.

## Harita satırının dikey dağılımı

**viewBox'taki ölü şerit (düzeltildi).** Harita kutusu özgün dosyadan gelen
`0 0 1007.478 527.323` idi, ama yolların gerçek sınırı **1007,5 × 443,5**:
altta %16'lık boş bir şerit taşınıyordu. Harita yükseklik sınırına göre
ölçeklendiği için o şerit doğrudan haritayı küçültüyordu — ölçülen: 900×303
kutunun içinde harita 578×254 çiziliyor, **altta 49 px ölü alan** kalıyordu
(kartın içindeki boşluk üstte 10, altta 59 px). Kutu yolların sınırına
çekildi (`-1 -1 1009.5 445.5`, 1 birim pay `.il` konturu için). Sonuç:
harita **685×301** (+%18), üst ve alt boşluk 11/11 px, satır boyu ve sayfa
ölçeği değişmedi.


Satırın boyunu harita belirliyor (ölçülen 303 px); dönüşüm oranı ve toplam
kanal dağılımı ondan kısa kalıyor. Artan yer tek bir yerde toplanınca
açıklama satırının hemen üstünde delik açılıyordu — **ölçülen 66 px (huni)
ve 42 px (kanal)**. Boşluğu bir yere yığmak yerine içeriğe dağıtıldı:

- `.huni` dikey flex + `space-between`, **çubuklar kalın**
  (`clamp(13px,2.2vh,24px)`, ölçülen 9 → 21 px). Önce yalnız `space-between`
  denendi: boşluk kademe aralarına dağılıp 18 px'lik açıklıklar bırakıyordu
  ve göze batıyordu. Artan yeri aralığa değil çubuğa vermek hem huniyi
  dolduruyor hem kademe farkını okunur kılıyor — aralık **9 px**.
- Halka sütunun verdiği kadar büyür (`max-height` 160 → 200 px tavan,
  ölçülen 132 → 169 px), lejant hemen altında.
- Harita sütununun yanındaki **bölge listesi başlığın hemen altından başlar**
  (`.bolge-liste{align-content:start}`). Önce `center` idi: artan yeri
  satırların üstüne ve altına bölüştürüyor, "Bölgeler" başlığıyla ilk satır
  arasında — liste haritadan kısa kaldığı her durumda — boşluk açılıyordu ve
  başlık listeden kopuk duruyordu. Satırın geri kalanındaki kuralın aynısı:
  artan yer üste yığılmaz.
- Üç sütunun da alt kenarı artık aynı hizada bitiyor (10 px).

## Türkiye haritası

Genel sayfada, seyrin altında. Harita **tek renktir** (marka yeşili); koyuluk o
ilin dönem cirosunu **logaritmik ölçekte** gösterir. Saha bölünmesi renge
değil etkileşime bırakıldı: bir ilin üstüne gelince o sahanın illeri öne çıkar.
Şubesi olmayan iller boş (gri) kalır.

- **Ciro ile ilin bağı şube üzerindendir.** `BOLGE_TANIM` içindeki her şube
  `"Şube|plaka"` biçiminde yazılır (`Kadıköy|34`); şube kurulurken `su.il`
  alanına geçer ve ciro şubeden ile toplanır. Gerçek şube listesi gelince
  yalnız bu plakalar güncellenecek.
- **Boyut ve ortalama:** viewBox 1,91:1 olduğu için çizilen boyu yükseklik
  sınırı belirliyor. Sınır `clamp(218px, 32.2vh, 500px)`, genişlik tavanı
  900 px. Genişlik tavanı 700'den 900'e çıkarıldı: yüksek ekranlarda
  yükseklik sınırına varılmadan genişlik bağlanıyor ve harita boşuna küçük
  kalıyordu. Sarmalayıcı (`.harita-sar`) flex ile hem yatay hem dikey
  ortalar, iç boşluğu sıfırlandı.

  **Harita satırın boyunu tek başına belirliyor** (huni 224 px, kanal payı
  209 px doğal boy; harita 303 px). Yani haritayı büyütmek doğrudan sayfayı
  uzatıyor. İki turda büyüdü: 271 → 287 px (gereken yer `.hsag` dikey iç
  boşluğundan alındığı için bedavaydı), sonra 287 → **303 px**. İkinci
  turda kart iç boşluğu da geri açıldı — içerik kartın tepesine yapışık
  duruyordu — `clamp(4px,0.5vh,8px)` → `clamp(10px,1.45vh,18px)`. Ölçülen
  (1920×940): harita satırı 297 → **331 px**, harita kartın içinde üstten
  ve alttan 14 px boşlukla ortalı.

  **Bunun bedeli katlamadır:** `.gdz` altı 933 → **967 px** (Hafta 944 →
  **978 px**), katlama 940 px. Yani harita bloğunun altı 1080p tam ekran
  tarayıcıda 27–38 px aşağıda kalıyor. Yer açmak gerekirse en yakın kaynak
  seyir satırıdır (129 px) — bkz. "Ekrana sığma".
- **Koyuluk ölçeği logaritmiktir:** `0,22 + 0,78 × log(ciro/en az) / log(en
  yüksek/en az)`. Taban opaklık, en düşük cirolu ilin de haritada
  seçilebilmesi için. Ciro ve koyuluk **seçili döneme göre** hesaplanır;
  Gün / Hafta / Ay / Yıl arasında geçince harita yeniden çizilir.

  Eskiden ölçek doğrusal orandı (`(ciro/en yüksek)^0,6`). Veri iki büyüklük
  mertebesine yayıldığı için — İstanbul 32,3 M ₺ ile toplamın %30'u, ortanca
  il 1,0 M ₺, yani en yüksek ilin **%2,7**'si; çünkü İstanbul'da ~57 şube var,
  çoğu ilde 1 — 49 ilin çoğu 0,27–0,38 aralığına sıkışıp birbirinden ayırt
  edilemiyordu. Log ölçek sıralamayı ve büyüklük hissini koruyarak aralığı
  dengeli kullanır: ölçülen çeyrekler **0,28 / 0,38 / 0,51** (eskisi
  0,28 / 0,31 / 0,38), dört dönemde de benzer.

  Denenen diğer seçenekler: üssü 0,35'e indirmek (alt çeyrek hâlâ sıkışık
  kalıyor, harita topyekûn koyulaşıyor) ve sıraya göre ölçeklemek (ayırt
  etme gücü en yüksek ama büyüklük siliniyor — İstanbul 32,3 M ile İzmir
  12,5 M neredeyse aynı tonda çıkıyor).

  **Taban koruması:** ölçek en fazla 200 kat aralığa yayılır
  (`enaz = max(gerçek en az, en yüksek/200)`). Tek bir satışsız il ölçeği
  gereksiz yere gerip bütün haritayı koyulaştırmasın diye.
- **Genel sayfası iki sütunludur** (`.gdz`): solda ciro bloğu, seyir ve harita;
  sağda dar bir sütunda dört saha kartı, sayfanın en üstünden başlayarak.
  Vurgulama üçünü birden etkilediği için aynı anda görünmeleri gerekiyor.
  1000 px altında tek sütuna düşer.
- **Karşılıklı vurgulama:** bir ilin üstüne gelince o sahanın illeri öne çıkar
  (diğerleri %10 opaklığa düşer, seçililer koyu konturlanır) ve yanındaki saha
  kartı vurgulanır. Tersi de geçerli. Saha rengi haritada kullanılmadığı için
  ayrım yalnız bu etkileşimde görünür.
- **Hover önizlemesi:** harita ya da kart üstündeyken **üstteki ciro bloğu ve
  seyir o sahaya filtrelenir** (`S.onizleme`, `kapsam()` üzerinden). Fare
  çekilince genele döner. Başlıkta "· önizleme" ibaresi çıkar ve ciro kartı
  hafif çerçevelenir. Harita ile kartlar bu sırada **yeniden çizilmez**: fare
  onların üstünde durduğu için yeniden üretim vurgulamayı düşürürdü.
- Saha kartına tek tıklama önizlemeyi sabitler; başka sahaya tıklamak seçimi
  değiştirir, ekranın başka yerine tıklamak genele döner. Bölge satırları da
  hover ve tek tıklamayla kendi verisine geçer/sabitlenir.
- **İkinci tık ayrıntıya iner** (`detayaIn`): zaten sabitlenmiş olan karta ya da
  satıra bir kez daha tıklamak bir alt kırılımı açar — saha → **Bölge** sayfası
  (o saha filtreli), bölge → **Şube** sayfası (o bölge filtreli). Kararı
  `sabitMi()` verir: ölçüt sabit seçimdir, hover değil — hover sabit seçimin
  üstüne önizleme bindirebiliyor, ama inilecek yeri sabit olan belirler.

> Önceden bu iş çift tıklamadaydı (`ondblclick`). Dokunmatikte çift dokunma
> hem zor hem görünmez olduğu için "bir kez daha seç" hareketine taşındı.
> Ayrı bir çift tık dinleyicisi gerekmiyor: çift tıklamanın ikinci click
> olayını zaten aynı dal yakalıyor, yani fareyle çift tıklama da çalışıyor.
> Aynı tıkla sabitlemeyi çözme (toggle) yok; genele dönüş dışa tıklamayla.
- İlin üstünde küçük bir balon açılır: il adı, dönem cirosu ve şube sayısı.
- Dinleyiciler tek tek illere değil **SVG'nin kendisine** bağlanır
  (`mouseover` / `mouseout` delegasyonu); iller her çizimde yeniden üretildiği
  için tek tek bağlamak hem pahalı hem kırılgan olurdu.
- Harita genişliği 660 px ile sınırlı ve ortalıdır; sayfanın geri kalanını
  ezmemesi için.
- **İl sınırları:** [SVG Türkiye Haritası](https://github.com/dnomak/svg-turkiye-haritasi)
  — Doğukan Güven Nomak, MIT lisansı. Path'ler Douglas-Peucker ile 0,5 birim
  toleransla sadeleştirildi: 298 KB → 54 KB. Veri `IL_YOL` dizisinde
  `[plaka, il adı, path]` biçiminde gömülüdür; dosya hâlâ tek parça ve dış
  bağımlılığı yoktur.

## Dönüşüm oranı

Harita satırının orta sütununda. Satırın boyunu harita belirlediği için bu
iki yan panelin (dönüşüm oranı · kanal payı) altında ölü boşluk kalıyordu —
ölçülen 68 ve 83 px — ve içerik kartın tepesine yığılmış görünüyordu.
Başlıkları yerinden oynatmadan içerik dağıtıldı: panel dikey flex, gövde
(huni / halka + lejant) otomatik kenar boşluklarıyla ortalanır, açıklama
satırı dibe oturur. Panel boyu değişmiyor, yalnız içerik yerleşiyor; iki
açıklama satırı da haritanın alt kenarıyla aynı hizada (14 px) bitiyor. Üç kademe, hepsi adet:
**Randevu → Kart → Kayıt.** Kademeler arasındaki yüzde bir üst kademeden
dönüşüm. Ölçülen değerler (Gün): 6.613 randevu → %22 → 1.434 kart → %31 →
446 kayıt.

- **Kart**, randevu ile kayıt arasında duruyor: randevu alınmış müşterilerin
  bir kısmında kart açılıyor, kartların bir kısmı kayıtla kapanıyor.
- Sıralamanın hiçbir dilimde ve hiçbir kırılımda bozulmaması için kart
  **kayıttan yukarı** türetilip **randevuyla tavanlanıyor**
  (`min(randevu, kayıt × KART_KAT × dalgalanma)`, `KART_KAT = 3,3`).
  Bağımsız üretilseydi bazı günlerde kart randevuyu aşabilir ya da kayıtın
  altına düşebilirdi.
- Blok saha önizlemesini izler: kart ya da il üstüne gelince o sahanın
  değerlerine döner.
- **Hizalama:** `.hunipan` ve `.paypan` satırın tepesine yaslanır
  (`align-self:stretch`), böylece "Dönüşüm oranı" ile "Toplam kanal dağılımı" başlıkları
  aynı satırda okunur ve aradaki ayırıcı çizgiler satırın tam boyunca iner.
  Ortada kalsalardı içerik boyları farklı olduğu için başlıklar 39 px
  kayıyordu.

## Teslimat

Ciro kartının sağ üstünde, kapsam etiketinin ("Türkiye Geneli") hemen altında:
**"299 Teslimat"**. Önce kanal payının altındaydı, oradan buraya alındı;
boşalttığı yer haritaya gitti.

**Bilerek dönüşüm bloğunun dışında:** teslimat bugünün satışının değil,
geçmişte satılanların sonucu; satış ölçüleriyle aynı blokta durursa yanlış
okunur. Kompakt biçim için önceki döneme göre değişim gösterilmiyor.

Veri, gecikmeli bir kayıt serisinden türer (`TES_GECIKME = 75` gün): yılın ilk
günlerinde gecikmeli kaynak olmadığı için o aralık kısılarak doldurulur.
Ölçülen: 299/gün · 8.190/ay · 67.487/yıl.

## Kanal dağılımı

Haritanın sağında. Cironun ne kadarının **şubeden**, ne kadarının **saha
personelinden** (SPY) geldiğini gösterir — iki dilim, yanında tutar ve yüzde.

- **Üstteki kanal seçiminden bilerek bağımsızdır.** "Alternatif Satış Kanalı"
  seçiliyken pay %100 saha çıkardı, o da hiçbir şey anlatmazdı. Halka her zaman
  iki kanalın toplamı üzerinden hesaplanır; panelin altında bu not yazılıdır.
- **Saha önizlemesini izler:** haritada ya da bir saha kartında bir sahanın
  üstüne gelince o sahanın kanal kırılımına iner; kart başlığı sabit kalır.
- Terim çakışmasına dikkat: buradaki **"Saha"** satış kanalıdır (SPY), ekranın
  geri kalanındaki **saha bölgesi** (İstanbul / Batı / Orta / Doğu) değildir.

## Ciro bloğu ile KPI bloğu arasındaki ayırıcı

Düz gri çizgi yerine markanın yeşilinden fıstığa giden, uçlarda sönen 2 px'lik
bir şerit (`.hero-l` üstünde bir arka plan gradyanı, `border-right` değil).
**Altın bilerek kullanılmadı:** bu ekranda altın "hedef" demek, kozmetik bir
çizgi o anlamı taşımamalı. Dar ekranda `.hero` tek sütuna düştüğünde gradyan
kapatılıp yerini yatay `border-bottom` alıyor.

## Renk paleti

Kurumsal renkler eminevim.com'dan alındı:

| Değişken | Değer | Yer |
|---|---|---|
| `--brand` | `#00724C` | marka yeşili; başlıklar, seçili düğmeler, harita |
| `--brand-deep` | `#00553A` | üst başlık şeridi |
| `--ink` | `#1E3856` | kurumsal lacivert; ana metin |
| `--fc` | `#2B8C8A` | turkuaz; tahmin/projeksiyon |
| `--gold` | `#C9A961` | altın; hedef işareti |
| `--fistik` | `#A1CB3A` | fıstık yeşili; dördüncü saha rengi |

Durum renkleri ayrıdır: hedefte `--good` (marka yeşili), izleme `--watch`,
risk `--bad`. Saha kimlik renkleri (`SAHA_RENK`) marka paletinden seçilir:
yeşil · turkuaz · lacivert · fıstık.

**Katman dili:** sayfa zemini kırık beyaz (`--surf`), bloklar beyaz + ince
kenarlık + yumuşak gölge (`--golge`, `--golge-uf`). Tek zemine indirmek düzlemi
yassılaştırdığı için bu üç seviye korunuyor.

## Ölçüm kuralları

- **Ciro**, seçili dönemde gerçekleşen satış tutarıdır.
- **Hedef** her kırılım için ayrı tanımlıdır ve kırılımlar arasında toplanmaz.
  En üst özette hedef, 4 saha bölgesi hedefinin toplamıdır.
- Ekranda gösterilen yüzde, dönem hedefine göre **ilerlemedir**. Renk ve ok ise
  dönemin geçen kısmına göre **beklenen seyre** bakar. Bu yüzden ayın başında
  "%22 ▲ yeşil" görülebilir: ilerleme düşüktür ama tempo yerindedir.
- **Yıl** görünümünde hedef sütunu yoktur; yerine **Ort.** (yıl başından bugüne
  gerçekleşen ÷ bugüne kadarki hedef) gösterilir.
- Karşılaştırmalar önceki dönemin **aynı kesitiyle** yapılır. Bugün yarım gün
  olduğu için geçen haftanın aynı günü de aynı saate kadar kısaltılır.
- Hafta Pazartesi–Pazar sayılır.
- Eşikler: ≥%100 hedefte · %95–99,9 izleme · <%95 risk. Gerçek veriyle kalibre edilecek.

## Veri durumu

Ekran **temsili veriyle** çalışır; CRM veya veri ambarı bağlantısı yoktur.
Bölge ve şube adları da temsilidir.

Bugünün cirosu tam gün değil, **geçen saatlerin payı** kadardır (`GUN_PAY`).
Gün içi saatlik dağılım tipik bir gün profilinden türetilir — satış kayıtlarında
saat:dakika damgası bulunmadığı için. Gerçek saatlik takip bu alanın kaynaktan
gelmesine bağlıdır ve veri talebi listesindedir.

## Ayarlanabilir sabitler

`<script>` bloğunun başında, tek yerde:

| Sabit | Ne yapar |
|---|---|
| `KESIT` | Veri kesiti tarihi ve saati. Sunumda ekranın hep aynı görünmesi için sabittir. |
| `OLCEK` | Ortalama personel aylık cirosu. Rakamların mertebesini buradan ayarlayın. |
| `SPY_ADET` | Alternatif Satış Kanalı personel sayısı. |
| `ESIK` | Hedefte / izleme eşik yüzdeleri. |
| `SAHALAR`, `BOLGE_TANIM` | Organizasyon ağacı. Gerçek bölge ve şube listesi geldiğinde yalnız bu blok değiştirilir. |
| `SAAT_W` | Gün içi saat ağırlık profili (09:00–19:00). |

Tipografi CSS'te dört ölçeğe bağlıdır: `--f1` ana rakam, `--f2` ikincil rakam,
`--f3` gövde, `--f4` etiket. Dar ekranda dördü birden küçülür.

Şu anki varsayım: 4 saha bölgesi, 22 bölge, 200 şube, 2.500 personel (250'si SPY).

**Ölçüler:** ciro (tutar) · kayıt (adet) · kart (adet) · randevu (adet) ·
teslimat (adet). Hepsi aynı kırılım ağacında toplanır ve aynı kanal
filtresinden geçer. Hedef yalnız ciro için tanımlıdır.

## Sabit başlık

Başlık şeridi ve gezinme çubuğu `position:sticky` ile yukarıda kalır. Gezinme
çubuğu başlığın altına yapışır; başlığın yüksekliği satır kaydırmasıyla
değiştiği için `ustOlc()` onu ölçüp `--top-h` değişkenine yazar (açılışta ve her
yeniden boyutlandırmada). Şerit yükseklikleri daraltıldı: başlık 8 px, gezinme
7/8 px iç boşluk, düğmeler 32 px.

**Dönem adımlayıcısının genişliği sabit.** `.stepper span` önce
`min-width:118px` ile duruyordu, ama etiketin kendisi bundan uzun olabiliyor:
ölçülen (12 px gövde) **"25 Ağustos – 31 Ağustos" 132 px**, "28 Ağustos
Pazartesi" 113 px, "Ağustos 2026" 74 px, "2026 yılı" 46 px. Adımlayıcı
büyüyünce `.zaman` `margin-left:auto` ile sağa yapışık olduğundan **dönem
düğmeleri sola kayıyordu** — Gün ↔ Hafta ↔ Ay arasında gezinirken şerit
görünür biçimde git gel yapıyordu. Çözüm, en uzun etikete göre sabit
`width:13em` (11,01em metin + 2×10 px padding; `box-sizing:border-box`).
px değil **em**, çünkü `--f4` kırılımla değişiyor ama metnin punto oranı
değişmiyor — dar ekranda padding kısaldığı için `12.6em`. Ölçüm: dört dönemde
de adımlayıcı **225,3 px**, etikette taşma yok.

## Hareket katmanı

Ekran iki tür hareket kullanır. İkisi de `prefers-reduced-motion: reduce`
altında dosyanın başındaki `*{transition:none!important;animation:none!important}`
kuralıyla topluca kapanır; JS tarafı ayrıca `SAKIN` ile aynı tercihi okur.

### Geçişler — veri değiştiğinde

Çizim fonksiyonları düğümleri her seferinde `innerHTML` ile yeniden üretmez.
Yapı aynı kaldığı sürece yalnız değerler güncellenir; böylece CSS geçişleri
çalışır ve çubuklar sıfırlanıp yeniden dolmak yerine eski orandan yeni orana
kayar.

| Parça | Nasıl |
|---|---|
| Rakamlar | `sayiAk(el, değer, biçim, tür)` — eski değerden yenisine akar. `tür` değişirse (yüzdeden TL'ye) akış yapılmaz, ara değer anlamsız olurdu. |
| Ciro çubukları | `iskelet()` ile satır **sayısı** imzalanır (etiket metni imzaya girmez, yoksa her dönem değişiminde çubuk sıfırlanıp yeniden dolardı); aynı imzada `.fill` / `.mark` / `.fcast` yerinde kalır, genişlikleri CSS geçişiyle taşınır. |
| Harita | 81 il yolu bir kez kurulur (`haritaKur`), sonra yalnız `fill-opacity` güncellenir. |
| Toplam kanal dağılımı halkası | Şube payı oranı akıtılır, yaylar her karede `payYaylari()` ile yeniden üretilir. |
| Saha kartları | İmza = saha adları + dönem tipi. Aynı imzada kartlar yerinde kalır; olay dinleyicileri yalnız yeniden kurulunca bağlanır. |

`ciz(neden)` çizimi neyin tetiklediğini taşır: `donem`, `kanal`, `seviye`,
`filtre` akıtır; `arama`, `boyut` ve `ilk` akıtmaz. Saha kartı üstüne gelince
çalışan önizleme `ciz()` üzerinden geçmediği için `NEDEN`'i kendisi `filtre`
olarak ayarlar.

`requestAnimationFrame` arka plandaki sekmede durduğu için her akışın bir
emniyet zamanlayıcısı vardır: süre dolduğunda son değer her hâlükârda yerine
yazılır, ekranda eski rakam asılı kalmaz.

### Hero'nun üst bandı: etiket, rakam ve kapsam kutusu

**Puntolar ve aralıklar.** Gerçekleşen tutar, günlük
hedef ve çubuk tek bir öbek gibi okunsun diye aralar kısıldı, kaybolan
hiyerarşi punto ile geri verildi: `.big` 38 → **48 px**, `.sub` 17 →
**21 px**, aralar rakam–hedef **1 px**, hedef–çubuk **4 px**. Sağdaki ilk
KPI'nın etiketi "Hedefe göre" yerine **"H/G"** (alt satır zaten "günlük
hedefe göre" diye açıyor). Puntosu diğer üç kutuyla **aynı**; dört kutunun
başı olduğunu büyüklükle değil hareketle söylüyor — rakam yavaşça büyüyüp
küçülerek odak oluyor (`hgOdak`, 3,2 sn).

**Kapsam adı** ("Türkiye Geneli" / saha adı) soldaki gerçekleşen tutarla
**aynı puntoda** (48 px, `.big` ile birebir aynı clamp): ekranın "neye
bakıyoruz" cevabı o.

Bedeli: hero 317 → 329 px, sayfa ölçeği 0,876 → 0,864.



Kapsam kutusu ("Türkiye Geneli" / saha adı + teslimat) **akıştan çıkarıldı**
(`position:absolute`). Eskiden "Gerçekleşen" etiketiyle aynı satırdaydı ve
satırın boyunu o belirlediği için etiket ile büyük rakam arasında 45 px'lik
bir delik açılıyordu. Şimdi:

- sol sütunda akış sıkı: etiket → rakam (ölçülen ara **6 px**),
- kutu sağda, dikey konumu `--kapsam-y` ile veriliyor.

`--kapsam-y`'yi `kapsamHizala()` ölçüyor: kutunun tepesi sağdaki **üst KPI
satırının** tepesine oturuyor. Bu hizayı CSS ifade edemiyor — KPI ızgarası
kartın içinde ortalanıyor, üst satırın nereye düştüğü kartın boyuna bağlı.
Kutu mutlak konumlu olduğu için yazmak yerleşimi değiştirmiyor; ölçüm
`ekranaSigdir` içinde, pano son genişliğine geldikten sonra yapılıyor.

Hiza referansı **soldaki gerçekleşen rakamı** (`.big`): kapsam adı da onunla
aynı puntoda (48 px), ikisi aynı çizgide okunuyor. Bir süre sağdaki "H/G"
etiketine hizalanıyordu; küçük etiketle büyük başlığı eşitlemek gözle
dengesiz duruyordu. Ölçülen: üst ve alt kenar farkı 0 px — dört dönemde ve
saha önizlemesinde de.

Hizalanan **kutu değil metin**: `.k`'nın 2,7em'lik yer tutucusu var ve yazı
onun dibine yaslı (`align-items:flex-end`), o yüzden kutu tepelerini
eşitlemek gözle hizalı görünmüyordu. `kapsamHizala()` iki yazının kendi
kutusunu `Range` ile ölçüp farkı kapatıyor. KPI ızgarası dikeyde
ortalandı (üst/alt boşluk eşit: 1920×940'ta 29/29, 1280×720'de 45/45).

### Dönemler arası boy sabitliği

Tek-ekran ölçeklemesi geldikten sonra bu kural daha da kritik: panonun doğal
boyu bir dönemde uzarsa **bütün ekran** o dönemde küçülüyor. Ölçülen kaçak:
Hafta'da ciro çubuğu üçe çıkıyor (hafta · hafta içi · hafta sonu) ve
`.btablo` 58 px yerine 89 px oluyordu — hero 295 → 326, `.gdz` 881 → 913,
sayfa ölçeği **0,896 → 0,867**. Yani Hafta'ya basınca bütün pano %3
küçülüyordu.

Üç satırlık yer artık her dönemde ayrılıyor ve ayrılan yer tahmin değil
hesap: `calc(65px + clamp(18px,2.6vh,24px))` — 65 = iki etiket satırı
(2×18) + iki satır arası (2×5) + uç etiketi (19); son terim ana çubuğun
kendi clamp'i, yükseklikle birlikte değişiyor.

Ölçülen sonuç — dört dönem, üç ekran boyu, hepsi birebir aynı:

| ekran | ölçek | `.btablo` | hero | saha kartı (ekranda) |
|---|---|---|---|---|
| 1920×940 | 0,867 | 89 | 326 | 190 px |
| 1280×720 | 0,751 | 84 | 298 | 137 px |
| 1920×1080 | 0,969 | 89 | 330 | 223 px |

Bedeli: Gün/Ay/Yıl'da 31 px boş yer ayrıldığı için ölçek 0,896'dan
0,867'ye iniyor. Alternatifi yok — Hafta'nın üç çubuğu gerçekten o yeri
istiyor; boy sabitliği istendiği için herkes Hafta'nın boyunda.

### Gün seyrinin viewBox'ı (düzeltildi)

`cizSpark` — Gün görünümünün saatlik ciro grafiği — **viewBox'ı hiç
kurmuyordu**. HTML'de `0 0 1000 120` yazıyordu, `SEYIR.H` ise 200'e
çıkarılmıştı. İlk yüklemede grafik eski kutuyla çiziliyor, y=120'nin altında
kalan her şey kutunun dışında kalıyordu: taban çizgisi (y=170), saat
etiketleri (y=194) ve tutar etiketlerinin altı. Ekran görüntüsünde tam
olarak bu görünüyor — ciro grafiğinde saat etiketleri yok, soldaki iki tutar
yarıdan kesik; kayıt grafiğinde ikisi de yerinde (`cizKayit` kutusunu kendi
kuruyor).

Hafta'ya basınca `cizGrafikHafta` kutuyu 200'e çekiyor ve Gün'e dönünce
grafik "kendiliğinden düzeliyordu". **Açılış animasyonuyla ilgisi yoktu.**
`cizSpark` artık kutusunu kendi kuruyor; HTML'deki sabit değerler de 200'e
çekildi.

### Hero: etiket ile rakam arasındaki uçurum

Üstteki satırın boyunu sağdaki kapsam kutusu belirliyor ("Türkiye Geneli" +
teslimat, 63 px). "Gerçekleşen" etiketi o satırın tepesinde kalınca etiket
ile büyük rakam arasında **45 px** boşluk oluşuyordu — solda kocaman bir
delik, çünkü kapsam kutusu sağda.

Rakam eksi üst boşlukla yukarı çekildi (`.big{margin-top:-26px}`) ve
`.hero-l` ortalamadan yukarı yaslamaya geçti (ortalama kalsaydı kazanılan
yerin yarısı etiketi aşağı iterek geri alınırdı). Sağdaki KPI bloğu da aynı
şekilde yukarı yaslandı (`.pair{align-content:start}`).

Ölçülen: etiket–rakam arası **45 → 15 px**, hero 324 → **294 px**, dört
dönemde de aynı. Kapsam kutusu sağda (x≥759), rakam metni en geniş hâlinde
302 px'te bitiyor — çakışma yok, 1280×720'de de yok. Hero kısaldığı için
sayfa ölçeği **0,867 → 0,896**'ya çıktı: ekrandaki her şey biraz büyüdü.

### Sığdırma sırası: önce kartlar, sonra ölçek

`ekranaSigdir` panoyu 2200–2400 px genişliğinde kurup ölçekliyor;
`kartSigdir` ise kartları soldaki sütuna göre sığdırıyor. İkisi farklı
genişliklerde çalışıyordu: `kartSigdir` doğal genişlikte (1920), ölçekleme
ondan sonra panoyu açıyordu. Kart yığınının boyu soldaki sütuna bağlı, o da
genişlikle değiştiği için `--ko` bir çizim geride kalıyordu.

Sonuç ölçülebilir bir hataydı: **sığdırma anında kart yığını 987 px,
oturduktan sonra 911 px** — 76 px fazla. Pano o fazlalığa göre küçültülüp
orada donuyordu. Hafta'dan Gün'e dönünce:

| | ölçek | kart (ekranda) |
|---|---|---|
| ilk açılış | 0,867 | 190 px |
| Hafta → Gün (eski) | **0,804** | **176 px** |
| Hafta → Gün (yeni) | 0,867 | 190 px |

Düzeltme iki parça: (1) `kartSigdir` artık `ekranaSigdir`'in içinde, panonun
**son genişliğinde** çağrılıyor; ölçüm ondan sonra alınıyor. (2) Geçişler
bitince bir kez daha ölçen emniyet turu (`ekranaSigdir(true)`, 1 sn sonra,
kendini tekrar çağırmaz). Ölçülen: dönem, kırılım, kanal, hızlı tıklama ve
`resize` — hepsinde ölçek 0,867 ve kart 190 px'te sabit (1280×720'de 0,760
ve 137 px).

## Gösteri (play) modu kaldırıldı

Sunum için yapılmış gösteri modu artık kullanılmıyor ve kodda da yok. Silinen:
play tuşu, `GOSTERI` nesnesi ve zamanlayıcıları, `gosteriTur` / `gosteriBaslat`
/ `gosteriDurdur` / `gosteriAdetCevir` / `gosteriAnlik` / `gosteriGeriYaz` /
`gosteriCevir`, kart çevirme yardımcıları (`kartCevir`, `kartlariSabitle`,
`kartlariCoz`), kartların arka yüzü (markup + `.arka` / `.arkada` CSS) ve on
kadar yerdeki `if(GOSTERI.acik) return` koruması.

Kalanlar, çünkü bekleme modu kullanıyor: `adetler` / `adetleriYaz` (KPI
kutularının adet yüzü — `GOSTERI_KUTU` artık `KPI_KUTU`), `sahaVurgula`
(harita turu), `onizlemeCevir`. `body.hazir:not(.gosteri-acik)` biçimindeki
dört kural düz `body.hazir` oldu.

## Bekleme modu — panonun kendi kendine canlılığı

Ekran duvarda dururken çalışan iki değişim. **Gösteriden farkı: saha
kartları dönmez**, kartlar yerinde kalır.

1. **KPI kutuları üç yüz arasında döner** — KPI değerleri → organizasyon
   adetleri (saha · bölge · şube · personel) → **teslimat** → başa. Her yüz
   6,5 sn duruyor.

   **Teslimat yüzü** karta ortalı tek satır: iri rakam + "teslimat", Georgia
   ile. Rakam sakin koyu yeşil (`#26764F`), etiket sıcak altın kahve
   (`#8B7034`); yavaş ve çok hafif büyüme kutlama hissini korur.
   Dört kutudan bilerek ayrışıyor — teslimat 75 gün gecikmeli bir
   kohorttan geliyor, güncel ciroyla aynı dilde okunmamalı. Ciro kartının sağ
   üstündeki eski teslimat satırı (`.tesEt`) kaldırıldı.

   Eski hâli (yalnız adetler) — ciro kartının sağındaki dört kutu
   saha · bölge · şube · personel adetlerine çevrilir, 6,5 sn durur, sonra
   KPI değerlerine geri döner. Gösterinin `gosteriAdetCevir` /
   `gosteriAnlik` / `gosteriGeriYaz` altyapısı yeniden kullanılıyor.
2. **Seyrin sağ yarısı randevu sayısına döner** — "Kayıt sayısı" yarısı
   aynı biçimde (`kutuCevir`) dönüp "Randevu sayısı"nı gösterir, 9 sn
   durur, kayda geri döner. Renk de değişiyor (lacivert → turkuaz), iki
   ölçü bakışta ayrılsın diye. `kayitDilim` ve `cizKayit` artık alan
   parametresi alıyor (`kyt` / `rnd`), seri yapıları aynı.

**Elle çevirme.** Dönen iki yüzün sağ üstünde birer tuş var (`.yuz-tus`,
sönük durur, üstüne gelince belirir, gösteri açıkken gizlenir). Aynı
işlevleri çağırıyorlar (`kpiYuzCevir` / `sagYuzCevir`), yani elle ve
kendiliğinden dönüş tek yoldan geçiyor.

Yüz bir **durum** olarak tutuluyor (`S.kpiYuz`, `S.sagSeyir`), anlık görüntü
alıp geri yazarak değil: böylece yüz açıkken yapılan yeniden çizim onu
bozmuyor (ölçülen: adet yüzündeyken saha kartına gelince kutular o sahanın
adetlerine dönüyor — Bölge 6 · Şube 55 · Personel 715 · İl 1). Dönem,
kırılım veya kanal değişince yüz başa dönüyor.

**Tuzak:** dört kutunun sonuncusunu (randevu) `cizRandevu` yazıyor, bu
yüzden adet yüzü `cizCiro`'nun değil **`cizRandevu`'nun** sonunda
basılmalı; önce basılınca son kutu üstüne yazılıyordu.

3. **Bölge değerleri ciroya döner** — sıra bozulmadan yalnız rakamlar döner
   (`.btl` dönüşü, satır başına 16 ms gecikmeyle dalga gibi), sonra H/G'ye
   geri. Liste **her zaman H/G'ye göre sıralı**; ciro yüzünde de aynı sırada
   kalıyor ki göz aynı satırı takip etsin. H/G yüzünde çubuk yüzde yanında
   ve en fazla %100 dolu; ciro yüzünde çubuk yok, göreli ciro rengi var.
   Yıl'da hedef tanımlı olmadığı için H/G yüzü kendiliğinden ciroya düşüyor.

Sıra 20 sn'de bir sırayla biri, yani her etki **60 sn'de bir**. Aralar
bilerek uzun: ekrana sürekli bakılmıyor, ara ara göz atılıyor. Ölçülen tur:
21 sn adetler → 27 sn geri · 41 sn randevu → 50 sn geri, saha kartları hiç
dönmedi.

Gösteri açıkken, Genel dışındaki kırılımlarda, saha önizlemesi sırasında,
sekme arka plandayken ve hareket azaltma tercihinde hiç çalışmıyor.
`ciz()` başında `beklemeSifirla()` yarıda kalan geri yazmaları iptal edip
sağ yarıyı kayda döndürüyor.

## Bekleme hareketleri

Açılış bittikten sonra (`body.hazir`) ve gösteri kapalıyken çalışan sürekli
hareketler. Hepsi sekme arka plana geçince duruyor (`body.sekme-pasif`) ve
`prefers-reduced-motion` altında kapalı.

- **Kartların etrafında dolaşan ışın — ŞİMDİLİK KAPALI** (`ISIN_ACIK=false`;
  kullanıcı isteği, ileride geri açılacak. Yol kurma ve CSS olduğu gibi
  duruyor, tek satır yeter.) Panodaki her blok (ciro kartı ·
  dört saha kartı · harita · seyir) **kendi** ışınını taşıyor ve kendi
  kenarında, kendi içinde kesintisiz dolanıyor. Bloklar arasında hiçbir şey
  çizilmiyor ve hiçbir sıra yok. Ara aşamalar: önce dört kartı tek şeritle
  bağlayan bir zikzaktı (kartlar bitişikmiş gibi duruyordu), sonra tek ışık
  bloktan bloğa dolaşıyordu (yine bir bağ hissi veriyordu).

  Renk açık yeşil (`#D8F0A6`), kalınlık 2,8, çevresinde ışıma. Hız bütün
  bloklarda aynı (`ISIN_HIZ` 105 px/sn): süre çevreye göre hesaplanıyor, yani
  büyük blokta ışın hızlanmıyor — ölçülen: saha kartı 9,7 sn/tur, ciro kartı
  42 sn/tur. Uzunluk 190 px. Başlangıç fazları dağıtık, hepsi aynı köşede
  olmuyor.

  Kapalı yolda deseni sürekli kılan şart: kesik + boşluk toplamı yolun
  uzunluğuna eşit olmalı (`seg + bos = uz`); yoksa tur başında kısa bir
  kararma oluyor.

  Ölçüler `getBoundingClientRect` ile alınıp **ölçeğe bölünüyor** (pano
  `transform:scale()` taşıyor). İki tuzak: `:not(body.hazir) .yilan`
  yazılırsa `.gdz` de body olmadığı için kural her zaman tutuyor —
  olumsuzlama `body:not(.hazir)` olmalı; ve ışın yalnız Genel'de
  gösterilmeli, diğer kırılımlarda `.gdz` sıfır boya iniyor.

- **Gerçekleşen ciro rakamı** yanıp söner (`ciroNabiz`, 2,8 sn). Durum
  rengi korunsun diye opaklık değil `brightness` (1 → 1,85) oynatılıyor,
  yanına yumuşak bir `drop-shadow` ışıması eklendi — ekranın odak noktası
  belirgin olsun diye.
- **Harita** 9,5 sn'de bir soldan sağa parlar (`haritaParla`). Tarama
  **SVG'nin içinde**, viewBox'ı kaplayan bir dikdörtgen: CSS ile
  sarmalayıcıya konunca haritanın iki yanındaki boş sütunu da katediyor ve
  "tüm genişliğe gidiyor" gibi duruyordu. viewBox yolların gerçek sınırına
  çekilmiş olduğu için içerideki dikdörtgen tam haritanın eni kadar
  (ölçülen 592 px; sarmalayıcı 777 px). `pointer-events:none`, hover ve
  balon etkilenmiyor.
- **Parlama yalnız haritada.** Veri damgasındaki ışık halkası
  (`veriIsigi`) ve hedef çubuğundaki tarama (`hedefParla`) kaldırıldı;
  ekranda aynı anda birden çok parıltı olması dikkati dağıtıyordu.
  "Şu an" noktasının nabzı ve güncel kayıt sütununun nefesi duruyor —
  onlar parlama değil, veri işareti.

### Seyrin açılışı soldan sağa

Soldaki ciro seyri artık tek seferde belirmiyor, soldan sağa kuruluyor:

1. gerçekleşen çizgi çizilir (`.cizgiAk`, 0,85 sn),
2. noktalar, tutarlar ve saat etiketleri çizginin ucunu takip ederek sırayla
   belirir — `--sira` her öğeye çizim sırasında yazılıyor,
   gecikme `120ms + sıra × 62ms`,
3. 0,82 sn'de "şu an" noktası ile akan saat,
4. 0,92 sn'de **kesik tahmin çizgisi**, 1,42 sn'den itibaren onun noktaları.

Kesik çizgi eskiden baştan duruyordu; gelecek zaten yazılmış gibi görünüyor
ve soldan sağa okunan anlatıyı bozuyordu. Tur 1,84 sn'de bitiyor, 2,3 sn'lik
pencerenin içinde.

**Tuzak:** taban kuraldaki `:not(#suAnNokta)` seçiciyi iki kimlik ağırlığına
çıkarıyor. `.tahmin` kuralı aynı şekli taşımayınca (`body.acilis #spark
.tahmin`) sessizce geçersiz kalıyordu — tahmin noktaları hâlâ erken
beliriyordu. Her iki kural da `:not(#…)` taşımalı.

### Açılış sayacı ilk boyamadan sonra başlar

`body.acilis` sabit 2,3 sn sonra kaldırılıyor, ama **CSS animasyonları
elemanın kendisi yaratıldığı anda başlıyor**. Sayaç çizimin başında
kuruluyordu; ilk yükleme yavaş olduğunda (235 KB ayrıştırma + 81 il yolu +
`ekranaSigdir`'in yerleşim döngüsü) aradaki fark pencereyi yiyor ve en geç
başlayan animasyonlar — saha kartları 1,59 sn, huni dolguları 1,43 sn —
bitmeden sınıf kalkabiliyordu. Sayaç artık `ekranaSigdir`'den sonraki
rAF'ta kuruluyor: 2,3 sn her zaman animasyonların tamamını kapsıyor.

### Seyir başlıkları

Solda **Ciro**, sağda **Kayıt sayısı**; ikisinin de altında silik dönem
yazısı (`.s-don`, `seyirBaslik(per)`). Eskiden başlık dönemin kendisini
söylüyordu ("Eylül 2026 (9. ay)") ve yanında bir açıklama satırı duruyordu
("kesik çizgi: tahmini kapanış" gibi) — iki grafiğin üstünde dört ayrı
metin oluyor, hangi grafiğin ne olduğu başlıktan okunmuyordu. Açıklama
satırları kaldırıldı; `sparkSon` ve `kytSon` artık yok, `cizGrafik` ve
`cizKayit` Genel'de `not:null` ile çağrılıyor. Saha sayfasındaki bloklar
kendi `sdNot` alanlarını kullanmaya devam ediyor.

### Ciro grafiğinin ekseni

Taban çizgisi ve eksen etiketleri kayıt grafiğiyle **aynı ağırlıkta**:
çizgi `#AEB7B1` 1,2 px, etiketler `.s-eksen` (koyu, kalın, 15 px). Bir
süre ciro tarafı `#C9CFC9` 1 px ve `#58645F` normal etiketle kalmıştı;
kayıt grafiği yeni biçime geçince ikisi yan yana durunca ciro tarafının
ekseni **yokmuş gibi** görünüyordu. Gün görünümü (`cizSpark`) zaten yeni
biçimdeydi, eksik olan `eksen()` ve `cizGrafikHafta` idi.

### Ölçek sıçramasında akış kapanır

Rakam akışı "aynı ölçü, başka tutar" der. Dönem ya da kırılım değişince
bakılan büyüklük bir mertebe atlıyor ve arada interpole edilen değerler
hiçbir şeye karşılık gelmiyordu. Ölçülen: Yıl → Gün geçişinde tahmin kutusu
600 ms boyunca **%15.534 → %8.536 → %4.246 → %1.719 → %542 → %142 → %110**
diye geri sayıyordu — ekran görüntüsünde hatalı veri gibi duruyor.

`ciz()` her çizimde kapsamın gerçekleşenini bir öncekiyle karşılaştırıyor
(`olcekBak`); oran **6 katı** aşarsa o çizim boyunca akış kapanır ve bütün
KPI'lar aynı anda son değerine geçer. Karar kutu kutu değil çizim başına
verilir, yoksa bazıları akıp bazıları sıçrardı.

Ölçülen oranlar ve sonuç:

| geçiş | oran | davranış |
|---|---|---|
| saha önizlemesi (hover) | 3,3 | **akar** — `ciz()`'den geçmediği için ölçüye hiç girmiyor |
| Yıl ↔ Gün | 219 | sıçrar |
| Hafta ↔ Ay | 23,6 | sıçrar |
| Tüm Satış ↔ Alternatif kanal | 9,5 | sıçrar |

Yani en sık görülen geçiş — kart ya da harita üstünde gezinmek — akmaya
devam ediyor; sıçrayanlar dönem ve kapsam değişimleri. Bayrak tek
çizimliktir, `BOLUMLER` döngüsünden hemen sonra sıfırlanır.

**Not:** Hafta dilimi 28 Eylül – 4 Ekim, kesit ise 28 Eylül. Yani Hafta'da
yalnız bir gün geçmiş; Gün ile Hafta'nın gerçekleşeni birebir aynı, Ay'a
geçiş de bu yüzden 23 kat. Hata değil.

### Ciro çubuğu

Çubukta üç şey var, üçü de gerçek bir değer: **dolu kısım gerçekleşen**,
**altın çizgi hedef**, **turkuaz kesik çizgi tahmin**. İkisi de dikey çizgi;
tahmin kesikli, çünkü hedef kesin bir sayı, tahmin değil.

Ölçek hedeften türer: `enb = hedef × 100 / 75`. Böylece altın çizgi her
sahada ve her dönemde aynı yerde, **%75'te** durur; kartlar arası gezerken
yalnız dolgu ile tahmin çizgisi hareket eder. Alanın kalan %25'i tahminin
hedefi aşabilmesi için — tahmin hedefin %133'üne kadar çubuğa sığar, ötesi
`yuzde()` ile kırpılır. Bu veride en yüksek taşma %117.

Gelinen nokta üç denemenin sonucu:

| | ölçek / tahminin gösterimi | neden bırakıldı |
|---|---|---|
| ilk | `max(hedef, gerçekleşen, tahmin) × 1,12`, tahmin taralı bant | tahmin hedefi aşınca ölçek büyüyor, altın çizgi sola kayıyordu — saha değiştikçe oynayan şey hedefmiş gibi görünüyordu |
| ikinci | hedef %75'te sabit, tahmin taralı bant | hedef yerinde duruyordu; bant çubuğu kalabalıklaştırıyordu |
| denendi, geri alındı | gri kutu hedefte bitiyor, taşma sinyal | sağ kenar anlam kazanıyordu ama görsel karmaşıklaşıyordu |
| **şimdi** | hedef %75'te sabit, **tahmin de tek çizgi** | — |

Denenip elenen bir seçenek daha: alanı `max(ciro, hedef, tahmin)` yapmak.
Sağ kenar hep gerçek bir değere karşılık gelirdi ama hedef çizgisi Gün'de
sahadan sahaya %86–%96 arasında oynuyor, Hafta'da ise hep sağ kenara yapışıp
hedef işareti olmaktan çıkıyordu.

**Hafta'daki üç çubuk** ana satırın hedefinden türeyen **tek ölçeği**
paylaşır; her satırın altın çizgisi kendi hedefinde durur. Hafta içi ile
hafta sonu çizgileri toplandığında hafta çizgisini verir — dağılım doğrudan
okunur.

### Ciro grafiği alan oldu, sağ yarı çubuk kaldı

İki yarı karşılaştırıldı; durum şu:

| dönem | SOL (ciro) | SAĞ (randevu/kart) | sol mantık | sağ mantık |
|---|---|---|---|---|
| Gün | birikimli **alan + çizgi** + hedef + tahmin + "şu an" | **çubuk**, 10 saat | birikimli | birikimli |
| Hafta | **çubuk**, 7 gün, hafta içi/sonu öbek + hedef çizgisi | **çubuk**, 7 gün, aynı öbek | gün gün | gün gün |
| Ay | birikimli **alan + çizgi** + projeksiyon | **çubuk**, 5 haftalık dilim | birikimli | birikimli |
| Yıl | **çubuk**, 12 ay + geçen yıl | **çubuk**, 12 ay | ay ay | ay ay |

**Mantık dört dönemde de aynı** — iki yarı aynı dilimlemeyi kullanıyor, yani
asıl tekrar görsel değil mantıksal. Sol tarafın dönemden döneme tür
değiştirmesi (çizgi -> çubuk -> çizgi -> çubuk) gerekçeli: tür değişimi
mantık değişimine bağlı (birikimli / gün gün).

Çizginin "güzel gözükmemesi" **incelik sorunuydu**: 628 px genişlikte 2,5-3 px
tek bir çizgi boşluk bırakıyordu. Çözüm çizgiyi atmak değil **altını
doldurmak** oldu (`alanTanimi` + `alanYolu`, yumuşak gradyan, %28 -> %2).
Dolgu YALNIZ çizginin altına giriyor: hedef çizgisi, tahmin projeksiyonu,
noktalar ve etiketler aynen duruyor, hiçbir okuma kaybolmadı. Alan yolu
eksenden hemen sonra, çizgilerden ve noktalardan önce basılıyor (ölçülen
z-sırası 0), yoksa yarı saydam dolgu "şu an" noktasının üstüne biniyor.
Gradyan kimliği SVG başına (`<svg id>-alan`): aynı sayfada Genel'in grafiği
ve saha bloklarının grafikleri var, sabit bir id çakışırdı.

> **Yapılmayan, bilinçli:** sağ yarı Gün ve Ay'da **birikimli kaldı**.
> Birikimli çubuk zayıf bir seçim — her çubuk tanım gereği öncekinden
> büyük ya da eşit, grafik asla düşemez, ve çubuk yüksekliği "o saatte ne
> oldu"yu değil "o ana kadarki toplam"ı kodluyor. Dilim başına çevirmek
> önerildi (sol = birikimli yolculuk, sağ = dilim başına ritim) ama
> birikimli kalmasına karar verildi. Fikir değişirse dokunulacak tek yer
> `kayitDilim()`'in Gün ve Ay dallarındaki `birik` toplaması.

### Seyir ikiye bölündü, iki yarı da dönüyor

`.spark` iki yarıya ayrıldı ve **her yarı iki ölçü arasında dönüyor**:

| yarı | yüzler | okuma |
|---|---|---|
| **sol** | **Ciro → Kayıt sayısı → Hedefe göre** | sonuç: tutar, adet karşılığı ve hedef tutma |
| **sağ** | **Randevu ↔ Kart sayısı** | sonuca giden yol: huninin ilk iki kademesi |

Aynı dönemin iki farklı ölçüsü yan yana durunca "tutar mı arttı, adet mi"
sorusu doğrudan okunuyor; dönüşlerle de huninin tamamı (randevu → kart →
kayıt) tek satırda gezilebiliyor. Hangi yüzün açık olduğu `S.solSeyir` ve
`S.sagSeyir`'de durur, yeniden çizim onu bozmaz; tuşlar ile bekleme modu aynı
yolu (`solYuzCevir` / `sagYuzCevir`) kullanır.

Ölçü adları tek yerde: `SEYIR_AD` / `SEYIR_ETIKET` — başlık, `aria-label` ve
dönüş tuşunun yanındaki hedef etiketi hepsi oradan okunuyor. Çubuk renkleri
huni sırasını izler (`KAYIT_RENK`): randevu turkuaz `#2B8C8A`, kart ara ton
`#2F6B7A`, kayıt lacivert `#1E3856`.

### Sol yarının üçüncü yüzü: H/G

Yalnız yüzde; tutar yok (`cizHg`). **GÜN'DE YOK**: günün hedefi saatlik
profilden (`SAAT_W`) türetilmiş bir dağıtımdır, ölçülmüş bir şey değil —
saat saat H/G okumak tahmini bir böleni kesin bir orana çevirip olmayan bir
hassasiyet gösterirdi. Yüz listesi döneme göre `solYuzler()`'den çıkıyor;
Ay'da H/G açıkken Gün'e basılırsa yüz sessizce ciroya düşüyor.

| dönem | çizim | okuma |
|---|---|---|
| Hafta | gün gün çubuk (hafta içi / hafta sonu iki öbek) | her gün kendi hedefine göre |
| Ay | kümülatif çizgi + noktalar | dönem başından o haftaya kadar — ciro tarafıyla aynı okuma |
| Yıl | ay ay çubuk + kesik projeksiyon çizgisi | her ay kendi hedefine göre; kesik çizgi **son 3 TAM ayın ortalamasıyla** Aralık'a kadar devam eder |

### Sıfır tabanlı ölçek, sütunlar dipten yukarı

**Her dönemde sütun** (Ay'daki kümülatif çizgi de sütuna döndü). Kümülatif bir
seriyi sütunla göstermek genelde zayıftır ama H/G bunun istisnası: bir **oran**,
toplam değil — %100'ün iki yanında gezindiği için sütunlar iniş çıkış yapıyor.

**Bütün sütunlar dipten yukarı büyür**, %100 referans çizgisi de üzerlerinden
geçer. Sütunun boyu değerin kendisidir.

> **Denenip geri alınan: sapma (diverging) sütunu.** H/G bir oran olduğu için hep
> %100 civarında geziniyor ve sıfır tabanlı ölçekte sütunlar birbirine çok
> benziyor — ölçülen (Yıl): dokuz ay %98–%103 arasında, en kısa sütun 96,9 en
> uzun 101,7 birim, fark çizim alanının yalnız **%4,1'i**. Tabanı %100'e alıp
> sütunları yukarı/aşağı çıkarmak o farkı **%36,7'ye** çıkarıyordu. Buna rağmen
> sıfır tabanlı hâle dönüldü: sütun boyunun doğrudan **değerin kendisi** olması,
> karşılaştırma hassasiyetinden önce geliyor. Rakamlar zaten sütunların üstünde
> yazılı ve %100 çizgisi nerede durduklarını söylüyor. Hassasiyet gerekirse tek
> değişiklik: `Y()` tabanını yeniden %100'e almak.

**Renkler kurumsal ve sabit:** sütun yeşil (`#00724C`), **değer beyaz ve sütunun
İÇİNDE, dikeyde ortada** — yeşil zeminde beyaz rakam en okunur eşleşme ve
sütunların üstündeki şerit boşaldığı için rakamlar %100 çizgisiyle aynı hatta
düşmüyor. `.s-deger`'in beyaz konturu burada ters çalışırdı (beyaz yazıya beyaz
kontur), `.hg-ic` onu kapatıyor. Sütun rakamı taşıyamayacak kadar kısayken
(boy < 26 birim) eski davranışa düşülüyor: üstte, yeşil.

Tek çizgi var ve turuncu (`#B06A1F`, kanal payındaki "Saha" dilimiyle aynı):
kesik = ağırlıklı ortalama. Bir süre çubuklar `durum()` rengini alıyordu
(yeşil/sarı/kırmızı); kaldırıldı, çünkü o zaman turuncu çizgi sarı çubuklardan
ayırt edilemiyordu.

> **%100 hedef çizgisi KALDIRILDI.** Karşılaştırma artık sütun ile hedef
> arasında değil, sütun ile kendi **ağırlıklı ortalaması** arasında: "bu dilim,
> son üç dilimin eğilimine göre nerede?" Buna bağlı olarak ölçek artık %100'ü
> ZORLAMIYOR — ekranda o çizgi olmadığı için oraya yer ayırmanın karşılığı yok;
> tavan veriden çıkıyor ve sütunlar kareyi daha dolu kullanıyor.

**Sütunlar biraz kısa, çizgi az yukarıda:** H/G grafiği `SEYIR.B` yerine kendi
dibini kullanıyor (B=42, eksen adları da dibin 20 birim altında). Sonuç: sütunlar
kısalıyor ve %100 çizgisi kareye göre az yukarı çıkıyor.

### Ağırlıklı hareketli ortalama

Ortalama çizgisi **düz değil, sütunları takip ediyor**: her sütunun kendi
**üç dilimlik ağırlıklı hareketli ortalaması** var (`hgHareketli`). Ağırlıklar
**3-2-1**, en yenisi en ağır — son dilim eğilimi daha çok belirlesin. Üçten az
geçmiş varsa eldeki kadarıyla aynı oranlarla hesaplanıyor, yani çizgi ilk
sütundan itibaren var. Son noktada içi boş bir halka: eğilimin şu an nerede
olduğu. Lejant tek kalem ve solda: "3 dilimlik ağırlıklı ortalama · %100".

Ölçülen (Yıl, ortalamanın viewBox y değerleri): 66,6 · 68,8 · 70,0 · 70,8 ·
70,5 · 70,4 · 70,0 · 70,2 · 69,6 — dokuz noktanın **sekizi ayrı değerde**, yani
çizgi gerçekten her sütunda farklı. Düze yakın görünmesinin sebebi verinin
%98–%103'e sıkışık olması: 4,2 birimlik oynama ~106 birimlik çizim alanında
%4 ediyor.

> **Eskisi tek bir sayıydı** (Yıl'da son üç ayın düz ortalaması, ötekilerde
> gerçekleşen dilimlerin ortalaması) ve dümdüz yatay bir çizgi olarak
> çiziliyordu. Her sütunda aynı değeri gösterdiği için eğilim hakkında hiçbir
> şey söylemiyordu. Şimdi nerede yukarı nerede aşağı döndüğü okunuyor.

### Yerleşim: dört şerit

Şikayet "yazılar, rakamlar, çizgiler karışıyor"du. Çizim alanı ayrıştırıldı,
hiçbiri öbürüne girmiyor:

| y | ne |
|---|---|
| 13 | lejant: solda "%100 hedef", sağda ortalama — önülerinde düz/kesik çentik |
| 31 | hafta öbek başlıkları (yalnız Hafta'da) |
| 48+ | sütun değerleri — yukarı sütunda üstte, aşağı sütunda altta |
| 192 | eksen adları |

Çizgi etiketleri artık çizgilerin UCUNDA değil lejantta: Yıl'da "%99" ile
"%100 hedef" iç içe giriyordu. Böleni söyleyen not da SVG'den çıkıp başlık
altındaki dönem satırına taşındı; içeride "Eki/Kas/Ara" ile çakışıyordu.
Dipteki gri taban çizgisi kaldırıldı — sütunlar orada başlamıyor, eksen %100
çizgisinin kendisi.

**Ölçülen: üç dönemde de yazı çakışması 0** (önce Hafta 2, Ay 1, Yıl 4).

> **BUGÜN yarım, böleni de yarım olmalı.** Bugünün cirosu `GUN_PAY` ile
> (16:30'a kadarki pay) ölçeklenmiş; hedefinin de aynı payı alınmazsa günün
> TAMAMININ hedefine bölünüyor ve bugün her zaman düşük çıkıyor — ölçülen:
> Pazartesi **%80** görünüyordu, düzeltmeyle **%110**. `olcum()` aynı
> düzeltmeyi `hedefAgir` için zaten yapıyor.

> **Böleni söylemek zorunlu.** KPI kutusundaki "Hedefe göre" dönemin
> TAMAMININ hedefine bölünür; grafikteki her nokta KENDİ diliminin hedefine.
> Ölçülen: haftanın ilk gününde kutu **%16** derken grafik **%110** diyor —
> ikisi de doğru ama aynı etiket iki ayrı soruyu yanıtlıyor. O yüzden grafiğin
> sağ üstünde böleni söyleyen bir satır var: "her dilim kendi hedefine göre"
> (Ay'da "dönem başından birikimli"). Bu satır olmadan grafik yanlış okunuyor.

**Üç yüz bekleme modunu değiştirdi.** "Çevir, sonra bir daha çevir" artık başa
getirmiyor; `beklemeSeyir` nereye döneceğini açıkça saklıyor
(`solGeri` / `sagGeri`) ve `solSeyreGec` ile oraya dönüyor.

**İki yarı bağımsız döndüğü için zamanlayıcılar da ayrı** (`yariZaman[0]` /
`yariZaman[1]`): sol yarının dönüşü sağdakinin yarıda kalan geri yazmasını
iptal etmesin.

**Rakamlar bir kademe büyütüldü.** Punto SVG birimi, ekrana ölçekle iniyor:
ölçülen 1600×900'de seyir SVG'si 628 px'e sığıyor, ölçek 0,628 — yani eski
15 birim ekranda yalnız **9,4 px**, 16 birim 10,05 px çıkıyordu. Gövde yazısı
14 px, grafik başlığı 17,9 px olduğu için rakamlar ikisinin de altında kalıp
okunmuyordu. Yeni değerler: taban 15, kalın 16, eksen 16, değer **18** birim —
ölçülen sonuç: değer rakamları ekranda **9,4 → 11,3 px**, eksen 9,4 → 10,1 px.

> **Özgüllük tuzağı.** Değer yazıları `font-weight="700"` niteliği taşıyor ve
> `.spark svg text[font-weight="700"]` seçicisi (0,2,2) salt
> `.spark svg .s-deger`'den (0,2,1) daha güçlü. Sıra sonra olmasına rağmen
> yenemiyordu: `.s-deger`'e 18 yazılmışken rakamlar ölçümde 16 birimde
> kalıyordu. Seçici `text.s-deger` yapılınca (0,2,2) eşitleniyor ve sıra
> kazanıyor. Bu bloğun puntosuna dokunulacaksa ikisi birlikte okunmalı.

**Kayıt = gerçekleşen cironun adet karşılığı.** Ciro ÷ ortalama sözleşme
tutarından türer (`ORT_SOZLESME = 228.000`); sözleşme tutarı kişiden kişiye
(`sozKat`) ve günden güne değiştiği için iki seri birbirinin kopyası olmuyor.
Ölçülen büyüklükler: **446 kayıt/gün · 10.625/ay · 98.295/yıl** (2.500 kişi).

Kayıt grafiğinin hedefi ve tahmini yok — kayıt için hedef tanımlı değil — o
yüzden çizgi değil çubuk; ciro tarafındaki birikimli çizgiyle karışmasın.
`kayitDilim()` dilimler, `cizKayit()` çizer. **Her sütunun rakamı üstünde
yazar**; dilimleme bunu mümkün kılacak şekilde seçildi, en çok 12 sütun:

| dönem | sütun | okuma |
|---|---|---|
| Gün | 10 saat | birikimli — gün içinde nereye gelindi |
| Hafta | 7 gün | gün gün, **hafta içi / hafta sonu iki öbek** (aralarında ayrım çizgisi) |
| Ay | 5 haftalık dilim (1–7, 8–14 …) | birikimli |
| Yıl | 12 ay | ay ay |

Ay'da gün gün çizilirken 30 sütun oluyordu ve sütun başına ~31 px'e ~45 px'lik
rakam sığmıyordu; haftalık dilim bu yüzden seçildi.

**Dikkat: rastgele akışa dokunmayın.** Kayıt üretimi ilk denemede ana
`PERSONEL.forEach` döngüsünün içine konmuştu; oraya fazladan bir `rnd()`
çağrısı eklemek bütün diziyi kaydırdı ve ciro, randevu, hedef dahil daha önce
konuşulmuş her rakam değişti. Üretim `kayitUret()` adlı ayrı bir geçişe ve
ayrı tohumlu (`mulberry32(20260930)`) bir akışa alındı. **Yeni bir seri
eklenecekse aynısı yapılmalı.**

### Dönüş tuşları nereye döneceğini söylüyor

Dönen her blokta (`.yuz-sar`) tuşun **yanında** sıradaki yüzün adı yazıyor.
↻ işareti "bu kutu dönüyor" diyordu ama neye döneceğini söylemiyordu; hangi
ölçünün geleceği ancak dönünce anlaşılıyordu. Dört tuşun hepsi aynı
işlevden besleniyor (`yuzHedefYaz`), böylece bir yüz eklenince etiket
kendiliğinden doğru kalıyor:

| tuş | blok | etiket |
|---|---|---|
| `kpiTus` | KPI kutuları | sıradaki yüz — KPI değerleri → Organizasyon adetleri → Teslimat |
| `solTus` | seyrin sol yarısı | Kayıt sayısı ↔ Ciro |
| `sagTus` | seyrin sağ yarısı | Kart sayısı ↔ Randevu sayısı |
| `bolTus` | bölge listesi | Ciro ↔ Hedefe göre |

Konumlama sarmala taşındı (`.yuz-sar{position:absolute}`), tuş artık akışın
içinde (`position:static`) — etiket tuşun solunda, çünkü tuş sağ üst köşeye
yapışık. Gösteri açıkken ikisi birlikte gizleniyor.

### Ciro çubuğunun ucundaki yüzde

Ana çubuğun bittiği yerin üstünde küçük bir **gerçekleşen/hedef** yüzdesi
durur (`.bullet .ucEt b`). Konumu dolgunun ucuyla aynı yüzdeye bağlı, rengi de
durumla aynı; dolgu kaydıkça aynı geçişle birlikte kayar. Sol uca çok yakınken
`translateX(-50%)` etiketi çubuğun dışına taşıracağı için orada sola yaslanır.
Hafta'daki alt çubuklarda (hafta içi / hafta sonu) etiket yoktur.

### Kutu dönüşü yalnız gösteride

**Elle yapılan hiçbir değişiklikte KPI kutuları dönmez.** Bir süre saha
kartlarının üstüne gelince de dönüyorlardı; kartlar arasında gezinirken dört
kutunun sürekli dönmesi göz yorduğu için kaldırıldı. Orada değerler rakam
akışıyla yumuşak geçiyor, dönmeye gerek yok.

**Dönüş artık `sahaVurgula`'nın içinde değil.** Vurgulama — elle de,
gösteride de — kutulara yalnız o sahanın KPI **değerlerini** yazar ve
rakamlar akar. Dönüş gösterinin ayrı bir adımı oldu; böylece gösteri önce
sahanın KPI'larını gösterip sonra adetlere çevirebiliyor.

Dönüş iki yerde kalıyor, ikisi de gösterinin içinde:

- **Adetler evresi** — dört kutu 620 ms arayla sırayla döner.
- **Saha turu, b adımı** — `gosteriAdetCevir` ile dördü birden ve daha
  hızlı döner (0,44 s). Değerler kutu tam yan dönmüşken, 220 ms'de yazılır.

Dönen kutunun içindeki rakam ayrıca akmaz (`sayiAk` içinde
`el.closest(".cevir")` kontrolü): dönüş zaten değişimi anlatıyor, ikisi üst
üste binince kutu açıldığında rakam yarı yolda görünüyordu.

### Saha önizlemesi

Saha kartının ya da haritadaki bir ilin üstüne gelince üstteki ciro bloğu,
seyir ve kanal payı o sahaya döner; rakamlar akarak geçer, çubuklar kayar.

**Önizleme sızıntısı (düzeltildi).** `S.onizleme` yalnız fare kartın ya da
ilin üstündeyken yaşayan geçici bir durumdur, ama hiçbir yerde
sıfırlanmıyordu. Kart gizlendiğinde `mouseleave` her zaman gelmediği için
`kapsam()` diğer sayfaları da sessizce o sahaya filtreliyordu — Bölge sayfası
"Tüm satış · İstanbul" diye açılabiliyordu. `ciz()` artık `onizlemeSifirla()`
ile durumu ve bağlı bütün sınıfları temizliyor.

### Açılış — yalnız ilk yüklemede

`ciz("ilk")` gövdeye `acilis` sınıfını koyar, 2,3 saniye sonra kaldırır.
CSS tarafındaki açılış kuralları bu sınıfa bağlı olduğu için dönem
değiştikçe tekrar etmez.

- Tutar ve adet rakamları sıfırdan sayar (`SIFIRDAN` türleri). Yüzdeler
  saymaz — "%0 ▼" diye başlamak bir şey anlatmıyor.
- Ciro bloğu, seyir grafiği ve harita paneli sırayla aşağıdan belirir;
  saha kartları 70 ms arayla (`--sira`).
- Ciro çubuğu ile saha kartlarının çubukları sayan rakamla aynı tempoda
  (0,95 s) dolar; hedef işareti baştan yerinde durur, çubuk ona doğru
  ilerler, tahmin bandı dolgu bitince belirir.
- Seyir çizgisi soldan sağa çizilir (`cizgiCizdir`, `stroke-dashoffset`).
  Kesik tahmin çizgisine dokunulmaz; `stroke-dasharray`'ini ezmek desenini
  kalıcı olarak bozardı.
- Haritada iller batıdan doğuya koyulaşır; gecikme ilin `getBBox().x`
  değerinden çıkar. Başlangıç sıfırı geçiş kapalıyken yazılır, yoksa
  `getBBox()` biçemi zaten hesaplattığı için geçiş 1'den başlıyordu.
- Kanal payı halkası tepeden saat yönünde yayılır (`payYaylari`'nin `dolum`
  parametresi); yayılma bitmeden yüzde etiketleri yazılmaz.

### İki tuzak

Açılış animasyonlarında iki yerde aynı hata tekrar etti, ikisi de aynı
kökten: **tarayıcı bir düğümün başlangıç değerini hesaplamadan geçiş
başlatmaz.**

- `width:auto` ya da `left:auto`'dan yüzdeye geçiş interpole edilemez.
  Çubukların dolgusu bu yüzden iskelette açıkça `width:0` ile kurulur.
- Yeni kurulan düğümün ilk biçem hesabı geçiş başlatmaz. `bicemiSabitle()`
  bir yerleşim okuması zorlayarak sıfırı "başlangıç" yapar; ardından yazılan
  gerçek değer artık geçişle taşınır. Haritada aynı sorunun tersi vardı:
  `getBBox()` biçemi zaten hesaplattığı için geçiş 1'den başlıyordu, orada
  sıfır geçiş kapalıyken yazılıyor.

### Ölçümün akışa takılması

`kartSigdir()` sağdaki dikey kartların ölçeğini soldaki blokların boyuna
göre bulur; **kartların başı ve sonu soldaki blokların başı ve sonuyla
hizalı olmalıdır.** Ölçüm bir akışın ortasına denk gelirse kutular yarım
rakamla ölçülür ve ölçek yanlış donar — açılışta rakamlar sıfırdan saydığı
için bu her zaman oluyordu. Ölçmeden hemen önce `sonDegerleriYaz()` akan
her rakamı son değerine yazar, ölçüm bitince `metinleriGeriAl()` geri alır;
boyama o görevin sonunda yapıldığı için ekranda görünmez.

Ölçek yine bir kez ölçülüp sabitleniyor (dönemden döneme kart boyu
oynamasın diye), ama artık **yalnız aşağı çekilebiliyor**: soldaki sütun
Hafta ve Ay'da daha uzun olduğu için o dönemlerde kartların altı taşıyordu.
Taşma görüldüğünde ölçek yeniden ölçülüp küçültülür, bir daha büyütülmez —
yani en dar dönemin istediği ölçekte durulur. Kartlar kısa kaldığında
`align-content:stretch` boşluğu doldurduğu için baş ve son dört dönemde de
hizalı kalır (ölçülen sapma ≤ 1,5 px). Ekran boyu değişirse (`resize`)
ölçek sıfırlanıp yeniden aranır.

## Ekrana sığma

Genel pano başlık ve gezinme şeridinden kalan kullanılabilir alana otomatik
sığdırılır. Doğal pano genişliği en az 1180 px tutulur; ekran daha dar veya
kısa olduğunda genişlik ve yükseklik oranlarından küçük olanı seçilip bütün
pano tek parça olarak ölçeklenir. Böylece masaüstü kompozisyonu bozulmadan
ciro, seyir, harita, huni, kanal dağılımı ve dört saha kartı aynı karede kalır.

Alt açıklama ve `ÖRNEK ÇALIŞMA` bandı kaldırıldı. Sığdırma yalnız Genel
sayfasına uygulanır; uzun liste/tablo içeren diğer kırılımlar okunabilirlik
için normal kaydırmalı akışta kalır. Pencere boyutu veya dönem değişince
ölçek doğal boyutta yeniden ölçülür.

## Sürüm kontrolü

Klasör 30 Eylül 2026'da git deposuna çevrildi (`git init`, ilk commit dört
dosyayı da kapsıyor). 250 KB'lık tek dosyada çalışıldığı için geri dönüş
güvencesi gerekiyordu. Uzak depo yok; gerekirse
`https://github.com/sualpsudas` altına açılır.

Saha/bölge sabitlemesi ve yeni koşullu biçimlendirme öncesi temiz sürüm
`oncesi-etkilesim-2026-09-30` etiketiyle (`c412b19`) korunuyor.

## Yayın

| | adres |
|---|---|
| **Canlı pano** | https://sualpsudas.github.io/eminevim-yonetici-ozeti/ |
| depo | https://github.com/sualpsudas/eminevim-yonetici-ozeti (public) |

GitHub Pages, `master` dalının kökünden servis ediliyor. Üç yardımcı dosya var:

- `index.html` — kök adresi panoya yönlendirir, böylece paylaşılan link kısa
  kalır. Hem `meta refresh` hem `location.replace` var; biri engellenirse
  öbürü çalışıyor, ikisi de olmazsa elle tıklanacak bağlantı görünüyor.
- `robots.txt` + panodaki `<meta name="robots" content="noindex, nofollow">`
  — **linki bilen açar, arama motorları indekslemez.** Pano şirket markalı
  olduğu ve rakamlar örnek olduğu için böyle seçildi. Arama sonuçlarında
  çıkması istenirse ikisi de kaldırılır.
- `.nojekyll` — Pages dosyaları Jekyll'den geçirmeden olduğu gibi servis etsin.

> **Depo PUBLIC.** Ücretsiz planda GitHub Pages yalnız public depoda
> çalışıyor; "herkesin tıklayıp açabildiği link" istendiği için bilerek
> public yapıldı. Yani **hem pano hem kaynak kod URL'yi bilen herkese
> açık.** Gerçek veri bağlandığında bu kurulum yeniden düşünülmeli:
> o noktada depo private'a alınıp Pages için GitHub Pro'ya geçmek ya da
> şirket içi bir sunucuya taşımak gerekir.

**Gizli bir ikinci kopya** Claude Artifact olarak da duruyor
(claude.ai/artifact/3G8R8iK2bQ66ptbu2rzWmk): varsayılanı gizli, yalnız sahibi
ve erişim verdiği kişiler açabiliyor, görüntülemek için claude.ai oturumu
gerekiyor.

## Ortamlar

Masaüstü ve telefon için ayrı düzen vardır. 720 px altında grafikler daha kare bir
viewBox'a, personel tablosu 7 sütundan 3 sütuna geçer. Ayrı bir televizyon modu yoktur;
genel müdürlükteki büyük ekran masaüstü düzenini kullanır.

### Attract mode: etkileşim sayacı sıfırlar

Kiosk tasarımında yaygın desen: ekran boştayken ince, döngüsel hareketle
canlı olduğunu gösterir, biri dokununca hareket geri çekilir. Pano da böyle
davranıyor — **her elle çevirme ve her seçim bekleme sayacını baştan
başlatıyor** (`beklemeGecikti`). Kullanıcı panoyla uğraşırken kendi kendine
hiçbir şey oynamıyor.

Bunun için sayaç sabit bir `setInterval(beklemeTik, BEKLE_ARA)` olmaktan
çıktı: tik artık 1 sn'de bir çalışıyor ve etkiyi ancak
`Date.now() - beklemeSonHareket >= BEKLE_ARA` olduğunda başlatıyor. Eski
hâlinde sabit aralık, elle çevirmenin hemen ardından tetiklenebiliyordu.

`beklemeUygun()` şunlara da bakıyor: Genel kırılımı, önizleme yok, **sabit
seçim yok**, sekme önde, hareket azaltma kapalı, sürmekte olan etki yok.

### Sıra: 15 sn'de bir, dört etki

`BEKLE_ARA` 20 → **15 sn**. Dört etki sırayla, yani her biri 60 sn'de bir:

| # | etki | ne yapar |
|---|---|---|
| 1 | `beklemeKpi` | dört KPI kutusu adetlere, sonra teslimata döner |
| 2 | `beklemeSeyir` | seyrin iki yarısı SIRAYLA öteki ölçüsüne döner |
| 3 | `beklemeBolge` | bölge listesi H/G ↔ ciro, yukarıdan aşağı dalga |
| 4 | `beklemeHaritaTur` | dört saha sırayla vurgulanır (`BEKLE_SAHA` 3 sn) |

### Harita turunun koreografisi

Her saha için aynı sıra (gecikmeler `TUR_ADIM`'da, tek yerde):

| offset | olan |
|---|---|
| 0 | karta "gelinir": o sahanın illeri öne çıkar, kart vurgulanır, üst bloklar o sahaya önizleme yapar, rakamlar akarak geçer |
| 1.300 ms | KPI kutuları o sahanın **adetlerine** döner |
| 2.900 ms | sol seyir: ciro -> kayıt sayısı |
| 3.300 ms | sağ seyir: randevu -> kart sayısı (400 ms sonra — bir-iki) |
| 4.900 ms | bölge listesi: H/G -> ciro, yukarıdan aşağı dalga |
| 6.200 ms | hepsi birden başlangıç yüzüne döner |
| 7.400 ms | sonraki saha |

Dört saha x 7,4 sn = **~30 sn**; tur 60 sn'de bir geldiği için pano turda
değilken yarım dakika sakin kalıyor. **Kartlar dönmüyor** — tur bir okuma
turu, gösteri değil. Önizleme açıkken `beklemeUygun()` false döndüğü için
tur kendi kendini bölmüyor; sonunda `sahaVurgula(null)` ile Türkiye
geneline dönülüyor.

"Normale dön" adımı için **belirli yüze geç** işlevleri eklendi
(`kpiYuzeGec` / `solSeyreGec` / `sagSeyreGec` / `bolgeYuzeGec`); toggle'lar
artık bunlara devrediyor. Üç yüzlü KPI kutusunda "geri al" diye bir şey
yok — hangi yüzde olduğuna bakmadan `kpi`'ya dönebilmek gerekiyordu.

Ölçülen tur (İstanbul adımı, 120 ms örnekleme): +1,4 KPI / +3,3 sol /
+3,8 sağ / +5,0 bölge / +6,3 normale / +7,5 sonraki saha — dört saha birebir
aynı tekrarladı, 49,1. sn'de Türkiye Geneli.

> Duvar panosu kaynaklarındaki 15–30 sn / 30–60 sn tavsiyeleri **sayfa
> değiştirme** süreleri; buradaki ise tek sayfa içinde yüz dönüşü. O yüzden
> 15 sn bu ölçekte rahat — kritik olan, 15 sn'ye birden fazla etki
> sıkıştırmamak.

### Ortam hareketleri — sıraya girmeyen üç hareket

Üçü de **veriye dokunmuyor**; ekrandaki hiçbir sayı değişmiyor, yalnız
panonun canlı olduğu görünüyor. Sıraya girmemeleri bilinçli: sıradaki dört
etki "bak, başka bir ölçü" diyor, bunlar yalnız "buradayım" diyor. Hepsi aynı
1 sn'lik tikten besleniyor, ayrı `setInterval` açılmıyor (`ortamTik`).

| hareket | periyot | ne yapar |
|---|---|---|
| **il nabzı** | 3 sn | en çok ciro yapan 6 il sırayla kısa bir nabız atar (`filter:brightness`) |
| **huni akışı** | 14 sn | dolu çubukların içinden soldan sağa ışık geçer, üç kademe 180 ms arayla |
| **halka süpürmesi** | 22 sn | kanal halkası 0'dan kendi oranına yeniden süpürülür |
| **etiket parlaması** | 9 sn | seyir veri etiketleri soldan sağa teker teker parlar (95 ms aralıkla) |

Etiket parlaması `x` niteliğine göre sıralanıyor, DOM sırasına göre DEĞİL:
DOM sırası çizim sırasıdır ve her zaman soldan sağa olmuyor. Ölçülen yanma
sırası (viewBox x): 32 -> 136 -> 239 -> 343 -> 446 -> 550 -> 653 -> 757 ->
860 -> 964, yani tam zaman yönünde. `transform-box:fill-box` zorunlu —
SVG'de varsayılan referans kutusu viewBox'ın kendisi, o yüzden `scale`
etiketi kendi merkezinden değil grafiğin köşesinden büyütüp yerinden
fırlatıyor. Etiketler her çizimde yeniden üretildiği için zamanlayıcılar
ayrı tutulup yeniden çizimde iptal ediliyor (`etiketDurdur`).

İl nabzı önizleme açıkken (`.harita-sar.vurgu`) susuyor — o sırada haritanın
kendi vurgusu okunuyor, iki işaret birbirine karışır. İller şubelerin ili
üzerinden toplanıp önbelleğe alınıyor (`nabizIlleriBul`), her nabızda
yeniden hesaplanmıyor. Ölçülen: 25 sn'de 6 il sırayla (34, 35, 6, 16, 31, 48),
huni 1, halka 4 kez.

Halka süpürmesi yeni kod istemedi: `payYaylari`'nın açılışta kullanılan
`dolum` parametresi yeniden çağrılıyor. Yayları üretebilmek için gereken
toplam ve dönem adı `cizPay` sırasında elemanın üstünde saklanıyor
(`h.__toplam`, `h.__perAd`).

### Dönüş neden daha doğal

`kutuCevir` zaten 3D'ydi (`rotateX` + `perspective`). Üç şey onu
plastikleştiriyordu, üçü de düzeltildi:

1. **Simetrik tek eğri iki fazı birden yönetiyordu.** Artık yumuşatma
   **asimetrik**, kare başına `animation-timing-function` ile: çıkışta
   hızlanır (`cubic-bezier(.45,.05,1,.6)`), inişte yavaşlar
   (`cubic-bezier(0,.5,.3,1)`). Gerçek bir kart dönüşü böyledir.
2. **Tepede `opacity:.2` vardı.** Panel 88°'de %20 opaklığa inip dönmek
   yerine "yanıp sönmüş" gibi görünüyordu. Kaldırıldı — tam **90°**'de
   kenarına geldiği için zaten görünmez oluyor. Onun yerine yan dönmüşken
   hafif koyulaşıyor (`brightness(.88)`), ışık açısı değişmiş gibi.
3. **İçerik takası elle yazılmış sayılarla zamanlanıyordu** (220 / 230 /
   300 ms), her blok biraz farklı kayıyordu. Artık süreler tek yerde
   (`CEVIR_SURE`) ve takas **tam sürenin yarısında**, yani kutu tam
   kenarına geldiği anda.

> **Teslimat yüzü dönmüyordu, PAT DİYE geliyordu.** Dönüş animasyonu
> `.pair > div` kutularının üzerinde, teslimat ise kardeş bir katman
> (`.tes-yuz`) ve `.hero-r.tes .pair{visibility:hidden}`. Kutular dönüşün
> orta noktasında görünmez olunca kalan 220 ms boşa oynuyor, yazı hiç
> dönmeden beliriyordu. Çözüm: katman dönüşün İKİNCİ yarısını kendisi
> oynuyor (`yuzGir`, `--cg` = süre/2) — tam kenarından açılıyor. Çıkışta
> düzeltme gerekmedi: orta noktada kutular geri gelip dönüşün ikinci
> yarısını zaten oynuyorlar. `.giris` oynarken `tesNabiz` bastırılıyor,
> ikisi aynı `transform`ı yazıyor.

**Yüz başına bekleme 6.500 -> 5.000 ms** (`BEKLE_ADET`): üç yüz 19,5 sn
sürüyordu, yani 15 sn'lik bekleme sayacını aşıyordu. Şimdi tam 15 sn.

### Bölge listesi: gerçek iki yüzlü dalga

Bölge listesi artık yukarıdan aşağı **dalga** hâlinde dönüyor, satır başına
48 ms (`BOLGE_DALGA`). Eskiden 16 ms'ydi ve gözle fark edilmiyordu — hepsi
birlikte dönmüş gibi duruyordu.

Dalganın mümkün olmasının sebebi: **satırın iki yüzü de zaten DOM'da.**
`cizBolgeListe` her çizimde hem H/G çubuğunu hem ciro değerini yazıyor,
hangisinin görüneceğine satırdaki `.hg-yuz` sınıfı karar veriyor. O yüzden
tek bir toptan yeniden çizim yerine her satırın sınıfını kendi yarı
noktasında çevirmek yetiyor — kitapta yazan iki yüzlü (`backface-visibility`)
dönüşün bu bloktaki karşılığı. Sonda bir kez `cizBolgeListe` çağrılıp durum
normalleştiriliyor.

Ölçülen dalga: dönen satır 2 → 7 → 12 → 17 → 22, yüzü değişen satır
22 → 16 → 11 → 6 → 0; satır sayısı dönüş öncesi ve sonrası aynı kalıyor.

> **KPI kutuları ve seyir yarıları hâlâ tek elemanlı.** KPI'da üç yüz var
> (değer / adet / teslimat), iki yüzlü bir kart üçü taşıyamaz. Seyirde ise
> iki yüz aynı `<svg>`'ye çizilen iki ayrı render; gerçek iki yüzlü yapı
> için yarı başına ikinci bir `<svg>` gerekir. İkisinde de yukarıdaki üç
> düzeltme (asimetrik eğri, opaklık yok, tam yarıda takas) uygulandı; daha
> da ileri gitmek istenirse seyir için ikinci SVG ayrı bir iş.

## Sıradaki adımlar

**Kaldığımız yer (30 Eylül 2026).** Genel sayfası 29 Eylül'de "tam istenen
gibi" onayını almıştı; o günden beri üstüne iki tur iş yapıldı.

**29 Eylül — hareket katmanı** (bkz. "Hareket katmanı"): önce veri değişimi
geçişleri, sonra açılış animasyonları, gösteri (play) tuşu, çubuk ucundaki
yüzde ve hedefe çivilenmiş çubuk ölçeği.

**30 Eylül — ölçüler ve düzen:**

- Haritanın koyuluk ölçeği **logaritmik** oldu; altındaki açıklama satırı
  kaldırıldı, harita büyütüldü (271 px boy, ~518 px çizilen genişlik).
- **Sayfa genişliğindeki 1440 px tavan kalktı** (`--sayfa-en`), ekrana yayıldı.
- **Seyir ikiye bölündü:** solda ciro, sağda kayıt sayısı. Kayıt grafiği
  Gün ve Ay'da birikimli, Hafta'da hafta içi/hafta sonu iki öbek, her sütunun
  rakamı üstünde.
- **Üç yeni ölçü:** kayıt · kart · teslimat. Harita satırı üç sütuna çıktı
  (harita · **dönüşüm oranı** · kanal payı); teslimat ciro kartının sağ üstüne
  girdi.
- Çubukta tahmin artık kesik çizgi; ciro ile KPI bloğu arasına kozmetik
  ayırıcı kondu.
- **Gösteri dört aşamaya çıktı:** adetler → KPI'lara dönüş → saha turu (KPI
  kutuları o sahanın adetlerine döner) → saha kartları arka yüzlerine dönüp
  bölgeleri listeler. Bir tur ~21 sn.
- Kart içi KPI puntosu büyütüldü.
- Elle yapılan değişikliklerde **kutu dönüşü kapatıldı** (göz yoruyordu);
  dönüş yalnız gösteride.

**30 Eylül, ikinci tur — gösteri sırası · ay dilimi · harita:**

- **Gösterinin sırası kart odaklı oldu.** Eski akış: adetler → KPI'lara dönüş
  → dört kartın turu → dördü birden arka yüze. Yeni akış: adetler →
  KPI'lara dönüş → **her kart için sırayla** vurgu + KPI dönüşü → o kartın
  arka yüzü → önüne dönüş → sonraki kart → başa sar. Bir sahanın adetleri ile
  bölgeleri artık aynı kartta arka arkaya okunuyor. Bir tur ~26 sn.
  (`kartlariCevir` → `kartCevir`, tek kart.)
- **Ay seyri gün gün değil hafta hafta.** 30 nokta yerine 5 haftalık öbek
  (`1–7`, `8–14`, …); ciro ile kayıt seyri artık aynı x eksenini okuyor.
- **Harita büyütüldü ve kart içinde ortalandı**: 271 → 287 px. Gereken yer
  `.hsag` dikey iç boşluğundan alındığı için satır boyu ve katlama
  değişmedi. Genişlik tavanı 700 → 900 px, sarmalayıcı flex ile ortalıyor.

**30 Eylül, üçüncü tur — gösteri sırası (2) · yerleşim · punto:**

- **Gösteride her saha üç adımda okunuyor:** sahaya gelinir ve **sahanın
  kendi KPI'ları** görünür (kutu dönmez, rakamlar akar) → kutular dönüp
  **adetleri** gösterir → **kart arka yüzüne dönüp bölgeleri** listeler.
  Kart ön yüzüne dönmeden sıra öbür sahaya geçer; dönenler birikir, turun
  sonunda hepsi birden öne çevrilir. Bir tur ~31 sn.
- **Kart boyu dönüşte sabitlendi** (`kartlariSabitle` / `kartlariCoz`);
  eskiden arka yüz kısa olduğu için dört kart birden oynuyordu.
- **Kutu dönüşü `sahaVurgula`'dan çıkarıldı**, gösterinin ayrı adımı oldu
  (`gosteriAdetCevir`); `GOSTERI.turda` bayrağı kalktı.
- **Harita satırının içeriği aşağı alındı:** kart iç boşluğu geri açıldı ve
  iki yan panelin altındaki ölü boşluk, içeriği dikeyde dağıtarak kapatıldı.
- **Harita 287 → 303 px.**
- **Ciro yanındaki KPI puntosu** bir kademe daha büyüdü; ciro bloğunun boyu
  değişmedi.
- **Bedel:** `.gdz` altı 933 → 967 px (Hafta 978). Bkz. "Ekrana sığma".

**30 Eylül, dördüncü tur — Codex değerlendirmesinden alınan üç madde:**

- **Ölçek sıçramasında rakam akışı kapanıyor** (gerçek hataydı): Yıl → Gün
  geçişinde tahmin kutusu %15.534'ten %110'a geri sayıyordu. Bkz. "Ölçek
  sıçramasında akış kapanır".
- **Klasör git deposu oldu** (`git init` + ilk commit).
- **"Kanal payı" → "Toplam kanal dağılımı"**: bloğun üstteki kanal
  filtresini izlemediği dipnotta yazıyordu, başlığa taşındı. Saha
  sayfasındaki aynı blok da tutarlılık için yeniden adlandırıldı (yalnız
  başlık; blok içeriğine dokunulmadı).

**Değerlendirmeden alınmayanlar ve sebebi:** "%80 ▲"nin ikiye bölünmesi
(yüzde = hedefe ilerleme, renk ve ok = beklenen seyir; bilinçli karar —
alternatif olarak kutunun alt satırına seyir farkını yazmak önerildi,
karar bekliyor) · "dekoratif dönüşleri azaltın" (ekran genel müdürlükte
duvarda dönecek, hareket kasıtlı) · "gösteride yarı eski–yarı yeni durum
olmasın" (kutuların sahanın adetlerini, üstün cirosunu göstermesi
anlatının adımı) · "tek dosya bölünsün" (dosyanın işi taşınabilir sunum
aracı olmak). Bekleyenler: saat + veri yaşı, gösteri paketi
(`prefers-reduced-motion`, durdurma bildirimi, sahne göstergesi), otomatik
5×4 test betiği, veri talebi listesi, ekrana sığma ve dokunmatik paketi.

**Kapsam Genel sayfasıdır.** Saha, Bölge, Şube ve Personel sayfaları hâlâ her
çizimde baştan üretiliyor — geçiş katmanı oralara taşınmadı. Saha sayfasındaki
blok içeriği hâlâ ara durumda ve kullanıcının farklı fikirleri var; sormadan
üzerine ekleme yapılmamalı.

**Son doğrulama (30 Eylül, dördüncü tur):** iki kanal × beş sayfa × dört
dönem taraması temiz (40 görünüm; hata, boş bölüm, `NaN`/`undefined`, boş
SVG yok). Ölçek sıçraması ölçüldü: Yıl↔Gün, Hafta↔Ay ve kanal değişimi
sıçrıyor, saha önizlemesi akmaya devam ediyor. Gösteri etkilenmedi.

Bir önceki tur:

**Son doğrulama (30 Eylül, üçüncü tur):** beş sayfa × dört dönem taraması
temiz; gösteri baştan sona izlendi (sıra doğru, kart boyu 201 px'te sabit,
dönenler birikiyor, sonda hepsi öne dönüyor, kapsam genele çıkıyor); gösteri
ortasında dönem değiştirilerek kesinti yolu denendi — gösteri durdu, kart
boyları çözüldü, `arkada`/`cevir` artığı kalmadı. Saha kartlarının başı ve
sonu soldaki blokların başı ve sonuyla tam hizalı (sapma 0 px).

Bir önceki tur:

**Son doğrulama (30 Eylül, ikinci tur):** beş sayfa × dört dönem taraması
temiz (hata, boş bölüm, `NaN`/`undefined`, boş SVG yok). Gösteri Gün ve
Yıl'da baştan sona izlendi: sıra doğru, tur sonunda kutular KPI değerlerine
döndü, Yıl'da gizli "Hedefe kalan" yeniden gizlendi, `.cevir` / `.arkada`
artığı kalmadı. Ölçülen `.gdz` altı: Gün/Ay/Yıl 933 px, Hafta 944 px
(değişiklikten önce 932 / 943). Saha, Bölge, Şube ve Personel
sayfalarının metin çıktıları bütün bu turlar boyunca **birebir değişmedi**
(fark 0) — değişen yalnız Genel. Saha kartlarının başı ve sonu dört dönemde de
soldaki blokların başı ve sonuyla **tam hizalı** (sapma 0 px); kartlar Gün, Ay
ve Yıl'da katlamanın 8 px içinde, Hafta'da 3 px taşıyor.

**Varsayılan sabitler (gerçek oranlar gelince değişecek, hepsi dosyanın
başında):** `ORT_SOZLESME = 228.000` (ciro ÷ bu = kayıt), `KART_KAT = 3,3`
(kart = kayıt × bu, randevuyla tavanlı), `TES_GECIKME = 75` gün.
Ölçülen büyüklükler: 446 kayıt · 1.434 kart · 6.613 randevu · 299 teslimat
(günlük, 2.500 kişi).

**Saha sayfasında yarım kalan iş:** karşılaştırmalı seyir grafiği kaldırıldı ve
bloklar Genel'in minyatürü olacak şekilde zenginleştirildi (ciro + ilerleme
çubuğu + tahmini kapanış + 4 KPI + dönem seyri + kanal payı halkası).
**Kullanıcının bu blokların içeriği için farklı fikirleri var, sonra ele
alınacak** — mevcut hâl ara durumdur, üzerine ekleme yapmadan önce sorulmalı.

1. **Saha sayfası** — kullanıcının fikirleri alınıp blok içeriği yeniden
   kurgulanacak. Saha haritası da buraya planlanıyor (her blokta o sahanın illeri).
2. **Bölge sayfası** — KPI şeridi (`.ozet`) Genel'deki `.pair` gibi eşit yayılsın,
   ortalansın, punto `clamp()` ile büyüsün.
3. **Şube ve Personel** — "Öne çıkanlar" kutuları için aynısı.
4. **Haritanın sütunu hâlâ geniş.** Satır üç sütuna çıktı (harita · dönüşüm
   oranı · kanal payı) ve harita satırdaki bütün boş payı aldı (254 px boy,
   çizilen genişlik ~485 px). Sütun ise 995 px: harita yatayda hâlâ yerini
   dolduramıyor, çünkü boyu satırın boyuna bağlı. Daha büyük bir harita
   isteniyorsa satırın kendisi uzamalı, o da katlamayı taşırır.
5. **Hareket katmanının diğer sayfalara taşınması** — Saha, Bölge, Şube ve
   Personel bloklarında da düğüm kalıcılığı kurulacak. Ayrıca henüz
   yapılmayan fikirler: dönem adımlarken yönlü kayma, kırılım düğmelerinde
   kayan pill, canlı veri bağlanınca yeni satış parlaması.
6. **Sunum modu** — tam ekran, otomatik geçiş, büyük punto.
7. **Veri talebi dokümanı** — ekrandaki her bloğun arkasında hangi tablo, hangi
   alan, hangi yenilenme sıklığı gerektiğini çıkaran liste. Birimlere bu gidecek.
8. **Gerçek bölge ve şube listesi** alınınca `BOLGE_TANIM` güncellenecek
   (şube adlarının yanındaki plaka kodlarıyla birlikte).
9. Ciro tanımının kilitlenmesi: iptal/iade kapsamı ve tarih alanı (sözleşme mi,
   onay mı).

### Çalışma kuralı (kullanıcının uyarısı)

Bir sayfa düzenlenirken başka sayfaların bozulmaması gerekiyor. Bu oturumda üç
kez oldu:

- `display:flex` verilen kaplarda `hidden` özniteliği ezildi (iki kez) →
  gizli bölümler görünür kaldı. Bir elemana `display` verilirken
  `[hidden]{display:none}` kuralı da birlikte yazılmalı.
- CSS'te iki yorum satırı arası blok komple değiştirildi; o aralıkta başka
  sayfaya ait kurallar vardı → Genel'in kart düzeni silindi.

**Kural:** CSS'e hedefli dokunulacak (aralık silme yok) ve her değişiklikten
sonra beş sayfa × dört dönem taraması çalıştırılacak (hata, boş bölüm,
`NaN`/`undefined` kontrolü).

### Bu oturumda yapılanlar

- **Türkiye haritası** eklendi (81 il, MIT lisanslı sınır verisi, 54 KB),
  tek yeşil tonlamalı, hover ile saha vurgulama ve **hover ile filtreleme**.
- **Kanal payı halkası** — şube / saha (SPY) kırılımı, kanal filtresinden bağımsız.
- **Başlık ve gezinme şeridi sabitlendi**, satırlar daraltıldı; dönem adı
  gezinme şeridinin ortasına alındı.
- **Kurumsal renk paletine** geçildi (bkz. "Renk paleti").
- Ciro kartının kapsam etiketi ("Türkiye Geneli" / saha adı) kartın sağ üstünde,
  büyük puntoyla.
- Gün grafiğinde **yanıp sönen kırmızı nokta + akan saat** (KESIT'ten başlar,
  gerçek zamanla ilerler; yalnız göstergedir, rakamları etkilemez).
- Hafta seyri gün gün çubuklara döndü, hafta içi / hafta sonu iki öbek;
  ciro kartındaki çubuk Hafta'da üçe çıkıyor (hafta · hafta içi · hafta sonu).
- Rapor **Gün** dönemiyle açılıyor.
- Kaldırılanlar: karar cümlesi, "Ciro" ve harita başlıkları, sayfa üstü dönem
  başlığı, "Hedef · Gerçekleşen · Tahmini kapanış" tablosu (özete uygun
  bulunmadı), Saha'daki karşılaştırmalı seyir grafiği.

## Bilinen açık konular

- **Saha sayfasındaki ilerleme çubukları görünmüyor.** `.sdet-bar .track` gri
  zemini çiziyor ama içindeki `.fill` / `.fcast` / `.mark` için hiçbir kural
  yok: bu sınıflar yalnız `.bullet` altında tanımlı, `.sdet-bar` ise `.bullet`
  içinde değil. Ölçüldü: dolgu `position:static`, yüksekliği 0 px. Yani Saha
  sayfasında çubukların yalnız boş zemini görünüyor. Eski bir hata, hareket
  katmanıyla ilgisi yok. Saha sayfası zaten yeniden kurgulanacak, düzeltme
  oraya bırakıldı.

- **Şube başına ciro haritası** ayrı bir seçenek olarak duruyor. Mevcut harita
  "hacim nerede" sorusunu cevaplıyor; çarpıklığın asıl sebebi şube sayısı
  (İstanbul ~57, çoğu ilde 1). Şube başına normalize edilmiş bir harita
  "nerede verim var" sorusunu cevaplardı — farklı bir harita olur, mevcudun
  yerine değil yanına düşünülmeli.

- Ciro büyüklükleri temsilidir; gerçek mertebe `OLCEK` ile ayarlanacak.
- Hedef toplamları kırılımlar arasında farklıdır (saha toplamı ≠ şube toplamı).
  Bu bilinçlidir — her kırılımın kendi hedefi vardır.
- Versiyon kontrolünde: https://github.com/sualpsudas/eminevim-yonetici-ozeti
- Saha ve bölge önizlemesi tek tıklamayla sabitlenir, ikinci tıkla ayrıntıya
  inilir; bu iki aşamalı hareket gerçek dokunmatik ekranda ayrıca
  doğrulanmalıdır.
- Genel sayfası kullanılabilir ekran yüksekliği ve genişliğine otomatik,
  orantılı olarak sığdırılıyor. Dar görünümde de masaüstü kompozisyonu
  korunuyor; pano en az 1180 px doğal genişlikte kurulup bütün olarak
  küçültülüyor. Uzun liste içeren Saha/Bölge/Şube/Personel sayfaları
  okunabilirlik için bu kurala dahil değil ve normal kaydırmalı kalıyor.
- Telefon düzeni artık hedeflenmiyor — masaüstü ve büyük ekran önceliklidir.
  Dosyadaki dar ekran kuralları duruyor ama bakımı yapılmıyor.
