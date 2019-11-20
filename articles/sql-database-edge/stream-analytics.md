---
title: SQL veritabanı DAC paketini kullanma ve Azure SQL veritabanı Edge ile Stream Analytics işleri | Microsoft Docs
description: SQL veritabanı Edge 'de Stream Analytics işlerini kullanma hakkında bilgi edinin
keywords: SQL veritabanı Edge, Stream Analytics, SqlPackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: c3ed84e06f693925ed8b484070616e223929e401
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108753"
---
# <a name="using-sql-database-dac-package-and-stream-analytics-job-with-sql-database-edge"></a>SQL veritabanı ile SQL veritabanı DAC paketi ve Stream Analytics işi kullanma

Azure SQL veritabanı Edge önizlemesi, IoT ve Edge dağıtımları için iyileştirilmiş bir ilişkisel veritabanı altyapısıdır. Bu, sektör lideri performans, güvenlik ve sorgu işleme özellikleri sağlayan Microsoft SQL Server veritabanı altyapısının en son sürümlerinde oluşturulmuştur. Azure SQL veritabanı Edge, SQL Server sektör lideri ilişkisel veritabanı yönetim özelliklerinin yanı sıra gerçek zamanlı analiz ve karmaşık olay işleme için yerleşik akış özelliği sağlar.

Azure SQL veritabanı Edge Ayrıca, SQL veritabanı kenarının dağıtımı sırasında kullanıcıların bir [SQL VERITABANı dac](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) paketi dağıtmasını sağlayan, SqlPackage. exe ' nin yerel bir uygulamasını sağlar.

Azure SQL veritabanı Edge, IoT Edge modülünün *istenen özellikler* seçeneği aracılığıyla isteğe bağlı iki parametre sunar.

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Alan | Açıklama |
|------|-------------|
| SQLPackage | SQL veritabanı DAC paketini içeren *. zip dosyası için Azure Blob depolama URI 'SI.
| Aşama Jobınfo | ASA Edge işi için Azure Blob depolama URI 'SI. ASA Edge işini yayımlama hakkında daha fazla bilgi için [SQL veritabanı Edge için BIR asa Edge Işi yayımlama](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge)konusuna bakın.

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>SQL veritabanı kenarıyla SQL veritabanı DAC paketlerini kullanma

SQL veritabanı kenarıyla SQL veritabanı DAC paketi (*. dacpac) kullanmak için lütfen aşağıda belirtilen adımları izleyin.

1. Bir SQL veritabanı DAC paketi oluşturun veya ayıklayın. Mevcut bir SQL veritabanı için bir DacPac oluşturmak üzere [mevcut bir VERITABANıNDAN dac ayıklama](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) bölümünde bahsedilen kavramları kullanabilirsiniz.

