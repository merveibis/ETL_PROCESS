# **Extract Transform Load (ETL)**

Bu bölümde;
*   CSV ve JSON formatında dosya türlerini okuyacağız.
*   Bu formattaki dosyalardan data 'extract' layacağız :)
*   Çıkardığımız dataları gerekli formata 'transform' edeceğiz
*   Dönüştürdüğümüz dataları veri mühendislerinin veya ihtiyacı olan kullanıcılarımızın rahatlıkla bir RDBMS'e yüklemeye hazır hale getireceğiz ..


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

```bash
import glob                                                                     # bu modül dosyalarımızı seçmemize yardım eder
import pandas as pd                                                             # bu modül CSV dosyalarımızı işlememize yardım eder
import xml.etree.ElementTree as ET                                              # bu modül XML dosyalarımızı işmememize yardım eder.
from datetime import datetime
```


## Dosyalarımızı Yükleyelim
Bu komut, belirtilen URL'den bir dosya indirmek için wget komutunu kullanır. İndirilecek dosya, "source.zip" adlı bir zip dosyasıdır. İlgili URL, veri dosyasının bulunduğu konumu belirtir.

```bash
  !wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0221EN-SkillsNetwork/labs/module%206/Lab%20-%20Extract%20Transform%20Load/data/source.zip
```

## Dosyalarımızı Unzip'leyelim
Bu komut, belirtilen URL'den bir dosya indirmek için wget komutunu kullanır. İndirilecek dosya, "source.zip" adlı bir zip dosyasıdır. İlgili URL, veri dosyasının bulunduğu konumu belirtir.

```bash
!unzip source.zip
```

## Dosyalarımızı Kaydedeceğimiz Path'lerimizi Ayarlayalım
```bash
tmpfile    = "temp.tmp"                                                         # Çıkardığımız tüm verilerimizi kaydetmek için kullanacağımız dosya
logfile    = "logfile.txt"                                                      # Tüm log faaliyetlerimiz bu dosyada kaydedilecektir
targetfile = "transformed_data.csv"                                             # Dönüştürdüğümüz verilerimizin tutulacağı dosya
```

# Extract (Çıkarma)

*Minnik Not ▶*
DataFrame, pandas kütüphanesinde bulunan ve Python'da verileri tablo şeklinde düzenleyen ve manipüle etmeye olanak tanıyan bir veri yapısıdır. Dataframe, veri analizi ve veri işleme işlemlerinde sıklıkla kullanılır ve tablo benzeri bir yapı sunarak verilerin satırlar ve sütunlar şeklinde organize edilmesini sağlar.

Dataframe, tablo benzeri yapıya sahip iki boyutlu bir veri yapısıdır ve her bir sütun farklı bir veri türünü temsil edebilir. Veriler, bir veya birden fazla sütunda depolanabilir ve her bir satır, verilerin bir örneğini veya girdisini temsil eder.

## Öncelikle CSV formatındaki dosyalarımızı çıkaracağımız fonksiyonumuzu oluşturalım
```bash
def extract_from_csv(file_to_process):                                          # "file_to_process" parametresi işlem yapılacak CSV dosyasının adı veya yolunu alır.
    dataframe = pd.read_csv(file_to_process)                                    # Pandas kütüphanesindeki read_csv fonksiyonu,
                                                                                  # belirtilen CSV dosyasını okur ve içindeki verileri bir DataFrame'e dönüştürür.
    return dataframe                                                            # Oluşturulan DataFrame nesnesini fonksiyonun çağrıldığı yere döndürür
```

## Daha sonrasında JSON formatındaki dosyalarımızı çıkartmak için gerekli fonksiyonumuzu oluşturalım
```bash
def extract_from_json(file_to_process):                                         # "file_to_process" parametresi işlem yapılacak JSON dosyasının adı veya yolunu alır.
    dataframe = pd.read_json(file_to_process,lines=True)                        # Pandas kütüphanesindeki read_csv fonksiyonu,
                                                                                  # belirtilen JSON dosyasını okur ve içindeki verileri bir DataFrame'e dönüştürür.
                                                                                # 'lines=True' parametresi, JSON dosyasının satır satır okunmasını ve
                                                                                #her bir satırın ayrı bir veri noktası olarak kabul edilmesini sağlar.
    return dataframe                                                            # Oluşturulan DataFrame nesnesini fonksiyonun çağrıldığı yere döndürür
```

