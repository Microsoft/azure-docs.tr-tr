---
title: 'Doğrusal regresyon: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Ardışık düzende kullanmak üzere doğrusal regresyon modeli oluşturmak için Azure Machine Learning doğrusal regresyon modülünü kullanmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 809b1be4f9f12e1963ff5caeaacd109c84db154f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93323663"
---
# <a name="linear-regression-module"></a>Doğrusal regresyon modülü
Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

İşlem hattında kullanılmak üzere doğrusal regresyon modeli oluşturmak için bu modülü kullanın.  Doğrusal regresyon bir veya daha fazla bağımsız değişken ile sayısal bir sonuç veya bağımlı değişken arasında doğrusal bir ilişki kurmaya çalışır. 

Bu modülü, doğrusal bir regresyon yöntemi tanımlamak ve sonra etiketli bir veri kümesi kullanarak bir modeli eğitebilmeniz için kullanırsınız. Daha sonra eğitilen model, tahminleri yapmak için kullanılabilir.

## <a name="about-linear-regression"></a>Doğrusal regresyon hakkında

Doğrusal regresyon, Machine Learning 'de benimsenen yaygın istatistiksel bir yöntemdir ve satır ve ölçü hatasına yönelik birçok yeni yöntemle geliştirilmiştir. Yalnızca put, regresyon bir sayısal hedefin tahminini ifade eder. Temel bir tahmine dayalı görev için basit bir model istediğinizde doğrusal regresyon hala iyi bir seçimdir. Doğrusal regresyon, yüksek boyutlu, karmaşıklık olmayan seyrek veri kümeleri üzerinde iyi çalışma eğilimi gösterir.

Azure Machine Learning, doğrusal gerileme ek olarak çeşitli gerileme modellerini destekler. Ancak, "gerileme" terimi gevşek olarak yorumlanamaz ve diğer araçlarda sunulan bazı gerileme türleri desteklenmez.

+ Klasik gerileme sorunu, tek bir bağımsız değişken ve bağımlı bir değişken içerir. Bu *basit gerileme* olarak adlandırılır.  Bu modül basit gerileme destekler.

+ *Birden çok doğrusal gerileme* , tek bir bağımlı değişkene katkıda bulunan iki veya daha fazla bağımsız değişken içerir. Tek bir sayısal sonucu tahmin etmek için birden çok girişin kullanıldığı sorunlara çok *sayıda doğrusal regresyon* de denir.

    Bu sorunları diğer regresyon modüllerinin çoğunda olduğu gibi, **Doğrusal regresyon** modülü de çözebilir.

+ *Çoklu etiket gerileme* , tek bir modelde birden çok bağımlı değişkeni tahmin etme görevleridir. Örneğin, çok etiketli lojistik regresyonda birden çok farklı etikete bir örnek atanabilir. (Bu, tek bir sınıf değişkeni içindeki birden çok düzeyi tahmin etme görevinden farklıdır.)

    Bu regresyon türü Azure Machine Learning desteklenmez. Birden çok değişkeni tahmin etmek için, tahmin etmek istediğiniz her bir çıktı için ayrı bir öğrenici oluşturun.

İstatistikçilerin yıllar için, gerileme için giderek gelişmiş gelişmiş yöntemler geliştirmiştir. Bu, doğrusal regresyon için de geçerlidir. Bu modül, hatayı ölçmek ve gerileme satırına uyması için iki yöntemi destekler: normal en az kareler yöntemi ve gradyan tanımı.

- **Gradyan tanımı** , model eğitimi sürecinin her adımında hata miktarını en aza indiren bir yöntemdir. Gradara üzerinde birçok çeşitte ve çeşitli öğrenme sorunları için en iyi duruma getirme işlemi, kapsamlı araştırdık. **Çözüm yöntemi** için bu seçeneği belirlerseniz, adım boyutunu, öğrenme oranını ve benzerlerini denetlemek için çeşitli parametreler ayarlayabilirsiniz. Bu seçenek ayrıca tümleşik bir parametre süpürme kullanımını destekler.

