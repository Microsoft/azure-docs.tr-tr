---
title: SQL Server VM ekip veri bilimi Işleminde model oluşturma ve dağıtma
description: Genel kullanıma açık bir veri kümesiyle Azure VM üzerinde SQL Server kullanarak Machine Learning modeli oluşturun ve dağıtın.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 148d0c203248e4dcde5baaadc596d56e8b8ea17a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669388"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Ekip veri bilimi Işlemi işlem içinde: SQL Server kullanma
Bu öğreticide, SQL Server ve genel kullanıma açık bir veri kümesi ( [NYC TAXI gezileri](https://www.andresmh.com/nyctaxitrips/) veri kümesi) kullanarak makine öğrenimi modeli oluşturma ve dağıtma sürecini adım adım ilerleyebilirsiniz. Yordam standart bir veri bilimi iş akışını izler: verileri alma ve araştırma, eğitim kolaylaştırmak ve bir model derlemek ve dağıtmak için mühendislik özellikleri.

## <a name="dataset"></a>NYC TAXI gidiş veri kümesi açıklaması
NYC TAXI seyahat verileri yaklaşık 173.000.000 ' den fazla gidiş ve her seyahat için ödenen farktan oluşan 20 GB sıkıştırılmış CSV dosyası (~ 48GB sıkıştırılmamış) ile ilgilidir. Her seyahat kaydı, toplama ve bırakma konumunu ve saatini, anonimleştirilmiş Hack (sürücü) lisans numarasını ve medalon (TAXI 'nin benzersiz kimliği) numarasını içerir. Veriler, 2013 yılında yapılan tüm döngüleri kapsamakta ve her ay için aşağıdaki iki veri kümelerinde sunulmaktadır:

1. ' Trip_data ' CSV, pascuların sayısı, toplama ve Dropoff noktaları, seyahat süresi ve seyahat uzunluğu gibi seyahat ayrıntılarını içerir. Aşağıda birkaç örnek kayıt verilmiştir:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. ' Trip_fare ' CSV, ödeme türü, tarifeli havayolu tutarı, ek ücret, vergiler, ipuçları ve Tolls ve ödenen toplam miktar gibi her seyahat için ödenen tarifeli havayolu 'ın ayrıntılarını içerir. Aşağıda birkaç örnek kayıt verilmiştir:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Seyahat\_veri ve seyahat\_tarifeli havayolu katılacak benzersiz anahtar, şu alanlardan oluşur: medtalon, Hack\_lisans ve toplama\_tarih saat.

## <a name="mltasks"></a>Tahmin görevlerinin örnekleri
*İpucu\_miktarına*göre üç tahmin sorunu ifade edeceğiz, yani:

1. İkili sınıflandırma: bir tıp 'nin seyahat için ödenip ödenmediğini tahmin edin, yani $0 ' den büyük bir tip *\_* pozitif bir örnektir ve *İpucu\_$0 miktarı* negatif bir örnektir.
2. Birden çok Lass sınıflandırması: seyahat için ödenen ipucu aralığını tahmin etmek Için. *İpucu\_miktarını* beş veya daha fazla sınıfa böyoruz:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. Regresyon görevi: seyahat için ödenen ipucu miktarını tahmin etmek Için.  

## <a name="setup"></a>Gelişmiş analiz için Azure veri bilimi ortamını ayarlama
[Ortam kılavuzumuzu planınızdan](plan-your-environment.md) görebileceğiniz gibi, Azure 'da NYC TAXI gidiş veri kümesiyle çalışmak için birkaç seçenek vardır:

* Azure Bloblarındaki verilerle çalışın, sonra Azure Machine Learning modeli
* Verileri bir SQL Server veritabanına yükleme Azure Machine Learning modeli

Bu öğreticide, SQL Server Management Studio ve IPython Not defteri 'ni kullanarak, verilerin SQL Server, veri araştırmasına, özellik mühendisine ve aşağı örneklemeye yönelik paralel toplu içe aktarmayı göstereceğiz. [Örnek betikler](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) ve [IPython Not defterleri](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) GitHub 'da paylaşılır. Azure Bloblarındaki verilerle çalışacak örnek bir IPython Not defteri aynı konumda de mevcuttur.

Azure veri bilimi ortamınızı ayarlamak için:

1. [Depolama hesabı oluşturma](../../storage/common/storage-quickstart-create-account.md)
2. [Bir Azure Machine Learning çalışma alanı oluşturma](../studio/create-workspace.md)
3. Bir SQL Server ve bir IPython Not defteri sunucusu sağlayan [veri bilimi sanal makinesi sağlayın](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
   
   > [!NOTE]
   > Örnek betikler ve IPython Not defterleri, kurulum işlemi sırasında veri bilimi sanal makinenize indirilir. VM yükleme sonrası betiği tamamlandığında, örnekler sanal makinenizin Belgeler kitaplığında olacaktır:  
   > 
   > * Örnek betikler: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Örnek IPython Not defterleri: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   Burada `<user_name>`, sanal makinenizin Windows oturum açma adıdır. Örnek klasörlere örnek **betikler** ve örnek **IPython Not defterleri**olarak başvuracağız.
   > 
   > 

Veri kümesi boyutu, veri kaynağı konumu ve seçili Azure hedef ortamı temel alınarak, bu senaryo [senaryo \#5: yerel dosyalardaki büyük veri kümesi, Azure VM 'de hedef SQL Server](plan-sample-scenarios.md#largelocaltodb)benzerdir.

## <a name="getdata"></a>Genel kaynaktaki verileri al
[NYC TAXI gidiş](https://www.andresmh.com/nyctaxitrips/) veri kümesini genel konumundan almak için, verileri yeni sanal makinenize kopyalamak üzere [Azure Blob Storage 'a veri taşıma](move-azure-blob.md) bölümünde açıklanan yöntemlerden herhangi birini kullanabilirsiniz.

AzCopy kullanarak verileri kopyalamak için:

1. Sanal makinenizde (VM) oturum açın
2. VM 'nin veri diskinde yeni bir dizin oluşturun (Not: VM ile birlikte bir veri diski olarak gelen geçici diski kullanmayın).
3. Bir komut Istemi penceresinde, aşağıdaki AzCopy komut satırını çalıştırın ve < path_to_data_folder > ' de oluşturulan veri klasörünüzle değiştirin (2):
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    AzCopy tamamlandığında, toplam 24 daraltılmış CSV dosyası (seyahat için 12\_verileri ve seyahat için 12\_tarifeli havayolu) veri klasöründe olmalıdır.
4. İndirilen dosyaları sıkıştırmayı açın. Sıkıştırılmamış dosyaların bulunduğu klasörü aklınızda edin. Bu klasör, veri\_\>dosyaları\_\_< yol olarak adlandırılacaktır.

## <a name="dbload"></a>Verileri SQL Server veritabanına toplu olarak Içeri aktarma
Bir SQL veritabanına büyük miktarlarda veri yükleme/aktarma performansı ve sonraki sorgularda *bölümlenmiş tablolar ve görünümler*kullanılarak iyileştirilen. Bu bölümde, yeni bir veritabanı oluşturmak ve verileri bölümlenmiş tablolara paralel olarak yüklemek için [SQL bölüm tablolarını kullanarak paralel toplu veri Içeri aktarma](parallel-load-sql-partitioned-tables.md) bölümünde açıklanan yönergeleri takip edeceğiz.

1. SANAL makinenizde oturum açıp **SQL Server Management Studio**başlatın.
2. Windows kimlik doğrulaması kullanarak bağlanın.
   
    ![SSMS bağlantısı][12]
3. SQL Server kimlik doğrulama modunu henüz değiştirmediyseniz ve yeni bir SQL oturum açma kullanıcısı oluşturduysanız, **örnek betikler** klasöründe **\_Auth. SQL** adlı betik dosyasını açın. Varsayılan Kullanıcı adını ve parolayı değiştirin. Tıklayın **!** Betiği çalıştırmak için araç çubuğunda yürütün.
   
    ![Betiği Yürüt][13]
4. Yeni oluşturulan veritabanlarının bir veri diskinde depolandığından emin olmak için SQL Server varsayılan veritabanı ve günlük klasörlerini doğrulayın ve/veya değiştirin. Veri ambarı yüklemeleri için en iyi duruma getirilmiş SQL Server VM görüntüsü, veri ve günlük disklerle önceden yapılandırılmıştır. VM 'niz bir veri diski içermiyorsa ve VM kurulum işlemi sırasında yeni sanal sabit diskler eklediyseniz, varsayılan klasörleri aşağıdaki gibi değiştirin:
   
   * Sol panelde SQL Server adına sağ tıklayın ve **Özellikler**' e tıklayın.
     
       ![SQL Server özellikleri][14]
   * Sol taraftaki **bir sayfa seçin** listesinden **veritabanı ayarları** ' nı seçin.
   * **Veritabanı varsayılan konumlarını** doğrulayın ve/veya seçtiğiniz **veri diski** konumlarına göre değiştirin. Bu, varsayılan konum ayarlarıyla oluşturulduysa yeni veritabanlarının bulunduğu yerdir.
     
       ![SQL veritabanı Varsayılanları][15]  
5. Yeni bir veritabanı ve bölümlenmiş tabloları tutacak bir dosya grubu kümesi oluşturmak için örnek betik **oluştur\_db\_default. SQL**' yi açın. Betik, varsayılan veri konumunda **Taxınrivc** ve 12 dosya grupları adlı yeni bir veritabanı oluşturur. Her dosya grubu, veri\_seyahat eden bir ay tutar ve\_verileri seyahat eder. İsterseniz veritabanı adını değiştirin. Tıklayın **!** Betiği çalıştırmak için yürütün.
6. Daha sonra, bir diğeri seyahat\_verileri için bir tane olmak üzere iki bölüm tablosu oluşturun ve seyahat\_tarifeli havayolu. Örnek betik **oluştur\_bölümlenmiş\_Table. SQL**' i açın ve şunları yapın:
   
   * Verileri aya bölmek için bir bölüm işlevi oluşturun.
   * Her ayın verisini farklı bir dosya grubuyla eşlemek için bir bölüm düzeni oluşturun.
   * Bölüm düzenine eşlenmiş iki bölümlenmiş tablo oluşturun: **nyctaxi\_seyahat** , seyahat\_verileri ve **nyctaxi\_tarifeli havayolu** seyahat\_tarifeli havayolu verilerini tutacaktır.
     
     Tıklayın **!** Betiği çalıştırmak ve bölümlenmiş tabloları oluşturmak için yürütün.
7. **Örnek komut dosyaları** klasöründe, SQL Server tablolarına paralel toplu veri içeri aktarmaları göstermek için Iki örnek PowerShell komut dosyası sağlanır.
   
   * **bcp\_parallel\_Generic. ps1** , paralel toplu içe verileri bir tabloya aktarmak için genel bir betiktir. Bu betiği, giriş ve hedef değişkenlerini betikteki yorum satırlarında gösterildiği gibi ayarlamak için değiştirin.
   * **bcp\_parallel\_nyctaxi. ps1** , genel betiğin önceden yapılandırılmış bir sürümüdür ve NYC TAXI verileri için her iki tabloyu da yüklemek üzere kullanılabilir.  
8. **Bcp\_parallel\_nyctaxi. ps1** betik adına sağ tıklayın ve PowerShell 'de açmak için **Düzenle** ' ye tıklayın. Önceden ayarlanmış değişkenleri gözden geçirin ve seçtiğiniz veritabanı adı, giriş veri klasörü, hedef günlük klasörü ve örnek biçimi dosyaları için **nyctaxi_trip. xml** ve **nyctaxi\_tarifeli havayolu. xml** örnek biçimindeki yollara göre değiştirin ( **örnek betiklerin içinde verilmiştir)** klasörü).
   
    ![Toplu Içeri aktarma verileri][16]
   
    Kimlik doğrulama modunu da seçebilirsiniz, varsayılan Windows kimlik doğrulamadır. Çalıştırmak için araç çubuğundaki yeşil oka tıklayın. Betik, her bölümlenmiş tablo için paralel, 12 ' de 24 toplu içeri aktarma işlemi başlatacaktır. Yukarıdaki SQL Server varsayılan veri klasörünü açarak veri alma ilerlemesini izleyebilirsiniz.
9. PowerShell betiği başlangıç ve bitiş zamanlarını raporlar. Tüm toplu almalar tamamlandığında, bitiş saati raporlanır. Toplu içeri aktarmaların başarılı olduğunu, yani hedef günlük klasöründe bildirilen bir hata olmadığını doğrulamak için hedef günlük klasörünü denetleyin.
10. Veritabanınız artık, istenen şekilde araştırma, özellik Mühendisliği ve diğer işlemler için hazırdır. Tablolar **toplama\_tarih saat** alanına göre bölümlendiğinden, **WHERE** yan tümcesindeki **toplama\_tarih saat** koşullarını içeren sorgular, bölüm düzeninden yarar olacaktır.
11. **SQL Server Management Studio**, belirtilen örnek betik **örneği\_sorguları. SQL**' i gezin. Örnek sorgulardan herhangi birini çalıştırmak için, sorgu satırlarını vurgulayın ve ardından ' a tıklayın **!** Araç çubuğunda yürütün.
12. NYC TAXI gidiş verileri iki ayrı tabloya yüklenir. JOIN işlemlerini geliştirmek için tabloların dizinlemek kesinlikle önerilir. Örnek betik, **create\_bölümlenmiş\_dizin. SQL** bileşik JOIN anahtarında bölümlendirilmiş **, Hack\_lisansı ve toplama\_Tarih/saatteki**dizinler oluşturur.

## <a name="dbexplore"></a>SQL Server 'de veri araştırması ve özellik Mühendisliği
Bu bölümde, daha önce oluşturulan SQL Server veritabanını kullanarak **SQL Server Management Studio** doğrudan SQL sorguları çalıştırarak veri keşif ve özellik oluşturmayı gerçekleştirecağız. Örnek **betikler** klasöründe örnek **\_sorguları. SQL** adlı örnek bir komut dosyası verilmiştir. Komut dosyasını, varsayılan: **Taxınyc**değerinden farklıysa veritabanı adını değiştirecek şekilde değiştirin.

Bu alıştırmada şunları yapacağız:

* Windows kimlik doğrulamasını veya SQL kimlik doğrulaması 'nı ve SQL oturum açma adını ve parolasını kullanarak **SQL Server Management Studio** bağlanın.
* Değişen zaman pencereleri içinde birkaç alanın veri dağıtımlarını araştırma.
* Boylam ve enlem alanlarının veri kalitesini araştırın.
* **İpucu\_miktarına**göre ikili ve çoklu sınıf sınıflandırma etiketleri oluşturun.
* Özellik oluşturun ve seyahat mesafelerini karşılaştırın.
* İki tabloyu birleştirin ve modelleri derlemek için kullanılacak rastgele bir örnek ayıklayın.

Azure Machine Learning devam etmeye hazırsanız şunlardan birini yapabilirsiniz:  

1. Verileri ayıklamak ve örneklendirme ve kopyalamak için son SQL sorgusunu kaydedin ve sorguyu Azure Machine Learning içindeki bir [Içeri aktarma veri][import-data] modülüne doğrudan yapıştırın veya
2. Model oluşturma için kullanmayı planladığınız örneklenmiş ve uygulanan verileri yeni bir veritabanı tablosunda kalıcı hale getirin ve Azure Machine Learning [verileri Içeri aktarma][import-data] modülünde yeni tabloyu kullanın.

Bu bölümde, verileri ayıklamak ve örneklemek için son sorguyu kaydedecağız. İkinci yöntem, [IPython Not defteri 'Ndeki veri araştırma ve özellik Mühendisliği](#ipnb) bölümünde gösterilmiştir.

Daha önce paralel toplu içeri aktarma kullanılarak doldurulmuş tablolardaki satır ve sütun sayısı hakkında hızlı bir doğrulama için

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Araştırma: medtalon tarafından seyahat dağılımı
Bu örnek, belirli bir süre içinde 100 ' den fazla dönüşle birlikte medalon (TAXI numaraları) tanımlar. Sorgu, **\_Tarih/saatteki**bölüm şeması tarafından koşullu olduğundan, bölümlenmiş tablo erişiminizden faydalanır. Tam veri kümesini sorgulamak bölümlenmiş tablo ve/veya dizin taramasını de kullanır.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Araştırma: medtalon ve hack_license tarafından seyahat dağılımı
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Veri kalitesi değerlendirmesi: yanlış boylam ve/veya enlem ile kayıtları doğrulama
Bu örnekte araştırır, boylam ve/veya enlem alanlarından herhangi biri geçersiz bir değer içeriyorsa (radyana bir derece-90 ile 90 arasında olmalıdır) veya (0, 0) koordinatlara sahip olur.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Araştırma: eğimli ve eğimli dönüşler dağıtımı
Bu örnek, belirli bir süre içinde (veya tam yılı kapsauyorsa tam veri kümesinde) eğimli olarak eğimli olan gidiş 'lerin sayısını bulur. Bu dağıtım, ikili sınıflandırma modellemesi için daha sonra kullanılmak üzere ikili etiket dağıtımını yansıtır.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Araştırma: tip sınıfı/Aralık dağılımı
Bu örnek, belirli bir dönemdeki (veya tam yılı kapsadıysanız tam veri kümesindeki) Tıp aralıklarının dağıtımını hesaplar. Bu, daha sonra birden çok Lass sınıflandırma modelleme için kullanılacak olan etiket sınıflarının bir dağıtımıdır.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

#### <a name="exploration-compute-and-compare-trip-distance"></a>Araştırma: Işlem ve karşılaştırma gidiş uzaklığı
Bu örnek, toplama ve, boylam ve enlem 'yi SQL Coğrafya noktalarına dönüştürür, SQL Coğrafya noktaları farkını kullanarak seyahat mesafesini hesaplar ve karşılaştırma sonuçlarının rastgele bir örneğini döndürür. Örnek, sonuçları geçerli koordinatlara yalnızca daha önce kapsanan veri kalitesi değerlendirmesi sorgusunu kullanarak sınırlandırır.

    SELECT
    pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
    ,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
    ,trip_distance
    ,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
    FROM nyctaxi_trip
    tablesample(0.01 percent)
    WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### <a name="feature-engineering-in-sql-queries"></a>SQL sorgularında Özellik Mühendisliği
Etiket oluşturma ve Coğrafya dönüştürme araştırması sorguları, sayım bölümünü kaldırarak Etiketler/özellikler oluşturmak için de kullanılabilir. Ek özellik Mühendisliği SQL örnekleri, [IPython Not defteri 'Ndeki veri araştırma ve özellik Mühendisliği](#ipnb) bölümünde verilmiştir. Özellik oluşturma sorgularını tam veri kümesinde veya doğrudan SQL Server veritabanı örneğinde çalışan SQL sorgularını kullanarak büyük bir alt kümesinde çalıştırmak daha etkilidir. Sorgular, veritabanına yerel olarak veya uzaktan erişebilen **SQL Server Management Studio**, IPython Not defteri veya herhangi bir geliştirme aracında/ortamda çalıştırılabilir.

#### <a name="preparing-data-for-model-building"></a>Model oluşturma için veriler hazırlanıyor
Aşağıdaki sorgu **nyctaxi\_seyahat** ve **nyctaxi\_tarifeli havayolu** tablolarını birleştirir, **eğimli**bir ikili sınıflandırma etiketi, çok sınıf sınıflandırma etiketi **İpucu\_sınıfı**oluşturur ve 1% rastgele ayıklar tam katılmış veri kümesinden örnek. Bu sorgu, doğrudan Azure 'daki SQL Server veritabanı örneğinden doğrudan veri alımı için [Azure Machine Learning Studio](https://studio.azureml.net) [veri alma][import-data] modülüne doğrudan yapıştırılabilir. Sorgu kayıtları yanlış (0, 0) koordinatlarla dışlar.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 percent)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>IPython not defterinde veri araştırması ve özellik Mühendisliği
Bu bölümde, daha önce oluşturulan SQL Server veritabanında hem Python hem de SQL sorguları kullanarak veri keşif ve özellik oluşturma işlemi gerçekleştireceğiz. **Machine-Learning-Data-Science-Process-SQL-öykü. ipynb** adlı örnek bir IPython Not defteri, **örnek IPython Not defterleri** klasöründe verilmiştir. Bu not defteri [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)'da da kullanılabilir.

Büyük verilerle çalışırken önerilen sıra aşağıda verilmiştir:

* Verilerin küçük bir örneğinde bellek içi veri çerçevesini okuyun.
* Örneklenmiş verileri kullanarak bazı görselleştirmeler ve araştırmalar gerçekleştirin.
* Örneklenmiş verileri kullanarak özellik Mühendisliği ile denemeler yapın.
* Daha büyük veri araştırması, veri işleme ve özellik Mühendisliği için, Azure VM 'deki SQL Server veritabanına doğrudan SQL sorguları vermek için Python 'u kullanın.
* Azure Machine Learning model oluşturma için kullanılacak örnek boyutuna karar verin.

Azure Machine Learning devam etmeye hazırlandığınızda şunlardan birini yapabilirsiniz:  

1. Verileri ayıklamak ve örnekleyin ve kopyalamak için son SQL sorgusunu kaydedin ve sorguyu doğrudan Azure Machine Learning [Içeri aktarma verileri][import-data] modülüne yapıştırın. Bu yöntem, [Azure Machine Learning model oluşturma](#mlmodel) bölümünde gösterilmiştir.    
2. Model oluşturma için kullanmayı planladığınız örneklenmiş ve uygulanan verileri yeni bir veritabanı tablosunda kalıcı hale getirin ve ardından [verileri Içeri aktarma][import-data] modülündeki yeni tabloyu kullanın.

Aşağıda, bazı veri araştırması, veri görselleştirme ve özellik Mühendisliği örnekleri verilmiştir. Daha fazla örnek için **örnek IPython Not defterleri** KLASÖRÜNDEKI örnek SQL IPython Not defteri ' ne bakın.

#### <a name="initialize-database-credentials"></a>Veritabanı kimlik bilgilerini Başlat
Aşağıdaki değişkenlerde veritabanı bağlantı ayarlarınızı başlatın:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Veritabanı bağlantısı oluştur
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Tablodaki satır ve sütun sayısını rapor nyctaxi_trip
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('nyctaxi_trip')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('nyctaxi_trip')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Toplam satır sayısı = 173179759  
* Toplam sütun sayısı = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>SQL Server veritabanından küçük bir veri örneğini okuyun
    t0 = time.time()

    query = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (0.05 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Örnek tablonun okunacağı süre 6,492000 saniyedir  
Alınan satır ve sütun sayısı = (84952, 21)

#### <a name="descriptive-statistics"></a>Açıklayıcı Istatistikler
Şimdi örneklenmiş verileri keşfetmeye hazır. **Seyahat\_uzaklık** (veya diğer) alanları için açıklayıcı istatistiklere bakmaya başlıyoruz:

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Görselleştirme: kutu çizimi örneği
Daha sonra, ücretlerini görselleştirmek için seyahat mesafesinin kutu çizilmesine baktık

    df1.boxplot(column='trip_distance',return_type='dict')

![#1 çiz][1]

#### <a name="visualization-distribution-plot-example"></a>Görselleştirme: dağıtım çizimi örneği
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![#2 Çiz][2]

#### <a name="visualization-bar-and-line-plots"></a>Görselleştirme: çubuk ve çizgi çizimleri
Bu örnekte, seyahat mesafesini beş bölmeye dönüştürür ve atma sonuçlarını görselleştirin.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Yukarıdaki bin dağılımını aşağıda gösterildiği gibi bir çubukta veya çizgi çiziminde çiziyoruz

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![#3 çiz][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![#4 çiz][4]

#### <a name="visualization-scatterplot-example"></a>Görselleştirme: dağınık Terçiz örneği
**\_saniye içinde seyahat\_zaman\_** arasında dağılım çizimi gösteririz ve herhangi bir bağıntı olup olmadığını görmek için **seyahat\_mesafe**

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![#6 çiz][6]

Benzer şekilde, **oran\_kod** ve **seyahat\_uzaklığı**arasındaki ilişkiyi kontrol edebilirsiniz.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![#8 çiz][8]

### <a name="sub-sampling-the-data-in-sql"></a>SQL 'de verileri alt örnekleme
[Azure Machine Learning Studio](https://studio.azureml.net)model oluşturma için veri hazırlarken, **verileri içeri aktarma modülünde doğrudan kullanmak üzere SQL sorgusuna** karar verebilir veya uygulanan ve örneklenmiş verileri içeri aktarma işleminde kullanabileceğiniz yeni bir tabloda kalıcı hale getirebiliriz [ ][import-data]Basit bir select * ile veri modülü **\_yeni\_tablonuz\_ad > <** .

Bu bölümde, Örneklenmiş ve uygulanan verileri tutacak yeni bir tablo oluşturacağız. Model oluşturma için doğrudan SQL sorgusunun bir örneği, [SQL Server bölümündeki veri araştırma ve özellik Mühendisliği](#dbexplore) bölümünde verilmiştir.

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Örnek tablo oluşturun ve birleştirilmiş tabloların %1 ' iyle doldurun. Varsa tabloyu önce bırakın.
Bu bölümde, **nyctaxi\_seyahat** ve **nyctaxi\_tarifeli havayolu**tablosuna katılıyoruz, 1% rastgele bir örnek ayıkladık ve örneklenen verileri yeni bir tablo adı **nyctaxi\_bir\_yüzde**olarak kalıcı hale getiriyoruz:

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
        INTO nyctaxi_one_percent
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (1 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
        AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>IPython not defterindeki SQL sorguları kullanılarak veri araştırması
Bu bölümde, yukarıda oluşturduğumuz yeni tabloda kalıcı olan 1% örneklenmiş verileri kullanarak veri dağıtımlarını araştırıyoruz. Benzer araştırmalar, özgün tablolar kullanılarak, isteğe bağlı olarak, araştırma örneğini sınırlandırmak için Ise kullanılarak veya sonuçları **\_tarih saat** **bölümleri kullanarak belirli** bir zaman süresiyle sınırlandırarak gerçekleştirilmeyeceğini unutmayın. [SQL Server bölümünde veri araştırması ve özellik Mühendisliği](#dbexplore) 'nda gösterilmektedir.

#### <a name="exploration-daily-distribution-of-trips"></a>Araştırma: günlük gidiş dağıtımı
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Araştırma: her bir ınınon için seyahat dağıtımı
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>IPython not defterindeki SQL sorguları kullanılarak Özellik oluşturma
Bu bölümde, önceki bölümde oluşturduğumuz 1% örnek tablosunda çalışan SQL sorgularını kullanarak doğrudan yeni Etiketler ve Özellikler üretecağız.

#### <a name="label-generation-generate-class-labels"></a>Etiket oluşturma: sınıf etiketleri oluşturma
Aşağıdaki örnekte, modelleme için kullanılacak iki etiket kümesi oluşturacağız:

1. İkili sınıf etiketleri **eğimli** (bir ipucu verildiyse tahmin edilir)
2. Birden çok Lass etiketleri **ipucu\_sınıfı** (İpucu kutusu veya aralığını tahmin etme)
   
        nyctaxi_one_percent_add_col = '''
            ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
        '''
   
        cursor.execute(nyctaxi_one_percent_add_col)
        cursor.commit()
   
        nyctaxi_one_percent_update_col = '''
            UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''
   
        cursor.execute(nyctaxi_one_percent_update_col)
        cursor.commit()

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Özellik Mühendisliği: kategorik sütunlar için sayı özellikleri
Bu örnek, her bir kategoriyi verilerdeki oluşumlarının sayısıyla değiştirerek kategorik bir alanı sayısal bir alana dönüştürür.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B AS
        (
            SELECT medallion, hack_license,
                SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
                SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
            FROM nyctaxi_one_percent
            GROUP BY medallion, hack_license
        )

        UPDATE nyctaxi_one_percent
        SET nyctaxi_one_percent.cmt_count = B.cmt_count,
            nyctaxi_one_percent.vts_count = B.vts_count
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion AND A.hack_license = B.hack_license
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Özellik Mühendisliği: sayısal sütunlara yönelik bin özellikleri
Bu örnek, sürekli bir sayısal alanı önceden ayarlanmış kategori aralıklarına dönüştürür, yani sayısal alanı bir kategorik alana dönüştürür.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
        (
            SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
            NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
        )

        UPDATE nyctaxi_one_percent
        SET trip_time_bin = B.BinNumber
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion
        AND A.hack_license = B.hack_license
        AND A.pickup_datetime = B.pickup_datetime
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Özellik Mühendisliği: ondalık Enlem/Boylam konumundaki konum özelliklerini Ayıkla
Bu örnek, bir enlem ve/veya boylam alanının ondalık temsilini, ülke/bölge, şehir, şehir, blok vb. gibi farklı ayrıntı düzeyi alanlarının birden çok bölge alanına ayırır. Yeni coğrafi alanların gerçek konumlara eşlenmediğini unutmayın. Kodlamayı konumlarını eşleme hakkında bilgi için bkz. [Bing Haritalar Rest Hizmetleri](https://msdn.microsoft.com/library/ff701710.aspx).

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent
        ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
            l5 varchar(3), l6 varchar(3), l7 varchar(3)
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET l1=round(pickup_longitude,0)
            , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
            , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
            , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
            , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
            , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
            , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Korlanmış tablonun son formunu doğrulama
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Artık [Azure Machine Learning](https://studio.azureml.net)' de model oluşturma ve model dağıtımına devam etmeye hazırsınız. Veriler, daha önce tanımlanan tahmin sorunlarından herhangi biri için hazırlanıyor, yani:

1. İkili sınıflandırma: bir ipucunun seyahat için ödenip ödenmediğini tahmin etmek Için.
2. Birden çok Lass sınıflandırması: önceden tanımlanmış sınıflara göre ücretli ipucu aralığını tahmin etmek Için.
3. Regresyon görevi: seyahat için ödenen ipucu miktarını tahmin etmek Için.  

## <a name="mlmodel"></a>Azure Machine Learning modeller oluşturma
Modelleme alıştırmaya başlamak için Azure Machine Learning çalışma alanınızda oturum açın. Henüz bir Machine Learning çalışma alanı oluşturmadıysanız, bkz. [Azure Machine Learning çalışma alanı oluşturma](../studio/create-workspace.md).

1. Azure Machine Learning kullanmaya başlamak için bkz. [Azure Machine Learning Studio nedir?](../studio/what-is-ml-studio.md)
2. [Azure Machine Learning Studio](https://studio.azureml.net)oturum açın.
3. Studio giriş sayfası, çok çeşitli bilgiler, videolar, Öğreticiler, modüller başvurusu ve diğer kaynaklar için bağlantılar sağlar. Azure Machine Learning hakkında daha fazla bilgi için [Azure Machine Learning belge merkezine](https://azure.microsoft.com/documentation/services/machine-learning/)başvurun.

Tipik bir eğitim denemesi aşağıdakilerden oluşur:

1. **+ Yeni** bir deneme oluşturun.
2. Azure Machine Learning verileri alın.
3. Verileri gereken şekilde ön işleme, dönüştürme ve değiştirme.
4. Gerektiğinde özellik oluşturun.
5. Verileri eğitim/doğrulama/test veri kümelerine bölmek (veya her biri için ayrı veri kümelerine sahip olmak).
6. Çözüm öğrenme sorununa bağlı olarak bir veya daha fazla makine öğrenimi algoritması seçin. Örneğin, ikili sınıflandırma, birden çok Lass sınıflandırması, regresyon.
7. Eğitim veri kümesini kullanarak bir veya daha fazla modeli eğitme.
8. Eğitilen model (ler) i kullanarak doğrulama veri kümesini puan edin.
9. Öğrenme sorunu için ilgili ölçümleri hesaplamak üzere model (ler) i değerlendirin.
10. Model (ler) i ince ayar yapın ve dağıtılacak en iyi modeli seçin.

Bu alıştırmada, SQL Server verileri zaten araştırmış ve sunduk ve örnek boyut üzerinde Azure Machine Learning almak için karar verdik. Kararımız bir veya daha fazla tahmin modeli oluşturmak için:

1. Veri **giriş ve çıkış** bölümünde bulunan [veri alma][import-data] modülünü kullanarak Azure Machine Learning verileri alın. Daha fazla bilgi için bkz. [veri modülü başvurusunu Içeri aktarma][import-data] sayfası.
   
    ![Azure Machine Learning verileri Içeri aktar][17]
2. **Özellikler** panelinde **veri kaynağı** olarak **Azure SQL veritabanı** ' nı seçin.
3. Veritabanı **sunucusu adı** ALANıNA veritabanı DNS adını girin. Biçim: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Karşılık gelen alana **veritabanı adını** girin.
5. **Sunucu Kullanıcı hesabı adı**' na **SQL Kullanıcı adı** ' nı ve **sunucu Kullanıcı hesabı parolasıyla** **parolayı** girin.
7. **Veritabanı sorgusu** düzenleme metin alanında, gerekli veritabanı alanlarını (Etiketler gibi hesaplanan alanlar da dahil olmak üzere) çıkaran sorguyu yapıştırın ve verileri istenen örnek boyutuna doğru örnekleyin.

Doğrudan SQL Server veritabanından veri okumayı denemek için ikili sınıflandırmanın bir örneği aşağıdaki şekilde yapılır. Birden çok Lass sınıflandırması ve gerileme sorunları için benzer denemeleri oluşturulabilir.

![Azure Machine Learning eğitme][10]

> [!IMPORTANT]
> Önceki bölümlerde sağlanan modelleme veri ayıklama ve örnekleme sorgusu örneklerinde, **üç modellemeye yönelik tüm Etiketler sorguya dahil**edilmiştir. Modelleme alýþtýrmalarının her birinde önemli (gerekli) bir adım, diğer iki soruna ve diğer **hedef sızıntılara**yönelik gereksiz etiketleri **dışlayamazsınız** . Örneğin, ikili sınıflandırma kullanırken, **eğimli** etiketini kullanın ve alanları **İpucu\_sınıfı**, **tıp\_tutarı**ve **Toplam\_tutarı**hariç tutun. İkincisi, ücretli olduğunu gösterdiğinden bu yana hedef sızıntılardır.
> 
> Gereksiz sütunları ve/veya hedef sızıntılarını dışlamak için, [veri kümesi modülünde sütunları seç][select-columns] veya [Veri Düzenle][edit-metadata]' yi kullanabilirsiniz. Daha fazla bilgi için bkz. [veri kümesindeki sütunları seçme][select-columns] ve [meta veri][edit-metadata] başvuru sayfalarını düzenleme.
> 
> 

## <a name="mldeploy"></a>Azure Machine Learning modelleri dağıtma
Modeliniz hazırlandığınızda, doğrudan denemenizin içinden Web hizmeti olarak kolayca dağıtım yapabilirsiniz. Azure Machine Learning Web hizmetlerini dağıtma hakkında daha fazla bilgi için bkz. [Azure Machine Learning Web hizmeti dağıtma](../studio/deploy-a-machine-learning-web-service.md).

Yeni bir Web hizmeti dağıtmak için şunları yapmanız gerekir:

1. Puanlama denemesi oluşturun.
2. Web hizmetini dağıtın.

**Tamamlanmış** bir eğitim deneyminden bir Puanlama denemesi oluşturmak için, alt eylem çubuğunda **Puanlama denemesi oluştur** ' a tıklayın.

![Azure Puanlama][18]

Azure Machine Learning, eğitim denemesinin bileşenlerine dayalı olarak bir Puanlama denemesi oluşturmaya çalışacaktır. Özellikle, şu şekilde olur:

1. Eğitilen modeli kaydedin ve model eğitimi modüllerini kaldırın.
2. Beklenen giriş verisi şemasını temsil eden bir mantıksal **giriş bağlantı noktası** belirler.
3. Beklenen Web hizmeti çıkış şemasını temsil eden bir mantıksal **çıkış bağlantı noktası** belirler.

Puanlama denemesi oluşturulduğunda, gözden geçirin ve gerektiğinde ayarlayın. Tipik bir ayar, giriş veri kümesini ve/veya sorgusunu, bu hizmet çağrıldığında kullanılabilir olmayacak şekilde, etiket alanlarını dışladığı bir şekilde değiştirmek olacaktır. Ayrıca, giriş veri kümesinin boyutunu azaltmak ve/veya birkaç kayıt için sorgu yapmak için yeterli bir uygulamadır. Çıkış bağlantı noktası için, tüm giriş alanlarını hariç tutmak ve yalnızca [veri kümesindeki sütunları seç][select-columns] modülünde yer alan Select sütunları kullanılarak **çıktıda puanlanmış** olan **olasılıkların** dahil olması yaygındır.

Örnek Puanlama denemesi aşağıdaki şekildir. Dağıtıma hazırsanız, alt eylem çubuğunda **Web hizmeti Yayımla** düğmesine tıklayın.

![Azure Machine Learning yayımlama][11]

Bu kılavuzda, bu adım adım öğreticide, bir Azure veri bilimi ortamı oluşturdunuz ve bir Azure Machine Learning Web hizmeti, veri alımı ve bir Web hizmetinin dağıtılması için büyük bir genel veri kümesiyle çalıştık.

### <a name="license-information"></a>Lisans bilgileri
Bu örnek izlenecek yol ve birlikte gelen betikler ve IPython Not defteri, Microsoft tarafından MıT lisansı kapsamında paylaşılır. Daha fazla ayrıntı için lütfen GitHub 'daki örnek kodun dizinindeki LICENSE. txt dosyasını denetleyin.

### <a name="references"></a>Başvurular
• [Andr, Monroy NYC Taxi seyahatler Indirme sayfası](https://www.andresmh.com/nyctaxitrips/)  
• [Cwhong  NYC 'Nin TAXI veri yolculuğu](https://chriswhong.com/open-data/foil_nyc_taxi/)  
• [NYC Taxi ve Limousine Komisyonu araştırması ve istatistikleri](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
