# Banka Pazarlamasında Abonelik Tahmini

> BIL545 · Veri Madenciliğinde İleri Konular · 2026–2027 Güz · Üsküdar Üniversitesi

## Proje ekibi

| Öğrenci | Öğrenci no | Proje 1 sorumluluğu |
|---|---|---|
| Hakan Arik | 264329400 | Bölüm 1 – Veri, ön işleme ve veri sızıntısı |
| Şeyma Sena Ünsal | 264309004 | Bölüm 2 – Sınıflandırma |
| Kerem Can Atlıhan | 264329002 | Bölüm 3 – Veri küpü ve birliktelik analizi |

**Grup adı:** Banka Pazarlamasında Abonelik Tahmini  
**Grup tipi:** 3 kişi  
**İletişim sorumlusu:** Kerem Can Atlıhan — keremcan.atlihan@st.uskudar.edu.tr

## Projenin amacı

Telefon görüşmesi başlamadan önce bilinen bilgilerle müşterinin vadeli mevduata abone olma eğilimini tahmin etmek; veri kalitesi ve ön işleme kararlarının model başarımına etkisini incelemek; elde edilen örüntüleri veri küpü ve birliktelik kurallarıyla açıklamak. Amaç kampanya kayıtlarını sıralamaktır; aramanın nedensel etkisi ölçülmez.

## Veri kümesi ve değerlendirme protokolü

