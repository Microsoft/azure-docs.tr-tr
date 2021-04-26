---
title: Oto ml ile & imsiz verileri aşırı sığdırmayı önleyin
titleSuffix: Azure Machine Learning
description: Azure Machine Learning otomatik makine öğrenimi çözümleri ile ortak yönetim modellerini tanımlayabilir ve yönetin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/09/2020
ms.openlocfilehash: ba85bda1e322d3efd467527b48bd4cd90eb7ce8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96922615"
---
# <a name="prevent-overfitting-and-imbalanced-data-with-automated-machine-learning"></a>Otomatik makine öğrenimi ile fazla sığdırma ve ıdengeli verileri önleme

Makine öğrenimi modelleri oluştururken, çok fazla sığdırma ve ı, imdengeli veriler yaygın olarak kullanılır. Varsayılan olarak, Azure Machine Learning otomatik makine öğrenimi, bu riskleri belirlemenize yardımcı olacak grafikler ve ölçümler sağlar ve bunları azaltmaya yardımcı olmak için en iyi yöntemleri uygular. 

## <a name="identify-over-fitting"></a>Fazla sığdırmayı tanımla

Makine öğreniminde fazla sığdırma, bir model eğitim verilerine çok uygun olduğunda oluşur ve sonuç olarak, görünmeyen test verilerini doğru şekilde tahmin edemeyebilir. Diğer bir deyişle, model eğitim verilerinde yalnızca belirli desenleri ve paraziti daha kolay hale getirir, ancak gerçek verilere ilişkin tahminleri yapmak için yeterince esnek değildir.

Aşağıdaki eğitilen modelleri ve bunlara karşılık gelen tren ve test accuracies göz önünde bulundurun.

| Modelleme | Tren doğruluğu | Test doğruluğu |
|-------|----------------|---------------|
| A | %99,9 | %95 |
| B | %87 | %87 |
| C | %99,9 | %45 |

Model **A**'yı düşünürken, görülmeyen veriler üzerinde test doğruluğu eğitim doğruluğunun altındaysa, modelin daha fazla uydurulur. Ancak, test doğruluğu her zaman eğitim doğruluğunu azaltır ve fazla sığdırma ile *uygun şekilde ayrım, daha az doğru* olacaktır. 

**A** ve **B** modellerini karşılaştırırken, **bir** model, daha yüksek test doğruluğu içerdiğinden daha iyi bir modeldir ve test doğruluğu %95 ' de biraz daha düşük olsa da, çok fazla sığdırma öneren önemli bir fark değildir. Tren ve test accuracies birlikte daha yakından olduğu için model **B** 'yi seçemezsiniz.

Model **C** , aşırı sığdırma durumunu açık bir şekilde temsil eder; Eğitim doğruluğu çok yüksektir, ancak test doğruluğu yüksek olan her yerde değildir. Bu ayrım öznel, ancak sorununuz ve verileriniz hakkında bilgi sahibi olur ve hata magnitudes ne olduğunu kabul edilebilir.

## <a name="prevent-over-fitting"></a>Fazla sığdırmayı engelle

En egregious durumlarda, daha fazla olan bir model, eğitim sırasında görülen Özellik değeri birleşimlerinin her zaman hedef için aynı çıkışa neden olacağını varsayacaktır.

Fazla sığdırmayı önlemenin en iyi yolu, aşağıdakiler dahil olmak üzere ML en iyi yöntemlerini izlemelidir:

* Daha fazla eğitim verisi kullanma ve istatistiksel farkı ortadan kaldırma
* Hedef sızıntısı engelleniyor
* Daha az özellik kullanma
* **Düzenleme ve hyperparameter iyileştirmesi**
* **Model karmaşıklık sınırlamaları**
* **Çapraz doğrulama**

Otomatikleştirilmiş ML bağlamında, yukarıdaki ilk üç öğe **en iyi uygulama uygulamalardır**. Son üç kalın öğe, **en iyi uygulamalar OTOMATIK ml** 'nin, aşırı sığdırma ile karşı koruma için varsayılan olarak uygular. Otomatik ML dışındaki ayarlarda, tüm altı en iyi uygulamalar, çok sığdırma modellerini kullanmaktan kaçınmak için aşağıda verilmiştir.

### <a name="best-practices-you-implement"></a>Uyguladığınız en iyi uygulamalar

