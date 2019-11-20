---
title: 'Doğrusal regresyon: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Ardışık düzende kullanmak üzere doğrusal regresyon modeli oluşturmak için Azure Machine Learning doğrusal regresyon modülünü kullanmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 688bf923c07d9417b002b7cab6e3c0a0c8d20dae
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497740"
---
# <a name="linear-regression-module"></a>Doğrusal regresyon modülü
Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

İşlem hattında kullanılmak üzere doğrusal regresyon modeli oluşturmak için bu modülü kullanın.  Doğrusal regresyon bir veya daha fazla bağımsız değişken ile sayısal bir sonuç veya bağımlı değişken arasında doğrusal bir ilişki kurmaya çalışır. 

Bu modülü, doğrusal bir regresyon yöntemi tanımlamak ve sonra etiketli bir veri kümesi kullanarak bir modeli eğitebilmeniz için kullanırsınız. Daha sonra eğitilen model, tahminleri yapmak için kullanılabilir.

## <a name="about-linear-regression"></a>Doğrusal regresyon hakkında

Doğrusal regresyon, Machine Learning 'de benimsenen yaygın istatistiksel bir yöntemdir ve satır ve ölçü hatasına yönelik birçok yeni yöntemle geliştirilmiştir. En basit anlamda, regresyon bir sayısal hedefin tahminini ifade eder. Temel bir tahmine dayalı görev için basit bir model istediğinizde doğrusal regresyon hala iyi bir seçimdir. Doğrusal regresyon, yüksek boyutlu, karmaşıklık olmayan seyrek veri kümeleri üzerinde iyi çalışma eğilimi gösterir.

Azure Machine Learning, doğrusal gerileme ek olarak çeşitli gerileme modellerini destekler. Ancak, "gerileme" terimi gevşek olarak yorumlanamaz ve diğer araçlarda sunulan bazı gerileme türleri desteklenmez.

+ Klasik gerileme sorunu, tek bir bağımsız değişken ve bağımlı bir değişken içerir. Bu *basit gerileme*olarak adlandırılır.  Bu modül basit gerileme destekler.

+ *Birden çok doğrusal gerileme* , tek bir bağımlı değişkene katkıda bulunan iki veya daha fazla bağımsız değişken içerir. Tek bir sayısal sonucu tahmin etmek için birden çok girişin kullanıldığı sorunlara çok *sayıda doğrusal regresyon*de denir.

    Bu sorunları diğer regresyon modüllerinin çoğunda olduğu gibi, **Doğrusal regresyon** modülü de çözebilir.

+ *Çoklu etiket gerileme* , tek bir modelde birden çok bağımlı değişkeni tahmin etme görevleridir. Örneğin, çok etiketli lojistik regresyonda birden çok farklı etikete bir örnek atanabilir. (Bu, tek bir sınıf değişkeni içindeki birden çok düzeyi tahmin etme görevinden farklıdır.)

    Bu regresyon türü Azure Machine Learning desteklenmez. Birden çok değişkeni tahmin etmek için, tahmin etmek istediğiniz her bir çıktı için ayrı bir öğrenici oluşturun.

İstatistikçilerin yıllar için, gerileme için giderek gelişmiş gelişmiş yöntemler geliştirmiştir. Bu, doğrusal regresyon için de geçerlidir. Bu modül, hatayı ölçmek ve gerileme satırına uyması için iki yöntemi destekler: normal en az kareler yöntemi ve gradyan tanımı.

- **Gradyan tanımı** , model eğitimi sürecinin her adımında hata miktarını en aza indiren bir yöntemdir. Gradara üzerinde birçok çeşitte ve çeşitli öğrenme sorunları için en iyi duruma getirme işlemi, kapsamlı araştırdık. **Çözüm yöntemi**için bu seçeneği belirlerseniz, adım boyutunu, öğrenme oranını ve benzerlerini denetlemek için çeşitli parametreler ayarlayabilirsiniz. Bu seçenek ayrıca tümleşik bir parametre süpürme kullanımını destekler.

- **Normal en az kareler** , doğrusal Regresyondaki en yaygın olarak kullanılan tekniklerin biridir. Örneğin, en az kare, Microsoft Excel için analiz araç takımı 'nda kullanılan yöntemidir.

    Normal en az kareler, hatayı gerçek değerden tahmin edilen satıra kadar olan uzaklık karenin toplamı olarak hesaplayan kayıp işlevini ifade eder ve kare içinde hatayı en aza indirerek modele uyar. Bu yöntem, girişler ve bağımlı değişken arasında güçlü doğrusal bir ilişki olduğunu varsayar.

## <a name="configure-linear-regression"></a>Doğrusal regresyon yapılandırma

Bu modül, farklı seçeneklerle bir gerileme modeline sığdırma için iki yöntemi destekler:

