---
title: Machine Learning genel bakış-Azure HDInsight
description: Azure HDInsight 'ta kümeler için büyük veri makine öğrenimi seçeneklerine genel bakış.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 492663ca35ed04da09e7f0d198444a4d40178e2b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867534"
---
# <a name="machine-learning-on-hdinsight"></a>HDInsight 'ta makine öğrenimi

HDInsight, büyük miktarlarda makine öğrenimini, yapılandırılmış, yapılandırılmamış ve hızlı hareketli verilerin büyük miktarlarından (petabaytlarca veya hatta eksabaytlarca) değerli Öngörüler elde etmenizi sağlar. HDInsight 'ta birkaç makine öğrenimi seçeneği vardır: mini ml ve Apache Spark MLlib, R, Apache Hive ve Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>Mini ml ve MLlib

[HDInsight Spark](spark/apache-spark-overview.md) , büyük veri analizlerini artırmak üzere bellek içi işlemeyi destekleyen, Birleşik, açık kaynaklı ve paralel veri Işleme çerçevesinin Azure tarafından barındırılan [Apache Spark](https://spark.apache.org/)bir sunumudur. Spark işleme altyapısı hız, kullanım kolaylığı ve gelişmiş analiz için oluşturulmuştur. Spark 'ın bellek içi dağıtılmış hesaplama özellikleri, makine öğrenimi ve grafik hesaplamaları 'nda kullanılan yinelemeli algoritmalar için iyi bir seçenek yapar. Algoritmik modelleme yeteneklerini bu dağıtılmış ortama getiren iki ölçeklenebilir makine öğrenimi kitaplığı vardır: MLlib ve mini-ml. MLlib, RDD 'lerin üstünde oluşturulmuş özgün API 'yi içerir. Parlak ml, ML işlem hatları oluşturmak için veri çerçevelerinin üzerine inşa edilen daha yüksek düzeyde bir API sağlayan daha yeni bir pakettir. Parlak ml, MLlib özelliklerinin tümünü henüz desteklemez, ancak MLlib 'i Spark 'ın Standart makine öğrenimi kitaplığı olarak değiştiriyor.

Apache Spark için Microsoft Machine Learning kitaplığı, [Mmlspark](https://github.com/Azure/mmlspark). Bu kitaplık, veri bilimcilerinin Spark üzerinde daha üretken olmasını, deneme oranını artırmayı ve çok büyük veri kümelerinde derin öğrenme dahil olmak üzere son teknoloji makine öğrenimi tekniklerinden yararlanmasını sağlamak için tasarlanmıştır. MMLSpark, dizin oluşturma dizeleri, makine öğrenimi algoritmaları tarafından beklenen bir düzene zorlama verileri ve özellik vektörlerini montaj gibi ölçeklenebilir ML modelleri derlerken, modern ml 'nin alt düzey API 'Lerinin üzerine bir katman sağlar. MMLSpark kitaplığı, PySpark 'da model oluşturmaya yönelik bu ve diğer yaygın görevleri basitleştirir.

## <a name="r"></a>R

[R](https://www.r-project.org/) , şu anda dünyanın en popüler istatistiksel programlama dilidir. Bu, 2.500.000 ' den fazla kullanıcı topluluğu olan ve büyümekte olan bir açık kaynaklı veri görselleştirme aracıdır. Kullanıcı tabanıyla ve 8.000 katkıda bulunan paketlerle, R, makine öğrenimine ihtiyaç duyan birçok şirket için büyük olasılıkla tercih edilir. ML Hizmetleri, çok büyük veri kümeleri ve modellerle kullanılmak üzere hazırlanmaya yönelik bir HDInsight kümesi oluşturabilirsiniz. Bu özellik, veri bilimcileri ve İstatistikçilerin ' yi, küme kurulumu ve bakım ek yükü olmadan HDInsight aracılığıyla isteğe bağlı olarak ölçeklendirilebilen tanıdık bir R arabirimiyle sağlar.

:::image type="content" source="./media/hdinsight-machine-learning-overview/training-for-prediction.png" alt-text="R Server ile tahmin için eğitim" border="false":::

Bir kümenin kenar düğümü, kümeye bağlanmak ve R betiklerinizi çalıştırmak için uygun bir yer sağlar.  Ayrıca, ScaleR 'nın Hadoop Map 'i azaltma veya Spark işlem bağlamlarını kullanarak R betiklerini küme düğümleri üzerinde de çalıştırabilirsiniz.

Spark ile HDInsight 'ta ML Hizmetleri ile Spark işlem bağlamı kullanarak bir kümenin düğümleri arasında eğitim paralel hale getirmek sağlayabilirsiniz. Tüm kullanılabilir çekirdekleri gerektiği gibi paralel olarak kullanarak R betiklerini doğrudan Edge düğümünde çalıştırabilirsiniz. Alternatif olarak, kümedeki tüm düğümlerde dağıtılan işlemeyi kapatmak için kodunuzu kenar düğümünden çalıştırabilirsiniz. Spark ile HDInsight üzerinde ML Hizmetleri, isterseniz açık kaynaklı R paketlerinden paralelleştirme işlevleri de sunar.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning ve Apache Hive

Azure Machine Learning, tahmine dayalı analiz modellerini modelleyen araçlar sağlar ve tahmine dayalı modellerinizi kullanıma hazırlama Web Hizmetleri olarak dağıtmak için kullanabileceğiniz tam olarak yönetilen bir hizmettir. Azure Machine Learning, bulutta, tahmine dayalı modeller oluşturmak, test etmek, işlemleri gerçekleştirmek ve yönetmek için kullanabileceğiniz, tahmine dayalı bir analiz çözümüdür. Büyük bir algoritma kitaplığından seçim yapın, model oluşturmak için Web tabanlı bir Studio kullanın ve modelinizi bir Web hizmeti olarak kolayca dağıtın.

:::image type="content" source="./media/hdinsight-machine-learning-overview/azure-machine-learning.png" alt-text="Microsoft Azure Machine Learning 'e genel bakış" border="false":::

[Hive sorguları](../machine-learning/team-data-science-process/create-features-hive.md)kullanarak bir HDInsight Hadoop kümesindeki veriler için özellikler oluşturun. *Özellik Mühendisliği* , öğrenme sürecini kolaylaştıran ham verilerden Özellikler oluşturarak öğrenme algoritmalarının tahmine dayalı gücünü artırmaya çalışır. Azure Machine Learning Studio (klasik) ' den HiveQL sorgularını çalıştırabilir ve [veri Içeri aktarma modülünü](../machine-learning/classic/import-data.md)kullanarak Hive içinde işlenen ve BLOB depolama alanında depolanan verilere erişim sağlayabilirsiniz.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Bilişsel Araç Seti

[Derin öğrenme](https://www.microsoft.com/en-us/research/group/dltc/) , insan Beyininin biik süreçleriyle ilham olan sinir ağlarını kullanan Machine Learning 'in bir daldır. Birçok araştırmacıcılar yapay zeka geliştirmek için taahhüt yaklaşımı olarak ayrıntılı öğrenme konusuna sahiptir. Derinlemesine öğrenme örnekleri, dil çeviricileri, görüntü tanıma sistemleri ve makine nedenleridir.

Microsoft, derin öğreniminde kendi çalışmasını kolaylaştırmaya yardımcı olmak için ücretsiz, kullanımı kolay ve açık kaynaklı [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)geliştirmiştir. Bu araç seti, dünyanın dört bir yanındaki ve en son algoritmalarda ve tekniklerle ilgilenen öğrencilerle dünya çapındaki birçok Microsoft ürünü tarafından kullanılmaktadır.

## <a name="see-also"></a>Ayrıca bkz.

### <a name="scenarios"></a>Senaryolar

* [Machine Learning ile Apache Spark: HVAC verilerini kullanarak oluşturma sıcaklığını çözümlemek için HDInsight 'ta Spark kullanma](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning Apache Spark: yemek İnceleme sonuçlarını tahmin etmek için HDInsight 'ta Spark kullanma](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Apache Mahout ile film önerileri oluşturma](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive ve Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Uçtan uca Apache Hive ve Azure Machine Learning](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [HDInsight üzerinde Apache Spark makine öğrenimi](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Derin öğrenme kaynakları

* [Microsoft Cognitive Toolkit derin öğrenme modelini Azure HDInsight Spark kümesiyle kullanma](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Veri Bilimi Sanal Makinesi (DSVM) üzerinde derin öğrenme ve AI çerçeveleri](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
