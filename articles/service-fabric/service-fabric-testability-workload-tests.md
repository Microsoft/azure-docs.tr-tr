---
title: Azure Service Fabric uygulamalarında hata benzetimi yapma | Microsoft Docs
description: Hizmetlerinizi doğru ve düzgün olmayan hatalara karşı nasıl harlaştırmak.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: chackdan
editor: ''
ms.assetid: 44af01f0-ed73-4c31-8ac0-d9d65b4ad2d6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: bbb89b66231c949627c7ffbf99ebe9b5dd379ca2
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348710"
---
# <a name="simulate-failures-during-service-workloads"></a>Hizmet iş yükleri sırasında hata benzetimleri yapma
Azure 'daki test Service Fabric senaryoları, geliştiricilerin bireysel hatalarıyla ilgilenme konusunda endişelenmelerine olanak tanır. Ancak, istemci iş yükünün ve hatalarının açık bir şekilde aramasının gerekebileceği senaryolar vardır. İstemci iş yükünün ve hatalarının araya getirilmesi, hata oluştuğunda hizmetin gerçekten bir eylem gerçekleştirmesini sağlar. Test edilebilirlik 'nın sağladığı denetim düzeyi verildiğinde, bunlar iş yükü yürütmesinin kesin noktalarında olabilir. Bu hata, uygulamadaki farklı durumlardaki hataları bulabilir ve kaliteyi iyileştirebilir.

## <a name="sample-custom-scenario"></a>Örnek özel senaryo
Bu test, iş yükünü [düzgün ve düzgün olmayan hatalarla](service-fabric-testability-actions.md#graceful-vs-ungraceful-fault-actions)karşılıklı bırakan bir senaryoyu gösterir. Hatalar, hizmet işlemlerinin ortasında veya en iyi sonuçlar için işlem olarak bildirilmelidir.

Dört iş yükü sunan bir hizmetin örneğini inceleyelim: A, B, C ve D. Her biri bir iş akışı kümesine karşılık gelir ve işlem, depolama veya karıştırma olabilir. Basitlik sağlamak için örneğimizde iş yüklerini Özet olarak ekleyeceğiz. Bu örnekte yürütülen farklı hatalar şunlardır:

* RestartNode: Makinenin yeniden başlatılmasının benzetimini yapmak için düzgün olmayan hata.
* RestartDeployedCodePackage: Hizmet ana bilgisayarı işleminin çökmesinin benzetimini yapmak için düzgün olmayan hata.
* RemoveReplica: Çoğaltma kaldırma benzetimi için düzgün hatası.
* MovePrimary Service Fabric yük dengeleyici tarafından tetiklenen çoğaltma hareketlerinin benzetimini yapmak için düzgün hatası.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        // Replace these strings with the actual version for your cluster and application.
        string clusterConnection = "localhost:19000";
        Uri applicationName = new Uri("fabric:/samples/PersistentToDoListApp");
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Workload Test...");
        try
        {
            RunTestAsync(clusterConnection, applicationName, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Workload Test failed: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Workload Test completed successfully.");
        return 0;
    }

    public enum ServiceWorkloads
    {
        A,
        B,
        C,
        D
    }

    public enum ServiceFabricFaults
    {
        RestartNode,
        RestartCodePackage,
        RemoveReplica,
        MovePrimary,
    }

    public static async Task RunTestAsync(string clusterConnection, Uri applicationName, Uri serviceName)
    {
        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);
        // Maximum time to wait for a service to stabilize.
        TimeSpan maxServiceStabilizationTime = TimeSpan.FromSeconds(120);

        // How many loops of faults you want to execute.
        uint testLoopCount = 20;
        Random random = new Random();

        for (var i = 0; i < testLoopCount; ++i)
        {
            var workload = SelectRandomValue<ServiceWorkloads>(random);
            // Start the workload.
            var workloadTask = RunWorkloadAsync(workload);

            // While the task is running, induce faults into the service. They can be ungraceful faults like
            // RestartNode and RestartDeployedCodePackage or graceful faults like RemoveReplica or MovePrimary.
            var fault = SelectRandomValue<ServiceFabricFaults>(random);

            // Create a replica selector, which will select a primary replica from the given service to test.
            var replicaSelector = ReplicaSelector.PrimaryOf(PartitionSelector.RandomOf(serviceName));
            // Run the selected random fault.
            await RunFaultAsync(applicationName, fault, replicaSelector, fabricClient);
            // Validate the health and stability of the service.
            await fabricClient.TestManager.ValidateServiceAsync(serviceName, maxServiceStabilizationTime);

            // Wait for the workload to finish successfully.
            await workloadTask;
        }
    }

    private static async Task RunFaultAsync(Uri applicationName, ServiceFabricFaults fault, ReplicaSelector selector, FabricClient client)
    {
        switch (fault)
        {
            case ServiceFabricFaults.RestartNode:
                await client.FaultManager.RestartNodeAsync(selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RestartCodePackage:
                await client.FaultManager.RestartDeployedCodePackageAsync(applicationName, selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RemoveReplica:
                await client.FaultManager.RemoveReplicaAsync(selector, CompletionMode.Verify, false);
                break;
            case ServiceFabricFaults.MovePrimary:
                await client.FaultManager.MovePrimaryAsync(selector.PartitionSelector);
                break;
        }
    }

    private static Task RunWorkloadAsync(ServiceWorkloads workload)
    {
        throw new NotImplementedException();
        // This is where you trigger and complete your service workload.
        // Note that the faults induced while your service workload is running will
        // fault the primary service. Hence, you will need to reconnect to complete or check
        // the status of the workload.
    }

    private static T SelectRandomValue<T>(Random random)
    {
        Array values = Enum.GetValues(typeof(T));
        T workload = (T)values.GetValue(random.Next(values.Length));
        return workload;
    }
}
```
