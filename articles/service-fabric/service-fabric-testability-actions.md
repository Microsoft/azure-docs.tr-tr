---
title: Azure mikro hizmetlerindeki hataların benzetimini yapın
description: Bu makale, Microsoft Azure Service Fabric bulunan test edilebilirlik eylemleri hakkında konuşur.
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 9f8221d92ded33350b182cce5d28dd889beae4a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732882"
---
# <a name="testability-actions"></a>Test edilebilirlik eylemleri
Güvenilir olmayan bir altyapının benzetimini yapmak için Azure Service Fabric, çeşitli gerçek dünyada hataların ve durum geçişlerinin benzetimini yapmak için kullanabileceğiniz bir geliştirici sağlar. Bunlar, test edilebilirlik eylemleri olarak gösterilir. Eylemler, belirli bir hata ekleme, durum geçişi veya doğrulamaya neden olan alt düzey API 'lerdir. Bu eylemleri birleştirerek hizmetleriniz için kapsamlı test senaryoları yazabilirsiniz.

Service Fabric, bu eylemlerden oluşan bazı yaygın test senaryoları sağlar. Yaygın durum geçişlerini ve hata durumlarını sınamak için dikkatle seçilmiş olan bu yerleşik senaryoları kullanmanızı kesinlikle öneririz. Ancak, henüz yerleşik senaryolar kapsamında olmayan veya uygulamanız için özel olarak uyarlanmış senaryolar için kapsam eklemek istediğinizde, Eylemler, özel test senaryoları oluşturmak için kullanılabilir.

Eylemlerin C# uygulamaları System.Fabric.dll derlemesinde bulunur. System Fabric PowerShell modülü Microsoft.ServiceFabric.Powershell.dll derlemesinde bulunur. Çalışma zamanı yüklemesinin bir parçası olarak, Service Fabric PowerShell modülü kullanım kolaylığı sağlamak için yüklenir.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Düzgün olmayan hata eylemleri
Test edilebilirlik eylemleri iki ana demet halinde sınıflandırılır:

* Düzgün olmayan hatalar: Bu hatalar makine yeniden başlatmaları ve işlem kilitlenmeleri gibi hataların benzetimini yapar. Bu tür hatalarda, işlemin yürütme bağlamı aniden yanıt vermez. Bu, uygulama yeniden başlatılmadan önce durum temizleme işleminin çalışabileceği anlamına gelir.
* Düzgün hatalar: Bu hatalar, Yük Dengeleme tarafından tetiklenen çoğaltma hareketi ve bırakılanlar gibi düzgün eylemlerin benzetimini yapar. Bu gibi durumlarda, hizmet kapanmadan önce bir bildirim alır ve çıkmadan önce durumu temizleyebilir.

Daha iyi kalite doğrulaması için, çeşitli düzgün ve düzgün olmayan hataları gerçekleştirirken hizmeti ve iş yükünü çalıştırın. Düzgün olmayan hatalar, hizmet işleminin bazı iş akışının ortasında aniden çıkılabileceği senaryolar. Bu, hizmet çoğaltması Service Fabric tarafından geri yüklendikten sonra kurtarma yolunu sınar. Bu, veri tutarlılığını test etmenize ve hatalardan sonra hizmet durumunun doğru şekilde tutulup tutulmayacağı konusunda yardımcı olur. Diğer başarısızlık kümesi (düzgün arızalar), hizmetin Service Fabric tarafından taşınmakta olan çoğaltmalara doğru şekilde tepki verdiğini test eder. RunAsync yönteminde iptali işleme testi. Hizmetin, ayarlanan iptal belirtecini denetlemesi, durumunu doğru şekilde kaydetmesi ve RunAsync yönteminden çıkış yapması gerekir.

