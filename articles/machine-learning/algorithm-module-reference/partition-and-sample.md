---
title: 'Bölüm ve örnek: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Bir veri kümesinde örnekleme gerçekleştirmek veya veri kümesinizden bölümler oluşturmak için Azure Machine Learning bölümünde Bölüm ve örnek modülün nasıl kullanılacağını öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 659dcae0bee0fe1015ba6225797c31bad438013f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497720"
---
# <a name="partition-and-sample-module"></a>Bölüm ve örnek modül

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

Bir veri kümesinde örnekleme gerçekleştirmek veya veri kümesinizden bölümler oluşturmak için bu modülü kullanın.

Örnekleme, makine öğreniminde önemli bir araçtır çünkü aynı değer oranını koruyarak bir veri kümesinin boyutunu azaltmanızı sağlar. Bu modül makine öğreniminde önemli olan birkaç ilgili görevi destekler: 

- Verilerinizi aynı boyuttaki birden çok alt tabloya bölme. 

    Bölümleri çapraz doğrulama için kullanabilir veya rastgele gruplara servis talepleri atayabilirsiniz.

- Verileri gruplara ayırma ve ardından belirli bir gruptaki verilerle çalışma. 

    Farklı gruplara rastgele bir şekilde servis talebi atadıktan sonra, yalnızca bir grupla ilişkili özellikleri değiştirmeniz gerekebilir.

- Aşağıdakine. 

    Verilerin bir yüzdesini çıkarabilir, rastgele örnekleme uygulayabilir ya da veri kümesini dengelemek için kullanılacak bir sütun seçebilir ve değerleri üzerinde kümeleştirilmiş örnekleme yapabilirsiniz.

- Test için daha küçük bir veri kümesi oluşturma. 

    Çok fazla veriniz varsa, işlem hattını ayarlarken yalnızca ilk *n* satırı kullanmak ve ardından modelinizi oluştururken tam veri kümesini kullanmaya geçiş yapmak isteyebilirsiniz. Geliştirme sırasında kullanmak için daha küçük veri kümesi oluşturmak üzere örnekleme de kullanabilirsiniz.

## <a name="configure-partition-and-sample"></a>Bölüm ve örnek yapılandırma

Bu modül, verilerinizi bölümlere bölmek veya örneklemeye yönelik birden çok yöntemi destekler. Önce yöntemi seçin ve ardından yöntemi için gereken ek seçenekleri ayarlayın.

- Baş
- Örnekleme
- Katlara ata
- Katlama Seç

### <a name="get-top-n-rows-from-a-dataset"></a>Bir veri kümesinden Ilk N satır al

Yalnızca ilk *n* satırı almak için bu modu kullanın. Bu seçenek, bir işlem hattını az sayıda satırda test etmek isterseniz ve verilerin herhangi bir şekilde dengelenmesi veya örneklenmesi gerekmiyorsa yararlıdır.

1. Arabirimde **bölüm ve örnek** modülünü ekleyin ve veri kümesini bağlayın.  

2. **Bölüm veya örnek modu**: Bu seçeneği **Head**olarak ayarlayın.

3. **Seçilecek satır sayısı**: Döndürülecek satır sayısını yazın.

    Belirttiğiniz satır sayısı negatif olmayan bir tamsayı olmalıdır. Seçilen satır sayısı veri kümesindeki satır sayısından büyükse, tüm veri kümesi döndürülür.

4. İşlem hattını çalıştırma.

Modül yalnızca belirtilen sayıda satırı içeren tek bir veri kümesi çıkarır. Satırlar her zaman veri kümesinin üstünden okunurdur.

### <a name="create-a-sample-of-data"></a>Veri örneği oluşturma

Bu seçenek, basit rastgele örnekleme veya düzensiz rastgele örneklemeyi destekler. Test için daha küçük bir temsili örnek veri kümesi oluşturmak istiyorsanız bu yararlı olur.

1. İşlem hattınızı **bölüm ve örnek** modülünü ekleyin ve veri kümesini bağlayın.

