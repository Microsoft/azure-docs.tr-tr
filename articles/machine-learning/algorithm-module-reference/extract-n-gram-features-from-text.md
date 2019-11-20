---
title: Metin modül başvurusundan N-gram özelliklerini Ayıkla
titleSuffix: Azure Machine Learning
description: Metin verilerini kullanmak için Azure Machine Learning Ayıkla N-gram modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 71c1afd294b880f68849b283ea1a4b058d744801
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497844"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>Metin modül başvurusundan N-gram özelliklerini Ayıkla

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır. Yapılandırılmamış metin verilerini *korturleştirmek* için metin modülündeki N-gram özelliklerini Ayıkla ' yı kullanın. 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Metin modülünden ayıklama N-gram özelliklerinin yapılandırması

Modül, n-gram sözlüğünü kullanmak için aşağıdaki senaryoları destekler:

* Boş metin sütunundan [Yeni bir n-gram sözlüğü oluşturun](#create-a-new-n-gram-dictionary) .

* Serbest bir metin sütununu korleştirmek için [var olan bir metin özellikleri kümesini kullanın](#use-an-existing-n-gram-dictionary) .

* N-gram kullanan [bir modeli puan edin veya yayımlayın](#score-or-publish-a-model-that-uses-n-grams) .

### <a name="create-a-new-n-gram-dictionary"></a>Yeni bir n-gram sözlüğü oluştur

1.  Metin modülünden N-gram özelliklerini ayıklama işlem hattınızla ekleyin ve işlemek istediğiniz metni içeren veri kümesini bağlayın.

1.  Ayıklamak istediğiniz metni içeren dize türünde bir sütun seçmek için **metin sütununu** kullanın. Sonuçlar ayrıntılı olduğundan, tek seferde yalnızca bir sütunu işleyebilirsiniz.

1. N-gram özelliklerinin yeni bir listesini kullandığınızı göstermek için **Sözlük modunu** **Oluştur** olarak ayarlayın. 

1. **N-gram boyutunu** , Ayıklanacak ve depolanacak *en fazla* n gram boyutunu gösterecek şekilde ayarlayın. 

    Örneğin, 3, unigram, bigram ve trigram girerseniz.

1. **Ağırlık işlevi** , belge özelliği vektörünün nasıl oluşturulacağını ve belgelerden nasıl sözlük ayıklanacağını belirtir.

    * **Ikili ağırlık**: ayıklanan n-gram 'a bir ikili bir varlık değeri atar. Her n-gram değeri, belgede mevcutsa 1, aksi durumda 0 ' dır.

    * **Tf Weight**: ayıklanan n-gram için bir dönem SıKLıĞı (TF) puanı atar. Her n-gram değeri, belgedeki oluşma sıklıktır.

    * **IDF ağırlığı**: ayıklanan n-gram 'a bir ters belge sıklığı (IDF) puanı atar. Her n-gram değeri, yapı boyutunun, tüm yapı içindeki oluşma sıklığıyla ayrılmış olan günlük değeridir.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **Tf-IDF Weight**: ayıklanan n-gram için bir dönem sıklığı/ters belge SıKLıĞı (TF/IDF) puanı atar. Her n-gram değeri, kendi ıDF puanı ile çarpılarak TF Score.

1. **En küçük sözcük uzunluğu** ' nu n-gram içindeki herhangi bir *sözcüğe* kullanılabilecek minimum harf sayısına ayarlayın.

1. N-gram içindeki herhangi bir *sözcüğe* kullanılabilecek maksimum harf sayısını ayarlamak için **Maksimum sözcük uzunluğunu** kullanın.

    Varsayılan olarak, sözcük veya belirteç başına en fazla 25 karaktere izin verilir.

1. N-gram sözlüğünde herhangi bir n-gram için gereken en düşük oluşumu ayarlamak için **en az n-gram belge mutlak sıklığını** kullanın. 

    Örneğin, varsayılan değer olan 5 ' i kullanırsanız, n-gram ' n i n-gram sözlüğüne dahil etmemizi sağlamak için herhangi bir n-gram, yapı içinde en az beş kez görünmelidir. 

1.  **En fazla n-gram belge oranını** , genel Corpus içindeki satır sayısına göre belirli bir n-gram içeren satır sayısının maksimum oranına ayarlayın.

    Örneğin, 1 oranı, her satırda belirli bir n-gram mevcut olsa bile, n-gram sözlüğüne n-gram eklenebilir. Genellikle, her satırda oluşan bir sözcük bir gürültü sözcüğü olarak değerlendirilir ve kaldırılır. Etki alanına bağımlı gürültü sözcüklerini filtrelemek için bu oranı azaltmayı deneyin.

    > [!IMPORTANT]
    > Belirli sözcüklerin oluşma oranı tek biçimli değildir. Belgeden belgeye farklılık gösterir. Örneğin, belirli bir ürünle ilgili müşteri açıklamalarını analiz ediyorsanız, ürün adı çok yüksek sıklığa sahip olabilir ve bir gürültü kelimesiyle kapatılabilir, ancak diğer bağlamlarda önemli bir terim olabilir.

1. Özellik vektörlerini normalleştirmek için **n-gram Özellik vektörlerini normalleştirin** seçeneğini belirleyin. Bu seçenek etkinleştirilirse, her n gram Özellik vektörü L2 norm ile bölünür.

1. İşlem hattını çalıştırma.

### <a name="use-an-existing-n-gram-dictionary"></a>Var olan n-gram sözlüğünü kullanma

1.  Metin modülünden N-gram özelliklerini ayıklama işlem hattınızı ekleyin ve işlemek istediğiniz metin olan veri kümesini **veri kümesi** bağlantı noktasına bağlayın.

1.  Metin **sütununu** kullanarak, istediğiniz metni içeren metin sütununu seçin. Varsayılan olarak, modül **dize**türünde tüm sütunları seçer. En iyi sonuçlar için, tek seferde tek bir sütun işleyin.

1. Daha önce oluşturulmuş bir n-gram sözlüğü içeren kaydedilmiş veri kümesini ekleyin ve **giriş sözlüğü** bağlantı noktasına bağlayın. Ayrıca, metin modülünden ayıklama N-gram özelliklerinin bir yukarı akış örneğinin **sonuç sözlük** çıkışını bağlayabilirsiniz.

1. **Sözlük modu**için, açılan listeden **ReadOnly** güncelleştirme seçeneğini belirleyin.

   **ReadOnly** seçeneği giriş sözlüğü için yapı girişini temsil eder. Yeni metin veri kümesinden (sol girişte) terim sıklıklarını hesaplama yerine, giriş sözlüğsdakiler n-gram ağırlıkları olduğu gibi uygulanır.

   > [!TIP]
   > Bir metin sınıflandırıcısını Puanlama yaparken bu seçeneği kullanın.

1.  Diğer tüm seçenekler için [önceki bölümde](#create-a-new-n-gram-dictionary)bulunan özellik açıklamalarına bakın.

1.  İşlem hattını çalıştırma.

### <a name="score-or-publish-a-model-that-uses-n-grams"></a>N gram kullanan bir modeli puan veya yayınlama

1.  Metin modülünden **ayıklama N-gram özelliklerini** , eğitim veri akışından Puanlama veri akışına kopyalayın.

1.  Eğitim veri akışındaki **sonuç sözlük** çıkışını, Puanlama veri akışındaki **giriş** sözlüğüne bağlayın.

1.  Puanlama iş akışında, N-gram özelliklerini metin modülünden ayıklayın ve **Sözlük modu** parametresini **ReadOnly**olarak ayarlayın. Diğer tümünü aynı bırakın.

1.  İşlem hattını yayımlamak için **sonuç sözlüğünü** bir veri kümesi olarak kaydedin.

1.  Kaydedilen veri kümesini, Puanlama grafiğinizde metin modülünden Ayıkla N-gram özelliklerine bağlayın.

## <a name="results"></a>Sonuçlar

Metin modülündeki N-gram özelliklerini Ayıkla iki tür çıktı oluşturur: 

* **Sonuç veri kümesi**: Bu çıktı, ayıklanan n-gram ile birleştirilmiş çözümlenen metnin bir özetidir. **Metin sütunu** seçeneğinde seçmediğiniz sütunlar çıkışa geçirilir. Analiz ettiğiniz her metin sütunu için modül şu sütunları oluşturur:

  * **N-gram oluşma matrisi**: modül, Toplam yapı içinde bulunan her n-gram için bir sütun oluşturur ve bu satır için n-gram kalınlığını göstermek için her sütuna bir puan ekler. 

* **Sonuç sözlüğü**: Sözlük, çözümlemenin bir parçası olarak oluşturulan terim sıklığı puanlarının yanı sıra gerçek n-gram sözlüğünü içerir. Veri kümesini, farklı bir giriş kümesiyle veya daha sonraki bir güncelleştirme için yeniden kullanım için kaydedebilirsiniz. Ayrıca, modelleme ve Puanlama için sözlüğü de kullanabilirsiniz.

### <a name="result-vocabulary"></a>Sonuç sözlüğü

Sözlük, çözümlemenin bir parçası olarak oluşturulan terim sıklığı puanlarını içeren n-gram sözlüğünü içerir. DF ve ıDF puanları, diğer seçeneklere bakılmaksızın oluşturulur.

+ **ID**: her benzersiz n-gram için oluşturulan bir tanımlayıcı.
+ **Ngram**: n-gram. Boşluk veya diğer sözcük ayırıcıları alt çizgi karakteriyle değiştirilmiştir.
+ **Df**: özgün Corpus içindeki n-gram için sıklık puanı skoru.
+ **IDF**: özgün Corpus içindeki n-gram için ters belge sıklığı puanı.

Bu veri kümesini el ile güncelleştirebilirsiniz, ancak hatalar ortaya çıkabilir. Örneğin:

* Modül, giriş sözlüğü 'nde aynı anahtarla yinelenen satırlar bulursa bir hata oluşur. Sözlük içindeki iki satırın aynı sözcüğe sahip olmadığından emin olun.
* Sözlük veri kümelerinin giriş şemasının, sütun adları ve sütun türleri dahil olmak üzere tam olarak eşleşmesi gerekir. 
* **ID** sütununun ve **df** sütununun tamsayı türünde olması gerekir. 
* **IDF** sütunu float türünde olmalıdır.

> [!Note]
> Veri çıktısını doğrudan eğitme modeli modülüne bağlama. Eğitim modeline gönderilmeden önce serbest metin sütunlarını kaldırmalısınız. Aksi takdirde, serbest metin sütunları kategorik özellikler olarak kabul edilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın.
