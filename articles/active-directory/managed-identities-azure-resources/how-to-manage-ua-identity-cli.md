---
title: Kullanıcı tarafından atanan yönetilen kimliği yönetme-Azure CLı-Azure AD
description: Azure CLı kullanarak Kullanıcı tarafından atanan yönetilen kimlik oluşturma, listeleme ve silme hakkında adım adım yönergeler.
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
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: a26f13b71ae96f4d6593cb4a4d9107f8ef6c207c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784884"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Azure CLı kullanarak Kullanıcı tarafından atanan yönetilen kimlik oluşturma, listeleme veya silme


Azure kaynakları için Yönetilen kimlikler, Azure Active Directory yönetilen bir kimlik ile Azure hizmetleri sağlar. Kodunuzda kimlik bilgileri gerekmeden Azure AD kimlik doğrulamasını destekleyen hizmetlere kimlik doğrulaması yapmak için bu kimliği kullanabilirsiniz. 

Bu makalede, Azure CLı kullanarak Kullanıcı tarafından atanan yönetilen kimlik oluşturmayı, listeleyeceğinizi ve silmeyi öğreneceksiniz.

Henüz bir Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

- Azure kaynakları için Yönetilen kimlikler hakkında bilgi sahibi değilseniz bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](overview.md). Sistem tarafından atanan ve Kullanıcı tarafından atanan yönetilen kimlik türleri hakkında bilgi edinmek için bkz. [yönetilen kimlik türleri](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!NOTE]   
> CLı kullanarak bir App Service sorumlusu kullanırken Kullanıcı izinlerini değiştirmek için, CLı 'nın Graph API karşı GET istekleri gerçekleştirmesini sağlamak üzere Azure AD Graph API 'de hizmet sorumlusu ek izinleri sağlamanız gerekir. Aksi takdirde, ' işlemi bitirmek için yeterli ayrıcalıklara sahip ' iletisini alabilirsiniz. Bunu yapmak için Azure Active Directory 'de uygulama kaydına gitmeniz gerekir, uygulamanızı seçin, API izinleri ' ne tıklayın, aşağı kaydırın ve Azure Active Directory Graf ' ı seçin. Uygulama izinlerini seçin ve ardından uygun izinleri ekleyin. 

## <a name="create-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik oluşturma 

Kullanıcı tarafından atanan yönetilen kimlik oluşturmak için hesabınıza [yönetilen kimlik katılımcısı](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) rol ataması gerekir.

Kullanıcı tarafından atanan yönetilen kimlik oluşturmak için [az Identity Create](/cli/azure/identity#az_identity_create) komutunu kullanın. `-g`Parametresi, Kullanıcı tarafından atanan yönetilen kimliğin oluşturulacağı kaynak grubunu belirtir ve `-n` parametresi adını belirtir. `<RESOURCE GROUP>`Ve `<USER ASSIGNED IDENTITY NAME>` parametre değerlerini kendi değerlerinizle değiştirin:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Kullanıcı tarafından atanan yönetilen kimlikleri listeleme

Kullanıcı tarafından atanan yönetilen kimliği listelemek/okumak için hesabınızın [yönetilen kimlik işleci](../../role-based-access-control/built-in-roles.md#managed-identity-operator) veya [yönetilen kimlik katılımcısı](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) rolü ataması gerekir.

Kullanıcı tarafından atanan yönetilen kimlikleri listelemek için [az Identity List](/cli/azure/identity#az_identity_list) komutunu kullanın. `<RESOURCE GROUP>`Değerini kendi değeri ile değiştirin:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

JSON yanıtında, Kullanıcı tarafından atanan yönetilen kimliklerin `"Microsoft.ManagedIdentity/userAssignedIdentities"` anahtar için döndürülen değeri vardır `type` .

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimliği silme

Kullanıcı tarafından atanan bir yönetilen kimliği silmek için hesabınıza [yönetilen kimlik katılımcısı](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) rol ataması gerekir.

Kullanıcı tarafından atanan bir yönetilen kimliği silmek için [az Identity Delete](/cli/azure/identity#az_identity_delete) komutunu kullanın.  -N parametresi adını belirtir ve-g parametresi, Kullanıcı tarafından atanan yönetilen kimliğin oluşturulduğu kaynak grubunu belirtir. `<USER ASSIGNED IDENTITY NAME>`Ve `<RESOURCE GROUP>` parametrelerinin değerlerini kendi değerlerinizle değiştirin:

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Kullanıcı tarafından atanan bir yönetilen kimliğin silinmesi başvuruyu, atandığı herhangi bir kaynaktan kaldırmaz. Lütfen komutunu kullanarak VM/VMSS 'den bunları kaldırın `az vm/vmss identity remove`

## <a name="next-steps"></a>Sonraki adımlar

Azure CLı kimlik komutlarının tam listesi için, bkz. [az Identity](/cli/azure/identity).

Azure VM 'ye Kullanıcı tarafından atanan yönetilen kimlik atama hakkında daha fazla bilgi için bkz. Azure [CLI kullanarak Azure VM 'de Azure kaynakları için Yönetilen kimlikler yapılandırma](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)
