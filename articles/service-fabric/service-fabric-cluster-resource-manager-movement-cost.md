---
title: 'Service Fabric kümesi Kaynak Yöneticisi: taşıma maliyeti | Microsoft Docs'
description: Service Fabric Hizmetleri için taşıma maliyetine genel bakış
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 80845fca8d163a4ebe9257f19825624acef3a815
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73243003"
---
# <a name="service-movement-cost"></a>Hizmet taşıma maliyeti
Service Fabric kümenin Kaynak Yöneticisi, bir kümede yapılacak değişikliklerin ne kadar değişiklik yaptığını belirlemeye çalışırken dikkate aldığı bir faktör bu değişikliklerin maliyetidir. "Maliyet" kavramı, kümenin ne kadar iyileşebileceğini bir şekilde satın alabilir. Hizmetler, Dengeleme, birleştirme ve diğer gereksinimler için Hizmetleri taşırken buna göre belirlenir. Amaç, gereksinimleri en az karışıklığa veya pahalı bir şekilde karşılamaktır. 

Hizmet maliyetleri CPU süresi ve ağ bant genişliği en az bir olarak taşınıyor. Durum bilgisi olan hizmetler için, ek bellek ve disk kullanan bu hizmetlerin durumunun kopyalanmasını gerektirir. Azure Service Fabric kümesinin Kaynak Yöneticisi birlikte geldiği çözümlerin maliyetini en aza indirmek, kümenin kaynaklarının gereksiz şekilde harcanmadığından emin olmanıza yardımcı olur. Ancak, kümedeki kaynakların ayrılmasını önemli ölçüde iyileştirebilecek çözümleri de yoksaymak istemezsiniz.

Küme Kaynak Yöneticisi, maliyetleri yönetmeye çalışırken maliyeti ve sınırlandırmanın iki yolu vardır. İlk mekanizma, yaptığı her taşımayı saymadır. Aynı Bakiye (puan) hakkında iki çözüm oluşturulursa, Küme Kaynak Yöneticisi en düşük maliyetle (Toplam iz sayısı) birini tercih eder.

Bu strateji iyi bir şekilde çalışmaktadır. Ancak, varsayılan veya statik yüklere göre, tüm taşımaların eşit olduğu karmaşık bir sistemde olma ihtimali düşüktür. Bazıları büyük olasılıkla çok daha pahalıdır.

## <a name="setting-move-costs"></a>Taşıma maliyetlerini ayarlama 
Bir hizmetin oluşturulduğu sırada varsayılan taşıma maliyetini belirtebilirsiniz:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C#: 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Hizmet oluşturulduktan sonra bir hizmet için de MoveCost 'ı dinamik olarak belirtebilir veya güncelleştirebilirsiniz: 

PowerShell: 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Taşıma maliyetini çoğaltma başına dinamik olarak belirtme

Yukarıdaki kod parçacıklarının hepsi hizmetin dışından bir hizmetin tamamına yönelik MoveCost belirtmektir. Ancak, taşıma maliyeti en çok yararlı olur ve belirli bir hizmet nesnesinin taşıma maliyeti, kullanım ömrü boyunca değişir. Hizmetler muhtemelen belirli bir süreyi taşımanın ne kadar maliyetlerine ait olduğu konusunda en iyi fikir sahibi olduğundan, hizmetler için çalışma zamanı sırasında kendi bireysel taşıma maliyetlerini raporlamak üzere bir API 'SI vardır. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Taşıma maliyetinin etkisi
MoveCost beş düzeye sahiptir: sıfır, düşük, orta, yüksek ve VeryHigh. Aşağıdaki kurallar geçerlidir:

* Sıfır ve VeryHigh dışında, Movemaliyetler birbirleriyle ilişkilidir. 
* Sıfır taşıma maliyeti, hareketin ücretsiz olduğu ve çözüm puanına göre saymamalıdır olması anlamına gelir.
* Taşıma maliyetinizi High veya VeryHigh olarak ayarlamak, çoğaltmanın *hiçbir* şekilde taşınmayacağı *garantisi sağlamaz.*
* VeryHigh taşıma maliyeti olan çoğaltmalar yalnızca kümede başka bir şekilde sabitlenemez bir kısıtlama ihlali varsa (diğer birçok çoğaltmanın ihlalin ihlaline yol açmasının gerekli olsa bile)



<center>

taşıma][Image1]
için çoğaltmaları seçerken maliyeti bir faktör olarak ![</center>

MoveCost, en az kesintiye neden olan çözümleri bulmanıza yardımcı olur ve yine de eşdeğer bakiyeye ulaşan en kolay şekilde elde edilir. Hizmetin maliyet kavramı birçok şeylere göre olabilir. Taşıma maliyetinizi hesaplamak için en yaygın etmenler şunlardır:

- Hizmetin taşıması gereken durum veya veri miktarı.
- İstemcilerin bağlantısının kesilmesi maliyeti. Birincil çoğaltmayı taşımak, genellikle ikincil bir çoğaltmanın taşınması maliyetinden daha maliyetlidir.
- Uçuş sırasında bir işlemi kesme maliyeti. Veri deposu düzeyindeki veya bir istemci çağrısına yanıt olarak gerçekleştirilen işlemlerde bazı işlemler maliyetlidir. Belirli bir noktadan sonra, gerek duymuyorsanız durdurmak istemezsiniz. Bu nedenle işlem devam ederken, hareket olasılığını azaltmak için bu hizmet nesnesinin taşıma maliyetini artırırsınız. İşlem tamamlandığında, maliyeti normal olarak ayarlayın.

> [!IMPORTANT]
> VeryHigh taşıma maliyetinin kullanılması, Küme Kaynak Yöneticisi kümede küresel olarak en uygun yerleştirme çözümünü bulma becerisini önemli ölçüde kısıtladığından, dikkatli bir şekilde ele alınmalıdır. VeryHigh taşıma maliyeti olan çoğaltmalar yalnızca kümede başka bir şekilde sabitlenemez bir kısıtlama ihlali varsa (diğer birçok çoğaltmanın ihlalin ihlaline yol açmasının gerekli olsa bile)

## <a name="enabling-move-cost-in-your-cluster"></a>Kümenizde taşıma maliyeti etkinleştiriliyor
Daha ayrıntılı bir MoveCost 'in hesaba alınması için, kümenizde MoveCost özelliğinin etkinleştirilmesi gerekir. Bu ayar olmadan, varsayılan sayım hareketi modu, MoveCost hesaplamak için kullanılır ve MoveCost raporları yok sayılır.


ClusterManifest. xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

Tek başına dağıtımlar için ClusterConfig. JSON veya Azure 'da barındırılan kümeler için Template. JSON aracılığıyla:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Sonraki adımlar
- Service Fabric küme kaynak yöneticisi, kümedeki tüketimi ve kapasiteyi yönetmek için ölçümleri kullanır. Ölçümler ve bunların nasıl yapılandırılacağı hakkında daha fazla bilgi edinmek için [ölçüm ile Service Fabric kaynak tüketimini yönetmeyi ve yüklemeyi](service-fabric-cluster-resource-manager-metrics.md)inceleyin.
- Kaynak Yöneticisi kümenin, kümedeki yükü nasıl yönettiğini ve dengeleyeceğinizi öğrenmek için, [Service Fabric kümenizin dengelenmesini](service-fabric-cluster-resource-manager-balancing.md)inceleyin.

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
