---
title: 'Verileri depo gözlerine gruplandırma: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Numaraları gruplandırmak veya sürekli verilerin dağıtımını değiştirmek için verileri depo gözleri ' nde nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/13/2020
ms.openlocfilehash: 392cb9b4c2ded1b98b79ce8dcd780ac59e96b78a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91998488"
---
# <a name="group-data-into-bins-module"></a>Verileri depo gözleri modülüne gruplandırma

Bu makalede, sayıları gruplamak veya sürekli verilerin dağıtımını değiştirmek için Azure Machine Learning tasarımcısında grup verileri yerleştirme modülünün nasıl kullanılacağı açıklanır.

Verileri depo gözleri halinde gruplandırma modülü, verileri binme için birden çok seçeneği destekler. Bölme kenarlarının nasıl ayarlanacağını ve değerlerin depo gözlerine nasıl alınacağını özelleştirebilirsiniz. Örneğin, şunları yapabilirsiniz:  

+ Küme sınırları olarak kullanılacak bir değer serisini el ile yazın.  
+ *Quantiles* veya yüzdelik dereceleri kullanarak depo gözlerine değerler atayın.  
+ Değerlerin eşit bir şekilde dağıtılmasını zorlamak için zorlayın.  

## <a name="more-about-binning-and-grouping"></a>Atma ve gruplandırma hakkında daha fazla bilgi

Veri *hazırlama veya gruplama* (bazen de bir değer elde *edilir) Machine* Learning için sayısal veri hazırlama konusunda önemli bir araçtır. Bunlar gibi senaryolarda faydalıdır:

+ Sürekli sayıların bir sütununda, modellenebilmeniz için çok sayıda benzersiz değer vardır. Bu nedenle, değerleri otomatik olarak veya el ile gruplar, daha küçük bir ayrık aralıklar kümesi oluşturacak şekilde gruplara atar.

+ Bir sayı sütununu belirli aralıkları temsil eden kategorik değerlerle değiştirmek istiyorsunuz.

    Örneğin, Kullanıcı demograflarındaki 1-15, 16-22, 23-30 gibi özel aralıklar belirterek bir yaş sütunundaki değerleri gruplandırmak isteyebilirsiniz.

+ Bir veri kümesi, beklenen aralığın dışında kalan çok sayıda değere sahiptir ve bu değerler eğitilen modelde bir üst düzeyde etkisi vardır. Modeldeki farkı azaltmak için, quantiles metodunu kullanarak verileri Tekdüzen dağıtımına dönüştürebilirsiniz.

    Bu yöntemde, verileri depo gözleri olarak Gruplandır modülü, her bir sepete yaklaşık olarak aynı sayıda örnek bulunduğundan emin olmak için ideal bölme konumları ve bölme genişlikleri belirler. Ardından, seçtiğiniz normalleştirme yöntemine bağlı olarak, depo gözlerinde yer alan değerler yüzdebirlik değeri olarak dönüştürülür veya bir bin numarası ile eşleştirilir.

### <a name="examples-of-binning"></a>Bina örnekleri

Aşağıdaki diyagramda, *quantiles* yöntemiyle bir önce ve sonra sayısal değerlerin dağılımı gösterilmektedir. Solda ham verilerle karşılaştırıldığında verilerin oluşturulmuş ve birim normal ölçeğe dönüştürüldüğünü fark edersiniz.  

> [!div class="mx-imgBorder"]
> ![Sonuç görselleştirme](media/module/group-data-into-bins-result-example.png)

Verileri gruplamak için pek çok yol olduğundan, tüm özelleştirilebilir, farklı yöntemler ve değerlerle denemeler yapmanızı öneririz. 

## <a name="how-to-configure-group-data-into-bins"></a>Grup verilerini depo gözlerine yapılandırma

1. Tasarımcı 'daki işlem hattınızı **Grup verilerini depo gözleri** modülüne ekleyin. Bu modülü kategori **veri dönüşümünde** bulabilirsiniz.

2. Sayısal verileri olan veri kümesini bin 'e bağlayın. Quantidileştirme yalnızca sayısal veri içeren sütunlara uygulanabilir. 

    Veri kümesi sayısal olmayan sütunlar içeriyorsa, birlikte çalışacak sütunların bir alt kümesini seçmek için [veri kümesinde sütunları seç](select-columns-in-dataset.md) modülünü kullanın.

