---
title: Azure HDInsight 'ta işlem hatalarını ayıklama dosyası işlemleri
description: Azure HDInsight kümeleriyle etkileşim kurarken sorunlar için sorun giderme adımlarını ve olası çözümleri açıklar.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f0f06c81906116dc278377cf9fd8871e8899a1d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938749"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Azure HDInsight 'ta işlem hatalarını ayıklama dosyası işlemleri

Azure depolama ile ilgili olan işlemleri hangi işlemlerin başlatıldığını anlamak isteyebileceğiniz zamanlar vardır. İstemci tarafında, bulunan sürücü her dosya sistemi işlemi için **hata ayıklama** düzeyinde Günlükler oluşturur. LıBB sürücüsü, günlük düzeyini denetlemek için Log4J kullanır ve varsayılan değer **bilgi** düzeyidir. Azure Storage sunucu tarafı analiz günlükleri için bkz. [Azure Storage Analytics günlüğü](../../storage/common/storage-analytics-logging.md).

Üretilmiş bir günlük şuna benzer olacaktır:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>Dosya işlemleri için işlem hata ayıklama günlüğünü aç

1. Bir Web tarayıcısından, ' a gidin `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` , burada `CLUSTERNAME` Spark kümenizin adıdır.

1. **Gelişmiş spark2-Log4J-Özellikler**' e gidin.

    1. Öğesini `log4j.appender.console.Threshold=INFO` olarak değiştirin `log4j.appender.console.Threshold=DEBUG` .

    1. Ekleyin `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG` .

1. **Gelişmiş livy2-Log4J-Özellikler**' e gidin.

    Ekleyin `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG` .

1. Değişiklikleri kaydet.

## <a name="additional-logging"></a>Ek günlükler

Yukarıdaki Günlükler dosya sistemi işlemlerinin üst düzey olarak anlaşılmasına sahip olmalıdır. Yukarıdaki Günlükler hala yararlı bilgiler sağlamaıyorsa veya blob depolama API çağrılarını araştırmak istiyorsanız, ' `fs.azure.storage.client.logging=true` ye ekleyin `core-site` . Bu ayar, tek bir depolama sürücüsüne yönelik Java SDK günlüklerini etkinleştirir ve her bir blob Storage Server çağrısını yazdırır. Diski hızla doldurabileceğinden ve işlemi yavaşlatabileceğinden, araştırmalar sonrasında ayarı kaldırın.

Arka uç Azure Data Lake tabanlıdır, bileşen için aşağıdaki Log4J ayarını kullanın (örneğin, Spark/tez/TBU):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Günlüklerde bulunan günlüklere bakın `/var/log/adl/adl.log` .

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.