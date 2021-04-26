---
title: REST kullanarak Kullanıcı tarafından atanan yönetilen kimlikleri yönetme-Azure AD
description: REST API çağrısı yapmak için Kullanıcı tarafından atanan yönetilen kimlik oluşturma, listeleme ve silme hakkında adım adım yönergeler.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 117f9a1c173f2083dd4621f4f3f41b6e83d1d46b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546701"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>REST API çağrılarını kullanarak Kullanıcı tarafından atanan yönetilen kimlik oluşturma, listeleme veya silme

Azure kaynakları için Yönetilen kimlikler, Azure hizmetlerine, kodunuzda kimlik bilgileri gerekmeden Azure AD kimlik doğrulamasını destekleyen hizmetlerde kimlik doğrulaması yapma yeteneği sağlar. 

Bu makalede, REST API çağrısı yapmak için KıVRıMLı kullanarak Kullanıcı tarafından atanan yönetilen kimlik oluşturmayı, listeleyeceğinizi ve silmeyi öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

- Azure kaynakları için Yönetilen kimlikler hakkında bilginiz varsa [genel bakış bölümüne](overview.md)bakın. **[Sistem tarafından atanan ve Kullanıcı tarafından atanan yönetilen kimlik arasındaki farkı](overview.md#managed-identity-types)gözden geçirdiğinizden emin** olun.
- Henüz bir Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).
- Bu makaledeki tüm komutları bulutta ya da yerel olarak çalıştırabilirsiniz:
    - Bulutta çalıştırmak için [Azure Cloud Shell](../../cloud-shell/overview.md)kullanın.
    - Yerel olarak çalıştırmak için, [kıvrımlı](https://curl.haxx.se/download.html) ve [Azure CLI](/cli/azure/install-azure-cli)'yi çalıştırın.

## <a name="obtain-a-bearer-access-token"></a>Bir taşıyıcı erişim belirteci edinme

1. Yerel olarak çalışıyorsa Azure CLı aracılığıyla Azure 'da oturum açın:

    ```
    az login
    ```

1. [Az Account Get-Access-Token](/cli/azure/account#az_account_get_access_token) kullanarak bir erişim belirteci alın

    ```azurecli-interactive
    az account get-access-token
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik oluşturma 

Kullanıcı tarafından atanan yönetilen kimlik oluşturmak için hesabınıza [yönetilen kimlik katılımcısı](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) rol ataması gerekir.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**İstek üst bilgileri**

|İstek üst bilgisi  |Açıklama  |
|---------|---------|
|*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
|*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.        |

**İstek gövdesi**

|Ad  |Açıklama  |
|---------|---------|
|location     | Gereklidir. Kaynak konumu.        |

## <a name="list-user-assigned-managed-identities"></a>Kullanıcı tarafından atanan yönetilen kimlikleri listeleme

Kullanıcı tarafından atanan yönetilen kimliği listelemek/okumak için hesabınızın [yönetilen kimlik işleci](../../role-based-access-control/built-in-roles.md#managed-identity-operator) veya [yönetilen kimlik katılımcısı](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) rolü ataması gerekir.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|İstek üst bilgisi  |Açıklama  |
|---------|---------|
|*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
|*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimliği silme

Kullanıcı tarafından atanan bir yönetilen kimliği silmek için hesabınıza [yönetilen kimlik katılımcısı](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) rol ataması gerekir.

> [!NOTE]
> Kullanıcı tarafından atanan bir yönetilen kimliğin silinmesi, atandığı herhangi bir kaynaktaki başvuruyu kaldırmaz. Bir VM 'den Kullanıcı tarafından atanan yönetilen kimliği, KıVRıMLı kullanarak kaldırmak için bkz. [Azure VM 'den Kullanıcı tarafından atanan kimliği kaldırma](qs-configure-rest-vm.md#remove-a-user-assigned-managed-identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|İstek üst bilgisi  |Açıklama  |
|---------|---------|
|*İçerik türü*     | Gereklidir. `application/json` olarak ayarlayın.        |
|*Yetkilendirme*     | Gereklidir. Geçerli bir `Bearer` erişim belirtecine ayarlayın.        |

## <a name="next-steps"></a>Sonraki adımlar

Kullanıcı tarafından atanan yönetilen kimliği, bir Azure VM/VMSS 'yi kullanarak atama hakkında daha fazla bilgi için bkz. [bir Azure VM üzerinde Azure kaynakları için Yönetilen kimlikler yapılandırma REST API çağırır](qs-configure-rest-vm.md#user-assigned-managed-identity) ve [REST API çağrılarını kullanarak bir sanal makine ölçek kümesindeki Azure kaynakları için Yönetilen kimlikler](qs-configure-rest-vmss.md#user-assigned-managed-identity)yapılandırma.
