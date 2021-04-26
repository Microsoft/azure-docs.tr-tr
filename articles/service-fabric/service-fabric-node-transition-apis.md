---
title: Küme düğümlerini başlatma ve durdurma
description: Küme düğümlerini başlatıp durdurarak bir Service Fabric uygulamasını test etmek için hata ekleme işlemini nasıl kullanacağınızı öğrenin.
author: LMWF
ms.topic: conceptual
ms.date: 6/12/2017
ms.author: lemai
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c31040ec13084f9e4b08bbc9a347e4ad44975bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89021264"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Düğüm geçişi API 'SI ile başlangıç düğümünü değiştirme ve düğüm API 'Lerini durdurma

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Düğüm Durdur ve düğüm başlatma API 'Leri ne işe başlar?

Düğüm durdurma API 'SI (yönetilen: [Stopnodeasync ()][stopnode], PowerShell: [stop-servicefabricnode][stopnodeps]) bir Service Fabric düğümünü durdurur.  Bir Service Fabric düğümü, VM veya makine değil işlem, VM veya makine çalışmaya devam eder.  "Node" belgesinin geri kalanı için Service Fabric düğüm anlamına gelir.  Düğüm durdurulduğunda, kümenin üyesi olmadığı ve Hizmetleri barındıramayan ve bu sayede bir *alt* düğümü taklit eden *durdurulmuş* bir duruma geçer.  Bu, uygulamanızı test etmek için sisteme ekleme hataları için yararlıdır.  Başlangıç düğümü API 'SI (yönetilen: [Startnodeasync ()][startnode], PowerShell: [Start-servicefabricnode][startnodeps]]), düğümü normal bir duruma GERI getiren durdurma düğümü API 'sini tersine çevirir.

## <a name="why-are-we-replacing-these"></a>Neden bunları değiştiriyor?

Daha önce açıklandığı gibi, *durdurulmuş* bir Service Fabric düğümü, KASıTLı düğüm API 'sini kullanarak bilerek hedeflenmiş bir düğümdür.  *Aşağı* düğüm, başka bir nedenle kapalı bir düğümdür (ÖRNEĞIN, VM veya makine kapalı olur).  Düğüm durdur API 'siyle, sistem *durdurulan* düğümleri ve *alt* düğümleri birbirinden ayırt etmek için bilgi sunmaz.

Ayrıca, bu API 'Ler tarafından döndürülen bazı hatalar, olabilecekleri için açıklayıcı değildir.  Örneğin, önceden *durdurulmuş* bir düğümdeki düğüm durdurma API 'sini çağırmak, *ınvalidaddress* hatasını döndürür.  Bu deneyim artırılabilir.

Ayrıca, başlangıç düğümü API 'SI çağrılana kadar bir düğümün durdurulma süresi "sonsuz" dır.  Bunun sorunlara neden olabileceğini ve hataya açık olabileceğini tespit ettik.  Örneğin, bir kullanıcının bir düğümdeki düğüm durdur API 'sini çağırdığından ve daha sonra bunu unutduğumuz sorunları gördük.  Daha sonra, düğüm *kapalıysa* veya *durdurulmuşsa* bu anlaşılır değildir.


## <a name="introducing-the-node-transition-apis"></a>Düğüm geçişi API 'Lerine giriş

Bu sorunları yeni bir API kümesinde ele aldık.  Yeni düğüm geçiş API 'SI (yönetilen: [Startnodegeçişli Tionasync ()][snt]) Service Fabric bir düğümü *durdurulmuş* duruma geçirebilir veya *durdurulmuş* bir durumdan normal bir duruma geçiş yapmak için kullanılabilir.  Lütfen API 'nin adı içindeki "Başlat" ın bir düğümü başlatmak için başvurmadığını unutmayın.  Sistemin *durdurulan* veya başlatılan durumuna geçiş için yürütebileceği zaman uyumsuz bir işlem başlangıcını gösterir.

**Kullanım**

