---
title: Azure Cosmos DB hesabınız için Azure AD ile yönetilen kimlikler yapılandırma
description: Azure Cosmos DB hesabınız için Azure Active Directory yönetilen kimlikleri nasıl yapılandıracağınızı öğrenin
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/02/2021
ms.author: thweiss
ms.openlocfilehash: 30efaed09a400611861bdd3adeae1f650054b405
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231614"
---
# <a name="configure-managed-identities-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Azure Cosmos DB hesabınız için Azure Active Directory Yönetilen kimlikler yapılandırma
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory ' de otomatik olarak yönetilen kimlik ile Azure hizmetleri sağlar. Bu makalede, Azure Cosmos DB hesapları için yönetilen kimlik oluşturma gösterilmektedir.

> [!NOTE]
> Şu anda Azure Cosmos DB tarafından yalnızca sistem tarafından atanan Yönetilen kimlikler desteklenir.

## <a name="prerequisites"></a>Önkoşullar

- Azure kaynakları için Yönetilen kimlikler hakkında bilgi sahibi değilseniz bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../active-directory/managed-identities-azure-resources/overview.md). Yönetilen kimlik türleri hakkında bilgi edinmek için bkz. [yönetilen kimlik türleri](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).
- Yönetilen kimlikleri ayarlamak için hesabınızın [DocumentDB hesabı katılımcısı rolüne](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)sahip olması gerekir.

## <a name="add-a-system-assigned-identity"></a>Sistem tarafından atanan kimlik ekleme

### <a name="using-an-azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM) şablonu kullanma

> [!IMPORTANT]
> `apiVersion` `2021-03-15` Yönetilen kimliklerle çalışırken veya daha yüksek bir sürümünü kullandığınızdan emin olun.

Yeni veya mevcut bir Azure Cosmos DB hesapta sistem tarafından atanan bir kimliği etkinleştirmek için, kaynak tanımına aşağıdaki özelliği ekleyin:

```json
"identity": {
    "type": "SystemAssigned"
}
```

`resources`ARM şablonunuzun bölümü aşağıdaki gibi görünmelidir:

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "SystemAssigned"
        },
        // ...
    },
    // ...
 ]
```

Azure Cosmos DB Hesabınız oluşturulduktan veya güncelleştirildikten sonra, aşağıdaki özelliği gösterir:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kaynakları için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) hakkında daha fazla bilgi edinin
- [Azure Cosmos DB 'de müşteri tarafından yönetilen anahtarlar](how-to-setup-cmk.md) hakkında daha fazla bilgi edinin
