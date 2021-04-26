---
title: Yalnızca Azure Cosmos DB ile veri işlemlerine Kullanıcı erişimini kısıtlama
description: Yalnızca Azure Cosmos DB ile veri işlemlerine erişimi nasıl kısıtlayacağınızı öğrenin
author: voellm
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 61e8ee2cbcd1252704b2d1e220fcfaaff17c2f28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93085998"
---
# <a name="restrict-user-access-to-data-operations-in-azure-cosmos-db"></a>Azure Cosmos DB veri işlemlerine Kullanıcı erişimini kısıtlama
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB, veritabanı hizmetiyle etkileşimlerinizi doğrulamak için iki yol vardır:

- Azure portal etkileşim kurarken Azure Active Directory kimliğinizi kullanarak
- API 'Ler ve SDK 'lardan çağrı verirken Azure Cosmos DB [anahtarlarını](database-security.md#primary-keys) veya [kaynak belirteçlerini](secure-access-to-data.md#resource-tokens) kullanma.

Her bir kimlik doğrulama yöntemi, bazı çakışmada farklı işlem kümelerine erişim sağlar:

:::image type="content" source="./media/how-to-restrict-user-data/operations.png" alt-text="Kimlik doğrulama türü başına işlem bölme" border="false":::

Bazı senaryolarda, kuruluşunuzun bazı kullanıcılarını veri işlemlerini (yani CRUD istekleri ve sorguları) gerçekleştirmeye kısıtlamak isteyebilirsiniz. Bu genellikle, kaynak oluşturmak veya silmek zorunda olmayan geliştiriciler veya üzerinde çalıştıkları kapsayıcıların sağlanan verimini değiştirmek zorunda değildir.

Aşağıdaki adımları uygulayarak erişimi kısıtlayabilirsiniz:
1. Erişimi kısıtlamak istediğiniz kullanıcılar için özel bir Azure Active Directory rolü oluşturma. Özel Active Directory rolü, Azure Cosmos DB [ayrıntılı eylemleri](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)kullanılarak işlemler için ayrıntılı erişim düzeyine sahip olmalıdır.
1. Anahtarlarla veri olmayan işlemlerin yürütülmesine izin vermeme. Bu işlemleri yalnızca Azure Resource Manager çağrıları ile sınırlayarak elde edebilirsiniz.

Bu makalenin sonraki bölümlerinde, bu adımların nasıl gerçekleştirileceği gösterilmektedir.

> [!NOTE]
> Komutları sonraki bölümlerde yürütmek için, değiştirmeye çalıştığınız abonelikte Azure PowerShell Module 3.0.0 veya sonraki bir sürümü ve [Azure Owner rolünü](../role-based-access-control/built-in-roles.md#owner) yüklemeniz gerekir.

Sonraki bölümlerdeki PowerShell betiklerine aşağıdaki yer tutucuları ortamınıza özgü değerlerle değiştirin:
- `$MySubscriptionId` -İzinleri sınırlandırmak istediğiniz Azure Cosmos hesabını içeren abonelik KIMLIĞI. Örneğin: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName` -Azure Cosmos hesabını içeren kaynak grubu. Örneğin: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName` -Azure Cosmos hesabınızın adı. Örneğin: `mycosmosdbsaccount`.
- `$MyUserName` -Erişimini sınırlamak istediğiniz kullanıcının oturum açması ( username@domain ). Örneğin: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Azure aboneliğinizi seçin

Azure PowerShell komutlar, komutları yürütmek için oturum açmanızı ve aboneliği seçmenizi gerektirir:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Özel Azure Active Directory rolünü oluşturma

Aşağıdaki betik, Azure Cosmos hesapları için "yalnızca anahtar" erişimine sahip Azure Active Directory bir rol ataması oluşturur. Rol, [Azure özel rollerine](../role-based-access-control/custom-roles.md) ve [Azure Cosmos DB yönelik ayrıntılı eylemlere](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)dayalıdır. Bu roller ve eylemler `Microsoft.DocumentDB` Azure Active Directory ad alanının bir parçasıdır.

1. İlk olarak, aşağıdaki içerikle adlı bir JSON belgesi oluşturun `AzureCosmosKeyOnlyAccess.json` :

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. Rol atamasını oluşturmak ve kullanıcıya atamak için aşağıdaki komutları çalıştırın:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Veri olmayan işlemlerin yürütülmesine izin verme

Aşağıdaki komutlar, anahtarları şu şekilde kullanma yeteneğini kaldırır:
- kaynakları oluşturma, değiştirme veya silme
- kapsayıcı ayarlarını güncelleştirin (Dizin oluşturma ilkeleri, verimlilik vs. dahil).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Sonraki adımlar

- [Cosmos DB rol tabanlı erişim denetimi](role-based-access-control.md) hakkında daha fazla bilgi edinin
- [Cosmos DB verilerine güvenli erişim](secure-access-to-data.md) hakkında genel bakış alın
