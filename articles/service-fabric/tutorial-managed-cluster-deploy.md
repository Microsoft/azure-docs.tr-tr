---
title: Service Fabric yönetilen bir küme dağıtma (Önizleme)
description: Bu öğreticide, test için Service Fabric yönetilen bir küme dağıtacaksınız.
ms.topic: tutorial
ms.date: 08/27/2020
ms.custom: references_regions
ms.openlocfilehash: fd54e6f8c5272c3a8fe0e0162ca2e57968b24642
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791719"
---
# <a name="tutorial-deploy-a-service-fabric-managed-cluster-preview"></a>Öğretici: Service Fabric yönetilen bir küme dağıtma (Önizleme)

Bu öğretici serisinde şunları tartışacağız:

> [!div class="checklist"]
> * Service Fabric yönetilen bir küme dağıtma 
> * [Service Fabric yönetilen bir kümeyi daraltma](tutorial-managed-cluster-scale.md)
> * [Service Fabric yönetilen bir kümede düğüm ekleme ve kaldırma](tutorial-managed-cluster-add-remove-node-type.md)
> * [Service Fabric yönetilen bir kümeye uygulama dağıtma](tutorial-managed-cluster-deploy-app.md)

Serinin bu bölümü aşağıdaki konuları içerir:

> [!div class="checklist"]
> * Azure hesabınıza bağlanma
> * Yeni bir kaynak grubu oluşturma
> * Service Fabric yönetilen bir küme dağıtma
> * Kümeye birincil düğüm türü ekleme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* Henüz bir Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun

* [SERVICE fabrıc SDK ve PowerShell modülünü](service-fabric-get-started.md)yükler.

* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) (veya üzeri) uygulamasını yükler.

## <a name="connect-to-your-azure-account"></a>Azure hesabınıza bağlanma

`<your-subscription>`Azure hesabınız için abonelik dizesiyle değiştirin ve bağlanın:

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <your-subscription>

```

## <a name="create-a-new-resource-group"></a>Yeni bir kaynak grubu oluşturma

Daha sonra, `<your-rg>` `<location>` istenen grup adı ve konumuyla değiştirerek yönetilen Service Fabric kümesi için kaynak grubu oluşturun.

> [!NOTE]
> Genel önizleme için desteklenen bölgeler,,,, `centraluseuap` `eastus2euap` ve içerir `eastasia` `northeurope` `westcentralus` `eastus2` .

```powershell
$resourceGroup = "myResourceGroup"
$location = "EastUS2"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="deploy-a-service-fabric-managed-cluster"></a>Service Fabric yönetilen bir küme dağıtma

### <a name="create-a-service-fabric-managed-cluster"></a>Service Fabric yönetilen kümesi oluşturma

Bu adımda, New-AzServiceFabricManagedCluster PowerShell komutunu kullanarak Service Fabric yönetilen bir küme oluşturacaksınız. Aşağıdaki örnek, myResourceGroup adlı kaynak grubunda myCluster adlı bir küme oluşturur. Bu kaynak grubu, eastus2 bölgesindeki önceki adımda oluşturulmuştur.

Bu adım için aşağıdaki parametreler için kendi değerlerinizi sağlayın:

* **Küme adı**: kümeniz için, *hayal fcluster* gibi benzersiz bir ad girin.
* **Yönetici parolası**: yöneticinin, kümedeki temel alınan VM 'lerde RDP için kullanılacak bir parola girin.
* **Istemci sertifikası parmak izi**: kümenize erişmek için kullanmak istediğiniz istemci sertifikasının parmak izini sağlayın. Bir sertifikanız yoksa, otomatik olarak imzalanan sertifika oluşturmak için [set ve bir sertifika al](../key-vault/certificates/quick-create-portal.md) ' ı izleyin.
* **Küme SKU 'su**: dağıtılacak [Service Fabric yönetilen kümenin türünü](overview-managed-cluster.md#service-fabric-managed-cluster-skus) belirtin. *Temel* SKU kümeleri yalnızca test dağıtımları için tasarlanmıştır ve düğüm türü ekleme veya kaldırma için izin vermez.

```powershell
$clusterName = "<unique cluster name>"
$password = "Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$clientThumbprint = "<certificate thumbprint>"
$clusterSku = "Standard"

New-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroup -Location $location -ClusterName $clusterName -ClientCertThumbprint $clientThumbprint -ClientCertIsAdmin -AdminPassword $password -Sku $clusterSKU -Verbose
```

### <a name="add-a-primary-node-type-to-the-service-fabric-managed-cluster"></a>Service Fabric yönetilen kümeye birincil düğüm türü ekleme

Bu adımda, yeni oluşturduğunuz kümeye bir birincil düğüm türü ekleyeceksiniz. Her Service Fabric kümesi en az bir birincil düğüm türüne sahip olmalıdır.

Bu adım için aşağıdaki parametreler için kendi değerlerinizi sağlayın:

* **Düğüm türü adı**: kümenize eklenecek düğüm türü için benzersiz bir ad girin (örneğin, "NT1").

> [!NOTE]
> Eklenmekte olan düğüm türü kümedeki ilk veya tek düğüm türüdür, birincil özellik kullanılmalıdır.

```powershell
$nodeType1Name = "NT1"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeType1Name -Primary -InstanceCount 5
```

Bu komutun tamamlanması birkaç dakika sürebilir.

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

### <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Dağıtım tamamlandıktan sonra, Portal 'daki Service Fabric yönetilen küme kaynağına Genel Bakış sayfasında Service Fabric Explorer değerini bulun. Bir sertifika istendiğinde, PowerShell komutunda istemci parmak izinin sağlandığı sertifikayı kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Bu adımda, ilk Service Fabric yönetilen kümemizi oluşturup dağıttık. Bir kümenin ölçeklendirilmesi hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Service Fabric yönetilen bir kümeyi genişletme](tutorial-managed-cluster-scale.md)
