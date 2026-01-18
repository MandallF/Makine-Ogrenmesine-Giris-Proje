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
0 2016-01-01 05:00:00  0.932833         0.801527

1 2016-01-01 05:00:01  0.934333         0.801527

2 2016-01-01 05:00:02  0.931817         0.801527

3 2016-01-01 05:00:03  1.022050         0.801527

4 2016-01-01 05:00:04  1.139400         0.801527

Model eğitiliyor... (Bu işlem verinin boyutuna göre biraz sürebilir)

------------------------------

Model R2 Skoru: 0.5630

Hata Payı (MAE): 0.2851 kW

------------------------------

<img width="923" height="530" alt="image" src="https://github.com/user-attachments/assets/7603a07e-c5bb-4805-aa1f-7b2a4f23407a" />

------------------------------

AD-Soyad: Kubilay İnanç

Numara:22360859047

3.Sınıf BTÜ Bilgisayar Mühendisliği Öğrencisi

-Bu projeyi gerçekleştirmeden önce bana bilgi katan öğretmenime teşekkürler ve bu süreçte aldığım kurslar:

[Sıfırdan_İleri_Seviye_Python_Programlama_Sertifika.pdf](https://github.com/user-attachments/files/24696510/Sifirdan_Ileri_Seviye_Python_Programlama_Sertifika.pdf)


[Makine_Öğrenmesi_Sertifika.pdf](https://github.com/user-attachments/files/24696509/Makine_Ogrenmesi_Sertifika.pdf)


[Büyük_Dil_Modellerine_Giriş_Sertifika.pdf](https://github.com/user-attachments/files/24696508/Buyuk_Dil_Modellerine_Giris_Sertifika.pdf)


[Yapay_Zekaya_Giriş_Sertifika.pdf](https://github.com/user-attachments/files/24696507/Yapay_Zekaya_Giris_Sertifika.pdf)

