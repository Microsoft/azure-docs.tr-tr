---
title: Tasarımcı&#58; sınıflandırma defteri İncelemeleri
titleSuffix: Azure Machine Learning
description: Makine öğrenimi modeli oluşturma İnceleme, farklı kategoriler halinde kitap İncelemeleri.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 949ddc847a6011d460f2a3685008d12e64868767
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647119"
---
# <a name="sample-7---text-classification-predict-company-category"></a>Örnek 7-metin sınıflandırması: Şirket kategorisini tahmin etme 

Bu örnek, Azure Machine Learning tasarımcısında metin sınıflandırma işlem hattı oluşturmak için metin analizi modüllerinin nasıl kullanılacağını gösterir (Önizleme).

Metin sınıflandırmasının amacı, bir veya daha fazla önceden tanımlanmış sınıfa veya kategoriye bir metin parçası atamaktır. Metin parçası bir belge, Haberler makalesi, arama sorgusu, e-posta, tweet, destek bileti, müşteri geri bildirimi, Kullanıcı ürün incelemesi vb. olabilir. Metin sınıflandırmasının uygulamaları, gazete makalelerini ve haber kullanımı içeriğini konu başlıkları halinde kategorilere ayırır, Web sayfalarını hiyerarşik kategoriler halinde düzenler, istenmeyen e-posta ve yaklaşım analizini filtreleyerek Kullanıcı amacını arama sorgularından tahmin eder, yönlendirme Bilet destekler ve müşteri geri bildirimini analiz edin. 

Bu işlem hattı, vikül 'den türetilen Vil. SP 500 veri kümesiyle şirket kategorisini tahmin etmek için çok **Lass lojistik regresyon sınıflandırıcısını** ister.  

Eğitim makinesi öğrenme modelinin metin verileri ile temel adımları şunlardır:

1. Verileri alma

1. Metin verilerini önceden işleme

1. Özellik Mühendisliği

   Metin özelliğini, özellik karması, metin verilerinden n. gram özelliğini ayıklama gibi özellik ayıklama modülü ile sayısal özelliğe dönüştürür.

1. Modeli eğitme

1. Puanlama veri kümesi

1. Modeli değerlendirme

İşte çalışdığımız işlem hattının son, tamamlanmış grafiği aşağıdadır. Kendi kendinize benzer kararlar verebilmeniz için tüm modüller için daha fazla şey sağlayacağız.

