---
title: 'Tasarımcı: araba fiyatlarını tahmin etme (gerileme)'
titleSuffix: Azure Machine Learning
description: Bu makalede, tasarımcı (Önizleme) kullanarak tek bir kod satırı yazmadan karmaşık makine öğrenimi işlem hattının nasıl oluşturulacağı gösterilmektedir. Bir otomobil 'nin teknik özelliklerine göre fiyatını tahmin etmek için birden çok regresyon modelini eğitme ve karşılaştırma hakkında bilgi edinin
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: d73f6d67afff13696de78d026ff65228fd68fb28
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647945"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Örnek 2-gerileme: fiyat ve karşılaştırma algoritmalarını tahmin etme
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Tasarımcı (Önizleme) kullanarak tek bir kod satırı yazmadan makine öğrenimi ardışık düzeni oluşturmayı öğrenin. Bu örnek, bir arabasının teknik özelliklerine göre fiyatını tahmin etmek için birden çok regresyon modelini karşılaştırır. Kendi makine öğrenimi sorunlarınızı ortadan kaldırmak için bu işlem hattında yapılan seçenekler için ktionale sağlayacağız.

Machine Learning 'i kullanmaya yeni başladıysanız, bu işlem hattının [temel sürümüne](how-to-designer-sample-regression-automobile-price-basic.md) göz atın.

Bu işlem hattı için tamamlanan grafik aşağıda verilmiştir:

[işlem hattının ![grafiği](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Açmak için örnek 2 ' ye tıklayın. 

## <a name="pipeline-summary"></a>Ardışık düzen Özeti

Makine öğrenimi ardışık düzeni oluşturmak için aşağıdaki adımları kullanın:

1. Verileri alın.
1. Verileri önceden işleyin.
1. Modeli eğitme.
1. Modelleri test edin, değerlendirin ve karşılaştırın.

## <a name="get-the-data"></a>Verileri alma

Bu örnek, UCI Machine Learning deposundan olan **otomobil fiyat verileri (ham)** veri kümesini kullanır. Bu veri kümesi, marka, model, Fiyat, araç özellikleri (silindir sayısı gibi), MPG ve sigorta risk puanı dahil olmak üzere otomobil 'ler hakkında bilgi içeren 26 sütun içerir.

## <a name="pre-process-the-data"></a>Verileri önceden işleme

Ana veri hazırlama görevleri, veri temizleme, tümleştirme, dönüştürme, azaltma ve ayırma ya da değerleştirme dahil olmak üzere. Tasarımcıda, bu işlemleri gerçekleştirmek için modüller ve sol paneldeki **veri dönüştürme** grubundaki diğer veri ön işleme görevlerini bulabilirsiniz.

Birçok eksik değeri olan normalleştirilmiş zararları hariç tutmak için **veri kümesinde sütunları seçme** modülünü kullanın. Daha sonra eksik değerleri olan satırları kaldırmak için **eksik verileri temizle** seçeneğini kullanıyoruz. Bu, temiz bir eğitim verisi kümesi oluşturulmasına yardımcı olur.

![Verileri önceden işleme](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Modeli eğitme

Machine Learning sorunları farklılık gösterir. Ortak makine öğrenimi görevleri, her biri farklı bir algoritma gerektirebilecek sınıflandırma, kümeleme, gerileme ve öneren sistemleri içerir. Algoritma seçiminiz genellikle kullanım durumunun gereksinimlerine bağlıdır. Bir algoritma seçtikten sonra, daha doğru bir modeli eğitmek için parametrelerini ayarlamanız gerekir. Daha sonra doğruluk, intelligibility ve verimlilik gibi ölçümlere göre tüm modelleri değerlendirmelisiniz.

Bu işlem hattının hedefi, otomobil fiyatlarını tahmin etmek ve etiket sütunu (fiyat) gerçek sayılar içerdiğinden, regresyon modeli iyi bir seçimdir. Özellik sayısının nispeten küçük (100 ' den az) olduğunu ve bu özelliklerin seyrek olmadığı düşünüldüğünde, karar sınırının doğrusal olması olasıdır.

Farklı algoritmaların performansını karşılaştırmak için, modellerden oluşan iki doğrusal algoritma kullanıyoruz, **karar ağacı gerileme** ve **karar ormanı gerilemesini**kullanırız. Her iki algoritmaların de değiştirebileceğiniz parametreleri vardır, ancak bu örnek bu işlem hattı için varsayılan değerleri kullanır.

Eğitim veri kümesinin orijinal verilerin %70 ' i içermesi ve test veri kümesinin özgün verilerin %30 ' ü içermesi için, **verileri ayırmak Için bölünmüş veri** modülünü kullanın.

## <a name="test-evaluate-and-compare-the-models"></a>Modelleri test edin, değerlendirin ve karşılaştırın

Önceki bölümde açıklandığı gibi, modeli eğitmek ve test etmek için iki farklı rastgele seçilmiş veri kümesi kullanırsınız. Modeli eğitme ve test etmek için modeli geliştirmek ve test etmek için veri kümesini bölme ve farklı veri kümeleri kullanma.

Model eğitilirken, **puan modeli** ' ni kullanın ve tahmin edilen sonuçlar oluşturmak ve modelleri değerlendirmek için **model modüllerini değerlendirin** . **Puan modeli** , eğitilen modeli kullanarak test veri kümesi için tahminler oluşturur. Ardından değerlendirme ölçümleri oluşturmak üzere **modeli değerlendirmek** için puanları geçirin.



Sonuçlar şunlardır:

![Sonuçları karşılaştırın](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Bu sonuçlar, **Artırılmış karar ağacı gerileme** ile oluşturulan modelin, **karar ormanı gerilemesi**üzerine inşa edilen modelden daha düşük bir kök ortalama kare hatası olduğunu gösterir.

Her iki algoritmadaki eğitim veri kümesinde görülmeyen test veri kümesinden daha düşük bir hata vardır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Tasarımcı için kullanılabilen diğer örnekleri keşfet:

- [Örnek 1-gerileme: bir otomobil fiyatını tahmin edin](how-to-designer-sample-regression-automobile-price-basic.md)
- [Örnek 3-Özellik seçimi ile sınıflandırma: gelir tahmini](how-to-designer-sample-classification-predict-income.md)
- [Örnek 4-sınıflandırma: kredi riskini tahmin etme (maliyet duyarlı)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Örnek 5-sınıflandırma: karmaşıklığı tahmin etme](how-to-designer-sample-classification-churn.md)
- [Örnek 6-sınıflandırma: uçuş gecikmelerini tahmin etme](how-to-designer-sample-classification-flight-delay.md)
- [Örnek 7-metin sınıflandırması: Vikipedi SP 500 veri kümesi](how-to-designer-sample-text-classification.md)
