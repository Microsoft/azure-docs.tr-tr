---
title: 'Eksik verileri temizle: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Eksik değerleri kaldırmak, değiştirmek veya çıkarsmak için Azure Machine Learning eksik verileri temizleme modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: fef8cb0c29eac4b26b93abee54faa4fe1078678d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493814"
---
# <a name="clean-missing-data-module"></a>Eksik veri modülünü temizle

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

Eksik değerleri kaldırmak, değiştirmek veya çıkarsmak için bu modülü kullanın. 

Veri bilimcileri genellikle eksik değerler için verileri denetler ve verileri onarmak veya yeni değerler eklemek için çeşitli işlemler gerçekleştirir. Bu tür Temizleme işlemlerinin hedefi, bir modeli eğitme sırasında ortaya çıkabilecek eksik verilerden kaynaklanan sorunları önlemektir. 

Bu modül, "temizlik" için birden çok işlem türünü destekler, örneğin:

+ Eksik değerleri bir yer tutucu, ortalama veya başka bir değerle değiştirme
+ Eksik değerleri olan satırlar ve sütunlar tamamen kaldırılıyor
+ İstatistiksel yöntemlere göre değerlerin miktarı


Bu modülün kullanılması, kaynak veri kümenizi değiştirmez. Bunun yerine, çalışma alanınızda sonraki iş akışında kullanabileceğiniz yeni bir veri kümesi oluşturur. Yeni, temizlenen veri kümesini yeniden kullanım için de kaydedebilirsiniz.

Bu modül aynı zamanda eksik değerleri temizlemek için kullanılan dönüşümün tanımını da verir. Bu dönüştürmeyi, [dönüştürme modülünü Uygula](./apply-transformation.md) ' yı kullanarak aynı şemaya sahip diğer veri kümelerinde yeniden kullanabilirsiniz.  

## <a name="how-to-use-clean-missing-data"></a>Eksik verileri temizleme kullanımı

Bu modül bir temizleme işlemi tanımlamanıza olanak sağlar. Temizleme işlemini, daha sonra yeni verilere uygulayabilmeniz için de kaydedebilirsiniz. Temizleme işlemi oluşturma ve kaydetme hakkında açıklama için aşağıdaki bağlantılara bakın: 
 
+ Eksik değerleri değiştirmek için
  
+ Yeni verilere Temizleme dönüştürmesi uygulamak için
 
> [!IMPORTANT]
> Eksik değerleri işlemek için kullandığınız temizleme yöntemi, sonuçlarınızı önemli ölçüde etkileyebilir. Farklı yöntemlerle denemeler yapmanızı öneririz. Belirli bir yöntemin kullanımı için gerekçe ve sonuçların kalitesini göz önünde bulundurun.

### <a name="replace-missing-values"></a>Eksik değerleri Değiştir  

Bir veri kümesine [Temizleme eksik veri](./clean-missing-data.md) modülünü her uyguladığınızda, seçtiğiniz tüm sütunlara aynı Temizleme işlemi uygulanır. Bu nedenle farklı yöntemleri kullanarak farklı sütunları temizlemeniz gerekiyorsa, modülün ayrı örneklerini kullanın.

1.  İşlem hattınızı [Temizleme eksik veri](./clean-missing-data.md) modülünü ekleyin ve eksik değerleri olan veri kümesini bağlayın.  
  
2.  **Temizlenecek sütunlarda**, değiştirmek istediğiniz eksik değerleri içeren sütunları seçin. Birden çok sütun seçebilirsiniz, ancak tüm seçili sütunlarda aynı değiştirme yöntemini kullanmanız gerekir. Bu nedenle, genellikle dize sütunlarını ve sayısal sütunları ayrı olarak temizlemeniz gerekir.

    Örneğin, tüm sayısal sütunlardaki eksik değerleri denetlemek için:

    1. Sütun seçiciyi açın ve **kurallarla**öğesini seçin.
    2. **Başlangıç**Için **sütun yok**' u seçin.

        Ayrıca, tüm SÜTUNLARLA başlayıp sütunları hariç tutabilir. İlk olarak, **tüm sütunlara**tıkladığınızda kurallar gösterilmez, ancak **sütun yok** ' a tıklayabilir ve **ardından tüm sütunlar** ' a tıklayabilir ve ardından sütunları ad, veri türü veya sütunlar dizinine göre filtreleyebilirsiniz (hariç tut).

    3. **İçerme**için, açılan listeden **sütun türü** ' nü seçin ve ardından **sayısal**veya daha belirli bir sayısal tür seçin. 
  
    Seçtiğiniz Temizleme veya değiştirme yöntemi, seçimdeki **Tüm** sütunlara uygulanabilir olmalıdır. Herhangi bir sütundaki veriler belirtilen işlemle uyumsuzsa, modül bir hata döndürür ve işlem hattını sonlandırır.
  
