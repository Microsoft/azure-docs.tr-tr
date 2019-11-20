---
title: Azure Service Fabric düğüm türünü ölçeklendirme | Microsoft Docs
description: Bir sanal makine ölçek kümesi ekleyerek bir Service Fabric kümesini ölçeklendirmeyi öğrenin.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: atsenthi
ms.openlocfilehash: 272bc571a0ea71fd6e7bd45a426460d2e0faf1d7
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599290"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Service Fabric kümesi birincil düğüm türünü büyütme
Bu makalede, sanal makine kaynaklarını artırarak Service Fabric kümesi birincil düğüm türünün nasıl ölçeklenebileceğinizi açıklamaktadır. Service Fabric küme, mikro hizmetlerinizin dağıtıldığı ve yönetildiği, ağa bağlı bir sanal veya fiziksel makine kümesidir. Bir kümenin parçası olan makine veya VM, düğüm olarak adlandırılır. Sanal Makine Ölçek Kümeleri, bir sanal makine koleksiyonunu bir küme olarak dağıtmak ve yönetmek için kullandığınız bir Azure işlem kaynağıdır. Bir Azure kümesinde tanımlanan her düğüm türü [ayrı bir ölçek kümesi olarak ayarlanır](service-fabric-cluster-nodetypes.md). Her düğüm türü ayrıca yönetilebilir. Service Fabric kümesi oluşturduktan sonra, küme düğümü türünü dikey olarak ölçeklendirebilir (düğümlerin kaynaklarını değiştirebilir) veya düğüm türü VM 'lerinin işletim sistemini yükseltebilirsiniz.  Küme üzerinde iş yükleri çalışırken bile kümeyi istediğiniz zaman ölçeklendirebilirsiniz.  Küme ölçeklenirken uygulamalarınız da otomatik olarak ölçeklendirilir.

> [!WARNING]
> Küme durumu uygun değilse, birincil NodeType VM SKU 'sunu değiştirmeye başlamaın. Küme durumu uygun değilse, VM SKU 'sunu değiştirmeye çalışırsanız kümeyi yalnızca daha fazla sabitleyecaksınız.
>
> Bir ölçek kümesi/düğüm türünün sanal makine SKU 'sunu, [gümüş dayanıklılık veya daha büyük](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)bir zamanda çalışmadığı takdirde değiştirmenizi öneririz. VM SKU 'SU boyutunun değiştirilmesi, veri bozucu bir yerinde altyapı işlemidir. Bu değişikliği gecikme veya izlemeye yönelik bir özellik olmadan, işlem durum bilgisi olmayan hizmetler için veri kaybına neden olabilir veya durum bilgisiz iş yükleri için bile öngörülemeyen çalışma sorunlarına neden olabilir. Bu, durum bilgisi olmayan Service Fabric sistem hizmetlerini çalıştıran birincil düğüm türü ya da durum bilgisi olan uygulama iş yüklerinizi çalıştıran herhangi bir düğüm türü anlamına gelir.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Birincil düğüm türü VM 'lerinin boyutunu ve işletim sistemini yükseltme
Birincil düğüm türü VM 'lerinin sanal makine boyutunu ve işletim sistemini güncelleştirme süreci aşağıda verilmiştir.  Yükseltmeden sonra birincil düğüm türü VM 'Ler boyut standart D4_V2 ve Windows Server 2016 Datacenter 'u kapsayıcılar ile çalıştırır.

> [!WARNING]
> Bir üretim kümesinde bu yordamı denemeden önce, örnek şablonları araştırmayı ve işlemi bir test kümesine karşı doğrulamanızı öneririz. Küme bir süre için de kullanılamaz. Paralel olarak aynı NodeType olarak belirtilen birden fazla VMSS üzerinde değişiklik yapamazsınız; Her NodeType VMSS 'de yapılan değişiklikleri ayrı ayrı uygulamak için ayrılmış dağıtım işlemleri gerçekleştirmeniz gerekir.