[işlem hattının ![grafiği](./media/how-to-ui-sample-text-classification/nlp-modules-overall.png)](./media/how-to-ui-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>Veriler

Bu işlem hattında, **Vikipedi SP 500** veri kümesini kullanacağız. Veri kümesi, her S & P 500 şirketinin makalelerini temel alan Viküm 'den (https://www.wikipedia.org/) türetilir. Azure Machine Learning tasarımcısına yüklemeden önce, veri kümesi şu şekilde işlenir:

- Belirli bir şirkete ait metin içeriğini Ayıkla
- Wiki biçimlendirmesini kaldır
- Alfasayısal olmayan karakterleri kaldır
- Tüm metni küçük harfe Dönüştür
- Bilinen şirket kategorileri eklendi

Makaleler, bazı şirketler için bulunamadı, bu nedenle kayıt sayısı 500 ' den küçük.

## <a name="pre-process-the-text-data"></a>Metin verilerini önceden işleme

Metin verilerini önceden işlemek için, cümlelere, simgeleştirme cümlelerini ve benzerlerini Algıla dahil olmak üzere, **önceden Işlem metin** modülünü kullanıyoruz. [**Işlem öncesi metin**](../algorithm-module-reference/preprocess-text.md) makalesinde desteklenen tüm seçenekleri buldum. Veri verileri önceden işlendikten sonra, eğitim veri kümesinin özgün verilerin %50 ' i içermesi ve test veri kümesinin özgün verilerin %50 ' i içermesi için, verileri ayrılmış olarak bölmek için **bölünmüş veri** modülünü kullanırız.

## <a name="feature-engineering"></a>Özellik Mühendisliği
Bu örnekte, özellik Mühendisliği gerçekleştiren iki yöntem kullanacağız.

### <a name="feature-hashing"></a>Özellik Karma
Makalelerin düz metnini tamsayılara dönüştürmek ve model için giriş özellikleri olarak tamsayı değerlerini kullandığımızda [**özellik karma**](../algorithm-module-reference/feature-hashing.md) modülünü kullandık. 

**Özellik karma** modülü, Vowpal Wabbit kitaplığı tarafından sunulan 32 bitlik murmurhash v3 yöntemi kullanılarak değişken uzunluklu metin belgelerinin eşit uzunlukta sayısal Özellik vektörlerine dönüştürülmesi için kullanılabilir. Özellik karmalığını kullanmanın amacı boyutlılık azalmasıyla aynıdır; Ayrıca özellik karması özelliği, dize karşılaştırması yerine karma değer karşılaştırması kullandığından, özellik ağırlıklarını Sınıflandırma sırasında daha hızlı bir şekilde kolaylaştırır.

Örnek işlem hattında, karma bitlerin sayısını 14 olarak ayarlar ve n-gram sayısını 2 olarak ayarlar. Bu ayarlarla, karma tablo 2 ^ 14 girişi, her karma özelliğin bir veya daha fazla n-gram özelliğini temsil ettiğini ve değeri, metin örneğindeki n-gram öğesinin oluşma sıklığını temsil eder. Birçok sorun için, bu boyutun karma tablosu yeterli değildir, ancak bazı durumlarda çarpışmalardan kaçınmak için daha fazla alan gerekebilir. Machine Learning Çözümünüzün performansını farklı sayıda bit kullanarak değerlendirin. 

### <a name="extract-n-gram-feature-from-text"></a>Metinden N-gram özelliğini Ayıkla

N-gram, belirli bir metin dizisinden n terimlerin ardışık sırasıdır. Bir n-gram Boyut 1, unigram olarak adlandırılır; 2 boyutlu bir n-gram bir bigram; bir n-gram Boyut 3, bir trigramı. N-daha büyük boyutlarda, bazen n değeri, örneğin "dört-gram", "beş-gram" vb. olarak adlandırılır.

Özellik Mühendisliği için başka bir çözüm olarak [**metin modülünden N-gram özelliğini ayıklama**](../algorithm-module-reference/extract-n-gram-features-from-text.md)kullandık. Bu modül ilk olarak n-gram kümesini ayıklar, n-gram sayısına ek olarak her n-gram metinde göründüğü belge sayısı sayılır (DF). Bu örnekte, özellik değerlerini hesaplamak için TF-ıDF ölçümü kullanılır. Daha sonra, yapılandırılmamış metin verilerini her bir özelliğin bir metin örneğindeki n-gram öğesinin TF-ıDF ' i temsil ettiği eşit uzunlukta sayısal Özellik vektörlerine dönüştürür.

Metin verilerini sayısal Özellik vektörlerine dönüştürdükten sonra, veri kümesinden metin verilerini kaldırmak için bir **Select Column** modülü kullanılır. 

## <a name="train-the-model"></a>Modeli eğitme

Algoritma seçiminiz genellikle kullanım durumunun gereksinimlerine bağlıdır. Bu işlem hattının hedefi şirket kategorisini tahmin ettiğinden, çok sınıf bir sınıflandırıcı modeli iyi bir seçimdir. Özellik sayısının büyük olduğunu ve bu özelliklerin seyrek olduğunu düşünürken, bu işlem hattı için **birden çok Lass lojistik regresyon** modeli kullanıyoruz.

## <a name="test-evaluate-and-compare"></a>Test edin, değerlendirin ve karşılaştırın

 Modeli eğitme ve test etmek için modeli eğitmek ve test etmek üzere farklı veri kümeleri kullandık.

Model eğitilirken, **puan modelini** kullanacağız ve tahmin edilen sonuçlar oluşturmak ve modelleri değerlendirmek için **model modüllerini değerlendirin** . Ancak, **model puanı** modülünü kullanmadan önce eğitim sırasında yaptığımız gibi özellik Mühendisliği gerçekleştirmek gerekir. 

**Özellik karma** modülü için, eğitim akışı olarak Puanlama akışında Özellik mühendisi yapmak kolaydır. Giriş metin verilerini işlemek için doğrudan **özellik karma** modülünü kullanın.

**Metin modülünden N-gram özelliğini ayıklamak** için, eğitim veri akışından **sonuç sözlük çıkışını** Puanlama veri akışındaki **giriş** sözlüğüne bağlayacağız ve **Sözlük modu** parametresini ReadOnly olarak ayarlayacağız.
[n-gram puanı ![grafiği](./media/how-to-ui-sample-text-classification/n-gram.png)](./media/how-to-ui-sample-text-classification/n-gram.png)

Mühendislik adımını tamamladıktan sonra, eğitilen model kullanılarak test veri kümesi için tahminleri oluşturmak amacıyla **puan modeli** kullanılabilir. Sonucu denetlemek için, **puan modelinin** çıkış bağlantı noktasını seçin ve ardından **Görselleştir**' i seçin.

Daha sonra değerlendirme ölçümleri oluşturmak için puanları **modeli değerlendir** modülüne geçiririz. Farklı yöntemlerle oluşturulan puanlanmış veri kümelerini değerlendirebilmemiz ve karşılaştırabilmemiz için **modeli değerlendir** iki giriş bağlantı noktasına sahiptir. Bu örnekte, özellik karma yöntemi ve n-gram yöntemiyle oluşturulan sonucun performansını karşılaştırıyoruz.
Sonucu denetlemek için, **değerlendirme modelinin** çıkış bağlantı noktasını seçin ve ardından **Görselleştir**' i seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Tasarımcı için kullanılabilen diğer örnekleri keşfet:
- [Örnek 1-gerileme: bir otomobil fiyatını tahmin edin](how-to-designer-sample-regression-automobile-price-basic.md)
- [Örnek 2-gerileme: otomobil fiyatlandırma için algoritmaları karşılaştırın](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Örnek 3-Özellik seçimi ile sınıflandırma: gelir tahmini](how-to-designer-sample-classification-predict-income.md)
- [Örnek 4-sınıflandırma: kredi riskini tahmin etme (maliyet duyarlı)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Örnek 5-sınıflandırma: karmaşıklığı tahmin etme](how-to-designer-sample-classification-churn.md)
- [Örnek 6-sınıflandırma: uçuş gecikmelerini tahmin etme](how-to-designer-sample-classification-flight-delay.md)
