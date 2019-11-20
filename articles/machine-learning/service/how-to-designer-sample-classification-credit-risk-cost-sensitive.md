---
title: 'Tasarımcı: kredi riskini tahmin etme (maliyet duyarlı)'
titleSuffix: Azure Machine Learning
description: Bu makalede tasarımcı (Önizleme) kullanarak karmaşık makine öğrenimi işlem hattı oluşturma gösterilmektedir. Özel Python betikleri uygulamayı ve en iyi seçeneği belirlemek için birden çok modeli karşılaştırmayı öğreneceksiniz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 7af0ee31c7d7e5dae4a38db7f6c74ff3e5f964bb
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647982"
---
# <a name="sample-4---classification-with-custom-python-script-predict-credit-risk"></a>Örnek 4-özel Python betiği ile sınıflandırma: kredi riskini tahmin etme
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Bu makalede tasarımcı (Önizleme) kullanarak karmaşık makine öğrenimi işlem hattı oluşturma gösterilmektedir. Python betikleri kullanarak özel mantık uygulamayı ve en iyi seçeneği belirlemek için birden çok modeli karşılaştırmayı öğreneceksiniz.

Bu örnek kredi geçmişi, yaş ve kredi kartı sayısı gibi kredi uygulama bilgilerini kullanarak kredi riskini tahmin etmek için bir sınıflandırıcının kullanımını sağlar. Ancak, bu makaledeki kavramları uygulayarak kendi makine öğrenimi sorunlarınızı ortadan kaldırabilirsiniz.

Bu işlem hattı için tamamlanan grafik aşağıda verilmiştir:

