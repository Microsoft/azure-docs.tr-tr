---
title: 'Karar ormanı gerileme: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Karar ağaçlarının bir listesini temel alan bir regresyon modeli oluşturmak için Azure Machine Learning karar ormanı gerileme modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 76fd09b5e2c2540cbc1608558800e7897a6cf839
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94375372"
---
# <a name="decision-forest-regression-module"></a>Karar ormanı gerileme modülü

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

Karar ağaçlarının bir listesini temel alan bir regresyon modeli oluşturmak için bu modülü kullanın.

Modeli yapılandırdıktan sonra, etiketli bir veri kümesi ve [model eğitimi](./train-model.md) modülünü kullanarak modeli eğmeniz gerekir. Daha sonra eğitilen model, tahminleri yapmak için kullanılabilir. 

## <a name="how-it-works"></a>Nasıl çalışır?

Karar ağaçları, her bir örnek için bir dizi basit test gerçekleştiren ve bir yaprak düğümüne (karar) kadar bir ikili ağaç veri yapısına geçiş yapan, parametrik olmayan modellerdir.

Karar ağaçları şu avantajları sunar:

- Eğitim ve tahmin sırasında hem hesaplama hem de bellek kullanımında etkilidir.

- Bunlar, doğrusal olmayan karar sınırlarını temsil edebilirler.

- Tümleşik Özellik seçimi ve sınıflandırması gerçekleştirirler ve gürültülü Özellikler olması halinde esnektir.

Bu regresyon modeli, karar ağaçlarının bir listesini içerir. Regresyon kararı ormanındaki her bir ağaç, bir tahmin olarak bir Gauss dağılımı çıktı. Modeldeki tüm ağaçlar için Birleşik dağıtıma en yakın bir Gauss dağılımı bulmak üzere ağaçları bir toplama işlemi gerçekleştirilir.

