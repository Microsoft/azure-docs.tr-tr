---
title: Azure HDInsight depolamada birçok dosya olduğunda yavaş Apache Spark
description: Azure depolama kapsayıcısı Azure HDInsight 'ta çok sayıda dosya içerdiğinde Apache Spark iş yavaş çalışır
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: 82280532fa91923bd08d8ff3164dc841282c392c
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241181"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Azure depolama kapsayıcısı Azure HDInsight 'ta çok sayıda dosya içerdiğinde Apache Spark iş yavaş çalışır

Bu makalede, Azure HDInsight kümelerinde Apache Spark bileşenleri kullanılırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

HDInsight kümesi çalıştırılırken, çok sayıda dosya/alt klasör olduğunda Azure Storage kapsayıcısına yazan Apache Spark işi yavaş olur. Örneğin, yeni bir kapsayıcıya yazarken 20 saniye sürer, ancak alanlarındaki 200.000 dosyaları olan bir kapsayıcıya yazarken yaklaşık 2 dakika.

## <a name="cause"></a>Nedeni

Bu bilinen bir Spark sorunudur. Yavaşlığın, Spark iş yürütmesi sırasında `ListBlob` ve `GetBlobProperties` işlemlerinden gelir.

Spark 'ın bölümleri izlemek için dizin yapısı hakkında bilgi içeren bir `FileStatusCache` sürdürmek gerekir. Spark, bu önbelleği kullanarak yolları ayrıştırarak kullanılabilir bölümlerin farkında olabilir. Bölümleri izlemenin avantajı, Spark 'ın yalnızca verileri okurken gerekli dosyalara dokunmasına yönelik bir avantajdır. Bu bilgileri güncel tutmak için, yeni veriler yazdığınızda Spark 'ın dizin altındaki tüm dosyaları Listeve bu önbelleği güncelleştirmesi gerekir.

Spark 2,1 ' de, her yazma işleminden sonra önbelleği güncelleştirmemiz gerekirken, Spark var olan bir bölüm sütununun geçerli yazma isteğinde önerilen ile eşleşip eşleşmediğini kontrol eder, bu nedenle, her yazma işlemi sırasında listeleme işlemlerine da yol açabilir.

Spark 2,2 ' de, ekleme moduyla veri yazarken bu performans sorunu düzeltilmelidir.

## <a name="resolution"></a>Çözünürlük

Bölümlenmiş bir veri kümesi oluşturduğunuzda, `FileStatusCache`güncelleştirmek için Spark 'ın listelenmesi gereken dosya sayısını sınırlayan bir bölümleme şeması kullanılması önemlidir.

Her nth Micro Batch için N %100 = = 0 (100 yalnızca bir örnektir), var olan verileri Spark tarafından yüklenebilen başka bir dizine taşıyın.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport) ile bağlanma-Azure Community 'yi doğru kaynaklara bağlayarak müşteri deneyimini iyileştirmeye yönelik resmi Microsoft Azure hesabı: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için lütfen [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.
