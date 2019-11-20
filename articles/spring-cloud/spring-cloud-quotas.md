---
title: Azure yay bulutu için hizmet planları ve Kotalar
description: Azure Spring Cloud için hizmet kotaları ve hizmet planları hakkında bilgi edinin
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 41a2b1a7d9aa5089ba2ee73cd3c5c5c5e31f5225
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607679"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure yay bulutu için kotalar ve hizmet planları

Tüm Azure Hizmetleri, kaynaklar ve özellikler için varsayılan limitleri ve kotaları ayarlar.  Önizleme dönemi boyunca Azure yay bulutu yalnızca bir hizmet planı sunar.

Bu makalede, geçerli önizleme döneminde sunulan hizmet kotaları ayrıntılı olarak açıklanır.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure yay bulut hizmeti katmanları ve kotaları

Önizleme dönemi boyunca Azure yay bulutu yalnızca bir hizmet katmanı sunar.

Kaynak | Miktar
------- | -------
Sanal işlemci | 4
Bellek | 8 GBayt
Azure yay bulut aboneliği | 1
Abonelik başına bölge başına Azure yay bulut hizmeti örnekleri | 2
Azure yay bulut hizmeti örneği başına toplam uygulama örnekleri | 50
Yay uygulaması başına toplam uygulama örneği sayısı | 20
Kalıcı birimler | 10 x 50 GBayt

Bir kotaya ulaştığınızda, şöyle bir 400 hatası alırsınız: "Kota, *Azure Spring Cloud Service 'in oluşturulduğu*bölge bölgesindeki *aboneliğiniz için* sınırı aşıyor.

## <a name="next-steps"></a>Sonraki adımlar

Bazı varsayılan limitler ve kotalar artırılabilir. Kaynağınız bir artış gerektiriyorsa, [bir destek isteği oluşturun](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).