Bu algoritmanın ve uygulamasının teorik çerçevesi hakkında daha fazla bilgi için şu makaleye bakın: [karar verme ormanları: sınıflandırma, gerileme, yoğunluk tahmini, bildirim, eski öğrenme ve Semi-Supervised öğrenimi Için Birleşik bir çerçeve](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Karar ormanı regresyon modelini yapılandırma

1. **Karar verme ormanı gerileme** modülünü ardışık düzene ekleyin. **Machine Learning**, **modeli Başlat** ve **gerileme**' nın altında, modülü tasarımcıda bulabilirsiniz.

2. Modül özelliklerini açın ve yeniden **örnekleme yöntemi** için, bireysel ağaçları oluşturmak için kullanılan yöntemi seçin.  **Bagging** veya **çoğaltma** seçeneklerinden birini belirleyebilirsiniz.

    - **Bagging**: Bagging de *önyükleme toplama* olarak adlandırılır. Regresyon kararı ormanındaki her ağaç, tahmin yöntemiyle bir Gauss dağılımı çıkarır. Toplama işlemi, her iki dakika, tek tek ağaçlar tarafından döndürülen tüm dağıtımları birleştirerek verilen Gauss dağıtımlarından oluşan bir süre ile eşleşen bir Gauss bulmadır.

         Daha fazla bilgi için bkz. [önyükleme toplama](https://wikipedia.org/wiki/Bootstrap_aggregating)Için Vikipedi girişi.

    - **Çoğaltma:** çoğaltmadaki her ağaç, tam olarak aynı giriş verilerinde eğitilir. Her ağaç düğümü için hangi bölünmüş koşulun kullanıldığını belirleme rasgele kalır ve ağaçlar birbirinden farklı olur.

         **Çoğalt** seçeneğiyle eğitim süreci hakkında daha fazla bilgi için bkz [. görüntü işleme ve tıbbi görüntü analizi için karar ormanları. Criminisi ve J. Shotton. Sprte 2013.](https://research.microsoft.com/projects/decisionforests/).

3. Model **oluşturma modunu** ayarlayarak modelin eğitilme şeklini belirleyin.

    - **Tek parametre**

      Modeli nasıl yapılandırmak istediğinizi biliyorsanız bağımsız değişken olarak belirli bir değer kümesi sağlayabilirsiniz. Bu değerleri deneme göre öğrenmiş veya bunları kılavuz olarak almış olabilirsiniz.

    - **Parametre aralığı**: en iyi parametrelerden emin değilseniz ve bir parametre süpürme çalıştırmak istiyorsanız bu seçeneği belirleyin. Yinelemek için bir değer aralığı seçin ve [ayarlama modeli hiper parametreleri](tune-model-hyperparameters.md) , en iyi sonuçları üreten hiper parametreleri belirlemek için, belirttiğiniz ayarların tüm olası birleşimlerinin üzerinde yinelenir. 



4. **Karar ağaçları sayısı** için, tam olarak birleştirmek üzere oluşturulacak karar ağacının toplam sayısını belirtin. Daha fazla karar ağacı oluşturarak daha iyi tedarik sağlayabilirsiniz, ancak eğitim süresi artar.

    > [!TIP]
    > Değerini 1 olarak ayarlarsanız Bununla birlikte, bu, yalnızca bir ağacın üretileceği (ilk parametre kümesini içeren ağaç) ve başka bir yinelemenin gerçekleştirilmeyeceği anlamına gelir.

5. **Karar ağaçlarının maksimum derinliği** için, herhangi bir karar ağacının maksimum derinliğini sınırlamak üzere bir sayı yazın. Ağacın derinliğini artırmak, bazı fazla sığdırma ve daha fazla eğitim süresi riskinde duyarlık artırabilir.

6. **Düğüm başına rastgele bölme sayısı** için, ağacın her düğümünü oluştururken kullanılacak bölme sayısını yazın. *Bölünmüş* , ağaç (node) düzeyindeki özelliklerin rastgele bölündüğü anlamına gelir.

7. **Yaprak düğüm başına minimum örnek sayısı** için, bir ağaçta herhangi bir Terminal düğümü (yaprak) oluşturmak için gereken minimum durum sayısını belirtin.

     Bu değeri artırarak, yeni kurallar oluşturma eşiğini artırırsınız. Örneğin, varsayılan 1 değeri ile tek bir durum bile yeni bir kuralın oluşturulmasına neden olabilir. Değeri 5 ' e artırırsanız eğitim verilerinin aynı koşulları karşılayan en az beş durum içermesi gerekir.


9. Modeli eğitme:

    + **Tek parametre** için bir görüntü **oluşturma modu** ayarlarsanız, etiketli bir veri kümesini ve [model eğitimi](train-model.md) modülünü bağlayın.  
  
    + **Parametre aralığına** **oluşturma** , bir etiketli veri kümesini bağlama ve modeli [Ayarla hiper parametrelerini](tune-model-hyperparameters.md)kullanarak modeli eğitme.  
  
    > [!NOTE]
    > 
    > [Modeli Eğiteetmek](train-model.md)için bir parametre aralığı geçirirseniz, tek parametre listesindeki yalnızca varsayılan değeri kullanır.  
    > 
    > Tek bir parametre değerleri kümesini [ayarlama modeli hiper parametreleri](tune-model-hyperparameters.md) modülüne geçirirseniz, her parametre için bir dizi ayar beklerken, değerleri yoksayar ve öğrenici için varsayılan değerleri kullanır.  
    > 
    > **Parametre aralığı** seçeneğini belirleyip herhangi bir parametre için tek bir değer girerseniz, belirtilen tek değer, diğer parametrelerin bir değer aralığı üzerinde değişse bile, tarama boyunca kullanılır.

   

10. İşlem hattını gönderme.

### <a name="results"></a>Sonuçlar

Eğitim tamamlandıktan sonra:

+ Eğitilen modelin anlık görüntüsünü kaydetmek için eğitim modülünü seçin ve sağ paneldeki **çıktılar** sekmesine geçin. Simge **kayıt modeline** tıklayın.  Kaydedilmiş modeli modül ağacında bir modül olarak bulabilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 