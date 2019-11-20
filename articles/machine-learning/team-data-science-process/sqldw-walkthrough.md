---
title: SQL veri ambarı kullanarak model oluşturma ve dağıtma-Team Data Science Process
description: Genel kullanıma açık bir veri kümesiyle SQL veri ambarı 'nı kullanarak bir makine öğrenimi modeli oluşturun ve dağıtın.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/24/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 76afafb59de762776b7d2614e383320b7d8f79e4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669397"
---
# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>Takım veri bilimi süreci: SQL veri ambarı kullanma
Bu öğreticide, genel kullanıma açık bir veri kümesi için SQL veri ambarı (SQL DW) kullanarak makine öğrenimi modeli oluşturma ve dağıtma konusunda size yol gösterir. Bu, [NYC TAXI gezme](https://www.andresmh.com/nyctaxitrips/) veri kümesi. Oluşturulan ikili sınıflandırma modeli, bir ucun seyahat için ödenip ödenmediğini tahmin eder ve çok sınıflı sınıflandırma ve gerileme için modeller, ücretli tıp miktarları için dağılımı tahmin eden bir şekilde da ele alınmıştır.

Yordam, [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) iş akışını izler. Veri bilimi ortamının nasıl ayarlanacağını, verilerin SQL DW 'e nasıl yükleneceğini ve modele veri ve mühendislik özelliklerini incelemek için SQL DW veya bir IPython Not defteri ' ni nasıl kullanacağınızı gösteririz. Daha sonra Azure Machine Learning ile model derleyip dağıtmayı göstereceğiz.

## <a name="dataset"></a>NYC TAXI gidiş veri kümesi
NYC TAXI seyahat verileri yaklaşık 20 GB sıkıştırılmış CSV dosyasından oluşur (~ 48GB sıkıştırılmamış), 173.000.000 ' den fazla gidiş ve her yolculuğa göre ödenen Fareli kayıt. Her seyahat kaydı, toplama ve bırakma konumlarını ve zamanlarını, anonim olarak bulunan Hack (sürücü) lisans numarasını ve medalon (TAXI 'nin benzersiz kimliği) numarasını içerir. Veriler, 2013 yılında yapılan tüm döngüleri kapsamakta ve her ay için aşağıdaki iki veri kümelerinde sunulmaktadır:

1. **Trip_data. csv** dosyası, pasmacılar, toplama ve açılan noktaların sayısı, seyahat süresi ve seyahat uzunluğu gibi seyahat ayrıntılarını içerir. Aşağıda birkaç örnek kayıt verilmiştir:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. **Trip_fare. csv** dosyası, ödeme türü, tarifeli havayolu tutarı, ek ücret, vergiler, ipuçları ve Tolls ve ödenen toplam miktar gibi her seyahat için ödenen tarifeli havayolu 'ın ayrıntılarını içerir. Aşağıda birkaç örnek kayıt verilmiştir:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Seyahat\_veri ve seyahat\_tarifeli havayolu birleştirmek için kullanılan **benzersiz anahtar** aşağıdaki üç alandan oluşur:

* medalon,
* Hack\_lisansı ve
* \_Tarih/çekme.

## <a name="mltasks"></a>Üç tahmin görevi türünü ele edin
Üç tür modelleme görevini göstermek için *ipucu\_miktarına* göre üç tahmin sorunu şekilliyoruz:

1. **İkili sınıflandırma**: bir tıp 'nin seyahat için ödenip ödenmediğini tahmin etmek için $0 'den büyük bir ipucu *\_* pozitif bir örnektir; *İpucu\_$0 miktarı* negatif bir örnektir.
2. **Birden çok Lass sınıflandırması**: seyahat için ödenen ipucu aralığını tahmin etmek için. *İpucu\_miktarını* beş veya daha fazla sınıfa böyoruz:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Regresyon görevi**: seyahat için ödenen ipucu miktarını tahmin etmek için.

## <a name="setup"></a>Gelişmiş analiz için Azure veri bilimi ortamını ayarlama
Azure veri bilimi ortamınızı ayarlamak için aşağıdaki adımları izleyin.

**Kendi Azure Blob depolama hesabınızı oluşturun**

* Kendi Azure Blob depolama alanınızı sağladığınızda, Azure Blob depolama alanınızı bir coğrafi konum seçin veya **Orta Güney ABD**, bu, NYC TAXI verilerinin saklandığı yerdir. Veriler, genel BLOB depolama kapsayıcısından gelen AzCopy kullanılarak kendi depolama hesabınızdaki bir kapsayıcıya kopyalanacak. Azure Blob depolama alanınızı daha yakından Orta Güney ABD, bu görevin daha hızlı (4. adım) tamamlanması tamamlanacaktır.
* Kendi Azure depolama hesabınızı oluşturmak için [Azure depolama hesapları hakkında](../../storage/common/storage-create-storage-account.md)bölümünde açıklanan adımları izleyin. Bu kılavuzda daha sonra gerekli olacağı için, aşağıdaki depolama hesabı kimlik bilgileri değerlerini not aldığınızdan emin olun.

  * **Depolama hesabı adı**
  * **Depolama hesabı anahtarı**
  * **Kapsayıcı adı** (verilerin Azure Blob depolamada depolanmasını istediğiniz)