[işlem hattının ![grafiği](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Örnek 4 ' e tıklayarak açın.

## <a name="data"></a>Veriler

Bu örnek, UC Irvine deposundaki Alman kredi kartı veri kümesini kullanır. 20 özellik ve bir etiket ile 1.000 örnek içerir. Her örnek bir kişiyi temsil eder. 20 Özellik sayısal ve kategorik özellikler içerir. Veri kümesi hakkında daha fazla bilgi için, bkz. [UCI Web sitesi](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29). Son sütun, kredi riskini belirten ve yalnızca iki olası değere sahip olan etikettir: yüksek kredi riski = 2 ve düşük kredi riski = 1.

## <a name="pipeline-summary"></a>Ardışık düzen Özeti

Bu işlem hattında, bu sorunu çözmek için modeller oluşturmak üzere iki farklı yaklaşım karşılaştırırsınız:

- Özgün veri kümesiyle eğitim.
- Çoğaltılan bir veri kümesiyle eğitim.

Her iki yaklaşımdan, sonuçların maliyet işleviyle hizalandığından emin olmak için, çoğaltma ile test veri kümesini kullanarak modelleri değerlendirmelisiniz. Her iki yaklaşımdan iki sınıflandırıcıyı test edin: **Iki sınıf destek vektör makinesi** ve **Iki sınıf artırılmış karar ağacı**.

Düşük riskli bir örneği yüksek olarak sınıflandırın maliyeti 1 ' dir ve yüksek riskli bir örneği düşük olarak sınıflandırın maliyeti 5 ' tir. Bu hatalı sınıflandırma maliyetini hesaba eklemek için bir **Python betik modülünü yürütme** kullanıyoruz.

İşlem hattının grafiği aşağıdadır:

[işlem hattının ![grafiği](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Veri işleme

Varsayılan sütun adlarını, UCı sitesindeki veri kümesi açıklamasından elde edilen daha anlamlı adlarla değiştirecek şekilde sütun adları eklemek için **meta veri Düzenleyicisi** modülünü kullanarak başlayın. Yeni sütun adlarını, **meta veri düzenleyicisinin** **Yeni sütun** adı alanında virgülle ayrılmış değerler olarak sağlayın.

Ardından, risk tahmin modelini geliştirmek için kullanılan eğitimi ve test kümelerini oluşturun. Özgün veri kümesini, **bölünmüş veri** modülünü kullanarak aynı boyuttaki eğitim ve test kümelerine ayırın. Eşit boyut kümesi oluşturmak için, **ilk çıkış veri kümesi seçeneğinde satır kesirini** 0,7 olarak ayarlayın.

### <a name="generate-the-new-dataset"></a>Yeni veri kümesini oluştur

Risk tahmini maliyeti yüksek olduğundan, yanlış sınıflandırma maliyetini şöyle ayarlayın:

- Yüksek riskli durumlar için düşük riskli olarak yanlış sınıflandırıldı: 5
- Düşük riskli durumlar için yüksek riskli olarak yanlış sınıflandırılmış: 1

Bu maliyet işlevini yansıtmak için yeni bir veri kümesi oluşturun. Yeni veri kümesinde, her bir yüksek riskli örnek beş kez çoğaltılır, ancak düşük riskli örneklerin sayısı değişmez. Aynı satırın her iki durumda da olmasını engellemek için verileri, çoğaltmadan önce eğitime ve test veri kümelerine ayırın.

Yüksek riskli verileri çoğaltmak için, bu python kodunu bir **Python betik** modülüne koyun:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

**Execute Python betik** modülü hem eğitim hem de test veri kümelerini çoğaltır.

### <a name="feature-engineering"></a>Özellik mühendisliği

**Iki sınıflı destek vektör makinesi** algoritması, normalleştirilmiş veriler gerektirir. Bu nedenle, tüm sayısal özelliklerin aralıklarını bir `tanh` dönüşümle normalleştirmek için **Normalleştir** modülünü kullanın. `tanh` dönüştürme, tüm sayısal özellikleri 0 ve 1 aralığı içindeki değerlere dönüştürür, ancak değerlerin genel dağıtımını korur.

**Iki sınıflı destek vektör makinesi** modülü, dize özelliklerini, bunları kategorik özelliklere dönüştürerek ve sonra sıfır veya bir değeri olan ikili özelliklere dönüştüren şekilde işler. Bu nedenle, bu özellikleri normalleştirmeniz gerekmez.

## <a name="models"></a>Modeller

İki **sınıflı, Iki sınıf destek vektör makinesi** (SVM) ve **iki sınıflı bir karar ağacı**ve iki veri kümesi uyguladığınız için, toplam dört model oluşturursunuz:

- SVM özgün verilerle eğitildi.
- SVM çoğaltılan verilerle eğitildi.
- Özgün verilerle eğitilen karar ağacı.
- Çoğaltılan verilerle eğitilen karar ağacı.

Bu örnek, modelleri oluşturmak, eğitme ve test etmek için standart veri bilimi iş akışını kullanır:

1. **Iki sınıf destek vektör makinesi** ve **Iki sınıf artırılmış karar ağacının**kullanıldığı öğrenme algoritmalarını başlatın.
1. Algoritmayı veriye uygulamak ve gerçek modeli oluşturmak için **eğitme modeli** kullanın.
1. Test örneklerini kullanarak puan oluşturmak için **puan modeli** kullanın.

Aşağıdaki diyagramda, bu işlem hattının, orijinal ve çoğaltılan eğitim kümelerinin iki farklı SVM modelini eğitmek için kullanıldığı bir kısmı gösterilmektedir. **Eğitme modeli** eğitim kümesine bağlıdır ve test kümesine **puan modeli** bağlıdır.

![Ardışık düzen grafiği](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

İşlem hattının değerlendirme aşamasında dört modelin her birinin doğruluğunu hesaplamalısınız. Bu işlem hattı için, aynı yanlış sınıflandırma maliyetine sahip örnekleri karşılaştırmak üzere **modeli değerlendir** ' i kullanın.

**Modeli değerlendir** modülü, performans ölçümlerini iki puandan daha fazla model için işlem yapabilir. Bu nedenle, iki SVM modelini değerlendirmek için bir **değerlendirme modeli** örneği ve Iki farklı karar ağacı modelini değerlendirmek Için **modeli değerlendir** başka bir örneği kullanabilirsiniz.

Çoğaltılan test veri kümesinin, **Puanlama modeli**girişi olarak kullanıldığına dikkat edin. Diğer bir deyişle, son doğruluk puanları etiketlerin yanlış alınması için maliyeti içerir.

## <a name="combine-multiple-results"></a>Birden çok sonucu birleştirme

**Modeli değerlendir** modülü, çeşitli ölçümler içeren tek bir satır içeren bir tablo oluşturur. Tek bir doğruluk sonuçları kümesi oluşturmak için, önce sonuçları tek bir tabloya birleştirmek üzere **satır ekle** ' yi kullanıyoruz. Daha sonra, sonuç tablosundaki her bir satır için model adını ve eğitim yaklaşımını eklemek üzere **Python betik modülünü yürütme** bölümünde aşağıdaki Python betiğini kullanacağız:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```

## <a name="results"></a>Sonuçlar

İşlem hattının sonuçlarını görüntülemek için **veri kümesi modülündeki son seçme sütunlarının** görselleştirilecek ' a sağ tıklayabilirsiniz.

![Çıktıyı görselleştirin](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

İlk sütunda, modeli oluşturmak için kullanılan makine öğrenimi algoritması listelenir.

İkinci sütun, eğitim kümesinin türünü gösterir.

Üçüncü sütun, maliyet duyarlı doğruluk değerini içerir.

Bu sonuçlardan en iyi doğruluk, **Iki sınıf destek vektör makinesi** ile oluşturulmuş ve çoğaltılan eğitim veri kümesi üzerinde eğitilen model tarafından sağlandığını görebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Tasarımcı için kullanılabilen diğer örnekleri keşfet:

- [Örnek 1-gerileme: bir otomobil fiyatını tahmin edin](how-to-designer-sample-regression-automobile-price-basic.md)
- [Örnek 2-gerileme: otomobil fiyatlandırma için algoritmaları karşılaştırın](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Örnek 3-Özellik seçimi ile sınıflandırma: gelir tahmini](how-to-designer-sample-classification-predict-income.md)
- [Örnek 5-sınıflandırma: karmaşıklığı tahmin etme](how-to-designer-sample-classification-churn.md)
- [Örnek 6-sınıflandırma: uçuş gecikmelerini tahmin etme](how-to-designer-sample-classification-flight-delay.md)
- [Örnek 7-metin sınıflandırması: Vikipedi SP 500 veri kümesi](how-to-designer-sample-text-classification.md)
