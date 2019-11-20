---
title: Uygun maliyetli düşük öncelikli VM 'Lerde iş yüklerini Çalıştır-Azure Batch | Microsoft Docs
description: Azure Batch iş yüklerinin maliyetini azaltmak için düşük öncelikli VM 'Ler sağlamayı öğrenin.
services: batch
author: mscurrell
manager: gwallace
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 03/19/2018
ms.author: markscu
ms.custom: seodec18
ms.openlocfilehash: 33d448bc95f4cb12f5a06232cbab168a43d522c1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095190"
---
# <a name="use-low-priority-vms-with-batch"></a>Batch ile düşük öncelikli VM’ler kullanma

Azure Batch, toplu iş yüklerinin maliyetini azaltmak için düşük öncelikli sanal makineler (VM) sağlar. Düşük öncelikli VM 'Ler, çok düşük maliyetli bir işlem gücünün kullanılmasını sağlayarak yeni toplu Iş yükü türlerini mümkün hale getirir.
 
Düşük öncelikli VM 'Ler, Azure 'daki fazlalık kapasiteden yararlanır. Havuzlarınızda düşük öncelikli VM 'Ler belirttiğinizde Azure Batch bu fazlalığı kullanılabilir olduğunda kullanabilir.
 
Düşük öncelikli VM 'Lerin kullanılması için zorunluluğunu getirir, kullanılabilir kapasiteye bağlı olarak, bu VM 'Lerin ayrılmayabilir veya herhangi bir zamanda yok edilebilir hale gelebilir. Bu nedenle, düşük öncelikli VM 'Ler, belirli iş yükü türleri için en uygundur. İş tamamlama süresinin esnek olduğu ve çalışmanın birçok VM 'ye dağıtıldığı, toplu iş ve zaman uyumsuz işleme iş yükleri için düşük öncelikli VM 'Ler kullanın.
 
