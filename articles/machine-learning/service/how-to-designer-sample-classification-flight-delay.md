---
title: 'Tasarımcı: sınıflandırma, uçuş gecikmelerini tahmin etme'
titleSuffix: Azure Machine Learning
description: Bu makalede, sürükle ve bırak tasarımcısını ve özel R kodunu kullanarak uçuş gecikmelerini tahmin etmek için bir makine öğrenimi modelinin nasıl oluşturulacağı gösterilmektedir.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 10b3b78b4878eeddf2f786fda90ab9709e4bd2b4
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647193"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>Örnek 6-sınıflandırma: R kullanarak uçuş gecikmelerini tahmin etme
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Bu işlem hattı, zamanlanan bir pasur uçuşun 15 dakikadan fazla ertelenmesini tahmin etmek için geçmiş Uçuş ve hava durumu verilerini kullanır. Bu sorun, bir sınıflandırma sorunu olarak approached olabilir ve iki sınıfı tahmin edebilir: gecikiyor ya da zaman.

Bu örnek için nihai işlem hattı grafiği aşağıda verilmiştir:

[işlem hattının ![grafiği](media/how-to-ui-sample-classification-predict-flight-delay/pipeline-graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Örnek 6 ' ya tıklayarak açın.

## <a name="get-the-data"></a>Verileri alma

Bu örnek, **uçuş gecikmeleri veri** veri kümesini kullanır. Bu, ABD 'deki nakliye departmanından gelen TranStats veri koleksiyonunun bir parçasıdır. Veri kümesi, Nisan 'dan Ekim 2013 ' e kadar olan Uçuş gecikmesi bilgilerini içerir. Veri kümesi aşağıdaki şekilde önceden işlenir:

* Sürekli olarak 70 ortalamanızı havaalanları içerecek şekilde filtrelenir Birleşik Devletler.
* 15 dakikadan uzun bir süre geciktiğinde, uçuşarak iptal edildi.
* Filtrelenmiş, ayrılmış fışıkları.
* 14 sütun seçildi.

Uçuş verilerini tamamlamak için **Hava durumu veri kümesi** kullanılır. Hava durumu verileri, NOAA 'den saatlik, Land tabanlı hava durumu gözlemlerini içerir ve hava durumu istasyonlarından alınan ve fışıksal veri kümesiyle aynı zaman dilimini kapsayan gözlemleri temsil eder. Önceden işlenir:

* Hava durumu istasyonu kimlikleri ilgili Havaalanı kimliklerine eşlendi.
* 70 ortalamanızı ile ilişkilendirilmemiş Hava durumu istasyonları kaldırılmıştır.
* Tarih sütunu ayrı sütunlara bölündü: Year, month ve Day.
* Seçili 26 sütun.

## <a name="pre-process-the-data"></a>Verileri önceden işleme

Bir veri kümesi, analiz edilebilmesi için genellikle bazı ön işleme gerektirir.

![veri işleme](media/how-to-ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Uçuş verileri

**Taşıyıcı**, **Originairportıd**ve **destairportıd** sütunları, tamsayılar olarak kaydedilir. Ancak kategorik özniteliklerdir, bunları kategorik olarak dönüştürmek için **meta verileri Düzenle** modülünü kullanın.

![Düzenle-meta veriler](media/how-to-ui-sample-classification-predict-flight-delay/edit-metadata.png)

Ardından, olası hedef makineleri olan veri kümesi sütunlarından dışlamak için veri kümesindeki **Sütunları Seç** modülünü kullanın: **depdelay**, **DepDel15**, **ArrDelay**, **iptal edildi**, **yıl**. 

Saatlik hava durumu kayıtlarıyla uçuş kayıtlarına katılabilmek için, JOIN anahtarlarından biri olarak zamanlanan ayrılma saatini kullanın. Birleştirmeyi yapmak için, CSRDepTime sütunu, **yürütme R betiği** modülünde tarafından gerçekleştirilen en yakın saate yuvarlanmalıdır. 

### <a name="weather-data"></a>Hava durumu verileri

Eksik değerlerin büyük bir oranındaki sütunlar **Proje sütunları** modülü kullanılarak dışlanır. Bu sütunlar, tüm dize değerli sütunları içerir: **ValueforwınWetBulbFarenheit character**,, **wetbulbsanti,** **PressureTendency**, **PressureChange**, **deniz levelbasınç**ve **stationbasıncı** .

Eksik verileri **Temizleme** modülü daha sonra eksik verileri olan satırları kaldırmak için kalan sütunlara uygulanır.

Hava durumu gözlemme süreleri en yakın tam saate yuvarlanır. Zamanlanan uçuş süreleri ve hava durumu izleme süreleri, modelin uçuş zamanından önce yalnızca hava durumunu kullandığından emin olmak için ters yönlere yuvarlanır. 

Hava durumu verileri yerel saatte raporlandığından, saat dilimi farklılıkları, zamanlanan ayrılma saatinden ve hava durumu izleme zamanından itibaren saat dilimi sütunları çıkarılarak hesaba katılmaz. Bu işlemler **R betiği Yürüt** modülü kullanılarak yapılır.

### <a name="joining-datasets"></a>Veri kümelerini birleştirme

Uçuş kayıtları, **veri katılma** modülünü kullanarak uçuş (**Originairportıd**) başlangıcında Hava durumu verileriyle birleştirilir.

 ![başlangıca göre uçuş ve hava durumunu birleştirin](media/how-to-ui-sample-classification-predict-flight-delay/join-origin.png)


Uçuş kayıtları, uçuşın hedefi (**Destairportıd**) kullanılarak hava durumu verileriyle birleştirilir.

 ![Hedefe göre uçuş ve hava durumunu birleştirin](media/how-to-ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Eğitim ve test örnekleri hazırlama

**Verileri Böl** modülü, verileri eğitim için Eylül kayıtlarına ve test için Ekim kayıtlarına böler.

 ![Eğitim ve test verilerini bölme](media/how-to-ui-sample-classification-predict-flight-delay/split.png)

Year, month ve TimeZone sütunları, select Columns modülünü kullanarak eğitim veri kümesinden kaldırılır.

## <a name="define-features"></a>Özellikleri tanımlama

Machine Learning 'de, Özellikler ilgilendiğiniz bir şeyin bağımsız olarak ölçülebilir özellikleridir. Güçlü bir özellik kümesinin bulunması deneme ve etki alanı bilgisi gerektirir. Bazı özellikler, hedefi tahmin etmede diğerlerinden daha uygundur. Ayrıca, bazı özelliklerin diğer özelliklerle güçlü bir bağıntısı olabilir ve modele yeni bilgi eklemez. Bu özellikler kaldırılabilir.

Bir model oluşturmak için, kullanılabilen tüm özellikleri kullanabilir veya özelliklerin bir alt kümesini seçebilirsiniz.

## <a name="choose-and-apply-a-learning-algorithm"></a>Öğrenme algoritması seçme ve uygulama

**Iki sınıf lojistik regresyon** modülünü kullanarak bir model oluşturun ve bunu eğitim veri kümesinde eğitin. 

**Eğitim modeli** modülünün sonucu, tahmine dayalı hale getirmek için yeni örnekleri puan etmek üzere kullanılabilecek eğitilen bir sınıflandırma modelidir. Eğitilen modellerden puanlar oluşturmak için test kümesini kullanın. Ardından, modellerin kalitesini analiz etmek ve karşılaştırmak için **modeli değerlendir** modülünü kullanın.
işlem hattı işlem hattını çalıştırdıktan sonra, çıkış bağlantı noktasına tıklayıp **Görselleştir**' i seçerek **puanı model** modülündeki çıktıyı görüntüleyebilirsiniz. Çıktı, Etiketler için puanlanmış etiketleri ve olasılıklara sahiptir.

Son olarak, sonuçların kalitesini test etmek için, **modeli değerlendir** modülünü işlem hattı tuvaline ekleyin ve sol giriş bağlantı noktasını puan modeli modülünün çıktısına bağlayın. İşlem hattını çalıştırın ve çıkış bağlantı noktasına tıklayıp **Görselleştir**' i seçerek **modeli değerlendir** modülünün çıkışını görüntüleyin.

## <a name="evaluate"></a>Değerlendir
Lojistik regresyon modelinde test kümesinde AUC 0,631 vardır.

 ![değerlendir](media/how-to-ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>Sonraki adımlar

Tasarımcı için kullanılabilen diğer örnekleri keşfet:

- [Örnek 1-gerileme: bir otomobil fiyatını tahmin edin](how-to-designer-sample-regression-automobile-price-basic.md)
- [Örnek 2-gerileme: otomobil fiyatlandırma için algoritmaları karşılaştırın](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Örnek 3-Özellik seçimi ile sınıflandırma: gelir tahmini](how-to-designer-sample-classification-predict-income.md)
- [Örnek 4-sınıflandırma: kredi riskini tahmin etme (maliyet duyarlı)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Örnek 5-sınıflandırma: karmaşıklığı tahmin etme](how-to-designer-sample-classification-churn.md)
- [Örnek 7-metin sınıflandırması: Vikipedi SP 500 veri kümesi](how-to-designer-sample-text-classification.md)