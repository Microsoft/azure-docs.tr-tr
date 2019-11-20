---
title: Önizleme ortamlarında veri modelleme-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights önizlemede veri modelleme hakkında bilgi edinin.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/29/2019
ms.custom: seodec18
ms.openlocfilehash: fb1bfb30d531f8b93489c8fc1dfbc6b8172339a3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006467"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Azure Time Series Insights önizlemede veri modelleme

Bu makalede, Azure Time Series Insights önizlemede zaman serisi modeliyle nasıl çalışılacağı açıklanır. Yaygın olarak karşılaşılan çeşitli veri senaryolarına ilişkin ayrıntılar.

Güncelleştirmeyi kullanma hakkında daha fazla bilgi edinmek için [Azure Time Series Insights önizleme Gezgini](./time-series-insights-update-explorer.md)' ni okuyun.

## <a name="types"></a>Türler

### <a name="create-a-single-type"></a>Tek bir tür oluşturma

1. Zaman serisi model Seçicisi paneline gidin ve menüden **türler** ' i seçin. Zaman serisi model türlerine odaklanmak için paneli daraltın.

    [!["türler" bölmesi](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. **+ Ekle** öğesini seçin.
1. Türlerle ilgili tüm ayrıntıları girin ve **Oluştur**' u seçin. Bu eylem, ortamda türler oluşturur.

    [tür eklemek için ![seçimleri](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Bir veya daha fazla türü toplu karşıya yükleme

1. **JSON karşıya yükle**' yi seçin.
1. Tür yükünü içeren dosyayı seçin.
1. **Karşıya Yükle**’yi seçin.

    [![seçimleri bir veya daha fazla türden toplu karşıya yükleme](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Tek bir türü düzenleme

1. Türü seçin ve **Düzenle**' yi seçin. 
1. Gerekli değişiklikleri yapın ve **Kaydet**' i seçin.

    [bir tür düzenlemede ![seçimleri](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Bir tür silme

1. Türü seçin ve **Sil**' i seçin.
1. Türlerle ilişkili bir örnek yoksa, silinir.

    ["Sil" düğmesini ![](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hiyerarşiler

### <a name="create-a-single-hierarchy"></a>Tek bir hiyerarşi oluşturma

1. Zaman serisi model Seçicisi paneline gidin ve menüden **hiyerarşiler** ' ı seçin. Zaman serisi model hiyerarşilerine odaklanmak için paneli daraltın.

    [!["hiyerarşiler" bölmesi](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. **+ Ekle** öğesini seçin.

    ["Ekle" düğmesini ![](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Sağ bölmede **+ Ekle düzeyi** ' ni seçin.

    ["düzey Ekle" düğmesi ![](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Hiyerarşi ayrıntılarını girip **Oluştur**' u seçin.

    [![hiyerarşi ayrıntıları ve "Oluştur" düğmesi](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Bir veya daha fazla hiyerarşiyi toplu karşıya yükleme

1. **JSON karşıya yükle**' yi seçin.
1. Hiyerarşi yükünü içeren dosyayı seçin.
1. **Karşıya Yükle**’yi seçin.

    [hiyerarşileri toplu karşıya yükleme için ![seçimleri](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Tek bir hiyerarşiyi düzenleme

1. Hiyerarşiyi seçin ve **Düzenle**' yi seçin.
1. Gerekli değişiklikleri yapın ve **Kaydet**' i seçin.

    [tek bir hiyerarşiyi düzenlemeyle ilgili ![seçimleri](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Hiyerarşiyi silme

1. Hiyerarşiyi seçin ve **Sil**' i seçin. 
1. Hiyerarşiyle ilişkili örnek yoksa, silinir.

    ["Sil" düğmesini ![](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Örnekler

### <a name="create-a-single-instance"></a>Tek bir örnek oluşturma

1. Zaman serisi model Seçicisi paneline gidin ve menüden **örnekler** ' i seçin. Zaman serisi model örneklerine odaklanmak için paneli daraltın.

    ["örnekler" bölmesi ![](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. **Add (Ekle)** seçeneğini belirleyin.

    [örnek eklemek için ![seçimleri](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Örnek ayrıntılarını girin, tür ve hiyerarşi ilişkilendirmesini seçin ve **Oluştur**' u seçin.

### <a name="bulk-upload-one-or-more-instances"></a>Bir veya daha fazla örneği toplu karşıya yükleme

1. **JSON karşıya yükle**' yi seçin.
1. Örnekler yükünü içeren dosyayı seçin.

    [bir veya daha fazla örnek toplu karşıya yükleme için ![seçimleri](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. **Karşıya Yükle**’yi seçin.

### <a name="edit-a-single-instance"></a>Tek bir örneği düzenleme

1. Örneği seçin ve **Düzenle**' yi seçin. 
1. Gerekli değişiklikleri yapın ve **Kaydet**' i seçin.

    [tek bir örneği düzenlemeyle ilgili ![seçimleri](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

- Zaman serisi modeli hakkında daha fazla bilgi için [veri modellemesini](./time-series-insights-update-tsm.md)okuyun.

- Önizleme hakkında daha fazla bilgi edinmek için [Azure Time Series Insights önizleme Gezgini ' nde verileri görselleştir](./time-series-insights-update-explorer.md)' i okuyun.

- Desteklenen JSON şekilleri hakkında bilgi edinmek için [desteklenen JSON şekillerini](./time-series-insights-send-events.md#supported-json-shapes)okuyun.
