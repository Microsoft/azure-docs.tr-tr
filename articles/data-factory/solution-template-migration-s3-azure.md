---
title: Amazon S3 'ten Azure Data Lake Storage 2. veri geçirme
description: Bir bölüm listesini, Azure Data Factory ile AWS S3 üzerinde depolamak için bir dış denetim tablosu kullanarak Amazon S3 'tan verileri geçirmek üzere bir çözüm şablonu kullanmayı öğrenin.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: c1fd4cb248abdc219c6ee5d098e10c329826c160
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100362004"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Amazon S3 'ten Azure Data Lake Storage 2. veri geçirme

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Lake Storage 2., Amazon S3 ' den ' den yüzlerce milyonlarca dosyadan oluşan verileri petabaytlarca geçirmek için şablonları kullanın. 

 > [!NOTE]
 > AWS S3 'ten Azure 'a (örneğin, 10 TB 'den az) küçük veri hacmi kopyalamak istiyorsanız, [Azure Data Factory veri kopyalama aracının](copy-data-tool.md)kullanımı daha verimli ve kolaydır. Bu makalede açıklanan şablon, ihtiyacınız olan miktardan daha fazla.

## <a name="about-the-solution-templates"></a>Çözüm şablonları hakkında

Veri bölümü, özellikle 10 TB 'den fazla veri geçirirken önerilir. Verileri bölümlemek için ' önek ' ayarından yararlanarak Amazon S3 adlı klasörleri ve dosyaları adlandırın ve ardından her ADF kopyalama işi aynı anda bir bölüm kopyalayabilir. Daha iyi üretilen iş için birden çok ADF kopyalama işini eşzamanlı olarak çalıştırabilirsiniz.

Veri geçişi normal olarak bir kerelik geçmiş veri geçişi gerektirir ve değişiklikleri düzenli olarak AWS S3 'ten Azure 'a eşitlemektir. Aşağıdaki iki şablon, bir şablonun bir kerelik geçmiş veri geçişini kapsamasının yanı sıra başka bir şablon da AWS S3 'teki değişiklikleri Azure 'a eşitlemeyi içerir.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Şablon için, veri Amazon S3 ' dan geçmiş verileri Azure Data Lake Storage 2. geçirme

Bu şablon (*şablon adı: AWS S3 'ten gelen geçmiş verileri Azure Data Lake Storage 2.*), Azure SQL veritabanı 'nda bir dış denetim tablosunda bir bölüm listesi yazmış olduğunuzu varsayar. Bu nedenle, dış denetim tablosundan bölüm listesini almak için bir *arama* etkinliği kullanır, her bölüm üzerinde yineleme yapar ve her ADF kopyalama işi aynı anda bir bölüm kopyalayacaktır. Herhangi bir kopyalama işi tamamlandıktan sonra, denetim tablosundaki her bir bölümü kopyalama durumunu güncelleştirmek için *saklı yordam* etkinliğini kullanır.

Şablon beş etkinlik içerir:
- **Arama** , bir dış denetim tablosundan Azure Data Lake Storage 2. kopyalanmamış bölümleri alır. Tablo adı *s3_partition_control_table* ve tablodaki verileri yüklemek için yapılan sorgu *"başarılı sorarızası = 0" Iken s3_partition_control_table PartitionPrefix ' i seçin*.
- **ForEach** , *arama* etkinliğinden bölüm listesini alır ve her bölümü *triggercopy* etkinliğine yineler. *BatchCount* öğesini eşzamanlı olarak bırden çok ADF kopyalama işi çalıştıracak şekilde ayarlayabilirsiniz. Bu şablonda 2 ' ye ayarlandık.
- **Executepipeline** *CopyFolderPartitionFromS3* işlem hattını yürütür. Her kopyalama işinin bir bölümü kopyalamasını sağlamak için başka bir işlem hattı oluşturduğumuz nedeni, bu belirli bir bölümü AWS S3 ' dan yeniden yüklemek için başarısız kopyalama işini yeniden çalıştırmayı kolay hale getirmek olacaktır. Diğer bölümleri yükleyen diğer tüm kopyalama işleri etkilenmeyecektir.
- Her bölümü AWS S3 öğesinden Azure Data Lake Storage 2. **kopyalayın** .
- **Sqlserverstoredprocedure** denetim tablosundaki her bir bölümü kopyalama durumunu güncelleştirir.

Şablon iki parametre içerir:
- **AWS_S3_bucketName** , AWS S3 üzerinde verileri geçirmek istediğiniz yer alan demet adıdır. AWS S3 üzerindeki birden çok demetten verileri geçirmek istiyorsanız, her bölüm için demet adını depolamak üzere dış denetim tablonuza bir sütun ekleyebilirsiniz ve ayrıca işlem hattınızı, bu sütundan verileri uygun şekilde almak için güncelleştirebilirsiniz.
- **Azure_Storage_fileSystem** , verileri geçirmek Istediğiniz Azure Data Lake Storage 2. dosya sistemi adıdır.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Şablonun değiştirilen dosyaları yalnızca Amazon S3 konumundan Azure Data Lake Storage 2. kopyalaması için

Bu şablon (*şablon adı: AWS S3 'dan Azure Data Lake Storage 2. 'a Delta verileri kopyalama*), yeni veya güncelleştirilmiş dosyaları yalnızca AWS S3 'ten Azure 'a kopyalamak için her bir dosyanın LastModifiedTime kullanır. Dosya veya klasörlerinizin AWS S3 (örneğin,/yyyy/mm/dd/file.csv) üzerindeki dosya veya klasör adının bir parçası olarak timeslice bilgileri ile bölümlenmiş olması durumunda, artımlı yükleme yeni dosyaları için daha fazla performans yaklaşımı sağlamak üzere bu [öğreticiye](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) gidebilirsiniz. Bu şablon, Azure SQL veritabanı 'nda bir dış denetim tablosunda bir bölüm listesi yazmış olduğunuzu varsayar. Bu nedenle, dış denetim tablosundan bölüm listesini almak için bir *arama* etkinliği kullanır, her bölüm üzerinde yineleme yapar ve her ADF kopyalama işi aynı anda bir bölüm kopyalayacaktır. Her bir kopyalama işi dosyaları AWS S3 'ten kopyalamaya başladığında, yalnızca yeni veya güncelleştirilmiş dosyaları tanımlamak ve kopyalamak için LastModifiedTime özelliğini kullanır. Herhangi bir kopyalama işi tamamlandıktan sonra, denetim tablosundaki her bir bölümü kopyalama durumunu güncelleştirmek için *saklı yordam* etkinliğini kullanır.

Şablon yedi etkinlik içerir:
- **Arama** , bir dış denetim tablosundan bölümleri alır. Tablo adı *s3_partition_delta_control_table* ve tablodaki verileri yüklemeye yönelik sorgu *"S3_partition_delta_control_table 'Den farklı PartitionPrefix Seç"* dir.
- **ForEach** , *arama* etkinliğinden bölüm listesini alır ve her bölümü *triggerdeltacopy* etkinliğine yineler. *BatchCount* öğesini eşzamanlı olarak bırden çok ADF kopyalama işi çalıştıracak şekilde ayarlayabilirsiniz. Bu şablonda 2 ' ye ayarlandık.
- **Executepipeline** *DeltaCopyFolderPartitionFromS3* işlem hattını yürütür. Her kopyalama işinin bir bölümü kopyalamasını sağlamak için başka bir işlem hattı oluşturduğumuz nedeni, bu belirli bir bölümü AWS S3 ' dan yeniden yüklemek için başarısız kopyalama işini yeniden çalıştırmayı kolay hale getirmek olacaktır. Diğer bölümleri yükleyen diğer tüm kopyalama işleri etkilenmeyecektir.
- **Arama** , yeni veya güncelleştirilmiş dosyaların LastModifiedTime aracılığıyla tanımlanması için dış denetim tablosundan son kopyalama işi çalışma süresini alır. Tablo adı *s3_partition_delta_control_table* ve tablodaki verileri yüklemeye yönelik sorgu *"Select Max (jobruntime) for S3_partition_delta_control_table WHERE PartitionPrefix = ' @ {Pipeline (). Parameters. prefixStr} ' ve başarılı sorfailure = 1" olarak değiştirildi*.
- Yeni veya değiştirilmiş dosyaları yalnızca AWS S3 ' dan Azure Data Lake Storage 2. ' ye **kopyalayın** . *Modifieddatetimestart* 'ın özelliği, son kopyalama işi çalışma zamanına ayarlanır. *Modifieddatetimeend* 'in özelliği geçerli kopyalama işi çalışma zamanına ayarlanır. Saatin UTC saat dilimine uygulandığını unutmayın.
- **Sqlserverstoredprocedure** başarılı olduğunda, denetim tablosundaki her bir bölümü kopyalama ve çalışma süresini kopyalama durumunu güncelleştirir. Başarımahatası sütunu 1 olarak ayarlanır.
- **Sqlserverstoredprocedure** , başarısız olduğunda denetim tablosundaki her bir bölümü kopyalama ve çalışma süresini kopyalama durumunu güncelleştirir. Başarımahatası sütunu 0 olarak ayarlanır.

Şablon iki parametre içerir:
- **AWS_S3_bucketName** , AWS S3 üzerinde verileri geçirmek istediğiniz yer alan demet adıdır. AWS S3 üzerindeki birden çok demetten verileri geçirmek istiyorsanız, her bölüm için demet adını depolamak üzere dış denetim tablonuza bir sütun ekleyebilirsiniz ve ayrıca işlem hattınızı, bu sütundan verileri uygun şekilde almak için güncelleştirebilirsiniz.
- **Azure_Storage_fileSystem** , verileri geçirmek Istediğiniz Azure Data Lake Storage 2. dosya sistemi adıdır.

## <a name="how-to-use-these-two-solution-templates"></a>Bu iki çözüm şablonunu kullanma

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Şablon için, veri Amazon S3 ' dan geçmiş verileri Azure Data Lake Storage 2. geçirme 

1. AWS S3 bölüm listesini depolamak için Azure SQL veritabanı 'nda bir denetim tablosu oluşturun. 

    > [!NOTE]
    > Tablo adı s3_partition_control_table.
    > Denetim tablosunun şeması, PartitionPrefix ve Başarısorfailure ' dır; burada PartitionPrefix, Amazon S3 içindeki klasör ve dosyaları filtrelemek için S3 ' deki ön ek ayarıdır. Bu bölüm Azure 'a kopyalanmamış ve 1, bu bölümün Azure 'a başarıyla kopyalandığı anlamına gelir.
    > Denetim tablosunda tanımlanmış 5 bölüm vardır ve her bölüm kopyalamanın varsayılan durumu 0 ' dır.

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. Denetim tablosu için aynı Azure SQL veritabanında bir saklı yordam oluşturun. 

    > [!NOTE]
    > Saklı yordamın adı sp_update_partition_success. ADF işlem hattınızda SqlServerStoredProcedure etkinliği tarafından çağrılır.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. **AWS S3 'ten geçmiş verileri Azure Data Lake Storage 2.** şablonuna gidin. Veri kaynağı deposu olarak AWS S3 ve hedef depo olarak Azure Data Lake Storage 2. dış denetim tablonuza yönelik bağlantıları girin. Dış denetim tablosunun ve saklı yordamın aynı bağlantıya başvurduğuna dikkat edin.

    ![AWS S3 'dan Azure Data Lake Storage 2. şablonuna geçmiş verileri geçirmeyi gösteren ekran görüntüsü.](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. **Bu şablonu kullan**' ı seçin.

    ![Bu şablonu kullan düğmesini vurgulayan bir ekran görüntüsü.](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. Aşağıdaki örnekte gösterildiği gibi 2 işlem hattı ve 3 veri kümesi oluşturulduğunu görürsünüz:

    ![Şablon kullanılarak oluşturulan iki işlem hattını ve üç veri kümesini gösteren ekran görüntüsü.](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. "BulkCopyFromS3" ardışık düzenine gidin ve **Hata Ayıkla**' yı seçin, **parametreleri** girin. Ardından **Son**'u seçin.

    ![Hata ayıklamayı seçme ve son ' u seçmeden önce parametreleri girme işlemlerini gösteren ekran görüntüsü.](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. Aşağıdaki örneğe benzer sonuçlar görürsünüz:

    ![Döndürülen sonuçları gösteren ekran görüntüsü.](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Şablonun değiştirilen dosyaları yalnızca Amazon S3 konumundan Azure Data Lake Storage 2. kopyalaması için

1. AWS S3 bölüm listesini depolamak için Azure SQL veritabanı 'nda bir denetim tablosu oluşturun. 

    > [!NOTE]
    > Tablo adı s3_partition_delta_control_table.
    > Denetim tablosunun şeması PartitionPrefix, burada PartitionPrefix,, Amazon adındaki dosya ve dosyaları filtrelemek için S3 ' deki önek ayarı olan JobRunTime ve Başarımahatası, iş çalışma zamanı, kopyalama işlerinin çalıştırıldığı zaman DateTime değeridir ve başarılı durum, bu bölümün Azure 'a kopyalanmadığı anlamına gelir.
    > Denetim tablosu 'nda tanımlanmış 5 bölüm vardır. JobRunTime için varsayılan değer, bir kerelik geçmiş veri geçişinin başladığı zaman olabilir. ADF kopyalama etkinliği, bu tarihten sonra son değiştirilen AWS S3 üzerindeki dosyaları kopyalayacaktır. Her bölüm kopyalamanın varsayılan durumu 1 ' dir.

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. Denetim tablosu için aynı Azure SQL veritabanında bir saklı yordam oluşturun. 

    > [!NOTE]
    > Saklı yordamın adı sp_insert_partition_JobRunTime_success. ADF işlem hattınızda SqlServerStoredProcedure etkinliği tarafından çağrılır.

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. **AWS S3 'ten Azure Data Lake Storage 2. şablonuna olan Delta verilerini kopyalama** bölümüne gidin. Veri kaynağı deposu olarak AWS S3 ve hedef depo olarak Azure Data Lake Storage 2. dış denetim tablonuza yönelik bağlantıları girin. Dış denetim tablosunun ve saklı yordamın aynı bağlantıya başvurduğuna dikkat edin.

    ![Yeni bağlantı oluşturma](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. **Bu şablonu kullan**' ı seçin.

    ![Bu şablonu kullan](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. Aşağıdaki örnekte gösterildiği gibi 2 işlem hattı ve 3 veri kümesi oluşturulduğunu görürsünüz:

    ![İşlem hattını gözden geçirme](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6.  "DeltaCopyFromS3" ardışık düzenine gidip **Hata Ayıkla**' yı seçin ve **parametreleri** girin. Ardından **Son**'u seçin.

    ![* * Hata Ayıkla * * öğesine tıklayın](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. Aşağıdaki örneğe benzer sonuçlar görürsünüz:

    ![Sonucu gözden geçirin](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. Ayrıca Denetim tablosundan sonuçları bir sorgu *"select * from s3_partition_delta_control_table"* olarak denetleyebilir, aşağıdaki örneğe benzer bir çıktı görürsünüz:

    ![Sorguyu çalıştırdıktan sonra denetim tablosunun sonuçlarını gösteren ekran görüntüsü.](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>Sonraki adımlar

- [Birden çok kapsayıcıdan dosyaları kopyalama](solution-template-copy-files-multiple-containers.md)
- [Dosyaları taşıma](solution-template-move-files.md)
