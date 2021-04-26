---
title: 'Two-Class karar ormanı: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Karar ormanları algoritması temelinde bir makine öğrenimi modeli oluşturmak için Azure Machine Learning Two-Class karar ormanı modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/03/2020
ms.openlocfilehash: c4c303a7640454ba0cb6622b21fd161354266068
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94375950"
---
# <a name="two-class-decision-forest-module"></a>Two-Class karar ormanı modülü

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

Karar ormanları algoritmaya dayalı bir makine öğrenimi modeli oluşturmak için bu modülü kullanın.  

Karar ormanları hızlı, denetimli modellerdir. En fazla iki sonuç içeren bir hedef tahmin etmek istiyorsanız bu modül iyi bir seçimdir. 

## <a name="understanding-decision-forests"></a>Karar ormanları anlama

Bu karar ormanı algoritması, sınıflandırma görevlerine yönelik bir ensebir öğrenme yöntemidir. Ensebirleştirileyen Yöntemler, tek bir modele güvenmek yerine, daha iyi sonuçlar ve daha genelleştirilmiş bir model oluşturarak, birden fazla ilgili model oluşturup bunları bir şekilde birleştirerek daha fazla bilgi edinebilirsiniz. Genel olarak, en iyi şekilde modelleyen modeller, tek karar ağaçlarından daha iyi kapsam ve doğruluk sağlar. 

Tek tek modeller oluşturmanın ve bunları bir ensede birleştirmenin birçok yolu vardır. Karar ormanı 'nın bu belirli uygulanması, birden çok karar ağacı oluşturup en popüler çıkış sınıfı üzerinde **Oylama** yaparak işe yarar. Oylama, bir ensebir modelde sonuç oluşturmaya yönelik daha iyi bilinen yöntemlerden biridir. 

+ Birçok ayrı sınıflandırma ağacının tamamı, tüm veri kümesi kullanılarak oluşturulur, ancak farklı (genellikle rastgele) başlangıç noktaları kullanılır. Bu, tek tek karar ağaçlarının yalnızca rastgele veri veya özellik bölümünü kullanabileceği rastgele orman yaklaşımına göre farklılık gösterir.
+ Karar ormanı ağacındaki her bir ağaç, etiketlerin normalleştirilmiş olmayan bir sıklık histogramını çıktı. 
+ Toplama işlemi, bu histogramları toplar ve her etiket için "olasılıkların" elde edilmesine ilişkin sonucu normalleştirir. 
+ Yüksek tahmin güvenli olan ağaçlar, en son kararının son kararına göre daha büyük bir ağırlığa sahip olacaktır.

Genel içindeki karar ağaçları, sınıflandırma görevlerinde birçok avantaj sağlar:
  
- Bu kişiler, doğrusal olmayan karar sınırlarını yakalayabilir.
- Hesaplama ve bellek kullanımında çok sayıda veriyi eğitebilir ve tahmin edebilirsiniz.
- Özellik seçimi eğitim ve sınıflandırma işlemlerinde tümleşiktir.  
- Ağaçlar, gürültülü verilere ve birçok özelliğe sahip olabilir.  
- Bunlar parametrik olmayan modellerdir, yani değişen dağıtımlar ile verileri işleyebilir. 

Ancak, basit karar ağaçları verilere fazla uyum sağlayabilir ve Tree Kümelemeler 'tan daha az genelleştirilebilir.