Düşük öncelikli VM 'Ler, ayrılmış VM 'lerle karşılaştırıldığında önemli ölçüde azaltılan bir fiyatla sunulur. Fiyatlandırma ayrıntıları için bkz. [Batch fiyatlandırması](https://azure.microsoft.com/pricing/details/batch/).

## <a name="use-cases-for-low-priority-vms"></a>Düşük öncelikli VM 'Ler için kullanım örnekleri

Düşük öncelikli VM 'lerin özellikleri verilsin, hangi iş yükleri bunları kullanamaz? Genel olarak, işler birçok paralel göreve bölündüğü veya çok sayıda sanal makineye yayılmış ve dağıtılan çok sayıda iş olduğu için toplu işleme iş yükleri iyi bir uyum.

-   Azure 'da daha fazla kapasite kullanımını en üst düzeye çıkarmak için uygun işlerin ölçeğini değiştirebilirsiniz.

-   Kimi zaman VM 'Ler kullanılamayabilir veya geçersiz hale gelebilir, bu da işler için kapasiteyi düşürür ve görev kesintiye uğramasına ve yeniden yürütülmesine neden olabilir. Bu nedenle, işlerin çalıştırılacağı sürede esnek olması gerekir.

-   Daha uzun görevlere sahip işler, kesintiye uğradığında daha fazla etkilenebilir. Uzun süre çalışan görevler yürütülürken ilerlemeye işaret eden bir işaret uygular, kesintiye uğramadan etkisi azalır. Daha kısa bir yürütme süresi olan görevler düşük öncelikli VM 'Ler ile en iyi şekilde çalışır, çünkü kesintiye uğralanma etkisi oldukça düşüktür.

-   Birden çok VM kullanan uzun süre çalışan MPı işleri, düşük öncelikli VM 'Leri kullanmak için uygun değildir, çünkü bir geçersiz bir VM, her zaman yeniden çalıştırmak zorunda olmasına neden olabilir.

Toplu işleme kullanım örneklerinin bazı örnekleri düşük öncelikli VM 'Leri kullanmak için uygundur:

-   **Geliştirme ve test**: Özellikle, büyük ölçekli çözümler geliştiriliyorsa, önemli tasarruflar gerçekleştirilebilir. Tüm test türleri avantaj sağlayabilir, ancak büyük ölçekli yük testi ve gerileme testi harika kullanımlardır.

-   **İsteğe bağlı kapasite takıma**: Düşük öncelikli VM 'Ler, kullanılabilir olduğunda, daha düşük maliyetli olan işleri ölçeklendirebilir ve bu nedenle daha hızlı tamamlanır. kullanılabilir olmadığında, adanmış VM 'lerin taban çizgisi kullanılabilir kalır.

-   **Esnek iş yürütme süresi**: İşlerin tamamlanmaları durumunda esneklik varsa, kapasitenin potansiyel olarak düşceye izin verebilir. Bununla birlikte, düşük öncelikli VM 'Lerin eklenmesi genellikle daha hızlı ve daha düşük bir maliyetle çalışır.

Batch havuzları, iş yürütme süresi esnekliğine bağlı olarak birkaç şekilde düşük öncelikli VM 'Leri kullanacak şekilde yapılandırılabilir:

-   Düşük öncelikli VM 'Ler, yalnızca bir havuzda kullanılabilir. Bu durumda toplu Işlem, kullanılabilir olduğunda, tüm açık kapasiteyi kurtarır. Bu yapılandırma, yalnızca düşük öncelikli VM 'Ler kullanıldığı için işleri yürütmeye yönelik en uygun yoldur.

-   Düşük öncelikli VM 'Ler, ayrılmış VM 'lerin sabit bir taban çizgisiyle birlikte kullanılabilir. Ayrılmış VM 'lerin sabit sayısı, bir işin devam etmesini sağlamak için her zaman bir kapasite olmasını sağlar.

-   Uygun ve düşük öncelikli VM 'lerin dinamik karışımı olabilir, böylece, en düşük öncelikli VM 'Ler kullanılabilir olduğunda yalnızca kullanılır, ancak tam fiyatlı ayrılmış VM 'ler gerektiğinde ölçeği artırılır. Bu yapılandırma işlerin devam etmesini sağlamak için kullanılabilen minimum kapasite miktarını korur.

## <a name="batch-support-for-low-priority-vms"></a>Düşük öncelikli VM 'Ler için Batch desteği

Azure Batch, düşük öncelikli VM 'Lerin kullanımını ve avantajlarından faydalanmayı kolaylaştıran çeşitli yetenekler sağlar:

-   Batch havuzları, hem adanmış VM 'ler hem de düşük öncelikli VM 'Ler içerebilir. Her bir sanal makine türünün sayısı, bir havuz oluşturulduğunda veya mevcut bir havuz için herhangi bir zamanda, açık yeniden boyutlandırma işlemi kullanılarak veya otomatik ölçeklendirmeli şekilde değiştirildiğinde belirtilebilir. İş ve görev gönderimi, havuzdaki VM türlerinden bağımsız olarak değişmeden kalabilir. Ayrıca, işleri mümkün olduğunca farklı bir şekilde çalıştırmak için düşük öncelikli VM 'Leri kullanmak için bir havuz yapılandırabilir, ancak işleri çalışır durumda tutmak için kapasite minimum eşiğin altına düşerse adanmış VM 'leri de çalıştırabilirsiniz.

-   Toplu iş havuzları, düşük öncelikli VM 'lerin hedef sayısını otomatik olarak arar. VM 'Ler önayarlanırsa, toplu Işlem kayıp kapasitesini değiştirmeye çalışır ve hedefe geri döner.

-   Görevler kesintiye uğradığında, Batch tarafından yeniden çalıştırılacak görevler algılanır ve otomatik olarak requeues.

-   Düşük öncelikli VM 'Ler, ayrılmış VM 'lerden farklı olan ayrı bir vCPU kotasına sahiptir. 
    Düşük öncelikli VM 'lere yönelik kota, düşük öncelikli VM 'Lerin maliyeti daha düşük olduğundan adanmış VM 'lerin kotasından daha yüksektir. Daha fazla bilgi için bkz. [Batch hizmeti kotaları ve limitleri](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> Düşük öncelikli VM 'Ler, [Kullanıcı aboneliği modunda](batch-api-basics.md#account)oluşturulan Batch hesapları için şu anda desteklenmiyor.
>

## <a name="create-and-update-pools"></a>Havuz oluşturma ve güncelleştirme

Bir Batch havuzu hem adanmış hem de düşük öncelikli VM 'Leri içerebilir (işlem düğümleri olarak da adlandırılır). Hem ayrılmış hem de düşük öncelikli VM 'Ler için işlem düğümlerinin hedef sayısını ayarlayabilirsiniz. Hedef düğüm sayısı, havuzda olmasını istediğiniz sanal makinelerin sayısını belirtir.

Örneğin, 5 ayrılmış VM ile Azure bulut hizmeti VM 'lerini ve 20 düşük öncelikli VM 'yi kullanarak bir havuz oluşturmak için:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

5 ayrılmış VM ve 20 düşük öncelikli VM 'nin hedefi olan Azure sanal makinelerini (Bu durumda Linux VM 'lerde) kullanarak bir havuz oluşturmak için:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

Hem ayrılmış hem de düşük öncelikli VM 'Ler için geçerli düğüm sayısını alabilirsiniz:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Havuz düğümlerinin, düğümün adanmış veya düşük öncelikli bir VM olup olmadığını belirten bir özelliği vardır:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Bir havuzdaki bir veya daha fazla düğüm, önayarlandığında, havuzdaki bir liste düğümleri işlemi yine de bu düğümleri döndürür. Geçerli düşük öncelikli düğümlerin sayısı değişmeden kalır, ancak söz konusu düğümlerin durumları, yok edilmiş duruma ayarlanır. Toplu iş VM 'Leri bulmaya çalışır ve başarılı olursa düğümler, yeni düğümler gibi, görev yürütmesi için kullanılabilir hale gelmeden önce durumlar **oluşturarak** ve sonra da **başlatılıyor** .

## <a name="scale-a-pool-containing-low-priority-vms"></a>Düşük öncelikli VM 'Ler içeren bir havuzu ölçeklendirme

Yalnızca ayrılmış VM 'lerden oluşan havuzlarda olduğu gibi, yeniden boyutlandırma yöntemini çağırarak veya otomatik ölçeklendirme kullanarak düşük öncelikli VM 'Ler içeren bir havuzu ölçeklendirmek mümkündür.

Havuz yeniden boyutlandırma işlemi, **Targetlowprioritynodes**değerini güncelleştiren ikinci bir isteğe bağlı parametre alır:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

Havuz otomatik ölçeklendirme formülü, düşük öncelikli VM 'Leri aşağıdaki gibi destekler:

-   **$TargetLowPriorityNodes**hizmet tanımlı değişkenin değerini alabilir veya ayarlayabilirsiniz.

-   **$CurrentLowPriorityNodes**hizmet tanımlı değişkenin değerini alabilirsiniz.

-   **$PreemptedNodeCount**hizmet tanımlı değişkenin değerini alabilirsiniz. 
    Bu değişken, yok edilen durumdaki düğüm sayısını döndürür ve kullanılmayan düğüm sayısına bağlı olarak, ayrılmış düğümlerin sayısını büyütme veya azaltma olanağı sağlar.

## <a name="jobs-and-tasks"></a>İşler ve görevler

İşler ve görevler düşük öncelikli düğümler için çok daha fazla yapılandırma gerektirir; tek destek aşağıdaki gibidir:

-   Bir işin JobManagerTask özelliği, **Allowlowprioritynode**adlı yeni bir özelliğe sahiptir. 
    Bu özellik true olduğunda, İş Yöneticisi görevi adanmış veya düşük öncelikli bir düğümde zamanlanabilir. Bu özellik false ise, İş Yöneticisi görevi yalnızca ayrılmış bir düğüm için zamanlanır.

-   Bir [ortam değişkeni](batch-compute-node-environment-variables.md) , bir görev uygulaması tarafından, düşük öncelikli veya ayrılmış bir düğümde çalışıp çalışmadığını belirleyebilmesi için kullanılabilir. Ortam değişkeni AZ_BATCH_NODE_IS_DEDICATED ' dir.

## <a name="handling-preemption"></a>Önalım işleme

VM 'Ler zaman zaman değiştirilebilir; Önalım gerçekleştiğinde Batch şunları yapar:

-   Üzerinde olan VM 'Lerin durumları, yok edilmişolarak güncelleştirilir.
-   Görevler, önceden bırakılan düğüm VM 'lerinde çalışıyorsa, bu görevler yeniden kuyruğa ve yeniden çalıştırılır.
-   VM, sanal makinede yerel olarak depolanan verilerin kaybedilmesi için etkin şekilde silinir.
-   Havuz, kullanılabilir düşük öncelikli düğümlerin hedef sayısına sürekli ulaşmaya çalışır. Değiştirme kapasitesi bulunduğunda düğümler kimliklerini tutar, ancak görev zamanlama için kullanılabilir olmadan önce durumlar **oluşturularak** başlatılır.
-   Önalım sayıları Azure portal bir ölçüm olarak kullanılabilir.

## <a name="metrics"></a>Ölçümler

Yeni ölçümler, düşük öncelikli düğümler için [Azure Portal](https://portal.azure.com) kullanılabilir. Bu ölçümler şunlardır:

- Düşük öncelikli düğüm sayısı
- Düşük öncelikli çekirdek sayısı 
- Önden düğüm sayısı

Azure portal ölçümleri görüntülemek için:

1. Portalda Batch hesabınıza gidin ve Batch hesabınızın ayarlarını görüntüleyin.
2. **İzleme** bölümünden **ölçümler** ' i seçin.
3. **Kullanılabilir ölçümler** listesinden istediğiniz ölçümleri seçin.

![Düşük öncelikli düğümler için ölçümler](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Sonraki adımlar

* Batch kullanmaya hazırlanan herkes için gerekli bilgileri içeren [Geliştiriciler için Batch özelliğine genel bakış](batch-api-basics.md) konusunu okuyun. Bu makalede havuzlar, düğümler, işler ve görevler gibi Batch hizmet kaynakları ve Batch uygulamanızı oluştururken kullanabileceğiniz birçok API özelliği hakkında daha ayrıntılı bilgi verilmektedir.
* Batch çözümleri oluşturmak için kullanılabilen [Batch API’leri ve araçları](batch-apis-tools.md) hakkında bilgi alın.
