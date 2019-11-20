---
title: 'Artırılmış karar ağacı gerileme: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Artırma kullanarak regresyon ağaçlarının bir listesini oluşturmak için Azure Machine Learning ' de, artırılmış karar ağacı gerileme modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: b04a44a82f793f39b24ffd94083ca8fd5e66cfb8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493878"
---
# <a name="boosted-decision-tree-regression-module"></a>Artırılmış karar ağacı gerileme modülü

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

Bu modülü, artırma kullanarak regresyon ağaçlarının bir listesini oluşturmak için kullanın. *Yükseltme* , her ağacın önceki ağaçlara bağlı olduğu anlamına gelir. Algoritma, önünde olan ağaçların fazlalıklarını sunarak öğrenir. Bu nedenle, bir karar ağacı ' nın artması, daha az sayıda daha az bir riske karşı doğruluğu artırmaya eğilimlidir.  
  
Bu regresyon yöntemi denetimli bir öğrenme yöntemidir ve bu nedenle *etiketli bir veri kümesi*gerektirir. Etiket sütunu sayısal değer içermelidir.  

> [!NOTE]
> Bu modülü yalnızca sayısal değişkenleri kullanan veri kümeleriyle kullanın.  

Modeli tanımladıktan sonra [eğitme modelini](./train-model.md)kullanarak eğitimi eğitin.

> [!TIP]
> Oluşturulan ağaçlar hakkında daha fazla bilgi edinmek istiyor musunuz? Model eğitilirken, [model eğitimi](./train-model.md) modülünün çıktısına sağ tıklayın ve her yinelemede oluşturulan ağacı görmek Için **Görselleştir** ' i seçin. Her bir ağaç için bölünmenize gidebilir ve her bir düğüm için kurallara bakabilirsiniz.  
  
## <a name="more-about-boosted-regression-trees"></a>Artırılmış regresyon ağaçları hakkında daha fazla bilgi  

Yükseltme, ileri doğru, rastgele ormanlar ve benzeri modellerle birlikte ensebirlikte bulunan modeller oluşturmaya yönelik birkaç klasik yöntemden biridir.  Azure Machine Learning, artırılmış karar ağaçları, MART gradyanı artırma algoritmasının verimli bir uygulamasını kullanır. Gradyan artırma, regresyon sorunları için bir makine öğrenimi tekniğidir. Her bir regresyon ağacını, her adımdaki hatayı ölçmek ve bir sonraki adımda düzeltmek için önceden tanımlanmış bir kayıp işlevi kullanarak bir adım temelinde oluşturur. Bu nedenle tahmin modeli, daha zayıf tahmin modellerinin gerçekten bir şekilde ele alınabildiği bir şeydir.  
  
Gerileme sorunlarında, artırma, bir dizi ağacı bir adım temelinde oluşturur ve sonra rastgele bir fark edici kayıp işlevi kullanarak en iyi ağacı seçer.  
  
Daha fazla bilgi için şu makalelere bakın:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Gradyan yükseltme üzerinde bu Vikipedi makalesi, daha fazla ağaç üzerinde bazı arka plan sağlar. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: RankNet 'Ten LambdaRank 'e lambda damart: genel bakış. J.C. tarafından Burges.

