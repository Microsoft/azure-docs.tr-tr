---
title: Derin ve gelişmiş analiz-Azure HDInsight
description: Gelişmiş analizler 'in Azure HDInsight 'ta büyük verileri işlemek için algoritmaları nasıl kullandığını öğrenin.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 436825c6e5df2be524dc06ccdaf82f0156714679
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241661"
---
# <a name="deep-dive---advanced-analytics"></a>Derin ve gelişmiş analiz

## <a name="what-is-advanced-analytics-for-hdinsight"></a>HDInsight için gelişmiş analiz nedir?

HDInsight, büyük miktarlarda yapılandırılmış, yapılandırılmamış ve hızlı hareketli verilerden değerli Öngörüler elde etme olanağı sunar. Gelişmiş analiz, yüksek düzeyde ölçeklenebilir mimariler, istatistiksel ve makine öğrenimi modelleri ve akıllı panolar sayesinde size anlamlı öngörüler sağlar. Makine öğrenimi veya tahmine *dayalı analiz*, tahminlere yol göstermek ve kararlarınıza kılavuzluk etmek için verilerinize ait ilişkilerden oluşan ve öğrenerek kullanılan algoritmaları kullanır.

## <a name="advanced-analytics-process"></a>Gelişmiş analiz işlemi

![Gelişmiş analiz işlem akışı](./media/apache-hadoop-deep-dive-advanced-analytics/hdinsight-analytic-process.png)

İş sorununu tanımladıktan ve verilerinizi toplamayı ve işlemeyi başlattıktan sonra, tahmin etmek istediğiniz soruyu temsil eden bir model oluşturmanız gerekir. Modelinizde, iş gereksinimlerinize en uygun tahmin türünü sağlamak için bir veya daha fazla makine öğrenimi algoritması kullanılır.  Verilerinizin büyük bölümü modelinize eğmekte veya bunu test etmek ya da değerlendirmek için kullanılır.

Modelinizi oluşturduktan, yükledikten, test ettikten ve değerlendirdikten sonra, bir sonraki adım modelinize yanıt sağlamaya başlayacak şekilde modelinizi dağıtmaktır. Son adım, modelinizin performansını izlemek ve gerektiği şekilde ayarlayabilmelidir.

## <a name="common-types-of-algorithms"></a>Ortak algoritma türleri

Gelişmiş analiz çözümleri, makine öğrenimi algoritmaları kümesi sağlar. Algoritma kategorilerinin ve ilişkili ortak iş kullanım durumlarının özeti aşağıda verilmiştir.

![Machine Learning kategori özetleri](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-use-cases.png)

En iyi sığdırma algoritmasını seçerken birlikte, eğitim için veri sağlamanız gerekip gerekmediğini göz önünde bulundurmanız gerekir. Makine öğrenimi algoritmaları aşağıdaki şekilde kategorize edilir:

* Denetimli-algoritmanın, sonuçlar sağlayabilmesi için bir etiketli veri kümesi üzerinde eğitililmesi gerekir
* Yarı denetimli-algoritma, eğitimin ilk aşamasında kullanılamayan bir gözye göre etkileşimli sorgu aracılığıyla ek hedefler tarafından Genişletilebilir
* Denetimli olmayan algoritma eğitim verileri gerektirmez 
* Pekiştirmeye dayalı algoritması, belirli bir bağlam içinde ideal davranışları (genellikle Robotics 'de kullanılır) belirlemede yazılım aracılarını kullanır


| Algoritma kategorisi| Kullanım | Öğrenme türü | Algoritmalar |
| --- | --- | --- | -- |
| Sınıflandırma | Kişileri veya şeyleri gruplar halinde sınıflandırın | Denetimli | Karar ağaçları, lojistik regresyon, sinir ağları |
| Lenmesi | Örnek kümesini hogenou gruplarına bölme | Denetimsiz | K-kümelemenin anlamı |
| Model algılama | Verilerdeki sık kullanılan ilişkilendirmeleri tanımla | Denetimsiz | İlişkilendirme kuralları |
| Regresyon | Sayısal sonuçları tahmin etme | Denetimli | Doğrusal regresyon, sinir Networks |
| Pekiştirmeye dayalı | Robots için en iyi davranışı belirleme | Pekiştirmeye dayalı | Monte Carlo benzetimleri, ayrıntılı fikir |

## <a name="machine-learning-on-hdinsight"></a>HDInsight 'ta makine öğrenimi

HDInsight, gelişmiş analiz iş akışı için çeşitli makine öğrenimi seçeneklerine sahiptir:

* Machine Learning ve Apache Spark
* R ve ML Hizmetleri
* Azure Machine Learning ve Apache Hive
* Apache Spark ve derin öğrenme

### <a name="machine-learning-and-apache-spark"></a>Machine Learning ve Apache Spark


[HDInsight Spark](../spark/apache-spark-overview.md) , büyük veri analizlerini artırmak için bellek içi işleme kullanan Birleşik, açık kaynaklı ve paralel veri Işleme çerçevesinin Azure tarafından barındırılan [Apache Spark](https://spark.apache.org/)bir sunumudur. Spark işleme altyapısı hız, kullanım kolaylığı ve gelişmiş analiz için oluşturulmuştur. Spark 'ın bellek içi dağıtılmış hesaplama özellikleri, makine öğrenimi ve grafik hesaplamaları 'nda kullanılan yinelemeli algoritmalar için iyi bir seçenek yapar. 


Bu dağıtılmış ortama algoritmik modelleme özellikleri getiren üç ölçeklenebilir makine öğrenimi kitaplığı vardır:

* [**Mllib**](https://spark.apache.org/docs/latest/ml-guide.html) -Mllib Spark rdds üzerinde oluşturulmuş özgün API 'yi içerir.
* [**Parlak ml**](https://spark.apache.org/docs/1.2.2/ml-guide.html) -parlak ML, ml işlem hatları oluşturmak Için Spark veri çerçevelerinin üzerine inşa edilen daha yüksek düzeyde bir API sağlayan daha yeni bir pakettir.
* [**Mmlspark**](https://github.com/Azure/mmlspark) -Apache Spark (mmlspark) için Microsoft Machine Learning kitaplığı, veri bilimcilerinin Spark üzerinde daha üretken olması, deneme oranını artırması ve son teknoloji makine öğrenimi tekniklerinden yararlanmak için tasarlanmıştır. çok büyük veri kümelerinde derin öğrenme. MMLSpark kitaplığı, PySpark 'da model oluşturmaya yönelik ortak modelleme görevlerini basitleştirir. 

### <a name="r-and-ml-services"></a>R ve ML Hizmetleri

HDInsight 'ın bir parçası olarak, [ml Hizmetleri](../r-server/r-server-overview.md) ile çok büyük veri kümeleri ve modellerle birlikte kullanılmak üzere kullanılabilecek bir HDInsight kümesi oluşturabilirsiniz. Bu yeni özellik, veri bilimcileri ve İstatistikçilerin ' yi, isteğe bağlı olarak HDInsight üzerinden ölçeklenebilen, küme kurulumu ve bakım yükü olmadan, tanıdık bir R arabirimi sağlar.

### <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning ve Apache Hive

[Azure Machine Learning Studio (klasik)](https://studio.azureml.net/) , tahmine dayalı analiz modellerini modelleyen araçlar sağlar ve tahmine dayalı modellerinizi kullanıma hazırlama Web Hizmetleri olarak dağıtmak için kullanabileceğiniz tam olarak yönetilen bir hizmettir. Azure Machine Learning, tahmine dayalı modelleri hızlı bir şekilde oluşturmak, test etmek, tekrarlaştır ve yönetmek için bulutta tüm tahmine dayalı analiz çözümleri oluşturmaya yönelik araçlar sağlar. Büyük bir algoritma kitaplığından seçim yapın, model oluşturmak için Web tabanlı bir Studio kullanın ve modelinizi bir Web hizmeti olarak kolayca dağıtın.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark ve derin öğrenme

[Derin öğrenme](https://www.microsoft.com/research/group/dltc/) , insan Beyininin biik süreçleriyle ilham olan *derin sinir ağlarını* (dnNs 'ler) kullanan Machine Learning 'in bir daldır. Birçok araştırmacıcılar yapay zeka için taahhüt yaklaşımı olarak ayrıntılı öğrenme konusuna sahiptir. Derin öğrenime örnek olarak, dil çevirmenleri, görüntü tanıma sistemleri ve makine nedenleridir. Microsoft, derin öğreniminde kendi çalışmasını kolaylaştırmaya yardımcı olmak için ücretsiz, kullanımı kolay ve açık kaynaklı [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)geliştirmiştir. Araç seti, dünyanın dört bir yanındaki çok sayıda Microsoft ürünü tarafından, büyük ölçekli bir öğrenme dağıtımı ve en son algoritmalarda ve tekniklerle ilgilenen öğrenciler tarafından kapsamlı olarak kullanılmaktadır. 

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Senaryo-kentsel geliştirmede desenleri belirlemek için görüntüleri puan edin

HDInsight kullanarak gelişmiş analiz makinesi öğrenimi işlem hattı örneğini gözden geçirelim.

Bu senaryoda, Microsoft 'un Cognitive Toolkit (CNTK) adlı derin bir öğrenme çerçevesinde oluşturulan DNNs 'nin, bir HDInsight Spark kümesinde PySpark kullanılarak bir Azure Blob depolama hesabında depolanan Puanlama büyük görüntü koleksiyonları için nasıl işlem görülemediği gösterilmektedir. Bu yaklaşım ortak DNN kullanım örneğine, havadan görüntü sınıflandırmasına uygulanır ve kentsel geliştirmede en son desenleri belirlemek için kullanılabilir.  Önceden eğitilen bir görüntü sınıflandırma modeli kullanacaksınız. Model, [Cifar-10 veri kümesinde](https://www.cs.toronto.edu/~kriz/cifar.html) önceden eğitilmiş ve 10.000 ile tutulan görüntülere uygulandı.

Bu gelişmiş analiz senaryosunda üç temel görev vardır:

1. Apache Spark 2.1.0 dağıtımına sahip bir Azure HDInsight Hadoop kümesi oluşturun. 
2. Bir Azure HDInsight Spark kümesinin tüm düğümlerine Microsoft Cognitive Toolkit yüklemek için özel bir komut dosyası çalıştırın. 
3. Spark Python API 'sini (PySpark) kullanarak bir Azure Blob depolama hesabındaki dosyalara eğitilen Microsoft Cognitive Toolkit derin bir öğrenme modeli uygulamak için önceden oluşturulmuş bir Jupyter Not defterini HDInsight Spark kümenize yükleyin. 

Bu örnek, Alex Kronizhevsky, Vinod nair ve Geoffey Hinton tarafından derlenen ve dağıtılan CIFAR-10 görüntü kümesini kullanır. CIFAR-10 veri kümesi, birbirini dışlayan 10 sınıfa ait 60.000 32 × 32 renk görüntüsünü içerir:

![Örnek görüntüleri Machine Learning](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-images.png)

Veri kümesi hakkında daha fazla bilgi için, bkz. Alex Kronizhevsky 'ın [çok sayıda özellik katmanını küçük resimlerden öğreniyor](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

Veri kümesi, 50.000 görüntü eğitimi kümesine ve bir 10.000 görüntü test kümesine bölümlenmiştir. İlk küme, Cognitive Toolkit GitHub deposundan [Bu öğreticiyi](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) izleyerek Microsoft Cognitive Toolkit kullanarak yirmi katmanlı derin bir evnetme ağı (ResNet) modelini eğitmek için kullanılmıştır. Kalan 10.000 görüntüleri, modelin doğruluğunu test etmek için kullanıldı. Bu, dağıtılmış bilgi işlemin yürütmeye geldiği yerdir: görüntülerin ön işleme ve Puanlama görevi oldukça paralelleştirilebilir. Kaydedilen eğitilen modelle birlikte şunu kullandık:

* , Görüntüleri ve eğitilen modeli kümenin çalışan düğümlerine dağıtmak için PySpark.
* HDInsight Spark kümesinin her bir düğümündeki görüntülerin ön işlemesini sağlamak için Python.
* Modeli yüklemek için Cognitive Toolkit ve her düğümdeki önceden işlenmiş görüntüleri puan edin.
* Jupyıter Not defterleri, PySpark betiğini çalıştırmak, sonuçları toplamak ve model performansını görselleştirmek için [Matplotlib](https://matplotlib.org/) kullanın.

10.000 görüntünün tüm ön işleme/Puanlama, 4 çalışan düğümü olan bir kümede bir dakikadan kısa sürer. Model ~ 9.100 etiketlerini doğru bir şekilde tahmin eder (%91) yansımasını. Bir karışıklık matrisi en yaygın sınıflandırma hatalarını gösterir. Örneğin, matris yanlış etiketleme ve diğer etiket çiftlerine göre çok daha sık gerçekleşmektedir.

![Machine Learning sonuçları grafiği](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-results.png)

### <a name="try-it-out"></a>Deneyin!

Bu çözümü uçtan uca uygulamak için [Bu öğreticiyi](../spark/apache-spark-microsoft-cognitive-toolkit.md) izleyin: bir HDInsight Spark kümesi kurma, Cognitive Toolkit yükleme ve 10.000 cifar görüntülerini puan veren Jupyter Notebook çalıştırma.

## <a name="next-steps"></a>Sonraki adımlar

Apache Hive ve Azure Machine Learning

* [Uçtan uca Apache Hive ve Azure Machine Learning](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [1 TB veri kümesinde Azure HDInsight Hadoop kümesi kullanma](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark ve MLLib

* [HDInsight üzerinde Apache Spark makine öğrenimi](../../machine-learning/team-data-science-process/spark-overview.md)
* [Machine Learning ile Apache Spark: HVAC verilerini kullanarak oluşturma sıcaklığını çözümlemek için HDInsight 'ta Apache Spark kullanın](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning ile Apache Spark: yemek İnceleme sonuçlarını tahmin etmek için HDInsight 'ta Apache Spark kullanın](../spark/apache-spark-machine-learning-mllib-ipython.md)

Derin öğrenme, Cognitive Toolkit ve diğerleri

* [Veri bilimi Azure sanal makinesi](../../machine-learning/data-science-virtual-machine/overview.md)
* [Azure HDInsight üzerinde H2O.ai tanıtımı](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
