---
title: Yönetilen kaynakları güncelleştirme
description: Yönetilen bir Azure uygulaması için yönetilen kaynak grubundaki kaynaklar üzerinde nasıl çalışabileceğinizi açıklar.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 3dbb88d53a961e277e102d7eec580309951c73b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75651221"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Azure yönetilen uygulaması için yönetilen kaynak grubundaki kaynaklarla çalışma

Bu makalede, yönetilen bir uygulamanın parçası olarak dağıtılan kaynakların nasıl güncelleştirileceğini açıklanmaktadır. Yönetilen bir uygulamanın yayımcısı olarak, yönetilen kaynak grubundaki kaynaklara erişebilirsiniz. Bu kaynakları güncelleştirmek için, yönetilen bir uygulamayla ilişkili yönetilen kaynak grubunu bulmanız ve bu kaynak grubundaki kaynağa erişmeniz gerekir.

Bu makalede yönetilen uygulamayı [Azure Yönetim Hizmetleri örnek projesi Ile yönetilen Web uygulamasında (IaaS)](https://github.com/Azure/azure-managedapp-samples/tree/master/Managed%20Application%20Sample%20Packages/201-managed-web-app) dağıttığınız varsayılmaktadır. Bu yönetilen uygulama **Standard_D1_v2** bir sanal makine içerir. Bu yönetilen uygulamayı dağıtmadıysanız, yönetilen bir kaynak grubunu güncelleştirme adımlarını öğrenmek için bu makaleyi kullanmaya devam edebilirsiniz.

Aşağıdaki görüntüde dağıtılan yönetilen uygulama gösterilmektedir.

![Dağıtılan yönetilen uygulama](./media/update-managed-resources/deployed.png)

Bu makalede, Azure CLı 'yi kullanarak şunları yapabilirsiniz:

* Yönetilen uygulamayı tanımla
* Yönetilen kaynak grubunu tanımla
* Yönetilen kaynak grubundaki sanal makine kaynakları (ler) i tanımla
* VM boyutunu (aşırı daha küçük bir boyuta veya daha fazla yük desteği sağlamak için daha büyük) değiştirin
* Yönetilen kaynak grubuna izin verilen konumları belirten bir ilke atama

## <a name="get-managed-application-and-managed-resource-group"></a>Yönetilen uygulamayı ve yönetilen kaynak grubunu al

Bir kaynak grubunda yönetilen uygulamaları almak için şunu kullanın:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Yönetilen kaynak grubunun KIMLIĞINI almak için şunu kullanın:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Yönetilen kaynak grubundaki VM 'Leri yeniden boyutlandır

Yönetilen kaynak grubundaki sanal makineleri görmek için, yönetilen kaynak grubunun adını belirtin.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

VM 'lerin boyutunu güncelleştirmek için şunu kullanın:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

İşlem tamamlandıktan sonra, uygulamanın standart D2 v2 üzerinde çalıştığını doğrulayın.

![Standart D2 v2 kullanan yönetilen uygulama](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>İlkeyi yönetilen kaynak grubuna Uygula

Yönetilen kaynak grubunu alın ve bu kapsamda ilke atamasını yapın. İlke **e56962a6-4747-49cd-b67b-bf8b01975c4c** , izin verilen konumları belirtmek için yerleşik bir ilkedir.

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

İzin verilen konumları görmek için şunu kullanın:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

İlke ataması portalda görüntülenir.

![İlke atamasını görüntüle](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Sonraki adımlar

* Yönetilen uygulamalara giriş için [Yönetilen uygulamalara genel bakış](overview.md) konusunu inceleyin.
* Örnek projeler için bkz. [Azure yönetilen uygulamalar Için örnek projeler](sample-projects.md).
