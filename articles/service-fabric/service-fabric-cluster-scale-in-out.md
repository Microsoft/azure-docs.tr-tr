---
title: Bir Service Fabric kümesini içinde veya dışarı ölçeklendirin
description: Her düğüm türü/sanal makine ölçek kümesi için otomatik ölçek kuralları ayarlayarak, talebe eşleştirmek için bir Service Fabric kümesini içinde veya dışarı ölçeklendirin. Service Fabric kümesine düğüm ekleme veya kaldırma
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 6ee04c73b75d6b335e450ff816c51f0a3089b918
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94409969"
---
# <a name="scale-a-cluster-in-or-out"></a>Bir kümenin ölçeğini daraltma veya genişletme

> [!WARNING]
> Ölçeklendirmeye başlamadan önce bu bölümü okuyun

İşlem kaynaklarını kaynak olarak ölçeklendirme uygulama iş yükünüz kasıtlı olarak planlanmasını gerektirir, neredeyse her zaman bir üretim ortamında tamamlanması için bir saatten daha uzun sürer ve iş yükünüzü ve iş bağlamını anlamanız gerekir; Aslında bu etkinliği daha önce yapmadıysanız, bu belgenin geri kalanına devam etmeden önce [Service Fabric küme kapasitesi planlama konularını](service-fabric-cluster-capacity.md)okuyup öğrenerek başlamanız önerilir. Bu öneri, istenmeyen bir LiveSite sorunlarından kaçınmaktır ve ayrıca üretim dışı bir ortama karşı gerçekleştirmeye karar vereceğiniz işlemleri başarıyla test etmeniz önerilir. Dilediğiniz zaman, [üretim sorunlarını rapor edebilir veya Azure için ücretli destek isteyebilirsiniz](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). Uygun içeriğe sahip olan bu işlemleri gerçekleştirmek için ayrılan mühendisler için, bu makale ölçeklendirme işlemlerini anlatmaktadır, ancak hangi işlemlerin kullanım için uygun olduğunu karar vermeniz ve anlamanız gerekir; hangi kaynakların Ölçeklendirilecek (CPU, depolama, bellek), hangi yönle (dikey veya yatay) ve gerçekleştirilecek işlemler (kaynak Şablon dağıtımı, Portal, PowerShell/CLı) gibi.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Otomatik ölçeklendirme kurallarını veya el ile Service Fabric bir kümeyi ölçeklendirin veya dışarı ölçeklendirin
Sanal Makine Ölçek Kümeleri, bir sanal makine koleksiyonunu bir küme olarak dağıtmak ve yönetmek için kullanabileceğiniz bir Azure işlem kaynağıdır. Bir Service Fabric kümesinde tanımlanan her düğüm türü ayrı bir sanal makine ölçek kümesi olarak ayarlanır. Her düğüm türü daha sonra bağımsız olarak veya dışarı ölçeklenebilen, farklı bağlantı noktası kümelerine açık olabilir ve farklı kapasite ölçümleri olabilir. [Service Fabric node Types](service-fabric-cluster-nodetypes.md) belgesinde bunun hakkında daha fazla bilgi edinin. Kümenizdeki Service Fabric düğüm türleri arka uçta sanal makine ölçek kümeleri yapıldığından, her düğüm türü/sanal makine ölçek kümesi için otomatik ölçek kuralları ayarlamanız gerekir.

