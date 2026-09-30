# Alternatif Satış Kanalı — Yönetici Özet Raporu — Proje Notları

*Bu dosya, HTML mockup'ı (Codex ile devam edilen "Cep Performans Defteri V3") üretmeden önce Claude ile yapılan metodoloji ve mimari konuşmalarının özetidir.*

## Genel Bakış

Eminevim'de yeni bir alternatif satış kanalı stratejisi kapsamında, kurumsal bir raporlama yapısı kuruluyor. İlk somut çıktı: **Alternatif Satış Kanalı** için yöneticilere yönelik, ay başından bugüne (MTD) durumu gösteren bir genel özet raporu.

- **Hedef kitle:** Üst yönetim — detay değil, tek bakışta durum özeti isteniyor.
- **Kapsam:** Yalnızca Alternatif Satış Kanalı (mevcut bölge/şube ana raporlamasından ayrı bir katman). Kanal zaten aktif ve raporlanıyor; bu proje mevcut raporun üstüne sağlam bir yönetici özeti inşa etmeyi hedefliyor.

## Mevcut Durum (rapor öncesi)

- **Veri akışı:** CRM export → manuel Excel taşıma → VBA rapor.
- **Rapor sıklığı:** Günlük ve aylık kesin üretiliyor; uzun dönem görünümler talep üzerine.
- **En büyük sorun:** Manuel CRM→Excel taşıma adımı — veri gecikmesi ve hata riski. Diğer adımlar (VBA rapor, üretim sıklığı) zaten oturmuş; bu proje esas olarak bu manuel adımın üstüne, otomatikleştirilmiş ve yönetici odaklı bir özet katmanı ekliyor.
- **Format kararı:** Statik Excel raporu değil — **canlı veri bağlantılı, Power BI benzeri bir dinamik dashboard çözümü**. Bu hem periyot esnekliğini (günlük/haftalık/aylık) hem de manuel CRM→Excel adımının doğrudan bağlantılarla ortadan kalkmasını sağlıyor.

## Yönetici Özet Raporu İçeriği

- **Kıyaslama boyutları:** H/G (Hedef/Gerçekleşme) + MoM (geçen aya göre) + YoY (geçen yılın aynı dönemine göre) — bir arada gösteriliyor.
- **Format:** Tek tabloda/ekranda hacim + hedef durumu + verimlilik bir arada ("hepsi bir arada" tercih edildi, ayrı ayrı özetler değil).
- **Zaman kırılımı:** Günlük / Haftalık / Aylık — yönetici hem "bugün nasılız" hem "ay/hafta sonunda nereye varacağız" sorularını tek ekranda cevaplayabilmeli.

## MTD Hedef Hesaplama Metodolojisi

Amaç: aylık hedefi mevsimsellik etkilerini yansıtacak şekilde günlere dağıtmak; günlük/haftalık/aylık MTD hedefler gerçek satış paternine göre anlamlı olsun.