3.  **Minimum eksik değer oranı**için, işlemin gerçekleştirilmesi için gereken eksik değer sayısı alt sınırını belirtin.  
  
    Bu seçeneği, veri kümesinde bir temizleme işleminin gerçekleştirildiği koşulları tanımlamak için **Maksimum eksik değer oranıyla** birlikte kullanır. Eksik değer olan çok sayıda veya çok az satır varsa, işlem gerçekleştirilemez. 
  
    Girdiğiniz sayı, eksik değerlerin sütundaki tüm değerlere **oranını** temsil eder. Varsayılan olarak, **eksik değer oranı** özelliği 0 olarak ayarlanır. Bu, eksik değerlerin yalnızca bir eksik değer olsa bile Temizlenebileceği anlamına gelir. 

    > [!WARNING]
    > Belirtilen işlemin uygulanabilmesi için bu koşulun her bir sütun ve her sütun tarafından karşılanması gerekir. Örneğin, üç sütun seçtiğinizi ve eksik değerlerin minimum oranını .2 (%20) olarak ayarlayadığınızı varsayalım, ancak yalnızca bir sütunda gerçekten %20 eksik değer vardır. Bu durumda, temizleme işlemi yalnızca %20 eksik değeri olan sütun için geçerlidir. Bu nedenle, diğer sütunlar değiştirilmez.
    > 
    > Eksik değerlerin değiştirilip değiştirilmediğini şüpheliyse, **eksik değer göstergesi sütununu oluştur**seçeneğini belirleyin. Her sütunun en düşük ve en büyük aralıklar için belirtilen ölçütlere uygun olup olmadığını belirtmek için veri kümesine bir sütun eklenir.  
  
4. **Eksik değer oranı üst sınırı**için, işlemin gerçekleştirilmesi için mevcut olabilecek maksimum değer sayısını belirtin.   
  
    Örneğin, eksik değer değiştirme işlemini yalnızca %30 veya daha az satır eksik değerler içeriyorsa, ancak satırların %30 ' ından fazla değeri eksik ise-olduğu gibi bırakmak isteyebilirsiniz.  
  
    Sayıyı, sütundaki tüm değerlere eksik değerlerin oranı olarak tanımlarsınız. Varsayılan olarak, **eksik değer oranının üst sınırı** 1 olarak ayarlanır. Bu, sütundaki değerlerin %100 ' nin eksik olması durumunda bile eksik değerlerin Temizlenebileceği anlamına gelir.  
  
   
  
5. **Temizleme modu**için eksik değerleri değiştirmek veya kaldırmak için aşağıdaki seçeneklerden birini belirleyin:  
  
  
    + **Özel değiştirme değeri**: tüm eksik değerler için geçerli olan bir yer tutucu değeri (0 veya yok gibi) belirtmek için bu seçeneği kullanın. Değiştirme olarak belirttiğiniz değer, sütunun veri türüyle uyumlu olmalıdır.
  
    + Şu **anlama Ile değiştirin**: sütun ortalaması hesaplar ve sütundaki eksik her bir değer için değiştirme değeri olarak ortalaması kullanır.  
  
        Yalnızca Integer, Double veya Boolean veri türlerine sahip sütunlar için geçerlidir.  
  
    + **Ortanca Ile Değiştir**: ortanca değerini sütunu hesaplar ve sütundaki eksik değer için değiştirme olarak ortanca değeri kullanır.  
  
        Yalnızca Integer veya Double veri türlerine sahip sütunlar için geçerlidir. 
  
    + **Mode Ile Değiştir**: sütunun modunu hesaplar ve sütunda eksik olan her bir değer için değiştirme değeri olarak modunu kullanır.  
  
        Integer, Double, Boolean veya kategorik veri türlerine sahip sütunlar için geçerlidir. 
  
    + **Tüm satırı Kaldır**: bir veya daha fazla eksik değeri olan veri kümesindeki herhangi bir satırı tamamen kaldırır. Bu, eksik değerin rastgele eksik olarak kabul edileceği durumlarda faydalıdır.  
  
    + **Tüm sütunu Kaldır**: veri kümesindeki bir veya daha fazla eksik değeri olan herhangi bir sütunu tamamen kaldırır.  
  
    
  