> [!NOTE]
> Aboneliğiniz, bu kümeyi oluşturan yeni VM 'Leri eklemek için yeterli çekirdeğe sahip olmalıdır. Şu anda bir model doğrulaması yok, bu nedenle kota limitlerinin herhangi birine isabet edildiğinde bir dağıtım zamanı hatası alırsınız. Ayrıca, tek düğümlü bir tür, VMSS başına 100 düğümü aşamaz. Hedeflenen ölçeğe ulaşmak için VMSS 'leri eklemeniz gerekebilir ve otomatik ölçeklendirme, VMSS 'leri otomatik olarak eklemez. VMSS 'leri canlı bir kümeye yerinde eklemek zorlu bir görevdir ve bu durum genellikle, kullanıcıların oluşturma zamanında sağlanan uygun düğüm türleriyle yeni kümeler sağlaması ile sonuçlanır; [küme kapasitesini](./service-fabric-cluster-capacity.md) uygun şekilde planlayın. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Ölçeklemek için düğüm türü/sanal makine ölçek kümesini seçin
Şu anda, Portal 'ı kullanarak sanal makine ölçek kümeleri için otomatik ölçek kurallarını belirleyemeyeceksiniz Service Fabric kümesi oluşturmak için, düğüm türlerini listelemek üzere Azure PowerShell (1.0 +) kullanmamıza ve bunlara otomatik ölçek kuralları eklemesine izin verin.

Kümenizi oluşturan sanal makine ölçek kümesinin listesini almak için aşağıdaki cmdlet 'leri çalıştırın:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Düğüm türü/sanal makine ölçek kümesi için otomatik ölçek kuralları ayarla
Kümenizin birden çok düğüm türü varsa, ölçeğini ölçeklendirmek istediğiniz her düğüm türü/sanal makine ölçek kümesi (ın veya out) için yineleyin. Otomatik ölçeklendirmeyi ayarlamadan önce sahip olmanız gereken düğüm sayısını dikkate alın. Birincil düğüm türü için gereken düğüm sayısı alt sınırı seçmiş olduğunuz güvenilirlik düzeyi tarafından yönetilir. [Güvenilirlik düzeyleri](service-fabric-cluster-capacity.md)hakkında daha fazla bilgi edinin.

> [!NOTE]
> Birincil düğüm türünde minimum sayıdan küçük olacak şekilde ölçekleme, kümeyi kararsız hale getirir veya hatta onu aşağı taşıyın. Bu, uygulamalarınız ve sistem hizmetleri için veri kaybına neden olabilir.
> 
> 

Şu anda otomatik ölçeklendirme özelliği, uygulamalarınızın Service Fabric için rapor olabileceği yüklemeler tarafından çalıştırılmamaktadır. Bu nedenle, aldığınız otomatik ölçek, sanal makine ölçek kümesi örneklerinin her biri tarafından yayılan performans sayaçlarıyla tamamen çalıştırılır.  

[Her bir sanal makine ölçek kümesi için otomatik ölçeklendirmeyi ayarlamak için](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)bu yönergeleri izleyin.

