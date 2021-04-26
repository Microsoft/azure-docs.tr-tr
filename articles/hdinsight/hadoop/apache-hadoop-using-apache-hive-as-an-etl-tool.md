---
title: ETL aracı olarak Apache Hive kullanma-Azure HDInsight
description: Azure HDInsight 'ta verileri ayıklamak, dönüştürmek ve yüklemek (ETL) için Apache Hive kullanın.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: bcf2f39423f033ccd5bfdb6bf51ebc89e254f802
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867823"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Ayıklama, dönüştürme ve yükleme (ETL) aracı olarak Apache Hive kullanma

Genellikle gelen verileri analiz için uygun bir hedefe yüklemeden önce temizlemeniz ve dönüştürmeniz gerekir. Ayıklama, dönüştürme ve yükleme (ETL) işlemleri, verileri hazırlamak ve bir veri hedefine yüklemek için kullanılır.  HDInsight üzerinde Apache Hive yapılandırılmamış verileri okuyabilir, verileri gerektiği gibi işleyebilir ve sonra karar destek sistemleri için verileri ilişkisel bir veri ambarına yükleyebilir. Bu yaklaşımda, veriler kaynaktan ayıklanır. Daha sonra, Azure depolama Blobları veya Azure Data Lake Storage gibi uyarlamalı tablo depolama alanında depolanır. Veriler daha sonra Hive sorguları dizisi kullanılarak dönüştürülür. Ardından, hedef veri deposuna toplu yükleme hazırlığı sırasında Hive içinde hazırlanır.

## <a name="use-case-and-model-overview"></a>Kullanım örneği ve modele genel bakış

Aşağıdaki şekilde, ETL otomasyonu için kullanım örneğine ve modele ilişkin bir genel bakış gösterilmektedir. Giriş verileri, uygun çıktıyı oluşturmak için dönüştürülür.  Bu dönüştürme sırasında, veri şekli, veri türü ve hatta dil değişir.  ETL işlemlerinde, kusurdan ölçüm 'e dönüştürülebilir, saat dilimlerini değiştirebilir ve hedefteki mevcut verilerle doğru şekilde uyum sağlamak için duyarlık geliştirebilirsiniz. ETL süreçler Ayrıca yeni verileri, raporlamayı güncel tutmak veya mevcut veriler hakkında daha fazla öngörü sağlamak için mevcut verilerle birleştirebilir. Raporlama araçları ve hizmetleri gibi uygulamalar bu verileri istenen biçimde tüketebilir.

:::image type="content" source="./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png" alt-text="ETL mimarisi olarak Apache Hive" border="false":::

Hadoop genellikle çok sayıda metin dosyası (CSV gibi) içeri aktarılan ETL işlemlerinde kullanılır. Ya da daha küçük, sık sık değişen metin dosyası sayısı veya her ikisi.  Hive, verileri veri hedefine yüklemeden önce hazırlamak için kullanılan harika bir araçtır.  Hive, CSV üzerinde bir şema oluşturmanıza ve verilerle etkileşime geçen MapReduce programları oluşturmak için SQL benzeri bir dil kullanmanıza olanak sağlar.

Hive 'yi yapmak için kullanılan tipik adımlar aşağıdaki gibidir:

1. Azure Data Lake Storage veya Azure Blob depolama alanına veri yükleme.
2. Şemalarınızı depolarken Hive tarafından kullanılmak üzere bir meta veri deposu veritabanı (Azure SQL veritabanı kullanarak) oluşturun.
3. HDInsight kümesi oluşturma ve veri deposunu bağlama.
4. Veri deposundaki veriler üzerinde okuma zamanında uygulanacak şemayı tanımlayın:

    ```hql
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs

    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT,
        system BIGINT,
        systemage BIGINT,
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    STORED AS TEXTFILE LOCATION 'wasbs://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Verileri dönüştürün ve hedefe yükleyin.  Dönüştürme ve yükleme sırasında Hive kullanmanın birkaç yolu vardır:

    * Hive kullanarak verileri sorgulama ve hazırlama ve Azure Data Lake Storage veya Azure Blob depolama alanında CSV olarak kaydetme.  Ardından, bu CSV 'leri almak ve verileri SQL Server gibi bir hedef ilişkisel veritabanına yüklemek için SQL Server Integration Services (SSIS) gibi bir araç kullanın.
    * Hive ODBC sürücüsünü kullanarak doğrudan Excel veya C# ' den verileri sorgulayın.
    * Hazırlanmış düz CSV dosyalarını okumak ve hedef ilişkisel veritabanına yüklemek için [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) kullanın.

## <a name="data-sources"></a>Veri kaynakları

Veri kaynakları genellikle veri deponuzdaki mevcut verilerle eşleştirilebilen dış veri olur, örneğin:

* Sosyal medya verileri, günlük dosyaları, algılayıcılar ve veri dosyaları üreten uygulamalar.
* Hava durumu istatistikleri veya satıcı satış numaraları gibi veri sağlayıcılarından elde edilen veri kümeleri.
* Yakalanan, filtrelenen ve uygun bir araç veya çerçeve üzerinden işlenen akış verileri.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Çıkış hedefleri

Hive kullanarak verileri farklı türlerde hedeflere aktarabilirsiniz:

* SQL Server veya Azure SQL veritabanı gibi bir ilişkisel veritabanı.
* Azure SYNAPSE Analytics gibi bir veri ambarı.
* Excel.
* Azure tablosu ve BLOB depolama.
* Verilerin belirli biçimlerde işlenmesini gerektiren uygulamalar veya hizmetler veya belirli bilgi yapısı türlerini içeren dosyalar.
* Azure Cosmos DB gibi bir JSON belge deposu.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

ETL modeli genellikle şunları yapmak istediğinizde kullanılır:

`*` Dış kaynaklardan mevcut bir veritabanına veya bilgi sistemine akış verileri veya yarı yapılandırılmış veya yapılandırılmamış verilerin büyük hacimlerini yükleyin.
`*` Küme üzerinde birden fazla dönüşüm geçişi kullanarak, yüklemeden önce verileri temizleyin, dönüştürün ve doğrulayın.
`*` Düzenli olarak güncellenen raporlar ve görselleştirmeler oluşturun. Örneğin, raporun gün içinde oluşturması çok uzun sürüyorsa, raporu gece boyunca çalışacak şekilde zamanlayabilirsiniz. Hive sorgusunu otomatik olarak çalıştırmak için [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) ve PowerShell 'i kullanabilirsiniz.

Verilerin hedefi bir veritabanı değilse sorgu içinde uygun biçimde, örneğin bir CSV dosyası oluşturabilirsiniz. Bu dosya daha sonra Excel veya Power BI içeri aktarılabilir.

ETL işleminin bir parçası olarak veriler üzerinde birkaç işlem yürütmeniz gerekiyorsa, bunları nasıl yöneteceğinizi göz önünde bulundurun. Bir dış program tarafından denetlenen işlemler, çözüm içinde iş akışı yerine, bazı işlemlerin paralel olarak yürütülüp yürütülmeyeceğine karar verin. Ve her bir işin tamamlandığı tespit. Hadoop içinde Oozie gibi bir iş akışı mekanizması kullanmak, dış betikler veya özel programlar kullanarak bir işlem dizisini düzenleme deneenden daha kolay olabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [ETL ölçeği](apache-hadoop-etl-at-scale.md)
* [`Operationalize a data pipeline`](../hdinsight-operationalize-data-pipeline.md)
