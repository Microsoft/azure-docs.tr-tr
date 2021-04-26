---
title: PostgreSQL için Azure veritabanı-hiper ölçek (Citus)-zamanlanmış bakım-Azure portal
description: Azure portal bir PostgreSQL için Azure veritabanı-hiper ölçek (Citus) için zamanlanmış bakım ayarlarını yapılandırmayı öğrenin.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 04dd37e3e9abbfbec7badb036802e645cc7732b0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108518"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL için Azure veritabanı için zamanlanmış bakım ayarlarını yönetme – hiper ölçek (Citus)

Azure aboneliğinizdeki her bir hiper ölçek (Citus) sunucu grubu için bakım seçeneklerini belirtebilirsiniz. Seçenekler, yaklaşan ve tamamlanmış bakım olaylarının bakım zamanlamasını ve bildirim ayarlarını içerir.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için şunlar gerekir:

- [PostgreSQL Için Azure veritabanı-Hyperscale (Citus) sunucu grubu](quickstart-create-hyperscale-portal.md)

## <a name="specify-maintenance-schedule-options"></a>Bakım zamanlaması seçeneklerini belirtin

1. Hiper ölçek (Citus) sunucu grubu sayfasında, **Ayarlar** başlığı altında **bakım** ' ı seçerek zamanlanmış bakım seçeneklerini açın.
2. Varsayılan (sistem tarafından yönetilen) zamanlama, haftanın rastgele bir günüdür ve bakım başlangıç saati ile 23.00 aralığındaki değerler ile 7pm sunucu grubunun [Azure bölge zamanı](https://go.microsoft.com/fwlink/?linkid=2143646)arasında 30 dakikalık bir penceredir. Bu zamanlamayı özelleştirmek istiyorsanız **özel zamanlama**' yı seçin. Daha sonra haftanın tercih edilen gününü ve bakım başlangıç zamanı için 30 dakikalık bir pencere seçebilirsiniz.

## <a name="notifications-about-scheduled-maintenance-events"></a>Zamanlanmış bakım olayları hakkında bildirimler

Azure hizmet durumu ' nu, Hiperscale (Citus) sunucu grubunuzda yaklaşan ve son zamanlanmış bakım hakkında [bildirimleri görüntülemek](../service-health/service-notifications.md) için kullanabilirsiniz. Ayrıca, bakım olayları hakkında bildirim almak için Azure hizmet durumu 'nda uyarılar [ayarlayabilirsiniz](../service-health/resource-health-alert-monitor-guide.md) .

## <a name="next-steps"></a>Sonraki adımlar

* [PostgreSQL Için Azure veritabanı 'nda zamanlanmış bakım hakkında bilgi edinin – hiper ölçek (Citus)](concepts-hyperscale-maintenance.md)
* [Azure hizmet durumu](../service-health/overview.md) hakkında yalın
