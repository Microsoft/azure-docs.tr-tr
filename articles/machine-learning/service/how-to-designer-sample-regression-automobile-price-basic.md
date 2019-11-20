---
title: 'Tasarımcı: tahmin fiyatı (gerileme)'
titleSuffix: Azure Machine Learning
description: Tek bir kod satırı yazmadan bir otomobil fiyatını tahmin etmek için bir makine öğrenimi modeli oluşturmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 0cdf2d0b632368a5a5bc24e092783c979f7c26bc
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647162"
---
# <a name="sample-1---regression-predict-price"></a>Örnek 1-gerileme: tahmin fiyatı
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Tasarımcı (Önizleme) kullanarak tek bir kod satırı yazmadan Machine Learning regresyon modeli oluşturmayı öğrenin.

Bu işlem hattı, marka, model, horseüs ve boyut gibi teknik özelliklere bağlı olarak bir otomobil fiyatını tahmin etmek için bir **karar ormanı gerileme** sağlar. "Ne kadar?" sorusunu yanıtlamaya çalıştığınız için Bu, regresyon sorunu olarak adlandırılır. Bununla birlikte, regresyon, sınıflandırma, kümeleme gibi her türlü makine öğrenimi sorununu çözmek için bu örnekteki temel adımları uygulayabilirsiniz.

Eğitim makinesi öğrenimi modelinin temel adımları şunlardır:

1. Verileri alma
1. Verileri önceden işleme
1. Modeli eğitme
1. Modeli değerlendirme

Ardışık düzenin son, tamamlanan grafiği aşağıda verilmiştir. Bu makalede, her modülle ilgili olarak benzer kararlar verebilmeniz için tüm modüller için daha fazla şey sunulmaktadır.

![İşlem hattının grafiği](media/how-to-ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Örnek 1 ' e tıklayarak açın.


## <a name="get-the-data"></a>Verileri alma

Bu örnek, UCI Machine Learning deposundan olan **otomobil fiyat verileri (ham)** veri kümesini kullanır. Veri kümesi, marka, model, Fiyat, araç özellikleri (silindir sayısı gibi), MPG ve bir sigorta risk puanı dahil olmak üzere otomobil 'ler hakkında bilgi içeren 26 sütun içerir. Bu örneğin amacı, arabasının fiyatını tahmin etmek için kullanılır.

## <a name="pre-process-the-data"></a>Verileri önceden işleme

Ana veri hazırlama görevleri, veri temizleme, tümleştirme, dönüştürme, azaltma ve ayırma ya da değerleştirme dahil olmak üzere. Tasarımcıda, bu işlemleri gerçekleştirmek için modüller ve sol paneldeki **veri dönüştürme** grubundaki diğer veri ön işleme görevlerini bulabilirsiniz.

Birçok eksik değeri olan normalleştirilmiş zararları hariç tutmak için **veri kümesinde sütunları seçme** modülünü kullanın. Daha sonra eksik değerleri olan satırları kaldırmak için **eksik verileri temizle** seçeneğini kullanın. Bu, temiz bir eğitim verisi kümesi oluşturulmasına yardımcı olur.

![Verileri önceden işleme](./media/how-to-ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Modeli eğitme

Machine Learning sorunları farklılık gösterir. Ortak makine öğrenimi görevleri, her biri farklı bir algoritma gerektirebilecek sınıflandırma, kümeleme, gerileme ve öneren sistemleri içerir. Algoritma seçiminiz genellikle kullanım durumunun gereksinimlerine bağlıdır. Bir algoritma seçtikten sonra, daha doğru bir modeli eğitmek için parametrelerini ayarlamanız gerekir. Daha sonra doğruluk, intelligibility ve verimlilik gibi ölçümlere göre tüm modelleri değerlendirmelisiniz.

Bu örneğin hedefi, otomobil fiyatlarını tahmin etmek ve etiket sütunu (fiyat) gerçek sayılar içerdiğinden, regresyon modeli iyi bir seçimdir. Özellik sayısının nispeten küçük (100 ' den az) olduğunu ve bu özelliklerin seyrek olmadığı düşünüldüğünde, karar sınırının doğrusal olması olasıdır. Bu nedenle, bu işlem hattı için **karar ormanı gerileme** kullanıyoruz.

Eğitim veri kümesinin orijinal verilerin %70 ' i içermesi ve test veri kümesinin özgün verilerin %30 ' ü içermesi için, **verileri ayırmak Için bölünmüş veri** modülünü kullanın.

## <a name="test-evaluate-and-compare"></a>Test edin, değerlendirin ve karşılaştırın

Modeli eğitme ve test etmek için modeli geliştirmek ve test etmek için veri kümesini bölme ve farklı veri kümeleri kullanma.

Model eğitilirken, **puan modelini** kullanabilir ve tahmin edilen sonuçlar oluşturmak ve modelleri değerlendirmek için **model modüllerini değerlendirin** .

**Puan modeli** , eğitilen modeli kullanarak test veri kümesi için tahminler oluşturur. Sonucu denetlemek için, **puan modelinin** çıkış bağlantı noktasını seçin ve ardından **Görselleştir**' i seçin.

![Puan sonucu](./media/how-to-ui-sample-regression-predict-automobile-price-basic/score-result.png)

Değerlendirme ölçümleri oluşturmak için puanları, **modeli değerlendir** modülüne geçirin. Sonucu denetlemek için, **değerlendirme modelinin** çıkış bağlantı noktasını seçin ve ardından **Görselleştir**' i seçin.

![Sonucu değerlendir](./media/how-to-ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Tasarımcı için kullanılabilen diğer örnekleri keşfet:

- [Örnek 2-gerileme: otomobil fiyatlandırma için algoritmaları karşılaştırın](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Örnek 3-Özellik seçimi ile sınıflandırma: gelir tahmini](how-to-designer-sample-classification-predict-income.md)
- [Örnek 4-sınıflandırma: kredi riskini tahmin etme (maliyet duyarlı)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Örnek 5-sınıflandırma: karmaşıklığı tahmin etme](how-to-designer-sample-classification-churn.md)
- [Örnek 6-sınıflandırma: uçuş gecikmelerini tahmin etme](how-to-designer-sample-classification-flight-delay.md)
- [Örnek 7-metin sınıflandırması: Vikipedi SP 500 veri kümesi](how-to-designer-sample-text-classification.md)