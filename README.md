#Smart Home Energy Consumption Prediction with Baseline Modeling

Bu proje, akıllı ev verilerini ve hava durumu bilgilerini kullanarak enerji tüketimini tahmin eden bir makine öğrenmesi modelidir. Projenin temel teknik yeniliği, Pivot Tabloları kullanılarak cihazların "normal" tüketim davranışlarının (Baseline) çıkarılması ve bunun modele bir referans noktası olarak öğretilmesidir.

#Projenin Amacı

Akıllı ev sistemlerinde enerji verimliliğini sağlamak için anlık tüketimi tahmin etmek kritiktir. Ancak sadece hava durumu verisi (sıcaklık, nem vb.) tek başına yeterli değildir. Ev halkının saatlik alışkanlıklarının da modele dahil edilmesi gerekir.

Yani günlük hayattan bir örnek ile kısaca anlatacak olursam şu soruya cevap veriyor:

"Bir evin o saatteki standart (baseline) tüketim alışkanlığını modele öğretirsek, tahmin başarısı ne kadar artar?"

#Veri Seti

Bu projede Kaggle üzerinde yayınlanan Smart Home Dataset with Weather Information kullanılmıştır.

Kaynak: Kaggle - Smart Home Energy Consumption

Veri İçeriği: Zaman damgası, enerji tüketimi (kW), sıcaklık, nem, basınç, rüzgar hızı vb.

#Kullanılan Yöntem ve Teknik Mimari

Proje 3 ana aşamadan oluşmaktadır:

1. Veri Ön İşleme (Preprocessing):

Karmaşık zaman damgaları (Unix Timestamp/String) işlenerek Hour (Saat) ve Day (Gün) özelliklerine ayrıştırıldı.

Hava durumu verileri ile enerji tüketim verileri entegre edildi.

2. Baseline Oluşturma (Pivot Table Tekniği):
   
Burası projenin en kritik adımı oldu. Modelin sadece anlık hava durumuna bakarak körleme tahmin yapmaması için Pivot Tablo kullanılarak tarihsel bir referans noktası oluşturuldu.

İşlemi anlatacak olursam, tüm veri seti üzerinde Hour (Saat) bazında gruplama yapıldı ve ortalama enerji tüketimi hesaplandı.

Sonuç olarak Hourly_Baseline adında yeni bir özellik (feature) türetildi.

Mantık ise günlük hayattan yine bir örenk olarak: Model, tahmin yaparken "Şu an saat 14:00, bu ev normalde saat 14:00'te X kadar enerji harcar" bilgisini girdi olarak aldı.

3. Modelleme (Random Forest Regressor):

Algoritma: Random Forest Regressor (Ensemble Learning) modeli kullanıldı.

Girdiler (Features): Temperature, Humidity, Pressure, WindSpeed, Hour, Day ve Hourly_Baseline girdilerini (öznitelik - X) kullandım.

Hedef (Target) ise yani sonuç olarak bulacağımız değer (hedef - Y) use [kW] (Gerçek Tüketim) bulundu.

#Kurulum ve Çalıştırma

A.Depoda bulunan .ipynb uzantılı MO_Proje isimli Jupyter Notebook dosyasını indirin ve colab üzerinden açın. Ardından ise çalışması için .rar uzantılı yüklediğim veri setini de indirip colab dosyaları üzerinden drive'ınıza yükleyiniz. Dosya yolunu da kontrol ettikten sonra artık web üzerinden kendiniz de çalıştırabilirsiniz.

B.Kendi bilgisayarınızda çalıştırmak isterseniz .py uzantılı MO_Proje adlı Python Script dosyasını indirip yine veri setini kendi bilgisayarınıza indirip dosya yolunu kontrol ettikten sonra kendi bilgisayarınızda çalışıtabilirsiniz.

#Sonuçlar ve Değerlendirme

Program çalıştıktan sonra,

1.Veri seti yükleniyor

2.Pivot işlemi gerçekleştirilerek bir Baseline oluşturuluyor. Bu sayede günün hangi saatinde ne kadar elektrik kullanılmış diye modele bilgi veriyoruz.

3.Ardından ise model eğitiliyor.