2. * *. Dacpac* kodu ve bir Azure Blob depolama hesabına yükleyin. Azure Blob depolamaya dosya yükleme hakkında daha fazla bilgi için bkz. [Azure Portal blob 'Ları yükleme, indirme ve listeleme](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Azure portal kullanarak ZIP dosyası için bir SAS imzası oluşturun. Daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) ile erişim temsilcisi](../storage/common/storage-sas-overview.md).

4. SQL veritabanı Edge modülü yapılandırmasını DAC paketi için SAS URI 'sini içerecek şekilde güncelleştirin. SQL veritabanı Edge modülünü güncelleştirmek için

    1. Azure portal, IoT Hub dağıtımınıza gidin.

    2. Sol taraftaki bölmede **IoT Edge**' ye tıklayın.

    3. **IoT Edge** sayfasında SQL veritabanı Edge modülünün dağıtıldığı IoT Edge bulup tıklayın.

    4. Cihaz aygıtı *IoT Edge* sayfasında, **modülü ayarla**' ya tıklayın. 

    5. **Modül ayarla** sayfasında SQL veritabanı Edge modülüne göre *Yapılandır* ' a tıklayın. 

    6. **IoT Edge özel modüller** bölmesinde, *Modül Ikizi istenen özelliklerini ayarla* ' yı seçin ve ardından istediğiniz özellikleri aşağıdaki örnekte gösterildiği gıbı SqlPackage seçeneğinin URI 'sini içerecek şekilde güncelleştirin. 

        > [!NOTE]
        > Aşağıdaki SAS URI 'SI yalnızca gösterim amaçlıdır. Lütfen URI 'yi dağıtımınızdaki gerçek URI ile değiştirin.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. **Kaydet**’e tıklayın.

    8. **Modülleri ayarla** sayfasında *İleri*' ye tıklayın.

    9. **Modülleri ayarla** sayfasında *İleri* ' ye ve ardından **Gönder**' e tıklayın.

5. Modül güncelleştirmesini gönderin, dacpac dosyası indirilir, sıkıştırıldı ve SQL veritabanı Edge örneğine göre dağıtılır.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>SQL veritabanı kenarıyla akış işlerini kullanma

Azure SQL veritabanı ucunun, Stream Analytics çalışma zamanının yerel bir uygulamasına sahip olması. Bu, kullanıcıların bir Azure Stream Analytics Edge işi oluşturmalarına ve bu işi SQL veritabanı Edge akış işi olarak dağıtmasına olanak sağlar. Stream Analytics Edge işi oluşturmak için aşağıdaki adımları izleyin.

1. Önizleme [URL 'sini](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)kullanarak Azure Portal gidin. Bu önizleme URL 'SI kullanıcıların Stream Analytics Edge işi için SQL veritabanı çıktısını yapılandırmalarına olanak sağlar.

2. Yeni bir **IoT Edge üzerinde Azure Stream Analytics** işi oluşturun ve ana bilgisayar hedefleme **ucunu**seçin.

3. Azure Stream Analytics işi için *giriş* ve *Çıkış* tanımlayın. Her SQL çıktısı (aşağıda yapılandırılan), veritabanı içindeki tek bir tabloya bağlıdır. Verilerin birden çok tabloya akışını sağlamak için birden çok SQL veritabanı çıkışı oluşturmanız gerekir. SQL çıktıları farklı veritabanlarına işaret etmek üzere yapılandırılabilir.

    *Giriş-kenar işinin girişi olarak EdgeHub ' ı seçin ve kaynak bilgilerini girin.*

    *Çıkış-SQL veritabanını çıkış olarak seç, "SQL veritabanı ayarlarını el ile sağlayın" ve veritabanı ve tablo için yapılandırma ayrıntılarını sağlayın.*

    |Alan      | Açıklama |
    |---------------|-------------|
    |Çıktı diğer adı | Çıkış diğer adının adı.|
    |Veritabanı | SQL veritabanının adı. Bunun SQL veritabanı Edge örneğinde bulunan geçerli bir veritabanı adı olması gerekir.|
    |Sunucu adı | SQL örneği için ad (veya IP adresi) ve bağlantı noktası numarası ayrıntıları. Bir SQL veritabanı Edge dağıtımı için sunucu adı olarak **TCP:., 1433** kullanabilirsiniz.|
    |Kullanıcı adı | Yukarıda belirtilen veritabanına veri okuyucu ve veri yazıcı erişimi olan SQL oturum açma hesabı.|
    |Parola | Yukarıda bahsedilen SQL oturum açma hesabının parolası.|
    |Tablo | Akış işi için çıkış olacak tablonun adı.|
    |Bölümlendirmeyi devralma| Bu SQL çıkış yapılandırma seçeneği, önceki sorgu adımlarınızın veya girişinin bölümleme düzeninin devralınmasını mümkün. Bu etkinken, disk tabanlı bir tabloya yazma ve işiniz için tamamen paralel topolojiye sahip olmak için daha iyi aktarım hızı görmeniz beklenir.|
    |Toplu iş boyutu| Toplu iş boyutu, her toplu ekleme hareketiyle gönderilen en fazla kayıt sayısıdır.|

    Örnek giriş/çıkış yapılandırması aşağıda verilmiştir:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output :
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > Azure Stream Analytics için SQL çıkış bağdaştırıcısı hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'na Azure Stream Analytics çıktısı](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Edge işi için ASA iş sorgusunu tanımlayın. Bu sorgu, sorguda giriş ve çıkış adları olarak tanımlanmış giriş/çıkış diğer adlarını kullanmalıdır. Daha fazla bilgi için bkz. [Stream Analytics sorgu dili başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Edge işi için depolama hesabı ayarlarını belirleyin. Depolama hesabı, kenar işi için yayımlama hedefi olarak kullanılır.

6. Yapılandır altında Yayımla ' yı seçin ve Yayımla düğmesine tıklayın. SQL veritabanı Edge modülü ile kullanmak için SAS URL 'sini kaydedin.

### <a name="deploy-the-stream-analytics-edge-job-to-the-sql-database-edge"></a>Stream Analytics Edge işini SQL veritabanı kenarına dağıtma

Akış işini SQL veritabanı Edge modülüne dağıtmak için SQL veritabanı Edge modülü yapılandırmasını, yukarıdaki adımdan akış işi için SAS URI 'sini içerecek şekilde güncelleştirin. SQL veritabanı Edge modülünü güncelleştirmek için

1. Azure portal, IoT Hub dağıtımınıza gidin.

2. Sol taraftaki bölmede **IoT Edge**' ye tıklayın.

3. **IoT Edge** sayfasında SQL veritabanı Edge modülünün dağıtıldığı IoT Edge bulup tıklayın.

4. Cihaz aygıtı *IoT Edge* sayfasında, **modülü ayarla**' ya tıklayın. 

5. **Modül ayarla** sayfasında SQL veritabanı Edge modülüne göre *Yapılandır* ' a tıklayın. 

6. **IoT Edge özel modüller** bölmesinde, *modülleri ayarla ikizi 'ın istenen özelliklerini* seçin ve ardından istediğiniz özellikleri aşağıdaki örnekte gösterildiği gibi ASAJOBıNFO seçeneğinin URI 'sini içerecek şekilde güncelleştirin. 

    > [!NOTE]
    > Aşağıdaki SAS URI 'SI yalnızca gösterim amaçlıdır. Lütfen URI 'yi dağıtımınızdaki gerçek URI ile değiştirin.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. **Kaydet**’e tıklayın.

8. **Modülleri ayarla** sayfasında *İleri*' ye tıklayın.

9. **Modülleri ayarla** sayfasında *İleri* ' ye ve ardından **Gönder**' e tıklayın.

10. Modül güncelleştirmesini gönderin, Stream Analytics iş dosyası SQL veritabanı Edge örneğine göre indirilir, sıkıştırıldı ve dağıtılır.

## <a name="next-steps"></a>Sonraki adımlar

- Fiyatlandırma ve kullanılabilirliğe ilişkin ayrıntılar için bkz. [Azure SQL veritabanı Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Aboneliğiniz için Azure SQL veritabanı Edge 'i etkinleştirme isteği.
- Başlamak için aşağıdakilere bakın:
  - [Azure portal aracılığıyla SQL veritabanı ucunu dağıtma](deploy-portal.md)
