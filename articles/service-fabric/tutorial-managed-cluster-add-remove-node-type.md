---
title: Service Fabric yönetilen kümenin düğüm türlerini ekleme ve kaldırma (Önizleme)
description: Bu öğreticide, Service Fabric yönetilen bir kümenin düğüm türlerini ekleme ve kaldırma hakkında bilgi edinin.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: bb33512652677fc4e46d8ba3668dca985bbcfe01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791248"
---
# <a name="tutorial-add-and-remove-node-types-from-a-service-fabric-managed-cluster-preview"></a>Öğretici: Service Fabric yönetilen bir kümeden düğüm türleri ekleme ve kaldırma (Önizleme)

Bu öğretici serisinde şunları tartışacağız:

> [!div class="checklist"]
> * [Service Fabric yönetilen bir küme dağıtma](tutorial-managed-cluster-deploy.md)
> * [Service Fabric yönetilen bir kümeyi daraltma](tutorial-managed-cluster-scale.md)
> * Service Fabric yönetilen bir kümede düğüm ekleme ve kaldırma
> * [Service Fabric yönetilen bir kümeye uygulama dağıtma](tutorial-managed-cluster-deploy-app.md)

Serinin bu bölümü aşağıdaki konuları içerir:

> [!div class="checklist"]
> * Service Fabric yönetilen bir kümeye düğüm türü ekleme
> * Service Fabric yönetilen bir kümeden düğüm türünü silme

## <a name="prerequisites"></a>Önkoşullar

* Service Fabric yönetilen bir küme (bkz. [*yönetilen küme dağıtma*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) veya üzeri (bkz. [*Install Azure PowerShell*](/powershell/azure/install-az-ps)).

## <a name="add-a-node-type-to-a-service-fabric-managed-cluster"></a>Service Fabric yönetilen bir kümeye düğüm türü ekleme

Bir Azure Resource Manager şablonu, PowerShell veya CLı aracılığıyla Service Fabric yönetilen bir kümeye düğüm türü ekleyebilirsiniz. Bu öğreticide, Azure PowerShell kullanarak bir düğüm türü ekleyeceğiz.

Yeni bir düğüm türü oluşturmak için üç özellik tanımlamanız gerekir:

* **Düğüm türü adı**: kümedeki var olan herhangi bir düğüm türünden benzersiz olan addır.
* **Örnek sayısı**: yeni düğüm türünün ilk düğüm sayısı.
* **VM boyutu**: düğümler IÇIN VM SKU 'su. Belirtilmemişse, varsayılan değer *Standard_D2* kullanılır.

> [!NOTE]
> Eklenmekte olan düğüm türü kümedeki ilk veya tek düğüm türüdür, birincil özellik kullanılmalıdır.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "nt2"
$vmSize = "Standard_D2_v2"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeTypeName -InstanceCount 3 -vmSize $vmSize
```

## <a name="remove-a-node-type-from-a-service-fabric-managed-cluster"></a>Service Fabric yönetilen kümeden düğüm türünü kaldırma

Service Fabric yönetilen bir kümeden düğüm türünü kaldırmak için PowerShell veya CLı kullanmanız gerekir. Bu öğreticide, Azure PowerShell kullanarak bir düğüm türünü kaldıracağız.

> [!NOTE]
> Kümedeki tek birincil düğüm türü ise, birincil düğüm türünü kaldırmak mümkün değildir.  

Düğüm türünü kaldırmak için:

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "myCluster"
$nodeTypeName = "nt2"

Remove-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName  -Name $nodeTypeName
```

## <a name="next-steps"></a>Sonraki adımlar

 Bu bölümde, düğüm türlerini ekledik ve sildik. Service Fabric yönetilen bir kümeye uygulama dağıtmayı öğrenmek için bkz.:

> [!div class="nextstepaction"]
> [Service Fabric yönetilen bir kümeye uygulama dağıtma](tutorial-managed-cluster-deploy-app.md)
