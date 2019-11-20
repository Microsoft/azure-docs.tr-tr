---
title: Olay etki alanlarıyla olayları Azure Event Grid ile yayımlama
description: Azure Event Grid ' deki büyük konu kümelerinin nasıl yönetileceğini ve olay etki alanlarını kullanarak bu olaylara olayları nasıl yayımlayacağınızı gösterir.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 1d07227249806b7d54523af66817a170c19354ee
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786548"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Olay etki alanlarını kullanarak konuları yönetme ve olayları yayımlama

Bu makalede nasıl yapılacağı gösterilmektedir:

* Event Grid etki alanı oluşturma
* Olay kılavuzuna abone olma konuları
* Anahtarları Listele
* Olayları bir etki alanına yayımlama

Olay etki alanları hakkında bilgi edinmek için bkz. [Event Grid yönetmek için olay etki alanlarını anlama](event-domains.md).

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Önizleme özelliğini yükler

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Olay etki alanı oluşturma

Büyük konu kümelerini yönetmek için bir olay etki alanı oluşturun.

# <a name="azure-clitabazurecli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```
---

Başarılı oluşturma aşağıdaki değerleri döndürür:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

`endpoint` ve `id` etki alanını yönetmek ve olayları yayımlamak için gerekli olduğu gibi, bunları aklınızda edin.

## <a name="manage-access-to-topics"></a>Konulara erişimi yönetin

Konuların erişimini yönetme [rol ataması](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)aracılığıyla yapılır. Rol ataması, Azure kaynaklarındaki işlemleri belirli bir kapsamdaki yetkili kullanıcılarla sınırlamak için rol tabanlı erişim denetimi kullanır.

Event Grid, bir etki alanı içindeki çeşitli konularda belirli kullanıcılara erişim atamak için kullanabileceğiniz iki yerleşik rol içerir. Bu roller, aboneliklerin oluşturulmasına ve silinmesine ve yalnızca olay abonelikleri listesine izin veren `EventGrid EventSubscription Reader (Preview)``EventGrid EventSubscription Contributor (Preview)`.

# <a name="azure-clitabazurecli"></a>[Azure CLI](#tab/azurecli)
Aşağıdaki Azure CLı komutu, yalnızca `demotopic1`konu başlığında olay abonelikleri oluşturma ve silme `alice@contoso.com` kısıtlar:

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Aşağıdaki PowerShell komutu, yalnızca `demotopic1`konu başlığında olay abonelikleri oluşturmak ve silmek için `alice@contoso.com` kısıtlar:

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

Event Grid işlemlerine erişimi yönetme hakkında daha fazla bilgi için bkz. [Event Grid güvenlik ve kimlik doğrulaması](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Konu başlıklarını ve abonelikleri oluşturma

Event Grid hizmeti bir etki alanında ilgili konuyu, bir etki alanı konusu için olay aboneliği oluşturma çağrısına göre otomatik olarak oluşturur ve yönetir. Bir etki alanında konu oluşturmak için ayrı bir adım yoktur. Benzer şekilde, bir konunun son olay aboneliği silindiğinde, konu da silinir.

Etki alanındaki bir konuya abone olmak, diğer tüm Azure kaynaklarına abone olma ile aynıdır. Kaynak kaynak KIMLIĞI için, daha önce etki alanı oluştururken döndürülen olay etki alanı KIMLIĞINI belirtin. Abone olmak istediğiniz konuyu belirtmek için kaynak kaynak KIMLIĞI sonuna `/topics/<my-topic>` ekleyin. Etki alanındaki tüm olayları alan bir etki alanı kapsamı olay aboneliği oluşturmak için herhangi bir konu belirtmeden olay etki alanı KIMLIĞINI belirtin.

Genellikle, önceki bölümde erişim izni verdiğiniz kullanıcı aboneliği oluşturur. Bu makaleyi basitleştirmek için aboneliği oluşturursunuz. 

# <a name="azure-clitabazurecli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

Olaylarınızın abone olması için bir test uç noktasına ihtiyacınız varsa, gelen olayları görüntüleyen [önceden oluşturulmuş bir Web uygulamasını](https://github.com/Azure-Samples/azure-event-grid-viewer) her zaman dağıtabilirsiniz. Olaylarınızı `https://<your-site-name>.azurewebsites.net/api/updates`' de test Web sitenize gönderebilirsiniz.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Bir konu için ayarlanan izinler Azure Active Directory depolanır ve açıkça silinmelidir. Bir olay aboneliğini silmek, bir konu üzerinde yazma erişimi varsa, bir kullanıcının olay abonelikleri oluşturma erişimini iptal eder.


## <a name="publish-events-to-an-event-grid-domain"></a>Olayları Event Grid etki alanına yayımlama

Olayları bir etki alanına yayımlamak, [özel bir konuya yayımlama](./post-to-custom-topic.md)ile aynıdır. Ancak, özel konuya yayımlamak yerine, tüm olayları etki alanı uç noktasına yayımlarsınız. JSON olay verilerinde, olayların gitmesini istediğiniz konuyu belirtirsiniz. Aşağıdaki olay dizisi, `"id": "2222"` konuya `demotopic2`konusuna `demotopic1` bir olay `"id": "1111"` konusunda olaya neden olur:

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

# <a name="azure-clitabazurecli"></a>[Azure CLI](#tab/azurecli)
Azure CLı ile etki alanı uç noktasını almak için şunu kullanın

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Bir etki alanı için anahtarları almak için şunu kullanın:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
PowerShell ile etki alanı uç noktasını almak için şunu kullanın

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Bir etki alanı için anahtarları almak için şunu kullanın:

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

Daha sonra, olaylarınızı Event Grid etki alanına yayımlamak için bir HTTP GÖNDERISI yapmak üzere sık kullandığınız yöntemi kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* Olay etki alanlarındaki üst düzey kavramlar ve bunlara neden yararlı oldukları hakkında daha fazla bilgi için bkz. [olay etki alanlarına kavramsal genel bakış](event-domains.md).
