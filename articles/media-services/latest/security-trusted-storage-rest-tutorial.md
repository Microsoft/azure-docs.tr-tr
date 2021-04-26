---
title: Güvenilen depolama Azure Media Services
description: Bu öğreticide, Azure Media Services için güvenilen depolamayı nasıl etkinleştireceğinizi, güvenilen depolama için güvenilen kimlikler kullanacağınızı ve güvenlik duvarı ya da VPN kullanırken bir depolama hesabına erişmek için Azure hizmetleri vermenizi öğreneceksiniz.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 2/8/2021
ms.openlocfilehash: f076381333457c9ba2fd4325fa8aa7baad5d8a5b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282389"
---
# <a name="tutorial-media-services-trusted-storage"></a>Öğretici: güvenilen depolama Media Services

Bu öğreticide şunları öğreneceksiniz:

> [!div class="checklist"]
> - Azure Media Services için güvenilen depolamayı etkinleştirme
> - Güvenilen depolama için Yönetilen kimlikler kullanma
> - Bir güvenlik duvarı ya da VPN gibi ağ erişim denetimi kullanırken bir depolama hesabına erişim için Azure hizmetleri verme

2020-05-01 API 'SI ile yönetilen bir kimliği Media Services hesabıyla ilişkilendirerek güvenilen depolamayı etkinleştirebilirsiniz.

>[!NOTE]
>Güvenilen depolama yalnızca API 'de kullanılabilir ve Azure portal Şu anda etkin değil.

Media Services, sistem kimlik doğrulamasını kullanarak depolama hesabınıza otomatik olarak erişebilir. Media Services, Media Services hesabının ve depolama hesabının aynı abonelikte olduğunu doğrular. Ayrıca, ilişkilendirmeyi ekleyen kullanıcının depolama hesabına Azure Resource Manager RBAC ile erişebileceğini de doğrular.

Ancak, depolama hesabınızı güvenli hale getirmek ve güvenilen depolamayı etkinleştirmek için ağ erişim denetimi 'ni kullanmak istiyorsanız, [Yönetilen kimlikler](concept-managed-identities.md) kimlik doğrulaması gerekir. Media Services, güvenilen depolama erişimi aracılığıyla bir güvenlik duvarı veya VNet kısıtlaması ile yapılandırılmış depolama hesabına erişmesini sağlar.

## <a name="overview"></a>Genel Bakış

> [!IMPORTANT]
> Media Services için tüm istekler için 2020-05-01 API 'sini kullanın.

Media Services için güvenilen depolama oluşturmak için genel adımlar şunlardır:

1. Bir kaynak grubu oluşturun.
1. Depolama hesabı oluşturma.
1. Depolama hesabını, başlamaya kadar yoklayın. Depolama hesabı hazırsa, istek hizmet sorumlusu KIMLIĞINI döndürür.
1. *Depolama Blobu veri katılımcısı* rolünün kimliğini bulun.
1. Yetkilendirme sağlayıcısını çağırın ve bir rol ataması ekleyin.
1. Yönetilen kimlik kullanarak depolama hesabının kimliğini doğrulamak için Media Services hesabını güncelleştirin.
1. Bunları kullanmaya ve ücretlendirmeye devam etmek istemiyorsanız kaynakları silin.

<!-- Link to storage role contributor role access differences information -->

## <a name="prerequisites"></a>Önkoşullar

