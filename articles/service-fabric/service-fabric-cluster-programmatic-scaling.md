---
title: Azure Service Fabric programlı ölçeklendirme
description: Azure Service Fabric kümesini özel tetikleyicilere göre programlı bir şekilde veya dışarı ölçeklendirin
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.custom: devx-track-csharp
ms.openlocfilehash: 9f39bd874c1f5a1be42ca1c88e6ea2fe8df22f87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94648217"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Service Fabric kümesini programlı olarak ölçeklendirme 

Azure 'da çalışan Service Fabric kümeleri, sanal makine ölçek kümelerinin üzerine kurulmuştur.  [Küme ölçeklendirme](./service-fabric-cluster-scale-in-out.md) , Service Fabric kümelerinin el ile veya otomatik ölçeklendirme kurallarıyla nasıl ölçeklendirileceğini açıklar. Bu makalede, daha gelişmiş bir senaryo olan akıcı Azure işlem SDK 'Sı kullanılarak kimlik bilgilerinin nasıl yönetileceği ve bir kümenin nasıl ölçekleneceği açıklanır. Genel bakış için [Azure ölçeklendirme işlemlerini koordine etmek için programlama yöntemlerini](service-fabric-cluster-scaling.md#programmatic-scaling)okuyun. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Kimlik bilgilerini yönetme
Ölçeklendirmeyi işlemek için bir hizmet yazmanın bir zorluğu, hizmetin etkileşimli bir oturum açma olmadan sanal makine ölçek kümesi kaynaklarına erişebilmelidir. Ölçeklendirme hizmeti kendi Service Fabric uygulamasını değiştiriyor, ancak ölçek kümesine erişmek için kimlik bilgileri gerekiyorsa Service Fabric kümesine erişim kolaydır. Oturum açmak için [Azure CLI](https://github.com/azure/azure-cli)ile oluşturulmuş bir [hizmet sorumlusu](/cli/azure/create-an-azure-service-principal-azure-cli) kullanabilirsiniz.

Bir hizmet sorumlusu aşağıdaki adımlarla oluşturulabilir:

1. Azure CLı 'da ( `az login` ) sanal makine ölçek kümesine erişimi olan bir kullanıcı olarak oturum açın
2. Hizmet sorumlusu oluşturma `az ad sp create-for-rbac`
    1. Daha sonra kullanmak üzere AppID (' istemci KIMLIĞI ' başka bir yerde), ad, parola ve kiracı ' yı unutmayın.
    2. Ayrıca, ile görüntülenebilecek abonelik KIMLIĞINIZ gerekir `az account list`

Akıcı işlem kitaplığı bu kimlik bilgilerini şu şekilde kullanarak oturum açabilir ( `IAzure` [Microsoft. Azure. Management. floent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) paketinde olduğu gibi, temel akıcı Azure türlerinin olduğunu unutmayın):

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

Oturum açıldıktan sonra, ölçek kümesi örnek sayısı aracılığıyla sorgulanabilir `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity` .

## <a name="scaling-out"></a>Ölçeği genişletme
Akıcı Azure işlem SDK 'sını kullanarak, örnekleri yalnızca birkaç çağrı ile sanal makine ölçek kümesine eklenebilir

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Alternatif olarak, sanal makine ölçek kümesi boyutu da PowerShell cmdlet 'leri ile yönetilebilir. [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) , sanal makine ölçek kümesi nesnesi alabilir. Geçerli kapasite özelliği aracılığıyla kullanılabilir `.sku.capacity` . Kapasiteyi istenen değere değiştirdikten sonra, Azure 'daki sanal makine ölçek kümesi, komutla birlikte güncelleştirilebilecek [`Update-AzVmss`](/powershell/module/az.compute/update-azvmss) .

Bir düğüm el ile eklenirken, ölçek kümesi şablonu yeni örnekleri Service Fabric kümesine otomatik olarak katmak için Uzantılar içerdiğinden, bir ölçek kümesi örneğinin eklenmesi, yeni bir Service Fabric düğümü başlatmak için gerekli olmalıdır. 

## <a name="scaling-in"></a>Ölçeklendirme

Ölçeklendirme, ölçeklendirmeye benzerdir. Gerçek sanal makine ölçek kümesi değişiklikleri neredeyse aynı. Ancak, daha önce anlatıldığı gibi, Service Fabric yalnızca altın veya gümüş bir dayanıklılık ile kaldırılan düğümleri otomatik olarak temizler. Bu nedenle, bronz dayanıklılık ölçeğinde, kaldırılacak düğümü kapatmak ve sonra durumunu kaldırmak için Service Fabric kümeyle etkileşimde bulunmak gerekir.

Düğümün kapanmaya hazırlanması, kaldırılacak düğümü bulmayı (en son eklenen sanal makine ölçek kümesi örneği) ve devre dışı bırakılmasını içerir. Sanal makine ölçek kümesi örnekleri eklendikleri sırada numaralandırılır, bu nedenle düğümlerin adlarındaki (temel sanal makine ölçek kümesi örnek adlarıyla eşleşen) sayı soneki karşılaştırılırken daha yeni düğümler bulunabilir. 

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Kaldırılacak düğüm bulunduğunda, bu, devre dışı bırakılabilir ve `FabricClient` `IAzure` daha önce kullanılan örnek ile kaldırılır.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Genişletme sırasında olduğu gibi, sanal makine ölçek kümesi kapasitesini değiştirmek için PowerShell cmdlet 'leri de bir betik yaklaşımı tercih ediliyorsa, burada kullanılabilir. Sanal makine örneği kaldırıldıktan sonra Service Fabric düğüm durumu kaldırılabilir.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Sonraki adımlar

Kendi otomatik ölçeklendirme mantığınızı uygulamaya başlamak için aşağıdaki kavramlar ve yararlı API 'Ler hakkında bilgi edinin:

- [El ile veya otomatik ölçeklendirme kurallarıyla ölçekleme](./service-fabric-cluster-scale-in-out.md)
- [.Net Için Azure Yönetim kitaplıkları](https://github.com/Azure/azure-libraries-for-net) (bir Service Fabric kümesinin temel alınan sanal makine ölçek kümeleriyle etkileşim için yararlı)
- [System. Fabric. FabricClient](/dotnet/api/system.fabric.fabricclient) (bir Service Fabric kümesi ve düğümleri ile etkileşim için yararlı)
