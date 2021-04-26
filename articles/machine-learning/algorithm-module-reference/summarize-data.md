---
title: Verileri Özetleme
titleSuffix: Azure Machine Learning
description: Bir veri kümesindeki sütunlar için temel bir açıklayıcı istatistik raporu oluşturmak üzere Azure Machine Learning ' de özetleme verileri modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: 5206565b85d1551e5e551f1dfe75d28c93bc53f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898208"
---
# <a name="summarize-data"></a>Verileri Özetleme

Bu makalede bir Azure Machine Learning Designer modülü açıklanmaktadır.

Giriş tablosundaki her bir sütunu tanımlayan bir dizi standart istatistiksel ölçü oluşturmak için veri özetleme modülünü kullanın.

Özet istatistikleri, tüm veri kümesinin özelliklerini anlamak istediğinizde faydalıdır. Örneğin, şunları bilmeniz gerekebilir:

- Her sütunda kaç tane eksik değer var?
- Bir özellik sütununda kaç benzersiz değer var?
- Her sütun için Ortalama ve standart sapma nedir?

Modül her bir sütunun önemli puanlarını hesaplar ve giriş olarak girilen her değişken (veri sütunu) için bir Özet istatistikleri satırı döndürür.

## <a name="how-to-configure-summarize-data"></a>Özet verileri yapılandırma  

1. **Özet veri** modülünü ardışık düzene ekleyin. Bu modülü, tasarımcı 'daki **Istatistiksel işlevler** kategorisinde bulabilirsiniz.

1. Rapor oluşturmak istediğiniz veri kümesini bağlayın.

    Yalnızca bazı sütunlara raporlamak istiyorsanız, birlikte çalışmak üzere bir sütun alt kümesini proje yapmak için [veri kümesindeki sütunları seçme](select-columns-in-dataset.md) modülünü kullanın.

1. Ek parametre gerekmez. Varsayılan olarak, modül girdi olarak sunulan tüm sütunları analiz eder ve sütunlardaki değerlerin türüne bağlı olarak, [sonuçlar](#results) bölümünde açıklandığı gibi ilgili bir istatistik kümesi verir.

1. İşlem hattını gönderme.

## <a name="results"></a>Sonuçlar

Modülden rapor aşağıdaki istatistikleri içerebilir. 

|Sütun adı|Description|
|------|------|  
|**Özellik**|Sütunun adı|
|**Biriktirme**|Tüm satırların sayısı|
|**Benzersiz değer sayısı**|Sütundaki benzersiz değer sayısı|
|**Eksik değer sayısı**|Sütundaki benzersiz değer sayısı|
|**Min**|Sütundaki en düşük değer|  
|**Biçimlendir**|Sütundaki en yüksek değer|
|**Ortası**|Tüm sütun değerlerinin ortalaması|
|**Ortalama sapma**|Sütun değerlerinin ortalama sapması|
|**1. DÖRTTEBİRLİK**|İlk dörttebir değer|
|**Ortanca**|Ortanca sütun değeri|
|**3. DÖRTTEBİRLİK**|Üçüncü dörttebir değer|
|**Mod**|Sütun değerlerinin modu|
|**Aralık**|Maksimum ve minimum değerler arasındaki değer sayısını temsil eden tamsayı|
|**Örnek varyans**|Sütun varyansı; bkz. nota|
|**Örnek standart sapması**|Sütun için standart sapma; bkz. nota|
|**Örnek çarpıklığı**|Sütun için çarpıklık; bkz. nota|
|**Örnek basıklık**|Sütun için basıklık; bkz. nota|
|**P 0,5**|% 0,5 yüzdebirlik|
|**P1**|1% yüzdebirlik|
|**P5**|5% yüzdebirlik|
|**P95**|%95 yüzdebirlik|
|**P 99,5**|% 99,5 yüzdebirlik |

## <a name="technical-notes"></a>Teknik notlar

- Sayısal olmayan sütunlar için yalnızca Count, Unique Value Count ve Missing değer Count değerleri hesaplanır. Diğer İstatistikler için null değeri döndürülür.

- Boole değerleri içeren sütunlar şu kurallar kullanılarak işlenir:

    - Min hesaplanırken, mantıksal bir ve uygulanır.
    
    - Max hesaplanırken, bir mantıksal OR uygulandı
    
    - İşlem aralığı hesaplanırken, modül ilk olarak sütundaki benzersiz değer sayısının 2 ' ye eşit olup olmadığını denetler.
    
    - Kayan nokta hesaplamaları gerektiren herhangi bir istatistiği hesaplarken, true değeri 1,0 olarak değerlendirilir ve false değerleri 0,0 olarak değerlendirilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın.  
