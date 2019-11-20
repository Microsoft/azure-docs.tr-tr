---
title: Azure Data Lake Team Data Science Işlemiyle ölçeklenebilir veri bilimi
description: Veri kümesi üzerinde veri araştırması ve ikili sınıflandırma görevlerini yapmak için Azure Data Lake kullanma.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4f1f60ef50b65c13464e7a777e9b8ce66b5fa122
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492454"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Azure Data Lake ile ölçeklenebilir veri bilimi: uçtan uca bir anlatım
Bu izlenecek yol, bir ipucunun bir tarifeli havayolu tarafından ödenip ödenmediğini tahmin etmek üzere NYC TAXI seyahat ve tarifeli havayolu veri kümesinin bir örneği üzerinde veri keşif ve ikili sınıflandırma görevlerini yapmak için Azure Data Lake nasıl kullanacağınızı gösterir. [Ekip veri bilimi sürecinin](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), uçtan uca, veri alımı ile model eğitimi arasında ve ardından modeli yayımlayan bir Web hizmetinin dağıtımına ilişkin adımlarda size yol gösterir.

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
[Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) , veri bilimcilerinin herhangi bir boyut, şekil ve hızda veri depolamasını ve veri işleme, gelişmiş analiz ve makine öğrenimi modellemesini, bir ekonomik bir yöntemdir.   Yalnızca veri işlendiği zaman iş başına temelinde ödeme yaparsınız. Azure Data Lake Analytics, ölçeklenebilir dağıtılmış sorgu yeteneği sağlamak için SQL 'in açıklayıcı gücünden oluşan C# , SQL 'e yönelik bildirime dayalı bir dil olan U-SQL ' i içerir. Okuma sırasında şema uygulayarak yapılandırılmamış verileri işleyebilir, özel mantık ve Kullanıcı tanımlı işlevler (UDF 'ler) ekleyebilir ve ölçekte nasıl yürütülebilecek üzerinde ayrıntılı denetim sağlamak için genişletilebilirlik içerir. U-SQL ' ın arkasındaki tasarım felseı hakkında daha fazla bilgi için bkz. [Visual Studio blog gönderisi](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Data Lake Analytics ayrıca Cortana Analytics Suite’in de önemli bir parçasıdır ve Azure SQL Veri Ambarı, Power BI ve Veri Fabrikası ile birlikte çalışır. Bu, size büyük bir bulut büyük veri ve gelişmiş analiz platformu sağlar.

Bu izlenecek yol, veri bilimi işlem görevlerini tamamlamak için gereken önkoşulları ve kaynakları yüklemeyi açıklayarak başlar. Ardından, veri işleme adımlarını U-SQL kullanarak özetler ve tahmine dayalı modelleri derlemek ve dağıtmak için Azure Machine Learning Studio (klasik) ile Python ve Hive kullanımını göstererek sonlanır.

### <a name="u-sql-and-visual-studio"></a>U-SQL ve Visual Studio
Bu izlenecek yol, Visual Studio kullanarak veri kümesini işlemek için U-SQL betiklerini düzenlemenizi önerir. U-SQL betikleri burada açıklanmış ve ayrı bir dosyada sunulmaktadır. Süreç, verileri geri almaya, araştırmaya ve örneklemeye dahildir. Ayrıca, Azure portal bir U-SQL komut dosyası oluşturma işinin nasıl çalıştırılacağını gösterir. Azure Machine Learning Studio ' de bir ikili sınıflandırma modelinin oluşturulmasını ve dağıtılmasını kolaylaştırmak amacıyla ilişkili HDInsight kümesindeki veriler için Hive tabloları oluşturulur.