Düğüm geçişi API 'SI çağrıldığında bir özel durum oluşturmaz, sistem zaman uyumsuz işlemi kabul etmiş ve yürütülür.  Başarılı bir çağrı, işlemin henüz tamamlandığını göstermez.  İşlemin geçerli durumu hakkında bilgi almak için, bu işlem için düğüm geçişi API 'SI çağrılırken kullanılan GUID ile düğüm geçişi Ilerleme durumu API 'sini (Managed: [GetNodeTransitionProgressAsync ()][gntp]) çağırın.  Düğüm geçişi Ilerleme durumu API 'SI bir Nodegeçişli Ilerleme nesnesi döndürür.  Bu nesnenin State özelliği işlemin geçerli durumunu belirtir.  Durum "çalışıyor" ise, işlem Yürütülüyor demektir.  İşlem tamamlanırsa, işlem hatasız olarak tamamlanmıştır.  Hata oluşursa, işlem çalıştırılırken bir sorun oluştu.  Result özelliğinin Exception özelliği, sorunun ne olduğunu belirtecektir.  https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstateDurum özelliği ve kod örnekleri için aşağıdaki "Örnek kullanım" bölümü hakkında daha fazla bilgi için bkz..


**Durdurulmuş bir düğüm ve aşağı düğüm arasında ayrım** Düğüm geçiş API 'SI kullanılarak bir düğüm *durdurulmuşsa* , düğüm sorgusunun çıkışı (yönetilen: [Getnodelistasync ()][nodequery], PowerShell: [Get-servicefabricnode][nodequeryps]), bu düğümün bir *ısdurdurulan* özelliği true değerine sahip olduğunu gösterir.  Bu, *düğüm durumu* özelliğinin değerinden farklı olduğunu fark *eder.*  *Nodestatus* özelliğinde bir değer *kapalıysa*, ancak *ısdurdurulan* değeri false ise düğüm, düğüm geçiş API 'si kullanılarak durdurulmadı ve başka bir nedenden dolayı *çalışmıyor* .  *Isdurdurulan* özelliği true Ise ve *Nodestatus* özelliği *kapalıysa*, düğüm geçişi API 'si kullanılarak durdurulmuştur.

Düğüm geçişi API 'sini kullanarak *durdurulmuş* bir düğümü başlatmak, bunu kümenin normal bir üyesi olarak çalışacak şekilde döndürür.  Düğüm sorgu API 'sinin çıktısı, *ısdurdurulan olarak ısdurdurulduğunu* ve *düğüm durumu* ' nu aşağı doğru değil (örneğin, bir) gösterir.


**Sınırlı süre** Düğümü durdurmak için düğüm geçişi API 'sini kullanırken, gerekli parametrelerden biri olan *stopNodeDurationInSeconds*, düğümün *durdurulma* süresini saniye cinsinden gösterir.  Bu değer, en az 600 ve en fazla 14400 olan izin verilen aralıkta olmalıdır.  Bu sürenin süresi dolduktan sonra, düğüm otomatik olarak durumunu otomatik olarak yeniden başlatır.  Kullanım örneği için aşağıdaki örnek 1 ' e bakın.

> [!WARNING]
> Düğüm geçişi API 'Lerini ve düğüm durdurma ve düğüm API 'Lerini karıştırmaktan kaçının.  Öneri yalnızca düğüm geçişi API 'sini kullanmaktır.  > düğüm durdur API 'SI kullanılarak zaten durdurulmuşsa, > düğüm geçişi API 'Leri kullanılmadan önce ilk düğüm API 'SI kullanılarak başlatılması gerekir.

> [!WARNING]
> Birden çok düğüm geçişi API çağrısı, paralel olarak aynı düğümde yapılamaz.  Böyle bir durumda, düğüm geçiş API 'SI, Nodegeçişli Tionınprogress Özellik değeri ile bir FabricException oluşturur >.  Belirli bir düğümdeki düğüm geçişi > başlatıldıktan sonra, aynı düğümdeki > yeni bir geçişe başlamadan önce işlem bir Terminal durumuna (tamamlandı, hatalı veya ForceCancelled) ulaşıncaya kadar beklemeniz gerekir.  Farklı düğümlerdeki paralel düğüm geçiş çağrılarına izin verilir.


#### <a name="sample-usage"></a>Örnek Kullanım


**Örnek 1** -aşağıdaki örnek, düğümü durdurmak Için düğüm geçişi API 'sini kullanır.

