---
title: 'ML Studio (klasik): algoritmaları Iyileştirme-Azure'
description: Azure Machine Learning Studio (klasik) ' de bir algoritma için en uygun parametre kümesini nasıl seçebileceğinizi açıklar.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: a216dc7cb17b9e35e412c6bebe34c0cccfb732e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519312"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-machine-learning-studio-classic"></a>Machine Learning Studio algoritmalarınızı iyileştirmek için parametreler seçin (klasik)

**Uygulama hedefi:** ![ İçin geçerlidir. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasik) ![ için geçerlidir.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Bu konuda, Azure Machine Learning Studio (klasik) ' de bir algoritma için doğru hiper parametre kümesinin nasıl oluşturulacağı açıklanmaktadır. Çoğu makine öğrenimi algoritmalarının ayarlanması parametreleri vardır. Bir modeli eğitedığınızda, bu parametrelerin değerlerini sağlamanız gerekir. Eğitilen modelin göre etkinliğine seçtiğiniz model parametrelerine bağlıdır. En iyi parametre kümesini bulma işlemi *model seçimi* olarak bilinir.



Model seçimi yapmak için çeşitli yollar vardır. Machine Learning 'de, çapraz doğrulama, model seçimi için en yaygın olarak kullanılan yöntemlerden biridir ve Azure Machine Learning Studio (klasik) ' de varsayılan model seçme mekanizmasından biridir. Azure Machine Learning Studio (klasik) hem R hem de Python desteklediğinden, R veya Python kullanarak her zaman kendi model seçim mekanizmalarının uygulanmasını sağlayabilirsiniz.

En iyi parametre kümesini bulma işleminde dört adım vardır:

1. **Parametre alanını tanımlayın**: algoritma için önce dikkate almak istediğiniz parametre değerlerini kesin olarak belirleyin.
2. **Çapraz doğrulama ayarlarını tanımlayın**: veri kümesi için çapraz doğrulama katlarının nasıl seçileceğine karar verin.
3. **Ölçüyü tanımlayın**: doğruluk, kök ortalama kare hatası, duyarlık, geri çağırma veya f puanı gibi en iyi parametre kümesini belirlemek için hangi metriğin kullanılacağına karar verin.
4. **Eğitme, değerlendirme ve karşılaştırma**: parametre değerlerinin her benzersiz birleşimi için, çapraz doğrulama tarafından ve tanımladığınız hata ölçümüne göre yürütülür. Değerlendirme ve karşılaştırmadan sonra en iyi şekilde gerçekleştiren modeli seçebilirsiniz.

Aşağıdaki görüntüde bunun Azure Machine Learning Studio (klasik) ' de nasıl elde edilebileceği gösterilmektedir.

![En iyi parametre kümesini bulun](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Parametre alanını tanımlayın
Model başlatma adımında parametre kümesini tanımlayabilirsiniz. Tüm makine öğrenimi algoritmalarının parametre bölmesinde iki farklı mod vardır: *tek parametre* ve *parametre aralığı*. Parametre aralığı modunu seçin. Parametre aralığı modunda, her bir parametre için birden çok değer girebilirsiniz. Metin kutusuna virgülle ayrılmış değerler girebilirsiniz.

![İki sınıf önceden artırılmış karar ağacı, tek parametre](./media/algorithm-parameters-optimize/fig2.png)

 Alternatif olarak, kılavuzun maksimum ve en düşük noktalarını ve **kullanım aralığı Oluşturucu** ile oluşturulacak toplam punto sayısını tanımlayabilirsiniz. Varsayılan olarak, parametre değerleri doğrusal bir ölçekte oluşturulur. Ancak, **günlük ölçeği** işaretlenirse, değerler günlük ölçeğinde oluşturulur (yani, bitişik noktaların oranı, aralarındaki fark yerine sabittir). Tamsayı parametreleri için, bir aralığı tire kullanarak tanımlayabilirsiniz. Örneğin, "1-10", 1 ile 10 arasındaki (ikisi de dahil) tüm tamsayılar parametre kümesini oluşturur. Karma mod da desteklenir. Örneğin, "1-10, 20, 50" parametre kümesi, 1-10, 20 ve 50 tamsayıları içerir.

![İki sınıf önceden artırılmış karar ağacı, parametre aralığı](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Çapraz doğrulama katlarını tanımlama
[Bölüm ve örnek][partition-and-sample] modül, verileri rastgele atamak için kullanılabilir. Modül için aşağıdaki örnek yapılandırmada beş kat tanımlarlar ve örnek örneklere rastgele katlama numarası atamalısınız.

![Bölüm ve örnek](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Ölçümü tanımlama
Set [model Hyperparameters][tune-model-hyperparameters] modülü, belirli bir algoritma ve veri kümesi için en iyi parametre kümesini seçme için destek sağlar. Modele eğitim ile ilgili diğer bilgilere ek olarak, Bu modülün **Özellikler** bölmesi en iyi parametre kümesini belirlemek için ölçüm içerir. Sınıflandırma ve regresyon algoritmaları için sırasıyla iki farklı açılan liste kutusu vardır. Değerlendirme kapsamındaki algoritma bir sınıflandırma algoritmasıdır, regresyon ölçümü yok sayılır ve tam tersi de geçerlidir. Bu belirli örnekte, ölçüm **doğruluk** olur.   

![Tarama parametreleri](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Eğitme, değerlendirme ve karşılaştırma
Aynı [ayar modeli Hyperparameters][tune-model-hyperparameters] modülü, parametre kümesine karşılık gelen tüm modelleri de hesaplar, çeşitli ölçümleri değerlendirir ve seçtiğiniz ölçümü temel alarak en iyi eğitilen modeli oluşturur. Bu modülün iki zorunlu girişi vardır:

* Eğitilen öğrenici
* Veri kümesi

Modülün Ayrıca isteğe bağlı bir veri kümesi girişi vardır. Veri kümesini katlama bilgisi ile zorunlu veri kümesi girişine bağlayın. Veri kümesine herhangi bir katlama bilgisi atanmamışsa, varsayılan olarak 10 katlı çapraz doğrulama otomatik olarak yürütülür. Katlama ataması yapılmazsa ve isteğe bağlı veri kümesi bağlantı noktasında bir doğrulama veri kümesi sağlandıysa, bir eğitim testi modu seçilir ve ilk veri kümesi modeli her bir parametre birleşimi için eğitede kullanılır.

![Artırılmış karar ağacı Sınıflandırıcısı](./media/algorithm-parameters-optimize/fig6a.png)

Daha sonra model doğrulama veri kümesi üzerinde değerlendirilir. Modülün sol çıkış bağlantı noktası, parametre değerlerinin işlevleri olarak farklı ölçümleri gösterir. Doğru çıkış bağlantı noktası, seçilen ölçüye (Bu durumda **doğruluk** ) göre en iyi şekilde uygulanan modele karşılık gelen eğitilen modeli sağlar.  

![Doğrulama veri kümesi](./media/algorithm-parameters-optimize/fig6b.png)

Doğru çıkış bağlantı noktasını görselleştirerek seçilen parametreleri tam olarak görebilirsiniz. Bu model, eğitilen bir model olarak kaydedildikten sonra bir test kümesi veya bir işlem tarafından kullanılan Web hizmetinde Puanlama içinde kullanılabilir.

<!-- Module References -->
[partition-and-sample]: /azure/machine-learning/studio-module-reference/partition-and-sample
[tune-model-hyperparameters]: /azure/machine-learning/studio-module-reference/tune-model-hyperparameters