### <a name="python"></a>Python
Bu izlenecek yol Ayrıca, Azure Machine Learning Studio ile Python kullanarak tahmine dayalı bir modelin nasıl oluşturulduğunu ve dağıtılacağını gösteren bir bölüm içerir. Bu işlemdeki adımlarla ilgili Python betiklerini içeren bir Jupyter Not defteri sağlar. Not defteri, burada özetlenen ikili sınıflandırma modelinin yanı sıra birden çok sınıf sınıflandırması ve regresyon modelleme gibi bazı ek özellik Mühendisliği adımları ve model oluşturma için kod içerir. Gerileme görevi, diğer ipucu özelliklerine göre ipucu miktarını tahmin etmek için kullanılır.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning Studio (klasik), tahmine dayalı modeller derlemek ve dağıtmak için kullanılır. Bu iki yaklaşım kullanılarak yapılır: ilk olarak Python betiklerine ve sonra HDInsight (Hadoop) kümesinde Hive tablolarıyla.

### <a name="scripts"></a>Betikler
Bu kılavuzda yalnızca asıl adımlar özetlenmiştir. Tam **U-SQL betiğini** ve **Jupyter Notebook** [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough)' dan indirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Bu konulara başlamadan önce aşağıdakilere sahip olmanız gerekir:

* Azure aboneliği. Henüz bir tane yoksa, bkz. [Azure Ücretsiz deneme sürümü](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Önerilen Visual Studio 2013 veya üzeri. Bu sürümlerden biri zaten yüklü değilse, [Visual Studio Community](https://www.visualstudio.com/vs/community/)'den ücretsiz bir topluluk sürümü indirebilirsiniz.

> [!NOTE]
> Visual Studio yerine, Azure Data Lake sorguları göndermek için Azure portal de kullanabilirsiniz. Yönergeler, hem Visual Studio hem de Portal üzerinde **U-SQL Ile işlem verileri**başlıklı bölümde verilmiştir.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Azure Data Lake için veri bilimi ortamını hazırlama
Bu izlenecek yol için veri bilimi ortamını hazırlamak üzere aşağıdaki kaynakları oluşturun:

* Azure Data Lake Store (ADLS)
* Azure Data Lake Analytics (ADLA)
* Azure Blob depolama hesabı
* Azure Machine Learning Studio (klasik) hesap
* Visual Studio için Azure Data Lake Araçları (önerilir)

Bu bölüm, bu kaynakların her birini oluşturma hakkında yönergeler sağlar. Hive tablolarını Python yerine Azure Machine Learning ile kullanmayı tercih ederseniz, bir model oluşturmak için bir HDInsight (Hadoop) kümesi de sağlamanız gerekir. Bu alternatif yordam, seçenek 2 bölümünde açıklanmıştır.


> [!NOTE]
> **Azure Data Lake Store** ayrı olarak veya varsayılan depolama alanı olarak **Azure Data Lake Analytics** oluşturduğunuzda oluşturulabilir. Bu kaynakların her birini oluşturmak için yönergelere ayrı olarak başvurulur, ancak Data Lake depolama hesabının ayrı ayrı oluşturulmaması gerekir.
>
>

### <a name="create-an-azure-data-lake-store"></a>Azure Data Lake Store oluşturma


[Azure Portal](https://portal.azure.com)BIR ADLS oluşturun. Ayrıntılar için bkz. [Azure Portal kullanarak Data Lake Store HDInsight kümesi oluşturma](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Burada açıklanan **Isteğe bağlı yapılandırma** dikey penceresinin **veri kaynağı** dikey penceresinde küme AAD kimliği ' ni ayarladığınızdan emin olun.

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Azure Data Lake Analytics hesabı oluşturma
[Azure Portal](https://portal.azure.com)bir ADLA hesabı oluşturun. Ayrıntılar için bkz. [öğretici: Azure Portal kullanarak Azure Data Lake Analytics kullanmaya başlama](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Azure Blob depolama hesabı oluşturma
[Azure Portal](https://portal.azure.com)bir Azure Blob depolama hesabı oluşturun. Ayrıntılar için [Azure depolama hesapları hakkında](../../storage/common/storage-create-storage-account.md)bölümündeki depolama hesabı oluşturma bölümüne bakın.

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-classic-account"></a>Azure Machine Learning Studio (klasik) hesabı ayarlama
[Azure Machine Learning Studio](https://azure.microsoft.com/services/machine-learning/) sayfasından Azure Machine Learning Studio (klasik) kaydolun. **Şimdi kullanmaya** başlayın düğmesine tıklayın ve ardından bir "ücretsiz çalışma alanı" veya "standart çalışma alanı" seçin. Artık Azure Machine Learning Studio 'da denemeleri oluşturmaya hazır olursunuz.

### <a name="install-azure-data-lake-tools-recommended"></a>Azure Data Lake araçlarını yükler [önerilir]
[Visual Studio için Azure Data Lake araçları](https://www.microsoft.com/download/details.aspx?id=49504)' dan Visual Studio sürümünüz Için Azure Data Lake araçları 'nı yükler.

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Yükleme başarıyla tamamlandıktan sonra Visual Studio 'Yu açın. Üstteki menünün Data Lake sekmesini görmeniz gerekir. Azure hesabınızda oturum açtığınızda Azure kaynaklarınızın sol bölmede görünmesi gerekir.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>NYC TAXI gidiş veri kümesi
Burada kullanılan veri kümesi, genel kullanıma açık bir veri kümesidir; [NYC TAXI gidiş veri kümesi](https://www.andresmh.com/nyctaxitrips/). NYC TAXI seyahat verileri yaklaşık 20 GB sıkıştırılmış CSV dosyasından oluşur (~ 48 GB sıkıştırılmamış), her seyahat için ' den fazla ayrı gidiş ve ödenen far173.000.000 ktan daha fazla kayıt yapılır. Her seyahat kaydı, toplama ve bırakma konumlarını ve zamanlarını, anonim olarak bulunan Hack (sürücü) lisans numarasını ve medalon (TAXI 'nin benzersiz KIMLIĞI) numarasını içerir. Veriler, 2013 yılında yapılan tüm döngüleri kapsamakta ve her ay için aşağıdaki iki veri kümelerinde sunulmaktadır:

' Trip_data ' CSV, pascuların sayısı, toplama ve Dropoff noktaları, seyahat süresi ve seyahat uzunluğu gibi seyahat ayrıntılarını içerir. Aşağıda birkaç örnek kayıt verilmiştir:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



' Trip_fare ' CSV, ödeme türü, tarifeli havayolu tutarı, ek ücret, vergiler, ipuçları ve Tolls ve ödenen toplam miktar gibi her seyahat için ödenen tarifeli havayolu 'ın ayrıntılarını içerir. Aşağıda birkaç örnek kayıt verilmiştir:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Seyahat\_veri ve seyahat\_tarifeli havayolu katılacak benzersiz anahtar aşağıdaki üç alandan oluşur: medtalon, Hack\_lisansı ve toplama\_tarih saat. Ham CSV dosyalarına ortak bir Azure Storage blobundan erişilebilir. Bu birleşimin U-SQL betiği, [yolculuğa katılmayı ve tarifeli havayolu tabloları](#join) bölümünde bulunur.

## <a name="process-data-with-u-sql"></a>U-SQL ile verileri işleme
Bu bölümde gösterilen veri işleme görevleri, verileri kullanıma almak, kaliteyi, araştırmayı ve örnekleme işlemlerini içerir. Yolculuğa katılıp tarifeli havayolu tabloları da gösterilir. Son bölümde Azure portal bir U-SQL komut dosyalı işi çalıştırma gösterilmektedir. Her alt bölümünün bağlantıları şunlardır:

* [Veri alımı: genel Blobun verileri okuma](#ingest)
* [Veri kalitesi denetimleri](#quality)
* [Veri araştırması](#explore)
* [Seyahat ve tarifeli havayolu tablolarına katılarak](#join)
* [Veri örnekleme](#sample)
* [U-SQL işlerini Çalıştır](#run)

U-SQL betikleri burada açıklanmış ve ayrı bir dosyada sunulmaktadır. Tam **U-SQL betiklerini** [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough)'dan indirebilirsiniz.

U-SQL ' i çalıştırmak için, Visual Studio 'yu açın, **Dosya--> New--> projesi**' ne tıklayın, **U-SQL projesi**' ni seçin ve bir klasöre kaydedin.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Visual Studio yerine U-SQL yürütmek için Azure portalını kullanabilirsiniz. Portalda Azure Data Lake Analytics kaynağına gidebilir ve sorguları doğrudan aşağıdaki şekilde gösterildiği gibi gönderebilirsiniz:
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>Veri alımı: genel Blobun verileri okuma

Azure Blob 'daki verilerin konumu, **wasb://container\_name\@blob\_depolama\_hesabı\_Name.blob.Core.Windows.net/BLOB_NAME** ve **ayıklayıcıları. csv ()** kullanılarak ayıklanabilen olarak başvurulur. Değişken\_adı\@blob\_depolama\_hesap\_adına sahip bir kapsayıcı adı ve depolama hesabı adınızı aşağıdaki betiklerin yerine koyun. Dosya adları aynı biçimde olduğundan, tüm 12 seyahat dosyalarında okumak için **\*\}. csv dosyasını\_seyahat\_verilerini \{** kullanabilirsiniz.

    ///Read in Trip data
    @trip0 =
        EXTRACT
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

İlk satırda üstbilgiler olduğundan, üstbilgileri kaldırmanız ve sütun türlerini uygun olanlarla değiştirmeniz gerekir. İşlenen verileri **swebbir://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ kullanarak Azure Data Lake Storage veya Azure Blob depolama hesabı ile **blob_storage_account_name b:-container_adı\@. blob. Core. Windows. net/BLOB_NAME**kullanarak kaydedebilirsiniz.

    // change data types
    @trip =
        SELECT
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        DateTime.Parse(pickup_datetime) AS pickup_datetime,
        DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
        Int32.Parse(passenger_count) AS passenger_count,
        Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
        Double.Parse(trip_distance) AS trip_distance,
        (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
        (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
        (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
        (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

    ////output data to ADL
    OUTPUT @trip
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
    USING Outputters.Csv();

    ////Output data to blob
    OUTPUT @trip
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
    USING Outputters.Csv();

Benzer şekilde, tarifeli havayolu veri kümelerinde okuyabilirsiniz. Azure Data Lake Store sağ tıklayın, verilerinizi **Azure Portal--> Veri Gezgini** veya Visual Studio Içinde **Dosya Gezgini** ' nde bulabilirsiniz.

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="quality"></a>Veri kalitesi denetimleri
Seyahat ve tarifeli havayolu tabloları okuduktan sonra, veri kalitesi denetimleri aşağıdaki şekilde yapılabilir. Elde edilen CSV dosyaları Azure Blob depolama veya Azure Data Lake Store çıktı olabilir.

Medalons sayısını ve benzersiz sayıda medalons sayısını bulun:

    ///check the number of medallions and unique number of medallions
    @trip2 =
        SELECT
        medallion,
        vendor_id,
        pickup_datetime.Month AS pickup_month
        FROM @trip;

    @ex_1 =
        SELECT
        pickup_month,
        COUNT(medallion) AS cnt_medallion,
        COUNT(DISTINCT(medallion)) AS unique_medallion
        FROM @trip2
        GROUP BY pickup_month;
        OUTPUT @ex_1
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
    USING Outputters.Csv();

100 ' den fazla seyahatte bulunan bu medalleri bulun:

    ///find those medallions that had more than 100 trips
    @ex_2 =
        SELECT medallion,
               COUNT(medallion) AS cnt_medallion
        FROM @trip2
        //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
        GROUP BY medallion
        HAVING COUNT(medallion) > 100;
        OUTPUT @ex_2
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
    USING Outputters.Csv();

Pickup_longitude açısından bu geçersiz kayıtları bul:

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv();

Bazı değişkenlerin eksik değerlerini bul:

    //check missing values
    @res =
        SELECT *,
               (medallion == null? 1 : 0) AS missing_medallion
        FROM @trip;

    @trip_summary6 =
        SELECT
            vendor_id,
        SUM(missing_medallion) AS medallion_empty,
        COUNT(medallion) AS medallion_total,
        COUNT(DISTINCT(medallion)) AS medallion_total_unique
        FROM @res
        GROUP BY vendor_id;
    OUTPUT @trip_summary6
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
    USING Outputters.Csv();



### <a name="explore"></a>Veri araştırması
Verileri daha iyi anlamak için aşağıdaki betiklerle veri araştırmayı yapın.

Eğimli ve eğimli olmayan dönüşlerin dağılımını bulun:

    ///tipped vs. not tipped distribution
    @tip_or_not =
        SELECT *,
               (tip_amount > 0 ? 1: 0) AS tipped
        FROM @fare;

    @ex_4 =
        SELECT tipped,
               COUNT(*) AS tip_freq
        FROM @tip_or_not
        GROUP BY tipped;
        OUTPUT @ex_4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
    USING Outputters.Csv();

Kapalı değerler içeren ipucu miktarının dağılımını bulun: 0, 5, 10 ve 20 dolar.

    //tip class/range distribution
    @tip_class =
        SELECT *,
               (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
        FROM @fare;
    @ex_5 =
        SELECT tip_class,
               COUNT(*) AS tip_freq
        FROM @tip_class
        GROUP BY tip_class;
        OUTPUT @ex_5
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
    USING Outputters.Csv();

Temel seyahat mesafesinin istatistiklerini bulun:

    // find basic statistics for trip_distance
    @trip_summary4 =
        SELECT
            vendor_id,
            COUNT(*) AS cnt_row,
            MIN(trip_distance) AS min_trip_distance,
            MAX(trip_distance) AS max_trip_distance,
            AVG(trip_distance) AS avg_trip_distance
        FROM @trip
        GROUP BY vendor_id;
    OUTPUT @trip_summary4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
    USING Outputters.Csv();

Yüzdebirlik değeri seyahat mesafesini bulun:

    // find percentiles of trip_distance
    @trip_summary3 =
        SELECT DISTINCT vendor_id AS vendor,
                        PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
        FROM @trip;
       // group by vendor_id;
    OUTPUT @trip_summary3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
    USING Outputters.Csv();


### <a name="join"></a>Seyahat ve tarifeli havayolu tablolarına katılarak
Seyahat ve tarifeli havayolu tabloları, medalon, hack_license ve pickup_time tarafından birleştirilebilecek.

    //join trip and fare table

    @model_data_full =
    SELECT t.*,
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

    //// output to blob
    OUTPUT @model_data_full
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
    USING Outputters.Csv();

    ////output data to ADL
    OUTPUT @model_data_full
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
    USING Outputters.Csv();


Her bir yolcular sayısı düzeyi için kayıt sayısını, ortalama tip miktarını, tıp tutarının varyansını, eğimli dönüşlerin yüzdesini hesaplayın.

    // contingency table
    @trip_summary8 =
        SELECT passenger_count,
               COUNT(*) AS cnt,
               AVG(tip_amount) AS avg_tip_amount,
               VAR(tip_amount) AS var_tip_amount,
               SUM(tipped) AS cnt_tipped,
               (float)SUM(tipped)/COUNT(*) AS pct_tipped
        FROM @model_data_full
        GROUP BY passenger_count;
        OUTPUT @trip_summary8
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
    USING Outputters.Csv();


### <a name="sample"></a>Veri örnekleme
İlk olarak, birleştirilen tablodaki verilerin% 0,1 ' u rastgele seçin:

    //random select 1/1000 data for modeling purpose
    @addrownumberres_randomsample =
    SELECT *,
            ROW_NUMBER() OVER() AS rownum
    FROM @model_data_full;

    @model_data_random_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_randomsample
    WHERE rownum % 1000 == 0;

    OUTPUT @model_data_random_sample_1_1000
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
    USING Outputters.Csv();

Sonra ikili değişken tip_class ile örnek örnekleme yapın:

    //stratified random select 1/1000 data for modeling purpose
    @addrownumberres_stratifiedsample =
    SELECT *,
            ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
    FROM @model_data_full;

    @model_data_stratified_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_stratifiedsample
    WHERE rownum % 1000 == 0;
    //// output to blob
    OUTPUT @model_data_stratified_sample_1_1000
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv();
    ////output data to ADL
    OUTPUT @model_data_stratified_sample_1_1000
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv();


### <a name="run"></a>U-SQL işlerini Çalıştır
U-SQL betiklerini düzenlemenizi bitirdiğinizde, Azure Data Lake Analytics hesabınızı kullanarak bunları sunucuya gönderebilirsiniz. **Data Lake**, **işi gönder**' e tıklayın, **analiz hesabınızı**seçin, **paralellik**' i seçin ve **Gönder** düğmesine tıklayın.

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

İş başarılı bir şekilde karmaşıkmış olduğunda, işin durumu izleme için Visual Studio 'da görüntülenir. İş çalışmayı bitirdikten sonra, iş yürütme işlemini de yeniden oynamanın yanı sıra iş verimliliğini artırmaya yönelik performans sorunlarını da öğrenebilirsiniz. Ayrıca, U-SQL işlerinizin durumunu denetlemek için Azure portal ' a gidebilirsiniz.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Artık çıktı dosyalarını Azure Blob depolama veya Azure portal denetleyebilirsiniz. Modelimiz için bir sonraki adımda bulunan örnek verileri kullanın.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Azure Machine Learning modelleri derleme ve dağıtma
Oluşturmak için Azure Machine Learning verileri çekmek üzere iki seçenek mevcuttur ve

* İlk seçenekte, bir Azure Blob 'Una yazılmış olan örneklenmiş verileri kullanırsınız (Yukarıdaki **veri örnekleme** adımında) ve Azure Machine Learning modelleri derlemek ve dağıtmak için Python kullanın.
* İkinci seçenekte, Azure Data Lake verileri doğrudan Hive sorgusu kullanarak sorgulayın. Bu seçenek, yeni bir HDInsight kümesi oluşturmanızı veya Hive tablolarının Azure Data Lake Storage içindeki NY vergi verilerini işaret ettiği mevcut bir HDInsight kümesini kullanmanızı gerektirir.  Bu seçeneklerin her ikisi de aşağıdaki bölümlerde ele alınmıştır.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Seçenek 1: makine öğrenimi modellerini derlemek ve dağıtmak için Python kullanma
Python kullanarak makine öğrenimi modelleri derlemek ve dağıtmak için yerel makinenizde veya Azure Machine Learning Studio bir Jupyter Notebook oluşturun. [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) 'da belirtilen Jupyter Notebook, araştırılacak, veri, özellik Mühendisliği, modelleme ve dağıtım için tam kodu içerir. Bu makalede, yalnızca modelleme ve dağıtım ele alınmıştır.

### <a name="import-python-libraries"></a>Python kitaplıklarını içeri aktarma
Örnek Jupyter Notebook veya Python betik dosyasını çalıştırmak için, aşağıdaki Python paketleri gereklidir. Azure Machine Learning Not Defteri hizmetini kullanıyorsanız, bu paketler önceden yüklenmiştir.

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random
    import sklearn
    from sklearn.linear_model import LogisticRegression
    from sklearn.cross_validation import train_test_split
    from sklearn import metrics
    from __future__ import division
    from sklearn import linear_model
    from azureml import services


### <a name="read-in-the-data-from-blob"></a>Blobun veride okuma
* bağlantı dizesi

        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Metin olarak oku

        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)
* Sütun adları ve ayrı sütunlar ekleme

        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* Bazı sütunları sayısal olarak değiştirme

        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Makine öğrenimi modelleri oluşturma
Burada, bir seyahati eğimli olup olmadığını tahmin etmek için bir ikili sınıflandırma modeli derleyebilirsiniz. Jupyter Notebook diğer iki modeli bulabilirsiniz: birden çok Lass sınıflandırması ve regresyon modelleri.

* İlk olarak, scikit-model öğrenme 'da kullanılabilecek kukla değişkenler oluşturmanız gerekir

        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* Modelleme için veri çerçevesi oluşturma

        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

        X = data.iloc[:,1:]
        Y = data.tipped
* Eğitim ve test 60-40 bölünmüş

        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* Eğitim kümesinde Lojistik gerileme

        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)

       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Test veri kümesini Puanlama

        Y_test_pred = logit_fit.predict(X_test)
* Değerlendirme ölçümlerini hesapla

        fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
        print fpr_train, tpr_train, thresholds_train

        fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred)
        print fpr_test, tpr_test, thresholds_test

        #AUC
        print metrics.auc(fpr_train,tpr_train)
        print metrics.auc(fpr_test,tpr_test)

        #Confusion Matrix
        print metrics.confusion_matrix(Y_train,Y_train_pred)
        print metrics.confusion_matrix(Y_test,Y_test_pred)

       ![c2](./media/data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>Web hizmeti API 'SI oluşturun ve Python 'da kullanın
Derlendikten sonra makine öğrenimi modelini kullanıma almak istiyorsunuz. İkili lojistik modeli örnek olarak burada kullanılır. Yerel makinenizde scikit-öğren sürümünün 0.15.1 olduğundan emin olun. Azure Machine Learning Studio kullanıyorsanız bu konuda endişelenmeniz gerekmez.

* Azure Machine Learning Studio (klasik) ayarlarından çalışma alanı kimlik bilgilerinizi bulun. Azure Machine Learning Studio, **ayarlar** --> **ad** --> **Yetkilendirme belirteçleri**' ne tıklayın.

    ![C3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* Web hizmeti oluştur

        @services.publish(workspaceid, auth_token)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)
* Web hizmeti kimlik bilgilerini al

        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key

        print url
        print api_key

        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* Web hizmeti API 'sini çağırın. Önceki adımdan sonra 5-10 saniye beklemeniz gerekir.

        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>2\. seçenek: modelleri doğrudan Azure Machine Learning oluşturma ve dağıtma
Azure Machine Learning Studio (klasik), doğrudan Azure Data Lake Store verileri okuyabilir ve ardından modeller oluşturup dağıtmak için kullanılabilir. Bu yaklaşım Azure Data Lake Store işaret eden bir Hive tablosu kullanır. Bunun için, Hive tablosunun oluşturulduğu ayrı bir Azure HDInsight kümesinin sağlanması gerekir. Aşağıdaki bölümlerde bunun nasıl yapılacağı gösterilmektedir.

### <a name="create-an-hdinsight-linux-cluster"></a>HDInsight Linux kümesi oluşturma
[Azure Portal](https://portal.azure.com)bir HDInsight kümesi (Linux) oluşturun. Ayrıntılar için [Azure Portal kullanarak Data Lake Store bir HDInsight kümesi oluşturma](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)konusunun **Azure Data Lake Store erişimi olan HDInsight kümesi oluşturma** bölümüne bakın.

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>HDInsight 'ta Hive tablosu oluşturma
Şimdi, önceki adımda Azure Data Lake Store depolanan verileri kullanarak HDInsight kümesinde Azure Machine Learning Studio (klasik) olarak kullanılacak Hive tabloları oluşturursunuz. Oluşturulan HDInsight kümesine gidin. **Ayarlar** --> **ÖZELLIKLER** --> **küme AAD kimliği** --> **adls erişimi**' ne tıklayın, Azure Data Lake Store hesabınızın okuma, yazma ve yürütme haklarıyla birlikte listesine eklendiğinden emin olun.

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Ardından **Ayarlar** düğmesinin yanındaki **Pano** ' ya ve bir pencere açılır. Sayfanın sağ üst köşesindeki **Hive görünümü** ' ne tıklayın ve **sorgu düzenleyicisini**görmeniz gerekir.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Bir tablo oluşturmak için aşağıdaki Hive betiklerine yapıştırın. Veri kaynağının konumu Azure Data Lake Store başvuruya şu şekilde başvuruyor: **adl://data_lake_store_name.azuredatalakestore.net: 443/klasör_adı/dosya_adı**.

    CREATE EXTERNAL TABLE nyc_stratified_sample
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string,
      payment_type string,
      fare_amount string,
      surcharge string,
      mta_tax string,
      tolls_amount string,
      total_amount string,
      tip_amount string,
      tipped string,
      tip_class string,
      rownum string
      )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';


Sorgu çalışmayı bitirdiğinde şöyle bir sonuç görmeniz gerekir:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio modelleri derleme ve dağıtma
Artık Azure Machine Learning bir tıp ödenip ödenmediğini tahmin eden bir model oluşturmaya ve dağıtmaya hazırsınız. Bu ikili sınıflandırma (tıp veya Not) sorununa yönelik örnek veriler kullanılmak üzere kullanılabilir. Birden çok Lass sınıflandırması (tip_class) ve gerileme (tip_amount) kullanan tahmine dayalı modeller Azure Machine Learning Studio de oluşturulup dağıtılabilir, ancak burada yalnızca ikili sınıflandırma modeli kullanılarak büyük/küçük harf işleme yöntemi gösterilmektedir.

1. Veri **giriş ve çıkış** bölümünde bulunan **veri içeri aktarma** modülünü kullanarak verileri Azure Machine Learning Studio (klasik) olarak alın. Daha fazla bilgi için bkz. [veri modülü başvurusunu Içeri aktarma](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) sayfası.
2. **Özellikler** panelinde **veri kaynağı** olarak **Hive sorgusu** ' nu seçin.
3. Aşağıdaki Hive betiğini **Hive veritabanı sorgu** düzenleyicisine yapıştırın

        select * from nyc_stratified_sample;
4. HDInsight kümesinin URI 'sini girin (Bu Azure portal bulunabilir), Hadoop kimlik bilgileri, çıktı verilerinin konumu ve Azure depolama hesabı adı/anahtarı/kapsayıcı adı.

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

Aşağıdaki şekilde, Hive tablosundan verileri okurken ikili sınıflandırma denemesinin bir örneği gösterilmektedir:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

Deneme oluşturulduktan sonra **Web hizmeti** --> tahmine **dayalı Web hizmeti** ayarla ' ya tıklayın.

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Otomatik olarak oluşturulan Puanlama denemesini çalıştırın, tamamlandığında **Web Hizmeti Dağıt** ' a tıklayın.

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Web hizmeti panosu kısa süre içinde görüntülenir:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Özet
Bu izlenecek yolu tamamlayarak, Azure Data Lake içinde ölçeklenebilir uçtan uca çözümler oluşturmak için bir veri bilimi ortamı oluşturdunuz. Bu ortam, veri bilimi sürecinin kurallı adımlarında, model eğitimi aracılığıyla veri edinmesinin ve daha sonra modelin bir Web hizmeti olarak dağıtımına kadar büyük bir genel veri kümesini çözümlemek için kullanıldı. U-SQL, verileri işlemek, araştırmak ve örneklemek için kullanıldı. Python ve Hive, tahmine dayalı modeller derlemek ve dağıtmak için Azure Machine Learning Studio (klasik) ile kullanılır.

## <a name="whats-next"></a>Sırada ne var?
[Ekip veri bilimi işlemi (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) için öğrenme yolu, gelişmiş analiz işlemindeki her adımı açıklayan konuların bağlantılarını sağlar. [Team Data Science işlem izlenecek yolları](walkthroughs.md) sayfasında, çeşitli tahmine dayalı analiz senaryolarında kaynakların ve hizmetlerin nasıl kullanılacağını gösteren bir dizi izlenecek yol vardır:

* [Takım veri bilimi süreci: SQL veri ambarı kullanma](sqldw-walkthrough.md)
* [Team Data Science süreci: HDInsight Hadoop kümelerini kullanma](hive-walkthrough.md)
* [Team Data Science Işlemi: SQL Server kullanma](sql-walkthrough.md)
* [Azure HDInsight 'ta Spark kullanarak veri bilimi Işlemine genel bakış](spark-overview.md)
