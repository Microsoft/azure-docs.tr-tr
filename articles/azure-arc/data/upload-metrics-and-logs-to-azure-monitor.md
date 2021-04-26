---
title: Kullanım verilerini, ölçümleri ve günlükleri Azure Izleyici 'ye yükleme
description: Kaynak envanterini, kullanım verilerini, ölçümleri ve günlükleri Azure Izleyici 'ye yükleme
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: a522a650413be056ff64d26e90b6c15cf88d9a7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643499"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Kullanım verilerini, ölçümleri ve günlükleri Azure Izleyici 'ye yükleme

Düzenli olarak, faturalama amaçları, ölçümleri izlemek ve günlükleri Azure 'a yüklemek için kullanım bilgilerini dışarı aktarabilirsiniz. Bu üç türden verilerin herhangi birini dışarı ve karşıya yükleme işlemi, Azure 'daki veri denetleyicisi, SQL yönetilen örnek ve PostgreSQL hiper ölçek sunucu grubu kaynaklarını da oluşturur ve güncelleştirir.

> [!NOTE] 
> Önizleme dönemi boyunca, Azure Arc etkin veri hizmetlerini kullanma maliyeti yoktur.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Kullanım verilerini, ölçümleri veya günlükleri karşıya yüklemeden önce şunları yapmanız gerekir:

* Araçları yükleme 
* [`Microsoft.AzureArcData`Kaynak sağlayıcısını kaydetme](#register-the-resource-provider) 
* [Hizmet sorumlusunu oluşturma](#create-service-principal)

## <a name="install-tools"></a>Araçları yükleme

Gerekli araçlar şunları içerir: 
* Azure CLı (az) 
* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 

Bkz. [yüklemeyi araçları](./install-client-tools.md).

## <a name="register-the-resource-provider"></a>Kaynak sağlayıcısını kaydetme

Ölçümleri veya Kullanıcı verilerini Azure 'a yüklemeden önce, Azure aboneliğinizin kaynak sağlayıcısının kayıtlı olduğundan emin olmanız gerekir `Microsoft.AzureArcData` .

Kaynak sağlayıcısını doğrulamak için şu komutu çalıştırın:

```azurecli
az provider show -n Microsoft.AzureArcData -o table
```

Kaynak sağlayıcısı şu anda aboneliğinizde kayıtlı değilse, kaydı gerçekleştirebilirsiniz. Kaydetmek için aşağıdaki komutu çalıştırın.  Bu komutun tamamlanması bir veya iki dakika kadar sürebilir.

```azurecli
az provider register -n Microsoft.AzureArcData --wait
```

## <a name="create-service-principal"></a>Hizmet sorumlusu oluşturma

Hizmet sorumlusu, kullanım ve ölçüm verilerini karşıya yüklemek için kullanılır.

Ölçüm karşıya yükleme hizmeti sorumlusunu oluşturmak için şu komutları izleyin:

> [!NOTE]
> Hizmet sorumlusu oluşturmak için [Azure 'da belirli izinler](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)gerekir.

Hizmet sorumlusu oluşturmak için aşağıdaki örneği güncelleştirin. `<ServicePrincipalName>`, `SubscriptionId` Ve `resourcegroup` değerlerini ile değiştirin ve komutunu çalıştırın:

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName> --role Contributor --scopes /subscriptions/{SubscriptionId}/resourceGroups/{resourcegroup}
```

Daha önce hizmet sorumlusu oluşturduysanız ve yalnızca geçerli kimlik bilgilerini almanız gerekiyorsa, kimlik bilgisini sıfırlamak için aşağıdaki komutu çalıştırın.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

Örneğin, adlı bir hizmet sorumlusu oluşturmak için `azure-arc-metrics` aşağıdaki komutu çalıştırın

```azurecli
az ad sp create-for-rbac --name azure-arc-metrics --role Contributor --scopes /subscriptions/a345c178a-845a-6a5g-56a9-ff1b456123z2/resourceGroups/myresourcegroup
```

Örnek çıktı:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

`appId` `password` `tenant` Daha sonra kullanmak üzere bir ortam değişkeninde, ve değerlerini kaydedin. 

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

::: zone-end

Hizmet sorumlusunu oluşturduktan sonra, hizmet sorumlusunu uygun role atayın. 

## <a name="assign-roles-to-the-service-principal"></a>Hizmet sorumlusuna roller atama

Hizmet sorumlusunu, `Monitoring Metrics Publisher` veritabanı örneği kaynaklarınızın bulunduğu abonelikte role atamak için bu komutu çalıştırın:

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> Windows ortamından çalışırken rol adları için çift tırnak kullanmanız gerekir.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/{SubscriptionID}/resourceGroups/{resourcegroup}

```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/{SubscriptionID}/resourceGroups/{resourcegroup}
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/{SubscriptionID}/resourceGroups/{resourcegroup}
```

::: zone-end

Örnek çıktı:

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

Uygun role atanan hizmet sorumlusu ile ölçümleri veya Kullanıcı verilerini karşıya yüklemeye devam edebilirsiniz. 

## <a name="upload-logs-metrics-or-user-data"></a>Günlükleri, ölçümleri veya Kullanıcı verilerini karşıya yükleme

Günlükleri, ölçümleri veya Kullanıcı verilerini karşıya yüklemeye yönelik belirli adımlar, karşıya yüklediğiniz bilgi türüne bağlı olarak değişiklik gösterir. 

[Günlükleri Azure Izleyici 'ye yükleme](upload-logs.md)

[Ölçümleri Azure Izleyici 'ye yükleyin](upload-metrics.md)

[Kullanım verilerini Azure Izleyici 'ye yükleme](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Kullanım, ölçüm verme ve karşıya yükleme hakkında genel yönergeler

Azure Arc etkin veri Hizmetleri üzerinde oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri faturalandırma ve izleme amacıyla günlüğe kaydedilir. Bu CRUD işlemlerini izleyen ve tüketimi uygun şekilde hesaplayan arka plan hizmetleri mevcuttur. Kullanım veya tüketimin gerçek hesaplaması, zamanlanan bir şekilde gerçekleşir ve arka planda yapılır. 

Önizleme süresince bu işlem gecelik olur. Genel rehberlik, kullanımı günde yalnızca bir kez karşıya yüklemek içindir. Kullanım bilgileri aktarıldığında ve aynı 24 saatlik süre içinde birden çok kez karşıya yüklendiğinde, yalnızca kaynak envanteri Azure portal ' de güncelleştirilir ancak kaynak kullanımı değildir.

Azure izleyici, ölçümleri karşıya yüklemek için yalnızca son 30 dakikalık verileri kabul eder ([daha fazla bilgi edinin](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)). Ölçüm Yükleme Kılavuzu, verileri dışa aktarma dosyası oluşturulduktan hemen sonra, Azure portal ' de tüm veri kümesini görüntüleyebilmeniz için, ölçümleri karşıya yüklemedir. Örneğin, ölçümleri 2:00 PM tarihinde ve 2:50 PM 'de karşıya yükle komutunu çalıştırdıysanız. Azure Izleyici yalnızca son 30 dakikalık verileri kabul ettiğinden portalda herhangi bir veri göremeyebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

[Hizmet sorumluları hakkında bilgi edinin](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal)

[Faturalama verilerini Azure 'a yükleyin ve Azure portal görüntüleyin](view-billing-data-in-azure.md)

[Azure Arc Data Controller kaynağını Azure portal görüntüle](view-data-controller-in-azure-portal.md)
