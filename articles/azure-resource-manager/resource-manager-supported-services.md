---
title: Kaynak sağlayıcıları ve kaynak türleri
description: Kaynak Yöneticisi, şemaları ve kullanılabilir API sürümlerini ve kaynakları barındırabildiğiniz bölgeleri destekleyen kaynak sağlayıcılarını açıklar.
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 01550adbce51e3498f1167f6a4ebfd846ec316f3
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149410"
---
# <a name="azure-resource-providers-and-types"></a>Azure kaynak sağlayıcıları ve türleri

Kaynakları dağıttığınızda, genellikle kaynak sağlayıcıları ve türleri hakkında bilgi almanız gerekir. Örneğin, anahtarları ve gizli dizileri depolamak istiyorsanız, Microsoft. Keykasası kaynak sağlayıcısı ile çalışırsınız. Bu kaynak sağlayıcısı, anahtar kasasını oluşturmak için kasaların adlı bir kaynak türü sunar.

Kaynak türü adı şu biçimdedir: **{kaynak-sağlayıcısı}/{kaynak-türü}** . Bir Anahtar Kasası için kaynak türü **Microsoft. keykasası/kasadır**.

Bu makalede şunları öğreneceksiniz:

* Azure 'daki tüm kaynak sağlayıcılarını görüntüleme
* Kaynak sağlayıcısının kayıt durumunu denetle
* Kaynak sağlayıcısını kaydetme
* Kaynak sağlayıcısı için kaynak türlerini görüntüleme
* Kaynak türü için geçerli konumları görüntüleme
* Kaynak türü için geçerli API sürümlerini görüntüleme

Bu adımları Azure portal, Azure PowerShell veya Azure CLı aracılığıyla yapabilirsiniz.

Kaynak sağlayıcılarını Azure hizmetleriyle eşleyen bir liste için bkz. [Azure hizmetleri Için kaynak sağlayıcıları](azure-services-resource-providers.md).

## <a name="azure-portal"></a>Azure portal

Tüm kaynak sağlayıcılarını ve aboneliğiniz için kayıt durumunu görmek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Tüm Hizmetler**’i seçin.

    ![abonelikleri Seç](./media/resource-manager-supported-services/select-subscriptions.png)
3. **Tüm hizmetler** kutusunda, **abonelik**girin ve ardından **abonelikler**' i seçin.
4. Görüntülenecek abonelik listesinden aboneliği seçin.
5. **Kaynak sağlayıcıları** ' nı seçin ve kullanılabilir kaynak sağlayıcılarının listesini görüntüleyin.

    ![Kaynak sağlayıcılarını göster](./media/resource-manager-supported-services/show-resource-providers.png)

6. Kaynak sağlayıcısı kaydı, aboneliğinizi kaynak sağlayıcısıyla çalışacak şekilde yapılandırır. Kayıt için kapsam her zaman aboneliktir. Varsayılan olarak, birçok kaynak sağlayıcısı otomatik olarak kaydedilir. Ancak, bazı kaynak sağlayıcılarını el ile kaydetmeniz gerekebilir. Kaynak sağlayıcısını kaydetmek için, kaynak sağlayıcısı için `/register/action` işlemini yapmak için izninizin olması gerekir. Bu işlem, Katkıda Bulunan ve Sahip rolleriyle birlikte sunulur. Bir kaynak sağlayıcısını kaydetmek için **Kaydet**' i seçin. Önceki ekran görüntüsünde, **kayıt** bağlantısı **Microsoft. Blueprint**için vurgulanır.

    Aboneliğinizde kaynak sağlayıcınızdan hala kaynak türleri varsa, bir kaynak sağlayıcısının kaydını silemezsiniz.

Belirli bir kaynak sağlayıcısına ilişkin bilgileri görmek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Tüm Hizmetler**’i seçin.

    ![Tüm hizmetleri seçin](./media/resource-manager-supported-services/more-services.png)

3. **Tüm hizmetler** kutusunda, **Kaynak Gezgini**' ni girin ve ardından **Kaynak Gezgini**' yi seçin.
4. Sağ oku seçerek **sağlayıcılar** ' ı genişletin.

    ![Sağlayıcıları seçin](./media/resource-manager-supported-services/select-providers.png)

5. Görüntülemek istediğiniz kaynak sağlayıcısını ve kaynak türünü genişletin.

    ![Kaynak türünü seçin](./media/resource-manager-supported-services/select-resource-type.png)

6. Tüm bölgelerde Kaynak Yöneticisi desteklenir, ancak dağıttığınız kaynaklar tüm bölgelerde desteklenmeyebilir. Ayrıca, aboneliğiniz üzerinde kaynağı destekleyen bazı bölgeler kullanmanızı önleyen sınırlamalar olabilir. Kaynak Gezgini, kaynak türü için geçerli konumlar görüntüler.

    ![Konumları göster](./media/resource-manager-supported-services/show-locations.png)