6. Seçenek **değiştirme değeri** , **özel değiştirme değeri**seçeneğini belirlediyseniz kullanılabilir. Sütundaki tüm eksik değerler için değiştirme değeri olarak kullanılacak yeni bir değer yazın.  
  
    Bu seçeneği yalnızca Integer, Double, Boolean veya date veri türlerine sahip sütunlarda kullanabileceğinizi unutmayın. Tarih sütunları için, değiştirme değeri 1/1/0001 12:00 ' den beri 100-nanosaniyelik Ticks sayısı olarak da girilebilir  
  
7. **Eksik değer göstergesi sütunu oluştur**: sütundaki değerlerin eksik değer Temizleme ölçütlerini karşılaıp karşılanmadığını belirten bir bildirim almak istiyorsanız bu seçeneği belirleyin. Bu seçenek özellikle yeni bir temizleme işlemi ayarlarken ve tasarlandığı gibi çalıştığından emin olmak istediğinizde yararlıdır.
  
8. İşlem hattını çalıştırma.

### <a name="results"></a>Sonuçlar

Modül iki çıkış döndürüyor:  

-   **Temizlenen veri kümesi**: Bu seçeneği belirlediyseniz, bir gösterge sütunuyla birlikte, belirtilen sütunlardan oluşan eksik değerlerle birlikte seçili sütunlardan oluşan bir veri kümesi.  

    Temizleme için seçilmemiş sütunlar da "geçirilir".  
  
-  **Temizleme dönüştürmesi**: çalışma alanınıza kaydedilebilecek ve yeni verilere daha sonra uygulanabilen temizlik için kullanılan bir veri dönüştürmesi.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Yeni verilere kaydedilmiş bir temizleme işlemi uygulama  

Temizleme işlemlerini sıklıkla tekrarlamanız gerekiyorsa, aynı veri kümesiyle yeniden kullanmak için, bir *dönüştürme*olarak veri temizleme tariflerini kaydetmenizi öneririz. Temizleme dönüşümünü kaydetme, özellikle de aynı şemaya sahip verileri daha sonra yeniden içe aktarmanız ve temizlemeniz gerekiyorsa yararlıdır.  
      
1.  İşlem hattınızda [dönüştürme modülünü Uygula](./apply-transformation.md) ' yı ekleyin.  
  
2.  Temizlemek istediğiniz veri kümesini ekleyin ve veri kümesini sağ taraftaki giriş bağlantı noktasına bağlayın.  
  
3.  Tasarımcının sol bölmesindeki **dönüşümler** grubunu genişletin. Kaydedilen dönüştürmeyi bulun ve ardışık düzene sürükleyin.  

4.  Kaydedilen dönüşümü, [dönüşüm Uygula](./apply-transformation.md)'nın sol giriş bağlantı noktasına bağlayın. 

    Kaydedilen bir dönüşüm uyguladığınızda, dönüşümün uygulandığı sütunları seçemezsiniz. Bunun nedeni, dönüştürmenin zaten tanımlandığından ve özgün işlemde belirtilen sütunlara otomatik olarak uygulananlarıdır.

    Ancak, sayısal sütunların bir alt kümesinde bir dönüşüm oluşturduğunuzu varsayalım. Eksik değerler yalnızca eşleşen sayısal sütunlarda değiştiğinden, bu dönüşümü bir hata oluşturmadan karışık sütun türleri veri kümesine uygulayabilirsiniz.

6.  İşlem hattını çalıştırma.  

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 