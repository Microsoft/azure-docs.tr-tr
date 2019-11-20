---
title: Pekiştirmeye dayalı öğrenme-kişiselleştirici
titleSuffix: Azure Cognitive Services
description: Kişiselleştirici, daha iyi derecelendirme önerileri sağlamak için Eylemler ve geçerli bağlam hakkındaki bilgileri kullanır. Bu eylemler ve bağlamla ilgili bilgiler, özellik olarak adlandırılan öznitelikler veya özelliklerdir.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 36071cdee25cfa99fc54b0e5c0c0aa822cb5fe2f
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662831"
---
# <a name="what-is-reinforcement-learning"></a>Pekiştirmeye dayalı Learning nedir?

Pekiştirmeye dayalı Learning, makine öğrenimine yönelik bir yaklaşım kullanarak, kullanım açısından geri bildirim alarak davranışları öğrenir.
 
Pekiştirmeye dayalı Learning şu şekilde geçerlidir:

* Kararlar veya seçimler yapma gibi bir davranışı harekete geçmek için bir fırsat veya serbestlik derecesi sağlama.
* Ortam ve seçimler hakkında bağlamsal bilgiler sağlama.
* Davranışın belirli bir amaca ne kadar iyi elde olduğu hakkında geri bildirim sağlama.

Pekiştirmeye dayalı Learning 'in birçok alt türleri ve stilleri olsa da, kavram kişiselleştirici içinde nasıl çalıştığı aşağıda gösterilmiştir:

* Uygulamanız, alternatifler listesinden bir içerik parçasını gösterme fırsatı sağlar.
* Uygulamanız, her bir alternatif ve kullanıcının bağlamı hakkında bilgi sağlar.
* Uygulamanız bir _ödül puanı_hesaplar.

Pekiştirmeye dayalı Learning 'teki bazı yaklaşımlardan farklı olarak, kişiselleştirici içinde çalışmak için bir benzetim gerektirmez. Öğrenme algoritmaları, bir dış dünyaya yanıt verecek şekilde tasarlanmıştır (denetimi buna karşı) ve her bir veri noktasından, oluşturulacak maliyet saati ve para ile ilgili benzersiz bir fırsat olduğunu ve bu da sıfır olmayan bir yazık sistemlerimiz (olası Reward kaybı) olduğunu anlamak için tasarlanmıştır. performans performansı oluşur.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Kişiselleştirici ne tür bir pekiştirmeye dayalı öğrenme algoritması kullanıyor?

Geçerli kişiselleştirici sürümü, belirli bir bağlamda ayrı eylemler arasında kararlar veya seçimler yapma etrafında çerçeveli bir pekiştirmeye dayalı öğrenimine yönelik **bağlamsal Bandits**kullanır.

_Karar belleği_, bir bağlam verildiğinde mümkün olan en iyi kararı yakalamak için eğitilen model, doğrusal modeller kümesi kullanır. Bunlar sürekli olarak çok hızlı bir şekilde gösterilen iş sonuçları ve kendini kanıtlamış bir yaklaşım, kısmen de gerçek dünyadan çok hızlı bir şekilde eğitim sağlayabildiğinden ve kısmen denetlenen öğrenme modellerini ve derin sinir ağ modelleri.

Keşfet/Exploit trafik ayırması, araştırma için ayarlanan yüzdeden sonra rastgele yapılır ve araştırma için varsayılan algoritma Epsilon-Greedy ' dir.

### <a name="history-of-contextual-bandits"></a>Bağlamsal Bandits geçmişi

John Langford, pekiştirmeye dayalı Learning 'in bir tractable alt kümesini anlatmak için bağlamsal Bandits (Langford ve Zasılı [2007]) adını kullandı ve bu paradigma hakkında öğrendiğimiz bir yarı düzine İnceleme üzerinde çalıştı:

* Beygelzimer et al. [2011]
* Dudík et al. [2011A, b]
* Agarwal et al. [2014, 2012]
* Beygelzimer ve Langford [2009]
* Li et al. [2010]

John, daha önce Birleşik tahmin (ICML 2015), bağlamsal bandıt teorisi (NıP 2013), etkin öğrenme (ICML 2009) ve örnek karmaşıklık sınırları (ICML 2003) gibi konularda daha fazla öğretici de vermiş.

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Kişiselleştirici hangi makine öğrenimi çerçeveler kullanır?

Kişiselleştirici Şu anda makine öğrenimi için temel olarak [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) kullanır. Bu çerçeve, kişiselleştirme dereceleri yaparken en yüksek aktarım hızı ve en düşük gecikme süresine izin verir ve modeli tüm etkinliklerle eğitme.

## <a name="references"></a>Referanslar

* [Düşük Teknik borç ile bağlamsal kararlar sağlama](https://arxiv.org/abs/1606.03966)
* [Dengeli bir sınıflandırmayla bir azaltmada yaklaşım](https://arxiv.org/abs/1803.02453)
* [Sabit olmayan wordl 'de verimli bağlamsal Bandits](https://arxiv.org/abs/1708.01799)
* [Kalan kayıp tahmini: Pekiştirmeye dayalı: artımlı geri bildirim olmadan öğrenme](https://openreview.net/pdf?id=HJNMYceCW)
* [Pekiştirmeye dayalı Learning ile eylemlerle yönergeler ve görsel gözlemleri eşleme](https://arxiv.org/abs/1704.08795)
* [Öğretmeninize göre daha Iyi arama hakkında bilgi edinme](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Sonraki adımlar

[Çevrimdışı değerlendirme](concepts-offline-evaluation.md) 