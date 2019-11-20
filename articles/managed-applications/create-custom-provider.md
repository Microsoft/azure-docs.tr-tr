---
title: Azure özel sağlayıcıları önizlemesi ile kaynak sağlayıcısı oluşturma
description: Bir kaynak sağlayıcısı oluşturmak ve özel kaynak türlerini dağıtma işlemleri açıklanır.
services: managed-applications
author: MSEvanhi
ms.service: managed-applications
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 41200139ef55fa1ae441192e2d81b5228cf29bad
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795310"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>Hızlı Başlangıç: Özel bir sağlayıcı oluşturmak ve özel kaynakları dağıtma

Bu hızlı başlangıçta, kendi kaynak sağlayıcısı oluşturur ve özel kaynak türleri için bu kaynak sağlayıcısı dağıtma. Özel sağlayıcıları hakkında daha fazla bilgi için bkz. [Azure özel sağlayıcıları Önizlemesi'ne genel bakış](custom-providers-overview.md).

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıçtaki adımları tamamlamak için REST işlemlerini çağırmak gerekir. Vardır [REST istekleri göndermek için farklı yollar](/rest/api/azure/). REST işlemleri için bir araç zaten yoksa, yükleme [ARMClient](https://github.com/projectkudu/ARMClient). Buna, Azure Resource Manager API'si çağırma basitleştiren bir açık kaynak komut satırı aracı var.

## <a name="deploy-custom-provider"></a>Özel sağlayıcısı dağıtma

Özel Sağlayıcı ' için dağıtmanız bir [örnek şablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) Azure aboneliğinize.

Şablonu dağıttıktan sonra aboneliğinizi aşağıdaki kaynakları içerir:

* İşlev uygulaması ile kaynaklara ve işlemlere yönelik işlemler.
* Özel sağlayıcı oluşturulan kullanıcılar depolamak için depolama hesabı'nı tıklatın.
* Eylemler ve özel kaynak türünü tanımlayan özel sağlayıcı. İşlev uygulaması uç noktası, istekleri göndermek için kullanır.
* Özel kaynak özel sağlayıcısından.

PowerShell ile özel sağlayıcı dağıtmak için kullanın:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

Veya aşağıdaki düğmeyle çözümü dağıtabilirsiniz:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Görünüm özel sağlayıcı ve kaynak

Portalda, özel bir sağlayıcı bir gizli kaynak türüdür. Kaynak sağlayıcısı dağıtıldığını doğrulamak için kaynak grubuna gidin. Seçeneğini **gizli türleri Göster**.

![Gizli kaynak türleri Göster](./media/create-custom-providers/show-hidden.png)

Dağıttığınız özel kaynak tipi görmek için kaynak türü üzerinde bir alma işlemi kullanın.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

ARMClient ile kullanın:

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

## <a name="call-action"></a>Eylem çağrısı

Özel sağlayıcınızın adlı bir eylem de sahip **ping**. İsteği işleyen kod, işlev uygulamasına uygulanır. Ping eylemi bir Karşılama ile yanıtlar.

Ping isteği göndermek için özel sağlayıcınızın POST işlemi kullanın.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

ARMClient ile kullanın:

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

## <a name="create-resource-type"></a>Kaynak türü oluştur

Özel kaynak türü oluşturmak için bir şablon kaynağında dağıtabilirsiniz. Bu hızlı başlangıçta dağıtılan şablonunda bu yaklaşım gösterilmektedir. Ayrıca, kaynak türü için bir PUT isteği gönderebilirsiniz.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

ARMClient ile kullanın:

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

## <a name="next-steps"></a>Sonraki adımlar

Özel sağlayıcılar için bir giriş için bkz [Azure özel sağlayıcıları Önizlemesi'ne genel bakış](custom-providers-overview.md).
