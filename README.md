# Banka pazarlamasında abonelik tahmini

> BIL545 · Veri Madenciliğinde İleri Konular · 2026–2027 Güz · Üsküdar Üniversitesi

## Grup

| Üye | Öğrenci no |
|264329400|Hakan Arik|
|264309004|Seyma Sena Unsal|
|264329002|Kerem Can Atlihan|


- **Grup adı:** Banka Pazarlamasinda Abonelik Tahmini
- **İletişim sorumlusu:** Kerem Can ATLIHAN / keremcan.atlihan@st.uskudar.edu.tr
- **Grup tipi:** 3 kisi

## Soru

Planlanmış telefon görüşmesi başlamadan önce bilinen bilgiler, müşterinin vadeli mevduata abone olmasını ne kadar öngörebilir? Amaç kampanya kayıtlarını sıralamaktır; aramanın nedensel kazancı ölçülmez.

## Veri

| Alan | Değer |
|---|---|
| Veri kümesi | UCI Bank Marketing · **bank-full.csv**, eski sürüm |
| Kaynak | https://archive.ics.uci.edu/dataset/222/bank+marketing |
| DOI / lisans | 10.24432/C5K306 / CC BY 4.0 |
| Boyut | 45.211 × 17; 16 girdi ve 1 hedef |
| Hedef | y=yes: abonelik, 5.289 kayıt (%11,70) |
| Dönem | Mayıs 2008–Kasım 2010; eski arşivdeki bank-names.txt |
| Dosya SHA256 | d1513ec63b385506f7cfce9f2c5caa9fe99e7ba4e8c3fa264b3aaf0f849ed32d |

**Verinin indirilmesi:** Veri depoya konmaz. UCI sayfasındaki Download ZIP'i indirin; içindeki `bank.zip` arşivinden **bank-full.csv** çıkarın. `bank-additional-full.csv` farklıdır ve bu defterin girdisi değildir. Colab dosyalar panelinden CSV'yi yükleyin; defterde yükleme istemi de vardır. Lisans atfını koruyun.

## Depo yapısı

```text
.
├── README.md
├── proje1/
│   └── BIL545_Proje1_Banka_Guncel.ipynb
└── proje2/
    └── [Proje 2 daha sonra eklenecek]
```

İndirilen defteri `proje1/` altına koyun. Grup adı netleşince dosya adı değiştirilebilir. `uretilen/` çalışma sırasında oluşur; CSV/ARFF/model dosyaları veri içerdiği için depoya eklemeyin. `.gitignore` içine `bank*.csv`, `uretilen/` ve `.ipynb_checkpoints/` ekleyin.

## Çalıştırma