| Özellik | Değer |
|---|---|
| Veri kümesi | UCI Bank Marketing – `bank-full.csv` |
| Kaynak | [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/222/bank+marketing) |
| Lisans ve DOI | CC BY 4.0 · [10.24432/C5K306](https://doi.org/10.24432/C5K306) |
| Boyut | 45.211 kayıt × 17 sütun |
| Hedef | `y=yes` (vadeli mevduat aboneliği): 5.289 kayıt (%11,70) |
| Geliştirme verisi | İlk 36.168 kayıt (%80); pozitif oranı %6,73 |
| İleri dönem testi | Son 9.043 kayıt (%20); pozitif oranı %31,58 |
| Girdi sayısı | 15; `duration` ve hedef değişkeni model girdisinden çıkarılmıştır. |
| Değerlendirme | Aynı 5 kat × 2 tekrar katmanlı CV; birincil ölçüt Average Precision (AP), tohum 42 |

Ham dosyanın doğrulanmış SHA-256 değeri:

```text
d1513ec63b385506f7cfce9f2c5caa9fe99e7ba4e8c3fa264b3aaf0f849ed32d
```

## Proje 1 — Nihai Colab notebook'u

Üç bölüm, tek bir çalıştırılabilir ve çıktı içeren notebook'ta birleştirilmiştir:

- [BIL545_Proje1_Banka_Nihai.ipynb](proje1/BIL545_Proje1_Banka_Nihai.ipynb)
- [Google Colab'da aç](https://colab.research.google.com/github/KeremATLIHAN/Veri_Bilimleri_Vize/blob/main/proje1/BIL545_Proje1_Banka_Nihai.ipynb)

### Bölüm 1 — Veri, ön işleme ve veri sızıntısı (4.1)

- Veri künyesi, keşifsel veri analizi, öznitelik kalitesi ve ölçüm ölçekleri.
- Özel eksiklik kodları, eksiklik desenleri ve mekanizmaları; eksiklik göstergeleri.
- Standart ve nadir kategori gruplamalı One-Hot Encoding, kat dışı Target Encoding ve sızıntı deneyleri.
- Pipeline içinde doğru ön işleme, baseline ve WEKA ZeroR/J48 kontrolü.

| Deney | Geliştirme CV AP |
|---|---:|
| Eksiklik göstergesiz lojistik | 0,2155 |
| Eksiklik göstergeli lojistik | 0,2197 |
| Standart One-Hot | 0,2265 |
| Nadir kategorili One-Hot | 0,2267 |
| Kat dışı Target Encoding | 0,2211 |
| Hatalı Target Encoding | 0,2247 |

Doğru Pipeline ile CV dışında öğrenilen hatalı ön işlemenin AP değerleri sırasıyla 0,2197 ve 0,2196'dır. Küçük veya negatif fark, hatalı yöntemi geçerli kılmaz. Target Encoding kıyasında day sütununa ölçekleme uygulanması da değiştiğinden fark yalnızca sızıntının saf etkisi olarak yorumlanmaz.

### Bölüm 2 — Sınıflandırma (4.2)

Altı yöntem ortak geliştirme katlarında değerlendirilmiştir. Nihai seçim ortalama AP'ye dayanır. Aşağıdaki değerler 19 Kasım 2026 tarihli bütünleşik Colab çalıştırmasının kayıtlı çıktılarındandır; standart sapma kat skorlarının örneklem standart sapmasıdır, güven aralığı değildir.

| Model | CV AP ortalama ± std |
|---|---:|
| DummyClassifier (Baseline) | 0,067269 ± 0,000067 |
| Lojistik regresyon | 0,219723 ± 0,010474 |
| Karar ağacı | 0,193650 ± 0,012081 |
| k-NN | 0,159669 ± 0,008172 |
| Rastgele orman | 0,231818 ± 0,008249 |
| **Histogram gradyan artırma** | **0,252247 ± 0,015036** |

**İleri dönem sonuçları** (0,5 karar eşiği):

| Model | AP | F1 | Hata oranı |
|---|---:|---:|---:|
| Histogram gradyan artırma | 0,479447 | 0,175664 | 0,312396 |
| Rastgele orman | 0,463742 | 0,484186 | 0,339047 |

Artırma AP bakımından tercih edilmiştir; sabit 0,5 eşiğinde Orman'ın F1'i daha yüksektir. Eşik ve kalibrasyon optimizasyonu yapılmamıştır.

- **McNemar:** b=1.156, c=1.397, exact p≈0,000002. Artırma'nın hata oranı 2,665 yüzde puan daha düşüktür. Test AP farkını ölçmez.
- **Dietterich 5×2cv:** AP farkı p=0,292537; 0,5 eşiğindeki hata farkı p=0,000332. AP uygulaması keşifseldir; anlamlı olmayan sonuç eşdeğerlik kanıtı değildir.
- **GridSearch:** 3 kat iç skor 0,193372; iç içe dış AP 0,206689 ± 0,016378. Aynı dış katlarla doğrudan arama AP'si 0,206689; gözlenen fark yuvarlatıldığında sıfırdır. Bu bulgu iyimserlik yanlılığının genel olarak bulunmadığını göstermez.

### Bölüm 3 — Veri küpü ve birliktelik analizi (4.3)

- Yaş, konut kredisi ve eğitim boyutlarıyla veri küpü; roll-up, drill-down, slice ve dice işlemleri.
- Simpson paradoksu, Wilson ve Newcombe %95 aralıkları.
- Seçenek B kapsamında Apriori, destek duyarlılığı, güven, lift, Kulczynski ve dengesizlik oranı (IR).
- Permütasyon önemleri ve WEKA/Python karşılaştırması.

| Konut kredisi | Kayıt | Abonelik oranı |
|---|---:|---:|
| Yok | 15.311 | %8,36 |
| Var | 20.857 | %5,53 |

Genel fark (var − yok) −2,83 yüzde puandır; %95 Newcombe aralığı [−3,37; −2,30]. Yaş ve eğitim katmanlarında sistematik tersine dönüş gözlenmediğinden incelenen boyutlarda Simpson paradoksu tespit edilmemiştir.

Minimum destek 0,002 ve güven 0,20 ile hedefi abonelik olan, bir veya iki öncüllü 12 kural elde edilmiştir; bunlar 10 farklı kayıt kümesini temsil eder.

| Örnek kural | Güven | Lift | Ortak kayıt |
|---|---:|---:|---:|
| Mart + kampanya dilim 1 | %50,95 | 7,57 | 107 |
| Nisan + konut kredisi yok | %39,09 | 5,81 | 274 |

En yüksek güven ve lift Mart + kampanya dilim 1 profilinde görülür; Nisan + konut kredisi yok profili daha geniş gözlenen olumlu kayıt kapsamına sahiptir. 41 tek öncüllü kuralda lift–Kulczynski sıra korelasyonu 0,35'tir. Bu sonuç, kural gücünün bir tek ölçütle değerlendirilmemesi gerektiğini destekler. WEKA ve Python, 12 kısa pozitif kuralın öncül kümeleri ile öncül ve ortak kayıt sayılarını birebir doğrulamıştır.

Permütasyon öneminde ilk beş öznitelik: önceki kampanya sonucu, ay, yaş, bakiye ve gün. Bu sıralama bir nedensellik kanıtı değildir.

## Çalıştırma ve yeniden üretilebilirlik

1. Nihai notebook'u Google Colab'da açın.
2. Gerekirse UCI Bank Marketing ZIP arşivinden `bank.zip` içindeki `bank-full.csv` dosyasını çıkararak Colab'a yükleyin. `bank-additional-full.csv` kullanılmamalıdır.
3. **Çalışma zamanı → Tümünü çalıştır** seçeneğini kullanın. Paket, veri ve WEKA indirmeleri internet erişimi gerektirir.
4. Çalıştırma sırasında `uretilen/` altında oluşturulan CSV, ARFF, model, kat indeksleri, deney tabloları ve protokol dosyalarını ham veri veya geçici çıktılarla birlikte depoya yüklemeyin. Dış kaynaklı güvenilmeyen joblib dosyalarını kullanmayın.

**Doğrulanan ortam:** Python 3.13.18, NumPy 2.4.6, pandas 2.2.3, SciPy 1.16.3, scikit-learn 1.6.1, Matplotlib 3.10.0, Java 21 ve WEKA 3.8.7. Kayıtlı nihai Colab çıktısında 64 kod hücresi baştan sona çalışmış ve hata çıktısı görülmemiştir. Ham veri SHA-256 kontrolü, ön işleme eşdeğerliği ve WEKA/Python kural sayımları başarılıdır.

## GitHub çalışma düzeni

Üç üyenin ayrı bölüm çalışmaları aşağıdaki branch'lerde ve birleştirme geçmişinde korunmaktadır. Nihai teslim için yukarıda bağlantısı verilen tek bütünleşik notebook esas alınır.

| Branch | Sorumluluk | Pull Request |
|---|---|---|
| `hakanarik` | Bölüm 1 | [#1](https://github.com/KeremATLIHAN/Veri_Bilimleri_Vize/pull/1) |
| `seymasena` | Bölüm 2 | [#2](https://github.com/KeremATLIHAN/Veri_Bilimleri_Vize/pull/2) |
| `keremcanatlihan` | Bölüm 3 | [#3](https://github.com/KeremATLIHAN/Veri_Bilimleri_Vize/pull/3) |

## Sınırlılıklar ve Proje 2

Veriler tek bir bankanın geçmiş kayıtlarına dayanmaktadır; müşteri kimliği ve tam tarih bulunmadığından kişi bazlı bölme ve bağımlılık kontrolü yapılamamaktadır. Geliştirme ve ileri dönem prevalans farkı AP kıyaslarının yorumunu sınırlar. Birliktelik eşikleri keşifseldir, güven aralıkları kural seçiminin iyimserliğini kapsamaz ve ileri dönem öznitelik önemleri nedensel etki göstermez.

Proje 2 kapsamında önceki deney protokolü, model ve anlamsal temiz veri aktarılabilir; kümeleme, aykırılık, kalibrasyon, karar eşiği ve adillik değerlendirmeleri henüz yapılmamıştır.

## Kaynaklar ve yapay zekâ kullanımı

- [UCI Bank Marketing veri kümesi ve lisans bilgisi](https://doi.org/10.24432/C5K306).
- Moro, Laureano ve Cortez (2011). *Using Data Mining for Bank Direct Marketing: An Application of the CRISP-DM Methodology*.
- [Kaufman vd. (2012), Leakage in Data Mining](https://doi.org/10.1145/2382577.2382579).
- [Dietterich (1998), Approximate Statistical Tests for Comparing Supervised Classification Learning Algorithms](https://doi.org/10.1162/089976698300017197).
- [Wu, Chen ve Han (2010), Re-examination of Interestingness Measures in Pattern Mining](https://doi.org/10.1007/s10618-009-0161-2).
- Han, Kamber ve Pei (2012). *Data Mining: Concepts and Techniques*, 3. baskı.
- [Scikit-learn](https://scikit-learn.org/) ve [WEKA](https://waikato.github.io/weka-wiki/) dokümantasyonu.

Üyelerin bölüm sorumlulukları ve mevcut bireysel katkı/yapay zekâ kullanım beyanları notebook'ta yer almaktadır.
