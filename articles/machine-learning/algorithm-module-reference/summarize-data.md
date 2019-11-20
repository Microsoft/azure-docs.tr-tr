---
title: Verileri özetleme
titleSuffix: Azure Machine Learning service
description: Bir veri kümesindeki sütunlar için temel bir açıklayıcı istatistik raporu oluşturmak üzere Azure Machine Learning hizmetinde özetleme verileri modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 503eb533b83cbeedcc16b73a9c1dbe821a4d4d94
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492580"
---
# <a name="summarize-data"></a>Verileri özetleme

Bu makalede bir Azure Machine Learning Designer (Önizleme) modülü açıklanır.

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

1. İşlem hattını çalıştırın veya modüle sağ tıklayıp **Seçileni Çalıştır**' ı seçin.

## <a name="results"></a>Sonuçlar

Modülden rapor aşağıdaki istatistikleri içerebilir. 

|Sütun adı|Açıklama|
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
|**Modundaysa**|Sütun değerlerinin modu|
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

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning hizmeti için [kullanılabilen modül kümesine](module-reference.md) bakın.  
