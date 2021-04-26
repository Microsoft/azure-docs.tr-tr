---
title: Mevcut bir Service Fabric kümesinde yönetilen kimlik desteğini yapılandırma
description: Mevcut bir Azure Service Fabric kümesinde Yönetilen kimlikler desteğini etkinleştirme
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: dc341688cae6d98f53be10351e4e4572a3539e4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790048"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster"></a>Mevcut bir Service Fabric kümesinde yönetilen kimlik desteğini yapılandırma

Service Fabric uygulamalarınızda [Azure kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) kullanmak üzere önce kümede *yönetilen kimlik belirteci hizmetini* etkinleştirin. Bu hizmet, yönetilen kimliklerini kullanan Service Fabric uygulamalarının kimlik doğrulamasından ve kendi adına erişim belirteçleri elde etmeye sorumludur. Hizmet etkinleştirildikten sonra, sol bölmedeki **sistem** bölümü altında, **doku:/System/Managedıdentitytokenservice** adı altında çalışan Service Fabric Explorer görebilirsiniz.

> [!NOTE]
> **Yönetilen kimlik belirteci hizmetini** etkinleştirmek için Service Fabric Runtime sürümü 6.5.658.9590 veya üzeri gereklidir.  
>
> Küme kaynağını açıp **temel** bileşenler bölümündeki **Service Fabric sürümü** özelliğini denetleyerek Azure Portal bir kümenin Service Fabric sürümünü bulabilirsiniz.
>
> Küme **el ile** yükseltme modundaysa, önce bunu 6.5.658.9590 veya sonraki bir sürüme yükseltmeniz gerekir.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>Mevcut kümede *yönetilen kimlik belirteci hizmetini* etkinleştir

Mevcut bir kümede yönetilen kimlik belirteci hizmetini etkinleştirmek için iki değişiklik belirten bir küme yükseltmesi başlatmanız gerekir: (1) yönetilen kimlik belirteci hizmetini etkinleştirme ve (2) her düğümün yeniden başlatılmasını isteme. İlk olarak, aşağıdaki kod parçacığını kümeniz Azure Resource Manager şablonu ekleyin:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

Değişikliklerin etkili olabilmesi için yükseltme ilkesini, yükseltmenin kümede ilerledikçe her düğümde Service Fabric çalışma zamanının zorla yeniden başlatılmasını belirtmek için de değiştirmeniz gerekir. Bu yeniden başlatma, yeni etkinleştirilmiş sistem hizmetinin her düğüm üzerinde başlatılmış ve çalışır olmasını sağlar. Aşağıdaki kod parçacığında, `forceRestart` yeniden başlatmayı etkinleştirmek için gerekli olan ayardır. Kalan parametreler için aşağıda açıklanan değerleri kullanın veya küme kaynağı için zaten belirtilmiş olan mevcut özel değerleri kullanın. Fabric yükseltme Ilkesi (' upgradeDescription ') için özel ayarlar, Service Fabric kaynağında veya resources.azure.com ' yapı yükseltmeleri ' seçeneği belirlenerek Azure portalından görüntülenebilir. Yükseltme ilkesi için varsayılan seçenekler (' upgradeDescription ') PowerShell veya resources.azure.com 'dan görüntülenemez. Daha fazla bilgi için bkz. [Clusterupgradepolicy](/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy) .  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> Yükseltmenin başarıyla tamamlanmasından sonra, `forceRestart` sonraki yükseltmelerin etkilerini en aza indirmek için ayarı geri almayı unutmayın. 

## <a name="errors-and-troubleshooting"></a>Hatalar ve sorun giderme

Dağıtım aşağıdaki iletiyle başarısız olursa, kümenin yeterince yüksek bir Service Fabric sürümünde çalışmadığı anlamına gelir:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Sonraki adımlar
* [Sistem tarafından atanan yönetilen kimlik ile Azure Service Fabric uygulaması dağıtma](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Kullanıcı tarafından atanan yönetilen kimlik ile bir Azure Service Fabric uygulaması dağıtma](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Hizmet kodundan Service Fabric uygulamasının yönetilen kimliğinden yararlanın](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric uygulamasına diğer Azure kaynaklarına erişim izni verme](./how-to-grant-access-other-resources.md)
