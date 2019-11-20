---
title: Kaynak sağlayıcısı kayıt hataları
description: Azure Resource Manager ile kaynak dağıtılırken Azure Kaynak sağlayıcısı kayıt hatalarının nasıl çözümlendiğini açıklar.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.openlocfilehash: 96595bab9d0db189911cac4fc1b42c722c2c1515
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150499"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Kaynak Sağlayıcısı kaydı için hataları çözümleyin

Bu makalede aboneliğinizde daha önce kullanmadığınız bir kaynak sağlayıcısı kullanırken karşılaşabileceğiniz hatalar açıklanır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Belirti

Kaynak dağıtım yaparken, aşağıdaki hata kodu ve şu iletiyle alabilirsiniz:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Veya bildiren benzer bir ileti alabilirsiniz:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Hata iletisi desteklenen konumları ve API sürümleri için öneriler vermeniz gerekir. Önerilen değerler birine şablonunuzu değiştirebilirsiniz. Azure portalı veya komut satırı arabirimi, kullanmakta olduğunuz tarafından otomatik olarak kayıtlı ancak tüm çoğu sağlayıcıları. Bir kaynak sağlayıcısı önce kullanmadıysanız, bu sağlayıcıyı kaydetmek gerekebilir.

Ya da sanal makineler için otomatik kapanmaya devre dışı bıraktığınızda şuna benzer bir hata iletisi alabilirsiniz:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Nedeni

Şu nedenlerden biri için bu hataları alırsınız:

* Gerekli kaynak sağlayıcısı aboneliğiniz için kayıtlı değil
* Kaynak türü için desteklenmeyen API sürümü
* Konum kaynak türü için desteklenmiyor
* VM 'lerin otomatik olarak kapatılmasını için, Microsoft. DevTestLab kaynak sağlayıcısının kayıtlı olması gerekir.

## <a name="solution-1---powershell"></a>Çözüm 1 - PowerShell

PowerShell için, kayıt durumunuzu görmek için **Get-AzResourceProvider** ' ı kullanın.

```powershell
Get-AzResourceProvider -ListAvailable
```

Bir sağlayıcıyı kaydetmek için **register-AzResourceProvider** ' ı kullanın ve kaydetmek istediğiniz kaynak sağlayıcının adını sağlayın.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Belirli bir kaynak türü için desteklenen konumlar almak için kullanın:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Belirli bir kaynak türü için desteklenen API sürümlerini almak için kullanın:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Çözüm 2 - Azure CLI

Sağlayıcı kayıtlı olup olmadığını görmek için `az provider list` komutu.

```azurecli-interactive
az provider list
```

Bir kaynak sağlayıcısını kaydetmek için kullanın `az provider register` komutunu ve belirtin *ad alanı* kaydedilecek.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Desteklenen konumlar ve bir kaynak türü için API sürümlerini görmek için bu seçeneği kullanın:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Çözüm 3 - Azure portalı

Kayıt durumunu görmek ve bir kaynak sağlayıcısı ad alanı Portalı aracılığıyla kaydolun.

1. Portaldan seçin **tüm hizmetleri**.

   ![Tüm hizmetleri seçin](./media/resource-manager-register-provider-errors/select-all-services.png)

1. **Abonelikler**'i seçin.

   ![Abonelikleri seçin](./media/resource-manager-register-provider-errors/select-subscriptions.png)

1. Abonelikler listesinden, kaynak sağlayıcısını kaydetmek için kullanmak istediğiniz aboneliği seçin.

   ![Kaynak sağlayıcısını kaydetmek için bir abonelik seçin](./media/resource-manager-register-provider-errors/select-subscription-to-register.png)

1. Aboneliğiniz için seçin **kaynak sağlayıcıları**.

   ![Kaynak sağlayıcılarını seçin](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. Kaynak sağlayıcıları listesini arayın ve gerekirse seçin **kaydetme** dağıtmaya çalıştığınız türü kaynak sağlayıcısını kaydetmek için bağlantı.

   ![Kaynak sağlayıcıları listesi](./media/resource-manager-register-provider-errors/list-resource-providers.png)