4. Son olarak sonucu tablo olarak elde ediyoruz. Hangi değişkenin tüketimi daha çok etkilediğine dair veriler ulaşıyoruz.

---Colab üzerinde şöyle bir çıktı oluşur:

Veri başarıyla yüklendi!

Pivot işlemi tamamlandı. Baseline özelliği eklendi.

                 time  use [kW]  Hourly_Baseline
0 2016-01-01 05:00:00  0.932833         0.926559

1 2016-01-01 05:00:01  0.934333         0.926559

2 2016-01-01 05:00:02  0.931817         0.926559

3 2016-01-01 05:00:03  1.022050         0.926559

4 2016-01-01 05:00:04  1.139400         0.926559

Model eğitiliyor... (Bu işlem verinin boyutuna göre biraz sürebilir)

------------------------------

Model R2 Skoru: 0.5567

Hata Payı (MAE): 0.3437 kW

------------------------------

<img width="919" height="533" alt="image" src="https://github.com/user-attachments/assets/10649f95-415e-4a6b-b15b-c0ed575762ec" />


Sonuçlarda da görüldüğü üzere Modelimiz, karmaşık insan davranışlarını ve hava durumu etkilerini %55 oranında başarıyla modellemiştir. Ortalama hata payımız 0.34 kW seviyesindedir. Bu, modelin genel tüketim trendlerini (sabah/akşam döngüsü, sıcaklık etkisi) başarıyla öğrendiğini, ancak gürültü değerlerinde zorlandığını göstermektedir.

Burada bunu yükseltebilmek amacıyla pivot işlemini alırken Baseline'ı sadece Saat (Hour) bazında aldık. Bunu Saat + Hafta Günü + ay kombinasyonuna çevirirsek Pivot daha akıllı olabilir diye düşünüyorum. 

Mantıken bu sayede model şöyle düşünebilir: 

"Saat 14:00" ortalamasına bakmak yerine; "Pazar günü Saat 14:00" ortalamasına bakarsa (çünkü pazar evde herkes vardır) ve yazın güneş daha uzun süreler kaldığı için daha az kullanım yapılır, Baseline çok daha hassas olur ve $R^2$ artar.

Yani sadece saat değil de saat + gün + ay üzerinden bir Baseline (pivot) oluşturup böyle modele eğitsek bu gürültü değerlerini de kaçırmaz diye düşünüyorum. Sonuç olarak:

Veri başarıyla yüklendi!

Zaman verileri dönüştürülüyor ve temizleniyor...

Mevsimsel Baseline eklendi.

Model eğitiliyor...

----------------------------------------

Final R2 Skoru: 0.5655

Final Hata Payı (MAE): 0.3433 kW

----------------------------------------

<img width="933" height="530" alt="image" src="https://github.com/user-attachments/assets/f32e0144-3f3a-4443-9f5e-d8a02e5d7884" />

Bu şekilde bir sonuç aldım ve görüldüğü üzere modelde bir gelişme gerçekleşti. Değiştirilmiş dosyalar: Depoda (saat+gün+ay) olarak adlandırdım.

------------------------------

AD-Soyad: Kubilay İnanç

Numara:22360859047

3.Sınıf BTÜ Bilgisayar Mühendisliği Öğrencisi

-Bu projeyi gerçekleştirmeden önce bana bilgi katan öğretmenime teşekkürler ve bu süreçte aldığım kurslar:

[Sıfırdan_İleri_Seviye_Python_Programlama_Sertifika.pdf](https://github.com/user-attachments/files/24696510/Sifirdan_Ileri_Seviye_Python_Programlama_Sertifika.pdf)


[Makine_Öğrenmesi_Sertifika.pdf](https://github.com/user-attachments/files/24696509/Makine_Ogrenmesi_Sertifika.pdf)


[Büyük_Dil_Modellerine_Giriş_Sertifika.pdf](https://github.com/user-attachments/files/24696508/Buyuk_Dil_Modellerine_Giris_Sertifika.pdf)


[Yapay_Zekaya_Giriş_Sertifika.pdf](https://github.com/user-attachments/files/24696507/Yapay_Zekaya_Giris_Sertifika.pdf)