2. **Bölüm veya örnek mod**: bunu **örneklemeye**ayarlayın.

3. **Örnekleme oranı**: 0 ile 1 arasında bir değer yazın. Bu değer, kaynak veri kümesinden çıktı veri kümesine dahil edilecek satırların yüzdesini belirtir.

    Örneğin, özgün veri kümesinin yalnızca yarısını istiyorsanız örnekleme hızının %50 olması gerektiğini belirtmek için `0.5` yazın.

    Giriş veri kümesinin satırları, belirtilen oranına göre karıştırılmış şekilde, çıktı veri kümesine seçmeli olarak konur.

4. **Örnekleme Için rastgele çekirdek**: isteğe bağlı olarak, çekirdek değeri olarak kullanılacak bir tamsayı yazın.

    Satırların her seferinde aynı şekilde bölüneceğini istiyorsanız bu seçenek önemlidir. Varsayılan değer 0 ' dır, yani bir başlangıç çekirdeği sistem saatine göre oluşturulur. Bu işlem hattı her çalıştırdığınızda biraz farklı sonuçlara yol açabilir.

5. **Örnekleme için ayrılmış bölme**: veri kümesindeki satırların örnekleme öncesinde bazı anahtar sütunlara eşit olarak bölünebilmesi önemliyse bu seçeneği belirleyin.

    **Örnekleme Için Stratification anahtar sütunu**için, veri kümesini bölmek üzere tek bir *konuşmasıyla Strata sütunu* seçin. Veri kümesindeki satırlar şu şekilde bölünür:

    1. Tüm giriş satırları, belirtilen konuşmasıyla Strata sütunundaki değerler tarafından gruplandırılır (alt).

    2. Satırlar her grup içinde karıştırılır.

    3. Her grup, belirtilen oranı karşılamak için, çıktı veri kümesine seçmeli olarak eklenir.


6. İşlem hattını çalıştırma.

    Bu seçenekle modül, verilerin temsili bir örneklemeyi içeren tek bir veri kümesi çıkarır. Veri kümesinin kalan, örneksiz olmayan kısmı çıkış değil. 

## <a name="split-data-into-partitions"></a>Verileri bölümlere ayırma

Veri kümesini verilerin alt kümelerine bölmek istediğinizde bu seçeneği kullanın. Bu seçenek ayrıca, çapraz doğrulama için özel sayıda katların oluşturulması veya satırları birkaç gruba bölmek istediğinizde yararlı olur.

1. İşlem hattınızı **bölüm ve örnek** modülünü ekleyin ve veri kümesini bağlayın.

2. **Bölüm veya örnek mod**Için, **katlara ata**' yı seçin.

3. **Bölümlemede değiştirme kullan**: örneklenmiş satırın olası yeniden kullanım için satır havuzuna geri dönmesi istiyorsanız bu seçeneği belirleyin. Sonuç olarak, aynı satır birkaç katlara atanabilir.

    Değiştirme (varsayılan seçenek) kullanmıyorsanız, Örneklenmiş satır olası yeniden kullanım için satır havuzuna geri yerleştirmez. Sonuç olarak, her satır yalnızca bir katlamalı atanabilir.

4. **Rastgele bölme**: satırların katlara rastgele atanmasını istiyorsanız bu seçeneği belirleyin.

    Bu seçeneği seçmezseniz, satırlar hepsini bir kez deneme yöntemi kullanılarak katlara atanır.

5. **Rastgele çekirdek**: isteğe bağlı olarak, çekirdek değeri olarak kullanılacak bir tamsayı yazın. Satırların her seferinde aynı şekilde bölüneceğini istiyorsanız bu seçenek önemlidir. Aksi takdirde, varsayılan 0 değeri rastgele bir başlangıç temel değerinin kullanılacağı anlamına gelir.

