# Satış Raporlaması Araştırması ve Mevcut HTML Değerlendirmesi

**Proje:** Eminevim — Alternatif Satış Kanalı Yönetici Özeti  
**Hazırlanma tarihi:** 25 Eylül 2026  
**İncelenen yerel dosyalar:** `README.md`, `Yonetici-Ozeti-Proje-Notlari.md`, `index.html`  
**Not:** Bu doküman yönetim raporlaması ve karar desteği içindir; resmi finansal tablo veya denetim görüşü değildir.

## 1. Yönetici özeti

Mevcut HTML doğru bir temel fikre sahip: yöneticinin aynı yerde **Gerçekleşen, Hedef, Tahmini Kapanış, dönem karşılaştırması ve zaman kırılımını** görmesini sağlıyor. Gün/Hafta/Ay/Yıl geçişi, gerçek–tahmin ayrımı, Türkçe sayı biçimi ve klavye erişimi düşünülmüş.

Ancak rapor bugün daha çok “iyi tasarlanmış bir performans defteri” görünümünde. Üst yönetim aracı olabilmesi için aşağıdaki beş eksiğin kapatılması gerekiyor:

1. **Karar mesajı:** Sayfanın en üstünde “Ne oluyor, neden oluyor, ne yapılmalı?” cümlesi bulunmalı.
2. **Adil dönem kıyası:** Gerçekleşen, hem tam dönem hedefiyle hem de aynı güne kadar birikmiş ağırlıklı hedefle ayrı ayrı karşılaştırılmalı.
3. **Sonuç + sürücü dengesi:** Ciro tek başına yeterli değil; lead, randevu, dönüşüm, ortalama işlem tutarı, satış çevrimi ve iptal/kalite göstergeleri eklenmeli.
4. **Tahmin güveni ve veri güveni:** Tahmin aralığı, tahmin doğruluğu, veri yenilenme zamanı, kaynak ve kalite durumu görünür olmalı.
5. **Görsel semantik:** Hedef, gerçekleşen ve tahmin her yerde aynı görsel kuralla gösterilmeli; renk yalnız istisna ve vurgu için kullanılmalı.

Önerilen yön: mevcut “cep performans defteri” karakterini tamamen silmek değil, onu **daha sakin, daha açıklayıcı ve daha denetlenebilir bir yönetici kokpitine** dönüştürmektir.

## 2. Araştırma yaklaşımı ve kaynak güvenilirliği

Araştırmada üç kaynak katmanı kullanıldı:

- **Uluslararası standart/standartlaşmış çerçeveler:** IBCS ve W3C WCAG 2.2.
- **Üretici ve platform rehberleri:** Microsoft Power BI, Salesforce, IBM Carbon Design System ve GOV.UK Design System.
- **Yaygın kabul gören uygulama kaynakları:** Tableau, Nielsen Norman Group ve HubSpot. Bunlar standart değildir; pratik örnek ve yaygın kullanım perspektifi için kullanılmıştır.

Kaynaklardan alınan ilkeler doğrudan kopyalanmamış, mevcut Eminevim kullanım senaryosuna uyarlanmıştır. “Premium görünüm” resmi bir standart değildir; aşağıdaki premium tasarım önerileri erişilebilirlik, görsel hiyerarşi, tutarlılık ve gereksiz süsten kaçınma ilkelerinden türetilmiş tasarım yorumudur.

## 3. İyi bir satış raporu hangi soruları cevaplamalı?

Bir yönetici ekranı beş saniye içinde şu soruların ilk dördünü; bir dakikada tamamını cevaplamalıdır:

1. Şu anda hedefin önünde miyiz, gerisinde miyiz?
2. Dönem sonunda hedefi tutturacak mıyız?
3. Fark kaç TL ve kaç yüzde puan?
4. Son güncellemeden bu yana ne değişti?
5. Sapmanın ana nedeni hangi satış sürücüsü?
6. Hangi kanal, ekip, ürün veya gün müdahale gerektiriyor?
7. Alınması gereken aksiyon nedir, sahibi kimdir, ne zamana kadar yapılmalıdır?
8. Veriye ve tahmine ne kadar güvenebiliriz?