> [!NOTE]
> Bir senaryoya göre, düğüm türü bir altın veya gümüş [dayanıklılık düzeyine][durability] sahip değilse, [Remove-ServiceFabricNodeState cmdlet 'ini](/powershell/module/servicefabric/remove-servicefabricnodestate) uygun düğüm adıyla çağırmanız gerekir. Bronz dayanıklılık için, aynı anda birden fazla düğümde ölçeklendirilmesi önerilmez.
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>VM 'Leri bir düğüm türüne/sanal makine ölçek kümesine el ile ekleme

Ölçeği genişlettiğinizde, ölçek kümesine daha fazla sanal makine örneği eklersiniz. Bu örnekler, Service Fabric tarafından kullanılan düğümler haline gelir. Service Fabric, ölçek kümesine ne zaman daha fazla örnek eklendiğini bilir (ölçek genişletilerek) ve otomatik olarak tepki verir. 

> [!NOTE]
> VM 'Lerin eklenmesi zaman alır, bu nedenle eklemeleri anlık olarak beklememeyi beklemez. Kapasite iyi bir şekilde daha fazla, çoğaltma/hizmet örnekleri için VM kapasitesini kullanabilmek için 10 dakikadan fazla süre önce bir süre daha ekleyin.
> 

### <a name="add-vms-using-a-template"></a>Şablon kullanarak VM ekleme
Her bir düğüm türündeki VM sayısını değiştirmek için [hızlı başlangıç şablonu galerisindeki](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) örnek/yönergeleri izleyin. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>PowerShell veya CLı komutlarını kullanarak VM ekleme
Aşağıdaki kod, ada göre bir ölçek kümesi alır ve ölçek kümesinin **kapasitesini** 1 artırır.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Bu kod, kapasiteyi 6 olarak ayarlar.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>VM 'Leri bir düğüm türünden/sanal makine ölçek kümesinden el ile kaldırma
Düğüm türünde ölçeklendirirseniz, sanal makine örneklerini ölçek kümesinden kaldırırsınız. Düğüm türü dayanıklılık düzeyi ise, Service Fabric ne olduğunu farkında değildir ve bir düğümün kayıp olduğunu bildirir. Daha sonra Service Fabric, küme için kötü bir durum olduğunu bildirir. Bu hatalı durumu engellemek için, düğümü kümeden açıkça kaldırmanız ve düğüm durumunu kaldırmanız gerekir.

Service Fabric sistem hizmetleri kümenizdeki birincil düğüm türünde çalışır. Birincil düğüm türünde ölçeklendirilirken, [güvenilirlik katmanının](service-fabric-cluster-capacity.md) ne kadar düşük olduğuna göre örnek sayısında hiçbir zaman ölçeklendirin. 
 
Durum bilgisi olan bir hizmet için, hizmetinizin kullanılabilirliği ve bakım durumunu korumak üzere her zaman bir kaç düğüm olması gerekir. En azından, Bölüm/hizmet için hedef çoğaltma kümesi sayısına eşit olan düğüm sayısının olması gerekir.

### <a name="remove-the-service-fabric-node"></a>Service Fabric düğümünü kaldırma

Düğüm durumunu el ile kaldırma adımları yalnızca *bronz* dayanıklılık katmanı olan düğüm türleri için geçerlidir.  *Gümüş* ve *altın* dayanıklılık katmanı için, bu adımlar platform tarafından otomatik olarak yapılır. Dayanıklılık hakkında daha fazla bilgi için bkz. [Service Fabric küme kapasitesi planlaması][durability].

>[!NOTE]
> Dayanıklılık düzeyi Gold veya gümüş etkin olan herhangi bir sanal makine ölçek kümesi için en az beş düğüm sayısını koruyun. Bu eşiğin altına ölçeklendirirseniz kümeniz hata durumuna girer ve kaldırılan düğümleri el ile temizlemeniz gerekir.

Küme düğümlerini yükseltme ve hata etki alanlarına eşit olarak dağıtarak eşit bir şekilde kullanılmalarını sağlamak için önce en son oluşturulan düğümün kaldırılması gerekir. Başka bir deyişle düğümler, oluşturma sırasının tersine kaldırılmalıdır. En son oluşturulan düğüm, `virtual machine scale set InstanceId` özelliğinin değeri en yüksek olandır. Aşağıdaki kod örnekleri en son oluşturulan düğümü döndürür.

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Service Fabric kümesinin, bu düğümün kaldırılacağını bilmesi gerekir. Uygulamanız gereken üç adım vardır:

1. Artık veriler için çoğaltma olmaması için düğümü devre dışı bırakın.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Service Fabric çalışma zamanının düzgün şekilde kapanması ve uygulamanızın bir sonlandırma isteği alması için düğümü durdurun.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Kümeden düğümü kaldırın.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

Bu üç adım düğüme uygulandıktan sonra ölçek kümesinden kaldırılabilir. [Bronz][durability]’un yanı sıra dayanıklılık katmanları kullanıyorsanız ölçek kümesi örneği kaldırıldığında bu adımlar sizin için uygulanır.

Aşağıdaki kod bloğu, en son oluşturulan düğümü alır, düğümü devre dışı bırakır, durdurur ve kümeden kaldırır.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

Aşağıdaki **sfctl** kodunda, en son oluşturulan düğümün **node-name** değerini almak için şu komut kullanılır: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```shell
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Her bir adımın durumunu denetlemek için şu **sfctl** sorgularını kullanın
>
> **Devre dışı bırakma durumunu denetle**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Durma durumunu denetle**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>Ölçek kümesinin ölçeğini daraltma

Şimdi Service Fabric düğümü kümeden kaldırıldığına göre sanal makine ölçek kümesinin ölçeği daraltılabilir. Aşağıdaki örnekte, ölçek kümesi kapasitesi 1 azaltılır.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Bu kod, kapasiteyi 5 olarak ayarlar.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Service Fabric Explorer gözlemleyebileceğiniz davranışlar
Bir kümeyi ölçeklendirirseniz, Service Fabric Explorer kümenin parçası olan düğüm (sanal makine ölçek kümesi örneği) sayısını yansıtır.  Bununla birlikte, içinde bir kümeyi ölçeklendirdiğiniz zaman, [kaldırma-ServiceFabricNodeState cmd](/powershell/module/servicefabric/remove-servicefabricnodestate) 'yi uygun düğüm adıyla çağırmadığınız müddetçe, kaldırılan düğümü/VM örneğini düzgün olmayan bir durumda görürsünüz.   

Bu davranışın açıklaması aşağıda verilmiştir.

Service Fabric Explorer listelenen düğümler, Service Fabric sistem hizmetlerinin (FM özellikle), kümenin sahip olduğu düğüm sayısını öğrenmek için bir yansıdır. Sanal makine ölçek kümesini ' de ölçeklendirirseniz, VM silinmiştir ancak FM sistem hizmeti hala düğümün (silinen VM ile eşlenmiş) geri dönebilmesini sağlar. Service Fabric Explorer, bu düğümü görüntülemeye devam eder (ancak sistem durumu hata veya bilinmeyen olabilir).

Bir VM kaldırıldığında bir düğümün kaldırıldığından emin olmak için iki seçeneğiniz vardır:

1. Kümenizdeki düğüm türleri için altın veya gümüş bir dayanıklılık düzeyi seçin. Bu, size altyapı tümleştirmesi sağlar. Daha sonra, ' de ölçeklendirirseniz, düğümleri sistem hizmetleri (FM) durumundan otomatik olarak kaldırır.
[Dayanıklılık düzeylerinin ayrıntılarına](service-fabric-cluster-capacity.md) bakın

> [!NOTE]
> Dayanıklılık düzeyi Gold veya gümüş etkin olan herhangi bir sanal makine ölçek kümesi için en az beş düğüm sayısını koruyun. Bu eşiğin altına ölçeklendirirseniz kümeniz hata durumuna girer ve kaldırılan düğümleri el ile temizlemeniz gerekir.

2. VM örneği içinde ölçeklendirildiğinde [Remove-ServiceFabricNodeState cmdlet 'ini](/powershell/module/servicefabric/remove-servicefabricnodestate)çağırmanız gerekir.

> [!NOTE]
> Service Fabric kümeler, kullanılabilirliği korumak ve durumu korumak için "çekirdeği koruma" olarak adlandırılan belirli sayıda düğümün her zaman kullanılabilir olmasını gerektirir. Bu nedenle, ilk olarak [durumlarınızın tam yedeklemesini](service-fabric-reliable-services-backup-restore.md)gerçekleştirmediğiniz müddetçe kümedeki tüm makineleri kapatmak genellikle güvenli değildir.
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
Küme kapasitesini planlama, küme yükseltme ve bölümlendirme hizmetleri hakkında bilgi edinmek için aşağıdakileri okuyun:

* [Küme kapasitenizi planlayın](service-fabric-cluster-capacity.md)
* [Küme yükseltmeleri](service-fabric-cluster-upgrade.md)
* [Maksimum ölçek için durum bilgisi olan hizmetleri bölümle](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-in-out/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-in-out/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
