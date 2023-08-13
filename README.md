
# **Extract Transform Load (ETL)**

Bu projede;
*   CSV, JSON ve XML formatında dosya türlerini okuyacağız.
*   Bu formattaki dosyalardan data 'extract' layacağız :)
*   Çıkardığımız dataları gerekli formata 'transform' edeceğiz
*   Dönüştürdüğümüz dataları veri mühendislerinin veya ihtiyacı olan kullanıcılarımızın rahatlıkla bir RDBMS'e yüklemeye hazır hale getireceğiz ..

NOT : Proje çalışmaları Google Colab platformu üzerinde gerçekleştirilmiştir. Python projesini local bilgisayarınızda çalıştırmak için gerekli ayarların yapıldığından ve kütüphanelerin yüklendiğinden emin olunuz !

Gerekli modülleri ve fonksiyonlarımızı projemize entegre edelim..

```bash
import glob                                                                     
import pandas as pd                                                             
import xml.etree.ElementTree as ET                                              
from datetime import datetime
```

**glob Modülü**

 Python'da glob fonksiyonu, belirli bir klasördeki dosya ve dizinleri belirli bir kalıp (pattern) kullanarak eşleştirmek için kullanılır. Bu fonksiyon, dosya ve dizin işlemleri yaparken belirli dosya türlerini veya isim düzenlemelerini filtrelemek veya listelemek için kullanışlıdır.

 glob fonksiyonunun çalışma mantığı, bir klasördeki dosya ve dizinleri belirli bir kalıp ile eşleştirerek bu eşleşen öğeleri bir liste olarak döndürmesidir.


 **pandas Modülü**

Pandas, Python programlama dilinde veri manipülasyonu ve analizi için kullanılan güçlü bir kütüphanedir. Pandas, veri işleme süreçlerini kolaylaştıran yüksek düzeyde yapılandırılmış veri yapıları ve veri analiz araçları sağlar. Temel olarak, pandas veri analisti ve veri bilimciler tarafından verilerin yüklenmesi, işlenmesi, temizlenmesi, dönüştürülmesi ve analiz edilmesi için kullanılır.

Pandas, veri dosyalarını (CSV, Excel, SQL vb.) yüklemek için kullanılabilir, veri setlerinde eksik verileri doldurmak veya çıkarmak için işlemler yapabilir, verileri sıralayabilir, filtreleyebilir, gruplayabilir ve aggrege edebilir, verileri birleştirebilir ve dönüştürebilir, verileri grafikler ve grafiklerle görselleştirebilir.

**ElementTree Modülü**

ElementTree modülü, Python'da XML (Extensible Markup Language) belgelerini işlemek ve analiz etmek için kullanılan bir kütüphanedir. XML, yapılandırılmış verileri taşımak için kullanılan bir işaretleme dilidir ve ağaç benzeri bir yapıda düzenlenmiş veriler içerir.

ElementTree modülü, XML belgelerini okumak, oluşturmak ve düzenlemek için bir dizi işlev ve sınıf içerir. Bu modül sayesinde, XML belgelerini bir Python programında veri yapısı olarak temsil edebilir ve bu belgeleri işlemek için gerekli işlemleri yapabilirsiniz.


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
tmpfile    = "temp.tmp"                                                        
logfile    = "logfile.txt"                                                     
targetfile = "transformed_data.csv"                                           
```

# Extract (Çıkarma)

*Minnik Not ▶*
DataFrame, pandas kütüphanesinde bulunan ve Python'da verileri tablo şeklinde düzenleyen ve manipüle etmeye olanak tanıyan bir veri yapısıdır. Dataframe, veri analizi ve veri işleme işlemlerinde sıklıkla kullanılır ve tablo benzeri bir yapı sunarak verilerin satırlar ve sütunlar şeklinde organize edilmesini sağlar.

Dataframe, tablo benzeri yapıya sahip iki boyutlu bir veri yapısıdır ve her bir sütun farklı bir veri türünü temsil edebilir. Veriler, bir veya birden fazla sütunda depolanabilir ve her bir satır, verilerin bir örneğini veya girdisini temsil eder.

## Öncelikle CSV formatındaki dosyalarımızı çıkaracağımız fonksiyonumuzu oluşturalım
```bash
def extract_from_csv(file_to_process):                                         
    dataframe = pd.read_csv(file_to_process)                                   
    return dataframe                                                           