Microsoft, dashboard'u tek sayfalık bir izleme alanı olarak tanımlar; ayrıntının alttaki raporlara bırakılmasını, kritik bilginin üst-sol bölgede yer almasını ve gereksiz kalabalığın kaldırılmasını önerir. IBCS ise raporun mesaj taşımasını, aynı anlamın aynı görünmesini, doğru grafik seçimini, görsel bütünlüğü ve tutarlı senaryo gösterimini vurgular.

## 4. Önerilen ölçüm mimarisi

### 4.1 Sonuç KPI'ları — üst yönetim katmanı

Ana ekranda en fazla 4–6 sonuç KPI'ı bulunmalı:

| KPI | Tanım / formül | Yöneticiye söylediği |
|---|---|---|
| Net satış/ciro | İptal ve iadeler sonrası onaylı tutar | Sonuç |
| Ağırlıklı MTD H/G | MTD gerçekleşen ÷ aynı güne kadar ağırlıklı hedef | Bugünkü tempo doğru mu? |
| Tam dönem ilerleme | MTD gerçekleşen ÷ tam ay hedefi | Hedefin ne kadarı tamamlandı? |
| Tahmini kapanış H/G | Dönem sonu tahmini ÷ tam dönem hedefi | Dönem sonu beklentisi |
| MoM / YoY eş dönem | Aynı ağırlıklı gün veya iş günü kesitine göre değişim | Gerçek performans değişimi |
| Tahmin doğruluğu | 1 − ABS(tahmin − gerçekleşen) ÷ gerçekleşen | Tahmine güven düzeyi |

“Aylık hedefin %82'si tamamlandı” ile “bugüne kadarki hedefin %103'ündeyiz” aynı şey değildir. Birincisi **ilerleme**, ikincisi **tempo** bilgisidir. Aynı halka veya aynı H/G etiketiyle verilmemelidir.

### 4.2 Sürücü KPI'ları — neden katmanı

Sürücü KPI'ları iş modeline göre netleştirilmeli. İlk öneri:

| Boyut | KPI örnekleri | Not |
|---|---|---|
| Talep | Yeni lead, nitelikli lead, randevu | Hacim sorunu var mı? |
| Huninin verimi | Lead→randevu, randevu→satış, toplam dönüşüm | Nerede kayıp var? |
| Değer | Ortalama işlem tutarı, ürün/segment karması | Ciro neden değişti? |
| Hız | Satış çevrim süresi, aşamada bekleme, günlük hız | Kapanış gecikiyor mu? |
| Kapasite | Aktif temsilci, temsilci başı üretim, temas hacmi | Kaynak yeterli mi? |
| Kalite | İptal/iade oranı, eksik evrak, tekrar işlem | Brüt satış kaliteli mi? |
| Pipeline | Açık fırsat değeri, ağırlıklı pipeline, kapsama oranı | Gelecek dönem riski |

Salesforce, yönetici satış panolarında toplam gelir, pipeline değeri, kota/hedef gerçekleşmesi ve tahmin doğruluğunu; tahmin panosunda ise kazanma oranı, ortalama işlem tutarı, satış çevrim süresi ve pipeline değerini öne çıkarır. Bu yüzden mevcut yalnız-ciro yaklaşımı sonuç gösterir fakat nedeni açıklamaz.

### 4.3 Her KPI için zorunlu “ölçüm sözlüğü”

Her metriğin arka planda aşağıdaki alanları olmalıdır:

- İş adı ve kısa adı
- İş tanımı ve karar amacı
- Formül; pay ve payda
- Para birimi ve KDV/iptal/iade kapsamı
- Veri tanecik seviyesi: işlem, müşteri, temsilci, gün vb.
- Kaynak sistem ve kaynak alanlar
- Filtreler ve hariç tutmalar
- Zaman alanı: oluşturma, onay, sözleşme veya tahsilat tarihi
- Veri sahibi ve iş sahibi
- Yenilenme sıklığı ve gecikme toleransı
- Hedef yönü: yüksek iyi / düşük iyi
- Eşikler ve eşik gerekçesi
- Geçmişe dönük düzeltme kuralı
- Sürüm ve değişiklik tarihi

KPI sözlüğü olmadan farklı ekiplerin aynı isimle farklı sayılar üretmesi kaçınılmazdır.

