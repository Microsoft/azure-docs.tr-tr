---
title: Machine Learning algoritması bir sayfa Tasarımcısı
titleSuffix: Azure Machine Learning
description: Yazdırılabilir bir Machine Learning algoritması oluşturma sayfası, Azure Machine Learning tasarımcısında tahmine dayalı modeliniz için doğru algoritmayı seçmenize yardımcı olur.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
adobe-target: true
ms.openlocfilehash: 730fcf3fb552b92a02b255ad654f64d5ff7a92fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100367065"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Azure Machine Learning tasarlayıcı için Machine Learning algoritması sayfası

**Azure Machine Learning algoritması** , bir tahmine dayalı analiz modeli için doğru algoritmayı seçmenize yardımcı olur.

Azure Machine Learning, ***Classification** _, _*_öneren Systems_*_, _*_kümeleme_*_, _*_anomali algılama_*_, _*_gerileme_*_ ve _ *_metin analizi_** ailelerine ait büyük bir algoritma kitaplığına sahiptir. Her biri farklı bir makine öğrenimi sorun türünü ele almak üzere tasarlanmıştır.

Ek yönergeler için bkz. [algoritmaları seçme](how-to-select-algorithms.md)

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>İndir: Machine Learning algoritması bir sayfa

**Bu sayfayı buraya indirin: [Machine Learning algoritması bir sayfa (11x17 inç)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Machine Learning algoritması bilgi sayfası: Machine Learning algoritmasını nasıl seçebileceğinizi öğrenin.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Machine Learning algoritması oluşturup, bir algoritma seçme konusunda yardım almak için algoritması sayfasını tabloid boyutunda indirip yazdırın.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Machine Learning algoritması oluşturma sayfasını kullanma

Bu algoritma ile sunulan öneriler, yaklaşık kaydırma kurallarından oluşur. Bazıları Bükülü olabilir ve bazıları düzbir şekilde ihlal edilebilir. Bu içerik sayfası, başlangıç noktası önermek için tasarlanmıştır. Verilerinize ilişkin çeşitli algoritmalar arasında baş bir yarışma yarışmaya gerek kalmaz. Her algoritmanın ilkelerini ve verilerinizi oluşturan sistemi anlamak için bir alternatif yoktur.

Her makine öğrenimi algoritmasının kendi stili veya endüktif sapması vardır. Belirli bir sorun için, çeşitli algoritmalar uygun olabilir ve bir algoritma diğerlerine göre daha iyi olabilir. Ancak, en uygun olanı daha önce bilmemiz her zaman mümkün değildir. Bunlar gibi durumlarda, birçok algoritma bir araya getirilebilir sayfasında birlikte listelenir. Uygun bir strateji tek bir algoritma denemek ve sonuçlar henüz tatmin edici değilse, diğerlerini deneyin. 

Azure Machine Learning tasarımcısında algoritmalar hakkında daha fazla bilgi edinmek için, [algoritma ve modül başvurusuna](algorithm-module-reference/module-reference.md)gidin.

## <a name="kinds-of-machine-learning"></a>Makine öğrenimi türleri

Machine Learning 'in üç ana kategorisi vardır: *denetimli öğrenme*, denetimli *öğrenme* ve *pekiştirmeye dayalı Learning*.

### <a name="supervised-learning"></a>Denetimli öğrenme

Denetimli öğrenmede her bir veri noktası, bir kategori veya bir ilgi değeri ile etiketlidir veya ilişkilendirilir. Kategorik bir etikete bir örnek, bir görüntüyü ' Cat ' veya ' köpek ' olarak atanıyor. Değer etiketine bir örnek, kullanılan bir otomobil ile ilişkili satış fiyatıdır. Denetimli öğrenmelerin amacı bunlar gibi etiketlenmiş birçok örneği denetmaktır ve gelecekteki veri noktaları hakkında tahminler yapabilmelidir. Örneğin, yeni fotoğrafları doğru bir hayvan ile tanımlama veya diğer kullanılan otomobillere doğru satış fiyatları atama. Bu, yaygın ve yararlı bir makine öğrenimi türüdür.

### <a name="unsupervised-learning"></a>Denetimli öğrenme

Denetimli öğreniminde, veri noktalarında bunlarla ilişkili etiket yok. Bunun yerine, denetimli bir öğrenme algoritmasının hedefi, verileri bir şekilde veya yapısını betimleyen şekilde düzenmaktır. Denetimsiz öğrenme, verileri kümeler halinde gruplar. böylece, K-anlamına gelir veya daha basit görünmesi için karmaşık verilere bakmanın farklı yollarını bulur.

### <a name="reinforcement-learning"></a>Pekiştirmeye dayalı öğrenme

Pekiştirmeye dayalı Learning 'de, algoritma her bir veri noktasına cevap olarak bir eylem seçer. Bu, bir noktada bir noktadaki algılayıcı okumaların bir veri noktası olduğu ve algoritmanın, robot 'un bir sonraki eylemini seçmesi gereken, Robotics 'de yaygın bir yaklaşımdır. Ayrıca, Nesnelerin İnterneti uygulamalar için de doğal bir uyum. Öğrenme algoritması, daha sonra kararının ne kadar iyi olduğunu belirten bir ödül sinyali de alır. Bu sinyal temelinde, algoritma, en yüksek ödül elde edilmesi için stratejisini değiştirir. 

## <a name="next-steps"></a>Sonraki adımlar

* [Algoritmaları seçme](how-to-select-algorithms.md) hakkında ek kılavuza bakın

* [Azure Machine Learning ve Azure Portal Studio hakkında bilgi edinin](overview-what-is-azure-ml.md).

* [Öğretici: Azure Machine Learning tasarımcısında bir tahmin modeli oluşturun](tutorial-designer-automobile-price-train-score.md).

* [Derin öğrenme ve makine öğrenimi hakkında bilgi edinin](concept-deep-learning-vs-machine-learning.md).