1. https://colab.research.google.com/ → Dosya → Not defteri yükle ile IPYNB'yi açın. Depo bağlantısı oluşmadığından sahte Colab bağlantısı verilmedi.
2. `bank-full.csv` yükleyin; tüm hücreleri sırayla çalıştırın. Paket kurulumu ve WEKA indirmesi internet ister; CSV yeniden indirilmeksizin yüklenir.
3. Python ve bilimsel kütüphane sürümleri ilk hücrede yazdırılır; tohum 42. İlk çalıştırmada paket kurulumu zaman alabilir. Tuning ve CV çok sayıda eğitim içerir.
4. Java 17+ gerekir (Colab'da genellikle hazırdır). Yerelde mevcut WEKA kullanılır; yoksa Maven'den WEKA 3.8.6 ve iki temel bağımlılık indirilir. Maven çalıştırma yolu ayrıca kontrol edildi. Yerel ana doğrulama WEKA 3.8.7 ile yapıldı.
5. Sonuç dosyaları `uretilen/` altındadır: model, anlamsal temiz veri, bölme ve kat indeksleri, protokol, metrik tabloları, WEKA ARFF ve logları. Yalnız güvenilen kaynaklardan alınmış joblib dosyalarını yükleyin.
6. Tümünü çalıştırın. Çıktılı aynı defteri GitHub ve STIX'e yükleyin.

**Doğrulama kapsamı:** Kullanıcının sağladığı Colab defterinde 17 kod hücresi 1–17 sırasıyla tamamlanmış, kayıtlı hata çıktısı yok ve 12 WEKA kuralı kontrolü geçmiş. İnceleme kaydedilmiş çıktılara dayanır; Colab oturumu inceleyen tarafından yeniden çalıştırılmadı. Ortam: Python 3.13.18; numpy 2.4.6; pandas 2.2.3; sklearn 1.6.1; scipy 1.16.3. Tekrarlanan jupyter_client datetime uyarıları bazı çıktıları kalabalıklaştırıp ekran görünümünü kısaltmış; nihai tablolar ve kontroller kayıtlıdır.

## Sonuç özeti

### Proje 1 (vize)

Aşağıdaki sayılar kullanıcının Colab’da çalıştırıp indirdiği defterin kayıtlı çıktılarından alınmıştır. Yeni bir model çalıştırması yapılmadı. Aynı geliştirme katlarında 5 kat × 2 tekrar kullanılıyor.

| Model | CV AP ortalama ± std |
|---|---|
| Baseline | 0,0673 ± 0,0001 |
| Lojistik | 0,2132 ± 0,0100 |
| Karar ağacı | 0,1938 ± 0,0124 |
| k-NN | 0,1574 ± 0,0077 |
| Rastgele orman | 0,2306 ± 0,0107 |
| Histogram gradyan artırma | 0,2519 ± 0,0150 |

Seçilen model **Artirma**; seçim geliştirme CV AP'sine dayanır. ± kat standart sapmasıdır; güven aralığı değildir.

| İleri dönem modeli | AP | F1 (eşik 0,5) | Hata oranı |
|---|---|---|---|
| Artırma | 0,4812 | 0,1345 | 0,310185 |
| Orman | 0,4661 | 0,5057 | 0,338052 |

Geliştirmede pozitif oranı %6,73, ileri dönemde %31,58. AP dönemler arasında prevalanstan bağımsız değildir. Tek ileri dönem testi CV ortalama ± std yerine geçmez. AP'ye göre artırma tercih edilirken sabit 0,5 eşikte ormanın F1'i daha yüksektir.

**McNemar:** b=1359 (artırma yanlış, orman doğru); c=1611 (artırma doğru, orman yanlış); exact p=4,0594e-06. Artırma eksi orman hata oranı −0,0278669; artırmanın hata oranı yaklaşık **2,79 yüzde puan** daha düşük. Test AP farkını veya iş faydasını sınamaz. Önceki yerel koşudaki 8,49 yüzde puanlık fark bu Colab koşusunun sonucu değildir.

**Arama:** GridSearch best_score_=0,192953; nested dış AP=0,207073 ± 0,016183. Fark −0,014120; her tek koşuda pozitif optimist fark beklenmez.

**Sızıntı ve kodlama:** doğru Pipeline AP 0,213244 ± 0,010024; tüm geliştirmede öğrenilen yanlış ön işleme 0,213278 ± 0,009864. Kat dışı hedef kodlama 0,213406 ± 0,010623; yanlış hedef kodlama 0,213357 ± 0,010997. Küçük fark yanlış yöntemi geçerli yapmaz.

**Küp:** İncelenen konut kredisi/yaş katmanlarında Simpson tersine dönüşü yok. **Kurallar:** 12 kısa pozitif kuralın tüm öncül kümeleri, öncül ve ortak sayımları WEKA/Python arasında eşleşmiş. Minimum destek 0,002 ve güven 0,20; keşifsel eşik değişikliği bağımsız doğrulama değildir. Kontrollü CART kıyasında aynı derinlikte ağırlık etkisi incelenir; J48 farkı tek nedene bağlanmaz.

**Permütasyon öneminde ilk beş:** onceki_sonuc, ay, yas, gun, bakiye. Yerel koşuya göre gun/bakiye sırası değişmiş, ilk beşin kümesi aynı. Önem test kümesindeki keşifsel açıklamadır; nedensel etki veya yeni bağımsız doğrulama değildir.

### Proje 2 (final)

Henüz yapılmadı. Model, anlamsal veri, CV katları ve protokol aktarılabilir. Model yalnız geliştirmede fit edilmiştir. Temiz CSV imputasyon/ölçekleme içermez; Proje 2'de yine eğitim içinde öğrenilmelidir. Süre/etiket gibi sütunları yanlışlıkla girdi yapmayın. Öğretim üyesinin geri bildirimi ve kümeleme/aykırılık/adillik bulguları sonra eklenecek.

**Sınırlılıklar:** Müşteri kimliği ve tam tarih yok; kişi bazlı bölme yapılamaz. Zaman kayması büyük, kayıtlar eski ve tek bankadandır. Kurallar ve ileri dönemde ölçülen permütasyon önemi açıklama amaçlıdır; seçim için kullanılmaz ve keşifseldir. Performans, nedensellik veya adillik kanıtı değildir.

## Katkı beyanı

|264329400|Hakan Arik|1.Kisim
|264309004|Seyma Sena Unsal|2.Kisim
|264329002|Kerem Can Atlihan|Son Kisim




| Kod | Sağlanan dosya | İlgili slayt/sayfa | Projedeki karşılığı |
|---|---|---|---|
| [D1] | BIL545_Hafta01_Giris_v6.html | slayt 9, 15, 18, 26–30, 33 | KDD, araç rolleri, baseline ve çoklu deneme |
| [D2] | BIL545_Hafta02_Veri_v6.pdf | s.6–16, 30, 34, 38 | Ölçek türü, betimleme, medyan/IQR, künye |
| [D3] | BIL545_Hafta03_On_Isleme_v3.html | slayt 9–16, 25, 28–37 | Eksiklik, doldurma, kodlama, ölçekleme ve Pipeline |
| [D4] | BIL545_Hafta04_Veri_Ambari_v2.0.pdf | s.7, 10, 12, 17, 21, 23, 25, 27 | Olgu/boyut/hiyerarşi, ölçü türü, OLAP ve Simpson |
| [D5] | BIL545_Hafta05_Birliktelik_v2.pdf | s.11, 27, 30–32 | Destek sayısını eşitleme, Seçenek B, lift/Kulczynski/IR |
| [D6] | BIL545_Hafta06_Siniflandirma.pdf | s.7, 9, 14, 18, 26–30, 35 | J48/CART, k-NN, dengeli doğruluk, ortak katlar |
| [D7] | BIL545_Hafta07_Siniflandirma2.pdf | s.8, 12–13, 17, 19, 23–24, 30–45, 50–51 | Topluluklar, eğitim dışı önem, eşik/maliyet, nested CV, McNemar ve teslim kontrolü |

**Bu sürümde değişenler:** Denetim tablosunda ölçüm ölçeği ve yalnız sayısal sıfır oranı; geliştirme betimlemesi/doldurma bedeli; dayanıklı ölçekleme duyarlılığı; ikincil dengeli doğruluk/duyarlılık/kesinlik; slice/dice ve ağırlıklı roll-up kontrolü; ileri dönemde permütasyon önemi; exact McNemar ile dersin χ² yaklaşımının karşılaştırması. Ek analizlerle ana model/parametre/eşik yeniden seçilmedi.


## Kaynaklar

- UCI Bank Marketing, https://doi.org/10.24432/C5K306, CC BY 4.0; bank-names.txt.
- Moro, Laureano ve Cortez (2011), Using Data Mining for Bank Direct Marketing: An Application of the CRISP-DM Methodology, ESM 2011, 117–121, http://hdl.handle.net/1822/14838.
- Kaufman vd. (2012), Leakage in data mining, https://doi.org/10.1145/2382577.2382579.
- Dietterich (1998), Approximate statistical tests, https://doi.org/10.1162/089976698300017197.
- Wu, Chen ve Han (2010), Re-examination of interestingness measures, https://doi.org/10.1007/s10618-009-0161-2.
- Han, Kamber ve Pei, Data Mining: Concepts and Techniques, 3. baskı (2012); ilgili bölümler ders eşleme tablosunda.
- scikit-learn TargetEncoder: https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.TargetEncoder.html
- WEKA Apriori: https://weka.sourceforge.io/doc.dev/weka/associations/Apriori.html

- [D1]–[D7]: Dr. Selma Güzel, BIL545, ders içerikleri