1. Bu örnek [şablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) ve [parametre](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) dosyalarını kullanarak, ilk kümeyi iki düğüm türü ve iki ölçek kümesi (düğüm başına bir ölçek kümesi) dağıtın.  Her iki ölçek kümesi de standart D2_V2 boyutlardır ve Windows Server 2012 R2 Datacenter çalıştırıyor.  Kümenin temel yükseltme işleminin tamamlanmasını bekleyin.   
2. İsteğe bağlı-kümeye durum bilgisi olan bir örnek dağıtın.
3. Birincil düğüm türü VM 'Leri yükseltmeye karar verdikten sonra, bu örnek [şablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) ve [parametre](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) dosyalarını kullanarak birincil düğüm türüne yeni bir ölçek kümesi ekleyin, böylece birincil düğüm türünün artık iki ölçek kümesi vardır.  Sistem Hizmetleri ve kullanıcı uygulamaları iki farklı ölçek kümesindeki VM 'Ler arasında geçiş yapabilir.  Yeni ölçek kümesi VM 'Leri boyut standardı D4_V2 ve kapsayıcılarla Windows Server 2016 Datacenter çalıştırır.  Yeni ölçek kümesiyle yeni bir yük dengeleyici ve genel IP adresi de eklenir.  
    Şablonda yeni ölçek kümesini bulmak için, *vmNodeType2Name* parametresi tarafından adlandırılan "Microsoft. COMPUTE/virtualMachineScaleSets" kaynağını arayın.  Yeni ölçek kümesi, > virtualMachineProfile-> extensionProfile-> Uzantıları-> Özellikleri-> Ayarlar-> nodeTypeRef ayarı kullanılarak birincil düğüm türüne eklenir.
4. Küme durumunu denetleyin ve tüm düğümlerin sağlıklı olduğunu doğrulayın.
5. Düğüm kaldırma amacına sahip birincil düğüm türünün eski ölçek kümesindeki düğümleri devre dışı bırakın. Tümünü tek seferde devre dışı bırakabilirsiniz ve işlemler sıraya alınır. Tüm düğümler devre dışı bırakılıncaya kadar bekleyin, bu işlem biraz zaman alabilir.  Düğüm türündeki eski düğümler devre dışı bırakıldığı için, sistem hizmetleri ve çekirdek düğümleri, birincil düğüm türünde yeni ölçek kümesinin VM 'lerine geçirilir.
6. Eski ölçek kümesini birincil düğüm türünden kaldırın.
7. Eski ölçek kümesiyle ilişkili yük dengeleyiciyi kaldırın. Yeni genel IP adresi ve yük dengeleyici yeni ölçek kümesi için yapılandırıldığında küme kullanılamaz.  
8. Bir değişkende eski birincil düğüm türü ölçek kümesiyle ilişkili genel IP adresinin DNS ayarlarını depolayın ve bu genel IP adresini kaldırın.
9. Yeni birincil düğüm türü ölçek kümesiyle ilişkili genel IP adresinin DNS ayarlarını, silinen genel IP adresinin DNS ayarları ile değiştirin.  Kümeye artık yeniden ulaşılabilir.
10. Düğümlerin düğüm durumunu kümeden kaldırın.  Eski ölçek kümesinin dayanıklılık düzeyi gümüş veya altın ise, bu adım sistem tarafından otomatik olarak gerçekleştirilir.
11. Durum bilgisi olan uygulamayı önceki bir adımda dağıttıysanız, uygulamanın işlevsel olduğunu doğrulayın.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.json" -Verbose

# Connect to the cluster and check the cluster health.
$ClusterName= "sfupgradetest.southcentralus.cloudapp.azure.com:19000"
$thumb="F361720F4BD5449F6F083DDE99DC51A86985B25B"

Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Get-ServiceFabricClusterHealth

# Deploy a new scale set into the primary node type.  Create a new load balancer and public IP address for the new scale set.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.json" -Verbose

# Check the cluster health again. All 15 nodes should be healthy.
Get-ServiceFabricClusterHealth

# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}

Write-Host "Checking node status..."
foreach($name in $nodeNames){
 
    $state = Get-ServiceFabricNode -NodeName $name 

    $loopTimeout = 50

    do{
        Start-Sleep 5
        $loopTimeout -= 1
        $state = Get-ServiceFabricNode -NodeName $name
        Write-Host "$name state: " $state.NodeDeactivationInfo.Status
    }

    while (($state.NodeDeactivationInfo.Status -ne "Completed") -and ($loopTimeout -ne 0))
    

    if ($state.NodeStatus -ne [System.Fabric.Query.NodeStatus]::Disabled)
    {
        Write-Error "$name node deactivation failed with state" $state.NodeStatus
        exit
    }
}

# Remove the scale set
$scaleSetName="NTvm1"
Remove-AzVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="next-steps"></a>Sonraki adımlar
* [Kümeye düğüm türü eklemeyi](virtual-machine-scale-set-scale-node-type-scale-out.md) öğrenin
* [Uygulama ölçeklenebilirliği](service-fabric-concepts-scalability.md)hakkında bilgi edinin.
* [Bir Azure kümesini içinde veya dışarı ölçeklendirin](service-fabric-tutorial-scale-cluster.md).
* Akıcı Azure işlem SDK 'sını kullanarak [bir Azure kümesini programlı bir şekilde ölçeklendirin](service-fabric-cluster-programmatic-scaling.md) .
* [Tek başına kümeyi içinde veya dışarı ölçeklendirin](service-fabric-cluster-windows-server-add-remove-nodes.md).