## 5. Zaman, hedef ve kıyas kuralları

### 5.1 Aynı kesiti karşılaştır

- MTD gerçekleşen ↔ MTD ağırlıklı hedef
- MTD gerçekleşen ↔ geçen ayın aynı ağırlıklı/iş günü kesiti
- MTD gerçekleşen ↔ geçen yılın aynı takvim ve özel-gün etkisi düzeltilmiş kesiti
- Tam ay tahmini ↔ tam ay hedefi
- Tamamlanmış ay ↔ tamamlanmış önceki ay/yıl

Tam ay hedefiyle ayın 12. günündeki gerçekleşeni “başarısız” diye kırmızıya boyamak yanlış alarm üretir. Tam hedef ilerlemesi nötr gösterilebilir; başarı rengi MTD tempo veya tahmini kapanış için kullanılmalıdır.

### 5.2 Ağırlıklı hedef yaklaşımı

Proje notlarındaki yöntem doğru yöndedir:

```text
Günlük Hedef(d) = Aylık Hedef × w(d) / Σw(ayın tüm günleri)
MTD Hedef       = Σ Günlük Hedef(ayın başı…bugün)
MTD H/G         = MTD Gerçekleşen / MTD Hedef
```

Uygulama şartları:

- Ağırlıklar ay başında kilitlenmeli; geriye dönük oynatılmamalı.
- Haftanın günü, ay içi konum, tatil/bayram ve kampanya etkisi ayrı alanlarda saklanmalı.
- Manuel override yapan kişi, gerekçe ve zaman damgası kaydedilmeli.
- MTD hedef ile tam ay hedefi aynı isim altında gösterilmemeli.
- Takvim haftası tanımı tüm sayfalarda aynı olmalı. ISO Pazartesi–Pazar kullanılıyorsa ay görünümü de 1–7, 8–14 bloklarına bölünmemeli.

### 5.3 Hedef eşikleri

Mevcut HTML'deki `>= %90 iyi, <%90 kötü` kuralı tüm dönemlere ve tüm KPI'lara uygulanıyor. Bu iş kuralı olarak belgelenmedikçe kullanılmamalı.

Önerilen örnek bant:

- **Hedefte/üzeri:** ≥ %100
- **İzleme:** %95–99,9
- **Risk:** < %95

Eşikler istatistiksel geçmiş, yönetim toleransı ve KPI yönüne göre farklılaştırılmalıdır. İptal oranında düşük değer iyidir; ciroda yüksek değer iyidir.

## 6. Tahmin raporlaması

Tahmin tek bir kesin sayı gibi sunulmamalıdır. Yönetici ekranında şu paket birlikte yer almalı:

- Nokta tahmini: ör. ₺5.320.000
- Güven aralığı: ör. ₺5.080.000–₺5.560.000
- Hedefi aşma olasılığı: ör. %68
- Tahmin kesit zamanı: 25 Eylül 2026 16:30
- Model/sürüm: ör. Blend v1.2
- Son 8 dönem tahmin doğruluğu: ör. %92
- Ana varsayım: kampanya/tatil/kapasite

Microsoft'ın satış tahmini örneği, tahmin periyodu, mevsimsellik ve güven aralığının ayarlanmasını özellikle belirtir. Salesforce ise kapanma olasılığının tarihsel veriyle kalibre edilmesini ve tahminlerin haftalık gözden geçirilmesini önerir.

Gün içi “potansiyel kapanış” için proje notlarındaki blend modeli kullanılabilir; ayrıca erken saat güveni düşük olduğu için aralık geniş, gün sonuna yaklaştıkça dar gösterilmelidir. Tahmin ile gerçekleşen aynı çizgi tipiyle gösterilmemeli: gerçekleşen düz/solid, tahmin kesik/dashed olmalıdır.

## 7. Veri güveni ve raporlama işletim modeli

### 7.1 Veri akışı kontrolleri

CRM → hesaplama katmanı → sunum katmanı hattında en az şu kontroller gerekir:

- Kaynak satır sayısı ve yüklenen satır sayısı mutabakatı
- Aynı işlem/fırsat için tekilleştirme
- Boş veya gelecekte tarih kontrolü
- Negatif/olağandışı tutar kontrolü
- İptal/iade ve durum geçişi kontrolü
- CRM toplamı ile rapor toplamı mutabakatı
- Son başarılı yenileme ve gecikme alarmı
- Hatalı kayıt karantinası ve veri kalite yüzdesi
- Hesaplama sürümü ve audit log

Canlı saat, yalnız veri gerçekten canlı yenileniyorsa gösterilmelidir. Aksi halde kullanıcı saati “veri şu an güncel” diye yorumlayabilir. Doğru etiket: **“Veri: 16:30 itibarıyla · 4 dk gecikme · Kalite %99,7”**.

### 7.2 Gözden geçirme ritmi

| Ritim | Amaç | İçerik |
|---|---|---|
| Gün içi | Operasyon ve tempo | Günlük hedef, anlık gerçekleşen, kalan ihtiyaç, kapasite |
| Günlük kapanış | Sapma ve aksiyon | Gün sonucu, neden, ertesi gün aksiyonu |
| Haftalık | Pipeline ve tahmin | W/W değişim, forecast, darboğaz, sorumlu |
| Aylık | Strateji ve öğrenme | H/G, MoM, YoY, segment katkısı, tahmin doğruluğu |
| Çeyreklik | KPI ve eşik bakımı | Metrik faydası, hedef kalibrasyonu, veri/model kalitesi |

Rapor tek başına süreç değildir. Her istisnanın aksiyon sahibi ve takip tarihi olmalıdır.

## 8. Yapılması gerekenler / yapılmaması gerekenler

### Yapılmalı

- Sayfa başlığına bir sonuç cümlesi yazılmalı.
- Gerçekleşen, hedef, tahmin ve geçen dönem tutarlı semantiklerle ayrılmalı.
- MTD/YTD kıyaslar aynı kesitte yapılmalı.
- KPI'a hem değer hem hedefe fark hem trend bağlamı verilmeli.
- Sonuç KPI'ı yanında onu açıklayan 3–5 sürücü gösterilmeli.
- İstisna odaklı tasarım kullanılmalı; normal olan sakin, riskli olan görünür olmalı.
- Veri güncelliği, kaynak ve kalite durumu görünür olmalı.
- Tahmin güven aralığı ve doğruluğu raporlanmalı.
- Grafiklere doğrudan etiket konmalı; gereksiz lejant azaltılmalı.
- Mobil görünümde içerik öncelik sırasıyla yeniden akmalı.
- Renk yanında ok, işaret, metin veya çizgi deseni kullanılmalı.
- Klavye, odak görünürlüğü, ekran okuyucu özeti ve azaltılmış hareket desteği korunmalı.

### Yapılmamalı

- Her KPI kart, her kart renkli ve her bölüm gölgeli yapılmamalı.
- Tam dönem hedefiyle kısmi dönem gerçekleşeni “iyi/kötü” diye doğrudan sınıflandırılmamalı.
- “Canlı” ifadesi veri yenilenme kanıtı olmadan kullanılmamalı.
- Tahmin, gerçekleşen gibi düz ve kesin gösterilmemeli.
- Pozitif yüzde her KPI'da otomatik yeşil sayılmamalı.
- Sırf çeşitlilik için halka, pasta, gauge ve 3B grafik kullanılmamalı.
- Kesilmiş eksen veya farklı ölçeklerle yanıltıcı kıyas yapılmamalı.
- Aynı ekranda farklı tarih kapsamları etiketsiz karıştırılmamalı.
- Renk tek anlam taşıyıcısı olmamalı.
- 8–11 px yardımcı metinler ana karar bilgisinde kullanılmamalı.
- “Vanity metric” sayısı artırılmamalı; aksiyon üretmeyen metrik kaldırılmalı.

## 9. Premium görünüm nasıl oluşturulur?

Premium görünüm, daha fazla efekt değil, daha fazla **kontrol ve tutarlılık** hissidir.

### 9.1 Tasarım dili

