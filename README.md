# İki Hanehalklı Stok-Akım Tutarlı (SFC) Bir Giriş Modeli

Bu depo, stok-akım tutarlı (SFC) modellemeye yeni başlayanlar için hazırlanmış küçük bir giriş modelinin R kodlarını içerir. Model, Godley ve Lavoie'nın (2007) en basit modeli SIM'in bir adım ötesine geçmekte: hanehalkını işçiler ve rantiyeler olarak ikiye ayırıp, bankaları, krediyle finanse edilen yatırımı ve ücret-kâr ayrımını eklemektedir.

Kodlar bilerek olabildiğince sade yazılmıştır: her değişken düz bir vektördür, model tek bir döngüdür ve her denklem makaledeki numarasıyla etiketlenmiştir.

## Dosyalar

| Dosya | İçerik |
|---|---|
| `model_2h.R` | **Model 2H.** Çekirdek model, sabit fiyatlar. Makaledeki (1)-(27) numaralı denklemler. |
| `model_2h_cc.R` | **Model 2H-CC.** Model 2H'ye ücretler, fiyatlar ve çatışan talepler eklenmiş hali. Makaledeki (1)-(40) numaralı denklemler. |

İki dosya birbirinden bağımsızdır; her biri tek başına çalışır.

## Modelin yapısı

Model altı sektör içermektedir: işçiler, rantiyeler, firmalar, bankalar, merkez bankası ve devlet. Dış dünya yoktur.

- İşçiler ücret geliri elde eder, tüketici kredisi faizi öder ve harcanabilir gelirlerinin tamamını tüketir.
- Rantiyeler ücret, faiz ve temettü geliri elde eder; gelirlerinin ve servetlerinin bir kısmını tüketir, tasarruflarını mevduat ve devlet tahvili olarak tutar.
- Firmalar fiyatı maliyetin üzerine kâr marjı ekleyerek belirler, yatırımı banka kredisiyle finanse eder ve kârın tamamını dağıtır.
- Bankalar kredi verir, mevduat toplar; merkez bankası politika faizini belirler; devlet harcama yapar, vergi toplar ve açığını tahville finanse eder.

## Gereksinimler

- [R](https://www.r-project.org/) (4.2 ya da daha yeni bir sürüm önerilir)
- Şekiller için `plotly` paketi. Bir kez kurmak yeterlidir:

```r
install.packages("plotly")
```

## Nasıl çalıştırılır?

Dosyaları aynı klasöre indirin, R'da çalışma klasörünü o klasöre ayarlayın, bir senaryo numarası atayın ve dosyayı çağırın:

```r
scenario <- 2
source("model_2h.R", encoding = "UTF-8")
```

`scenario` atanmazsa kod 0 numaralı senaryoyu (şok yok) çalıştırır. Bütün şoklar 10. dönemde başlar ve kalıcıdır.

Kod çalışınca iki şey yapmaktadır:

1. Stok-akım tutarlılığı sınamalarının sonucunu ekrana çıktı olarak yazdırır.
2. Dokuz panelli, etkileşimli bir panel çizer.

## Senaryolar

**`model_2h.R`**

| `scenario` | Deney |
|---|---|
| 0 | Şok yok (durağan durum) |
| 1 | Kamu harcaması 150'den 160'a çıkar |
| 2 | Kâr marjı 3/7'den 0.50'ye yükselir |
| 3 | Politika faizi 2 puan artar |
| 4 | Rantiyelerden işçilere yeniden dağıtım (rantiyelerin vergi oranı ve sosyal transferler artar) |

**`model_2h_cc.R`**

| `scenario` | Deney |
|---|---|
| 0 | Şok yok (durağan durum, sıfır enflasyon) |
| 1 | Kâr marjı 3/7'den 0.50'ye yükselir |
| 2 | İşçiler reel ücret hedefini 500 artırır |
| 3 | Reel kamu harcaması 150'den 160'a çıkar |

Kendi deneyinizi yapmak için kodun 2. kısmına ("Senaryo") bir satır eklemeniz yeterlidir. Örneğin rantiyelerin otonom tüketimini 10. dönemden itibaren artırmak için:

```r
if (scenario == 5) c0[s] <- 30
```

## Stok-akım tutarlılığı sınamaları

Kod, simülasyondan sonra üç koşulu her dönem için hesaplar:

| Koşul | Anlamı |
|---|---|
| (C1) | Sektörlerin net borç vermesinin toplamı sıfırdır. |
| (C2) | Sektörlerin net finansal servetinin toplamı sıfırdır. |
| (C3) | Her sektörün net finansal serveti, tam olarak net borç vermesi kadar değişir. |

Ekrana yazılan değerler bu koşullardan en büyük sapmayı göstermektedir ve sıfıra çok yakın olmalıdır (örn. 10⁻¹⁰ mertebesinde). Koşullardan biri sağlanmazsa kod "Model stok-akım tutarlı DEĞİL!" mesajıyla durur.

Bu sınama, modeli değiştirirken en önemli yardımcınız olacaktır: bir denklem eklediğinizde ya da değiştirdiğinizde bir ödemenin karşılığını unutursanız kod bunu size söyler.

## Kodun yapısı

İki dosya da aynı yedi kısımdan oluşur:

1. Parametreler
2. Senaryo
3. Değişkenler
4. Başlangıç değerleri
5. Model (dönemler üzerinde dönen tek bir döngü)
6. Stok-akım tutarlılığı sınamaları
7. Şekiller

Dönem sayısı dosyanın başındaki `T` ile belirlenir (varsayılan değer 100). Yeni durağan duruma tam olarak ulaşıldığını görmek için `T` büyütülebilir.

## Notlar

- Dosyalar UTF-8 ile kodlanmıştır. Şekil başlıklarındaki Türkçe karakterler bozuk görünürse dosyayı yukarıdaki gibi `encoding = "UTF-8"` ile çağırın.
- Değişken adları makaledeki simgeleri izler; karşılıkları makalenin ekindeki tabloda verilmiştir.

## Kaynak

Modelin ayrıntılı anlatımı, denklemleri, bilanço ve işlem akış matrisleri ile deneylerin yorumu şu çalışmadadır:

> Özden, Oktay. "İki Hanehalklı Stok-Akım Tutarlı (SFC) Bir Giriş Modeli." (2027, yayın aşamasında)

Model, doktora tezimin 4. bölümünde geliştirilen modele dayanmaktadır:

> Özden, Oktay. 2025. "Inequality, Post-COVID Inflation and Incomes Policy in a Two Household SFC Model for the UK Economy." Doktora tezi, Kadir Has Üniversitesi, İstanbul.

Modelin Birleşik Krallık ekonomisine uygulanmış genişletilmiş ampirik sürümü:

> Özden, Oktay, ve Alp Erinç Yeldan. 2026. "A Two-Household Stock-Flow Consistent Model of the UK Economy: Distributional Consequences of Orthodox Disinflationary Policy and Alternatives." *Review of Political Economy*. https://doi.org/10.1080/09538259.2026.2691595

SFC modellemesi için temel başvuru kaynağı:

> Godley, Wynne, ve Marc Lavoie. 2007. *Monetary Economics: An Integrated Approach to Credit, Money, Income, Production and Wealth*. Palgrave Macmillan.

## Lisans

Kodlar MIT lisansı ile paylaşılmaktadır: kaynak göstererek serbestçe kullanabilir, değiştirebilir ve dağıtabilirsiniz.

## İletişim

Oktay Özden (oktay.ozdn@gmail.com)
İstanbul Bilgi Üniversitesi
www.oktayozden.com
