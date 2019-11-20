---
title: Taşıma hatalarını giderme
description: Kaynakları yeni kaynak grubuna veya aboneliğe taşıma için Azure Resource Manager'ı kullanın.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 46fedeb8b50b19a0adc34b2099e7e76b888821f6
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149114"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Azure kaynaklarını yeni kaynak grubuna veya aboneliğe taşıma sorunlarını giderme

Bu makalede, kaynakları taşırken oluşan sorunları çözmeye yardımcı olacak öneriler sunulmaktadır.

## <a name="upgrade-a-subscription"></a>Aboneliği yükseltme

Azure aboneliğinizi gerçekten yükseltmek istiyorsanız (örneğin, ücretsiz 'den Kullandıkça Öde 'ye geçiş), aboneliğinizi dönüştürmeniz gerekir.

* Ücretsiz deneme sürümü yükseltmek için bkz: [ücretsiz deneme sürümü ya da Microsoft Imagine Azure aboneliğinizi Kullandıkça Öde aboneliğine yükseltme](../billing/billing-upgrade-azure-subscription.md).
* Bir Kullandıkça Öde hesabına değiştirmek için bkz [Azure Kullandıkça Öde aboneliğinizi değiştirmek için farklı bir teklif](../billing/billing-how-to-switch-azure-offer.md).

Abonelik dönüştüremezse [bir Azure destek isteği oluşturma](../azure-supportability/how-to-create-azure-support-request.md). Seçin **abonelik yönetimi** sorun türü için.

## <a name="service-limitations"></a>Hizmet sınırlamaları

Bazı hizmetler, kaynakları taşırken ek hususlar gerektirir. Aşağıdaki hizmetleri taşıyorsanız, kılavuz ve sınırlamaları kontrol ettiğinizden emin olun.

* [Uygulama Hizmetleri](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Klasik dağıtım modeli](./move-limitations/classic-model-move-limitations.md)
* [Ağ](./move-limitations/networking-move-limitations.md)
* [Kurtarma Hizmetleri](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Sanal Makineler](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Büyük istekler

Mümkün olduğunda, kesme büyük ayrı taşıma işlemlerini taşır. Tek bir işlemde 800 'den fazla kaynak olduğunda hemen Kaynak Yöneticisi bir hata döndürür. Ancak, 800'den daha az kaynağı taşımadan da zaman aşımına göre başarısız olabilir.

## <a name="resource-not-in-succeeded-state"></a>Kaynak başarılı değil durumunda

Kaynak başarılı bir durumda olmadığından, bir kaynağın taşınamayacağını belirten bir hata iletisi aldığınızda, gerçekten taşımayı engelleyen bir bağımlı kaynak olabilir. Genellikle, hata kodu **MoveCannotProceedWithResourcesNotInSucceededState**' dir.

Kaynak veya hedef kaynak grubu bir sanal ağ içeriyorsa, sanal ağ için tüm bağımlı kaynakların durumları taşıma sırasında denetlenir. Denetim, bu kaynakları doğrudan ve sanal ağa dolaylı olarak bağlı olarak içerir. Bu kaynaklardan herhangi biri başarısız durumdaysa taşıma engellenir. Örneğin, sanal ağ kullanan bir sanal makine başarısız olduysa, taşıma engellenir. Sanal makine, taşınmakta olan kaynaklardan biri olmadığında ve taşıma için kaynak gruplarından birinde yer alsa bile taşıma engellenir.

Bu hatayı aldığınızda, iki seçeneğiniz vardır. Kaynaklarınızı sanal ağı olmayan bir kaynak grubuna taşıyın veya [desteğe başvurun](../azure-supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Sonraki adımlar

Kaynakları taşıma komutları için bkz. [kaynakları yeni kaynak grubuna veya aboneliğe taşıma](resource-group-move-resources.md).
