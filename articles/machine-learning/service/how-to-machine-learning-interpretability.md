---
title: Azure Machine Learning hizmetinde model yorumlenebilirliği
titleSuffix: Azure Machine Learning
description: Modelinizin Azure Machine Learning SDK 'Yı kullanarak neden tahmin sağladığını açıklayacağınızı öğrenin. Bu, modelinizin tahminleri nasıl yaptığını anlamak için eğitim ve çıkarım sırasında kullanılabilir.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 5f1008e8fcbbf7b82a694fd151a9dea9ca7f001e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515349"
---
# <a name="model-interpretability-in-azure-machine-learning-service"></a>Azure Machine Learning hizmetinde model yorumlenebilirliği
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Model yorumlenebilirliğini genel bakış

Yorumların, şirket ilkeleri, sektör standartları ve kamu yönetmelikleri ile uyumluluğunu sağlamak için veri bilimcileri ve iş karar mekanizmaları için çok önemlidir:
+ Veri bilimcileri, modellerini Yöneticiler ve hissedarlar için açıklayabilme olanağı sağlar, bu sayede bulgularının değer ve doğruluğunu anlayabilirler 
+ İş karar mekanizmalarının, son kullanıcıların güveneceği ve güvenliklerini sürdürmesi için saydamlık sağlama yeteneğinin içinizin olması gerekir

Bir makine öğrenimi modelinin açıklanması özelliğinin etkinleştirilmesi, model geliştirmenin iki ana aşaması sırasında önemlidir:
+ Machine Learning modeli geliştirme döngüsünün eğitim aşamasında. Model tasarımcıları ve değerlendiricileri, hipotezleri doğrulamak ve paydaşlarla güven oluşturmak için bir modelin yorum çıktısını kullanabilir. Ayrıca, hata ayıklama için ilgili öngörüleri, model davranışını doğrulamak ve sapma veya önemli özellikleri denetlemek için de kullanır.
+ Dağıtım aşamasında, dağıtılmış modeller etrafında saydamlığın olması, yöneticilerin nasıl çalıştığını ve kararlarının gerçek hayatta nasıl davrandığını ve etkilenmediğini anlamalarına olanak sağlar. 

## <a name="interpretability-with-azure-machine-learning"></a>Azure Machine Learning ile yorumlenebilirlik

Bu makalede, model yorumlimlebilirlik kavramlarının SDK 'da nasıl uygulandığını öğrenirsiniz.

SDK 'daki sınıfları ve yöntemleri kullanarak şunları edinebilirsiniz:
+ Hem ham hem de mühendislik uygulanmış özellikler için özellik önem değerleri
+ Eğitim ve çıkarım sırasında gerçek dünyada veri kümelerinde ölçeklendirerek uyumluluk.
+ Eğitim sırasında veri ve açıklamaları içindeki desenlerin bulunmasına yardımcı olacak etkileşimli görselleştirmeler


Machine Learning 'de **Özellikler** , hedef veri noktasını tahmin etmek için kullanılan veri alanlarıdır. Örneğin, kredi riskini tahmin etmek için yaş, hesap boyutu ve hesap yaşı için veri alanları kullanılıyor olabilir. Bu durumda, yaş, hesap boyutu ve hesap yaşı **özelliklerdir**. Özellik önemi, her bir veri alanının modelin tahminlerini nasıl etkilediğine bildirir. Örneğin, yaş, hesap boyutu ve yaş açısından tahmin doğruluğunu önemli ölçüde etkilemediğinden, tahminde yoğun bir şekilde kullanılıyor olabilir. Bu işlem, veri bilimcilerinin ortaya çıkan tahminleri açıklamasına olanak tanır. bu sayede, hissedarların modelde hangi veri noktalarının en önemlileri olduğuna ilişkin görünürlüğü vardır.

Bu araçları kullanarak, makine öğrenimi modellerini **tüm verilerde küresel**olarak veya **belirli bir veri noktasında yerel olarak** , kullanımı kolay ve ölçeklenebilir bir şekilde, son teknoloji olan teknolojileri kullanarak tanımlayabilirsiniz.

Yorumlenebilirlik sınıfları birden çok SDK paketi aracılığıyla kullanılabilir hale getirilir. [Azure Machine Learning IÇIN SDK paketlerini yüklemeyi](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)öğrenin.

* Microsoft tarafından desteklenen işlevleri içeren ana paket `azureml.interpret`.

* deneyebilmeniz için `azureml.contrib.interpret`, önizleme ve deneysel işlevleri.

* otomatik makine öğrenimi modellerini yorumlama `azureml.train.automl.automlexplainer` paketi.

> [!IMPORTANT]
> `contrib` ad alanındaki içerik tam olarak desteklenmez. Deneysel işlevler, diğer bir deyişle, bu, yavaş olarak ana ad alanına taşınır.

## <a name="how-to-interpret-your-model"></a>Modelinizi yorumlama