**Azure SQL DW örneğinizi sağlayın.**
SQL veri ambarı örneği sağlamak için [SQL veri ambarı oluşturma](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) bölümündeki belgeleri izleyin. Sonraki adımlarda kullanılacak aşağıdaki SQL veri ambarı kimlik bilgileri üzerinde gösterimler olduğunuzdan emin olun.

* **Sunucu adı**: \<sunucu adı >. Database. Windows. net
* **SQLDW (veritabanı) adı**
* **Kullanıcı Adı**
* **Parola**

**Visual Studio 'Yu ve SQL Server Veri Araçları 'yi yükleyip.** Yönergeler için bkz. [SQL veri ambarı Için Visual Studio 2015 ve/veya SSDT 'yi (SQL Server veri araçları)](../../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Visual Studio ile Azure SQL DW 'nize bağlanın.** Yönergeler için bkz. Adım 1 & 2 [Visual Studio Ile Azure SQL veri ambarı 'Na bağlanma](../../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

> [!NOTE]
> **Ana anahtar oluşturmak**IÇIN, SQL veri Ambarınızda oluşturduğunuz veritabanında (bağlantı konusunun 3. adımında belirtilen sorgu yerine) aşağıdaki SQL sorgusunu çalıştırın.
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Azure aboneliğiniz altında bir Azure Machine Learning çalışma alanı oluşturun.** Yönergeler için bkz. [Azure Machine Learning çalışma alanı oluşturma](../studio/create-workspace.md).

## <a name="getdata"></a>Verileri SQL veri ambarı 'na yükleme
Bir Windows PowerShell komut konsolunu açın. GitHub 'da sizinle paylaşdığımız örnek SQL komut dosyalarını, *-DestDir*parametresiyle belirttiğiniz yerel bir dizine indirmek Için aşağıdaki PowerShell komutlarını çalıştırın. *-DestDir* parametresinin değerini herhangi bir yerel dizin olarak değiştirebilirsiniz. *-DestDir* yoksa, PowerShell betiği tarafından oluşturulur.

> [!NOTE]
> *DestDir* dizininizin bu dosyayı oluşturmak veya yazmak için yönetici ayrıcalıklarına ihtiyacı varsa, aşağıdaki PowerShell betiğini yürütürken **yönetici olarak çalıştırmanız** gerekebilir.
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Başarılı yürütmeden sonra, geçerli çalışma dizininiz *-DestDir*olarak değişir. Aşağıdaki gibi bir ekran görebilmelisiniz:

![Geçerli çalışma dizini değişiklikleri][19]

*-DestDir*' de, aşağıdaki PowerShell betiğini yönetici modunda yürütün:

    ./SQLDW_Data_Import.ps1

PowerShell betiği ilk kez çalıştırıldığında, Azure SQL DW 'nizden ve Azure Blob Storage hesabınızdan bilgileri imzalamanız istenir. Bu PowerShell betiği ilk kez çalışmayı tamamladığında, girdiğiniz kimlik bilgileri mevcut çalışma dizinindeki SQLDW. conf yapılandırma dosyasına yazılır. Bu PowerShell betik dosyasının gelecekte çalıştırılması, bu yapılandırma dosyasından gereken tüm parametreleri okuma seçeneğine sahiptir. Bazı parametreleri değiştirmeniz gerekiyorsa, bu yapılandırma dosyasını silerek ve parametreler değerlerini istendiği şekilde kaydederek ve parametreleri parametre değerlerini değiştirerek,-' de SQLDW. conf dosyasını düzenleyerek parametre değerlerini değiştirmek üzere ekranda parametreleri yeniden seçebilirsiniz.  *DestDir* dizini.

> [!NOTE]
> Şema adının Azure SQL DW 'niz üzerinde zaten mevcut olanlarla çakışmasını önlemek için, parametreleri doğrudan SQLDW. conf dosyasından okurken, SQLDW. conf dosyasındaki şema adına her çalıştırma için varsayılan şema adı olarak 3 basamaklı bir rastgele sayı eklenir. PowerShell betiği, bir şema adı isteyip istemeyebilir: ad kullanıcı tarafından belirtilmeyebilir.
>
>

Bu **PowerShell betik** dosyası aşağıdaki görevleri tamamlar:

* AzCopy zaten yüklü değilse **AzCopy indirir ve yükler**

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
               Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }
* **Özel BLOB depolama hesabınıza** , AzCopy ile genel Blobun verileri kopyalar

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* , **PolyBase kullanarak verileri yükler (LoadDataToSQLDW. SQL dosyasını çalıştırarak) özel BLOB depolama hesabınızdan Azure SQL DW 'nize** aşağıdaki komutlarla

  * Şema oluşturma

          EXEC (''CREATE SCHEMA {schemaname};'');
  * Veritabanı kapsamlı kimlik bilgisi oluşturma

          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Azure Depolama Blobu için dış veri kaynağı oluşturma

          CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;

          CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;
  * CSV dosyası için harici dosya biçimi oluşturun. Verilerin sıkıştırması kaldırılır ve alanlar kanal karakteriyle ayrılır.

          CREATE EXTERNAL FILE FORMAT {csv_file_format}
          WITH
          (
              FORMAT_TYPE = DELIMITEDTEXT,
              FORMAT_OPTIONS
              (
                  FIELD_TERMINATOR ='','',
                  USE_TYPE_DEFAULT = TRUE
              )
          )
          ;
  * Azure Blob depolamada NYC TAXI veri kümesi için dış tarifeli havayolu ve seyahat tabloları oluşturun.

          CREATE EXTERNAL TABLE {external_nyctaxi_fare}
          (
              medallion varchar(50) not null,
              hack_license varchar(50) not null,
              vendor_id char(3),
              pickup_datetime datetime not null,
              payment_type char(3),
              fare_amount float,
              surcharge float,
              mta_tax float,
              tip_amount float,
              tolls_amount float,
              total_amount float
          )
          with (
              LOCATION    = ''/nyctaxifare/'',
              DATA_SOURCE = {nyctaxi_fare_storage},
              FILE_FORMAT = {csv_file_format},
              REJECT_TYPE = VALUE,
              REJECT_VALUE = 12
          )

            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                   medallion varchar(50) not null,
                   hack_license varchar(50)  not null,
                   vendor_id char(3),
                   rate_code char(3),
                   store_and_fwd_flag char(3),
                   pickup_datetime datetime  not null,
                   dropoff_datetime datetime,
                   passenger_count int,
                   trip_time_in_secs bigint,
                   trip_distance float,
                   pickup_longitude varchar(30),
                   pickup_latitude varchar(30),
                   dropoff_longitude varchar(30),
                   dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12
            )

    - Azure Blob depolama 'daki dış tablolardaki verileri SQL veri ambarı 'na yükleme

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Örnek veri tablosu oluşturun (NYCTaxi_Sample) ve seyahat ve tarifeli havayolu tablolarında SQL sorguları ' nı seçerek bu tabloya veri ekleyin. (Bu izlenecek yolun bazı adımlarının bu örnek tabloyu kullanması gerekir.)

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

