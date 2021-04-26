---
title: Yönetilen kimlikler
description: Yönetilen kimliklerin Azure App Service ve Azure Işlevlerinde nasıl çalıştığını, yönetilen bir kimliği yapılandırmayı ve arka uç kaynağı için bir belirteç oluşturmayı öğrenin.
author: mattchenderson
ms.topic: article
ms.date: 05/27/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: badc6b6f1b45938e950ffadeefe30d81ed383440
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762452"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>App Service ve Azure Işlevleri için Yönetilen kimlikler kullanma

Bu konu, App Service ve Azure Işlevleri uygulamaları için yönetilen bir kimlik oluşturmayı ve diğer kaynaklara erişmek için nasıl kullanılacağını gösterir. 

> [!Important] 
> App Service ve Azure Işlevleri için Yönetilen kimlikler, uygulamanız abonelikler/kiracılar arasında geçirilirse beklendiği gibi davranır. Uygulamanın, özelliği devre dışı bırakıp yeniden etkinleştirerek gerçekleştirilen yeni bir kimlik alması gerekir. Aşağıdaki [kimliği kaldırma](#remove) bölümüne bakın. Aşağı akış kaynakları, yeni kimliği kullanmak için erişim ilkelerinin güncelleştirilmesini de gerekir.

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

## <a name="add-a-system-assigned-identity"></a>Sistem tarafından atanan kimlik ekleme

Sistem tarafından atanan kimlik ile uygulama oluşturmak, uygulamada ek bir özellik ayarlanmasını gerektirir.

### <a name="using-the-azure-portal"></a>Azure portalını kullanma

Portalda yönetilen bir kimlik ayarlamak için öncelikle normal olarak bir uygulama oluşturun ve ardından özelliği etkinleştirmeniz gerekir.

1. Portalda genellikle yaptığınız gibi bir uygulama oluşturun. Portalda bu sayfaya gidin.

2. Bir işlev uygulaması kullanıyorsanız, **platform özellikleri**' ne gidin. Diğer uygulama türleri için, sol gezinti bölmesinde **Ayarlar** grubuna gidin.

3. **Kimlik** seçin.

4. **Sistem atandı** sekmesinde **durumu** **Açık** olarak değiştirin. **Kaydet**’e tıklayın.

    ![Durumun açık olduğunu gösteren ekran görüntüsü ve sonra Kaydet ' i seçin.](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)


> [!NOTE] 
> Web uygulamanız veya yuva uygulamanız için yönetilen kimliği Azure portal, **Kurumsal uygulamalar** altında, **Kullanıcı ayarları** bölümüne bakın. Genellikle yuva adı öğesine benzerdir `<app name>/slots/<slot name>` .


### <a name="using-the-azure-cli"></a>Azure CLI'yı kullanma

Azure CLı kullanarak yönetilen bir kimlik ayarlamak için, `az webapp identity assign` mevcut bir uygulamada komutunu kullanmanız gerekir. Bu bölümde örnekleri çalıştırmak için üç seçeneğiniz vardır:

- Azure portal [Azure Cloud Shell](../cloud-shell/overview.md) kullanın.
- Aşağıdaki her kod bloğunun sağ üst köşesinde bulunan "dene" düğmesini kullanarak katıştırılmış Azure Cloud Shell kullanın.
- Yerel bir CLı konsolu kullanmayı tercih ediyorsanız, [en son Azure CLI sürümünü](/cli/azure/install-azure-cli) (2.0.31 veya üzeri) yükleyebilirsiniz. 

Aşağıdaki adımlar, bir Web uygulaması oluşturma ve CLı kullanarak bir kimlik atama işleminde size kılavuzluk eder:

1. Azure CLI'yi yerel bir konsolda kullanıyorsanız, önce [az login](/cli/azure/reference-index#az_login) kullanarak Azure'da oturum açın. Uygulamayı dağıtmak istediğiniz Azure aboneliğiyle ilişkili bir hesabı kullanın:

    ```azurecli-interactive
    az login
    ```

2. CLı kullanarak bir Web uygulaması oluşturun. CLı 'yı App Service ile kullanma hakkında daha fazla örnek için bkz. [App SERVICE CLI örnekleri](../app-service/samples-cli.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. `identity assign`Bu uygulamanın kimliğini oluşturmak için komutunu çalıştırın:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aşağıdaki adımlar, bir uygulama oluşturmak ve Azure PowerShell kullanarak bir kimlik atamak için size yol gösterecektir. Bir Web uygulaması ve bir işlev uygulaması oluşturma yönergeleri farklıdır.

#### <a name="using-azure-powershell-for-a-web-app"></a>Bir Web uygulaması için Azure PowerShell kullanma

1. Gerekirse, [Azure PowerShell kılavuzunda](/powershell/azure/)bulunan yönergeleri kullanarak Azure PowerShell yükleyip `Login-AzAccount` Azure ile bağlantı oluşturmak için öğesini çalıştırın.

2. Azure PowerShell kullanarak bir Web uygulaması oluşturun. App Service Azure PowerShell kullanma hakkında daha fazla örnek için bkz. [App Service PowerShell örnekleri](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $resourceGroupName -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $resourceGroupName
    ```

3. `Set-AzWebApp -AssignIdentity`Bu uygulamanın kimliğini oluşturmak için komutunu çalıştırın:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName $resourceGroupName 
    ```

#### <a name="using-azure-powershell-for-a-function-app"></a>İşlev uygulaması için Azure PowerShell kullanma

1. Gerekirse, [Azure PowerShell kılavuzunda](/powershell/azure/)bulunan yönergeleri kullanarak Azure PowerShell yükleyip `Login-AzAccount` Azure ile bağlantı oluşturmak için öğesini çalıştırın.

2. Azure PowerShell kullanarak bir işlev uygulaması oluşturun. Azure Işlevleri ile Azure PowerShell kullanma hakkında daha fazla örnek için, [az. Functions başvurusuna](/powershell/module/az.functions/#functions)bakın:

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a function app with a system-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType SystemAssigned
    ```

Bunun yerine, var olan bir işlev uygulamasını da güncelleştirebilirsiniz `Update-AzFunctionApp` .

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanma

Azure Resource Manager şablonu, Azure kaynaklarınızın dağıtımını otomatikleştirmek için kullanılabilir. App Service ve Işlevlerine dağıtma hakkında daha fazla bilgi edinmek için bkz. [App Service kaynak dağıtımını otomatikleştirme](../app-service/deploy-complex-application-predictably.md) ve [Azure Işlevlerinde kaynak dağıtımını otomatikleştirme](../azure-functions/functions-infrastructure-as-code.md).

`Microsoft.Web/sites`Kaynak tanımına aşağıdaki özelliği ekleyerek, herhangi bir kaynak türü bir kimlikle oluşturulabilir:

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Bir uygulama aynı anda hem sistem tarafından hem de Kullanıcı tarafından atanan kimliklere sahip olabilir. Bu durumda, `type` özelliği `SystemAssigned,UserAssigned`

Sistem tarafından atanan tür eklendiğinde Azure, uygulamanız için kimlik oluşturma ve yönetme konusunda sizi söyler.

Örneğin, bir Web uygulaması aşağıdaki gibi görünebilir:

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Site oluşturulduğunda, aşağıdaki ek özelliklere sahiptir:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Tenantıd özelliği, kimliğin ait olduğu Azure AD kiracısını tanımlar. PrincipalId, uygulamanın yeni kimliği için benzersiz bir tanımlayıcıdır. Azure AD 'de hizmet sorumlusu, App Service veya Azure Işlevleri örneğine verdiğiniz aynı ada sahiptir.

Bu özelliklere şablonda daha sonraki bir aşamada başvuru yapmanız gerekiyorsa, bu örnekte olduğu gibi bayrağıyla birlikte [ `reference()` şablon işlevi](../azure-resource-manager/templates/template-functions-resource.md#reference) aracılığıyla bunu yapabilirsiniz `'Full'` :

```json
{
    "tenantId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.tenantId]",
    "objectId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.principalId]",
}
```

## <a name="add-a-user-assigned-identity"></a>Kullanıcı tarafından atanan kimlik ekleme

Kullanıcı tarafından atanan kimlik ile uygulama oluşturmak için kimlik oluşturmanız ve ardından kaynak tanımlayıcısını uygulama yapılandırmaya eklemeniz gerekir.

### <a name="using-the-azure-portal"></a>Azure portalını kullanma

İlk olarak, Kullanıcı tarafından atanan bir kimlik kaynağı oluşturmanız gerekir.

1. [Bu yönergelere](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)göre Kullanıcı tarafından atanan bir yönetilen kimlik kaynağı oluşturun.

2. Portalda genellikle yaptığınız gibi bir uygulama oluşturun. Portalda bu sayfaya gidin.

3. Bir işlev uygulaması kullanıyorsanız, **platform özellikleri**' ne gidin. Diğer uygulama türleri için, sol gezinti bölmesinde **Ayarlar** grubuna gidin.

4. **Kimlik** seçin.

5. **Kullanıcı atandı** sekmesinde **Ekle**' ye tıklayın.

6. Daha önce oluşturduğunuz kimliği arayın ve seçin. **Ekle**'ye tıklayın.

    ![App Service yönetilen kimliği](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aşağıdaki adımlar, bir uygulama oluşturmak ve Azure PowerShell kullanarak bir kimlik atamak için size yol gösterecektir.

> [!NOTE]
> Azure PowerShell Command'in geçerli sürümü, Azure App Service için Kullanıcı tarafından atanan kimlikleri desteklemez. Aşağıdaki yönergeler Azure Işlevleri içindir.

1. Gerekirse, [Azure PowerShell kılavuzunda](/powershell/azure/)bulunan yönergeleri kullanarak Azure PowerShell yükleyip `Login-AzAccount` Azure ile bağlantı oluşturmak için öğesini çalıştırın.

2. Azure PowerShell kullanarak bir işlev uygulaması oluşturun. Azure Işlevleri ile Azure PowerShell kullanma hakkında daha fazla örnek için, [az. Functions başvurusuna](/powershell/module/az.functions/#functions)bakın. Aşağıdaki komut dosyası Ayrıca, `New-AzUserAssignedIdentity` [Azure PowerShell kullanarak Kullanıcı tarafından atanan yönetilen kimlik oluşturma, listeleme veya silme](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)ile ayrı olarak yüklenmesi gereken öğesinin kullanımını da sağlar.

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create a function app with a user-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType UserAssigned -IdentityId $userAssignedIdentity.Id
    ```

Bunun yerine, var olan bir işlev uygulamasını da güncelleştirebilirsiniz `Update-AzFunctionApp` .

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanma

Azure Resource Manager şablonu, Azure kaynaklarınızın dağıtımını otomatikleştirmek için kullanılabilir. App Service ve Işlevlerine dağıtma hakkında daha fazla bilgi edinmek için bkz. [App Service kaynak dağıtımını otomatikleştirme](../app-service/deploy-complex-application-predictably.md) ve [Azure Işlevlerinde kaynak dağıtımını otomatikleştirme](../azure-functions/functions-infrastructure-as-code.md).

`Microsoft.Web/sites`Kaynak tanımına aşağıdaki blok eklenerek, `<RESOURCEID>` istenen KIMLIğIN kaynak kimliği ile değiştirilerek bir kimlik ile herhangi bir kaynak oluşturulabilir:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Bir uygulama aynı anda hem sistem tarafından hem de Kullanıcı tarafından atanan kimliklere sahip olabilir. Bu durumda, `type` özelliği `SystemAssigned,UserAssigned`

Kullanıcı tarafından atanan türü eklemek, Azure 'un uygulamanız için belirtilen kullanıcı tarafından atanan kimliğini kullanmasını söyler.

Örneğin, bir Web uygulaması aşağıdaki gibi görünebilir:

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

Site oluşturulduğunda, aşağıdaki ek özelliklere sahiptir:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

PrincipalId, Azure AD yönetimi için kullanılan kimlik için benzersiz bir tanımlayıcıdır. ClientID, uygulamanın çalışma zamanı çağrıları sırasında hangi kimliğin kullanılacağını belirtmek için kullanılan yeni kimliği için benzersiz bir tanımlayıcıdır.

## <a name="obtain-tokens-for-azure-resources"></a>Azure kaynakları için belirteçleri alma

Bir uygulama, Azure Key Vault gibi Azure AD tarafından korunan diğer kaynaklara erişmek için belirteçleri almak üzere yönetilen kimliğini kullanabilir. Bu belirteçler, uygulamanın belirli bir kullanıcısı değil, kaynağa erişen uygulamayı temsil eder. 

Uygulamanızdaki erişime izin vermek için hedef kaynağı yapılandırmanız gerekebilir. Örneğin, Key Vault erişmek için bir belirteç istemeniz durumunda uygulamanızın kimliğini içeren bir erişim ilkesi eklediğinizden emin olmanız gerekir. Aksi takdirde, belirteci içerse bile Key Vault çağrılarınız reddedilir. Azure Active Directory belirteçlerini destekleyen kaynaklar hakkında daha fazla bilgi edinmek için bkz. [Azure AD kimlik doğrulamasını destekleyen Azure hizmetleri](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

> [!IMPORTANT]
> Yönetilen kimlikler için arka uç Hizmetleri, yaklaşık 24 saat boyunca kaynak URI başına bir önbellek tutar. Belirli bir hedef kaynağın erişim ilkesini güncelleştirir ve bu kaynak için hemen bir belirteç alırsanız, belirtecin süresi dolana kadar eski izinlerle önbelleğe alınmış bir belirteç almaya devam edebilirsiniz. Şu anda belirteç yenilemeyi zorlamaya yönelik bir yol yoktur.

App Service ve Azure Işlevlerinde belirteç almak için basit bir REST Protokolü vardır. Bu, tüm uygulamalar ve diller için kullanılabilir. .NET ve Java için Azure SDK, bu protokol üzerinden bir soyutlama sağlar ve yerel bir geliştirme deneyimini kolaylaştırır.

### <a name="using-the-rest-protocol"></a>REST protokolünü kullanma

> [!NOTE]
> "2017-09-01" API sürümünü kullanarak bu protokolün daha eski bir sürümü, `secret` yerine üst bilgiyi kullandı `X-IDENTITY-HEADER` ve yalnızca `clientid` Kullanıcı tarafından atanan özelliği kabul eder. Ayrıca, `expires_on` bir zaman damgası biçiminde döndürülür. MSI_ENDPOINT, IDENTITY_ENDPOINT bir diğer ad olarak kullanılabilir ve MSI_SECRET IDENTITY_HEADER için bir diğer ad olarak kullanılabilir. Protokolün bu sürümü şu anda Linux tüketim barındırma planları için gereklidir.

Yönetilen kimliğe sahip bir uygulama tanımlı iki ortam değişkenine sahiptir:

- IDENTITY_ENDPOINT-yerel belirteç hizmeti URL 'SI.
- IDENTITY_HEADER-sunucu tarafı istek sahteciliğini önleme (ssrf) saldırılarını azaltmaya yardımcı olmak için kullanılan bir üst bilgi. Değer, platform tarafından döndürülür.

**IDENTITY_ENDPOINT** , uygulamanızın belirteç isteyebileceği yerel bir URL 'dir. Bir kaynağın belirtecini almak için, bu uç noktaya yönelik bir HTTP GET isteği oluşturun ve aşağıdaki parametreleri de dahil edin:

> | Parametre adı    | İçinde     | Description                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | kaynak          | Sorgu  | Belirtecin alınması gereken kaynağın Azure AD Kaynak URI 'SI. Bu, [Azure AD kimlik doğrulamasını](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) veya DIĞER Kaynak URI 'Yi destekleyen Azure hizmetlerinden biridir.    |
> | api-sürümü       | Sorgu  | Kullanılacak belirteç API 'sinin sürümü. Lütfen "2019-08-01" veya üstünü kullanın (Şu anda yalnızca "2017-09-01" sağlayan Linux tüketimini kullanmadıkça, Yukarıdaki nota bakın).                                                                                                                                                                                                                                                                 |
> | X-ıDENTITY-HEADER | Üst bilgi | IDENTITY_HEADER ortam değişkeninin değeri. Bu üst bilgi, sunucu tarafı istek sahteciliğini önleme (ssrf) saldırılarını azaltmaya yardımcı olmak için kullanılır.                                                                                                                                                                                                    |
> | client_id         | Sorgu  | Seçim Kullanılacak kullanıcı tarafından atanan kimliğin istemci KIMLIĞI. ,, Veya içeren bir istek üzerinde kullanılamaz `principal_id` `mi_res_id` `object_id` . Tüm kimlik parametreleri (, `client_id` , `principal_id` `object_id` ve `mi_res_id` ) atlanırsa, sistem tarafından atanan kimlik kullanılır.                                             |
> | principal_id      | Sorgu  | Seçim Kullanılacak kullanıcı tarafından atanan kimliğin asıl KIMLIĞI. `object_id` Bunun yerine kullanılabilecek bir diğer addır. Client_id, mi_res_id veya object_id içeren bir istekte kullanılamaz. Tüm kimlik parametreleri (, `client_id` , `principal_id` `object_id` ve `mi_res_id` ) atlanırsa, sistem tarafından atanan kimlik kullanılır. |
> | mi_res_id         | Sorgu  | Seçim Kullanılacak kullanıcı tarafından atanan kimliğin Azure Kaynak KIMLIĞI. ,, Veya içeren bir istek üzerinde kullanılamaz `principal_id` `client_id` `object_id` . Tüm kimlik parametreleri (, `client_id` , `principal_id` `object_id` ve `mi_res_id` ) atlanırsa, sistem tarafından atanan kimlik kullanılır.                                      |

> [!IMPORTANT]
> Kullanıcı tarafından atanan kimlikler için belirteçleri almaya çalışıyorsanız, isteğe bağlı özelliklerden birini eklemeniz gerekir. Aksi takdirde, belirteç hizmeti sistem tarafından atanan bir kimlik için belirteç edinmeye çalışır, bu da mevcut olabilir.

Başarılı bir 200 Tamam yanıtı, aşağıdaki özelliklere sahip bir JSON gövdesi içerir:

> | Özellik adı | Description                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | İstenen erişim belirteci. Çağıran Web hizmeti, alıcı Web hizmetinde kimlik doğrulaması yapmak için bu belirteci kullanabilir.                                                                                                                               |
> | client_id     | Kullanılan kimliğin istemci KIMLIĞI.                                                                                                                                                                                                       |
> | expires_on    | Erişim belirtecinin süresi dolduğu zaman aralığı. Tarih, "1970-01-01T0:0: 0Z UTC" (belirtecin talebine karşılık gelir) için saniye sayısı olarak gösterilir `exp` .                                                                                |
> | not_before    | Erişim belirteci yürürlüğe girer ve kabul edilebilir. Tarih, "1970-01-01T0:0: 0Z UTC" (belirtecin talebine karşılık gelir) için saniye sayısı olarak gösterilir `nbf` .                                                      |
> | kaynak      | İsteğin sorgu dizesi parametresiyle eşleşen erişim belirtecinin istendiği kaynak `resource` .                                                                                                                               |
> | token_type    | Belirteç türü değerini gösterir. Azure AD 'nin desteklediği tek tür taşıyıcı. Taşıyıcı belirteçleri hakkında daha fazla bilgi için bkz. [OAuth 2,0 yetkilendirme çerçevesi: taşıyıcı belirteç kullanımı (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |

Bu yanıt, [Azure AD hizmetten hizmete erişim belirteci isteğine yönelik yanıt](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)ile aynıdır.

### <a name="rest-protocol-examples"></a>REST protokol örnekleri

Örnek bir istek aşağıdakine benzeyebilir:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

Bir örnek yanıt aşağıdaki gibi görünebilir:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "1586984735",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer",
    "client_id": "5E29463D-71DA-4FE0-8E69-999B57DB23B0"
}
```

### <a name="code-examples"></a>Kod örnekleri

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> .NET dilleri için, bu isteği kendiniz taslağı yapmak yerine [Microsoft. Azure. Services. AppAuthentication](#asal) ' yi de kullanabilirsiniz.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2019-08-01", Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT"), resource));
    request.Headers.Add("X-IDENTITY-HEADER", Environment.GetEnvironmentVariable("IDENTITY_HEADER"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["IDENTITY_ENDPOINT"]}/?resource=${resource}&api-version=2019-08-01`,
        headers: {
            'X-IDENTITY-HEADER': process.env["IDENTITY_HEADER"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

identity_endpoint = os.environ["IDENTITY_ENDPOINT"]
identity_header = os.environ["IDENTITY_HEADER"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{identity_endpoint}?resource={resource_uri}&api-version=2019-08-01"
    head_msi = {'X-IDENTITY-HEADER':identity_header}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:IDENTITY_ENDPOINT + "?resource=$resourceURI&api-version=2019-08-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"X-IDENTITY-HEADER"="$env:IDENTITY_HEADER"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>.NET için Microsoft. Azure. Services. AppAuthentication kitaplığını kullanma

.NET uygulamaları ve işlevleri için, yönetilen bir kimlikle çalışmanın en kolay yolu Microsoft. Azure. Services. AppAuthentication paketi aracılığıyla yapılır. Bu kitaplık Ayrıca, Visual Studio, [Azure CLI](/cli/azure)veya Active Directory tümleşik kimlik doğrulaması için kullanıcı hesabınızı kullanarak kodunuzu geliştirme makinenizde yerel olarak sınamanızı sağlar. Bulutta barındırıldığında, varsayılan olarak sistem tarafından atanan bir kimlik kullanılır, ancak bu davranışı Kullanıcı tarafından atanan bir kimliğin istemci KIMLIĞINE başvuran bir bağlantı dizesi ortam değişkeni kullanarak özelleştirebilirsiniz. Bu kitaplıkla ilgili geliştirme seçenekleri hakkında daha fazla bilgi için [Microsoft. Azure. Services. AppAuthentication başvurusuna]bakın. Bu bölümde, kodunuzda kitaplığı kullanmaya nasıl başlacağınız gösterilmektedir.

1. Uygulamanıza [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) ve diğer gerekli NuGet paketlerine başvurular ekleyin. Aşağıdaki örnek [Microsoft. Azure. Keykasasını](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)de kullanır.

2. Aşağıdaki kodu uygulamanıza ekleyerek doğru kaynağı hedefleyin. Bu örnekte Azure Key Vault çalışmak için iki yol gösterilmektedir:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Kullanıcı tarafından atanan yönetilen kimlik kullanmak istiyorsanız, `AzureServicesAuthConnectionString` uygulama ayarını olarak ayarlayabilirsiniz `RunAs=App;AppId=<clientId-guid>` . `<clientId-guid>`Kullanmak istediğiniz kimliğin ISTEMCI kimliği ile değiştirin. Özel uygulama ayarlarını kullanarak ve değerlerini AzureServiceTokenProvider oluşturucusuna geçirerek, birden fazla bağlantı dizesi tanımlayabilirsiniz.

```csharp
    var identityConnectionString1 = Environment.GetEnvironmentVariable("UA1_ConnectionString");
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider(identityConnectionString1);
    
    var identityConnectionString2 = Environment.GetEnvironmentVariable("UA2_ConnectionString");
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider(identityConnectionString2);
```

AzureServiceTokenProvider ve sunduğu işlemleri yapılandırma hakkında daha fazla bilgi edinmek için, [MSI .net örneği Ile](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet) [Microsoft. Azure. Services. appauthentication başvurusu] ve App Service ve keykasası ' na bakın.

### <a name="using-the-azure-sdk-for-java"></a>Java için Azure SDK 'sını kullanma

Java uygulamaları ve işlevleri için, yönetilen bir kimlikle çalışmanın en kolay yolu, [Java Için Azure SDK](https://github.com/Azure/azure-sdk-for-java)'ıdır. Bu bölümde, kodunuzda kitaplığı kullanmaya nasıl başlacağınız gösterilmektedir.

1. [Azure SDK kitaplığına](https://mvnrepository.com/artifact/com.microsoft.azure/azure)bir başvuru ekleyin. Maven projeleri için, bu kod parçacığını `dependencies` projenin Pod dosyasının bölümüne ekleyebilirsiniz:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. `AppServiceMSICredentials`Kimlik doğrulaması için nesnesini kullanın. Bu örnek, bu mekanizmanın Azure Key Vault çalışmak için nasıl kullanılabileceğini gösterir:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>Kimlik kaldırma

Portal, PowerShell veya CLı kullanılarak oluşturulduğu gibi özellik devre dışı bırakılarak sistem tarafından atanan bir kimlik kaldırılabilir. Kullanıcı tarafından atanan kimlikler tek tek kaldırılabilir. Tüm kimlikleri kaldırmak için kimlik türünü "none" olarak ayarlayın.

Sistem tarafından atanan bir kimliğin bu şekilde kaldırılması, Azure AD 'den de silinecek. Uygulama kaynağı silindiğinde, sistem tarafından atanan kimlikler de Azure AD 'den otomatik olarak kaldırılır.

[ARM şablonundaki](#using-an-azure-resource-manager-template)tüm kimlikleri kaldırmak için:

```json
"identity": {
    "type": "None"
}
```

Azure PowerShell tüm kimlikleri kaldırmak için (yalnızca Azure Işlevleri):

```azurepowershell-interactive
# Update an existing function app to have IdentityType "None".
Update-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -IdentityType None
```

> [!NOTE]
> Ayrıca, yalnızca yerel belirteç hizmetini devre dışı bırakan WEBSITE_DISABLE_MSI ayarlanbilen bir uygulama ayarı da vardır. Ancak, kimliği yerinde bırakır ve araç, yönetilen kimliği "açık" veya "etkin" olarak göstermeye devam eder. Sonuç olarak, bu ayarın kullanılması önerilmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Yönetilen kimlik kullanarak SQL veritabanına güvenli bir şekilde erişin](app-service-web-tutorial-connect-msi.md)
- [Yönetilen kimlik kullanarak Azure depolama ile güvenli bir şekilde erişin](scenario-secure-app-access-storage.md)
- [Yönetilen kimlik kullanarak güvenli Microsoft Graph çağrısı yapın](scenario-secure-app-access-microsoft-graph-as-app.md)

[Microsoft. Azure. Services. AppAuthentication başvurusu]: /dotnet/api/overview/azure/service-to-service-authentication