```csharp
        // Helper function to get information about a node
        static Node GetNodeInfo(FabricClient fc, string node)
        {
            NodeList n = null;
            while (n == null)
            {
                n = fc.QueryManager.GetNodeListAsync(node).GetAwaiter().GetResult();
                Task.Delay(TimeSpan.FromSeconds(1)).GetAwaiter();
            };

            return n.FirstOrDefault();
        }

        static async Task WaitForStateAsync(FabricClient fc, Guid operationId, TestCommandProgressState targetState)
        {
            NodeTransitionProgress progress = null;

            do
            {
                bool exceptionObserved = false;
                try
                {
                    progress = await fc.TestManager.GetNodeTransitionProgressAsync(operationId, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                    exceptionObserved = true;
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                    exceptionObserved = true;
                }

                if (!exceptionObserved)
                {
                    Console.WriteLine("Current state of operation '{0}': {1}", operationId, progress.State);

                    if (progress.State == TestCommandProgressState.Faulted)
                    {
                        // Inspect the progress object's Result.Exception.HResult to get the error code.
                        Console.WriteLine("'{0}' failed with: {1}, HResult: {2}", operationId, progress.Result.Exception, progress.Result.Exception.HResult);

                        // ...additional logic as required
                    }

                    if (progress.State == targetState)
                    {
                        Console.WriteLine("Target state '{0}' has been reached", targetState);
                        break;
                    }
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (true);
        }

        static async Task StopNodeAsync(FabricClient fc, string nodeName, int durationInSeconds)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            // Create a Guid
            Guid guid = Guid.NewGuid();

            // Create a NodeStopDescription object, which will be used as a parameter into StartNodeTransition
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, durationInSeconds);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStopDescription from above, which will stop the target node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    // This is retryable
                }
                catch (FabricTransientException fte)
                {
                    // This is retryable
                }

                // Backoff
                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Örnek 2** -aşağıdaki örnek *durdurulmuş* bir düğümü başlatır.  İlk örnekten bazı yardımcı yöntemler kullanır.

```csharp
        static async Task StartNodeAsync(FabricClient fc, string nodeName)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = n.NodeInstanceId;

            // Create a NodeStartDescription object, which will be used as a parameter into StartNodeTransition
            NodeStartDescription description = new NodeStartDescription(guid, n.NodeName, nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Örnek 3** -aşağıdaki örnekte yanlış kullanım gösterilmektedir.  Sağladığı *stopDurationInSeconds* izin verilen aralıktan büyük olduğu için bu kullanım yanlış.  Startnodegeçişli Tionasync () önemli bir hata ile başarısız olacak, işlem kabul edilmedi ve ilerleme API 'SI çağrılmamalıdır.  Bu örnek, ilk örnekten bazı yardımcı yöntemler kullanır.

```csharp
        static async Task StopNodeWithOutOfRangeDurationAsync(FabricClient fc, string nodeName)
        {
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();

            // Use an out of range value for stopDurationInSeconds to demonstrate error
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, 99999);

            try
            {
                await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
            }

            catch (FabricException e)
            {
                Console.WriteLine("Caught {0}", e);
                Console.WriteLine("ErrorCode {0}", e.ErrorCode);
                // Output:
                // Caught System.Fabric.FabricException: System.Runtime.InteropServices.COMException (-2147017629)
                // StopDurationInSeconds is out of range ---> System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80071C63
                // << Parts of exception omitted>>
                //
                // ErrorCode InvalidDuration
            }
        }
```

**Örnek 4** -aşağıdaki örnek, düğüm geçişi API 'si tarafından başlatılan işlem kabul edildiğinde, ancak daha sonra yürütülürken başarısız olan düğüm GEÇIŞI ilerleme API 'sinden döndürülecek hata bilgilerini gösterir.  Bu durumda, düğüm geçişi API 'SI varolmayan bir düğümü başlatmaya çalıştığı için başarısız olur.  Bu örnek, ilk örnekten bazı yardımcı yöntemler kullanır.

```csharp
        static async Task StartNodeWithNonexistentNodeAsync(FabricClient fc)
        {
            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = 12345;

            // Intentionally use a nonexistent node
            NodeStartDescription description = new NodeStartDescription(guid, "NonexistentNode", nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.  In this case, it will end up in the Faulted state since the node does not exist.
            // When StartNodeTransitionProgressAsync()'s returned progress object has a State if Faulted, inspect the progress object's Result.Exception.HResult to get the error code.
            // In this case, it will be NodeNotFound.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Faulted).ConfigureAwait(false);
        }
```

[stopnode]: /dotnet/api/system.fabric.fabricclient.faultmanagementclient
[stopnodeps]: /previous-versions/azure/mt125982(v=azure.100)
[startnode]: /dotnet/api/system.fabric.fabricclient.faultmanagementclient
[startnodeps]: /previous-versions/azure/mt163520(v=azure.100)
[nodequery]: /dotnet/api/system.fabric.fabricclient.queryclient
[nodequeryps]: /powershell/module/servicefabric/get-servicefabricnode
[snt]: /dotnet/api/system.fabric.fabricclient.testmanagementclient
[gntp]: /dotnet/api/system.fabric.fabricclient.testmanagementclient
