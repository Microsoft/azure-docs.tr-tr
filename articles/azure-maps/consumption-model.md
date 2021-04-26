---
title: Yönlendirme için araç tüketim modelleri | Microsoft Azure haritaları
description: 'Azure haritalarının desteklediği tüketim modelleri hakkında bilgi edinin: combustion ve elektrik. Her modelin hangi parametrelere kullandığını görün ve parametre kısıtlamalarını görüntüleyin.'
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: b44186d783a249192a8c13ee97063034ee319df7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013148"
---
# <a name="consumption-model"></a>Tüketim modeli

Yönlendirme hizmeti, araç açısından özel tüketim modelinin ayrıntılı açıklaması için bir parametre kümesi sağlar.
**VehicleEngineType** değerine bağlı olarak, Iki ana tüketim modeli desteklenir: _combustion_ ve _elektrik_. Aynı istekteki farklı modellere ait olan parametreleri belirtmek yanlış. Ayrıca, tüketim modeli parametreleri aşağıdaki **Seyahatmodu** değerleriyle birlikte kullanılamaz: _Bisiklet_ ve _ayaklı bir_.

## <a name="parameter-constraints-for-consumption-model"></a>Tüketim modeli için parametre kısıtlamaları

Her iki tüketim modelinde de parametre belirtirken bazı bağımlılıklar vardır. Açıkça bazı parametrelerin belirtilme anlamına gelir. Aşağıda bilinmesi gerekolan bağımlılıklar verilmiştir:

* Tüm parametrelerin **constantSpeedConsumption** için Kullanıcı tarafından belirtilmesi gerekir. **ConstantSpeedConsumption** belirtilmemişse, başka bir tüketim modeli parametresi belirtmekte bir hatadır. **VehicleWeight** parametresi bu gereksinim için bir özel durumdur.
* **ivationverimlilik** ve **yavaşlatma ationverimlilik** her zaman çift olarak belirtilmelidir (yani, veya None).
* **İvationverimlilik** ve **yavaşlatma ationverimlilik** belirtilmişse, değerlerinin çarpımı 1 ' den büyük olmamalı (kalıcı hareketi engellemek için).
* **uphillEfficiency** ve **downhillEfficiency** her zaman çift olarak belirtilmelidir (yani veya None).
* **UphillEfficiency** ve **downhillEfficiency** belirtilirse, değerlerinin çarpımı 1 ' den büyük olmamalı (kalıcı hareketi engellemek için).
* \* __Verimlilik__ parametreleri Kullanıcı tarafından belirtilmişse, **vehicleWeight** de belirtilmelidir. **VehicleEngineType** , _combustion_ olduğunda, **fuelenercaydensityınmjoulesperlitre** de belirtilmelidir.
* **Maxchargeınkwh** ve **Currentchargeınkwh** her zaman çift olarak belirtilmelidir (yani veya None).

> [!NOTE]
> Yalnızca **constantSpeedConsumption** belirtilirse, Slopes ve araç hızlandırma gibi başka bir tüketim yönü, tüketim hesaplamaları için hesaba alınmaz.

## <a name="combustion-consumption-model"></a>Combustion tüketim modeli

**VehicleEngineType** , _combustion_ olarak ayarlandığında, combustion tüketim modeli kullanılır.
Bu modele ait parametrelerin listesi aşağıda verilmiştir. Ayrıntılı açıklama için parametreler bölümüne bakın.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* Currentfuelinlinler
* auxiliaryPowerInLitersPerHour
* Fuelenercaydensityınmjoulesperlitre
* Ivationverimlilik
* Yavaşlatma verimliliği
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Elektrik tüketim modeli

Elektrik tüketim modeli, **vehicleEngineType** _elektrik_ olarak ayarlandığında kullanılır.
Bu modele ait parametrelerin listesi aşağıda verilmiştir. Ayrıntılı açıklama için parametreler bölümüne bakın.

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* Currentchargeınkwh
* Maxchargeınkwh
* auxiliaryPowerInkW
* Ivationverimlilik
* Yavaşlatma verimliliği
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Tüketim parametrelerinin erişilebilir değerleri

Küme tüm açık gereksinimleri yerine getirebilse de, belirli bir tüketim parametreleri kümesi reddedilebilir. Belirli bir parametre değeri veya bazı parametrelerin değerlerinin bir birleşimi, tüketim değerlerinin makul olmayan magnitudes bir şekilde kabul edildiği zaman gerçekleşir. Bu durumda, büyük olasılıkla bir giriş hatası belirtilir ve bu, tüketim parametrelerinin tüm uygun değerlerini karşılamak için gerekli bir işlem yapılır. Belirli bir tüketim parametreleri kümesinin reddedilmesi durumunda, eşlik eden hata mesajı neden (s) için bir metinsel açıklama içerecektir.
Parametrelerin ayrıntılı açıklamaları her iki model için de onaylanan değer örneklerine sahiptir.