+ [Çevrimiçi degradeyi kullanarak regresyon modeli oluşturma](#bkmk_GradientDescent)

    Gradyan tanımı, daha karmaşık olan veya çok az eğitim verilerine sahip olan modeller için daha iyi bir kayıp işlevidir.



+ [Normal en az kare kullanarak regresyon modelini sığdırma](#bkmk_OrdinaryLeastSquares)

    Küçük veri kümeleri için en iyisi, normal en az kare seçer. Bu, Excel 'e benzer sonuçlar vermelidir.

## <a name="bkmk_OrdinaryLeastSquares"></a>Normal en az kare kullanarak regresyon modeli oluşturma

1. Tasarımcıdaki ardışık düzene **Doğrusal regresyon modeli** modülünü ekleyin.

    Bu modülü **Machine Learning** kategorisinde bulabilirsiniz. **Modeli Başlat**' ı genişletin, **regresyon**' i genişletin ve sonra **Doğrusal regresyon modeli** modülünü işlem hattınızla sürükleyin.

2. **Özellikler** bölmesinde, **çözüm yöntemi** açılan listesinde, **normal en az kareler**' i seçin. Bu seçenek, regresyon çizgisini bulmak için kullanılan hesaplama yöntemini belirtir.

3. **L2 düzenleme ağırlığı**' nda, L2 düzenleme için ağırlık olarak kullanılacak değeri yazın. Fazla sığdırmayı önlemek için sıfır olmayan bir değer kullanmanızı öneririz.

     Düzenleme model sığdırmayı nasıl etkilediği hakkında daha fazla bilgi edinmek için şu makaleye bakın: [L1 ve L2 düzenleme Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Kesme terimini görüntülemek istiyorsanız, **kesme terimini Ekle**seçeneğini belirleyin.

    Regresyon formülünü gözden geçirmeniz gerekmiyorsa bu seçeneğin işaretini kaldırın.

5. **Rastgele sayı**çekirdeği için, model tarafından kullanılan rastgele sayı oluşturucuyu temel alarak isteğe bağlı olarak bir değer yazabilirsiniz.

    Aynı işlem hattının farklı çalıştırmaları üzerinde aynı sonuçların bakımını yapmak istiyorsanız, çekirdek değeri kullanmak faydalıdır. Aksi takdirde, varsayılan olarak sistem saatinden bir değer kullanılır.


7. İşlem hattınızla [model eğitimi](./train-model.md) modülünü ekleyin ve etiketli bir veri kümesini bağlayın.

8. İşlem hattını çalıştırma.

## <a name="results-for-ordinary-least-squares-model"></a>Normal en az kareler modelinin sonuçları

Eğitim tamamlandıktan sonra:

+ Modelin parametrelerini görüntülemek için, eğitmen çıktısına sağ tıklayın ve **Görselleştir**' i seçin.

+ Tahmine dayalı hale getirmek için eğitilen modeli, yeni değerlerin bir veri kümesiyle birlikte [puan modeli](./score-model.md) modülüne bağlayın. 


## <a name="bkmk_GradientDescent"></a>Çevrimiçi degradeyi kullanarak regresyon modeli oluşturma

1. Tasarımcıdaki ardışık düzene **Doğrusal regresyon modeli** modülünü ekleyin.

    Bu modülü **Machine Learning** kategorisinde bulabilirsiniz. **Modeli Başlat**' ı genişletin, **regresyon**' i genişletin ve **Doğrusal regresyon modeli** modülünü ardışık düzene sürükleyin

2. **Özellikler** bölmesinde, **çözüm yöntemi** açılan listesinde, regresyon çizgisini bulmak için kullanılan hesaplama yöntemi olarak **çevrimiçi gradyan** ' ı seçin.

3. **Oluşturma modu**için, modeli önceden tanımlanmış bir parametre kümesiyle eğmek istediğinizi veya bir parametre süpürme kullanarak modeli iyileştirmek isteyip istemediğinizi belirtin.

    + **Tek parametre**: doğrusal regresyon ağını nasıl yapılandırmak istediğinizi biliyorsanız bağımsız değişken olarak belirli bir değer kümesi sağlayabilirsiniz.

   
4. **Öğrenme oranı**için, stochastik gradyan için en iyi duruma getirme için ilk öğrenme oranını belirtin.

5. **Eğitim dönemlerinde sayısı**için, algoritmanın örneklerle kaç kez yineolmayacağını belirten bir değer yazın. Az sayıda örneğe sahip veri kümelerinde bu sayı yakınsama için büyük olmalıdır.

6. **Özellikleri Normalleştir**: modeli eğleştirmek için kullanılan sayısal verileri zaten normalleştirmeniz durumunda bu seçeneğin seçimini kaldırabilirsiniz. Varsayılan olarak, modül tüm sayısal girdileri 0 ile 1 arasında bir aralığa normalleştirir.

    > [!NOTE]
    > 
    > Puanlama için kullanılan yeni verilere aynı normalleştirme yöntemini uygulamayı unutmayın.

7. **L2 düzenleme ağırlığı**' nda, L2 düzenleme için ağırlık olarak kullanılacak değeri yazın. Fazla sığdırmayı önlemek için sıfır olmayan bir değer kullanmanızı öneririz.

    Düzenleme model sığdırmayı nasıl etkilediği hakkında daha fazla bilgi edinmek için şu makaleye bakın: [L1 ve L2 düzenleme Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Öğrenme oranının yineleme ilerleme durumunu azaltmasını istiyorsanız, **öğrenme oranını azalt**seçeneğini belirleyin.  

10. **Rastgele sayı**çekirdeği için, model tarafından kullanılan rastgele sayı oluşturucuyu temel alarak isteğe bağlı olarak bir değer yazabilirsiniz. Aynı işlem hattının farklı çalıştırmaları üzerinde aynı sonuçların bakımını yapmak istiyorsanız, çekirdek değeri kullanmak faydalıdır.


12. Etiketli bir veri kümesi ve eğitim modülleriyle bir tane ekleyin.

    Bir parametre süpürme kullanmıyorsanız, [model eğitme](train-model.md) modülünü kullanın.

13. İşlem hattını çalıştırma.

## <a name="results-for-online-gradient-descent"></a>Çevrimiçi gradyan sonuçları

Eğitim tamamlandıktan sonra:

+ Tahmine dayalı hale getirmek için eğitilen modeli, yeni giriş verileriyle birlikte [puan modeli](./score-model.md) modülüne bağlayın.


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 