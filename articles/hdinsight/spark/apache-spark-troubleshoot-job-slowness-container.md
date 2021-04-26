---
title: Azure HDInsight depolamada birçok dosya olduğunda yavaş Apache Spark
description: Azure depolama kapsayıcısı Azure HDInsight 'ta çok sayıda dosya içerdiğinde Apache Spark iş yavaş çalışır
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/21/2019
ms.openlocfilehash: d256292956b42c65722ea5f9c87d6835297d1d40
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219102"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Azure HDInsight’ta Azure depolama kapsayıcısı birçok dosya içerdiğinde Apache Spark işleri yavaş çalışıyor

Bu makalede, Azure HDInsight kümelerinde Apache Spark bileşenleri kullanılırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

HDInsight kümesi çalıştırılırken, çok sayıda dosya/alt klasör olduğunda Azure Storage kapsayıcısına yazan Apache Spark işi yavaş olur. Örneğin, yeni bir kapsayıcıya yazarken 20 saniye sürer, ancak alanlarındaki 200.000 dosyaları olan bir kapsayıcıya yazarken yaklaşık 2 dakika.

## <a name="cause"></a>Nedeni

Bu bilinen bir Spark sorunudur. Yavaşlığın, `ListBlob` `GetBlobProperties` Spark iş yürütmesi sırasında ve işlemlerinden gelir.

Bölümleri izlemek için Spark 'ın `FileStatusCache` Dizin yapısı hakkında bilgi içeren bir bakımını vardır. Spark, bu önbelleği kullanarak yolları ayrıştırarak kullanılabilir bölümlerin farkında olabilir. Bölümleri izlemenin avantajı, Spark 'ın yalnızca verileri okurken gerekli dosyalara dokunmasına yönelik bir avantajdır. Bu bilgileri güncel tutmak için, yeni veriler yazdığınızda Spark 'ın dizin altındaki tüm dosyaları Listeve bu önbelleği güncelleştirmesi gerekir.

Spark 2,1 ' de, her yazma işleminden sonra önbelleği güncelleştirmemiz gerekirken, Spark var olan bir bölüm sütununun geçerli yazma isteğinde önerilen ile eşleşip eşleşmediğini kontrol eder, bu nedenle, her yazma işlemi sırasında listeleme işlemlerine da yol açabilir.

Spark 2,2 ' de, ekleme moduyla veri yazarken bu performans sorunu düzeltilmelidir.

Spark 2,3 ' de Spark 2,2 ile aynı davranış beklenmektedir.

## <a name="resolution"></a>Çözüm

Bölümlenmiş bir veri kümesi oluşturduğunuzda, Spark 'ın güncelleştirmesini listelemesini sağlayacak dosya sayısını sınırlayan bir bölümleme şeması kullanılması önemlidir `FileStatusCache` .

Her nth Micro Batch için N %100 = = 0 (100 yalnızca bir örnektir), var olan verileri Spark tarafından yüklenebilen başka bir dizine taşıyın.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
