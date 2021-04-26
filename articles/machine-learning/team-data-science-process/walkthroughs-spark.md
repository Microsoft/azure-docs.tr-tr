---
title: PySpark, Scala-Team Data Science Işlemi ile HDInsight Spark üzerinde analiz
description: Bir Azure HDInsight Spark PySpark ve Scala kullanımını adım adım sunan ekip veri bilimi sürecinin örnekleri.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1662af6cd3499fdf851d4e1bd8a0db48da7635b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93320133"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Azure 'da PySpark ve Scala kullanarak HDInsight Spark veri bilimi talimatları

Bu izlenecek yollar, tahmine dayalı analiz yapmak için bir Azure Spark kümesinde PySpark ve Scala kullanır. Bunlar, Team Data Science Işleminde özetlenen adımları izler. Ekip veri bilimi sürecine genel bir bakış için bkz. [veri bilimi işlemi](overview.md). HDInsight 'ta Spark 'a genel bakış için bkz. [HDInsight 'Ta Spark 'A giriş](../../hdinsight/spark/apache-spark-overview.md).

Ekip veri bilimi sürecini yürütecek olan ek veri bilimi talimatları, kullandıkları **platforma** göre gruplandırılır. Bu örneklerin bir öğesi için [ekip veri bilimi sürecini yürüten Izlenecek yollara](walkthroughs.md) bakın.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Azure Spark 'ta PySpark kullanarak TAXI ipuçlarını tahmin etme

New York TAXI verilerini kullanarak, [Azure HDInsight 'Ta Spark kullanma](spark-overview.md) Kılavuzu, bir ipucunun ödenip ödenmediğini ve beklenen tutarların aralığını tahmin eder. Bu örnek, genel olarak kullanılabilir NYC TAXI yolculuğuna ve tarifeli havayolu veri kümesinden verileri depolamak, araştırmak ve özellik mühendislerinden veri eklemek için bir [Azure HDInsight Spark kümesi](https://azure.microsoft.com/services/hdinsight/) kullanan bir senaryoda Team Data Science işlemini kullanır. Bu genel bakış konusu, HDInsight Spark kümesi ve Jupyıter PySpark not defterlerini kullanır. Bu not defterleri, verilerinizi nasıl keşfedebileceğiniz ve ardından modeller oluşturma ve kullanma işlemlerinin nasıl yapılacağını gösterir. Gelişmiş veri araştırması ve modelleme not defteri, çapraz doğrulama, hiper parametre kullanımı ve model değerlendirmesinin nasıl ekleneceğini gösterir.

### <a name="data-exploration-and-modeling-with-spark"></a>Spark ile veri araştırması ve modelleme 
[Spark MLlib araç seti ile veri için ikili sınıflandırma ve regresyon modelleri oluşturma](spark-data-exploration-modeling.md) konusunun veri kümesini İnceleme ve makine öğrenimi modelleri oluşturma, Puanlama ve değerlendirme.

### <a name="model-consumption"></a>Model tüketimi
Bu konuda oluşturulan sınıflandırma ve regresyon modellerinin nasıl puan alabileceğinizi öğrenmek için bkz. [Spark ile oluşturulmuş makine öğrenimi modellerini Puanlama ve değerlendirme](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Çapraz doğrulama ve hiper parametre SWE
Modellerin çapraz doğrulama ve hiper parametre tatkileri kullanılarak eğitilme hakkında [Spark Ile gelişmiş veri araştırması ve modelleme](spark-advanced-data-exploration-modeling.md) hakkında bilgi alabilirsiniz.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Azure Spark 'ta Scala 'yı kullanarak TAXI ipuçlarını tahmin etme

[Azure 'Da Spark Ile Scala 'Yı kullanma](scala-walkthrough.md) Kılavuzu, bir ipucunun ödenip ödenmediğini tahmin eder ve ücretlendirildiği tutarların aralığını tahmin eder. Bir Azure HDInsight Spark kümesinde Spark Machine Learning kitaplığı (MLlib) ve mini ml paketleri ile denetimli makine öğrenimi görevleri için Scala 'ın nasıl kullanılacağını gösterir. [Veri bilimi işlemini](./index.yml)ve araştırma, görselleştirme, özellik Mühendisliği, modelleme ve model tüketimini oluşturan görevlerde size kılavuzluk eder. Oluşturulan modeller lojistik ve doğrusal regresyon, rastgele ormanlar ve gradyan artırılmış ağaçlar içerir.


## <a name="next-steps"></a>Sonraki adımlar

Ekip veri bilimi Işlemine genel bakış için bkz. [Team Data Science işlemine genel bakış](overview.md).

Team Data Science Işlem yaşam döngüsü hakkında bir tartışma için bkz. [Team Data Science işlem yaşam döngüsü](lifecycle.md). Bu yaşam döngüsü, başlangıçtan sonuna kadar olan adımları özetler ve bu da projeler genellikle yürütüldüğünde izler.