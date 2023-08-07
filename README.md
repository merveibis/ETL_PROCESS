# ETL_PROCESS
Veri işlemede kullanılan ETL süreçlerine dair örnek bir projenin detaylarına yer verilmiştir.

# **Extract Transform Load (ETL)**
Bu bölümde;
*   CSV ve JSON formatında dosya türlerini okuyacağız.
*   Bu formattaki dosyalardan data 'extract' layacağız :)
*   Çıkardığımız dataları gerekli formata 'transform' edeceğiz
*   Dönüştürdüğümüz dataları veri mühendislerinin veya ihtiyacı olan kullanıcılarımızın rahatlıkla bir RDBMS'e yüklemeye hazır hale getireceğiz ..

GO GO GO ->

Gerekli modülleri ve fonksiyonlarımızı projemize entegre edelim..

**glos Modülü**

Python'da glob fonksiyonu, belirli bir klasördeki dosya ve dizinleri belirli bir kalıp (pattern) kullanarak eşleştirmek için kullanılır. Bu fonksiyon, dosya ve dizin işlemleri yaparken belirli dosya türlerini veya isim düzenlemelerini filtrelemek veya listelemek için kullanışlıdır.

glob fonksiyonunun çalışma mantığı, bir klasördeki dosya ve dizinleri belirli bir kalıp ile eşleştirerek bu eşleşen öğeleri bir liste olarak döndürmesidir.

**pandas Modülü**

Pandas, Python programlama dilinde veri manipülasyonu ve analizi için kullanılan güçlü bir kütüphanedir. Pandas, veri işleme süreçlerini kolaylaştıran yüksek düzeyde yapılandırılmış veri yapıları ve veri analiz araçları sağlar. Temel olarak, pandas veri analisti ve veri bilimciler tarafından verilerin yüklenmesi, işlenmesi, temizlenmesi, dönüştürülmesi ve analiz edilmesi için kullanılır.

Pandas, veri dosyalarını (CSV, Excel, SQL vb.) yüklemek için kullanılabilir, veri setlerinde eksik verileri doldurmak veya çıkarmak için işlemler yapabilir, verileri sıralayabilir, filtreleyebilir, gruplayabilir ve aggrege edebilir, verileri birleştirebilir ve dönüştürebilir, verileri grafikler ve grafiklerle görselleştirebilir.

**ElementTree Modülü**

ElementTree modülü, Python'da XML (Extensible Markup Language) belgelerini işlemek ve analiz etmek için kullanılan bir kütüphanedir. XML, yapılandırılmış verileri taşımak için kullanılan bir işaretleme dilidir ve ağaç benzeri bir yapıda düzenlenmiş veriler içerir.

ElementTree modülü, XML belgelerini okumak, oluşturmak ve düzenlemek için bir dizi işlev ve sınıf içerir. Bu modül sayesinde, XML belgelerini bir Python programında veri yapısı olarak temsil edebilir ve bu belgeleri işlemek için gerekli işlemleri yapabilirsiniz.