Başlamak için bir Azure aboneliğine sahip olmanız gerekir.  Azure aboneliğiniz yoksa [ücretsiz bir deneme hesabı oluşturun](https://azure.microsoft.com/free/).

### <a name="get-your-tenant-id-and-subscription-id"></a>Kiracı KIMLIĞINIZI ve abonelik KIMLIĞINIZI alın

Kiracı KIMLIĞINIZ ve abonelik KIMLIĞINIZI nasıl alacağınız hakkında bilgi sahibi değilseniz, bkz. [KIRACı kimliğinizi bulma](setup-azure-tenant-how-to.md) ve [kiracı kimliğinizi bulma](setup-azure-tenant-how-to.md).

### <a name="create-a-service-principal-and-secret"></a>Hizmet sorumlusu ve gizli dizi oluşturma

Hizmet sorumlusu ve gizli dizi oluşturmayı bilmiyorsanız bkz. [MEDIA SERVICES API 'sine erişmek için kimlik bilgilerini alma](access-api-howto.md).

## <a name="use-a-rest-client"></a>REST istemcisi kullanma

Bu betik, Visual Studio Code uzantılarında kullanılabilir olan gibi bir REST istemcisiyle kullanılmak üzere tasarlanmıştır.  Geliştirme ortamınız için uyarlayın.

## <a name="set-initial-variables"></a>Başlangıç değişkenlerini ayarla

Betiğin bu bölümü bir REST istemcisinde kullanım içindir. Değişkenleri, geliştirme ortamınızda farklı şekilde kullanabilirsiniz.

```rest
### AAD details
@tenantId = your tenant ID
@servicePrincipalId = the service principal ID
@servicePrincipalSecret = the service principal secret

### AAD resources
@armResource = https%3A%2F%2Fmanagement.core.windows.net%2F
@graphResource = https%3A%2F%2Fgraph.windows.net%2F
@storageResource = https%3A%2F%2Fstorage.azure.com%2F

### Service endpoints
@armEndpoint = management.azure.com
@graphEndpoint = graph.windows.net
@aadEndpoint = login.microsoftonline.com

### ARM details
@subscription = your subscription id
@resourceGroup = the resource group you'll be creating
@storageName = the name of the storage you'll be creating
@accountName = the name of the account you'll be creating
@resourceLocation = East US (or the location that works best for your region)
```

## <a name="get-a-token-for-azure-resource-manager"></a>Azure Resource Manager için belirteç al

```rest
// @name getArmToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{armResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-a-token-for-the-graph-api"></a>Graph API için bir belirteç alın

Betiğin bu bölümü bir REST istemcisinde kullanım içindir. Değişkenleri, geliştirme ortamınızda farklı şekilde kullanabilirsiniz.

```rest
// @name getGraphToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{graphResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-the-service-principal-details"></a>Hizmet sorumlusu ayrıntılarını al

```rest
// @name getServicePrincipals
GET https://{{graphEndpoint}}/{{tenantId}}/servicePrincipals?$filter=appId%20eq%20'{{servicePrincipalId}}'&api-version=1.6
x-ms-client-request-id: cae3e4f7-17a0-476a-a05a-0dab934ba959
Authorization:  Bearer {{getGraphToken.response.body.access_token}}
```

## <a name="store-the-service-principal-id"></a>Hizmet sorumlusu KIMLIĞINI depola

```rest
@servicePrincipalObjectId = {{getServicePrincipals.response.body.value[0].objectId}}
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

```rest
// @name createResourceGroup
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}
    ?api-version=2016-09-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "location": "{{resourceLocation}}"
}
```

## <a name="create-storage-account"></a>Depolama hesabı oluştur

```rest
// @name createStorageAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
    }
}
```

## <a name="get-the-storage-account-status"></a>Depolama hesabı durumunu al

Bu istek, bu isteğin durumunu yokladığı için, depolama hesabının hazırlanmaya devam edecek.  Depolama hesabı hazırlanana kadar bu isteği tekrarlayın.

```rest
// @name getStorageAccountStatus
GET {{createStorageAccount.response.headers.Location}}
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-the-storage-account-details"></a>Depolama hesabı ayrıntılarını al

Depolama hesabı hazırlandığınızda, depolama hesabının özelliklerini alın.

```rest
// @name getStorageAccount
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-a-token-for-arm"></a>ARM için belirteç al

```rest
// @name getStorageToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{storageResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="create-a-media-services-account-with-a-system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimlik ile Media Services hesabı oluşturma

```rest
// @name createMediaServicesAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="get-the-storage-storage-blob-data-role-definition"></a>Depolama alanı blob verileri rol tanımını al

```rest
// @name getStorageBlobDataContributorRoleDefinition
GET https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions?$filter=roleName%20eq%20%27Storage%20Blob%20Data%20Contributor%27&api-version=2015-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

### <a name="set-the-storage-role-assignment"></a>Depolama rolü atamasını ayarlama

Rol ataması, Media Services hesabı için hizmet sorumlusunun depolama rolü *Depolama Blobu verilerinin katkıda* bulunduğunu belirtir.  Bu işlem biraz zaman alabilir ve bunun beklenmesi veya Media Services hesabının doğru ayarlanmaması önemlidir.

```rest
PUT https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleAssignments/{{$guid}}?api-version=2020-04-01-preview
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "roleDefinitionId": "/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions/{{getStorageBlobDataContributorRoleDefinition.response.body.value[0].name}}",
    "principalId": "{{createMediaServicesAccount.response.body.identity.principalId}}"
  }
}
```

## <a name="give-managed-identity-bypass-access-to-the-storage-account"></a>Depolama hesabına yönetilen kimlik atlama erişimi verme

Bu eylem, sistem tarafından yönetilen kimliğin yönetilen kimliğe erişimini değiştirir. Bu şekilde, depolama hesabı, Azure Hizmetleri, IP erişim kurallarından (ACL 'Ler) bağımsız olarak depolama hesabına erişebildiğine yönelik bir güvenlik duvarı üzerinden depolama hesabına erişebilir.

Yeniden, rol depolama hesabına atanana kadar bekleyin veya Media Services hesabı yanlış ayarlanır.

```rest
// @name setStorageAccountFirewall
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
      "minimumTlsVersion": "TLS1_2",
      "networkAcls": {
        "bypass": "AzureServices",
        "virtualNetworkRules": [],
        "ipRules": [],
        "defaultAction": "Deny"
      }
    }
}
```

## <a name="update-the-media-services-account-to-use-the-managed-identity"></a>Media Services hesabını yönetilen kimliği kullanacak şekilde güncelleştirin

Depolama rolü atamasının yayılması birkaç dakika sürebilmesi için bu isteğin birkaç kez yeniden denenmesi gerekebilir.

```rest
// @name updateMediaServicesAccountWithManagedStorageAuth
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "storageAuthentication": "ManagedIdentity",
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="test-access"></a>Test erişimi

Depolama hesabında bir varlık oluşturarak erişimi test edin.

```rest
// @name createAsset
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}/assets/testasset{{index}}withoutmi?api-version=2018-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
}
```

## <a name="delete-resources"></a>Kaynakları silme

Oluşturduğunuz kaynakları tutmak ve ücretlendirmeye devam etmek istemiyorsanız, bunları silin.

```rest
### Clean up the Storage account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="next-steps"></a>Sonraki adımlar

[Varlık oluşturma](asset-create-asset-how-to.md)