```

## Daha sonrasında JSON formatındaki dosyalarımızı çıkartmak için gerekli fonksiyonumuzu oluşturalım
```bash
def extract_from_json(file_to_process):                                         
    dataframe = pd.read_json(file_to_process,lines=True)                       
    return dataframe                                                            
```

## Son olarak da XML formatındaki dosyalarımızı çıkartma işlemini gerçekleştirecek fonksiyonumuzu yazalım
```bash
def extract_from_xml(file_to_process):                                          
    dataframe = pd.DataFrame(columns=["name", "height", "weight"])              
    tree = ET.parse(file_to_process)                                            
    root = tree.getroot()                                                      
    for person in root:                                                        
        name = person.find("name").text                                         
        height = float(person.find("height").text)                              
        weight = float(person.find("weight").text)                              
        dataframe = dataframe.append({"name":name, "height":height,"weight":weight}, ignore_index=True)        
    return dataframe                                                            
```

## Sıra geldi tüm dosya tiplerini çıkarma işlemimize ait fonksiyona;
```bash
def extract():
    extracted_data = pd.DataFrame(columns=['name','height','weight'])           

    #tüm csv dosyalarımızı işleyelim
    for csvfile in glob.glob("*.csv"):                                          
        extracted_data = extracted_data.append(extract_from_csv(csvfile),ignore_index=True)

    #tüm json dosyalarımızı işleyelim
    for jsonfile in glob.glob("*.json"):                                        
        extracted_data = extracted_data.append(extract_from_json(jsonfile), ignore_index=True)

    #tüm xml dosyalarımızı işleyelim
    for xmlfile in glob.glob("*.xml"):                                          
        extracted_data = extracted_data.append(extract_from_xml(xmlfile), ignore_index=True)

    return extracted_data                                                       
```

Bu işlev, aynı klasörde bulunan farklı formatlardaki veri dosyalarını tek bir DataFrame'de birleştirmek için kullanılabilir ve verilerin daha kolay analiz edilmesini ve işlenmesini sağlayabilir.

# Transform (Dönüştürme)
Dönüştürme işlemi iki aşama içermektedir;

  *   inches olarak alınan height verileri metre'ye
  *   pounds olarak alınan weight verileri kilograma dönüştürülecektir.


```bash
def transform(data):
        data['height'] = round(data.height * 0.0254,2)                          
        data['weight'] = round(data.weight * 0.45359237,2)                      
        return data                                                             
```

# Loading (Yükleme)
Yazacağımız load fonksiyonu, verileri bir pandas DataFrame nesnesinden CSV dosyasına kaydetmek için kullanılır.
```bash
def load(targetfile,data_to_load):                                              
    data_to_load.to_csv(targetfile)                                             
```

# Logging
Logging, bilgisayar programlarında çalışma süreci boyunca oluşan olayları ve bilgileri kaydetme işlemidir. Bu olaylar ve bilgiler, programın çalışması sırasında hataları, uyarıları, hata ayıklama bilgilerini ve diğer önemli bilgileri içerebilir. Loglar, programın nasıl çalıştığını ve hangi durumlarla karşılaşıldığını anlamak için kullanılır ve genellikle hata ayıklama ve sorun giderme süreçlerinde büyük öneme sahiptir.

Bu Python fonksiyonu, gelen mesajı ve mevcut zaman bilgisini alarak bir log dosyasına yazan bir işlevdir. Log dosyası, her log kaydının tarih-saat bilgisi ile mesajın kaydedildiği bir metin dosyasıdır.
```bash
def log(message):                                                               
    timestamp_format = '%Y-%h-%d-%H:%M:%S'                                      
    now = datetime.now()                                                        
    timestamp = now.strftime(timestamp_format)                                  
    with open("logfile.txt","a") as f:                                          
        f.write(timestamp + ',' + message + '\n')                               
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
load(targetfile,transformed_data)                                               
log("Load phase Ended")
```

ETL sürecimiz bitti.
```bash
log("ETL Job Ended")
```
