---
title: Genel Önizleme hizmeti limitleri-Azure dijital TWINS | Microsoft Docs
description: Azure dijital TWINS için genel önizleme hizmeti sınırları hakkında bilgi edinin.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 3cea4fe65e49bfa2d49822d443103ae6cc6ce69f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014163"
---
# <a name="public-preview-service-limits"></a>Genel önizleme hizmet sınırları

Genel Önizleme sırasında Azure dijital TWINS 'in aşağıdaki geçici abonelik, örnek ve hız sınırları vardır.

Bu kısıtlamalar, yeni hizmet ve birçok özelliği hakkında bilgi edinmeye yardımcı olmak için mevcuttur.

> [!NOTE]
> Bu sınırlar, genel kullanıma (GA) göre artar veya kaldırılır.

## <a name="per-subscription-limits"></a>Abonelik başına sınırlar

Genel Önizleme sırasında, her bir Azure aboneliği tek seferde yalnızca bir Azure dijital TWINS örneği oluşturabilir veya çalıştırabilir.

> [!TIP]
> Örneğinizi silerseniz yeni bir tane oluşturabilirsiniz.

## <a name="per-instance-limits"></a>Örnek başına sınırlar

Sırasıyla, her Azure dijital TWINS örneği şunları içerebilir:

- Hizmet sağlama sırasında otomatik olarak oluşturulan bir Embedded **ıothub** kaynağı.
- Olay türü **Devicemessage**için tam olarak bir **EventHub** uç noktası.
- **Sensorchange**, **spacechange**, **Topologyoperation**veya **udfcustom**olay türü için en fazla üç **EventHub**, **ServiceBus**veya **eventgrid** uç noktası.

> [!NOTE]
> Genellikle yukarıdaki Azure IoT varlıklarının oluşturulması sırasında tanımlanan bazı parametreler genel önizleme sırasında gerekli değildir.
> - En son API belirtimleri için [Swagger başvuru belgelerine](./how-to-use-swagger.md) bakın.

## <a name="azure-digital-twins-management-api-limits"></a>Azure dijital TWINS yönetim API sınırları

Azure dijital TWINS yönetim API 'niz için istek oranı sınırları şunlardır:

- Azure Digital TWINS yönetim API 'sine saniye başına 100 istek.
- Tek bir Azure dijital TWINS yönetim API sorgusu tarafından döndürülen en fazla 1.000 nesne.

> [!IMPORTANT]
> 1\.000-nesne sınırını aşarsanız bir hata alırsınız ve sorgunuzu basitleştirmelidir.

## <a name="user-defined-functions-rate-limits"></a>Kullanıcı tanımlı işlevlerin hız sınırları

Aşağıdaki sınırlar, Azure dijital TWINS örneğiniz için yapılan tüm Kullanıcı tanımlı işlev çağrılarının toplam sayısını ayarlar:

- saniye başına 400 istemci kitaplığı çağrısı
- 100 **SendNotification** çağrısı/saniye

> [!NOTE]
> Aşağıdaki eylemler ek hız sınırlarının geçici olarak uygulanmasına neden olabilir:
> - Topoloji nesnesi meta verilerinde yapılan düzenlemeler
> - Kullanıcı tanımlı işlev tanımında yapılan güncelleştirmeler
> - İlk kez telemetri gönderen cihazlar

## <a name="device-telemetry-limits"></a>Cihaz telemetri limitleri

Aşağıdaki sınırlar, cihazlarınızın Azure dijital TWINS örneğine gönderebileceği tüm iletilerin toplam sayısıdır:

- tüm cihazlarda saniye başına 100 ileti
-   cihaz başına saniyede 25 ileti

## <a name="next-steps"></a>Sonraki adımlar

- Bir Azure dijital TWINS örneği denemek için hızlı başlangıç bölümüne giderek [kullanılabilir odaları bulun](./quickstart-view-occupancy-dotnet.md).
