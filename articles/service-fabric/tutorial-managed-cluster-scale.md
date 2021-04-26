---
title: Service Fabric yönetilen kümenin ölçeğini genişletme (Önizleme)
description: Bu öğreticide, Service Fabric yönetilen bir kümenin düğüm türünü nasıl ölçeklendireceğinizi öğrenin.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 769bcb339b2cc1419c7a3d92d0f08130029a9f95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785439"
---
# <a name="tutorial-scale-out-a-service-fabric-managed-cluster-preview"></a>Öğretici: Service Fabric yönetilen bir kümeyi genişletme (Önizleme)

Bu öğretici serisinde şunları tartışacağız:

> [!div class="checklist"]
> * [Service Fabric yönetilen bir küme nasıl dağıtılır.](tutorial-managed-cluster-deploy.md)
> * Service Fabric yönetilen bir kümeyi daraltma
> * [Service Fabric yönetilen bir kümede düğüm türlerini ekleme ve kaldırma](tutorial-managed-cluster-add-remove-node-type.md)
> * [Service Fabric yönetilen bir kümeye uygulama dağıtma](tutorial-managed-cluster-deploy-app.md)

Serinin bu bölümü aşağıdaki konuları içerir:

> [!div class="checklist"]
> * Service Fabric yönetilen bir küme düğümünü ölçeklendirme

## <a name="prerequisites"></a>Önkoşullar

* Service Fabric yönetilen bir küme (bkz. [*yönetilen küme dağıtma*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) veya üzeri (bkz. [*Install Azure PowerShell*](/powershell/azure/install-az-ps)).

## <a name="scale-a-service-fabric-managed-cluster"></a>Service Fabric yönetilen bir kümeyi ölçeklendirme
Örnek sayısını, ölçeklendirmek istediğiniz düğüm türündeki düğümlerin sayısını artırmak veya azaltmak için değiştirin. Düğüm türü adlarını, küme dağıtımınızdaki veya Service Fabric Explorer Azure Resource Manager şablonunda (ARM şablonunda) bulabilirsiniz.  

> [!NOTE]
> Düğüm türü birincili ise, temel bir SKU kümesi için 3 düğüm ve standart SKU kümesi için 5 düğüm altına gidemeyeceksiniz.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "FE"
$instanceCount = "7"

Set-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -name $nodeTypeName -InstanceCount $instanceCount -Verbose
```

Küme otomatik olarak yükseltmeye başlayacaktır ve birkaç dakika sonra ek düğümleri görürsünüz.

## <a name="next-steps"></a>Sonraki adımlar

Bu adımda Service Fabric yönetilen bir kümede düğüm türünü ölçeklendirdik. Düğüm türlerini ekleme ve kaldırma hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Service Fabric yönetilen bir kümeye düğüm türleri ekleme ve kaldırma](tutorial-managed-cluster-add-remove-node-type.md)