## Son olarak da XML formatındaki dosyalarımızı çıkartma işlemini gerçekleştirecek fonksiyonumuzu yazalım
```bash
def extract_from_xml(file_to_process):                                          # "file_to_process" parametresi işlem yapılacak XML dosyasının adı veya yolunu alır.
    dataframe = pd.DataFrame(columns=["name", "height", "weight"])              # Sütunları "name", "height" ve "weight" isimleriyle boş bir pandas DataFrame nesnesi oluşturulur
    tree = ET.parse(file_to_process)                                            # ElementTree modülündeki parse fonksiyonu kullanılarak XML dosyası ağaç yapısına çevrilir ve tree adlı bir değişkene atanır.
                                                                                  # XML dosyasının ağaç yapısı, ElementTree modülü tarafından temsil edilir ve verileri gezinmeye olanak tanır.
    root = tree.getroot()                                                       # XML ağacının kök düğümüne erişim sağlanır ve root adlı bir değişkene atanır.
                                                                                  # Kök düğüm, XML belgesinin en üst düzey düğümüdür ve verileri gezinmeye başlamak için temel düğüm olarak kullanılır.
    for person in root:                                                         # XML ağacındaki her "person" düğümü için bir döngü başlatır. Burada "person", XML dosyasındaki her "person" etiketini temsil eder.
        name = person.find("name").text                                         # Her "person" düğümünün altında bulunan "name" etiketini bulur ve içerdiği metni alarak "name" değişkenine atanır. find fonksiyonu, belirtilen etiketi bulmak için kullanılır.
        height = float(person.find("height").text)                              # Her "person" düğümünün altında bulunan "height" etiketini bulur ve içerdiği metni alarak float veri türüne dönüştürerek "height" değişkenine atanır.
        weight = float(person.find("weight").text)                              # Her "person" düğümünün altında bulunan "weight" etiketini bulur ve içerdiği metni alarak float veri türüne dönüştürerek "weight" değişkenine atanır.
        dataframe = dataframe.append({"name":name, "height":height,             # Her "person" düğümünden alınan "name", "height" ve "weight" değerleri bir satır olarak pandas DataFrame'e eklenir. append fonksiyonu, DataFrame'e yeni bir satır eklemek için kullanılır.
                                      "weight":weight}, ignore_index=True)        # ignore_index=True parametresi, satır eklenirken mevcut indeksin görmezden gelinmesini ve otomatik olarak yeni bir indeks oluşturulmasını sağlar.
    return dataframe                                                            # Oluşturulan DataFrame nesnesini fonksiyonun çağrıldığı yere döndürür
```

## Sıra geldi tüm dosya tiplerini çıkarma işlemimize ait fonksiyona;
```bash
def extract():
    extracted_data = pd.DataFrame(columns=['name','height','weight'])           # Çıkarılacak verilerimizi tutacağımız sütunları "name", "height" ve "weight" isimleriyle boş bir pandas DataFrame nesnesi oluşturulur

    #tüm csv dosyalarımızı işleyelim
    for csvfile in glob.glob("*.csv"):                                          # glob modülü kullanılarak tüm CSV dosyalarının isimlerini içeren bir döngü başlatılır.
        extracted_data = extracted_data.append(extract_from_csv(csvfile),       # "extract_from_csv" fonksiyonu kullanılarak her CSV dosyasındaki veriler çıkarılır ve bu veriler "extracted_data" DataFrame'ine eklenir.
                                               ignore_index=True)

    #tüm json dosyalarımızı işleyelim
    for jsonfile in glob.glob("*.json"):                                        # glob modülü kullanılarak tüm JSON dosyalarının isimlerini içeren bir döngü başlatılır.
        extracted_data = extracted_data.append(extract_from_json(jsonfile),     # "extract_from_json" fonksiyonu kullanılarak her JSON dosyasındaki veriler çıkarılır ve bu veriler "extracted_data" DataFrame'ine eklenir.
                                               ignore_index=True)

    #tüm xml dosyalarımızı işleyelim
    for xmlfile in glob.glob("*.xml"):                                          # glob modülü kullanılarak tüm XML dosyalarının isimlerini içeren bir döngü başlatılır.
        extracted_data = extracted_data.append(extract_from_xml(xmlfile),       # "extract_from_xml" fonksiyonu kullanılarak her XML dosyasındaki veriler çıkarılır ve bu veriler "extracted_data" DataFrame'ine eklenir.
                                               ignore_index=True)

    return extracted_data                                                       # Oluşturulan DataFrame nesnesini fonksiyonun çağrıldığı yere döndürür
```

Bu işlev, aynı klasörde bulunan farklı formatlardaki veri dosyalarını tek bir DataFrame'de birleştirmek için kullanılabilir ve verilerin daha kolay analiz edilmesini ve işlenmesini sağlayabilir.

# Transform (Dönüştürme)
Dönüştürme işlemi iki aşama içermektedir;

  *   inches olarak alınan height verileri metre'ye
  *   pounds olarak alınan weight verileri kilograma dönüştürülecektir.


