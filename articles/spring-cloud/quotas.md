---
title: Azure yay bulutu için hizmet planları ve Kotalar
description: Azure Spring Cloud için hizmet kotaları ve hizmet planları hakkında bilgi edinin
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b02ccb3acb4546e08e7d58159ab9d85bca2d0eed
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878816"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure yay bulutu için kotalar ve hizmet planları

**Bu makale şu şekilde geçerlidir:** ✔️ Java ✔️ C #

Tüm Azure Hizmetleri, kaynaklar ve özellikler için varsayılan limitleri ve kotaları ayarlar.   Azure yay bulutu iki fiyatlandırma katmanı sunar: temel ve standart. Bu makaledeki her iki katmanda da limitleri ayrıntılandıracağız.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure yay bulut hizmeti katmanları ve limitleri

| Kaynak | Kapsam | Temel | Standart
------- | ------- | -------
Sanal işlemci | uygulama örneği başına | 1 | 4
Bellek | uygulama örneği başına | 2 GB | 8 GB
Azure yay bulut hizmeti örnekleri | Abonelik başına bölge başına | 10 | 10
Toplam uygulama örnekleri | Azure yay bulut hizmeti örneği başına | 25 | 500
Özel Etki Alanları | Azure yay bulut hizmeti örneği başına | 0 | 25 
Kalıcı birimler | Azure yay bulut hizmeti örneği başına | 1 GB/uygulama x 10 uygulamaları | 50 GB/uygulama x 10 uygulamaları

> [!TIP]
> Hizmet örneği başına toplam uygulama örnekleri için listelenen sınırlar, durdurulmuş durum da dahil olmak üzere herhangi bir durumda uygulamalar ve dağıtımlar için geçerlidir. Lütfen kullanımda olmayan uygulamaları veya dağıtımları silin.

## <a name="next-steps"></a>Sonraki adımlar

Bazı varsayılan sınırlar artırılabilir. Kurulum için bir artış gerekiyorsa, [bir destek isteği oluşturun](../azure-portal/supportability/how-to-create-azure-support-request.md).