Depolama hesaplarınızın coğrafi konumu yükleme sürelerini etkiler.

> [!NOTE]
> Özel BLOB depolama hesabınızın coğrafi konumuna bağlı olarak, genel bir Blobun verileri özel depolama hesabınıza kopyalama işlemi yaklaşık 15 dakika, hatta daha uzun sürebilir ve depolama hesabınızdan Azure 'a veri yükleme işlemi gerçekleştirebilir SQL DW 20 dakika veya daha uzun sürebilir.
>
>

Yinelenen kaynak ve hedef dosyalarınız varsa ne olacağına karar vermeniz gerekir.

> [!NOTE]
> Genel BLOB depolama alanında özel BLOB depolama hesabınıza kopyalanan. csv dosyaları özel BLOB depolama hesabınızda zaten mevcutsa AzCopy, üzerine yazmak isteyip istemediğinizi sorar. Üzerine yazmak istemiyorsanız, istendiğinde **n** girin. Bunların **tümünün** üzerine yazmak isterseniz, istendiğinde **bir** girin. Ayrıca,. csv dosyalarının üzerine yazmak için **y** girişi de yapabilirsiniz.
>
>

![AzCopy 'tan çıkış][21]

Kendi verilerinizi kullanabilirsiniz. Verileriniz gerçek yaşam uygulamanızdaki şirket içi makinenizde ise, şirket içi verileri özel Azure Blob depolama alanına yüklemek için AzCopy kullanmaya devam edebilirsiniz. Yalnızca, PowerShell betik dosyasının AzCopy komutunda `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`**kaynak** konumunu, verilerinizi içeren yerel dizine değiştirmeniz gerekir.

> [!TIP]
> Verileriniz gerçek yaşam uygulamanızda zaten özel Azure Blob depolarlarda varsa, PowerShell betiğinin AzCopy adımını atlayabilir ve verileri doğrudan Azure SQL DW 'ye yükleyebilirsiniz. Bu, verilerinizin biçimine uyarlamak için betiğin ek düzenlemeleri yapılmasını gerektirir.
>
>

Bu PowerShell betiği Ayrıca, bu üç dosyanın anında denenmeye hazır olması için SQLDW_Explorations. SQL, SQLDW_Explorations. ipynb ve SQLDW_Explorations_Scripts. into veri araştırma örnek dosyalarına Azure SQL DW bilgilerini de takar PowerShell betiği tamamlanır.

Başarılı bir yürütmeden sonra aşağıdaki gibi bir ekran göreceksiniz:

![Başarılı bir betik yürütmenin çıkışı][20]

## <a name="dbexplore"></a>Azure SQL veri ambarı 'nda veri araştırması ve özellik Mühendisliği
Bu bölümde, doğrudan **Visual Studio veri araçlarını**kullanarak Azure SQL DW 'ye karşı SQL sorguları çalıştırarak veri keşif ve özellik oluşturma gerçekleştiririz. Bu bölümde kullanılan tüm SQL sorguları *SQLDW_Explorations. SQL*adlı örnek betikte bulunabilir. Bu dosya, PowerShell betiği tarafından yerel dizininize zaten indirildi. Ayrıca, [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql)'dan da alabilirsiniz. Ancak GitHub 'daki dosyada Azure SQL DW bilgileri takılı değildir.

SQL DW oturum açma adı ve parolasıyla Visual Studio 'Yu kullanarak Azure SQL DW 'nize bağlanın ve veritabanını ve tabloları içeri aktardığından emin olmak için **sql Nesne Gezgini** açın. *SQLDW_Explorations. SQL* dosyasını alın.

> [!NOTE]
> Bir paralel veri ambarı (PDW) sorgu Düzenleyicisi açmak için, **SQL nesne Gezgini**'da PDW seçildiğinde **Yeni sorgu** komutunu kullanın. Standart SQL sorgu Düzenleyicisi, PDW tarafından desteklenmez.
>
>

Bu bölümde gerçekleştirilen veri keşif ve özellik oluşturma görevlerinin türü aşağıda verilmiştir:

* Değişen zaman pencereleri içinde birkaç alanın veri dağıtımlarını araştırma.
* Boylam ve enlem alanlarının veri kalitesini araştırın.
* **İpucu\_miktarına**göre ikili ve çoklu sınıf sınıflandırma etiketleri oluşturun.
* Özellik oluşturun ve seyahat mesafelerini karşılaştırın.
* İki tabloyu birleştirin ve modelleri derlemek için kullanılacak rastgele bir örnek ayıklayın.

### <a name="data-import-verification"></a>Veri içeri aktarma doğrulaması
Bu sorgular, PolyBase 'in paralel toplu içeri aktarması kullanılarak daha önce doldurulmuş tablolardaki satır ve sütun sayısı için hızlı bir doğrulama sağlar,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Çıkış:** 173.179.759 satır ve 14 sütun almalısınız.

### <a name="exploration-trip-distribution-by-medallion"></a>Araştırma: medtalon tarafından seyahat dağılımı
Bu örnek sorgu, belirli bir süre içinde 100 ' den fazla dönüşten fazla geçen bir aralıktaki (TAXI numaralarını) bir sorgu tanımlar. Sorgu, **\_Tarih/saatteki**bölüm şeması tarafından koşullu olduğundan, bölümlenmiş tablo erişiminizden faydalanır. Tam veri kümesini sorgulamak bölümlenmiş tablo ve/veya dizin taramasını de kullanır.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Çıkış:** Sorgu, 13.369 medalons 'yi (taxsıs) belirten satırları içeren bir tablo döndürmelidir ve bu sayı 2013 ' de tarafından tamamlandı. Son sütun, tamamlanan gezlerin sayısını içerir.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Araştırma: medtalon ve hack_license tarafından seyahat dağılımı
Bu örnek, belirli bir süre içinde 100 ' den fazla dönüşten daha fazlasını tamamlayan medalons (TAXI rakamları) ve hack_license numaralarını (sürücüler) tanımlar.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Çıkış:** Sorgu, 100 2013 ' te daha fazla tamamlanan 13.369 otomobil/sürücü kimliklerini belirten 13.369 satırlık bir tablo döndürmelidir. Son sütun, tamamlanan gezlerin sayısını içerir.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Veri kalitesi değerlendirmesi: yanlış boylam ve/veya enlem ile kayıtları doğrulama
Bu örnekte araştırır, boylam ve/veya enlem alanlarından herhangi biri geçersiz bir değer içeriyorsa (radyana bir derece-90 ile 90 arasında olmalıdır) veya (0, 0) koordinatlara sahip olur.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Çıkış:** Sorgu, geçersiz boylam ve/veya enlem alanları olan 837.467 gezileri döndürüyor.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Araştırma: eğimli ve eğimli dönüşler dağıtımı
Bu örnek, eğimli ve belirli bir süre içinde (veya burada ayarlandığı üzere tam yıl varsa tam veri kümesinde) eğimli olan gidiş sayısını bulur. Bu dağıtım, ikili sınıflandırma modellemesi için daha sonra kullanılmak üzere ikili etiket dağıtımını yansıtır.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Çıkış:** Sorgu, 2013:90.447.622 eğimli ve 82.264.709-eğik olmayan yıl için aşağıdaki uç sıklığı döndürmelidir.

### <a name="exploration-tip-classrange-distribution"></a>Araştırma: tip sınıfı/Aralık dağılımı
Bu örnek, belirli bir dönemdeki (veya tam yılı kapsadıysanız tam veri kümesindeki) Tıp aralıklarının dağıtımını hesaplar. Bu, daha sonra birden çok Lass sınıflandırma modelleme için kullanılacak olan etiket sınıflarının bir dağıtımıdır.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Çıktıların**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Araştırma: Işlem ve karşılaştırma gidiş uzaklığı
Bu örnek, toplama ve, boylam ve enlem 'yi SQL Coğrafya noktalarına dönüştürür, SQL Coğrafya noktaları farkını kullanarak seyahat mesafesini hesaplar ve karşılaştırma sonuçlarının rastgele bir örneğini döndürür. Örnek, sonuçları geçerli koordinatlara yalnızca daha önce kapsanan veri kalitesi değerlendirmesi sorgusunu kullanarak sınırlandırır.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>SQL işlevleri kullanarak özellik Mühendisliği
Bazen SQL işlevleri Özellik Mühendisliği için etkili bir seçenek olabilir. Bu kılavuzda, toplama ve bırakma konumları arasındaki doğrudan mesafeyi hesaplamak için bir SQL işlevi tanımladık. **Visual Studio veri araçları**'NDA aşağıdaki SQL betiklerini çalıştırabilirsiniz.

Distance işlevini tanımlayan SQL betiği aşağıda verilmiştir.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

SQL sorgunuzda özellikler oluşturmak için bu işlevi çağırmak üzere bir örnek aşağıda verilmiştir:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Çıkış:** Bu sorgu, potitudes ve Longitudes ve mil içindeki karşılık gelen doğrudan uzaklıklarla birlikte toplama ve Dropoff ile bir tablo (2.803.538 satırlık) oluşturur. İlk 3 satırın sonuçları aşağıda verilmiştir:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40,731804 |-74,001083 |40,736622 |-73,988953 |.7169601222 |
| 2 |40,715794 |-74, 010635 |40,725338 |-74,00399 |.7448343721 |
| 3 |40,761456 |-73,999886 |40,766544 |-73,988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Model oluşturma için verileri hazırlama
Aşağıdaki sorgu **nyctaxi\_seyahat** ve **nyctaxi\_tarifeli havayolu** tablolarını birleştirir, **eğimli**bir ikili sınıflandırma etiketi, bir çok sınıf sınıflandırma etiketi **İpucu\_sınıfı**oluşturur ve bir örneği kümeden ayıklar tam katılmış veri kümesi. Örnekleme zamanına göre dönüşlerin bir alt kümesini alarak örnekleme yapılır.  Bu sorgu daha sonra doğrudan Azure 'daki SQL veritabanı örneğinden veri [alımı Için veri içeri aktarma][import-data] modülüne [Azure Machine Learning Studio](https://studio.azureml.net) kopyalanabilir. Sorgu kayıtları yanlış (0, 0) koordinatlarla dışlar.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Azure Machine Learning devam etmeye hazırsanız şunlardan birini yapabilirsiniz:

1. Verileri ayıklamak ve örneklendirme ve kopyalamak için son SQL sorgusunu kaydedin ve sorguyu Azure Machine Learning içindeki bir [Içeri aktarma veri][import-data] modülüne doğrudan yapıştırın veya
2. Model oluşturma için kullanmayı planladığınız örneklenmiş ve uygulanan verileri yeni bir SQL DW tablosunda kalıcı hale getirin ve Azure Machine Learning [verileri Içeri aktarma][import-data] modülünde yeni tabloyu kullanın. Önceki adımda bulunan PowerShell betiği sizin için bunu yaptık. Veri Içeri aktarma modülündeki bu tablodan doğrudan okuma yapabilirsiniz.

## <a name="ipnb"></a>IPython not defterinde veri araştırması ve özellik Mühendisliği
Bu bölümde, daha önce oluşturulan SQL DW 'de hem Python hem de SQL sorguları kullanarak veri keşif ve özellik oluşturma işlemi gerçekleştireceğiz. **SQLDW_Explorations. ipynb** adlı örnek bir IPython Not defteri ve bir Python betik dosyası **SQLDW_Explorations_Scripts. Kopyala** yerel dizininize indirildi. Bunlar [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW)'da da kullanılabilir. Bu iki dosya Python betiklerine benzer. Python betik dosyası, bir IPython Not defteri sunucunuz olmadığı durumlarda size sağlanır. Bu iki örnek Python dosyası, **Python 2,7**altında tasarlanmıştır.

Örnek IPython not defterindeki gerekli Azure SQL DW bilgileri ve yerel makinenize indirilen Python betik dosyası, daha önce PowerShell betiği tarafından takılmış. Bunlar herhangi bir değişiklik yapılmadan çalıştırılabilir.

Zaten bir AzureML çalışma alanı ayarladıysanız, örnek IPython Not defterini AzureML ıdithon Not defteri hizmetine doğrudan yükleyebilir ve çalıştırmaya başlayabilirsiniz. Aşağıda, AzureML ıvthon Not defteri hizmetine yükleme adımları verilmiştir:

1. AzureML çalışma alanınızda oturum açın, üstteki "Studio" ya tıklayın ve Web sayfasının sol tarafındaki "Not DEFTERLERI" ne tıklayın.

    ![Stüdyo ve Not DEFTERLERI ' ne tıklayın][22]
2. Web sayfasının sol alt köşesindeki "yenı" ye tıklayın ve "Python 2" öğesini seçin. Ardından, not defterine bir ad verin ve yeni boş IPython Not defteri oluşturmak için onay işaretine tıklayın.

    ![YENI ' ye tıklayıp Python 2 ' yi seçin][23]
3. Yeni IPython Not defterinin sol üst köşesindeki "Jupyter" simgesine tıklayın.

    ![Jupyıter simgesine tıklayın][24]
4. Örnek IPython Not defterini sürükleyip, AzureML VMM 'nizin **ağaç** sayfasına bırakın ve **karşıya yükle**' ye tıklayın. Ardından, örnek IPython Not defteri, AzureML ıdion Not defteri hizmetine yüklenir.

    ![Karşıya yükle 'ye tıklayın][25]

Örnek IPython Not defteri 'ni veya Python betik dosyasını çalıştırmak için aşağıdaki Python paketleri gereklidir. AzureML IPython Not Defteri hizmetini kullanıyorsanız, bu paketler önceden yüklenmiştir.

- Pandas
- sayısal tuş takımı
- Matplotlib
- pyodbc
- PyTables

Büyük verilerle AzureML üzerinde gelişmiş analitik çözümler oluştururken önerilen dizi aşağıdakiler aşağıda verilmiştir:

* Verilerin küçük bir örneğinde bellek içi veri çerçevesini okuyun.
* Örneklenmiş verileri kullanarak bazı görselleştirmeler ve araştırmalar gerçekleştirin.
* Örneklenmiş verileri kullanarak özellik Mühendisliği ile denemeler yapın.
* Daha büyük veri araştırması, veri işleme ve özellik Mühendisliği için Python kullanarak SQL sorgularını doğrudan SQL DW 'ye karşı verin.
* Örnek boyutunu Azure Machine Learning model oluşturma için uygun olacak şekilde belirleyin.

Her bir veri araştırması, veri görselleştirme ve özellik Mühendisliği örnekleri aşağıda verilmiştir. Örnek IPython not defterinde ve örnek Python betik dosyasında daha fazla veri araştırması bulunabilir.

### <a name="initialize-database-credentials"></a>Veritabanı kimlik bilgilerini Başlat
Aşağıdaki değişkenlerde veritabanı bağlantı ayarlarınızı başlatın:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Veritabanı bağlantısı oluştur
Veritabanına bağlantıyı oluşturan bağlantı dizesi aşağıda verilmiştir.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Tablodaki satır ve sütun sayısını rapor < nyctaxi_trip >
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Toplam satır sayısı = 173179759
* Toplam sütun sayısı = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>Tablodaki satır ve sütun sayısını rapor < nyctaxi_fare >
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Toplam satır sayısı = 173179759
* Toplam sütun sayısı = 11

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>SQL veri ambarı veritabanından küçük bir veri örneğini okuyun
    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Örnek tablonun okunacağı süre 14,096495 saniyedir.
Alınan satır ve sütun sayısı = (1000, 21).

### <a name="descriptive-statistics"></a>Açıklayıcı istatistikler
Artık örneklenmiş verileri araştırmaya hazırsınız. **Seyahat\_mesafesi** (veya belirtmek için seçtiğiniz diğer alanlar) için bazı açıklayıcı istatistiklere bakmaya başladık.

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Görselleştirme: kutu çizimi örneği
Daha sonra, ücretlerini görselleştirmek için seyahat mesafesinin kutu çizilmesine bakacağız.

    df1.boxplot(column='trip_distance',return_type='dict')

![Kutu çizimi çıkışı][1]

### <a name="visualization-distribution-plot-example"></a>Görselleştirme: dağıtım çizimi örneği
Örneklenen seyahat mesafeleri için dağılımı ve histogramı görselleştirerek çizimler.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Dağıtım çizimi çıkışı][2]

### <a name="visualization-bar-and-line-plots"></a>Görselleştirme: çubuk ve çizgi çizimleri
Bu örnekte, seyahat mesafesini beş bölmeye dönüştürür ve atma sonuçlarını görselleştirin.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Yukarıdaki bin dağılımını bir çubuk veya çizgi çiziminde şu şekilde çizebilirsiniz:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Çubuk çizim çıkışı][3]

ve

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Satır çizim çıkışı][4]

