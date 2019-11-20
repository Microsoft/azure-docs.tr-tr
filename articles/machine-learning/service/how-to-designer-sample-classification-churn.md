---
title: 'Tasarımcı: sınıflandırma, karmaşıklığı tahmin etme'
titleSuffix: Azure Machine Learning
description: Bu tasarımcı (Önizleme) örnek işlem hattı, müşteri ilişkileri yönetimi (CRM) için ortak bir görev olan ikili sınıflandırıcı tahminini gösterir.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 11/04/2019
ms.openlocfilehash: a09060e16b877df4412b785edbb60930cb4122bc
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647184"
---
# <a name="sample-5---classification-predict-churn"></a>Örnek 5-sınıflandırma: karmaşıklığı tahmin etme
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Tasarımcı (Önizleme) kullanarak tek bir kod satırı yazmadan karmaşık makine öğrenimi ardışık düzeni oluşturmayı öğrenin.

Bu işlem hattı, müşteri ilişkileri yönetimi (CRM) sistemleri için ortak görevleri tahmin etmek üzere 2 **iki sınıf önceden artırılmış karar ağacı** sınıflandırmasını sağlar. Veri değerleri ve Etiketler birden çok veri kaynağına ayrılır ve Anonimleştir müşteri bilgilerine göre karıştırır; ancak, tasarımcı 'yı yine de veri kümelerini birleştirmek ve örtülü değerleri kullanarak bir modeli geliştirmek için kullanabiliriz.

"Bir tane mi?" sorusunu yanıtlamaya çalıştığınız için Bu bir sınıflandırma sorunu olarak adlandırılır, ancak bu örnekte gösterilen aynı mantığı, regresyon, sınıflandırma, kümeleme gibi her türlü makine öğrenimi sorununu çözmek için de uygulayabilirsiniz.

Bu işlem hattı için tamamlanan grafik aşağıda verilmiştir:

![Ardışık düzen grafiği](./media/how-to-ui-sample-classification-predict-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Örnek 5 ' e tıklayarak açın. 

## <a name="data"></a>Veriler

Bu işlem hattının verileri, KDD Kupa 2009 ' dir. 50.000 satır ve 230 Özellik sütunları vardır. Bu görev, bu özellikleri kullanan müşteriler için dalgalanma, uygulama ve BT satışı tahmin etmeye yöneliktir. Veriler ve görev hakkında daha fazla bilgi için, bkz. [kdd Web sitesi](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="pipeline-summary"></a>Ardışık düzen Özeti

Tasarımcıda Bu örnek işlem hattı, müşteri ilişkileri yönetimi (CRM) için ortak bir görev olan değişim, uygulama ve iş satışı için ikili sınıflandırıcı tahminini gösterir.

Birincisi, bazı basit veri işlemleri.

- Ham veri kümesinde birçok eksik değer vardır. Eksik değerleri 0 ile değiştirmek için **Temizleme eksik veri** modülünü kullanın.

    ![Veri kümesini Temizleme](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- Özellikler ve karşılık gelen karmaşıklık farklı veri kümelerinde bulunur. Etiket sütunlarını Özellik sütunlarına eklemek için **sütun Ekle** modülünü kullanın. İlk sütun olan **Sütun1**, etiket sütunudur. Görselleştirme sonucundan veri kümesinin dengesiz olduğunu görebiliriz. Pozitif örneklere (+ 1) göre daha negatif (-1) örnek vardır. Daha sonra, daha sonra temsil edilen durumları artırmak için **Smote** modülünü kullanacağız.

    ![Sütun veri kümesini ekleme](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)



- Veri kümesini eğitme ve test kümelerine bölmek için **verileri Böl** modülünü kullanın.

- Daha sonra, tahmin modellerini derlemek için varsayılan parametrelerle birlikte, daha önce artırılmış karar ağacı ikili sınıflandırıcısını kullanın. Her görev için bir model oluşturun, diğer bir deyişle, her biri için bir model oluşturun ve bunları tahmin edin, etkili bir şekilde ve karmaşıklığı tahmin edin.

- Ardışık düzenin sağ bölümünde, pozitif örneklerin yüzdesini artırmak için **Smote** modülünü kullanırız. SMOTE yüzdesi, pozitif örneklere çift 100 olarak ayarlanmıştır. SMOTE modülünün [Reference0 smome modülü](../././algorithm-module-reference/SMOTE.md)ile nasıl çalıştığı hakkında daha fazla bilgi edinin.

## <a name="results"></a>Sonuçlar

Test kümesinde modelin performansını görmek için **modeli değerlendir** modülünün çıkışını görselleştirin. 

![Sonuçları değerlendirin](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 **Eşik** kaydırıcısını taşıyabilir ve ikili sınıflandırma görevi için ölçüm değişikliğini görebilirsiniz. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Tasarımcı için kullanılabilen diğer örnekleri keşfet:

- [Örnek 1-gerileme: bir otomobil fiyatını tahmin edin](how-to-designer-sample-regression-automobile-price-basic.md)
- [Örnek 2-gerileme: otomobil fiyatlandırma için algoritmaları karşılaştırın](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Örnek 3-Özellik seçimi ile sınıflandırma: gelir tahmini](how-to-designer-sample-classification-predict-income.md)
- [Örnek 4-sınıflandırma: kredi riskini tahmin etme (maliyet duyarlı)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Örnek 6-sınıflandırma: uçuş gecikmelerini tahmin etme](how-to-designer-sample-classification-flight-delay.md)
- [Örnek 7-metin sınıflandırması: Vikipedi SP 500 veri kümesi](how-to-designer-sample-text-classification.md)
