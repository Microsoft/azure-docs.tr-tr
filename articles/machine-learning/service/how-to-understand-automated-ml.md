---
title: Otomatik ML sonuçlarını anlama
titleSuffix: Azure Machine Learning
description: Otomatik makine öğrenimi çalışmalarınızın her biri için grafikleri ve ölçümleri görüntüleme ve anlama hakkında bilgi edinin.
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7f8789076b00cd2b5a0694cf1f52e5dfe1569aee
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571346"
---
# <a name="understand-automated-machine-learning-results"></a>Otomatik makine öğrenimi sonuçlarını anlama
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, otomatik makine öğrenimi çalışmalarınızın her biri için grafikleri ve ölçümleri görüntüleme ve anlama hakkında bilgi edineceksiniz. 

Aşağıdakiler hakkında daha fazla bilgi edinin:
+ [Sınıflandırma modelleri için ölçümler, grafikler ve eğriler](#classification)
+ [Regresyon modelleriyle ilgili ölçümler, grafikler ve grafikler](#regression)
+ [Model yorumlenebilirliği ve özellik önemi](#explain-model)

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

* SDK ile ya da Azure Machine Learning Studio 'da otomatik makine öğrenimi çalıştırmak için bir deneme oluşturun.

    * Bir [sınıflandırma modeli](how-to-auto-train-remote.md) veya [regresyon modeli](tutorial-auto-train-models.md) oluşturmak için SDK 'yı kullanma
    * Uygun verileri karşıya yükleyerek bir sınıflandırma veya regresyon modeli oluşturmak için [Azure Machine Learning Studio 'yu](how-to-create-portal-experiments.md) kullanın.

## <a name="view-the-run"></a>Çalıştırmayı görüntüle

Otomatik makine öğrenimi denemesinin çalıştırılmasından sonra, Machine Learning çalışma alanınızda çalıştırmaların bir geçmişi bulunabilir. 

1. Çalışma alanınıza gidin.

1. Çalışma alanının sol bölmesinde **denemeleri**' yi seçin.

   ![Deneme menüsünün ekran görüntüsü](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Denemeleri listesinde, araştırmak istediğiniz birini seçin.

   [![deneme listesi](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Alt tabloda, **Çalıştır**' ı seçin.

   [![deneme çalıştırması](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. Modellerde, daha fazla incelemek istediğiniz modelin **algoritma adını** seçin.

   [![deneme modeli](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Ayrıca, `RunDetails`[jupi pencere öğesini](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)kullandığınızda bir çalıştırma sırasında aynı sonuçları görürsünüz.

## <a name="classification"></a>Sınıflandırma sonuçları

Azure Machine Learning otomatik makine öğrenimi özelliklerini kullanarak oluşturduğunuz her sınıflandırma modeli için aşağıdaki ölçümler ve grafikler mevcuttur

+ [Ölçümler](#classification-metrics)
+ [Karışıklık matrisi](#confusion-matrix)
+ [Kesinlik-geri çağırma grafiği](#precision-recall-chart)
+ [Alıcı bir işlem özelliklerini (ya da ROC)](#roc)
+ [Eğriyi yükselt](#lift-curve)
+ [Kazanç eğrisi](#gains-curve)
+ [Ayarlama çizimi](#calibration-plot)

### <a name="classification-metrics"></a>Sınıflandırma ölçümleri

Aşağıdaki ölçümler, bir sınıflandırma görevinin her bir çalıştırma yinelemesinde kaydedilir.

|Ölçüm|Açıklama|Hesaplama|Ek parametreler
--|--|--|--|
AUC_Macro| AUC, alıcı Işletim özelliği eğrisinin altındaki alandır. Makro her sınıf için AUC 'nin aritmetik ortalaması olur.  | [Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Average = "makro"|
AUC_Micro| AUC, alıcı Işletim özelliği eğrisinin altındaki alandır. Micro, her bir sınıftan doğru pozitif sonuçlar ve hatalı pozitif sonuçlar birleştirilerek Global olarak hesaplanır.| [Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Average = "Micro"|
AUC_Weighted  | AUC, alıcı Işletim özelliği eğrisinin altındaki alandır. Ağırlıklı her sınıf için her bir sınıftaki doğru örnek sayısı ağırlıklı olarak ağırlıklı aritmetik ortalamadır.| [Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Average = "ağırlıklı"
accuracy|Doğruluk, gerçek etiketlerle tam olarak eşleşen tahmini etiketlerin yüzdesidir. |[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |None|
average_precision_score_macro|Ortalama duyarlık, her bir eşiğin elde ettiği ağırlıklı ortalamanın ağırlıklı ortalaması olarak bir duyarlık geri çağırma eğrisini özetler ve bu da ağırlık olarak kullanılan önceki eşikten geri çekme artışı artar. Makro, her sınıfın ortalama duyarlık puanının aritmetik ortasıdır.|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Average = "makro"|
average_precision_score_micro|Ortalama duyarlık, her bir eşiğin elde ettiği ağırlıklı ortalamanın ağırlıklı ortalaması olarak bir duyarlık geri çağırma eğrisini özetler ve bu da ağırlık olarak kullanılan önceki eşikten geri çekme artışı artar. Micro, her kesme sırasında gerçek pozitif sonuçları ve hatalı pozitif sonuçları birleştirerek Global olarak hesaplanır.|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Average = "Micro"|
average_precision_score_weighted|Ortalama duyarlık, her bir eşiğin elde ettiği ağırlıklı ortalamanın ağırlıklı ortalaması olarak bir duyarlık geri çağırma eğrisini özetler ve bu da ağırlık olarak kullanılan önceki eşikten geri çekme artışı artar. Ağırlıklı, her sınıf için Ortalama duyarlık puanının aritmetik ortasıdır ve her bir sınıftaki gerçek örnek sayısı tarafından ağırlıklı olarak hesaplanır.|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Average = "ağırlıklı"|
balanced_accuracy|Dengeli doğruluk, her sınıf için geri çekmenin aritmetik ortalaması anlamına gelir.|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "makro"|
f1_score_macro|F1 puanı duyarlık ve geri çağırma için harmonik bir anlama gelir. Makro her sınıf için F1 puanının aritmetik ortalaması anlamına gelir.|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Average = "makro"|
f1_score_micro|F1 puanı duyarlık ve geri çağırma için harmonik bir anlama gelir. Mikro, toplam doğru pozitif sonuç sayısı, yanlış negatifler ve hatalı pozitif sonuçlar sayımarak Global olarak hesaplanır.|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Average = "Micro"|
f1_score_weighted|F1 puanı duyarlık ve geri çağırma için harmonik bir anlama gelir. Her sınıf için F1 puanının sınıf sıklığıyla ağırlıklı ortalama|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Average = "ağırlıklı"|
log_loss|Bu, bir dayalı sınıflandırıcının tahminlerini verilen doğru etiketlerin olumsuz günlük olma olasılığı olarak tanımlanan (ÇOKTERİMLİ) Lojistik gerileme ve sinir Networks gibi uzantılar içinde kullanılan kayıp işlevidir. {0,1} ile gerçek etiket ve tahmini olasılık YP ile ilgili olan tek bir örnek için, günlük kaybı-log P (YT&#124;YP) =-(YT günlüğü (YP) + (1. YT) günlüğü (1-YP)).|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|None|
norm_macro_recall|Normalleştirilmiş makro geri çağırma, rastgele performansın 0 puanına sahip olması ve mükemmel performansın 1 puanına sahip olması için makro geri çağırma 'dir. Bu, norm_macro_recall: = (recall_score_macro-R)/(1-R) ile elde edilir; burada R, rastgele tahmine dayalı olarak beklenen recall_score_macro değeri (örneğin, ikili sınıflandırma için R = 0,5 ve C sınıfı sınıflandırma sorunları için R = (1/C) olur).|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "makro" |
precision_score_macro|Duyarlık, doğru şekilde etiketlenmiş pozitif tahmin edilen öğelerin yüzdesidir. Makro her sınıf için Duyarlığın aritmetik ortalaması anlamına gelir.|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Average = "makro"|
precision_score_micro|Duyarlık, doğru şekilde etiketlenmiş pozitif tahmin edilen öğelerin yüzdesidir. Mikro, toplam doğru pozitif sonuç sayısı ve hatalı pozitif sonuçlar eklenerek Global olarak hesaplanır.|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Average = "Micro"|
precision_score_weighted|Duyarlık, doğru şekilde etiketlenmiş pozitif tahmin edilen öğelerin yüzdesidir. Ağırlıklı, her sınıf için duyarlık ortalamasıdır ve her bir sınıftaki doğru örnek sayısına göre ağırlıklı olur.|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Average = "ağırlıklı"|
recall_score_macro|Hatırlayın, belirli bir sınıfın doğru etiketlenmiş öğelerinin yüzdesidir. Makro her sınıf için geri çekmenin aritmetik ortalaması anlamına gelir.|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "makro"|
recall_score_micro|Hatırlayın, belirli bir sınıfın doğru etiketlenmiş öğelerinin yüzdesidir. Mikro, toplam doğru pozitif sonuç sayısı, yanlış negatifler ve hatalı pozitif sonuçlar sayımarak Global olarak hesaplanır|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "Micro"|
recall_score_weighted|Hatırlayın, belirli bir sınıfın doğru etiketlenmiş öğelerinin yüzdesidir. Ağırlıklı, her sınıf için geri çekmenin aritmetik ortalamasıdır ve her bir sınıftaki doğru örnek sayısına göre ağırlıklı olur.|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "ağırlıklı"|
weighted_accuracy|Ağırlıklı doğruluk, her örneğe verilen ağırlığa, bu örneğin true sınıfındaki gerçek örneklerin oranına eşit olan bir doğruluk örneğidir.|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight, hedefteki her öğe için bu sınıfın oranına eşit bir vektördür|

### <a name="confusion-matrix"></a>Karışıklık matrisi

Bir karışıklık matrisi, bir sınıflandırma modelinin performansını tanımlamakta kullanılır. Her satırda, doğru sınıfın örnekleri görüntülenir ve her sütun, tahmin edilen sınıfın örneklerini temsil eder. Karışıklık matrisi, belirli bir model için doğru sınıflandırılan etiketleri ve hatalı sınıflandırılmış etiketleri gösterir.

Sınıflandırma sorunları için, otomatik olarak Azure Machine Learning oluşturulan her model için bir karışıklık matrisi sağlar. Her bir karışıklık matrisi için otomatik ML, her tahmin edilen etiketin ve her bir doğru etiket kesişmesinin sıklığını gösterir. Rengin daha koyu olması, matrisin o belirli bölümünde bulunan sayının yükseği. İdeal olarak, en koyu renkler matrisin Köşegeninin yanı da olacaktır. 

Örnek 1: yetersiz doğruluk içeren bir sınıflandırma modeli, yanlış doğrulukla ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

Örnek 2: yüksek doğruluk içeren bir sınıflandırma modeli (ideal) yüksek doğruluk içeren bir sınıflandırma modeli ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>Kesinlik-geri çağırma grafiği

Bu grafikle, her model için duyarlık geri çağırma eğrilerini, belirli bir iş sorununuz için duyarlık ve geri çekme arasında kabul edilebilir bir ilişkiye sahip olduğunu tespit edebilirsiniz. Bu grafik, bir modelin tüm sınıflarıyla ilişkili ortalama duyarlılık-geri çağırma, mikro ortalama duyarlık-geri çağırma ve duyarlık geri çağırma için makro gösterir.

Duyarlık terimi, bir sınıflandırıcının tüm örnekleri doğru şekilde etiketlemesini sağlar. Geri çağırma, bir sınıflandırıcının belirli bir etiketin tüm örneklerini bulma yeteneğini temsil eder. Duyarlık geri çekme eğrisi, bu iki kavram arasındaki ilişkiyi gösterir. İdeal olarak, modelin %100 duyarlık ve %100 doğruluk olması gerekir.

Örnek 1: düşük duyarlık ve düşük geri çağırma özellikli bir sınıflandırma modeli, düşük duyarlık ve düşük geri çekme](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png) sahip bir sınıflandırma modeliyle ![

Örnek 2: yaklaşık %100 duyarlığa sahip bir sınıflandırma modeli ve ~ 100% geri çek (ideal) ![bir sınıflandırma modeli yüksek duyarlıklı ve geri çağırma](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>ROC

Alıcı işletim özelliği (veya ROC), doğru sınıflandırılan etiketlerin ve belirli bir modelin hatalı sınıflandırılan etiketlerinin bir çizmesinden oluşur. ROC eğrisi, yüksek sapma içeren veri kümelerinde modeller yaparken, yanlış pozitif Etiketler göstermediğinden, daha az bilgilendirici olabilir.

Örnek 1: düşük doğru etiketlere sahip bir sınıflandırma modeli ve yüksek yanlış Etiketler, düşük doğru Etiketler ve yüksek yanlış etiketlere sahip sınıflandırma modeline ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

Örnek 2: yüksek doğru etiketlere sahip bir sınıflandırma modeli ve düşük yanlış Etiketler, yüksek doğru Etiketler ve düşük yanlış etiketlere sahip bir sınıflandırma modeli ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>Eğriyi yükselt

Söz konusu modelin değer kazancını görüntülemek için Azure Machine Learning otomatik olarak oluşturulan modelin, taban çizgisine göre bir kopyasını karşılaştırabilirsiniz.

Kaldırma grafikleri, bir sınıflandırma modelinin performansını değerlendirmek için kullanılır. Model olmadan karşılaştırıldığında bir model ile ne kadar iyi ne kadar daha fazla performans beklendiğini gösterir. 

Örnek 1: model, rastgele seçim modelinden daha kötü olan bir sınıflandırma modeli ![rastgele bir seçim modelinden daha kötü gerçekleştirir](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

Örnek 2: model, daha iyi gerçekleştirilen bir sınıflandırma modeli ![bir rastgele seçim modelinden daha iyi gerçekleştirir](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>Kazanç eğrisi

Kazanç grafiği, verilerin her bir bölümü tarafından bir sınıflandırma modelinin performansını değerlendirir. Veri kümesinin her yüzdelik değeri için, rastgele bir seçim modeliyle karşılaştırıldığında ne kadar daha fazla ne kadar iyi performans olacağını gösterir.

Bir modelden istenen kazancı karşılayan bir yüzde kullanarak sınıflandırma kesme listesini seçmenize yardımcı olması için birikmeli kazançlar grafiğini kullanın. Bu bilgiler, birlikte bulunan kaldırma grafiğindeki sonuçlara bakmaya yönelik başka bir yol sağlar.

Örnek 1: minimum kazanılı bir sınıflandırma modeli ![en az kazanılı bir sınıflandırma modeli](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

Örnek 2: önemli miktarda kazanç ![bir sınıflandırma modeli olan bir sınıflandırma modeli](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>Ayarlama çizimi

Tüm sınıflandırma sorunları için, belirli bir tahmine dayalı modelde mikro ortalama, makro-ortalama ve her bir sınıf için ayarlama satırını gözden geçirebilirsiniz. 

Bir ayarlama çizimi, tahmine dayalı bir modelin güvenini göstermek için kullanılır. Bunu, tahmin edilen olasılık ve gerçek olasılık arasındaki ilişkiyi göstererek yapar; burada "olasılık" belirli bir örneğin bazı etiketlerinin altına ait olma olasılığını temsil eder. İyi ayarlanmış bir model y = x satırıyla hizalanır ve bu, tahminlerde makul ölçüde emin olur. Daha duyarlı bir model y = 0 satırı ile hizalanır ve tahmin edilen olasılık yoktur ancak gerçek bir olasılık yoktur.

Örnek 1: daha iyi kalibre edilmiş model ![ daha iyi kalibre edilmiş bir model](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

Örnek 2: daha duyarlı olmayan bir model ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Gerileme sonuçları

Azure Machine Learning otomatik makine öğrenimi özelliklerini kullanarak oluşturduğunuz her regresyon modeli için aşağıdaki ölçümler ve grafikler mevcuttur

+ [Ölçümler](#reg-metrics)
+ [Tahmin edilen ve true](#pvt)
+ [Fazlalıklar grafiği](#histo)


### <a name="reg-metrics"></a>Gerileme ölçümleri

Aşağıdaki ölçümler, bir gerileme veya tahmin görevi için her bir çalıştırma yinelemesinde kaydedilir.

|Ölçüm|Açıklama|Hesaplama|Ek parametreler
--|--|--|--|
explained_variance|Açıklanamayan Varyans, belirli bir veri kümesinin varyasyonuna yönelik matematik modeli hesaplarından oluşan orandır. Bu, başlangıçtaki verilerin farkının, hataların farkının yüzdesidir. Hataların ortalaması 0 olduğunda, açıklanamayan varyansı eşittir.|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|None|
r2_score|R2 ortalama değeri veren bir taban çizgisi modeliyle karşılaştırıldığında, kare içinde bir belirleme veya yüzde azaltma yüzdesi olarak desteklenmez. |[Hesaplama](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|None|
spearman_correlation|Spearman bağıntısı, iki veri kümesi arasındaki ilişkinin monoton olmayan bir ölçüdür. Pearson bağıntı aksine, Spearman bağıntısı, her iki veri kümesinin de normalde dağıtıldığını varsaymaz. Diğer bağıntı katkatkatkatına benzer şekilde, bu, 0 ile + 1 arasında değişen hiçbir bağıntı yok. -1 veya + 1 correlations, tam bir monoton ilişkisi olduğunu kapsıyor. Pozitif bağıntılar, x arttıkça, o kadar artar. Negatif bağıntılar, x arttıkça y 'nin azaldığı anlamına gelir.|[Hesaplama](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|None|
mean_absolute_error|Mutlak ortalama hatası, hedef ve tahmin arasındaki mutlak fark değerinin beklenen değeridir|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|None|
normalized_mean_absolute_error|Normalleştirilmiş ortalama mutlak hata, verilerin aralığına bölünen mutlak bir hata anlamına gelir|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Verilerin aralığına göre Böl|
median_absolute_error|Ortanca mutlak hatası, hedef ve tahmin arasındaki tüm mutlak farklılıkların ortancası. Bu kayıp, aykırı değerler için sağlam.|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|None|
normalized_median_absolute_error|Normalleştirilmiş ortanca mutlak hatası, veri aralığına bölünen ortanca mutlak hatadır|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Verilerin aralığına göre Böl|
root_mean_squared_error|Kök ortalama kare hatası, hedef ve tahmin arasındaki beklenen kare farkının kare köküdür|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|None|
normalized_root_mean_squared_error|Normalleştirilmiş kök ortalama kare hatası, kök ortalama kare hatası, verilerin aralığına bölünür|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Verilerin aralığına göre Böl|
root_mean_squared_log_error|Kök ortalama kare günlüğü hatası, beklenen kareli Logaritmik hatanın kare köküdür|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|None|
normalized_root_mean_squared_log_error|Normalleştirilmiş kök ortalama kare günlük hatası, kök ortalama kareler günlük hatası veri aralığına bölünür|[Hesaplama](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Verilerin aralığına göre Böl|

### <a name="pvt"></a>Tahmin edilen ve true

Tahmin edilen ve true, bir gerileme sorunu için tahmin edilen bir değer ve onun ilişkili doğru değeri arasındaki ilişkiyi gösterir. Bu grafik, bir modelin performansını y = x satırına, tahmine dayalı bir modelin daha iyi doğruluğuna göre ölçmek için kullanılabilir.

Her çalıştıktan sonra, her regresyon modeli için öngörülen ve gerçek bir grafik görebilirsiniz. Veri gizliliğini korumak için değerler birlikte oluşturulur ve her bir bin boyutu grafik alanının alt bölümünde çubuk grafik olarak gösterilir. Tahmine dayalı modeli, hata kenar boşluklarını gösteren daha açık gölge alanla, modelin nerede olması gerektiği ideal değere göre karşılaştırabilirsiniz.

Örnek 1: tahminlerde düşük doğruluk içeren bir regresyon modeli, tahminlerde düşük doğruluk ile bir regresyon modeli ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

Örnek 2: tahminlerde yüksek doğruluk içeren bir regresyon modeli [, tahminlerde yüksek doğruluk Içeren bir regresyon modeli![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Fazlalıklar grafiği

Bir Artımlar, gözlemlenen y 'yi temsil eder. tahmin edilen y. Düşük sapma ile hata kenar boşluğunu göstermek için, kalan grafik histogramı, 0 etrafında ortalanmış bir zil eğrisi olarak şekillendirilir. 

Örnek 1: hata halinde sapma olan bir gerileme modeli, hata halinde sapma ile SA regresyon modeli ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

Örnek 2: hataların eşit bir şekilde dağıtımına sahip bir regresyon modeli, daha eşit hata dağıtımına sahip bir regresyon modeli ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Model yorumlenebilirliği ve özellik önemi

Özellik önemi, her özelliğin bir model oluşturma konusunda ne kadar değerli olduğunu görmenizi sağlar. Bu hesaplama, çalışma süresini önemli ölçüde arttırabileceğiniz için varsayılan olarak kapalıdır.   Tüm modeller için model açıklamasını etkinleştirebilir veya yalnızca en uygun modeli açıklayadırabilirsiniz.

Bir tahmine dayalı modelde, model için özellik önem puanı ' nı ve sınıf başına ' yı gözden geçirebilirsiniz. Her bir sınıfta ve genelde önem derecesine göre her özellik için nasıl Karşılaştırıldığı hakkında bilgi alabilirsiniz.

![Özellik açıkla özelliği](./media/how-to-understand-automated-ml/feature-importance.gif)

Yorumlu özellikleri [etkinleştirme hakkında daha](how-to-machine-learning-interpretability-automl.md) fazla bilgi için bkz. otomatik ml denemeleri 'da yorumlenebilirliği etkinleştirme.

## <a name="next-steps"></a>Sonraki adımlar

+ Azure Machine Learning 'de [Otomatikleştirilmiş ml](concept-automated-ml.md) hakkında daha fazla bilgi edinin.
+ [Otomatik Machine Learning modeli açıklama](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) örnek Not defterini deneyin.