**Daha fazla veri** kullanmak, aşırı sığdırmayı önlemenin en basit ve en iyi yoludur ve ek bir prim genellikle doğruluğu arttırır. Daha fazla veri kullandığınızda, modelin tam desenleri daha zor hale getirmek daha zordur ve daha fazla koşullara uyum sağlamak için daha esnek çözümlere ulaşmaya zorlanır. Eğitim verilerinizin canlı tahmin verilerinde mevcut olmayan yalıtılmış desenler içermediğinden emin olmak için **istatistiksel farkı** tanımak de önemlidir. Eğeceğiniz ve test kümleriniz arasında aşırı yerleştirme bulunmayabilir, ancak canlı test verileriyle karşılaştırıldığında çok fazla sığdırma söz konusu olabileceğinden, bu senaryoya çözüm daha zor olabilir.

**Hedef sızıntısı** , eğitim/test kümeleri arasında fazla sığdırmayı görmeyebilirsiniz, ancak bunun yerine tahmin sırasında görünmesini mümkün bir sorundur. Hedef sızıntısı, modelinize "Cheats", normalde tahmin zamanında sahip olmadığı verilere erişim izni vererek oluşur. Örneğin, sorununuz Pazartesi günü ücretlendiriyor, ancak özelliklerden biri yanlışlıkla Perşembe ' dan veri içeriyordu, ancak bu veri, bu durum, gelecekte göremeyeceği için bir tahmin süresi değildir. Hedef sızıntısı, kaçırılması kolay bir hata, ancak genellikle sorununuz için anormal ölçüde yüksek doğruluk ile ayırdedilir. Hisse senedi fiyatını tahmin etmeye ve %95 doğruluk oranında bir model eğitimeye çalışıyorsanız, özelliklerinizin bir yerinde hedef sızıntısı olabilir.

**Özelliklerin kaldırılması** , modelin belirli desenleri kaldırmak için çok fazla alan kullanmasını önleyecek ve bu sayede daha esnek olmasına neden olabilir. Ölçüyü Tayana ölçmek zor olabilir, ancak özellikleri kaldırabilmeniz ve aynı doğruluğu koruuyorsanız, büyük olasılıkla modeli daha esnek hale yapmış ve fazla sığdırma riskini azaltacaksınız.

### <a name="best-practices-automated-ml-implements"></a>En iyi uygulamalar otomatik ML uygulamaları

**Düzenleme** , karmaşık ve daha fazla olan modelleri sızmak için bir maliyet işlevini en aza indirmenizi sağlar. Farklı türlerde düzenleme işlevleri vardır, ancak genel olarak model katsayı boyutunu, varyansı ve karmaşıklığını tamamen penler. Otomatik ML, L1 (Kement), L2 (Ridge) ve Elaviznet (L1 ve L2 eşzamanlı) ' i, farklı birleşimlerde fazla sığdırmayı denetleyen farklı model hiper parametre ayarlarına sahip farklı bileşimlerde kullanır. Basit koşullarda, otomatik ML bir modelin ne kadarının düzenlenebilir olduğunu farklılık gösterecektir ve en iyi sonucu seçer.

Otomatik ML, aşırı sığdırmayı engellemek için açık **model karmaşıklığı kısıtlamalarını** da uygular. Çoğu durumda bu uygulama özellikle karar ağacı veya orman algoritmalarının yanı sıra, tek tek ağaç en yüksek derinliğinin sınırlı olduğu ve ormanda kullanılan toplam ağaç sayısı veya ensesıya da zenginlik teknikleri sınırlıdır.

**Çapraz doğrulama (CV)** , tam eğitim verilerinizin çok sayıda alt kümesini alma ve her bir alt kümede bir modeli eğitme işlemidir. Bu, bir modelin "Lucky" alabilir ve bir alt küme ile harika bir doğruluk elde etmenizi sağlar, ancak birçok alt küme kullanarak modelin her seferinde bu yüksek doğruluğu elde edilmeyeceği. CV 'yi yaparken, bir doğrulama veri kümesi sağlarsınız, CV katlarınızı (alt küme sayısı) belirtin ve otomatik ML, doğrulama kümenizdeki hatayı en aza indirmek için modelinize eğitme ve hiper parametreleri ayarlamanıza yardımcı olur. Bir CV katlaması daha fazla olabilir, ancak bunların çoğunu kullanarak, son modelinizin daha fazla sığdırma olasılığını azaltır. Zorunluluğunu getirir, CV 'nin bir kez eğitim sağladığından, her *n* CV alt kümesi için bir kez eğitecaksınız. 

