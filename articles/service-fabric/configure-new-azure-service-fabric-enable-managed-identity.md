---
title: Yeni bir Service Fabric kümesi için yönetilen kimlik desteğini yapılandırma
description: Yeni bir Azure Service Fabric kümesinde Yönetilen kimlikler desteğini etkinleştirme
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: ae4fec452a2342a68843d874ba955b594014c46d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574675"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster"></a>Yeni bir Service Fabric kümesi için yönetilen kimlik desteğini yapılandırma

Service Fabric uygulamalarınızda [Azure kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) kullanmak üzere önce kümede *yönetilen kimlik belirteci hizmetini* etkinleştirin. Bu hizmet, yönetilen kimliklerini kullanan Service Fabric uygulamalarının kimlik doğrulamasından ve kendi adına erişim belirteçleri elde etmeye sorumludur. Hizmet etkinleştirildikten sonra, sol bölmedeki **sistem** bölümü altında, diğer sistem hizmetleri ' nin yanında bulunan **Fabric:/System/Managedıdentitytokenservice** altında çalışan Service Fabric Explorer görebilirsiniz.

> [!NOTE]
> **Yönetilen kimlik belirteci hizmetini** etkinleştirmek için Service Fabric Runtime sürümü 6.5.658.9590 veya üzeri gereklidir.  

## <a name="enable-the-managed-identity-token-service"></a>Yönetilen kimlik belirteci hizmetini etkinleştirin

Küme oluşturma sırasında yönetilen kimlik belirteci hizmetini etkinleştirmek için, aşağıdaki kod parçacığını kümenize Azure Resource Manager ekleyin:

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

## <a name="errors"></a>Hatalar

Dağıtım Bu iletiyle başarısız olursa, kümenin gerekli Service Fabric sürümünde olmadığı anlamına gelir (desteklenen en düşük çalışma zamanı 6,5 CU2 UYGULAMAZSANıZ):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>İlgili Makaleler

* Azure Service Fabric [yönetilen kimlik desteğini](./concepts-managed-identity.md) gözden geçirme

* [Mevcut bir Azure Service Fabric kümesinde yönetilen kimlik desteğini etkinleştir](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Sistem tarafından atanan yönetilen kimlik ile Azure Service Fabric uygulaması dağıtma](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Kullanıcı tarafından atanan yönetilen kimlik ile bir Azure Service Fabric uygulaması dağıtma](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Hizmet kodundan Service Fabric uygulamasının yönetilen kimliğinden yararlanın](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric uygulamasına diğer Azure kaynaklarına erişim izni verme](./how-to-grant-access-other-resources.md)