### <a name="visualization-scatterplot-examples"></a>Görselleştirme: dağınık Terçiz örnekleri
**\_saniye içinde seyahat\_zaman\_** arasında dağılım çizimi gösteririz ve herhangi bir bağıntı olup olmadığını görmek için **seyahat\_mesafe**

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Zaman ve uzaklık arasındaki ilişkinin dağınık terçiz çıkışı][6]

Benzer şekilde, **oran\_kod** ve **seyahat\_uzaklığı**arasındaki ilişkiyi kontrol edebilirsiniz.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Kod ve uzaklık arasındaki ilişkinin dağınık terçiz çıkışı][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>IPython not defterindeki SQL sorguları kullanılarak örneklenmiş verilerle veri araştırması
Bu bölümde, yukarıda oluşturduğumuz yeni tabloda kalıcı olan örneklenmiş verileri kullanarak veri dağıtımlarını araştırıyoruz. Benzer araştırmaları, özgün tablolar kullanılarak gerçekleştirilebileceğini unutmayın.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Araştırma: örneklenmiş tablodaki satır ve sütun sayısını rapor edin
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Araştırma: eğimli/Üçlü dağıtım
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Araştırma: tıp sınıfı dağıtımı
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Araştırma: sınıfa göre ipucu dağıtımını çizdirme
    tip_class_dist['tip_freq'].plot(kind='bar')

![#26 çiz][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Araştırma: günlük gidiş dağıtımı
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Araştırma: her bir ınınon için seyahat dağıtımı
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Araştırma: medtalon ve Hack lisansınıza göre seyahat dağılımı
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Araştırma: seyahat süresi dağılımı
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Araştırma: seyahat uzaklığı dağılımı
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Araştırma: ödeme türü dağılımı
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Korlanmış tablonun son formunu doğrulama
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Azure Machine Learning modelleri derleme
Artık [Azure Machine Learning](https://studio.azureml.net)' de model oluşturma ve model dağıtımına devam etmeye hazırsınız. Veriler, daha önce tanımlanan tüm tahmin sorunlarından birinde kullanılmak üzere hazırlanmıştır, yani:

1. **İkili sınıflandırma**: bir ipucunun seyahat için ödenip ödenmediğini tahmin etmek için.
2. **Birden çok Lass sınıflandırması**: önceden tanımlanmış sınıflara göre ücretli ipucu aralığını tahmin etmek için.
3. **Regresyon görevi**: seyahat için ödenen ipucu miktarını tahmin etmek için.

Modelleme alıştırmaya başlamak için **Azure Machine Learning (klasik)** çalışma alanınızda oturum açın. Henüz bir Machine Learning çalışma alanı oluşturmadıysanız, bkz. [Azure Machine Learning Studio (klasik) çalışma alanı oluşturma](../studio/create-workspace.md).

1. Azure Machine Learning kullanmaya başlamak için bkz. [Azure Machine Learning Studio nedir?](../studio/what-is-ml-studio.md)
2. [Azure Machine Learning Studio](https://studio.azureml.net)oturum açın.
3. Studio giriş sayfası, çok çeşitli bilgiler, videolar, Öğreticiler, modüller başvurusu ve diğer kaynaklar için bağlantılar sağlar. Azure Machine Learning hakkında daha fazla bilgi için [Azure Machine Learning belge merkezine](https://azure.microsoft.com/documentation/services/machine-learning/)başvurun.

Tipik bir eğitim denemesi aşağıdaki adımlardan oluşur:

1. **+ Yeni** bir deneme oluşturun.
2. Verileri Azure Machine Learning Studio 'ya alın.
3. Verileri gereken şekilde ön işleme, dönüştürme ve değiştirme.
4. Gerektiğinde özellik oluşturun.
5. Verileri eğitim/doğrulama/test veri kümelerine bölmek (veya her biri için ayrı veri kümelerine sahip olmak).
6. Çözüm öğrenme sorununa bağlı olarak bir veya daha fazla makine öğrenimi algoritması seçin. Örneğin, ikili sınıflandırma, birden çok Lass sınıflandırması, regresyon.
7. Eğitim veri kümesini kullanarak bir veya daha fazla modeli eğitme.
8. Eğitilen model (ler) i kullanarak doğrulama veri kümesini puan edin.
9. Öğrenme sorunu için ilgili ölçümleri hesaplamak üzere model (ler) i değerlendirin.
10. Model (ler) i ince ayar yapın ve dağıtılacak en iyi modeli seçin.

Bu alıştırmada, verileri SQL veri ambarı 'nda zaten araştırmış ve sunduk ve örnek boyutuna Azure Machine Learning Studio 'da giriş yapmaya karar verdik. Tahmin modellerinin bir veya birkaçını oluşturmak için aşağıdaki yordam aşağıda verilmiştir:

1. Veri **giriş ve çıkış** bölümünde bulunan [veri içeri aktarma][import-data] modülünü kullanarak verileri Azure Machine Learning Studio (klasik) olarak alın. Daha fazla bilgi için bkz. [veri modülü başvurusunu Içeri aktarma][import-data] sayfası.

    ![Azure ML verileri Içeri aktarma][17]
2. **Özellikler** panelinde **veri kaynağı** olarak **Azure SQL veritabanı** ' nı seçin.
3. Veritabanı **sunucusu adı** ALANıNA veritabanı DNS adını girin. Biçim: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Karşılık gelen alana **veritabanı adını** girin.
5. **Sunucu Kullanıcı hesabı adı**' na *SQL Kullanıcı adı* ' nı ve **sunucu Kullanıcı hesabı parolasıyla** *parolayı* girin.
7. **Veritabanı sorgusu** düzenleme metin alanında, gerekli veritabanı alanlarını (Etiketler gibi hesaplanan alanlar da dahil olmak üzere) çıkaran sorguyu yapıştırın ve verileri istenen örnek boyutuna doğru örnekleyin.

İkili sınıflandırmanın bir örneği, doğrudan SQL veri ambarı veritabanından veri okumayı deneyin (nyctaxi_trip ve nyctaxi_fare tablo adlarını şema adı ve kılavuzınızda kullandığınız tablo adları ile değiştirmeyi unutmayın). Birden çok Lass sınıflandırması ve gerileme sorunları için benzer denemeleri oluşturulabilir.

![Azure ML eğitme][10]

> [!IMPORTANT]
> Önceki bölümlerde sağlanan modelleme veri ayıklama ve örnekleme sorgusu örneklerinde, **üç modellemeye yönelik tüm Etiketler sorguya dahil**edilmiştir. Modelleme alýþtýrmalarının her birinde önemli (gerekli) bir adım, diğer iki soruna ve diğer **hedef sızıntılara**yönelik gereksiz etiketleri **dışlayamazsınız** . Örneğin, ikili sınıflandırma kullanırken, **eğimli** etiketini kullanın ve alanları **İpucu\_sınıfı**, **tıp\_tutarı**ve **Toplam\_tutarı**hariç tutun. İkincisi, ücretli olduğunu gösterdiğinden bu yana hedef sızıntılardır.
>
> Gereksiz sütunları veya hedef sızıntılarını dışlamak için, [veri kümesi modülünde sütunları seç][select-columns] veya [verileri Düzenle][edit-metadata]' yi kullanabilirsiniz. Daha fazla bilgi için bkz. [veri kümesindeki sütunları seçme][select-columns] ve [meta veri][edit-metadata] başvuru sayfalarını düzenleme.
>
>

## <a name="mldeploy"></a>Azure Machine Learning modelleri dağıtma
Modeliniz hazırlandığınızda, doğrudan denemenizin içinden Web hizmeti olarak kolayca dağıtım yapabilirsiniz. Azure ML Web Hizmetleri 'ni dağıtma hakkında daha fazla bilgi için bkz. [Azure Machine Learning Web hizmeti dağıtma](../studio/deploy-a-machine-learning-web-service.md).

Yeni bir Web hizmeti dağıtmak için şunları yapmanız gerekir:

1. Puanlama denemesi oluşturun.
2. Web hizmetini dağıtın.

**Tamamlanmış** bir eğitim deneyminden bir Puanlama denemesi oluşturmak için, alt eylem çubuğunda **Puanlama denemesi oluştur** ' a tıklayın.

![Azure Puanlama][18]

Azure Machine Learning, eğitim denemesinin bileşenlerine dayalı olarak bir Puanlama denemesi oluşturmaya çalışacaktır. Özellikle, şu şekilde olur:

1. Eğitilen modeli kaydedin ve model eğitimi modüllerini kaldırın.
2. Beklenen giriş verisi şemasını temsil eden bir mantıksal **giriş bağlantı noktası** belirler.
3. Beklenen Web hizmeti çıkış şemasını temsil eden bir mantıksal **çıkış bağlantı noktası** belirler.

Puanlama denemesi oluşturulduğunda, gözden geçirin ve gerektiğinde ayarla ' yı yapın. Tipik bir ayar, giriş veri kümesini ve/veya sorgusunu, bu hizmet çağrıldığında kullanılabilir olmayacak şekilde, etiket alanlarını dışladığı bir şekilde değiştirmek olacaktır. Ayrıca, giriş veri kümesinin boyutunu azaltmak ve/veya birkaç kayıt için sorgu yapmak için yeterli bir uygulamadır. Çıkış bağlantı noktası için, tüm giriş alanlarını hariç tutmak ve yalnızca [veri kümesindeki sütunları seç][select-columns] modülünde yer alan Select sütunları kullanılarak **çıktıda puanlanmış** olan **olasılıkların** dahil olması yaygındır.

Örnek Puanlama denemesi aşağıdaki şekilde sunulmaktadır. Dağıtıma hazırsanız, alt eylem çubuğunda **Web hizmeti Yayımla** düğmesine tıklayın.

![Azure ML yayımlama][11]

## <a name="summary"></a>Özet
Bu adım adım öğreticide yaptığımız şeyleri yeniden kaplamanız için, bir Azure veri bilimi ortamı oluşturdunuz, büyük bir genel veri kümesiyle çalıştık ve bunu ekip veri bilimi Işlemi aracılığıyla, veri alma, model eğitimi ' ne kadar tüm yollar ve ardından Azure Machine Learning Web hizmeti dağıtımı.

### <a name="license-information"></a>Lisans bilgileri
Bu örnek izlenecek yol ve birlikte gelen betikler ve IPython Not defteri, Microsoft tarafından MıT lisansı kapsamında paylaşılır. Daha fazla ayrıntı için lütfen GitHub 'daki örnek kodun dizinindeki LICENSE. txt dosyasını denetleyin.

## <a name="references"></a>Başvurular
• [Andr, MONROY NYC TAXI geri dönüş sayfası](https://www.andresmh.com/nyctaxitrips/) • [Chris, NYC 'Nin TAXI veri yolculuğu](https://chriswhong.com/open-data/foil_nyc_taxi/) [](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
