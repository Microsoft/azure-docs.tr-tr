---
title: Azure API Management API Analytics 'i kullanma | Microsoft Docs
description: API 'lerinizin ve API performanlarınızın kullanımını anlamanıza ve kategorilere ayırmanıza yardımcı olması için Azure API Management 'de analiz kullanın.
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/24/2020
ms.author: apimpm
ms.openlocfilehash: ca7bd70bbf99a6d0079717a7a02328b11528d2e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96841509"
---
# <a name="get-api-analytics-in-azure-api-management"></a>Azure API Management API analizlerini alın

Azure API Management, API 'leriniz için yerleşik analiz sağlar. API Management örneğinizdeki API 'lerin kullanımını ve performansını çeşitli boyutlarda analiz edin, örneğin:

* Saat
* Coğrafya
* API'ler
* API işlemleri
* Ürünler
* Abonelikler
* Kullanıcılar
* İstekler

:::image type="content" source="media/howto-use-analytics/analytics-report-portal.png" alt-text="Portalda zaman çizelgesi Analizi":::

API 'lerinizin üst düzey izleme ve sorun giderme için analiz kullanın. Tanılama ve denetim için neredeyse gerçek zamanlı ölçümler ve kaynak günlükleri dahil olmak üzere ek izleme özellikleri için bkz. [öğretici: Yayınlanan API 'Leri izleme](api-management-howto-use-azure-monitor.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="analytics---portal"></a>Analiz-Portal

Analiz verilerini API Management örneğiniz için bir bakışta gözden geçirmek için Azure portal kullanın.

1. [Azure portal](https://portal.azure.com)API Management örneğinize gidin. 
1. Sol taraftaki menüde, **izleme** altında **analiz**' ı seçin.

    :::image type="content" source="media/howto-use-analytics/monitoring-menu-analytics.png" alt-text="Portalda API Management örneği için analiz seçme":::  
1. Veriler için bir zaman aralığı seçin veya özel bir zaman aralığı girin.
1. Analiz verileri için **zaman çizelgesi**, **Coğrafya** vb. gibi bir rapor kategorisi seçin.
1. İsteğe bağlı olarak, raporu bir veya daha fazla ek kategoriye göre filtreleyin.

## <a name="analytics---rest-api"></a>Analiz-REST API

API Management örneğinizin analiz verilerini almak ve filtrelemek için API Management REST API [rapor](/rest/api/apimanagement/2019-12-01/reports) işlemlerini kullanın.

Kullanılabilir işlemler API, coğrafya, API işlemleri, ürün, istek, abonelik, zaman veya kullanıcıya göre rapor kayıtları döndürür.

## <a name="next-steps"></a>Sonraki adımlar

* API Management Azure Izleyici özelliklerine giriş için bkz [. Öğretici: yayımlanan API 'Leri izleme](api-management-howto-use-azure-monitor.md)
* Ayrıntılı HTTP günlük kaydı ve izleme için bkz. [Azure API Management, Event Hubs ve Moesif Ile API 'Lerinizi izleme](api-management-log-to-eventhub-sample.md).
* Azure API Management Azure [Application Insights ile](api-management-howto-app-insights.md)tümleştirme hakkında bilgi edinin.