- **Normal en az kareler** , doğrusal Regresyondaki en yaygın olarak kullanılan tekniklerin biridir. Örneğin, en az kare, Microsoft Excel için analiz araç takımı 'nda kullanılan yöntemidir.

    Normal en az kareler, hatayı gerçek değerden tahmin edilen satıra kadar olan uzaklık karenin toplamı olarak hesaplayan kayıp işlevini ifade eder ve kare içinde hatayı en aza indirerek modele uyar. Bu yöntem, girişler ve bağımlı değişken arasında güçlü doğrusal bir ilişki olduğunu varsayar.

## <a name="configure-linear-regression"></a>Doğrusal regresyon yapılandırma

Bu modül, farklı seçeneklerle bir gerileme modeline sığdırma için iki yöntemi destekler:

+ [Normal en az kare kullanarak regresyon modelini sığdırma](#create-a-regression-model-using-ordinary-least-squares)

    Küçük veri kümeleri için en iyisi, normal en az kare seçer. Bu, Excel 'e benzer sonuçlar vermelidir.
    
+ [Çevrimiçi degradeyi kullanarak regresyon modeli oluşturma](#create-a-regression-model-using-online-gradient-descent)

    Gradyan tanımı, daha karmaşık olan veya çok az eğitim verilerine sahip olan modeller için daha iyi bir kayıp işlevidir.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Normal en az kare kullanarak regresyon modeli oluşturma

1. Tasarımcıdaki ardışık düzene **Doğrusal regresyon modeli** modülünü ekleyin.

    Bu modülü **Machine Learning** kategorisinde bulabilirsiniz. **Modeli Başlat**' ı genişletin, **regresyon**' i genişletin ve sonra **Doğrusal regresyon modeli** modülünü işlem hattınızla sürükleyin.

2. **Özellikler** bölmesinde, **çözüm yöntemi** açılan listesinde, **normal en az kareler**' i seçin. Bu seçenek, regresyon çizgisini bulmak için kullanılan hesaplama yöntemini belirtir.

3. **L2 düzenleme ağırlığı**' nda, L2 düzenleme için ağırlık olarak kullanılacak değeri yazın. Fazla sığdırmayı önlemek için sıfır olmayan bir değer kullanmanızı öneririz.

     Düzenleme model sığdırmayı nasıl etkilediği hakkında daha fazla bilgi edinmek için şu makaleye bakın: [L1 ve L2 düzenleme Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)

4. Kesme terimini görüntülemek istiyorsanız, **kesme terimini Ekle** seçeneğini belirleyin.

    Regresyon formülünü gözden geçirmeniz gerekmiyorsa bu seçeneğin işaretini kaldırın.

5. **Rastgele sayı** çekirdeği için, model tarafından kullanılan rastgele sayı oluşturucuyu temel alarak isteğe bağlı olarak bir değer yazabilirsiniz.

    Aynı işlem hattının farklı çalıştırmaları üzerinde aynı sonuçların bakımını yapmak istiyorsanız, çekirdek değeri kullanmak faydalıdır. Aksi takdirde, varsayılan olarak sistem saatinden bir değer kullanılır.


7. İşlem hattınızla [model eğitimi](./train-model.md) modülünü ekleyin ve etiketli bir veri kümesini bağlayın.

8. İşlem hattını gönderme.

### <a name="results-for-ordinary-least-squares-model"></a>Normal en az kareler modelinin sonuçları

Eğitim tamamlandıktan sonra:


+ Tahmine dayalı hale getirmek için eğitilen modeli, yeni değerlerin bir veri kümesiyle birlikte [puan modeli](./score-model.md) modülüne bağlayın. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Çevrimiçi degradeyi kullanarak regresyon modeli oluşturma

1. Tasarımcıdaki ardışık düzene **Doğrusal regresyon modeli** modülünü ekleyin.

    Bu modülü **Machine Learning** kategorisinde bulabilirsiniz. **Modeli Başlat**' ı genişletin, **regresyon**' i genişletin ve **Doğrusal regresyon modeli** modülünü ardışık düzene sürükleyin

2. **Özellikler** bölmesinde, **çözüm yöntemi** açılan listesinde, regresyon çizgisini bulmak için kullanılan hesaplama yöntemi olarak **çevrimiçi gradyan** ' ı seçin.

3. **Oluşturma modu** için, modeli önceden tanımlanmış bir parametre kümesiyle eğmek istediğinizi veya bir parametre süpürme kullanarak modeli iyileştirmek isteyip istemediğinizi belirtin.

    + **Tek parametre**: doğrusal regresyon ağını nasıl yapılandırmak istediğinizi biliyorsanız bağımsız değişken olarak belirli bir değer kümesi sağlayabilirsiniz.
    
    + **Parametre aralığı**: en iyi parametrelerden emin değilseniz ve bir parametre süpürme çalıştırmak istiyorsanız bu seçeneği belirleyin. Yinelemek için bir değer aralığı seçin ve [ayarlama modeli hiper parametreleri](tune-model-hyperparameters.md) , en iyi sonuçları üreten hiper parametreleri belirlemek için, belirttiğiniz ayarların tüm olası birleşimlerinin üzerinde yinelenir.  

   
4. **Öğrenme oranı** için, stochastik gradyan için en iyi duruma getirme için ilk öğrenme oranını belirtin.

5. **Eğitim dönemlerinde sayısı** için, algoritmanın örneklerle kaç kez yineolmayacağını belirten bir değer yazın. Az sayıda örneğe sahip veri kümelerinde bu sayı yakınsama için büyük olmalıdır.

6. **Özellikleri Normalleştir**: modeli eğleştirmek için kullanılan sayısal verileri zaten normalleştirmeniz durumunda bu seçeneğin seçimini kaldırabilirsiniz. Varsayılan olarak, modül tüm sayısal girdileri 0 ile 1 arasında bir aralığa normalleştirir.

    > [!NOTE]
    > 
    > Puanlama için kullanılan yeni verilere aynı normalleştirme yöntemini uygulamayı unutmayın.

7. **L2 düzenleme ağırlığı**' nda, L2 düzenleme için ağırlık olarak kullanılacak değeri yazın. Fazla sığdırmayı önlemek için sıfır olmayan bir değer kullanmanızı öneririz.

    Düzenleme model sığdırmayı nasıl etkilediği hakkında daha fazla bilgi edinmek için şu makaleye bakın: [L1 ve L2 düzenleme Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)


9. Öğrenme oranının yineleme ilerleme durumunu azaltmasını istiyorsanız, **öğrenme oranını azalt** seçeneğini belirleyin.  

10. **Rastgele sayı** çekirdeği için, model tarafından kullanılan rastgele sayı oluşturucuyu temel alarak isteğe bağlı olarak bir değer yazabilirsiniz. Aynı işlem hattının farklı çalıştırmaları üzerinde aynı sonuçların bakımını yapmak istiyorsanız, çekirdek değeri kullanmak faydalıdır.


12. Modeli eğitme:

    + **Tek parametre** için bir görüntü **oluşturma modu** ayarlarsanız, etiketli bir veri kümesini ve [model eğitimi](train-model.md) modülünü bağlayın.  
  
    + **Parametre aralığına** **oluşturma** , bir etiketli veri kümesini bağlama ve modeli [Ayarla hiper parametrelerini](tune-model-hyperparameters.md)kullanarak modeli eğitme.  
  
    > [!NOTE]
    > 
    > [Modeli Eğiteetmek](train-model.md)için bir parametre aralığı geçirirseniz, tek parametre listesindeki yalnızca varsayılan değeri kullanır.  
    > 
    > Tek bir parametre değerleri kümesini [ayarlama modeli hiper parametreleri](tune-model-hyperparameters.md) modülüne geçirirseniz, her parametre için bir dizi ayar beklerken, değerleri yoksayar ve öğrenici için varsayılan değerleri kullanır.  
    > 
    > **Parametre aralığı** seçeneğini belirleyip herhangi bir parametre için tek bir değer girerseniz, belirtilen tek değer, diğer parametrelerin bir değer aralığı üzerinde değişse bile, tarama boyunca kullanılır.

13. İşlem hattını gönderme.

### <a name="results-for-online-gradient-descent"></a>Çevrimiçi gradyan sonuçları

Eğitim tamamlandıktan sonra:

+ Tahmine dayalı hale getirmek için eğitilen modeli, yeni giriş verileriyle birlikte [puan modeli](./score-model.md) modülüne bağlayın.


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın.