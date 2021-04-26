---
title: Uygulama ve hizmet kullanılabilirliği sorunları SSS
description: Bu makalede Microsoft Azure Cloud Services yönelik uygulama ve hizmet kullanılabilirliği hakkında sık sorulan sorular listelenmektedir.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c1a5b63a33f951857bd4837380c1465af5b7583e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742905"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Azure Cloud Services (klasik) için uygulama ve hizmet kullanılabilirliği sorunları: sık sorulan sorular (SSS)

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Bu makalede [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services)yönelik uygulama ve hizmet kullanılabilirliği sorunları hakkında sık sorulan sorular yer almaktadır. Boyut bilgileri için [Cloud SERVICES VM boyutu sayfasına](cloud-services-sizes-specs.md) de başvurabilirsiniz.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Rolm geri dönüştürüldü. Bulut hizmetim için bir güncelleştirme yoktu mi?
Ayda kabaca bir kez, Microsoft, Windows Azure PaaS VM 'Leri için yeni bir konuk işletim sistemi sürümü yayınlar.Konuk işletim sistemi, ardışık düzendeki yalnızca bir güncelleştirmedir. Bir sürüm birçok farklı faktörden etkilenebilir. Ayrıca, Azure yüzlerce binlerce makinede çalışır. Bu nedenle, rollerinizin yeniden başlatılması işleminin tam tarihini ve saatini tahmin etmek olanaksızdır. Konuk işletim sistemi güncelleştirme RSS akışını yaptığımız en son bilgilerle güncelleştiririz, ancak bildirilen süreyi yaklaşık bir değer olacak şekilde göz önünde bulundurmanız gerekir. Bu, müşteriler için sorunlu olduğunu ve yeniden başlatmaları sınırlamak veya tam olarak yeniden başlatmalar için bir plan üzerinde çalışıyor olduğunu biliyoruz.

Son Konuk işletim sistemi güncelleştirmeleriyle ilgili tüm ayrıntılar için bkz. [Azure Konuk işletim sistemi sürümleri ve SDK uyumluluk matrisi](cloud-services-guestos-update-matrix.md).

Yeniden başlatmalar hakkında yararlı bilgiler ve konuk ve konak işletim sistemi güncelleştirmelerinin teknik ayrıntılarına yönelik işaretçiler için, [işletim sistemi yükseltmeleri nedenıyle MSDN blog gönderisi rolü örneği yeniden başlatmaları](/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades)bölümüne bakın.

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Hizmetin bir süre sonra bulut hizmetmin ilk isteği normalden daha uzun sürer mi?
Web sunucusu ilk isteği aldığında, önce kodu yeniden derler ve sonra isteği işler. İlk isteğin diğerlerinden daha uzun sürme nedeni budur. Varsayılan olarak, uygulama havuzu kullanıcının etkin olmaması durumunda kapatılır. Uygulama havuzu, varsayılan olarak her 1.740 dakikada bir de geri dönüştürülecek (29 saat).

Internet Information Services (IIS) uygulama havuzları, uygulama kilitlenmelerine, askıda kalmasına veya bellek sızıntılarına neden olabilecek kararsız durumları önlemek için düzenli aralıklarla geri dönüştürülebilir.

Aşağıdaki belgeler bu sorunu anlamanıza ve azaltmanıza yardımcı olur:
* [IIS için yavaş ilk yük düzeltiliyor](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [IIS 7,5 Web uygulaması ilk istek, uygulama havuzu geri dönüşüm çok yavaş](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

IIS 'nin varsayılan davranışını değiştirmek istiyorsanız, Web rolü örneklerine el ile değişiklikler uyguladığınızda başlangıç görevlerini kullanmanız gerekir, ancak değişiklikler kaybolur.

Daha fazla bilgi için bkz. [bulut hizmeti için başlangıç görevlerini yapılandırma ve çalıştırma](cloud-services-startup-tasks.md).