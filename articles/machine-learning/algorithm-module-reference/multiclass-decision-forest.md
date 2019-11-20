---
title: 'Birden çok Lass karar ormanı: modül başvurusu'
titleSuffix: Azure Machine Learning
description: "*Karar ormanı* algoritmasına dayalı bir makine öğrenimi modeli oluşturmak için Azure Machine Learning 'de çok Lass karar ormanı modülünü nasıl kullanacağınızı öğrenin."
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 85459fe586ccbb5adb1e86b153ec63252c0cce2d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466012"
---
# <a name="multiclass-decision-forest-module"></a>Birden çok Lass karar ormanı modülü

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

*Karar ormanı* algoritmasını temel alan bir makine öğrenimi modeli oluşturmak için bu modülü kullanın. Karar verme ormanı, etiketli verilerden öğrenirken, bir dizi karar ağacının hızla bir dizisini oluşturan bir ensebir modeldir.

## <a name="more-about-decision-forests"></a>Karar ormanları hakkında daha fazla bilgi

Karar orman algoritması, sınıflandırma için bir ensebölümlü öğrenme yöntemidir. Algoritma, birden çok karar ağacı oluşturup en popüler çıkış sınıfı üzerinde *oy* vererek işe yarar. Oylama, bir sınıflandırma kararı ormanındaki her bir ağacın etiketlerin Normalleştirilmemiş bir sıklık histogramını çıktılarından oluşan bir toplama biçimidir. Toplama işlemi, bu histogramları toplar ve her etiket için "olasılıkların" elde edilmesine ilişkin sonucu normalleştirir. Yüksek tahmin güvenilirliği olan ağaçlar, en son karar veren kararına daha büyük bir ağırlığa sahiptir.

Genel içindeki karar ağaçları, farklı dağıtımlarla verileri destekledikleri anlamına gelen, parametrik modellerdir. Her bir ağaçta, her sınıf için bir dizi basit test çalıştırılır ve bir yaprak düğümüne (karar) kadar bir ağaç yapısının düzeylerini artırır.

Karar ağaçları birçok avantaj sunar:

+ Bunlar, doğrusal olmayan karar sınırlarını temsil edebilirler.
+ Eğitim ve tahmin sırasında hesaplama ve bellek kullanımında etkilidir.
+ Tümleşik Özellik seçimi ve sınıflandırması gerçekleştirir.
+ Bu kişiler, gürültülü Özellikler bulunması halinde esnektir.

Azure Machine Learning karar ormanı Sınıflandırıcısı, karar ağaçlarının bir listesini içerir. Genel olarak, en iyi şekilde modelleyen modeller, tek karar ağaçlarından daha iyi kapsam ve doğruluk sağlar. Daha fazla bilgi için bkz. [karar ağaçları](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Birden çok Lass karar ormanını yapılandırma

1. Tasarımcı 'daki işlem hattınıza çok **Lass karar ormanı** modülünü ekleyin. Bu modülü **Machine Learning**, **modeli başlatabilir**ve **sınıflandırmada**bulabilirsiniz.

2. **Özellikler** bölmesini açmak için modüle çift tıklayın.

3. Yeniden **örnekleme yöntemi**için, bireysel ağaçları oluşturmak için kullanılan yöntemi seçin.  Bagging veya çoğaltma arasından seçim yapabilirsiniz.

    + **Bagging**: Bagging de *önyükleme toplama*olarak adlandırılır. Bu yöntemde, her ağaç yeni bir örnek üzerinde büyüerek orijinal veri kümesini rastgele örnekleyerek, özgün veri kümesinin orijinal bir veri kümesine sahip olana kadar bir şekilde oluşturulur. Modellerin çıkışları, bir toplama biçimi olan *Oylama*tarafından birleştirilir. Daha fazla bilgi için bkz. önyükleme toplama için Vikipedi girişi.

    + **Çoğaltma:** çoğaltmadaki her ağaç, tam olarak aynı giriş verilerinde eğitilir. Her ağaç düğümü için hangi bölünmüş koşulun kullanıldığını belirleme rastgele kalır ve farklı ağaçlar oluşturur.

   

4. Model **oluşturma modunu** ayarlayarak modelin eğitilme şeklini belirleyin.

    + **Tek parametre**: modeli nasıl yapılandırmak istediğinizi biliyorsanız ve bağımsız değişken olarak bir değer kümesi sağlamak için bu seçeneği belirleyin.


5. **Karar ağacının sayısı**: en yüksek sayıda karar ağacının, en fazla bir şekilde oluşturulabilir. Daha fazla karar ağacı oluşturarak daha iyi kapsam edinebilirsiniz, ancak eğitim süresi artabilir.

    Bu değer Ayrıca, eğitilen modeli görselleştirirken sonuçlarda görünen ağaç sayısını da denetler. Tek bir ağacı görmek veya yazdırmak için, değeri 1 olarak ayarlayabilirsiniz. Ancak, bu, yalnızca bir ağacın üretilebileceği (ilk parametre kümesini içeren ağaç) ve başka bir yinelemenin gerçekleştirilmeyeceği anlamına gelir.

6. **Karar ağaçlarının en yüksek derinliği**: herhangi bir karar ağacının maksimum derinliğini sınırlamak için bir sayı yazın. Ağacın derinliğini artırmak, bazı fazla sığdırma ve daha fazla eğitim süresi riskinde duyarlık artırabilir.

7. **Düğüm başına rastgele bölme sayısı**: ağacın her bir düğümünü oluştururken kullanılacak bölme sayısını yazın. *Bölünmüş* , ağaç (node) düzeyindeki özelliklerin rastgele bölündüğü anlamına gelir.

8. **Yaprak düğüm başına minimum örnek sayısı**: bir ağaçta herhangi bir Terminal düğümü (yaprak) oluşturmak için gereken minimum durum sayısını belirtin. Bu değeri artırarak, yeni kurallar oluşturma eşiğini artırırsınız.

    Örneğin, varsayılan 1 değeri ile tek bir durum bile yeni bir kuralın oluşturulmasına neden olabilir. Değeri 5 ' e artırırsanız eğitim verilerinin aynı koşulları karşılayan en az beş durum içermesi gerekir.



10. Etiketli bir veri kümesini ve eğitim modüllerden birini bağlayın:

    + **Tek parametreye**oluşturma ve ayarlama **modunu** ayarlarsanız, [model eğitme](./train-model.md) modülünü kullanın.

11. İşlem hattını çalıştırma.

## <a name="results"></a>Sonuçlar

Eğitim tamamlandıktan sonra:

+ Her yinelemede oluşturulan ağacı görmek için [eğitim modeli](./train-model.md) modülünün çıktısına sağ tıklayın ve **Görselleştir**' i seçin.
+ Her bir düğümün kurallarını görmek için, her bir ağaca tıklayarak bölünmeleri inceleyin.


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 