3. Binme modunu belirtin. Binme modu diğer parametreleri belirler, bu nedenle ilk olarak **binme modu** seçeneğini seçtiğinizden emin olun. Aşağıdaki tür bir atma desteklenir:

    - **Quantiles**: quantile yöntemi, yüzdelik derecelendirmeler temelinde depo gözlerine değerler atar. Bu yöntem, eşit yükseklik binme olarak da bilinir.

    - **Eşit genişlik**: Bu seçenekle, toplam bölme sayısını belirtmeniz gerekir. Veri sütunundaki değerler, her bir bin başlangıç ve bitiş değerleri arasında aynı aralığa sahip olduğu zaman gözlerine yerleştirilir. Sonuç olarak, veriler belirli bir nokta etrafında kümelendirilebilir bazı depo gözleri daha fazla değere sahip olabilir.

    - **Özel kenarlar**: her bir bin başlangıç değerlerini belirtebilirsiniz. Kenar değeri her zaman bin alt sınırıdır. 
    
      Örneğin, değerleri iki bölme halinde gruplandırmak istediğinizi varsayın. Biri 0 ' dan büyük değerlere sahip olur ve biri 0 ' dan küçük veya buna eşit değerlere sahip olur. Bu durumda, bin kenarları için, **virgülle ayrılmış bölme kenarları listesine** **0** girersiniz. Modülün çıktısı 1 ve 2 olacaktır, her satır değeri için bin dizinini gösterir. Virgülle ayrılmış değer listesinin, 1, 3, 5, 7 gibi artan bir düzende olması gerektiğini unutmayın.
    
    > [!Note]
    > *ENTROPI MDL* modu, Studio 'da (klasik) tanımlanır ve henüz tasarımcıda desteklemek için yararlanılabilir olabilecek karşılık gelen açık kaynak paketi yoktur.        

4. **Quantiles** ve **eşittir genişlik** atma modlarını kullanıyorsanız, kaç tane depo *gözü veya ölçü* oluşturmak istediğinizi belirtmek için **bölme sayısı** seçeneğini kullanın.

5. **Sütunlarda** yer alan sütunları seçmek için, sütun seçiciyi kullanarak, depo gözüne istediğiniz değerlere sahip sütunları seçin. Sütunlar sayısal bir veri türü olmalıdır.

    Aynı binkenme kuralı, seçtiğiniz tüm ilgili sütunlara uygulanır. Bazı sütunları farklı bir yöntem kullanarak ayarlamanız gerekiyorsa, her sütun kümesi için grup verilerinin farklı bir örneğini bin modülüne kullanın.

    > [!WARNING]
    > İzin verilen bir tür olmayan bir sütun seçerseniz, bir çalışma zamanı hatası oluşturulur. Modül, izin verilmeyen bir türdeki herhangi bir sütunu bulur oluşturmaz bir hata döndürür. Bir hata alırsanız, tüm seçili sütunları gözden geçirin. Hata, tüm geçersiz sütunları listelemez.

6. **Çıkış modu** için, quantideðerlendirilmiş değerleri nasıl çıktısını almak istediğinizi belirtin:

    + **Append**: bulunan değerleri içeren yeni bir sütun oluşturur ve bunu giriş tablosuna ekler.

    + **InPlace**: özgün değerleri veri kümesindeki yeni değerlerle değiştirir.

    + **Resultonly**: yalnızca sonuç sütunlarını döndürür.

7. **Quantiles** 'in modunu seçerseniz, değerlerin, quantiles 'e sıralamadan önce normalleştirilme şeklini belirlemek Için, **quantile normalleştirme** seçeneğini kullanın. Değerlerin normalleştirilmesi, değerlerin dönüştürdüğüne ancak son bölme sayısını etkilemediğini unutmayın.

    Aşağıdaki normalleştirme türleri desteklenir:

    + **Yüzde**: değerler [0100] aralığı içinde normalleştirilmelidir.

    + **Pquantile**: değerler [0, 1] aralığı içinde normalleştirilir.

    + **Quantitaındex**: değerler [1, bölme sayısı] aralığında normalleştirilir.

8. **Özel kenarlar** seçeneğini belirlerseniz, **bin kenarları metin kutusunun virgülle ayrılmış listesinde** *küme kenarları* olarak kullanılacak sayıların virgülle ayrılmış bir listesini girin. 

    Değerler, depo gözlerini ayıran noktayı işaret. Örneğin, bir bin kenar değeri girerseniz, iki bölme oluşturulur. İki bin kenar değeri girerseniz, üç bölme oluşturulur.

    Değerler, depo gözlerinin oluşturulduğu sırada ve en düşüğe doğru sıralanmalıdır.

10. Quantidiklen sütunların kategorik değişkenler olarak işleneceğini belirtmek için **etiketleri kategorik seçeneği olarak** seçin.

11. İşlem hattını gönderme.

## <a name="results"></a>Sonuçlar

Gruplandırma verileri, her bir öğenin belirtilen moda göre oluşturulduğu bir veri kümesini döndürür. 

Ayrıca, bir *binme dönüştürmesi* de döndürür. Bu işlev, aynı binme modu ve parametreleri kullanılarak, yeni veri örneklerini bölmeye dönüştürmek için [dönüştürme modülünü Uygula](apply-transformation.md) ' ya geçirilebilir.  

> [!TIP]
> Eğitim verilerinizde bir binüyi kullanıyorsanız, test ve tahmin için kullandığınız verilerde aynı binkenme yöntemini kullanmanız gerekir. Aynı bölme konumlarını ve bölme genişliklerini de kullanmalısınız. 
> 
> Verilerin her zaman aynı binme yöntemi kullanılarak dönüştürüldüğünden emin olmak için faydalı veri dönüştürmelerini kaydetmenizi öneririz. Ardından [dönüştürme modülünü Uygula](apply-transformation.md) ' yı kullanarak bunları diğer veri kümelerine uygulayın.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 
