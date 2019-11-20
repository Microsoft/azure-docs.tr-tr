---
title: Azure uygulama ağ geçidi kaynak durumu genel bakış
description: Bu makalede kaynak durumu Özelliği Azure Application Gateway için bir genel bakıştır
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659508"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Azure uygulama ağ geçidi kaynak durumu genel bakış

[Azure kaynak durumu](../service-health/resource-health-overview.md) tanılamanıza ve bir Azure hizmet sorunu kaynaklarınızı etkilediğinde destek almanıza yardımcı olur. Güncel ve geçmiş kaynaklarınızın sistem durumunu hakkında bilgilendirir. Ve sorunları azaltmaya yardımcı olmak için teknik destek sağlar.

Application Gateway için kaynak durumu, iyi durumda olup olmadığını değerlendirmek için çalışan ağ geçidi tarafından yayılan sinyalleri üzerinde kullanır. Kaynak durumu, ağ geçidi kötü durumda, sorunun kaynağını belirlemek üzere ek bilgiler analiz eder. Ayrıca, Microsoft sürüyor eylemler veya sorunu çözmek için yapabilecekleriniz de tanımlar.

Sistem durumu nasıl değerlendirdiğini ilgili ek ayrıntılar kaynak türlerinin tam listesini gözden geçirin ve durum denetimleri [Azure kaynak durumu](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


Application Gateway sistem durumunu aşağıdaki durumlardan biri görüntülenir:

## <a name="available"></a>Kullanılabilir

Bir **kullanılabilir** durumu, hizmet kaynak durumunu etkileyen herhangi bir olayı algılandı taşınmadığından anlamına gelir. Göreceğiniz **yakın zamanda çözülen** bildirim burada ağ geçidi kurtarıldı Planlanmamış kapalı kalma süresi son 24 saatte bir durumda.

![Kullanılabilir sistem durumu](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Kullanılamaz

Bir **kullanılamıyor** durumu, devam eden platform ya da ağ geçidi durumunu etkileyen platform olmayan olayı service algıladı anlamına gelir.

### <a name="platform-events"></a>Platform etkinlikleri

Platform olayı birden çok Azure altyapısının bileşenleri tarafından tetiklenir. Bunlar, hem zamanlanmış Eylemler (örneğin, planlı bakım) hem de beklenmedik olaylar (örneğin, bir beklenmeyen ana bilgisayar yeniden başlatma) içerir.

Kaynak durumu, olay ve kurtarma işlemi hakkında ek ayrıntılar sağlar. Ayrıca destek etkin bir Microsoft olmasa bile destek ile iletişime geçmenizi sağlar.

![Kullanılabilir durum](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Bilinmiyor

**Bilinmeyen** sistem durumunu gösteren kaynak durumu 10 dakikadan daha fazla ağ geçidi hakkında bilgi almadı. Bu durum, eksiksiz bir ağ geçidi durumu göstergesi değildir. Ancak, bir sorun giderme işlemi içinde önemli bir veri noktasıdır.

Ağ geçidi beklenen şekilde çalışıyorsa, durumu değişerek **kullanılabilir** birkaç dakika sonra.

Sorunları yaşıyorsanız **bilinmeyen** sistem durumu bir platform olayı ağ geçidi etkilediğini Öner.

![Bilinmeyen durum](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Düşürüldü

**Degraded** sistem durumunu gösterir, ağ geçidi, performans kaybı algıladı kullanımı için hala kullanılabilir olsa da.

![Degrated durumu](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Sonraki adımlar

Application Gateway Web uygulaması Güvenlik Duvarı (WAF) sorun giderme hakkında bilgi edinmek için [sorun giderme Web uygulaması Güvenlik Duvarı (WAF) için Azure Application Gateway](web-application-firewall-troubleshoot.md).