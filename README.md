# İki Hanehalklı Stok-Akım Tutarlı (SFC) Bir Giriş Modeli

Bu depo, stok-akım tutarlı (SFC) modellemeye yeni başlayanlar için hazırlanmış küçük bir giriş modelinin R kodlarını içerir. Model, Godley ve Lavoie'nın (2007) en basit modeli SIM'in bir adım ötesine geçer: hanehalkını işçiler ve rantiyeler olarak ikiye ayırır, bankaları, krediyle finanse edilen yatırımı ve ücret-kâr ayrımını ekler.

Bu belge, daha önce hiç R kullanmamış birinin modeli adım adım çalıştırabilmesi için yazılmıştır. Sırayla izlemeniz yeterlidir.

| Dosya | İçerik |
|---|---|
| `model_2h.R` | **Model 2H.** Çekirdek model, sabit fiyatlar. |
| `model_2h_cc.R` | **Model 2H-CC.** Model 2H'ye ücretler, fiyatlar ve enflasyon eklenmiş hali. |

İki dosya birbirinden bağımsızdır; her biri tek başına çalışır.

---

## Adım 0. R ve Positron'u kurun (yalnızca bir kez)

1. Önce R'ı indirip kurun: <https://cran.r-project.org>
2. Sonra Positron'u indirip kurun: <https://positron.posit.co/download>

R hesaplamayı yapan programdır; Positron ise R'ı rahat kullanmanızı sağlayan arayüzdür. Positron R'ı kendisi kurmaz, bu yüzden sırayı izleyin: önce R, sonra Positron. Bundan sonra hep Positron'u açacaksınız.

RStudio kullanıyorsanız aynı adımları orada da izleyebilirsiniz; yalnızca menü adları biraz farklıdır.

## Adım 1. Dosyaları indirin

1. Bu sayfanın üstündeki yeşil **Code** düğmesine, ardından **Download ZIP** seçeneğine tıklayın.
2. İnen ZIP dosyasını açın ve içindeki klasörü masaüstüne taşıyın.
3. Klasörün adını `SFCyegiris` yapın.

Artık masaüstünüzde, içinde `model_2h.R` ve `model_2h_cc.R` dosyaları bulunan bir `SFCyegiris` klasörü olmalı.

## Adım 2. Positron'u açın ve klasörü tanımlayın

R'a dosyaların nerede olduğunu söylemeniz gerekir. Buna "çalışma klasörü" (working directory) denir. Positron'da bir klasörü açtığınızda çalışma klasörü kendiliğinden o klasör olur.

1. Positron'u açın.
2. Üst menüden **File → Open Folder...** yolunu izleyin.
3. Masaüstündeki `SFCyegiris` klasörünü seçin ve onaylayın. "Bu klasördeki dosyaların yazarlarına güveniyor musunuz?" diye bir soru çıkarsa **Yes, I trust the authors** seçeneğine tıklayın.
4. Sol taraftaki dosya listesinde `model_2h.R` ve `model_2h_cc.R` dosyalarını görmelisiniz.
5. Alttaki **Console** penceresinde R'ın başladığını (bir `>` işareti) görmelisiniz. Görmüyorsanız sağ üst köşedeki oturum seçicisinden R'ı seçin.

Doğru yapıp yapmadığınızı sınamak için alttaki **Console** penceresine şunu yazıp Enter'a basın:

```r
list.files()
```

Çıktıda `model_2h.R` ve `model_2h_cc.R` adlarını görüyorsanız çalışma klasörü doğrudur.

## Adım 3. Şekiller için gereken paketi kurun (yalnızca bir kez)

Console penceresine şunu yazıp Enter'a basın ve kurulumun bitmesini bekleyin:

```r
install.packages("plotly")
```

Bu adımı her bilgisayarda yalnızca bir kez yapmanız yeterlidir.

## Adım 4. Boş bir R sayfası açın ve modeli çalıştırın

