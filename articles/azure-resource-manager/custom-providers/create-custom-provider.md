---
title: Kaynak sağlayıcısı oluştur
description: Bir kaynak sağlayıcısı oluşturmayı ve özel kaynak türlerini dağıtmayı açıklar.
author: MSEvanhi
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: evanhi
ms.openlocfilehash: 915856e3f9875c0a3318bc19138eaae8742bf72f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871750"
---
# <a name="quickstart-create-a-custom-provider-and-deploy-custom-resources"></a>Hızlı başlangıç: özel bir sağlayıcı oluşturma ve özel kaynaklar dağıtma

Bu hızlı başlangıçta, kendi kaynak sağlayıcınızı oluşturup bu kaynak sağlayıcısı için özel kaynak türleri dağıtırsınız. Özel sağlayıcılar hakkında daha fazla bilgi için bkz. [Azure özel sağlayıcılar önizlemeye genel bakış](overview.md).

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
- Bu hızlı başlangıçtaki adımları tamamlayabilmeniz için, işlemleri çağırmanız gerekir `REST` . [Rest isteklerinin gönderilmesi için farklı yollar](/rest/api/azure/)vardır.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ortamınızı Azure CLı için hazırlayın.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Azure CLı örnekleri, `az rest` istekler için kullanır `REST` . Daha fazla bilgi için bkz. [az Rest](/cli/azure/reference-index#az_rest).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- PowerShell komutları, PowerShell 7 veya üzeri ve Azure PowerShell modülleri kullanılarak yerel olarak çalıştırılır. Daha fazla bilgi için bkz. [ınstall Azure PowerShell](/powershell/azure/install-az-ps).
- Zaten işlemler için bir aracınız yoksa `REST` , [Armclient](https://github.com/projectkudu/ARMClient)'ı yükleyebilirsiniz. Bu, Azure Resource Manager API 'nin çağrılması basitleşerek açık kaynaklı bir komut satırı aracıdır.
- **Armclient** yüklendikten sonra, şunu yazarak bir PowerShell komut isteminden kullanım bilgilerini görüntüleyebilirsiniz: `armclient.exe` . Ya da [Armclient wiki](https://github.com/projectkudu/ARMClient/wiki)'ye gidin.

---

## <a name="deploy-custom-provider"></a>Özel sağlayıcı dağıt

Özel sağlayıcıyı ayarlamak için Azure aboneliğinize [örnek bir şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) dağıtın.

Şablonu dağıttıktan sonra, aboneliğiniz aşağıdaki kaynaklara sahiptir:

- Kaynaklar ve eylemler için işlemlerle İşlev Uygulaması.
- Özel sağlayıcı aracılığıyla oluşturulan kullanıcıları depolamaya yönelik depolama hesabı.
- Özel kaynak türlerini ve eylemlerini tanımlayan özel sağlayıcı. İstek göndermek için App Endpoint işlevini kullanır.
- Özel sağlayıcıdan özel kaynak.

Özel sağlayıcıyı dağıtmak için Azure CLı, PowerShell veya Azure portal kullanın:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu örnek, bir kaynak grubu, konum ve sağlayıcının işlev uygulaması adını girmenizi ister. Adlar, diğer komutlarda kullanılan değişkenlerde depolanır. [Az Group Create](/cli/azure/group#az_group_create) ve [az Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create) komutları kaynakları dağıtır.

```azurecli-interactive
read -p "Enter a resource group name:" rgName &&
read -p "Enter the location (i.e. eastus):" location &&
read -p "Enter the provider's function app name:" funcName &&
templateUri="https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json" &&
az group create --name $rgName --location "$location" &&
az deployment group create --resource-group $rgName --template-uri $templateUri --parameters funcName=$funcName &&
echo "Press [ENTER] to continue ..." &&
read
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bu örnek, bir kaynak grubu, konum ve sağlayıcının işlev uygulaması adını girmenizi ister. Adlar, diğer komutlarda kullanılan değişkenlerde depolanır. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) ve [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) komutları, kaynakları dağıtır.

```powershell
$rgName = Read-Host -Prompt "Enter a resource group name"
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$funcName = Read-Host -Prompt "Enter the provider's function app name"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json"
New-AzResourceGroup -Name $rgName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $rgName -TemplateUri $templateUri -funcName $funcName
Read-Host -Prompt "Press [ENTER] to continue ..."
```

---

Çözümü Azure portal de dağıtabilirsiniz. Azure portal şablonu açmak için **Azure 'A dağıt** düğmesini seçin.

[![Azure’a dağıtın](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json)

## <a name="view-custom-provider-and-resource"></a>Özel sağlayıcıyı ve kaynağı görüntüle

Portalda, özel sağlayıcı gizli bir kaynak türüdür. Kaynak sağlayıcının dağıtıldığını doğrulamak için kaynak grubuna gidin. **Gizli türleri gösterme** seçeneğini belirleyin.

![Gizli kaynak türlerini göster](./media/create-custom-provider/show-hidden.png)

Dağıttığınız özel kaynak türünü görmek için, `GET` kaynak türü üzerinde işlemi kullanın.

```http
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
subID=$(az account show --query id --output tsv)
requestURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"
az rest --method get --uri $requestURI
```

Yanıtı alırsınız:

```json
{
  "value": [
    {
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole",
        "provisioningState": "Succeeded"
      },
      "resourceGroup": "<rg-name>",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

Yanıtı alırsınız:

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

---

## <a name="call-action"></a>Çağrı eylemi

Özel sağlayıcıınızda adlı bir eylem de vardır `ping` . İsteği işleyen kod, işlev uygulamasında uygulanır. `ping`Eylem bir selamlama ile yanıt verir.

İstek göndermek için `ping` `POST` özel sağlayıcıınızda işlemi kullanın.

```http
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
pingURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"
az rest --method post --uri $pingURI
```

Yanıtı alırsınız:

```json
{
  "message": "hello <function-name>.azurewebsites.net",
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  }
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

Yanıtı alırsınız:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

---

## <a name="create-a-resource-type"></a>Kaynak türü oluşturma

Özel kaynak türünü oluşturmak için, kaynağı bir şablonda dağıtabilirsiniz. Bu yaklaşım, bu hızlı başlangıçta dağıttığınız şablonda gösterilmiştir. `PUT`Kaynak türü için de bir istek gönderebilirsiniz.

```http
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
addURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
az rest --method put --uri $addURI --body "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"
```

Yanıtı alırsınız:

```json
{
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "properties": {
    "FullName": "Test User",
    "Location": "Earth",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "<rg-name>",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

Yanıtı alırsınız:

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

---

## <a name="custom-resource-provider-commands"></a>Özel kaynak sağlayıcısı komutları

Özel kaynak sağlayıcınızda çalışmak için [özel sağlayıcılar](/cli/azure/custom-providers/resource-provider) komutlarını kullanın.

### <a name="list-custom-resource-providers"></a>Özel kaynak sağlayıcıları listeleme

`list`Bir abonelikteki tüm özel kaynak sağlayıcılarını göstermek için komutunu kullanın. Varsayılan, geçerli aboneliğin özel kaynak sağlayıcılarını listeler veya `--subscription` parametresini belirtebilirsiniz. Bir kaynak grubunu listelemek için `--resource-group` parametresini kullanın.

```azurecli-interactive
az custom-providers resource-provider list --subscription $subID
```

```json
[
  {
    "actions": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "ping",
        "routingType": "Proxy"
      }
    ],
    "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceproviders/<provider-name>",
    "location": "eastus",
    "name": "<provider-name>",
    "provisioningState": "Succeeded",
    "resourceGroup": "<rg-name>",
    "resourceTypes": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "users",
        "routingType": "Proxy, Cache"
      }
    ],
    "tags": {},
    "type": "Microsoft.CustomProviders/resourceproviders",
    "validations": null
  }
]
```

### <a name="show-the-properties"></a>Özellikleri göster

`show`Özel kaynak sağlayıcısının özelliklerini göstermek için komutunu kullanın. Çıkış biçimi `list` çıkışa benzer.

```azurecli-interactive
az custom-providers resource-provider show --resource-group $rgName --name $funcName
```

### <a name="create-a-new-resource"></a>Yeni kaynak oluşturma

`create`Özel bir kaynak sağlayıcısı oluşturmak veya güncelleştirmek için komutunu kullanın. Bu örnek, `actions` ve ' ni güncelleştirir `resourceTypes` .

```azurecli-interactive
az custom-providers resource-provider create --resource-group $rgName --name $funcName \
--action name=ping endpoint=https://myTestSite.azurewebsites.net/api/{requestPath} routing_type=Proxy \
--resource-type name=users endpoint=https://myTestSite.azurewebsites.net/api{requestPath} routing_type="Proxy, Cache"
```

```json
"actions": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api/{requestPath}",
    "name": "ping",
    "routingType": "Proxy"
  }
],

"resourceTypes": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api{requestPath}",
    "name": "users",
    "routingType": "Proxy, Cache"
  }
],
```

### <a name="update-the-providers-tags"></a>Sağlayıcının etiketlerini Güncelleştir

`update`Komut yalnızca özel bir kaynak sağlayıcısı için etiketleri güncelleştirir. Azure portal, özel kaynak sağlayıcısının App Service etiketi gösterir.

```azurecli-interactive
az custom-providers resource-provider update --resource-group $rgName --name $funcName --tags new=tag
```

```json
"tags": {
  "new": "tag"
},
```

### <a name="delete-a-custom-resource-provider"></a>Özel bir kaynak sağlayıcısını silme

`delete`Komut sizi sorar ve yalnızca özel kaynak sağlayıcısını siler. Depolama hesabı, App Service ve App Service planı silinmez. Sağlayıcı silindikten sonra bir komut istemine döndürülürsünüz.

```azurecli-interactive
az custom-providers resource-provider delete --resource-group $rgName --name $funcName
```

## <a name="next-steps"></a>Sonraki adımlar

Özel sağlayıcılara giriş için aşağıdaki makaleye bakın:

> [!div class="nextstepaction"]
> [Azure özel sağlayıcıları önizlemesine genel bakış](overview.md)
