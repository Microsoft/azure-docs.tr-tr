---
title: 'Tasarımcı: sınıflandırma, gelir tahmin etme'
titleSuffix: Azure Machine Learning
description: Tasarımcı (Önizleme) kullanarak tek bir kod satırı yazmadan makine öğrenimi sınıflandırıcısını oluşturmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 383cbc11955598505730a4613c50536afac75f95
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647971"
---
# <a name="sample-3---classification-with-feature-selection-income-prediction"></a>Örnek 3-Özellik seçimi ile sınıflandırma: gelir tahmini
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Tasarımcı (Önizleme) kullanarak tek bir kod satırı yazmadan makine öğrenimi sınıflandırıcısını oluşturmayı öğrenin. Bu örnek, Yetişkin görselleştirmenizdeki geliri (> = 50K veya < = 50K) tahmin etmek için iki sınıf bir öngörme **kararı ağacı** sağlar.

Soru yanıt veriyor mu? " Bu, sınıflandırma sorunu olarak adlandırılır. Ancak, aynı temel süreci, herhangi bir makine öğrenimi sorununun, gerileme, sınıflandırma, kümeleme gibi bir tür olup olmadığını bir şekilde uygulayabilir.

Bu örnek için nihai işlem hattı grafiği aşağıda verilmiştir:

![İşlem hattının grafiği](media/how-to-ui-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Örnek 3 ' e tıklayarak açın.



## <a name="data"></a>Veriler

Veri kümesi 14 özellik ve bir etiket sütunu içerir. Sayısal ve kategorik dahil olmak üzere birden çok özellik türü vardır. Aşağıdaki diyagramda veri kümesinden bir alıntı gösterilmektedir: ![verileri](media/how-to-ui-sample-classification-predict-income/data.png)



## <a name="pipeline-summary"></a>Ardışık düzen Özeti

İşlem hattını oluşturmak için aşağıdaki adımları izleyin:

1. Yetişkin Census geliri Ikili veri kümesi modülünü ardışık düzen tuvaline sürükleyin.
1. Eğitim ve test kümelerini oluşturmak için bir **bölünmüş veri** modülü ekleyin. İlk çıkış veri kümesindeki satır kesirini 0,7 olarak ayarlayın. Bu ayar, verilerin %70 ' un, modülün sol bağlantı noktasına ve geri kalanı doğru bağlantı noktasına çıkış olacağını belirtir. Eğitim için sol veri kümesini ve test için doğru olanı kullanırız.
1. PearsonCorreclation tarafından 5 özellik seçmek için **filtre tabanlı özellik seçimi** modülünü ekleyin. 
1. Önceden maliyetli bir karar ağacı sınıflandırıcısını başlatmak için **Iki sınıf bir önceden maliyetli karar ağacı** modülü ekleyin.
1. **Eğitim modeli** modülü ekleme. Önceki adımdan gelen sınıflandırıcının, **tren modelinin**sol giriş bağlantı noktasına bağlanmasını sağlar. Filtrelenmiş veri kümesini filtre tabanlı özellik seçim modülünden eğitim veri kümesi olarak bağlayın.  **Eğitme modeli** sınıflandırıcısını eğitecektir.
1. Veri kümesini test etmek için aynı dönüştürmeyi (filtrelenmiş tabanlı özellik seçimi) uygulamak için sütun Seç dönüşümünü ve dönüştürme modülünü Uygula öğesini ekleyin.
![uygulama dönüştürme](media/how-to-ui-sample-classification-predict-income/transformation.png)
1. **Puan modeli** modülünü ekleyin ve **model eğitme** modülünü buna bağlayın. Daha sonra test kümesini (özellik seçimini test kümesine çok fazla uygulanan dönüştürme modülünün çıktısı) **puan modeline**ekleyin. **Puan modeli** tahmine göre yapılır. Tahmine dayalı ve olumlu sınıf olasılıklarına bakmak için çıkış bağlantı noktasını seçebilirsiniz.


    Bu işlem hattının iki puan modülü vardır, tahmine dayalı olmadan önce sağ taraftaki etiket sütunu hariç tutulur. Bu, Web hizmeti girişi yalnızca etiket olmayan özellikler beklediğinden gerçek zamanlı bir uç nokta dağıtımı için hazırdır. 

1. **Model değerlendirme** modülünü ekleyin ve puanlanmış veri kümesini sol giriş bağlantı noktasına bağlayın. Değerlendirme sonuçlarını görmek için **modeli değerlendir** modülünün çıkış bağlantı noktasını seçin ve **Görselleştir**' i seçin.

## <a name="results"></a>Sonuçlar

![Sonuçları değerlendirin](media/how-to-ui-sample-classification-predict-income/evaluate-result.png)

Değerlendirme sonuçlarında, ROC, duyarlık-geri çekme ve karışıklık ölçümleri gibi eğrilerin olduğunu görebilirsiniz. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Tasarımcı için kullanılabilen diğer örnekleri keşfet:

- [Örnek 1-gerileme: bir otomobil fiyatını tahmin edin](how-to-designer-sample-regression-automobile-price-basic.md)
- [Örnek 2-gerileme: otomobil fiyatlandırma için algoritmaları karşılaştırın](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Örnek 4-sınıflandırma: kredi riskini tahmin etme (maliyet duyarlı)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Örnek 5-sınıflandırma: karmaşıklığı tahmin etme](how-to-designer-sample-classification-churn.md)
- [Örnek 6-sınıflandırma: uçuş gecikmelerini tahmin etme](how-to-designer-sample-classification-flight-delay.md)
- [Örnek 7-metin sınıflandırması: Vikipedi SP 500 veri kümesi](how-to-designer-sample-text-classification.md)