- Tek güçlü marka zemini + açık ana yüzey + sınırlı vurgu renkleri.
- Kart denizi yerine hizalı bölümler, ince ayraçlar ve geniş boşluk.
- Bir ana sayı, 3–4 ikincil sayı, geri kalan bilgi daha düşük tonda.
- En fazla 2–3 yazı boyutu ailesi; rakamlarda tabular numerals.
- Tek tip köşe yarıçapı; en fazla iki gölge seviyesi.
- Dekoratif altın yalnız ince vurgu/çizgi için; veri veya küçük metin rengi olarak değil.
- Gerçekleşen düz, hedef işaret/çizgi, tahmin kesik/desenli semantiği.
- “Defter” dokusu çok hafif tutulmalı; raporun önüne geçmemeli.

Nielsen Norman Group'un görsel hiyerarşi rehberi, önemi boyut ve kontrastla sıralamayı ve sınırlı sayıda boyut kullanmayı önerir. IBCS de aynı anlamın aynı görünmesini ve senaryoların standart notasyonla ayrılmasını ister.

### 9.2 Önerilen renk rolleri

| Rol | Renk | Kullanım | Açık zemin kontrastı |
|---|---|---|---:|
| Marka | `#276F4E` | Üst bant, marka vurgusu | 5,84:1 |
| Ana metin / hedef | `#273755` | Başlık, hedef, ana rakam | 10,98:1 |
| Olumlu | `#1F6B45` | Başarı/pozitif sapma | 6,25:1 |
| Risk | `#9B342C` | Negatif sapma/uyarı | 6,94:1 |
| Tahmin | `#1C6F73` | Tahmin çizgisi ve etiketi | 5,68:1 |
| İkincil metin | `#58645F` | Açıklama/meta | 5,96:1 |
| Ana yüzey | `#FCFBF7` | Rapor zemini | — |
| Ayraç | `#D9DED9` | Çizgi/border | — |
| Dekoratif altın | `#D6A94F` | Sadece kalın çizgi/ikon; küçük metin değil | 2,10:1 |

Kontrastlar `#FCFBF7` zeminine göre hesaplanmıştır. WCAG 2.2 AA, normal metinde en az 4,5:1; büyük metinde en az 3:1 ister. IBM Carbon veri görselleştirme paletlerini erişilebilirlik ve sayfa içi uyum için kurgular. Renklerin rollere bağlanması ve aynı rolün her yerde aynı renk/desenle gösterilmesi gerekir.

### 9.3 Mevcut palette erişilebilirlik bulgusu

`#F7F6EE` yüzeyi üzerinde yaklaşık kontrastlar:

- `#77817D`: 3,71:1 — küçük metinde yetersiz
- `#65706C`: 4,74:1 — AA geçer, çok küçük puntoda yine zor okunur
- `#4C8F3C`: 3,65:1 — normal metinde yetersiz
- `#C65A45`: 3,91:1 — normal metinde yetersiz
- `#69BAB8`: 2,08:1 — metin ve ince veri çizgisinde yetersiz
- `#273755`: 10,98:1 — güçlü

Renk körlüğü desteği için okların kullanılması doğru bir başlangıçtır; fakat okun neye göre yön verdiği açık yazılmalıdır: “hedefe göre”, “geçen aya göre” veya “düne göre”.

## 10. Grafik seçimi

| Karar ihtiyacı | Önerilen görsel | Kaçınılacak |
|---|---|---|
| Hedefe mesafe | Bullet/progress bar + fark etiketi | Büyük donut/gauge tekrarı |
| Zaman içi trend | Çizgi; gerçek düz, tahmin kesik | Her noktaya büyük etiket |
| Kategori/ekip kıyası | Yatay bar / dot plot | Pasta ve 3B sütun |
| Sapma | Variance bar, waterfall | Sadece renkli yüzde |
| Huni | Aşama tablosu + dönüşüm oranı | Alanı yanıltan dekoratif funnel |
| Yoğun detay | Isı haritası + erişilebilir tablo | Çok renkli kart matrisi |

IBCS, sütun/bar/çizgileri pasta ve gauge'a tercih eder. Microsoft da dairesel grafiklerin kıyas için ideal olmadığını, bar ve sütunların daha kolay karşılaştırıldığını belirtir.

## 11. Mevcut `index.html` raporunun mantığı

### 11.1 Yapı

