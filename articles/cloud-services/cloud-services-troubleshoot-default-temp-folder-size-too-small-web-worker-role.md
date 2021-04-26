---
title: Varsayılan GEÇICI klasör boyutu bir rol için çok küçük | Microsoft Docs
description: Bir bulut hizmeti rolü, GEÇICI klasör için sınırlı miktarda alana sahiptir. Bu makalede, boş alan çalıştırmanın nasıl önleneceğini gösteren bazı öneriler sunulmaktadır.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 1b7bfb47168c31f9e2e1b7e40764439118c00805
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743211"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-classic-webworker-role"></a>Varsayılan GEÇICI klasör boyutu bir bulut hizmeti (klasik) Web/çalışan rolünde çok küçük

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Bir bulut hizmeti çalışanının veya Web rolünün varsayılan geçici dizini en fazla 100 MB olabilir, bu da bazı bir noktada dolu olabilir. Bu makalede, geçici dizin için boş alan kalmadı.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Neden çalışma alanım?
TEMP ve TMP standart Windows ortam değişkenleri, uygulamanızda çalışan kod tarafından kullanılabilir. Hem TEMP hem de TMP, en fazla 100 MB boyutunda tek bir dizine işaret noktasıdır. Bu dizinde depolanan tüm veriler, bulut hizmetinin yaşam döngüsü boyunca kalıcı değildir; bir bulut hizmetindeki rol örnekleri geri dönüştürüldüğünde, Dizin temizlenir.

## <a name="suggestion-to-fix-the-problem"></a>Sorunu gidermeye yönelik öneri
Aşağıdaki seçeneklerden birini uygulayın:

* Yerel bir depolama kaynağı yapılandırın ve TEMP veya TMP kullanmak yerine doğrudan erişin. Uygulamanızda çalışan koddan yerel bir depolama kaynağına erişmek için [Roleenvironment. GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) yöntemini çağırın.
* Yerel bir depolama kaynağı yapılandırın ve yerel depolama kaynağının yolunu işaret etmek için TEMP ve TMP dizinlerini işaret edin. Bu değişiklik [Roleentrypoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) yöntemi içinde gerçekleştirilmelidir.

Aşağıdaki kod örneği, OnStart yönteminin içinden TEMP ve TMP için hedef dizinlerin nasıl değiştirileceğini göstermektedir:

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar
[Azure Web rolü ASP.NET geçici klasörünün boyutunu nasıl artırabileceğinizi](/archive/blogs/kwill/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder)açıklayan bir blog okuyun.

Cloud Services için daha fazla [sorun giderme makalesini](/visualstudio/azure/vs-azure-tools-debugging-cloud-services-overview) görüntüleyin.

Azure PaaS bilgisayar tanılama verilerini kullanarak bulut hizmeti rolü sorunlarını giderme hakkında bilgi edinmek için [Kevin Williamson 'ın blog serisini](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)görüntüleyin.