## <a name="testability-actions-list"></a>Test edilebilirlik eylemleri listesi
| Eylem | Açıklama | Yönetilen API | PowerShell cmdlet 'i | Düzgün olmayan/düzgün olmayan hatalar |
| --- | --- | --- | --- | --- |
| CleanTestState |Test sürücüsünün kötü bir şekilde kapatılmasını durumunda tüm test durumunu kümeden kaldırır. |CleanTestStateAsync |Remove-ServiceFabricTestState |Uygulanamaz |
| Invokedataloss |Bir hizmet bölümüne veri kaybı. |Invokedatalossasync |Invoke-ServiceFabricPartitionDataLoss |Normal |
| Invokequorumkaybetme |Belirli bir durum bilgisi olan hizmet bölümünü çekirdek kaybına geçirir. |Invokequorumlossasync |Invoke-ServiceFabricQuorumLoss |Normal |
| MovePrimary |Durum bilgisi olan bir hizmetin belirtilen birincil çoğaltmasını belirtilen küme düğümüne taşımadır. |Moveının Yasync |Move-ServiceFabricPrimaryReplica |Normal |
| MoveSecondary |Durum bilgisi olan bir hizmetin geçerli ikincil çoğaltmasını farklı bir küme düğümüne taşımadır. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Normal |
| Moveınstance | Durum bilgisi olmayan bir hizmetin geçerli örneğini farklı bir küme düğümüne kaydırır. | Moveınstanceasync | Move-ServiceFabricInstance | Normal |
| RemoveReplica |Kümeden bir çoğaltmayı kaldırarak bir çoğaltma başarısızlığının benzetimini yapar. Bu, çoğaltmayı kapatacak ve ' none ' rolüne geçirecek ve tüm durumu kümeden kaldırılarak bu uygulamayı kaldırır. |Removereperepasync |Remove-ServiceFabricReplica |Normal |
| RestartDeployedCodePackage |Bir kümedeki düğüme dağıtılan bir kod paketini yeniden başlatarak kod paketi işlem başarısızlığının benzetimini yapar. Bu işlem, bu işlemde barındırılan tüm Kullanıcı Hizmeti çoğaltmalarını yeniden başlatacak olan kod paketi işlemini iptal eder. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Yaşanmamasını |
| RestartNode |Bir düğümü yeniden başlatarak Service Fabric küme düğümü hatasına benzetir. |RestartNodeAsync |Restart-ServiceFabricNode |Yaşanmamasını |
| RestartPartition |Bir bölümün bazı veya tüm çoğaltmalarını yeniden başlatarak bir veri merkezi kesintisi veya küme kararma senaryosunu taklit eder. |RestartPartitionAsync |Restart-ServiceFabricPartition |Normal |
| RestartReplica |Bir kümede kalıcı bir çoğaltmayı yeniden başlatarak çoğaltmayı kapatıp yeniden açarak bir çoğaltma hatasına benzetim yapar. |RestartReplicaAsync |Restart-ServiceFabricReplica |Normal |
| StartNode |Zaten durdurulmuş bir kümede bir düğüm başlatır. |StartNodeAsync |Start-ServiceFabricNode |Uygulanamaz |
| StopNode |Bir kümedeki düğümü durdurarak bir düğüm başarısızlığının benzetimini yapar. StartNode çağrılana kadar düğüm aşağı kalacak. |StopNodeAsync |Stop-ServiceFabricNode |Yaşanmamasını |
| ValidateApplication |Bir uygulamadaki tüm Service Fabric hizmetlerinin kullanılabilirliğini ve sistem durumunu doğrular, genellikle sisteme bazı bir hata alındıktan sonra. |ValidateApplicationAsync |Test-ServiceFabricApplication |Uygulanamaz |
| ValidateService |Service Fabric bir hizmetin kullanılabilirliğini ve sistem durumunu doğrular ve genellikle sisteme bazı bir hata alındıktan sonra. |ValidateServiceAsync |Test-ServiceFabricService |Uygulanamaz |

## <a name="running-a-testability-action-using-powershell"></a>PowerShell kullanarak bir test edilebilirlik eylemi çalıştırma
Bu öğreticide, PowerShell kullanarak bir test edilebilirlik eyleminin nasıl çalıştırılacağı gösterilmektedir. Yerel (tek Box) bir kümede veya Azure kümesinde nasıl bir test edilebilir eylemi çalıştırılacağını öğreneceksiniz. Microsoft.Fabric.Powershell.dll--Service Fabric PowerShell modülü--Microsoft Service Fabric MSI yüklediğinizde otomatik olarak yüklenir. Bir PowerShell istemi açtığınızda modül otomatik olarak yüklenir.

Öğretici segmentleri:

* [Tek bir kutu kümesine karşı eylem çalıştırma](#run-an-action-against-a-one-box-cluster)
* [Bir Azure kümesinde eylem çalıştırma](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Tek bir kutu kümesine karşı eylem çalıştırma
Yerel bir kümede bir test edilebilirlik eylemi çalıştırmak için önce kümeye bağlanın ve PowerShell komut satırını yönetici modunda açın. **Restart-ServiceFabricNode** eylemine bakmamıza izin verin.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

**Yeniden başlatma eylemi-ServiceFabricNode** , "Düğüm1" adlı bir düğüm üzerinde çalışıyor. Tamamlama modu, yeniden başlatma düğümü eyleminin gerçekten başarılı olup olmadığını doğrulayamamalıdır. Tamamlama modunun "Doğrula" olarak belirtilmesi, yeniden başlatma eyleminin gerçekten başarılı olup olmadığını doğrulamasına neden olur. Düğümü adına göre doğrudan belirtmek yerine, aşağıdaki gibi bir bölüm anahtarı ve çoğaltma türü aracılığıyla belirtebilirsiniz:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode** , bir kümedeki Service Fabric düğümünü yeniden başlatmak için kullanılmalıdır. Bu işlem, bu düğümde barındırılan tüm sistem hizmetini ve Kullanıcı Hizmeti çoğaltmalarını yeniden başlatacak olan Fabric.exe işlemini durdurur. Hizmetinizi test etmek için bu API 'nin kullanılması yük devretme kurtarma yollarında hataları ortaya çıkarabilir. Kümede düğüm hatalarının benzetimini yapmanıza yardımcı olur.

Aşağıdaki ekran görüntüsünde **restart-ServiceFabricNode** teistiki komutu eylemde gösterilmiştir.

![PowerShell 'de Restart-ServiceFabricNode komutunu çalıştırmanın ekran görüntüsü.](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

İlk **Get-ServiceFabricNode** (Service Fabric PowerShell modülünden bir cmdlet) çıkışı, yerel kümede beş düğüm olduğunu gösterir: node. 1 ila Node. 5. Test edilebilirlik eylemi (cmdlet) **Yeniden Başlat-ServiceFabricNode** düğüm üzerinde, Node. 4 adlı düğüm üzerinde yürütüldükten sonra, düğümün çalışma süresinin sıfırlandığını görüyoruz.

### <a name="run-an-action-against-an-azure-cluster"></a>Bir Azure kümesinde eylem çalıştırma
Bir Azure kümesine karşı bir test edilebilirlik eylemi (PowerShell kullanarak) çalıştırmak, eylemi yerel bir kümede çalıştırmaya benzerdir. Tek fark, yerel kümeye bağlanmak yerine, eylemi çalıştırmadan önce, önce Azure kümesine bağlanmanız gerekir.

## <a name="running-a-testability-action-using-c35"></a>C&#35; kullanarak bir test edilebilirlik eylemi çalıştırma
C# kullanarak bir test edilebilirlik eylemi çalıştırmak için öncelikle FabricClient kullanarak kümeye bağlanmanız gerekir. Sonra eylemi çalıştırmak için gereken parametreleri edinin. Aynı eylemi çalıştırmak için farklı parametreler kullanılabilir.
RestartServiceFabricNode eylemine bakarak, bunu çalıştırmanın bir yolu, kümedeki düğüm bilgilerini (düğüm adı ve düğüm örneği KIMLIĞI) kullanmaktır.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Parametre açıklaması:

* **Completemode** modu, yeniden başlatma eyleminin gerçekten başarılı olup olmadığını doğrulayamayacağını belirtir. Tamamlama modunun "Doğrula" olarak belirtilmesi, yeniden başlatma eyleminin gerçekten başarılı olup olmadığını doğrulamasına neden olur.  
* **OperationTimeout** , bir TimeoutException özel durumu oluşturulmadan önce işlemin ne kadar süre sonra bitmesini belirler.
* **CancellationToken** bekleyen bir çağrının iptal edilmesine olanak sağlar.

Düğümü adına göre doğrudan belirtmek yerine, bir bölüm anahtarı ve çoğaltma türü aracılığıyla belirtebilirsiniz.

Daha fazla bilgi için bkz. PartitionSelector ve ReplicaSelector.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector ve ReplicaSelector
### <a name="partitionselector"></a>PartitionSelector
PartitionSelector, test edilebilirlik içinde kullanıma sunulan bir yardımcıdır ve herhangi bir test edilebilirlik eylemini gerçekleştirecek belirli bir bölümü seçmek için kullanılır. Bölüm KIMLIĞI önceden biliniyorsa belirli bir bölümü seçmek için kullanılabilir. Ya da, Bölüm anahtarını sağlayabilirsiniz ve işlem bölüm KIMLIĞINI dahili olarak çözer. Ayrıca rastgele bir bölüm seçme seçeneğiniz de vardır.

Bu yardımcıyı kullanmak için, PartitionSelector nesnesini oluşturun ve Select * yöntemlerinden birini kullanarak bölümü seçin. Sonra PartitionSelector nesnesini, bunu gerektiren API 'ye geçirin. Hiçbir seçenek seçilmezse, varsayılan olarak rastgele bir bölüm olur.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector, test edilebilirlik içinde kullanıma sunulan bir yardımcıdır ve herhangi bir test edilebilir eylemin gerçekleştirileceği bir çoğaltma seçme konusunda yardımcı olmak için kullanılır. Çoğaltma KIMLIĞI önceden biliniyorsa belirli bir çoğaltmayı seçmek için kullanılabilir. Ayrıca, birincil çoğaltma veya rastgele bir ikincil seçme seçeneğiniz vardır. ReplicaSelector, PartitionSelector 'dan türetilir, bu nedenle hem çoğaltma hem de test edilebilir işlemi gerçekleştirmek istediğiniz bölümü seçmeniz gerekir.

Bu yardımcıyı kullanmak için bir ReplicaSelector nesnesi oluşturun ve çoğaltmayı ve bölümü seçmek istediğiniz yöntemi ayarlayın. Daha sonra bunu gerektiren API 'ye geçirebilirsiniz. Hiçbir seçenek seçilmezse, varsayılan olarak rastgele bir çoğaltma ve rastgele bölüm kullanılır.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Sonraki adımlar
* [Test edilebilirlik senaryoları](service-fabric-testability-scenarios.md)
* Hizmetinizi test etme
  * [Hizmet iş yükleri sırasında hataların benzetimini yapın](service-fabric-testability-workload-tests.md)
  * [Hizmetten hizmete iletişim sorunları](service-fabric-testability-scenarios-service-communication.md)