7. API sürümü, kaynak sağlayıcısı tarafından yayınlanan REST API işlemlerinin bir sürümüne karşılık gelir. Bir kaynak sağlayıcısı yeni özellikleri sağladığından, REST API yeni bir sürümünü yayınlar. Kaynak Gezgini, kaynak türü için geçerli API sürümlerini görüntüler.

    ![API sürümlerini göster](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure 'daki tüm kaynak sağlayıcılarını ve aboneliğinizin kayıt durumunu görmek için şunu kullanın:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Şuna benzer sonuçlar döndüren:

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Kaynak sağlayıcısı kaydı, aboneliğinizi kaynak sağlayıcısıyla çalışacak şekilde yapılandırır. Kayıt için kapsam her zaman aboneliktir. Varsayılan olarak, birçok kaynak sağlayıcısı otomatik olarak kaydedilir. Ancak, bazı kaynak sağlayıcılarını el ile kaydetmeniz gerekebilir. Kaynak sağlayıcısını kaydetmek için, kaynak sağlayıcısı için `/register/action` işlemini yapmak için izninizin olması gerekir. Bu işlem, Katkıda Bulunan ve Sahip rolleriyle birlikte sunulur.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Şuna benzer sonuçlar döndüren:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Aboneliğinizde kaynak sağlayıcınızdan hala kaynak türleri varsa, bir kaynak sağlayıcısının kaydını silemezsiniz.

Belirli bir kaynak sağlayıcısına ilişkin bilgileri görmek için şunu kullanın:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Şuna benzer sonuçlar döndüren:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Bir kaynak sağlayıcısının kaynak türlerini görmek için şunu kullanın:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Şunu döndürür:

```powershell
batchAccounts
operations
locations
locations/quotas
```

API sürümü, kaynak sağlayıcısı tarafından yayınlanan REST API işlemlerinin bir sürümüne karşılık gelir. Bir kaynak sağlayıcısı yeni özellikleri sağladığından, REST API yeni bir sürümünü yayınlar.

Bir kaynak türü için kullanılabilir API sürümlerini almak için şunu kullanın:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Şunu döndürür:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Tüm bölgelerde Kaynak Yöneticisi desteklenir, ancak dağıttığınız kaynaklar tüm bölgelerde desteklenmeyebilir. Ayrıca, aboneliğiniz üzerinde kaynağı destekleyen bazı bölgeler kullanmanızı önleyen sınırlamalar olabilir.

Kaynak türü için desteklenen konumları almak için kullanın.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Şunu döndürür:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Azure 'daki tüm kaynak sağlayıcılarını ve aboneliğinizin kayıt durumunu görmek için şunu kullanın:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Şuna benzer sonuçlar döndüren:

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Kaynak sağlayıcısı kaydı, aboneliğinizi kaynak sağlayıcısıyla çalışacak şekilde yapılandırır. Kayıt için kapsam her zaman aboneliktir. Varsayılan olarak, birçok kaynak sağlayıcısı otomatik olarak kaydedilir. Ancak, bazı kaynak sağlayıcılarını el ile kaydetmeniz gerekebilir. Kaynak sağlayıcısını kaydetmek için, kaynak sağlayıcısı için `/register/action` işlemini yapmak için izninizin olması gerekir. Bu işlem, Katkıda Bulunan ve Sahip rolleriyle birlikte sunulur.

```azurecli
az provider register --namespace Microsoft.Batch
```

Bu, kaydın devam eden bir ileti döndürür.

Aboneliğinizde kaynak sağlayıcınızdan hala kaynak türleri varsa, bir kaynak sağlayıcısının kaydını silemezsiniz.

Belirli bir kaynak sağlayıcısına ilişkin bilgileri görmek için şunu kullanın:

```azurecli
az provider show --namespace Microsoft.Batch
```

Şuna benzer sonuçlar döndüren:

```azurecli
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Bir kaynak sağlayıcısının kaynak türlerini görmek için şunu kullanın:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Şunu döndürür:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

API sürümü, kaynak sağlayıcısı tarafından yayınlanan REST API işlemlerinin bir sürümüne karşılık gelir. Bir kaynak sağlayıcısı yeni özellikleri sağladığından, REST API yeni bir sürümünü yayınlar.

Bir kaynak türü için kullanılabilir API sürümlerini almak için şunu kullanın:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Şunu döndürür:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Tüm bölgelerde Kaynak Yöneticisi desteklenir, ancak dağıttığınız kaynaklar tüm bölgelerde desteklenmeyebilir. Ayrıca, aboneliğiniz üzerinde kaynağı destekleyen bazı bölgeler kullanmanızı önleyen sınırlamalar olabilir.

Kaynak türü için desteklenen konumları almak için kullanın.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Şunu döndürür:

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Sonraki adımlar

* Kaynak Yöneticisi şablonları oluşturma hakkında bilgi edinmek için bkz. [yazma Azure Resource Manager şablonları](resource-group-authoring-templates.md). 
* Kaynak sağlayıcısı şablon şemalarını görüntülemek için bkz. [şablon başvurusu](/azure/templates/).
* Kaynak sağlayıcılarını Azure hizmetleriyle eşleyen bir liste için bkz. [Azure hizmetleri Için kaynak sağlayıcıları](azure-services-resource-providers.md).
* Bir kaynak sağlayıcısına yönelik işlemleri görüntülemek için bkz. [Azure REST API](/rest/api/).
