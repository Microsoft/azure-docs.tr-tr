---
title: Machine Learning eşitliği (Önizleme)
titleSuffix: Azure Machine Learning
description: Machine Learning eşitliği hakkında bilgi edinin ve Fairlearn Python paketinin unfairness değerlendirmenize ve azaltmanıza nasıl yardımcı olabileceğini öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 04/02/2021
ms.custom: responsible-ml
ms.openlocfilehash: 45c63780eac021301e5b01c0ff8abea7d704a4cf
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220530"
---
# <a name="machine-learning-fairness-preview"></a>Machine Learning eşitliği (Önizleme)

Machine Learning eşitliği hakkında bilgi edinin ve [Fairlearn](https://fairlearn.github.io/) açık kaynaklı Python paketinin makine öğrenimi modellerinde unfairness sorunları değerlendirmenize ve azaltmanıza nasıl yardımcı olabileceğini öğrenin. 

## <a name="what-is-machine-learning-fairness"></a>Machine Learning eşitliği nedir?

Yapay zeka ve makine öğrenimi sistemleri, dengeli olmayan davranışları gösterebilir. Haksız davranışı tanımlamanın bir yolu, zarar veya insanlar üzerindeki etkilerden biridir. AI sistemlerinin artmasıyla ilgili birçok tür zarar vardır. Daha fazla bilgi için bkz. [Kate Crawford için neurıp 2017 Açılış](https://www.youtube.com/watch?v=fMym_BKWQzk) .

Yaygın olarak kullanılan iki tür AI:

- Ayırma zarar: bir AI sistemi, belirli gruplar için fırsatları, kaynakları veya bilgileri genişletir veya barındırır. Örneğin, bir modelin belirli bir kişi grubu arasında diğer grupların arasından iyi aday çekme işlemleri yaparken çok daha iyi bir şekilde kullanıma hazır olabileceği örnek olarak işe alma, okul adi ve ödünç verme sayılabilir.

- Hizmet kalitesi: bir AI sistemi, başka bir kişi grubu için de çalışır. Örnek olarak, bir ses tanıma sistemi, adam için yaptığı gibi kadınlar için de başarısız olabilir.

AI sistemlerindeki haksız davranışı azaltmak için, bu Harms 'yi değerlendirmeniz ve azaltmanız gerekir.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Fairlearn ile eşitliği değerlendirmesi ve risk azaltma

Fairlearn, makine öğrenimi geliştiricilerin sistemlerini "eşitliği" değerlendirmesini ve unfairness azaltmasını sağlayan açık kaynaklı bir Python paketidir.

>[!NOTE]
> Eşitliği, bir Socio-Technical Challenge. Eşitliği 'in ve son işlem gibi birçok yönü, nicel eşitliği ölçümleri içinde yakalanmaz. Ayrıca, birçok nicel eşitliği ölçümü aynı anda karşılanamayacak. Fairlearn açık kaynaklı paketiyle amaç, insanların farklı etki ve hafifletme stratejilerini değerlendirmesini sağlamaktır. Son olarak, bu, senaryolarına uygun olan ve bu kişilerin kendi senaryosuna uygun olan yapay zeka ve makine öğrenimi modelleri oluşturan insan kullanıcılarına yönelik olarak tasarlanmıştır.

Fairlearn açık kaynak paketinin iki bileşeni vardır:

- Değerlendirme panosu: bir modelin tahminlerinin farklı grupları nasıl etkilediğini değerlendirmek için bir Jupyter Not defteri pencere öğesi. Ayrıca, eşitliği ve performans ölçümlerini kullanarak birden çok modeli karşılaştırmayı de mümkün kılar.
- Risk azaltma algoritmaları: ikili sınıflandırmada ve regresyonda unfairness azaltmaya yönelik bir algoritma kümesi.

Bu bileşenler birlikte, veri bilimcilerinin ve iş liderlerinin eşitliği ve performans arasında herhangi bir denge kurmasını ve ihtiyaçlarına en uygun risk azaltma stratejisini seçmesini sağlar.

## <a name="assess-fairness-in-machine-learning-models"></a>Makine öğrenimi modellerinde eşitliği değerlendirin

Fairlearn açık kaynaklı pakette, eşitliği, **Grup eşitliği** olarak bilinen bir yaklaşım olsa da, hangi kişi gruplarının Harms 'nin risk altında olduğunu soran Alt oluşturmalar olarak da bilinen ilgili gruplar **gizli özellikler** veya gizli öznitelikler aracılığıyla tanımlanır. Hassas özellikler, Fairlearn açık kaynaklı paketteki bir tahmin aracı 'a vektör veya çağrılan bir matris olarak geçirilir  `sensitive_features` . Bu terim, Grup eşitliği değerlendirmek için sistem tasarımcısının bu özelliklere duyarlı olması önerilir. 

En küçük bir şey, bu özelliklerin özel veriler nedeniyle gizlilik etkilerini içerip içermediğini belirtir. Ancak "hassas" sözcüğü, bu özelliklerin tahminlerde kullanılması gerektiğini göstermez.

>[!NOTE]
> Eşitliği değerlendirmesi tamamen teknik bir alıştırma değildir.  Fairlearn açık kaynaklı paket, bir modelin eşitliği 'i değerlendirmenize yardımcı olabilir, ancak değerlendirme gerçekleştirmeyecektir.  Fairlearn açık kaynaklı paket, eşitliği değerlendirmek için nicel ölçümlerini belirlemesine yardımcı olur, ancak geliştiricilerin kendi modellerinin eşitliği değerlendirmek için bir nitel çözümlemesi de gerçekleştirmelidir.  Yukarıda belirtilen hassas özellikler, bu tür bir tür nitel analizinin bir örneğidir.     

Değerlendirme aşaması sırasında, eşitliği, ayırt eşliği ölçülerine göre belirlenir. **Ayırt edilebilir ölçümler** , modelin farklı gruplar üzerindeki davranışlarını, oranlar veya farklar olarak değerlendirebilir ve karşılaştırabilir. Fairlearn açık kaynak paketi iki sınıf ayırt eden ölçüm sınıfını destekler:


- Model performansında ayırıcı: Bu ölçüm kümeleri, farklı alt gruplar genelinde seçili performans ölçümünün değerlerinde ayırt eşliği (fark) hesaplar. Bazı örnekler:

  - doğruluk hızında ayırt eşliği
  - hata hızında ayırt eşliği
  - duyarlılık halinde ayırt eşliği
  - geri çağırma sırasında ayırt eşliği
  - MAE içinde ayırt eşliği
  - diğer birçok

- Seçim hızında ayırıcı: Bu ölçüm, farklı alt gruplar arasındaki seçim oranından farkı içerir. Bu, kredi onayı ücretine eşlik eden bir örnektir. Seçim oranı, her sınıftaki veri noktalarının kesiri olarak 1 (ikili sınıflandırmada) veya tahmin değerlerinin dağılımı (gerileme) anlamına gelir.

## <a name="mitigate-unfairness-in-machine-learning-models"></a>Makine öğrenimi modellerinde unfairness 'i azaltma

### <a name="parity-constraints"></a>Eşlik kısıtlamaları

Fairlearn açık kaynak paketi, çeşitli unfairness azaltma algoritmaları içerir. Bu algoritmalar, tahmine Tor 'un, **eşlik kısıtlamaları** veya ölçütleri olarak adlandırılan davranışındaki bir kısıtlama kümesini destekler. Eşlik kısıtlamaları, tahmine Tor davranışının bazı yönlerini gizli özelliklerin (ör. farklı çces) tanımlanduyduğu gruplar arasında karşılaştırılabilir olmasını gerektirir. Fairlearn açık kaynaklı paketteki risk azaltma algoritmaları, gözlemlenen eşitliği sorunlarını azaltmak için bu tür eşlik kısıtlamalarını kullanır.

>[!NOTE]
> Bir modelde azaltıcı unfairness, unfairness 'in azaltılması anlamına gelir, ancak bu teknik risk azaltma bu unfairness tamamen ortadan kaldırmaz.  Fairlearn açık kaynaklı paketteki unfairness risk azaltma algoritmaları, bir makine öğrenimi modelinde unfairness azaltmaya yardımcı olmak için önerilen risk azaltma stratejileri sunabilir, ancak unfairness tamamen ortadan kaldırmaya yönelik çözümler değildir.  Belirli bir geliştirici makine öğrenimi modeli için göz önünde bulundurmanız gereken başka eşlik kısıtlamaları veya ölçütler olabilir. Azure Machine Learning kullanan geliştiriciler, risk azaltma, makine öğrenimi modellerinin kullanımı ve dağıtımı konusunda herhangi bir unfairness yeterince ortadan kaldırıyorsa kendilerini belirlememelidir.  

Fairlearn açık kaynak paketi aşağıdaki tür eşlik kısıtlamalarını destekler: 

|Eşlik kısıtlaması  | Amaç  |Machine Learning görevi  |
|---------|---------|---------|
|Demografik eşliği     |  Ayırma şiddemini azaltma | İkili sınıflandırma, regresyon |
|Eşitlenmiş gürültü  | Dağıtım ve hizmet kalitesi güvenliğini aşmanızı 'leri tanılayın | İkili sınıflandırma        |
|Eşit fırsat | Dağıtım ve hizmet kalitesi güvenliğini aşmanızı 'leri tanılayın | İkili sınıflandırma        |
|Sınırlanmış grup kaybı     |  Hizmet kalitesi güvenliğini aşmanızı 'leri azaltma | Regresyon |

### <a name="mitigation-algorithms"></a>Risk azaltma algoritmaları

Fairlearn açık kaynak paketi, karşı işlem ve azaltma unfairness azaltma algoritmaları sağlar:

- Azaltma: Bu algoritmalar standart bir siyah kutu Machine Learning tahmin aracı (örn. bir lightgbm modeli) alır ve yeniden ağırlıklı eğitim veri kümeleri dizisi kullanarak bir dizi geri çekme modeli oluşturur. Örneğin, belirli bir cinsiyet olan başvuranlar, modellerin yeniden eğitilmesi ve farklı cinsiyet gruplarının genelinde farklılıkları azaltmak için yukarı ağırlıklı veya aşağı ağırlıklı olabilir. Kullanıcılar daha sonra doğruluk (veya diğer performans ölçümü) arasında en iyi ticareti sağlayan bir model seçebilir ve genellikle iş kurallarını ve maliyet hesaplamalarını temel alan olması gerekir.  
- İşlem sonrası: Bu algoritmalar mevcut bir sınıflandırıcının yanı sıra hassas özelliği giriş olarak alır. Ardından, belirtilen eşitliği kısıtlamalarını zorlamak için sınıflandırıcının tahminiyle bir dönüşüm türetirsiniz. Eşik iyileştirmesinin en büyük avantajı, modeli yeniden eğitmesinin gerekli olmadığı için kolaylık ve esneklik sağlar. 

| Algoritma | Description | Machine Learning görevi | Hassas Özellikler | Desteklenen eşlik kısıtlamaları | Algoritma türü |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | [Bir indirimde bir Indirimleri yaklaşımında](https://arxiv.org/abs/1803.02453) açıklanan, dengeli sınıflandırmada siyah kutu yaklaşımı | İkili sınıflandırma | Kategorik | [Demografik eşliği](#parity-constraints), [eşitlenmiş gürültü](#parity-constraints) | Azaltma |
| `GridSearch` | [Bir indirimde bir azaltmada](https://arxiv.org/abs/1803.02453) tanımlanan siyah kutu yaklaşımı| İkili sınıflandırma | İkili | [Demografik eşliği](#parity-constraints), [eşitlenmiş gürültü](#parity-constraints) | Azaltma |
| `GridSearch` | Kılavuzlu [gerileme: nicel tanımları ve azaltma tabanlı algoritmalarda](https://arxiv.org/abs/1905.12843) açıklanan sınırlı grup kaybı algoritmasıyla birlikte bir ızgara arama varyantı uygulayan siyah kutu yaklaşımı | Regresyon | İkili | [Sınırlanmış grup kaybı](#parity-constraints) | Azaltma |
| `ThresholdOptimizer` | [Denetimli öğreniminde fırsatın kağıt eşitliğine](https://arxiv.org/abs/1610.02413)göre postprocessing algoritması. Bu teknik, mevcut bir sınıflandırıcı ve hassas özelliği giriş olarak alır ve belirtilen eşlik kısıtlamalarını zorlamak için sınıflandırıcının tahminiyle tek tonlu bir dönüşüm türetiliyor. | İkili sınıflandırma | Kategorik | [Demografik eşliği](#parity-constraints), [eşitlenmiş gürültü](#parity-constraints) | İşlem sonrası |

## <a name="next-steps"></a>Sonraki adımlar

- Fairlearn 'in [GitHub](https://github.com/fairlearn/fairlearn/), [Kullanıcı Kılavuzu](https://fairlearn.github.io/main/user_guide/index.html), [örnekler](https://fairlearn.github.io/main/auto_examples/index.html)ve [örnek not defterlerini](https://github.com/fairlearn/fairlearn/tree/master/notebooks)kullanıma alarak farklı bileşenleri nasıl kullanacağınızı öğrenin.
- Azure Machine Learning makine öğrenimi modellerinin eşitliği değerlendirmesini [nasıl](how-to-machine-learning-fairness-aml.md) etkinleştirebileceğinizi öğrenin.
- Azure Machine Learning ek eşitliği değerlendirmesi senaryoları için [örnek not defterlerine](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) bakın. 