- Tek dosyada HTML + CSS + JavaScript.
- Gün, Hafta, Ay ve Yıl için ARIA tab yapısı.
- Gün sayfası: gerçekleşen/beklenti, hedef, tahmini kapanış, dün, H/G halkası, geçen hafta günlüğü ve saatlik kümülatif grafik.
- Hafta sayfası: haftalık toplam, hedef, tahmin, önceki hafta, hafta içi/hafta sonu ayrımı.
- Ay sayfası: ay toplamı, hedef, tahmin, MoM, YoY ve dört haftalık kümülatif rota.
- Yıl sayfası: YTD sonuç, yıllık hedef, tahmin, YoY, çeyrek ve ay bazlı H/G.
- Tarih ve gün/hafta/ay/yıl seçicileri istemci tarafında üretiliyor.
- Tüm sayılar örnek/hard-coded veri; gerçek CRM bağlantısı yok.

### 11.2 Hesap mantığı

- Performans durumu tüm ana KPI'larda `%90` eşiğine göre iyi/kötü.
- Haftalık H/G: mevcut gerçekleşen ÷ tam hafta hedefi.
- Aylık H/G: MTD gerçekleşen ÷ tam ay hedefi.
- Ay MoM ve YoY kıyası takvim günü oranıyla yaklaşık prorate ediliyor.
- YTD H/G: gerçekleşmiş ayların toplamı ÷ aynı ayların hedef toplamı.
- Saatlik değerler sabit ağırlık dizisiyle dağıtılıyor; gün içi gerçek CRM zaman damgası kullanılmıyor.
- Gelecek gün/ay değerleri tahmin gibi işaretleniyor; ancak güven aralığı ve model bilgisi yok.

## 12. Mevcut raporda korunması gerekenler

1. Gün/Hafta/Ay/Yıl düşünce modeli.
2. Gerçek ile tahminin metinsel olarak ayrılması.
3. Tam tutar ve Türkçe sayı biçimi.
4. Marka kimliğinin lacivert/yeşil ekseni.
5. Klavye ile tab ve gün gezintisi.
6. `prefers-reduced-motion` desteği.
7. Mobil öncelikli düşünce.
8. Rakamların örnek olduğunu belirten dipnot.
9. Geçmiş dönem seçiminin aynı ekran içinde yapılabilmesi.

## 13. Teker teker değiştirilmesi gerekenler

### 1 — Üstte karar cümlesi

Başlığın altına dinamik mesaj eklenmeli:

> “Ay sonu tahmini hedefin %103'ü. Son 3 günde dönüşüm oranı 1,8 puan düştü; Ege ve Dijital kanal izlenmeli.”

### 2 — “İlerleme” ile “tempo” ayrılmalı

Ay ekranında iki ayrı oran gösterilmeli:

- Tam ay hedefinin tamamlanan kısmı: %82
- Bugüne kadarki ağırlıklı hedefe göre tempo: %103

### 3 — Halka azaltılmalı

Her periyotta büyük halka yerine hedef işaretli yatay bullet bar kullanılmalı. Halka yalnız mobilde tek bir özet gösterge olarak kalabilir.

### 4 — Gerçek/hedef/tahmin semantiği standartlaştırılmalı

- Gerçek: koyu lacivert/yeşil düz dolgu
- Hedef: ince dikey işaret veya koyu referans çizgisi
- Tahmin: teal kesik çizgi veya taralı alan
- Geçen dönem: açık gri

### 5 — Eşik sistemi düzeltilmeli

`%90 = iyi` sabiti kaldırılmalı. KPI bazlı eşik tablosu veri katmanından gelmeli; %95–100 “izleme” gibi ara durum eklenmeli.

### 6 — Aynı dönem kıyasları net etiketlenmeli

“Geçen ay” yerine “Geçen ay, aynı ağırlıklı gün”; “Eylül 2025” yerine “25 Eylül 2025 eş dönem” yazılmalı.

### 7 — Haftalar tutarlı tanımlanmalı

Hafta ekranı ISO Pazartesi–Pazar kullanırken ay ekranı 1–7 blokları kullanıyor. Tek takvim standardı seçilmeli ve her yerde aynı kullanılmalı.

### 8 — Tahmin güveni eklenmeli

Tahmini kapanış altında güven aralığı, model adı, tahmin zamanı ve son dönem doğruluğu gösterilmeli.