6. **Bölümleyici yöntemini belirtin**: şu seçenekleri kullanarak verilerin her bölüme nasıl alınacağını belirtmek istediğinizi belirtin:

    - **Bölüm eşit**: her bölüme eşit sayıda satır yerleştirmek için bu seçeneği kullanın. Çıkış bölümlerinin sayısını belirtmek için, **eşit olarak bölmek için katlama sayısını belirtin** metin kutusuna bir tamsayı girin.

    - **Özelleştirilmiş oranlar Içeren bölüm**: Bu seçeneği, her bölümün boyutunu virgülle ayrılmış bir liste olarak belirtmek için kullanın.

        Örneğin, üç bölüm oluşturmak istiyorsanız, verilerin %50 ' i ve her biri verilerin %25 ' i içeren kalan iki bölüm varsa, **virgülle ayrılmış metin kutusuna göre boyut listesine** tıklayın ve şu sayıları yazın: @no_ _t_1_`.5, .25, .25`

        Tüm bölüm boyutlarının toplamı tam olarak 1 ' i içermelidir.

        - **1 ' den az**olan sayı girerseniz, kalan satırları tutmak için ek bir bölüm oluşturulur. Örneğin, .2 ve .3 değerlerini yazarsanız, tüm satırların yüzde 50 ' unu tutan üçüncü bir bölüm oluşturulur.

        - **1 ' den fazla**ekleyen sayılar girerseniz, işlem hattını çalıştırdığınızda bir hata oluşur.

7. Dikey **bölme**: satırların bölünmesi sırasında dikey olmasını istiyorsanız bu seçeneği belirleyin ve ardından _konuşmasıyla Strata sütununu_seçin.

8. İşlem hattını çalıştırma.

    Bu seçenekle modül, belirttiğiniz kuralları kullanarak bölümlenmiş birden çok veri kümesi çıkışı verir.

### <a name="use-data-from-a-predefined-partition"></a>Önceden tanımlanmış bir bölümden verileri kullanma  

Bu seçenek, bir veri kümesini birden çok bölüme ayırdığınızda ve şimdi daha fazla analiz veya işleme için her bölümü sırayla yüklemek istediğinizde kullanılır.

1. **Bölüm ve örnek** modülünü işlem hattına ekleyin.

2. Önceki **bölüm ve örnek**örneğinin çıkışına bağlayın. Bu örnek, birkaç bölüm oluşturmak için **katlara ata** seçeneğini kullanmış olmalıdır.

3. **Bölüm veya örnek modu**: **katlama**Seç ' i seçin.

4. **Hangi katlamayı örnekleneceğini belirtin**: dizinini yazarak kullanılacak bir bölüm seçin. Bölüm dizinleri 1 tabanlıdır. Örneğin, veri kümesini üç parçaya ayrılmışsa, bölümler 1, 2 ve 3 ' e sahip olur.

    Geçersiz bir dizin değeri yazarsanız, bir tasarım zamanı hatası oluşur: "hata 0018: veri kümesi geçersiz veri içeriyor."

    Veri kümesini katlara göre gruplandırmanın yanı sıra, veri kümesini iki grup halinde ayırabilirsiniz: hedef katlama ve diğer her şey. Bunu yapmak için, tek bir katın dizinini yazın ve ardından seçeneği belirleyin, her şeyi, belirtilen katdaki verileri almak için **Seçili katlamayı**seçin.

5. Birden çok bölümle çalışıyorsanız, bölüm **ve örnek** modülün her birini işlemek için ek örnekler eklemeniz gerekir.

    Örneğin, daha önce yaşı kullanarak dört katta hastaları daha önce bölümlüyordu. Her katlama ile birlikte çalışmak için **bölüm ve örnek** modülün dört kopyası gerekir ve her birinde aşağıda gösterildiği gibi farklı bir katlama seçersiniz. Doğrudan çıktıyı çıkarmak **Için ata** ' yı kullanmak doğru değildir.  

    [Bölüm ve örnek ![](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. İşlem hattını çalıştırma.

    Bu seçenekle, modül yalnızca o katlamalı satırları içeren tek bir veri kümesi çıkarır.

> [!NOTE]
>  Katlama gösterimlerini doğrudan görüntüleyemezsiniz; yalnızca meta verilerde bulunur.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 