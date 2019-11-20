---
title: Yönetilen kimliklere genel bakış-Azure App Service | Microsoft Docs
description: Azure App Service ve Azure Işlevlerinde Yönetilen kimlikler için kavramsal başvuru ve Kurulum Kılavuzu
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/30/2019
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: a2f6d7f881e404e9e4dbdb8087cabf25f67d561b
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847315"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>App Service ve Azure Işlevleri için Yönetilen kimlikler kullanma

> [!Important] 
> App Service ve Azure Işlevleri için Yönetilen kimlikler, uygulamanız abonelikler/kiracılar arasında geçirilirse beklendiği gibi davranmaz. Uygulamanın, özelliği devre dışı bırakıp yeniden etkinleştirerek yapılabilecek yeni bir kimlik alması gerekir. Aşağıdaki [kimliği kaldırma](#remove) bölümüne bakın. Aşağı akış kaynakları, yeni kimliği kullanmak için erişim ilkelerinin güncelleştirilmesini de gerekecektir.

Bu konu, App Service ve Azure Işlevleri uygulamaları için yönetilen bir kimlik oluşturmayı ve diğer kaynaklara erişmek için nasıl kullanılacağını gösterir. Azure Active Directory’de yönetilen bir kimlik, uygulamanızın Azure Key Vault gibi AAD korumalı kaynaklara kolayca ulaşmasını sağlar. Kimlik, Azure platformu tarafından yönetilir ve herhangi bir gizli dizi sağlamanızı veya döndürmenizi gerektirmez. AAD 'deki Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md).

Uygulamanıza iki tür kimlik verilebilir: 
- **Sistem tarafından atanan bir kimlik** uygulamanıza bağlanır ve uygulamanız silinirse silinir. Uygulamanın yalnızca bir sistem tarafından atanmış kimliği olabilir.
- **Kullanıcı tarafından atanan bir kimlik** , uygulamanıza atanabilecek tek başına bir Azure kaynağıdır. Bir uygulamada birden çok kullanıcı tarafından atanan kimlik olabilir.

## <a name="adding-a-system-assigned-identity"></a>Sistem tarafından atanan kimlik ekleme

Sistem tarafından atanan kimlik ile uygulama oluşturmak, uygulamada ek bir özellik ayarlanmasını gerektirir.

### <a name="using-the-azure-portal"></a>Azure portalını kullanma

Portalda yönetilen bir kimlik ayarlamak için öncelikle normal olarak bir uygulama oluşturun ve ardından özelliği etkinleştirmeniz gerekir.

1. Portalda genellikle yaptığınız gibi bir uygulama oluşturun. Portalda bu sayfaya gidin.

2. Bir işlev uygulaması kullanıyorsanız, **platform özellikleri**' ne gidin. Diğer uygulama türleri için, sol gezinti bölmesinde **Ayarlar** grubuna gidin.

3. **Kimlik**seçin.

4. **Sistem atandı** sekmesinde **durumu** **Açık**olarak değiştirin. **Kaydet** düğmesine tıklayın.

    ![App Service yönetilen kimliği](media/app-service-managed-service-identity/msi-blade-system.png)

### <a name="using-the-azure-cli"></a>Azure CLI kullanma

Azure CLı kullanarak yönetilen bir kimlik ayarlamak için, mevcut bir uygulamada `az webapp identity assign` komutunu kullanmanız gerekir. Bu bölümde örnekleri çalıştırmak için üç seçeneğiniz vardır:

- Azure portal [Azure Cloud Shell](../cloud-shell/overview.md) kullanın.
- Aşağıdaki her bir kod bloğunun sağ üst köşesinde bulunan "dene" düğmesini kullanarak katıştırılmış Azure Cloud Shell kullanın.
- Yerel bir CLı konsolu kullanmayı tercih ediyorsanız, [en son Azure CLI sürümünü](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 veya üzeri) yükleyebilirsiniz. 

Aşağıdaki adımlar, bir Web uygulaması oluşturma ve CLı kullanarak bir kimlik atama işleminde size kılavuzluk eder:

1. Azure CLI'yi yerel bir konsolda kullanıyorsanız, önce [az login](/cli/azure/reference-index#az-login) kullanarak Azure'da oturum açın. Uygulamayı dağıtmak istediğiniz Azure aboneliğiyle ilişkili bir hesabı kullanın:

    ```azurecli-interactive
    az login
    ```
2. CLı kullanarak bir Web uygulaması oluşturun. CLı 'yı App Service ile kullanma hakkında daha fazla örnek için bkz. [App SERVICE CLI örnekleri](../app-service/samples-cli.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Bu uygulamanın kimliğini oluşturmak için `identity assign` komutunu çalıştırın:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aşağıdaki adımlar, bir Web uygulaması oluşturma ve Azure PowerShell kullanarak bir kimlik atama işleminde size yol gösterecektir:

1. Gerekirse, [Azure PowerShell kılavuzunda](/powershell/azure/overview) bulunan yönergeleri kullanarak Azure PowerShell’i yükleyin ve ardından Azure ile bağlantı oluşturmak için `Login-AzAccount` komutunu çalıştırın.

2. Azure PowerShell kullanarak bir Web uygulaması oluşturun. App Service Azure PowerShell kullanma hakkında daha fazla örnek için bkz. [App Service PowerShell örnekleri](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Bu uygulamanın kimliğini oluşturmak için `Set-AzWebApp -AssignIdentity` komutunu çalıştırın:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanma

Azure Resource Manager şablonu, Azure kaynaklarınızın dağıtımını otomatikleştirmek için kullanılabilir. App Service ve Işlevlerine dağıtma hakkında daha fazla bilgi edinmek için bkz. [App Service kaynak dağıtımını otomatikleştirme](../app-service/deploy-complex-application-predictably.md) ve [Azure Işlevlerinde kaynak dağıtımını otomatikleştirme](../azure-functions/functions-infrastructure-as-code.md).

`Microsoft.Web/sites` türündeki herhangi bir kaynak, kaynak tanımına aşağıdaki özelliği ekleyerek bir kimlikle oluşturulabilir:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> Bir uygulama aynı anda hem sistem tarafından hem de Kullanıcı tarafından atanan kimliklere sahip olabilir. Bu durumda `type` özelliği `SystemAssigned,UserAssigned`

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

`<TENANTID>` ve `<PRINCIPALID>`, GUID 'Ler ile değiştirilmiştir. Tenantıd özelliği, kimliğin ait olduğu AAD kiracısının ne olduğunu tanımlar. PrincipalId, uygulamanın yeni kimliği için benzersiz bir tanımlayıcıdır. AAD 'de, hizmet sorumlusu App Service veya Azure Işlevleri Örneğinizde verdiğiniz aynı ada sahiptir.


## <a name="adding-a-user-assigned-identity"></a>Kullanıcı tarafından atanan kimlik ekleme

Kullanıcı tarafından atanan kimlik ile uygulama oluşturmak için kimlik oluşturmanız ve ardından kaynak tanımlayıcısını uygulama yapılandırmaya eklemeniz gerekir.

### <a name="using-the-azure-portal"></a>Azure portalını kullanma

İlk olarak, Kullanıcı tarafından atanan bir kimlik kaynağı oluşturmanız gerekir.

1. [Bu yönergelere](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)göre Kullanıcı tarafından atanan bir yönetilen kimlik kaynağı oluşturun.

2. Portalda genellikle yaptığınız gibi bir uygulama oluşturun. Portalda bu sayfaya gidin.

3. Bir işlev uygulaması kullanıyorsanız, **platform özellikleri**' ne gidin. Diğer uygulama türleri için, sol gezinti bölmesinde **Ayarlar** grubuna gidin.

4. **Kimlik**seçin.

5. **Kullanıcı atandı** sekmesinde **Ekle**' ye tıklayın.

6. Daha önce oluşturduğunuz kimliği arayın ve seçin. **Ekle**'ye tıklayın.

    ![App Service yönetilen kimliği](media/app-service-managed-service-identity/msi-blade-user.png)

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanma

Azure Resource Manager şablonu, Azure kaynaklarınızın dağıtımını otomatikleştirmek için kullanılabilir. App Service ve Işlevlerine dağıtma hakkında daha fazla bilgi edinmek için bkz. [App Service kaynak dağıtımını otomatikleştirme](../app-service/deploy-complex-application-predictably.md) ve [Azure Işlevlerinde kaynak dağıtımını otomatikleştirme](../azure-functions/functions-infrastructure-as-code.md).

`Microsoft.Web/sites` türünde herhangi bir kaynak, kaynak tanımına aşağıdaki blok dahil ederek bir kimlikle oluşturulabilir ve `<RESOURCEID>` istenen kimliğin kaynak KIMLIĞI ile değiştiriliyor:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}    
```

> [!NOTE] 
> Bir uygulama aynı anda hem sistem tarafından hem de Kullanıcı tarafından atanan kimliklere sahip olabilir. Bu durumda `type` özelliği `SystemAssigned,UserAssigned`

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

`<PRINCIPALID>` ve `<CLIENTID>`, GUID 'Ler ile değiştirilmiştir. PrincipalId, AAD yönetimi için kullanılan kimlik için benzersiz bir tanımlayıcıdır. ClientID, uygulamanın çalışma zamanı çağrıları sırasında hangi kimliğin kullanılacağını belirtmek için kullanılan yeni kimliği için benzersiz bir tanımlayıcıdır.


## <a name="obtaining-tokens-for-azure-resources"></a>Azure kaynakları için belirteçleri alma

Bir uygulama, Azure Key Vault gibi AAD tarafından korunan diğer kaynaklara belirteç almak için kimliğini kullanabilir. Bu belirteçler, uygulamanın belirli bir kullanıcısı değil, kaynağa erişen uygulamayı temsil eder. 

> [!IMPORTANT]
> Uygulamanızdaki erişime izin vermek için hedef kaynağı yapılandırmanız gerekebilir. Örneğin, Key Vault için bir belirteç istemeniz durumunda uygulamanızın kimliğini içeren bir erişim ilkesi eklediğinizden emin olmanız gerekir. Aksi takdirde, belirteci içerse bile Key Vault çağrılarınız reddedilir. Azure Active Directory belirteçlerini destekleyen kaynaklar hakkında daha fazla bilgi edinmek için bkz. [Azure AD kimlik doğrulamasını destekleyen Azure hizmetleri](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

App Service ve Azure Işlevlerinde belirteç almak için basit bir REST Protokolü vardır. Bu, tüm uygulamalar ve diller için kullanılabilir. Bazı .NET ve Java için Azure SDK, bu protokol üzerinden bir soyutlama sağlar ve yerel bir geliştirme deneyimini kolaylaştırır.

### <a name="using-the-rest-protocol"></a>REST protokolünü kullanma

Yönetilen kimliğe sahip bir uygulama tanımlı iki ortam değişkenine sahiptir:

- MSI_ENDPOINT-yerel belirteç hizmeti URL 'SI.
- MSI_SECRET-sunucu tarafı istek sahteciliğini önleme (ssrf) saldırılarını azaltmaya yardımcı olmak için kullanılan bir üst bilgi. Değer, platform tarafından döndürülür.

**MSI_ENDPOINT** , uygulamanızın belirteç isteyebileceği yerel bir URL 'dir. Bir kaynağın belirtecini almak için, bu uç noktaya yönelik bir HTTP GET isteği oluşturun ve aşağıdaki parametreleri de dahil edin:

> |Parametre adı|'Ndaki|Açıklama|
> |-----|-----|-----|
> |Kaynak|Sorgu|Belirtecin alınması gereken kaynağın AAD Kaynak URI 'SI. Bu, [Azure AD kimlik doğrulamasını](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) veya DIĞER Kaynak URI 'Yi destekleyen Azure hizmetlerinden biridir.|
> |api sürümü|Sorgu|Kullanılacak belirteç API 'sinin sürümü. "2017-09-01" Şu anda desteklenen tek sürümdür.|
> |gizli dizi|Üst bilgi|MSI_SECRET ortam değişkeninin değeri. Bu üst bilgi, sunucu tarafı istek sahteciliğini önleme (ssrf) saldırılarını azaltmaya yardımcı olmak için kullanılır.|
> |ClientID|Sorgu|(Kullanıcı atanmadığı için isteğe bağlı) Kullanılacak kullanıcı tarafından atanan kimliğin KIMLIĞI. Atlanırsa, sistem tarafından atanan kimlik kullanılır.|

> [!IMPORTANT]
> Kullanıcı tarafından atanan kimlikler için belirteçleri almaya çalışıyorsanız, `clientid` özelliğini eklemeniz gerekir. Aksi takdirde, belirteç hizmeti sistem tarafından atanan bir kimlik için belirteç edinmeye çalışır, bu da mevcut olabilir.

Başarılı bir 200 Tamam yanıtı, aşağıdaki özelliklere sahip bir JSON gövdesi içerir:

> |Özellik adı|Açıklama|
> |-------------|----------|
> |access_token|İstenen erişim belirteci. Çağıran Web hizmeti, alıcı Web hizmetinde kimlik doğrulaması yapmak için bu belirteci kullanabilir.|
> |expires_on|Erişim belirtecinin süre sonu. Tarih, 1970-01-01T0:0: 0Z UTC 'den sona erme zamanına kadar saniye sayısı olarak gösterilir. Bu değer, önbelleğe alınmış belirteçlerin ömrünü belirlemede kullanılır.|
> |Kaynak|Alıcı Web hizmetinin uygulama KIMLIĞI URI 'SI.|
> |token_type|Belirteç türü değerini gösterir. Azure AD 'nin desteklediği tek tür taşıyıcı. Taşıyıcı belirteçleri hakkında daha fazla bilgi için bkz. [OAuth 2,0 yetkilendirme çerçevesi: taşıyıcı belirteç kullanımı (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt).|

Bu yanıt, [AAD hizmetten hizmete erişim belirteci isteğine yönelik yanıt](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)ile aynıdır.

> [!NOTE]
> Ortam değişkenleri, işlem ilk kez başladığında ayarlanır, bu nedenle uygulamanız için yönetilen bir kimlik etkinleştirildikten sonra, `MSI_ENDPOINT` ve `MSI_SECRET` kodunuzda kullanabilmeniz için uygulamanızı yeniden başlatmanız veya kodunu yeniden dağıtmanız gerekebilir.

### <a name="rest-protocol-examples"></a>REST protokol örnekleri

Örnek bir istek aşağıdakine benzeyebilir:

```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

Bir örnek yanıt aşağıdaki gibi görünebilir:

```
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Kod örnekleri

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!TIP]
> .NET dilleri için, bu isteği kendiniz taslağı yapmak yerine [Microsoft. Azure. Services. AppAuthentication](#asal) ' yi de kullanabilirsiniz.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2017-09-01", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource));
    request.Headers.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await _client.SendAsync(request);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=2017-09-01`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
import os
import requests

msi_endpoint = os.environ["MSI_ENDPOINT"]
msi_secret = os.environ["MSI_SECRET"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{msi_endpoint}?resource={resource_uri}&api-version=2017-09-01"
    head_msi = {'Secret':msi_secret}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=2017-09-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="asal"></a>.NET için Microsoft. Azure. Services. AppAuthentication kitaplığını kullanma

.NET uygulamaları ve işlevleri için, yönetilen bir kimlikle çalışmanın en kolay yolu Microsoft. Azure. Services. AppAuthentication paketi aracılığıyla yapılır. Bu kitaplık Ayrıca, Visual Studio, [Azure CLI](/cli/azure)veya Active Directory tümleşik kimlik doğrulaması için kullanıcı hesabınızı kullanarak kodunuzu geliştirme makinenizde yerel olarak sınamanızı sağlar. Bu kitaplıkla ilgili yerel geliştirme seçenekleri hakkında daha fazla bilgi için [Microsoft. Azure. Services. AppAuthentication başvurusu]bakın. Bu bölümde, kodunuzda kitaplığı kullanmaya nasıl başlacağınız gösterilmektedir.

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

Microsoft. Azure. Services. AppAuthentication ve sunduğu işlemler hakkında daha fazla bilgi edinmek için, [MSI .net örneği Ile](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet) [Microsoft. Azure. Services. appauthentication başvurusu] ve App Service ve keykasası ' na bakın.

### <a name="using-the-azure-sdk-for-java"></a>Java için Azure SDK 'sını kullanma

Java uygulamaları ve işlevleri için, yönetilen bir kimlikle çalışmanın en kolay yolu, [Java Için Azure SDK](https://github.com/Azure/azure-sdk-for-java)'ıdır. Bu bölümde, kodunuzda kitaplığı kullanmaya nasıl başlacağınız gösterilmektedir.

1. [Azure SDK kitaplığına](https://mvnrepository.com/artifact/com.microsoft.azure/azure)bir başvuru ekleyin. Maven projeleri için, bu kod parçacığını projenin Pod dosyasının `dependencies` bölümüne ekleyebilirsiniz:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Kimlik doğrulaması için `AppServiceMSICredentials` nesnesini kullanın. Bu örnek, bu mekanizmanın Azure Key Vault çalışmak için nasıl kullanılabileceğini gösterir:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove"></a>Kimlik kaldırma

Portal, PowerShell veya CLı kullanılarak oluşturulduğu gibi özellik devre dışı bırakılarak sistem tarafından atanan bir kimlik kaldırılabilir. Kullanıcı tarafından atanan kimlikler tek tek kaldırılabilir. Tüm kimlikleri kaldırmak için REST/ARM şablon protokolünde, bu, türü "none" olarak ayarlayarak yapılır:

```json
"identity": {
    "type": "None"
}
```

Sistem tarafından atanan bir kimliğin bu şekilde kaldırılması, AAD 'den de silinecek. Uygulama kaynağı silindiğinde, sistem tarafından atanan kimlikler de AAD 'den otomatik olarak kaldırılır.

> [!NOTE]
> Ayrıca, yalnızca yerel belirteç hizmetini devre dışı bırakan WEBSITE_DISABLE_MSI ayarlanbilen bir uygulama ayarı da vardır. Ancak, kimliği yerinde bırakır ve araç, yönetilen kimliği "açık" veya "etkin" olarak göstermeye devam eder. Sonuç olarak, bu ayarın kullanılması önerilmez.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yönetilen kimlik kullanarak SQL veritabanına güvenli bir şekilde erişin](app-service-web-tutorial-connect-msi.md)

[Microsoft. Azure. Services. AppAuthentication başvurusu]: https://go.microsoft.com/fwlink/p/?linkid=862452