### 9 — Veri güncelliği eklenmeli

Canlı saat yerine veya yanında “Veri 16:30'da yenilendi · CRM · 4 dk gecikme · kalite %99,7” kullanılmalı.

### 10 — Ciroyu açıklayan sürücüler eklenmeli

Ana sayfanın ikinci bölümünde lead, randevu, dönüşüm, ortalama işlem tutarı ve iptal oranı yer almalı. Her biri hedefe ve önceki döneme göre kıyaslanmalı.

### 11 — İstisna ve aksiyon alanı eklenmeli

En fazla üç madde: bulgu, iş etkisi, önerilen aksiyon, sorumlu ve tarih.

### 12 — Saatlik grafik sadeleştirilmeli

Her noktanın tutarı yerine başlangıç, son gerçekleşen, hedef ve kapanış tahmini etiketlenmeli. Tahmin bölümü kesik çizgi ve hafif tarama ile ayrılmalı.

### 13 — Yazı boyutları büyütülmeli

6–9 px yardımcı yazılar kaldırılmalı. Mobilde kritik olmayan metin en az 12 px, ana açıklamalar 14 px civarı olmalı; etkileşim alanları en az 24×24 px ve pratikte 40–44 px hedeflenmeli.

### 14 — Renk kontrastı düzeltilmeli

Mevcut açık yeşil, mercan ve teal küçük metinde kullanılmamalı; koyu erişilebilir karşılıkları uygulanmalı. Renk yanında sembol/metin kalmalı.

### 15 — Ölçüm tanımları erişilebilir olmalı

Her KPI yanında kısa bilgi açıklaması veya alt detay paneli bulunmalı: formül, kapsam, veri kaynağı, son yenileme.

### 16 — Drill-through tasarlanmalı

Yönetici özetindeki bir istisna seçildiğinde kanal → ekip → temsilci/ürün detayı açılmalı; ana ekran detaya boğulmamalı.

### 17 — Mobil içerik sırası değiştirilmelidir

Mobil sıra: karar mesajı → ana KPI → tempo/tahmin → sürücüler → istisnalar → trend → detay. Yatay kaydırma kritik bilgi için kullanılmamalı.

### 18 — Gerçek veri entegrasyonu öncesi kontrol kapısı

CRM mutabakatı, ölçüm sözlüğü, zaman alanı kararı, iptal/iade kuralı ve hedef kilitleme süreci onaylanmadan “canlı dashboard” yayına alınmamalı.

## 14. Önerilen tek-ekran yerleşim

```text
┌────────────────────────────────────────────────────────────────────┐
│ Marka · Dönem seçimi                  Veri zamanı · Kalite durumu │
├────────────────────────────────────────────────────────────────────┤
│ Karar mesajı: Ne oluyor, neden, önerilen aksiyon                  │
├───────────────────────────────┬────────────────────────────────────┤
│ Ana KPI: Net satış            │ Tempo / Tahmin / Fark             │
│ Tam tutar + eş dönem değişim  │ Bullet bar + güven aralığı        │
├───────────────────────────────┴────────────────────────────────────┤
│ Satış sürücüleri: Lead · Randevu · Dönüşüm · Ort. tutar · İptal  │
├──────────────────────────────────────────┬─────────────────────────┤
│ Gerçek + hedef + tahmin zaman çizgisi    │ 3 kritik istisna/aksiyon│
├──────────────────────────────────────────┴─────────────────────────┤
│ Tanımlar · kaynak · yenileme · tahmin modeli · detay bağlantısı   │
└────────────────────────────────────────────────────────────────────┘
```

## 15. Örnek dosyanın kapsamı

`ornek-premium-satis-yonetici-ozeti.html` mevcut dosyanın yerine geçmez. Aşağıdaki kararları göstermek için hazırlanmış bağımsız bir prototiptir:

- Bir ana mesaj ve tek baskın KPI
- MTD tempo ile tam dönem ilerlemesinin ayrılması
- Gerçek/hedef/tahmin için tutarlı çizgi ve renk semantiği
- Tahmin aralığı ve güven notu
- Sonuç KPI'ını açıklayan sürücü tablosu
- İstisna ve aksiyon bölümü
- Veri tazeliği ve kalite göstergesi
- Daha koyu, WCAG uyumlu renkler ve daha büyük yazılar
- Masaüstü ve mobil için yeniden akış