**Temel formül:**
- Günlük Hedef(d) = Aylık Hedef × [ w(d) ÷ Σw(ayın tüm günleri) ]
- Haftalık Hedef = o haftaya denk gelen günlerin toplam hedefi (ay başında bir kez hesaplanır, ay boyunca sabit kalır)
- MTD Hedef = bugüne kadarki günlerin kümülatif toplam hedefi
- MTD Gerçekleşme = ay başından bugüne kümülatif gerçekleşen (CRM'den)
- MTD H/G% = MTD Gerçekleşme ÷ MTD Hedef

**Gün ağırlığı w(d) — iki bileşenin bileşimi:**
1. Haftanın günü faktörü (DOW) — Pazartesi/Salı/…/Pazar için normalize edilmiş ortalama pay
2. Ay içi konum faktörü — ayın kaçıncı günü olduğuna göre (ay başı/ortası/sonu eğilimi) normalize edilmiş pay

İki faktör çarpılıp normalize edilerek günün nihai ağırlığı elde edilir.

**Veri penceresi ve güncellik (recency):** Elimizdeki tüm geçmiş CRM verisi kullanılacak, ancak yakın geçmiş daha fazla ağırlık taşımalı. Önerilen yaklaşım: üstel azalan ağırlık (recency decay) — her bir ay geriye gittikçe belirli bir katsayıyla (örn. %90, kesinleşmedi) azalan bir ağırlıkla katkı verir.

**Özel günler (tatil/bayram/kampanya):** Önceden tanımlı bir özel gün takvimi tutulacak; bu günler için otomatik hesaplanan ağırlık yerine manuel girilen bir ağırlık kullanılacak (override mantığı).

**Sabitlik kuralı:** Ağırlıklar ve türeyen günlük/haftalık hedefler ay başında bir kez hesaplanır, o ay boyunca değiştirilmez.

## Hedef Seviyeleri

Aylık hedef bazen tek parça (kanal geneli, 1 toplam hedef), bazen 3 parça (alt kanal/segment veya dönem bazında) olarak tanımlanıyor. Hesaplama mantığı bu seviyeden bağımsız tasarlanmalı — hedef hangi seviyede tanımlıysa formül o seviyede uygulanır, gerekirse üst seviyede toplanır.

## Gün İçi Dinamik Projeksiyon ("Potansiyel Kapanış")

Amaç: gün içinde, o ana kadar gelen kayıtlardan sürekli güncellenen bir "potansiyel kapanış" (gün sonu tahmini) üretmek — motivasyon aracı.

- **Bağımlılık:** CRM'de zaman damgası (saat:dakika) şu an yok ama erişilebilir olduğu düşünülüyor.
- **Güncelleme sıklığı:** Dakika bazında, gerçek zamanlı/akışkan.
- **Gün içi ağırlık eğrisi:** Ay-içi gün ağırlığı mantığının gün içine indirgenmiş hali — saatlere tarihsel dağılıma göre bir ağırlık profili atanır.

**Blend edilmiş projeksiyon formülü (tarihsel eğri + bugünün momentumu):**

```
Potansiyel Kapanış = Gerçekleşen(şimdi) + [α·k + (1-α)] × Σw(kalan zaman)
```

- **k (bugün katsayısı):** Son 1-2 saatlik yumuşatılmış gerçekleşme hızının, o saatler için tarihsel beklenen hıza oranı.
- **α (güven payı):** Gün ilerledikçe 0'dan 1'e artan bir ağırlık — günün başında tamamen tarihsel eğriye güvenilir (α≈0), gün ilerledikçe bugünün gerçek hızına daha çok itibar edilir (α→1). Erken saatte tek bir kayıtla projeksiyonun sıçramasını engeller.

**Motivasyon mesajı:** `Kalan İhtiyaç = Hedef − Gerçekleşen(şimdi)`, `Kalan Kapasite = Σw(kalan zaman)` üzerinden "hedefi tutturmak için kalan X saatte ortalama saatte Y adet daha gerekiyor" gibi somut bir mesaj üretilir.

**Personel başına dağılım:** Ayrı bir aşamada ele alınacak, henüz tasarlanmadı.

## Hesaplama Mimarisi

Hesaplama zekası Excel/Power BI'ın hesaplama sınırlarına bağlı kalmayacak — iki katmanlı mimari:

- **Katman 1 — Hesaplama/Zeka Katmanı:** Python (veya benzeri) tabanlı; ağırlık eğrilerini ve projeksiyon modellerini üretir. İleride regresyon/ML modellerine (zaman serisi, gradient boosting vb.) açık.
- **Katman 2 — Sunum Katmanı:** Power BI/dashboard; Katman 1'in ürettiği sonuçları okuyup görselleştirir, kendisi hesaplama yapmaz.
- **Bağlantı:** Katman 1 çıktısını bir veritabanına/tabloya yazar, Katman 2 canlı bağlantıyla oradan okur.
- **Aşamalandırma:** v1 — şeffaf, basit formül (blend modeli), yorumlanabilirlik ve yönetici güveni için. v2 — veri biriktikçe regresyon/ML ile genişletme.

## Açık Kararlar / Sonraki Adımlar

- Recency decay oranı henüz belirlenmedi (test edilip ince ayar yapılacak).
- Veri akışı otomasyonunun bağlantı yöntemi (CRM'e doğrudan API/veritabanı bağlantısı mı, otomatik export/import mı) henüz netleşmedi.
- Personel bazlı hedef/beklenti dağılımı henüz tasarlanmadı.
- Özel gün/tatil override mantığının arayüzde nasıl yönetileceği henüz netleşmedi.

---

## Görsel tasarım — mockup'lardan çıkan ilkeler

Codex ile devam edilen HTML dosyasına (Cep Performans Defteri V3) geçmeden önce Claude ile yapılan görsel deneylerden süzülen ilkeler:

- **Kurumsal kimlik:** Eminevim'in resmi logosu ve renk paleti (ana yeşil `#276F4E`, lacivert `#273755`; yardımcı turkuaz `#69BAB8`, turuncu `#C97A3A`, yeşil `#4C8F3C`/`#A1CA3A`, mercan `#C65A45`/`#E66A57`) kullanılmalı.
- **Jenerik "yapay zeka yapmış" görünümden kaçınma:** Herkeste aynı yuvarlatılmış kutu+gölge, ALL-CAPS etiketler, nokta-nokta birleştirilmiş metinler gibi klişelerden uzak durulmalı; halka (ring) göstergeler, defter/cüzdan estetiği tercih edilmeli.
- **Gerçek vs. tahmini ayrımı:** Projeksiyon değerleri her zaman "(tahmini)" gibi bir işaretle ayrılmalı, gerçek veriyle karıştırılmamalı.
- **Renk körlüğü desteği:** İyi/kötü durumlar sadece renkle değil, ▲/▼ gibi ok işaretleriyle de gösterilmeli.
- **Format:** Tutarlar tam sayı + nokta binlik ayraç (₺230.000), K/M kısaltması yok. Hedef ve Tahmini Kapanış sabit (lacivert) renkte; sadece Gerçekleşen duruma göre (kırmızı/yeşil) renklenir.
- **Hiyerarşik boyutlandırma:** Gün → Hafta → Ay → Yıl sırasıyla ana metrik (halka + büyük rakam + başlık) kademeli küçülür; Hedef/Tahmini Kapanış gibi referans rakamlar ise her sekmede aynı (sabit) boyutta kalır.
- **Tutarlılık:** Tüm periyot seçicileri (gün/hafta/ay/yıl bilet satırları) aynı görsel dili (nokta + iki satır metin, kart değil) paylaşmalı; hover ve seçili durumlar da tutarlı olmalı.

---

## HTML son durum değişiklik kaydı — 30 Eylül 2026

Ana çalışma dosyası: `yonetici-ozeti.html`

### İçerik ve yerleşim

- Ciro açıklaması ile “Örnek çalışma” bilgilendirme satırı kaldırıldı.
- Genel görünüm, ekran oranı ne olursa olsun bütün ana bölümler tek ekranda kalacak şekilde orantılı ölçeklenir hâle getirildi. Saha, Bölge, Şube ve Personel gibi uzun liste görünümleri bu sıkıştırmadan ayrı tutuldu.
- Ciro kartının dikey yüksekliği azaltılarak seyir ve alt analiz bölümüne daha fazla alan verildi.
- Saatlik ciro ve kayıt seyri bölümü büyütüldü; grafik ve eksen yazıları okunabilir hâle getirildi.
- Alt analiz satırındaki harita büyütüldü ve kendi alanında dikey/yatay ortalandı.
- Dönüşüm oranı ve toplam kanal dağılımı görsellerinin boyut ve hizaları birbirine yaklaştırıldı.
- Kanal dağılımı halkası ve açıklama puntoları büyütüldü; halkanın merkezindeki dönem yazısı kaldırıldı.

### Ciro kartı

- “Gerçekleşen” başlığındaki parantezli ifade kaldırıldı.
- Gerçekleşen tutarı ve hedef bilgisi büyütüldü.
- H/G göstergesi büyütülüp hafifçe yukarı alındı.
- “Türkiye Geneli” etiketi, gerçekleşen alanıyla daha tutarlı hizalandı.
- Teslimat bilgisi kapsam etiketiyle birlikte ciro kartında konumlandırıldı.

### Seyir grafikleri

- Saatlik ciro tutar etiketleri büyütüldü ve kontrastları güçlendirildi.
- İlk ve son ciro etiketlerinin SVG kenarlarında kırpılması engellendi.
- Saatlik ciro grafiğine görünür bir yatay eksen çizgisi eklendi.
- Ciro grafiğindeki saat etiketleri koyulaştırılıp kalınlaştırıldı.
- Kayıt Sayısı grafiğinin günlük ekseni `09:00`, `10:00` … biçimine çevrildi; hafta, ay ve yıl etiketleri kendi biçimlerini koruyor.
- Güncel saat dilimindeki kayıt sütunu özel olarak işaretlenir hâle getirildi.

### Açılış animasyonu

- Açılış, yaklaşık iki saniyelik sıralı bir anlatı hâline getirildi:
  1. Ciro ve KPI değerleri belirir; uygun rakamlar sıfırdan hedef değerlerine akar.
  2. Ciro çizgisi soldan sağa çizilir; kayıt sütunları sırayla yükselir.
  3. Harita, dönüşüm hunisi ve kanal dağılımı görünür olur.
  4. Saha kartları kısa aralıklarla sırayla açılır.
- Başlık ve filtre alanları açılış boyunca sabit bırakılarak yerleşim sıçraması önlendi.
- Hareket azaltma (`prefers-reduced-motion`) tercihi olan sistemlerde animasyonlar kapalı kalır.

### Bekleme durumu ve mikro animasyonlar

- Saatlik cirodaki “şu an” noktası daha yavaş ve yumuşak bir nabız hareketine geçirildi.
- Güncel kayıt sütununa düşük yoğunluklu bir nefes hareketi eklendi.
- Veri damgasındaki yeşil nokta seyrek aralıklarla yumuşak bir ışık halkası yayıyor.
- Ciro hedef çubuğundan yaklaşık 8,5 saniyede bir ince bir parlama geçiyor.
- Saha kartlarının üst çizgilerinde sırayla gezen, düşük yoğunluklu bir ışık vurgusu bulunuyor.
- Dekoratif bekleme hareketleri yalnız açılış tamamlandıktan sonra başlıyor.
- Tarayıcı sekmesi arka plana geçtiğinde sürekli animasyonlar duraklatılıyor; sekmeye dönüldüğünde saat konumu hemen güncelleniyor.
- Gösteri modu başladığında dekoratif bekleme animasyonları kapatılarak iki hareket katmanının üst üste binmesi engellendi.

### Gösteri modu durumu

- Mevcut gösteri akışı korunuyor: KPI kutularının adetlere dönüşmesi, saha turu, harita vurgusu ve saha kartlarının bölge yüzlerine dönmesi.
- Konuşulan sahne tabanlı yeni gösteri kurgusu henüz uygulanmadı; ayrı bir sonraki geliştirme olarak değerlendirilebilir.

### Kontroller

- Genel/Gün görünümü dar ve F11 benzeri ekran oranında kontrol edildi.
- Tek ekran ölçeklemesinde yatay taşma ve ana bölüm kırılması gözlenmedi.
- JavaScript sözdizimi değişikliklerden sonra doğrulandı.
