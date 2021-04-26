---
title: Tek vs-bir çoklu sınıf
titleSuffix: Azure Machine Learning
description: İkili sınıflandırma modellerinin bir örneğini kullanarak çok modüllü bir sınıflandırma modeli oluşturmak için Azure Machine Learning ' de tek vs-bir birden çok Lass modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 92ef5b372113d0d226335b8ec464a75a5a684bc3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94592915"
---
# <a name="one-vs-one-multiclass"></a>Tek vs-bir çoklu sınıf

Bu makalede Azure Machine Learning tasarımcısında tek vs-tek bir birden çok Lass modülünün nasıl kullanılacağı açıklanır. Amaç, *tek tek karşı bir* yaklaşımı kullanarak birden çok sınıfı tahmin edebilen bir sınıflandırma modeli oluşturmaktır.

Bu modül, sonucun sürekli veya kategorik tahmin değişkenlerine bağlı olması durumunda üç veya daha fazla olası sonucu tahmin eden modeller oluşturmak için faydalıdır. Bu yöntem, birden çok çıktı sınıfı gerektiren sorunlar için ikili sınıflandırma yöntemleri kullanmanızı da sağlar.

### <a name="more-about-one-versus-one-models"></a>Bire ve tek modeller hakkında daha fazla bilgi

Bazı sınıflandırma algoritmaları, tasarıma göre ikiden fazla sınıfın kullanılmasına izin verir. Diğerleri olası sonuçları iki değerden biriyle kısıtlar (ikili veya iki sınıflı bir model). Ancak çift sayıda ikili sınıflandırma algoritması, çeşitli stratejiler aracılığıyla çok sınıflı Sınıflandırma görevleri için de uyarlanabilir. 

Bu modül, bir ikili modelin sınıf çifti başına oluşturulduğu tek yönlü bir yöntemi uygular. Tahmin sırasında, en fazla oyları alan sınıf seçilidir. `n_classes * (n_classes - 1) / 2`Sınıflandırıcılarına uyması gerektiğinden, bu yöntem, O (n_classes ^ 2) karmaşıklığı nedeniyle genellikle tek başına bir ile daha yavaştır. Ancak, bu yöntem, ile birlikte ölçeklendirilmez çekirdek algoritmaları gibi algoritmalarda avantajlı olabilir `n_samples` . Bunun nedeni, her bir öğrenme sorununun yalnızca verilerin küçük bir alt kümesini (tek-ve hepsi ile) içerdiği durumlar, tam veri kümesinin de kullanıldığı `n_classes` zamandır.

Temelde, modül tek tek modellerin bir kopyasını oluşturur ve ardından sonuçları birleştirir ve tüm sınıfları tahmin eden tek bir model oluşturur. Herhangi bir ikili sınıflandırıcı, tek yönlü bir modelin temeli olarak kullanılabilir.  

Örneğin, [Iki sınıflı bir destek vektör makine](two-class-support-vector-machine.md) modeli yapılandırıp bunu tek vs-bir çok sınıf modülüne giriş olarak sağlayadığınızı varsayalım. Modül, çıkış sınıfının tüm üyeleri için iki sınıf destek vektör makine modeli oluşturur. Ardından, tüm sınıfların sonuçlarını birleştirmek için tek yönlü bir yöntemi uygular.  

Modül OneVsOneClassifier of sköğren kullanır ve [burada](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsOneClassifier.html)daha fazla bilgi edinebilirsiniz.

## <a name="how-to-configure-the-one-vs-one-multiclass-classifier"></a>Tek vs-tek birden çok sınıf sınıflandırıcısını yapılandırma  

Bu modül, birden çok sınıfı çözümlemek için ikili sınıflandırma modellerinin bir kopyasını oluşturur. Bu modülü kullanmak için önce bir *ikili sınıflandırma* modeli yapılandırıp eğmelisiniz. 

İkili modeli tek vs-bir çok Lass modülüne bağlayabilirsiniz. Daha sonra, etiketli eğitim veri kümesiyle [modeli eğitme](train-model.md) kullanarak modellerin kullanımını eğitebilirsiniz.

Modelleri birleştirdiğinizde, tek vs-bir çoklu sınıf birden çok ikili sınıflandırma modeli oluşturur, her sınıf için algoritmayı iyileştirir ve sonra modelleri birleştirir. Eğitim veri kümesi birden çok sınıf değerine sahip olsa da, modül bu görevleri yapar.

1. Tek vs-bir çok Lass modülünü tasarımcıda işlem hattınızı ekleyin. Bu modülü, **Sınıflandırma** kategorisinde **Machine Learning-Initialize** altında bulabilirsiniz.

   Tek vs-bir çok sınıf sınıflandırıcının kendisine ait yapılandırılabilir parametreleri yoktur. Tüm özelleştirmeler, giriş olarak sunulan ikili sınıflandırma modelinde yapılmalıdır.

2. Ardışık düzene bir ikili sınıflandırma modeli ekleyin ve bu modeli yapılandırın. Örneğin, [Iki sınıf destek vektör makinesi](two-class-support-vector-machine.md) veya [iki sınıf artırılmış bir karar ağacı](two-class-boosted-decision-tree.md)kullanabilirsiniz.

3. İşlem hattınızla [model eğitimi](train-model.md) modülünü ekleyin. Tek vs-bir çok Lass 'ın çıktısı olan eğitilen sınıflandırıcısını bağlayın.

4. [Eğitim modeli](train-model.md)diğer girişinde, birden fazla sınıf değeri olan etiketli bir eğitim veri kümesini bağlayın.

5. İşlem hattını gönderme.

## <a name="results"></a>Sonuçlar

Eğitim tamamlandıktan sonra, modeli kullanarak birden çok Lass tahminleri yapabilirsiniz.

Alternatif olarak, etiketli bir doğrulama veri kümesine karşı çapraz doğrulama için eğitilen sınıflandırıcının [çapraz doğrulama modeline](cross-validate-model.md) geçirebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 