> [!NOTE]
> Çapraz doğrulama varsayılan olarak etkinleştirilmemiştir; Otomatik ML ayarları ' nda yapılandırılması gerekir. Ancak, çapraz doğrulama yapılandırıldıktan ve bir doğrulama veri kümesi sağlandıktan sonra, işlem sizin için otomatikleştirilir. [OTOMATIK ml 'de çapraz doğrulama yapılandırması](how-to-configure-cross-validation-data-splits.md) hakkında daha fazla bilgi edinin

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>İmdengelenmiş verilerle modelleri tanımla

Makine öğrenimi sınıflandırma senaryolarına ilişkin verilerde yaygın olarak imledengeli veriler bulunur ve her sınıfta orantısız oranını içeren verilere başvurur. Bu dengesizlik, bir modelin doğruluğu üzerinde sapma yaptığından, bu durum, eğitim modelinin bu farkı taklit etmek için eğitilmesine neden olacak 

Ayrıca, otomatik ML çalıştırmaları aşağıdaki grafikleri otomatik olarak oluşturur. Bu, modelinizdeki sınıflandırmaların doğruluğunu anlamanıza yardımcı olabilir ve imdenli verilerden etkilenen modelleri tanımlayabilir.

Grafik| Description
---|---
[Karışıklık matrisi](how-to-understand-automated-ml.md#confusion-matrix)| Doğru sınıflandırılan etiketleri verilerin gerçek etiketlerine göre değerlendirir. 
[Precision-geri çek](how-to-understand-automated-ml.md#precision-recall-curve)| Doğru etiketlerin oranını, verilerin bulunan etiket örneklerinin oranına göre değerlendirir 
[ROC eğrileri](how-to-understand-automated-ml.md#roc-curve)| Doğru etiketlerin oranını, yanlış pozitif etiketlerin oranına göre değerlendirir.

## <a name="handle-imbalanced-data"></a>İmdengeli verileri işle 

Machine Learning iş akışını basitleştirmenin bir parçası olarak, otomatik ML, gibi imdengelenmiş verilerle başa çıkmanıza yardımcı olmak için **yerleşik yeteneklere sahiptir** 

- **Ağırlık sütunu**: otomatik ml, bir sınıfı daha fazla veya daha az "önemli" haline getirmek için kullanılabilen verilerin ağırlıklı veya aşağı bir şekilde kalınlığını sağlar.

- Minınor sınıfında örnek sayısı en az örnek olan bir değere eşitse ve çoğunluk sınıfının çoğu örnek içeren bir değeri ifade ettiği durumlarda, otomatik ML tarafından kullanılan algoritmalar, minalalallık sınıfındaki örnek sayısı %20 ' ye eşit veya ondan daha az Daha sonra, bu sorunu gidermek ve performansı artırmak için, oto ml 'nin, Sub-örneklenmiş verilerle bir deneme çalıştırması gerekir. Bu deneme boyunca daha iyi bir performans yoklarsa bu çözüm uygulanır.

- İmdengeli verilerle daha iyi anlaşmalar sağlayan bir performans ölçümü kullanın. Örneğin AUC_weighted, bu sınıfı temsil eden örneklerin göreli sayısına göre her sınıfın katkısını hesaplayan birincil bir ölçümdür, bu nedenle dengesizine karşı daha sağlamdır.

Aşağıdaki teknikler, **OTOMATIK ml dışında** imdengeli verileri işlemek için ek seçeneklerdir. 

- Daha küçük sınıfları örnekleyerek veya daha büyük sınıfları aşağı örnekleyerek sınıf dengesizliği olarak yeniden örnekleme yapın. Bu yöntemler, işlemek ve analiz etmek için uzmanlık gerektirir.

- İmdengelenmiş veriler için performans ölçümlerini gözden geçirin. Örneğin, F1 puanı duyarlık ve geri çağırma 'nin harmonik ortalaması olur. Duyarlık, bir sınıflandırıcının exactness ' i ölçer, burada daha yüksek duyarlık daha az yanlış pozitif sonuç gösteriyorsa, geri çağırma bir sınıflandırıcının tamamlanmasını ölçer ve daha yüksek geri çekmenin daha az yanlış negatifleri gösterir.

## <a name="next-steps"></a>Sonraki adımlar

Bkz. örnekler ve otomatik makine öğrenimi kullanarak modeller oluşturmayı öğrenme:

+ [Öğreticiyi izleyin: Azure Machine Learning ile regresyon modelini otomatik olarak eğitme](tutorial-auto-train-models.md)

+ Otomatik eğitim denemenize yönelik ayarları yapılandırın:
  + Azure Machine Learning Studio 'da [Bu adımları kullanın](how-to-use-automated-ml-for-ml-models.md).
  + Python SDK ile [Bu adımları kullanın](how-to-configure-auto-train.md).