Örnek veriler temsili ve statiktir.

## 16. Uygulama sırası

### Faz 1 — Karar ve tanım

1. Yönetici karar sorularını onayla.
2. KPI sözlüğünü oluştur.
3. Zaman/kıyas ve hedef ağırlık kurallarını onayla.
4. İptal/iade/net satış tanımını kilitle.

### Faz 2 — Veri güveni

5. CRM veri hattını otomatikleştir.
6. Mutabakat ve kalite kontrollerini kur.
7. Yenilenme SLA'sı ve audit log ekle.

### Faz 3 — Analitik

8. Şeffaf blend tahmini devreye al.
9. Backtest ile doğruluk ve güven aralığını hesapla.
10. Eşikleri tarihsel dağılıma göre kalibre et.

### Faz 4 — Arayüz

11. Örnek tek-ekran yönünü paydaşla seç.
12. Gerçek Power BI/dashboard bileşenlerine uyarla.
13. Masaüstü, mobil, klavye, ekran okuyucu ve kontrast testlerini tamamla.

### Faz 5 — İşletim

14. Günlük/haftalık toplantı ritmini tanımla.
15. Kullanılmayan KPI'ları çeyreklik kaldır; yeni KPI'yı sözlüksüz ekleme.

## 17. Kaynaklar

Erişim tarihi: 25 Eylül 2026.

1. [IBCS — International Business Communication Standards ve SUCCESS ilkeleri](https://www.ibcs.com/IBCS/)
2. [Microsoft Learn — Tips for designing a great Power BI dashboard](https://learn.microsoft.com/en-us/power-bi/create-reports/service-dashboards-design-tips)
3. [Microsoft Learn — KPI visualizations](https://learn.microsoft.com/en-us/power-bi/visuals/power-bi-visualization-kpi)
4. [Microsoft Learn — Sales Forecasting report](https://learn.microsoft.com/en-us/dynamics365/business-central/sales-powerbi-sales-forecasting)
5. [Microsoft Learn — Store Sales sample](https://learn.microsoft.com/en-us/power-bi/create-reports/sample-store-sales)
6. [Salesforce Help — Pipeline Forecasting Best Practices](https://help.salesforce.com/s/articleView?id=forecasts3_best_practices.htm&language=en_US&type=5)
7. [Salesforce — Sales dashboard examples and KPI guidance](https://www.salesforce.com/blog/sales/sales-dashboard-examples/)
8. [Salesforce Trailhead — Build a healthy sales pipeline](https://trailhead.salesforce.com/content/learn/modules/sales-pipeline-basics/build-a-healthy-sales-pipeline)
9. [W3C — Web Content Accessibility Guidelines (WCAG) 2.2](https://www.w3.org/TR/wcag/)
10. [IBM Carbon Design System — Data visualization color palettes](https://carbondesignsystem.com/data-visualization/color-palettes/)
11. [GOV.UK Brand Guidelines — Data visualization principles](https://brand.design-system.service.gov.uk/data/)
12. [GOV.UK Design System — Colour and contrast](https://design-system.service.gov.uk/styles/colour/)
13. [Nielsen Norman Group — Visual Design Principles](https://www.nngroup.com/articles/principles-visual-design/)
14. [Tableau — 10 Best Practices for Building Effective Dashboards](https://www.tableau.com/sites/default/files/2021-09/10%20Best%20Practices%20for%20Building%20Effective%20DashboardsWP.pdf)
15. [HubSpot — Sales performance dashboard guidance](https://blog.hubspot.com/sales/sales-dashboard)

## 18. Son karar için öneri

İlk karar, “mevcut defter estetiğini küçük iyileştirmelerle sürdürmek” ile “karar mesajı ve sürücü KPI'ları merkezli yeni tek-ekran yapıya geçmek” arasındadır. Araştırma bulgularına göre ikinci yön daha güçlüdür; ancak marka sıcaklığını korumak için mevcut kağıt yüzey ve koyu yeşil çerçeve kontrollü biçimde devam ettirilebilir.