Gradyan artırma yöntemi, uygun bir kayıp işleviyle regresyon için azaltılarak sınıflandırma sorunları için de kullanılabilir. Sınıflandırma görevleri için artırılmış ağaçlar uygulamasıyla ilgili daha fazla bilgi için, bkz. [Iki sınıf önceden artırılmış karar ağacı](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Artırılmış karar ağacı gerilemesini yapılandırma

1.  İşlem hattınızı **artırmalı karar ağacı** modülünü ekleyin. Bu modülü, **regresyon** kategorisinin altında **Machine Learning**, **Initialize**altında bulabilirsiniz. 
  
2.  Model **oluşturma modunu** ayarlayarak modelin eğitilme şeklini belirleyin.  
  
    -   **Tek parametre**: modeli nasıl yapılandırmak istediğinizi biliyorsanız ve bağımsız değişken olarak belirli bir değer kümesi sağlamak için bu seçeneği belirleyin.  
   
  
3. **Ağaç başına en fazla yaprakları**: herhangi bir ağaçta oluşturulabilecek maksimum Terminal düğümü sayısını (yaprakları) belirtin.  

    Bu değeri artırarak ağacın boyutunu artırabilir ve daha fazla anlayışın ve daha uzun eğitim süresi riskinden daha iyi bir duyarlık elde edersiniz.  

4. **Yaprak düğüm başına minimum örnek sayısı**: bir ağaçta herhangi bir Terminal düğümü (yaprak) oluşturmak için gereken minimum durum sayısını belirtin.

    Bu değeri artırarak, yeni kurallar oluşturma eşiğini artırırsınız. Örneğin, varsayılan 1 değeri ile tek bir durum bile yeni bir kuralın oluşturulmasına neden olabilir. Değeri 5 ' e artırırsanız eğitim verilerinin aynı koşulları karşılayan en az 5 durum içermesi gerekir.

5. **Öğrenme oranı**: öğrenirken adım boyutunu tanımlayan 0 ile 1 arasında bir sayı yazın. Öğrenme oranı, öğrenimi en iyi çözüm üzerinde ne kadar hızlı veya yavaş söylebileceğinizi belirler. Adım boyutu çok büyükse en iyi çözümü fazla gerçekleştirebilirsiniz. Adım boyutu çok küçükse, eğitimin en iyi çözüm üzerinde yakınsama işlemi daha uzun sürer.

6. **Oluşturulan ağaç sayısı**: ensede oluşturmak için gereken karar ağacının toplam sayısını belirtin. Daha fazla karar ağacı oluşturarak daha iyi tedarik sağlayabilirsiniz, ancak eğitim süresi artar.

    Bu değer aynı zamanda eğitilen modeli görselleştirirken gösterilecek ağaç sayısını da denetler. tek bir ağacı görmek veya yazdırmak isterseniz, değeri 1 olarak ayarlayabilirsiniz. Ancak, yalnızca bir ağaç üretilir (ilk parametre kümesini içeren ağaç) ve başka yineleme yapılmaz.

7. **Rastgele sayı çekirdek**: rastgele çekirdek değeri olarak kullanılacak isteğe bağlı negatif olmayan bir tamsayı yazın. Bir çekirdek belirtmek, aynı verilere ve parametrelere sahip olan çalışmalarda reproducibility sağlar.

    Varsayılan olarak, rastgele çekirdek 0 olarak ayarlanır; Bu, ilk çekirdek değerin sistem saatinden elde ettiği anlamına gelir.
  
8. **Bilinmeyen kategorik düzeylerine Izin ver**: eğitim ve doğrulama kümelerinde bilinmeyen değerler için bir grup oluşturmak üzere bu seçeneği belirleyin. Bu seçeneğin işaretini kaldırırsanız model yalnızca eğitim verilerinde bulunan değerleri kabul edebilir. Model, bilinen değerler için daha az kesin olabilir, ancak yeni (bilinmiyor) değerler için daha iyi tahminler sağlayabilir.

9. Eğitim veri kümesi ve eğitim modüllerinden birini ekleyin:

    - **Tek parametreye**bir **eğitmen modu** seçeneği ayarlarsanız, [model eğitme](train-model.md) modülünü kullanın.  
  
    

10. İşlem hattını çalıştırma.  
  
## <a name="results"></a>Sonuçlar

Eğitim tamamlandıktan sonra:

+ Her yinelemede oluşturulan ağacı görmek için [eğitim modeli](train-model.md) modülünün çıktısına sağ tıklayın ve **Görselleştir**' i seçin.
  
     Her bir ağaca tıklayarak her bir düğümün kurallarını inceleyin ve her bir düğüm için kuralları görüntüleyin.  

+ Puanlama için modeli kullanmak üzere, yeni giriş örneklerine ilişkin değerleri tahmin etmek için [modeli puan](./score-model.md)alanına bağlayın.

+ Eğitilen modelin anlık görüntüsünü kaydetmek için eğitim modülünün **eğitilen model** çıktısına sağ tıklayın ve **farklı kaydet**' i seçin. Tasarruf ettiğiniz eğitilen modelin kopyası, ardışık düzenin art arda çalıştırmaları üzerinde güncellenmez.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 