Daha fazla bilgi için bkz. [karar ormanları](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Yapılandırma
  
1.  Azure Machine Learning ' de **Iki sınıf karar ormanı** modülünü ardışık düzene ekleyin ve modülün **Özellikler** bölmesini açın. 

    Modül **Machine Learning** altında bulunabilir. **Başlat**' ı ve ardından **Sınıflandırma**' yı genişletin.  
  
2.  Yeniden **örnekleme yöntemi** için, bireysel ağaçları oluşturmak için kullanılan yöntemi seçin.  **Bagging** veya **çoğaltma** seçeneklerinden birini belirleyebilirsiniz.  
  
    -   **Bagging**: Bagging de *önyükleme toplama* olarak adlandırılır. Bu yöntemde, her ağaç yeni bir örnek üzerinde büyüerek orijinal veri kümesini rastgele örnekleyerek, özgün veri kümesinin orijinal bir veri kümesine sahip olana kadar bir şekilde oluşturulur.  
  
         Modellerin çıkışları, bir toplama biçimi olan *Oylama* tarafından birleştirilir. Sınıflandırma kararı ormanındaki her ağaç, etiketlerin Normalleştirilmemiş bir sıklık histogramı çıkarır. Toplama işlemi, her etiket için "olasılıklara" almak üzere bu histogramları ve normalleştirerek toplanacak. Bu şekilde, yüksek tahmine sahip olan ağaçlar, en son karar veren kararının daha büyük bir ağırlığına sahip olacaktır.  
  
         Daha fazla bilgi için bkz. önyükleme toplama için Vikipedi girişi.  
  
    -   **Çoğaltma:** çoğaltmadaki her ağaç, tam olarak aynı giriş verilerinde eğitilir. Her ağaç düğümü için hangi bölünmüş koşulun kullanıldığını belirleme rasgele kalır ve ağaçlar birbirinden farklı olur.   
  
3.  Model **oluşturma modunu** ayarlayarak modelin eğitilme şeklini belirleyin.  
  
    -   **Tek parametre**: modeli nasıl yapılandırmak istediğinizi biliyorsanız bağımsız değişken olarak belirli bir değer kümesi sağlayabilirsiniz.

    -   **Parametre aralığı**: en iyi parametrelerden emin değilseniz, [model hiper parametrelerini ayarla](tune-model-hyperparameters.md) modülünü kullanarak en iyi parametreleri bulabilirsiniz. Birkaç değer aralığı sağlarsınız ve en iyi sonucu üreten değerlerin birleşimini öğrenmek için eğitmen ayarların birden fazla birleşimine yinelenir.
  
4.  **Karar ağaçları sayısı** için, en fazla, en fazla sayıda karar ağacının oluşturulabilecek en fazla sayısını yazın. Daha fazla karar ağacı oluşturarak daha iyi tedarik sağlayabilirsiniz, ancak eğitim süresi artar.  
  
    > [!NOTE]
    >  Değerini 1 olarak ayarlarsanız. Ancak, yalnızca bir ağaç üretilebilir (ilk parametre kümesine sahip ağaç) ve başka yineleme yapılmaz.
  
5.  **Karar ağaçlarının maksimum derinliği** için, herhangi bir karar ağacının maksimum derinliğini sınırlamak üzere bir sayı yazın. Ağacın derinliğini artırmak, bazı fazla sığdırma ve daha fazla eğitim süresi riskinde duyarlık artırabilir.
  
  
7.  **Yaprak düğüm başına minimum örnek sayısı** için, bir ağaçta herhangi bir Terminal düğümü (yaprak) oluşturmak için gereken minimum durum sayısını belirtin.
  
     Bu değeri artırarak, yeni kurallar oluşturma eşiğini artırırsınız. Örneğin, varsayılan 1 değeri ile tek bir durum bile yeni bir kuralın oluşturulmasına neden olabilir. Değeri 5 ' e artırırsanız eğitim verilerinin aynı koşulları karşılayan en az beş durum içermesi gerekir.  
  
8.  Eğitim veya doğrulama kümelerinde bilinmeyen değerler için bir grup oluşturmak üzere **kategorik özellikler için bilinmeyen değerlere Izin ver** seçeneğini belirleyin. Model, bilinen değerler için daha az kesin olabilir, ancak yeni (bilinmiyor) değerler için daha iyi tahminler sağlayabilir. 

     Bu seçeneğin işaretini kaldırırsanız model yalnızca eğitim verilerinde bulunan değerleri kabul edebilir.
  
9. Etiketli bir veri kümesi iliştirin ve modeli eğitme:

    + **Tek parametre** için bir görüntü **oluşturma modu** ayarlarsanız, etiketli bir veri kümesini ve [model eğitimi](train-model.md) modülünü bağlayın.  
  
    + **Parametre aralığına** **oluşturma** , bir etiketli veri kümesini bağlama ve modeli [Ayarla hiper parametrelerini](tune-model-hyperparameters.md)kullanarak modeli eğitme.  
  
    > [!NOTE]
    > 
    > [Modeli Eğiteetmek](train-model.md)için bir parametre aralığı geçirirseniz, tek parametre listesindeki yalnızca varsayılan değeri kullanır.  
    > 
    > Tek bir parametre değerleri kümesini [ayarlama modeli hiper parametreleri](tune-model-hyperparameters.md) modülüne geçirirseniz, her parametre için bir dizi ayar beklerken, değerleri yoksayar ve öğrenici için varsayılan değerleri kullanır.  
    > 
    > **Parametre aralığı** seçeneğini belirleyip herhangi bir parametre için tek bir değer girerseniz, belirtilen tek değer, diğer parametrelerin bir değer aralığı üzerinde değişse bile, tarama boyunca kullanılır.  
    
## <a name="results"></a>Sonuçlar

Eğitim tamamlandıktan sonra:

+ Eğitilen modelin anlık görüntüsünü kaydetmek için **model eğitimi** modülünün sağ panelindeki **çıktılar** sekmesini seçin. Modeli yeniden kullanılabilir bir modül olarak kaydetmek için **veri kümesini kaydet** simgesini seçin.

+ Puanlama için modeli kullanmak üzere, bir işlem hattına **puan modeli** modülünü ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 