```bash
def transform(data):
        data['height'] = round(data.height * 0.0254,2)                          # "height" sütunundaki verileri dönüştürülerek DataFrame'de güncellenir.
        data['weight'] = round(data.weight * 0.45359237,2)                      # "weight" sütunundaki verileri dönüştürülerek DataFrame'de güncellenir.
        return data                                                             # Dönüştürülmüş DataFrame'i fonksiyonun çağrıldığı yere döndürür.

```

# Loading (Yükleme)
Yazacağımız load fonksiyonu, verileri bir pandas DataFrame nesnesinden CSV dosyasına kaydetmek için kullanılır.
```bash
def load(targetfile,data_to_load):                                              #" targetfile" parametresi dosya adı/yolunu alırken "data_to_load" parametresi ise pandas DataFrame nesnesini alır
    data_to_load.to_csv(targetfile)                                             # "data_to_load" DataFrame nesnesindeki veriler, "targetfile" adında belirtilen CSV dosyasına kaydedilir.
                                                                                  # to_csv fonksiyonu, DataFrame'deki verileri CSV formatında bir dosyaya yazmak için kullanılır.
```

# Logging
Logging, bilgisayar programlarında çalışma süreci boyunca oluşan olayları ve bilgileri kaydetme işlemidir. Bu olaylar ve bilgiler, programın çalışması sırasında hataları, uyarıları, hata ayıklama bilgilerini ve diğer önemli bilgileri içerebilir. Loglar, programın nasıl çalıştığını ve hangi durumlarla karşılaşıldığını anlamak için kullanılır ve genellikle hata ayıklama ve sorun giderme süreçlerinde büyük öneme sahiptir.

Bu Python fonksiyonu, gelen mesajı ve mevcut zaman bilgisini alarak bir log dosyasına yazan bir işlevdir. Log dosyası, her log kaydının tarih-saat bilgisi ile mesajın kaydedildiği bir metin dosyasıdır.
```bash
def log(message):                                                               # "message" adında bir parametre Log dosyasına yazılacak olan mesajı alır
    timestamp_format = '%Y-%h-%d-%H:%M:%S'                                      # Log dosyasına yazılacak zaman bilgisinin formatı belirtilir. %Y yılın dört haneli formatını, %h ayın kısaltılmış adını, %d günü, %H saati, %M dakikayı ve %S saniyeyi temsil eder
    now = datetime.now()                                                        # "datetime" modülündeki now fonksiyonu kullanılarak mevcut zaman bilgisi alınır. datetime.now() fonksiyonu, sistem saatine göre mevcut tarih ve saati döndürür.
    timestamp = now.strftime(timestamp_format)                                  # Mevcut zaman bilgisi belirtilen timestamp_format formatına göre biçimlendirilir ve timestamp adlı bir değişkene atanır.
    with open("logfile.txt","a") as f:                                          # "logfile.txt" adlı bir metin dosyası açılır.
                                                                                  # Dosya, loglar için kullanılacak olan dosyadır. open fonksiyonu, dosyayı açmak ve işlem yapmak için kullanılır. "a" modu, dosyanın ekleme (append) modunda açılmasını sağlar.
                                                                                  # Yani, dosyaya yazma işlemi, dosyanın sonuna eklenerek yapılır ve mevcut içeriği korunur.
        f.write(timestamp + ',' + message + '\n')                               # Log mesajı ve zaman bilgisi log dosyasına yazılır. write fonksiyonu, verilen veriyi dosyaya yazmak için kullanılır.
```

*Dipnot ▶* "with open" bloğu kullanıldığında, dosya otomatik olarak kapanır, böylece açık dosya yönetimiyle ilgili sorunlar önlenir.

# ETL Sürecini Çalıştırma
ETL sürecimiz başlıyor..
```bash
log("ETL Job Started")
```

Extract sürecimiz işleniyor..
```bash
log("Extract phase Started")
extracted_data = extract()
log("Extract phase Ended")
extracted_data
```

Transform sürecimiz işleniyor..
```bash
log("Transform phase Started")
transformed_data = transform(extracted_data)
log("Transform phase Ended")
transformed_data
```

Load sürecimiz işleniyor..
```bash
log("Load phase Started")
load(targetfile,transformed_data)                                               # "load" fonksiyonu çağrılır ve "targetfile" adı verilen hedef CSV dosyasına "transformed_data" adı verilen bir DataFrame nesnesini yükler.
                                                                                  #"load" fonksiyonu, verileri bir CSV dosyasına yazma işlemini gerçekleştirir.
log("Load phase Ended")
```

ETL sürecimiz bitti.
```bash
log("ETL Job Ended")
```