Modelin genel davranışını veya belirli tahminleri anlamak için yorumlamalar sınıflarını ve yöntemlerini uygulayabilirsiniz. İlki genel açıklama olarak adlandırılır ve ikincisi yerel açıklama olarak adlandırılır.

Yöntemler Ayrıca, yöntemin model belirsiz veya modele özgü olup olmadığına göre kategorize edilebilir. Bazı yöntemler belirli model türlerini hedeflemelidir. Örneğin, SHAP 'nin Tree açıklama yalnızca ağaç tabanlı modeller için geçerlidir. Bazı yöntemler, modeli taklit açıklama veya SHAP 'nin çekirdek açıklama gibi siyah bir kutu olarak değerlendirir. `interpret` paketi veri kümelerine, model türlerine ve kullanım örneklerine göre bu farklı yaklaşımlar kullanır.

Çıktı, belirli bir modelin tahminini nasıl yaptığı hakkında bilgi kümesidir, örneğin:
* Küresel/yerel göreli Özellik önemi
* Küresel/yerel özellik ve tahmin ilişkisi

### <a name="explainers"></a>Explainers

Bu paket, eğitim yorumlartablosu modellerine yönelik açık kaynaklı bir Python paketi olan yorumlama ve ınterbox AI sistemlerinin açıklanmasına yardımcı olan [yorumlanmış](https://github.com/interpretml/interpret-community/)teknikleri kullanır. [Yorumlama-topluluk](https://github.com/interpretml/interpret-community/) , bu SDK 'nın desteklenen explainers ana bilgisayarı olarak görev yapar ve şu anda aşağıdaki yorumlamış teknikleri destekler:

* **Shap Tree açıklama**: [Shap](https://github.com/slundberg/shap)ağaç açıklama, ağaçları ve Kümelemeler ağaçlara özel, POLINOM zaman hızlı Shap değer tahmini algoritmasına odaklanır.
* **Shap derin açıklama**: [Shap](https://github.com/slundberg/shap)'nin açıklamasına göre, derin açıklama ", [Shap NIP](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)'lerde açıklanan ayrıntılı bir bağlantı üzerinde bağlantı kurulan derin öğrenme modellerindeki Shap değerleri için yüksek hızda bir yaklaşık algoritmadır. TensorFlow arka ucunu kullanan TensorFlow modelleri ve keras modelleri desteklenir (Ayrıca, PyTorch için de ön destek vardır) ".
* **Shap doğrusal açıklama**: [Shap](https://github.com/slundberg/shap)'nin doğrusal açıklama değeri, doğrusal bir model için Shap değerlerini hesaplar ve isteğe bağlı olarak yetenek ilişkileri için hesaplar.

* **Shap Kernel açıklama**: [Shap](https://github.com/slundberg/shap)çekirdek açıklama, herhangi bir model için Shap değerlerini tahmin etmek üzere özel ağırlıklı yerel doğrusal regresyon kullanır.
* **Benzeme açıklama**: benzeme açıklama, [genel yedek modelleriyle](https://christophm.github.io/interpretable-ml-book/global.html) ilgili eğitimin modellerini taklit etmek için eğitim fikrini temel alır. Genel yedek modeli, siyah bir kutu modelinin tahmin edilebilmesini mümkün olduğunca doğru şekilde tahmin etmek için eğitilen bir doğası gereği ınterbox modelidir. Veri bilimconu, & kara kutu modeli hakkında ekibinizle çizmek için yedek modeli yorumlayabilir. Aşağıdaki yorumlu modellerden birini yedek modeliniz olarak kullanabilirsiniz: LightGBM (LGBMExplainableModel), doğrusal regresyon (LinearExplainableModel), Stochastic gradyan Descent explainable model (SGDExplainableModel) ve karar ağacı ( DecisionTreeExplainableModel).


* **Permütasyon özelliği önem açıklama**: permütasyon özelliği önem derecesi, [Breiman 'nın rastgele ormanlar kağıdına](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) göre önemli olan sınıflandırma ve regresyon modellerini açıklamak için kullanılan bir tekniktir (bkz. Bölüm 10). Yüksek düzeyde, çalışma şekli, veri kümesinin tamamı için bir seferde bir özelliği rastgele karıştırarak ve ilgilendiğiniz performans ölçüsünün ne kadarını değiştirdiğine göre belirlenir. Değişiklik ne kadar büyükse, bu özellik o kadar önemli olur.

* **Lime açıklama** (`contrib`) [: limon yeşili tabanlı, lime](https://github.com/marcotcr/lime)açıklama yerel yedek modeller oluşturmak Için, son teknoloji yerel yorumlanmış açıklamaları (Lime) algoritmasını kullanır. Genel yedek modellerinden farklı olarak, açık bir şekilde öngörülere yönelik olarak yerel yedek modellerine yönelik olarak, açık bir şekilde
* **Han metin açıklama** (`contrib`): Han Text açıklama, belirli bir siyah kutu metin modelinin metin verilerinden model açıklamaları almak için hiyerarşik bir dikkat ağı kullanır. Belirli bir siyah kutu modelinin öngörülen çıktılarında HAN yedek modelini alır. Corpus metin genelinde eğitimin üzerinde küresel bir şekilde eğitim ettikten sonra, açıklamaları doğruluğunu artırmak için belirli bir belge için ince ayar adımı ekler. HAN, tümce ve sözcük ilgilenilmesi için iki dikkat katmanıyla birlikte çift yönlü bir RNN kullanır. DNN, siyah kutu modeli üzerinde eğitilirken ve belirli bir belgede ince ayar yaptıktan sonra, Kullanıcı dikkat çekici katmanlardan önemli sözcükleri ayıklayabilir. HAN, metin verileri için LIME veya SHAP 'den daha doğru bir şekilde gösterilir, ancak eğitim süresi bakımından daha fazla maliyetli olur. Kullanıcılara eğitim süresini azaltmak için GloVe Word katıştırmasına sahip ağı başlatma seçeneğini sunmak için geliştirmeler yapılmıştır. Eğitim süresi, uzak bir Azure GPU VM 'sinde HAN çalıştırılarak önemli ölçüde artırılabilir. HAN 'in uygulanması, [' belge sınıflandırması Için hiyerarşik dikkat ağları (Yang et al., 2016) '](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification)bölümünde açıklanmaktadır.


* **Tablosal açıklama**: `TabularExplainer` doğrudan [Shap](https://github.com/slundberg/shap) explainers çağırmak için aşağıdaki mantığı kullanır:

    1. Ağaç tabanlı bir modelise, SHAP `TreeExplainer`uygulayın, aksi takdirde
    2. DNN modeli ise, SHAP `DeepExplainer`uygulayın, aksi takdirde
    3. Doğrusal bir modelise, SHAP `LinearExplainer`uygulayın, aksi takdirde
    3. Bunu bir siyah kutu modeli olarak değerlendirin ve SHAP `KernelExplainer` uygulayın


`TabularExplainer`, doğrudan SHAP explainers üzerinde önemli özellikler ve performans iyileştirmeleri de yaptı:

* **Başlatma veri kümesinin Özeti**. Açıklama hızının en önemli olduğu durumlarda, başlatma veri kümesini özetliyoruz ve genel ve yerel açıklamayı hızlandıran küçük bir temsilci örnekleri kümesi oluşturur.
* **Değerlendirme verisi kümesini örnekleme**. Kullanıcı büyük bir değerlendirme örnekleri kümesinde geçerse ancak bunların tümünün değerlendirilmemesi gerekmiyorsa, genel açıklamayı hızlandırmak için örnekleme parametresi true olarak ayarlanabilir.

Aşağıdaki diyagramda, doğrudan ve meta explainers 'in geçerli yapısı gösterilmektedir.

[![Machine Learning Yorumlenebilirlik mimarisi](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Desteklenen modeller

Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`veya `scipy.sparse.csr_matrix` biçimindeki veri kümelerinde eğitilen tüm modeller SDK 'nın yorumlenebilirlik `explain` paketi tarafından desteklenir.

Açıklama işlevleri, giriş olarak hem modelleri hem de işlem hatlarını kabul eder. Bir model sağlanmışsa, modelin, Scikit kuralına uyan `predict` veya `predict_proba` tahmin işlevini uygulaması gerekir. Bir işlem hattı (ardışık düzen betiğinin adı) sağlanmışsa, açıklama işlevi çalışan ardışık düzen betiğinin bir tahmin döndürdüğünü varsayar. PyTorch, TensorFlow ve keras derin öğrenme çerçeveleri aracılığıyla eğitilen modelleri destekliyoruz.

### <a name="local-and-remote-compute-target"></a>Yerel ve uzak işlem hedefi

`explain` paketi hem yerel hem de uzak işlem hedefleri ile çalışacak şekilde tasarlanmıştır. Yerel olarak çalıştırırsanız, SDK işlevleri hiçbir Azure hizmetine başvurmaz. Azure Machine Learning Işlem sırasında açıklamayı Uzaktan çalıştırabilir ve açıklama bilgilerini Azure Machine Learning çalıştırma geçmişi Hizmetleri ' ne kaydedebilirsiniz. Bu bilgiler günlüğe kaydedildikten sonra, açıklamada bulunan raporlar ve görselleştirmeler Kullanıcı analizine yönelik Azure Machine Learning Çalışma Alanı portalında kullanıma hazırdır.


## <a name="next-steps"></a>Sonraki adımlar

Modeller için [, hem](how-to-machine-learning-interpretability-aml.md) yerel olarak hem de uzaktan işlem kaynakları Azure Machine Learning için yorumlenebilirliği etkinleştirme konusuna bakın. Ek senaryolar için [örnek not defterlerine](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/tabular-data) bakın.
