---
title: Konuk işletim sistemi ailesi 1 kullanımdan kaldırma bildirimi | Microsoft Docs
description: Azure Konuk işletim sistemi ailesi 1 kullanımdan kaldırma gerçekleştiği ve etkilenip etkilenmediğini belirleme hakkında bilgi sağlar
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
ms.service: cloud-services
ms.topic: article
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: 7f6d3feee95d4cce654b2cc1547b8bd7f4eb45d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743024"
---
# <a name="guest-os-family-1-retirement-notice"></a>Konuk İşletim Sistemi Ailesi 1 kullanımdan kaldırma bildirimi

OS ailesi 1 ' in kullanımdan kaldırılması ilk olarak 1 Haziran 2013 ' de duyuruldu.

**2 Eyl, 2014** Windows Server 2008 işletim sistemini temel alan Azure Konuk işletim sistemi (konuk OS) ailesi 1. x resmi olarak kullanımdan kaldırıldı. Yeni hizmetleri dağıtmaya veya aile 1 kullanarak mevcut hizmetleri yükseltmeye yönelik tüm girişimler, Konuk işletim sistemi ailesi 1 ' in devre dışı olduğunu bildiren bir hata iletisiyle başarısız olur.

**3 kasım 2014** Konuk işletim sistemi ailesi 1 için genişletilmiş destek sona erdi ve tamamen kullanımdan kaldırıldı. Tüm hizmetler hala aile 1 ' de etkilenecektir. Bu hizmetleri dilediğiniz zaman durduracağız. Kendiniz de el ile yükseltmediğiniz müddetçe hizmetlerinizin çalışmaya devam edeceğini garanti yoktur.

Başka sorularınız varsa, [Cloud Services Için Microsoft Q&soru sayfasını](/answers/topics/azure-cloud-services.html) ziyaret edin veya [Azure desteği 'ne başvurun](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Etkilensin mi?
Aşağıdakilerden biri geçerliyse Cloud Services etkilenir:

1. Bulut hizmetiniz için ServiceConfiguration. cscfg dosyasında açıkça belirtilen "osFamily =" 1 "değerine sahip olursunuz.
2. Bulut hizmetiniz için ServiceConfiguration. cscfg dosyasında açıkça belirtilmiş bir osFamily değeri yok. Şu anda sistem bu durumda varsayılan değeri "1" kullanır.
3. Azure portal, Konuk Işletim sistemi aile değerini "Windows Server 2008" olarak listeler.

Bulut hizmetlerinizin hangi işletim sistemi ailesinden çalıştığını öğrenmek için, Azure PowerShell aşağıdaki betiği çalıştırabilirsiniz, ancak önce [Azure PowerShell ayarlamanız](/powershell/azure/) gerekir. Betiği hakkında daha fazla bilgi için bkz. [Azure Konuk işletim sistemi ailesi 1 yaşam sonu: haziran 2014](/archive/blogs/ryberry/azure-guest-os-family-1-end-of-life-june-2014).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Betik çıktısındaki osFamily sütunu boşsa veya bir "1" içeriyorsa, bulut hizmetlerinizin işletim sistemi ailesi 1 kullanımdan kaldırılması etkilenecektir.

## <a name="recommendations-if-you-are-affected"></a>Etkileniyorsanız öneriler
Bulut hizmeti rollerinizi desteklenen konuk işletim sistemi ailelerinin birine geçirmeniz önerilir:

**Konuk işletim sistemi ailesi 4. x** -Windows Server 2012 R2 *(önerilir)*

1. Uygulamanızın .NET Framework 4,0, 4,5 veya 4.5.1 SDK 2,1 veya üstünü kullandığından emin olun.
2. ServiceConfiguration. cscfg dosyasında osFamily özniteliğini "4" olarak ayarlayın ve bulut hizmetinizi yeniden dağıtın.

**Konuk işletim sistemi ailesi 3. x** -Windows Server 2012

1. Uygulamanızın, .NET Framework 4,0 veya 4,5 ile SDK 1,8 veya sonraki bir sürümünü kullandığından emin olun.
2. ServiceConfiguration. cscfg dosyasında osFamily özniteliğini "3" olarak ayarlayın ve bulut hizmetinizi yeniden dağıtın.

**Konuk işletim sistemi ailesi 2. x** -Windows Server 2008 R2

1. Uygulamanızın, .NET Framework 3,5 veya 4,0 ile SDK 1,3 ve üzeri kullandığından emin olun.
2. ServiceConfiguration. cscfg dosyasında osFamily özniteliğini "2" olarak ayarlayın ve bulut hizmetinizi yeniden dağıtın.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Konuk işletim sistemi ailesi 1 için genişletilmiş destek 3 Kasım 2014
Konuk işletim sistemi ailesi 1 ' de bulut hizmetleri artık desteklenmiyor. Hizmet kesintisini önlemek için Family 1 ' den en kısa sürede geçiş yapın.  

## <a name="next-steps"></a>Sonraki adımlar
En son [Konuk işletim sistemi sürümlerini](cloud-services-guestos-update-matrix.md)gözden geçirin.