1. Üst menüden **File → New File...** yolunu izleyin ve çıkan listeden **R File** seçeneğini seçin (kısayolu **Ctrl + Shift + N**, Mac'te **Cmd + Shift + N**). Boş bir sayfa açılır.
2. Bu boş sayfaya aşağıdaki iki satırı yazın:

```r
scenario <- 0
source("model_2h.R", encoding = "UTF-8")
```

3. İki satırı fareyle seçin ve **Ctrl + Enter** (Mac'te **Cmd + Enter**) tuşlarına basın.

İlk satır hangi senaryonun çalışacağını söyler, ikinci satır modeli çalıştırır. `model_2h.R` dosyasını açmanıza gerek yoktur.

**Ne görmelisiniz?**

- Console penceresinde şuna benzer dört satır:

```
Senaryo 0
(C1) toplam net borç verme, en büyük değer     : ...
(C2) toplam net finansal servet, en büyük değer: ...
(C3) stok-akım bağı, en büyük fark             : ...
```

Üç noktaların yerinde sıfıra çok yakın sayılar (örneğin `1e-10` ya da daha küçük) görünür. Bu, modelin muhasebesinin tuttuğu, yani hiçbir paranın yoktan var olmadığı ve kaybolmadığı anlamına gelir.

- Sağ taraftaki **Plots** (ya da **Viewer**) penceresinde dokuz panelli bir şekil. Fareyi çizgilerin üzerine getirdiğinizde değerleri okuyabilirsiniz.

0 numaralı senaryoda hiçbir şok yoktur; bütün çizgiler düzdür. Bu, ekonominin durağan durumudur.

## Adım 5. Bir senaryo seçin

Başka bir senaryoyu çalıştırmak için yalnızca ilk satırdaki sayıyı değiştirin ve iki satırı yeniden çalıştırın:

```r
scenario <- 2
source("model_2h.R", encoding = "UTF-8")
```

Bütün şoklar 10. dönemde başlar ve kalıcıdır.

**`model_2h.R` dosyasındaki senaryolar**

| `scenario` | Deney |
|---|---|
| 0 | Şok yok (durağan durum) |
| 1 | Kamu harcaması 150'den 160'a çıkar |
| 2 | Kâr marjı 3/7'den 0.50'ye yükselir |
| 3 | Politika faizi 2 puan artar |
| 4 | Rantiyelerden işçilere yeniden dağıtım (rantiyelerin vergi oranı ve sosyal transferler artar) |

**`model_2h_cc.R` dosyasındaki senaryolar**

| `scenario` | Deney |
|---|---|
| 0 | Şok yok (durağan durum, sıfır enflasyon) |
| 1 | Kâr marjı 3/7'den 0.50'ye yükselir |
| 2 | İşçiler reel ücret hedefini 500 artırır |
| 3 | Reel kamu harcaması 150'den 160'a çıkar |

İkinci modeli çalıştırmak için dosya adını değiştirmeniz yeterlidir:

```r
scenario <- 1
source("model_2h_cc.R", encoding = "UTF-8")
```

## Adım 6. Şokun boyutunu değiştirin

Şimdi model dosyasının içine gireceksiniz.

1. Sol taraftaki dosya listesinde `model_2h.R` dosyasına tıklayarak açın.
2. `# ----- 2. Senaryo` başlıklı bölümü bulun. Orada şu satırları göreceksiniz:

```r
if (scenario == 1) G.exp[s] <- 160
if (scenario == 2) phi[s]   <- 0.50
if (scenario == 3) i.cb[s]  <- 0.045
if (scenario == 4) { tau.r[s] <- 0.25 ; G.wel[s] <- 14.8 + 18.4 }
```

Her satır bir senaryodur ve şöyle okunur: "senaryo 1 seçildiyse, kamu harcamasını 10. dönemden itibaren 160 yap".

3. Şokun boyutunu değiştirmek için satırın sonundaki sayıyı değiştirin. Örneğin kamu harcamasının 160 yerine 170'e çıkmasını istiyorsanız:

```r
if (scenario == 1) G.exp[s] <- 170
```

4. Dosyayı kaydedin (**Ctrl + S**). Bu adımı atlamayın: `source()` komutu dosyanın kaydedilmiş halini okur.
5. Boş sayfanıza dönün ve iki satırı yeniden çalıştırın:

```r
scenario <- 1
source("model_2h.R", encoding = "UTF-8")
```

Başlangıç değerlerini (örneğin kamu harcamasının 150 olduğunu) dosyanın en başındaki `# ----- 1. Parametreler` bölümünde görebilirsiniz.

## Adım 7. Yeni bir senaryo ekleyin

Kendi deneyinizi yapmak için `# ----- 2. Senaryo` bölümüne, mevcut senaryo satırlarının altına yeni bir satır ekleyin. Örneğin rantiyelerin otonom tüketimini 10. dönemden itibaren 24.44'ten 30'a çıkaran bir senaryo:

```r
if (scenario == 5) c0[s] <- 30
```

Dosyayı kaydedin, sonra boş sayfanızdan çağırın:

```r
scenario <- 5
source("model_2h.R", encoding = "UTF-8")
```

**Hangi parametrelere şok verebilirsiniz?** `1. Parametreler` bölümünde `rep(..., T)` ile tanımlanmış olanlara:

| Dosya | Şok verilebilen parametreler |
|---|---|
| `model_2h.R` | `tau.w`, `tau.r`, `c0`, `phi`, `i0`, `i.cb`, `i.B`, `G.exp`, `G.wel` |
| `model_2h_cc.R` | `tau.w`, `tau.r`, `c0`, `phi`, `i0`, `i.cb`, `i.B`, `g.exp`, `g.wel`, `pr`, `omega0`, `rho0` |

Tek bir sayı olarak tanımlanmış bir parametreye (örneğin `c1 <- 0.70`) şok vermek isterseniz önce onu aynı biçime çevirin: `c1 <- rep(0.70, T)`. Ardından model döngüsünde `c1` geçen yerleri `c1[t]` yapın.

**İki şoku birlikte vermek** için 4 numaralı senaryodaki gibi süslü parantez kullanın:

```r
if (scenario == 6) { G.exp[s] <- 160 ; i.cb[s] <- 0.045 }
```

**Şokun başladığı dönemi değiştirmek** için aynı bölümdeki `s <- 10:T` satırındaki 10'u değiştirin.

## Adım 8. Simülasyonu uzatın

Model varsayılan olarak 100 dönem çalışır. Ekonominin yeni durağan duruma tam olarak ulaştığını görmek isterseniz model dosyasının en başındaki şu satırda 100'ü büyütün (örneğin 500 yapın), dosyayı kaydedin ve modeli yeniden çalıştırın:

```r
T <- 100                       # dönem sayısı
```

---

## Sık karşılaşılan hatalar

| Gördüğünüz mesaj | Nedeni | Çözümü |
|---|---|---|
| `cannot open file 'model_2h.R': No such file or directory` | Çalışma klasörü yanlış. | Adım 2'yi yeniden yapın: klasörü **File → Open Folder...** ile açın. |
| Console penceresinde `>` işareti yok, kod çalışmıyor. | R kurulu değil ya da oturum başlamamış. | Adım 0'da R'ı kurduğunuzdan emin olun; Positron'u kapatıp yeniden açın. |
| `there is no package called 'plotly'` | Paket kurulmamış. | Adım 3'ü yapın. |
| `input string 1 is invalid UTF-8` | Dosya Türkçe karakterlerle doğru okunmamış. | Modeli `encoding = "UTF-8"` ile çağırdığınızdan emin olun. Positron dosyaları varsayılan olarak UTF-8 ile kaydeder; pencerenin sağ alt köşesinde `UTF-8` yazdığını denetleyin. Başka bir şey yazıyorsa üzerine tıklayıp **Save with Encoding → UTF-8** seçin. |
| `Model stok-akım tutarlı DEĞİL! Bir sızıntı mevcut!` | Modelde yaptığınız bir değişiklik muhasebeyi bozmuş. | Aşağıdaki bölüme bakın. |
| Senaryoyu değiştirdim ama şekil aynı. | Model dosyasını kaydetmemiş ya da iki satırı birlikte çalıştırmamış olabilirsiniz. | Dosyayı kaydedin (Ctrl + S), sonra `scenario <- ...` ve `source(...)` satırlarının ikisini de çalıştırın. |

## Stok-akım tutarlılığı sınamaları ne işe yarar?

Kod, simülasyondan sonra üç koşulu her dönem için hesaplar:

| Koşul | Anlamı |
|---|---|
| (C1) | Sektörlerin net borç vermesinin toplamı sıfırdır. |
| (C2) | Sektörlerin net finansal servetinin toplamı sıfırdır. |
| (C3) | Her sektörün net finansal serveti, tam olarak net borç vermesi kadar değişir. |

Yalnızca senaryo satırlarını değiştirdiğinizde bu sınamalar hep sağlanır. Ama modelin denklemlerini değiştirir ya da yeni bir denklem eklerseniz ve bir ödemenin karşılığını unutursanız kod "Model stok-akım tutarlı DEĞİL! Bir sızıntı mevcut!" mesajıyla durur. Bu bir arıza değil, bir uyarıdır: eklediğiniz her ödemenin bir ödeyeni ve bir alanı olmalıdır.

## Kodun yapısı

İki dosya da aynı yedi kısımdan oluşur:

1. Parametreler
2. Senaryo
3. Değişkenler
4. Başlangıç değerleri
5. Model (dönemler üzerinde dönen tek bir döngü)
6. Stok-akım tutarlılığı sınamaları
7. Şekiller

Her denklem, makaledeki numarasıyla etiketlenmiştir. Değişken adlarının makaledeki simgelerle karşılıkları makalenin ekindeki tabloda verilmiştir.

## Kaynak

Modelin ayrıntılı anlatımı, denklemleri, bilanço ve işlem akım matrisleri ile deneylerin yorumu şu çalışmadadır:

> Özden, Oktay. "İki Hanehalklı Stok-Akım Tutarlı (SFC) Bir Giriş Modeli." (yayın bilgisi eklenecek)

Modelin Birleşik Krallık ekonomisine uygulanmış ampirik sürümü:

> Özden, Oktay, ve Alp Erinç Yeldan. 2026. "A Two-Household Stock-Flow Consistent Model of the UK Economy: Distributional Consequences of Orthodox Disinflationary Policy and Alternatives." *Review of Political Economy*. https://doi.org/10.1080/09538259.2026.2691595

SFC modellemesi için temel başvuru kaynağı:

> Godley, Wynne, ve Marc Lavoie. 2007. *Monetary Economics: An Integrated Approach to Credit, Money, Income, Production and Wealth*. Palgrave Macmillan.

## Lisans

Kodlar MIT lisansı ile paylaşılmaktadır: kaynak göstererek serbestçe kullanabilir, değiştirebilir ve dağıtabilirsiniz.

## İletişim

Oktay Özden (iletişim bilgisi eklenecek)
