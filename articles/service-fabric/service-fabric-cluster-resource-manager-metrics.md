---
title: Ölçümleri kullanarak Azure Service Fabric uygulama yükünü yönetme
description: Hizmet kaynak tüketimini yönetmek için Service Fabric ölçümleri yapılandırma ve kullanma hakkında bilgi edinin.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a7dedea2937c9cafb4216da3628aa1360ad6993
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92172998"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Ölçümlerle Service Fabric kaynak tüketimini yönetme ve yükleme
*Ölçümler* , hizmetlerinizin önem verdiği ve kümedeki düğümler tarafından sağlandığı kaynaklardır. Bir ölçüm, hizmetlerinizin performansını artırmak veya izlemek için yönetmek istediğiniz her şey olur. Örneğin, hizmetinizin aşırı yüklenmiş olup olmadığını bilmeniz için bellek tüketimini izleyebilirsiniz. Başka bir kullanım, daha iyi performans sağlamak için, belleğin daha az kısıtlandığı yerde hizmetin hareket etmeyeceğini anlayabilir.

Bellek, disk ve CPU kullanımı gibi şeyler, ölçüm örnekleridir. Bu ölçümler fiziksel ölçümlerdir ve yönetilmesi gereken düğümde fiziksel kaynaklara karşılık gelen kaynaklardır. Ölçümler ayrıca mantıksal ölçümler de (ve yaygın olarak) olabilir. Mantıksal ölçümler "MyWorkQueueDepth" veya "Iletitoprocess" veya "TotalRecords" gibi bir işlemdir. Mantıksal ölçümler uygulama tanımlı ve dolaylı olarak bazı fiziksel kaynak tüketimine karşılık gelir. Mantıksal ölçümler, hizmet başına temelinde fiziksel kaynakların tüketimini ölçmek ve raporlamak zor olabileceğinden yaygındır. Kendi fiziksel ölçümlerinizi ölçmenize ve raporlamaya yönelik karmaşıklık, Service Fabric bazı varsayılan ölçümleri de sağlar.

## <a name="default-metrics"></a>Varsayılan ölçümler
Şimdi, hizmetinizi yazmaya ve dağıtmaya başlamak istediğinizi varsayalım. Bu noktada, tükettiği fiziksel veya mantıksal kaynakları bilemezsiniz. Bu güzel! Service Fabric kümesi Kaynak Yöneticisi başka ölçüm belirtilmediğinde bazı varsayılan ölçümleri kullanır. Bunlar:

  - PrimaryCount-düğümdeki birincil çoğaltmaların sayısı 
  - ReplicaCount-düğümdeki toplam durum bilgisi olan çoğaltma sayısı
  - Düğüm üzerindeki tüm hizmet nesnelerinin sayısı (durum bilgisiz ve durum bilgisi olmayan)

| Metric | Durum bilgisi olmayan örnek yükü | Durum bilgisi olan Ikincil yük | Durum bilgisi olan birincil yük | Ağırlık |
| --- | --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |Yüksek |
| ReplicaCount |0 |1 |1 |Orta |
| Count |1 |1 |1 |Düşük |


Temel iş yükleri için varsayılan ölçümler, kümede işin daha iyi bir dağıtımını sağlar. Aşağıdaki örnekte, iki hizmet oluşturduğumuz ve dengeleme için varsayılan ölçümleri temel alarak ne olacağını görelim. İlk hizmet, üç bölümden oluşan ve üç bölümlü bir hedef çoğaltma kümesi boyutu ile durum bilgisi olan bir hizmettir. İkinci hizmet, bir bölüm ve örnek sayısı üç olan durum bilgisi olmayan bir hizmettir.

Elde edeceğiniz sonuç şu:

<center>

![Varsayılan ölçümlerle küme düzeni][Image1]
</center>

Dikkat edilecek bazı noktalar:
  - Durum bilgisi olan hizmetin birincil çoğaltmaları çeşitli düğümlere dağıtılır
  - Aynı bölümün çoğaltmaları farklı düğümlerde
  - Kümede dağıtılan toplam ve ikincil değer sayısı
  - Toplam hizmet nesnesi sayısı her bir düğümde eşit olarak ayrılır

İyi!

Varsayılan ölçümler bir başlangıç olarak harika çalışır. Ancak, varsayılan ölçümler sizi yalnızca şu ana kadar taşıyacaktır. Örneğin: seçtiğiniz bölümlendirme düzeninin tüm bölümlerin tam olarak kullanımı için ne olduğu da Belirli bir hizmetin yükünün zaman içinde sabit olması veya yalnızca birden çok bölümde de aynı anda aynı olması olasılığı nedir?

Yalnızca varsayılan ölçülerle çalıştırabilirsiniz. Bununla birlikte, genellikle küme kullanımlarınızın daha az ve daha düzensiz olduğu anlamına gelir. Bunun nedeni, varsayılan ölçümlerin Uyarlamalı olmadığı ve her şeyin eşit olduğu varsayılır. Örneğin, meşgul olan bir birincil olan ve her ikisi de PrimaryCount ölçüsüne "1" katkıda bulunmayan bir birincil. En kötü durumda, yalnızca varsayılan ölçümlerin kullanılması, fazla zamanlanmış düğümlerin performans sorunlarına neden olabilir. Kümenizden en iyi şekilde yararlanmak ve performans sorunlarından kaçınmak istiyorsanız, özel ölçümler ve dinamik yük raporlama kullanmanız gerekir.

## <a name="custom-metrics"></a>Özel ölçümler
Ölçümler, hizmeti oluştururken adlandırılmış hizmet örneği temelinde yapılandırılır.

Her türlü ölçüm, onu tanımlayan bazı özelliklere sahiptir: bir ad, ağırlık ve varsayılan yükleme.

* Ölçüm adı: ölçümün adı. Ölçüm adı, Kaynak Yöneticisi perspektifinden küme içindeki ölçüm için benzersiz bir tanımlayıcıdır.
* Ağırlık: Ölçüm ağırlığı, bu ölçümün bu hizmet için diğer ölçümlere ne kadar önemli olduğunu tanımlar.
* Varsayılan yükleme: varsayılan Yük, hizmetin durum bilgisiz veya durum bilgisiz olmasına bağlı olarak farklı şekilde temsil edilir.
  * Durum bilgisi olmayan hizmetler için, her ölçümün DefaultLoad adlı tek bir özelliği vardır
  * Durum bilgisi olan hizmetler için şunları tanımlarsınız:
    * PrimaryDefaultLoad: Bu hizmetin birincil olduğu zaman kullandığı bu ölçümün varsayılan miktarı
    * SecondaryDefaultLoad: Bu hizmet, Ikincil olduğunda bu ölçümün kullandığı varsayılan miktar

> [!NOTE]
> Özel ölçümleri tanımlayabilir ve varsayılan ölçümleri _de_ kullanmak istiyorsanız, varsayılan ölçümleri _açıkça_ eklemeniz ve bunların ağırlıklarını ve değerlerini tanımlamanız gerekir. Bunun nedeni, varsayılan ölçümler ve özel ölçümleriniz arasındaki ilişkiyi tanımlamanız gerekir. Örneğin, ConnectionCount veya WorkQueueDepth özelliğinden daha fazla birincil dağıtım olabilir. Varsayılan olarak, PrimaryCount ölçüsünün ağırlığı yüksektir. bu nedenle, diğer ölçümlerinizi eklediğinizde daha fazla öncelik aldığından emin olmak için bunu orta düzeyde azaltmak istersiniz.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Hizmetiniz için ölçümleri tanımlama-bir örnek
Aşağıdaki yapılandırmayı istediğinizi varsayalım:

  - Hizmetiniz "ConnectionCount" adlı bir ölçüm bildiriyor
  - Varsayılan ölçümleri de kullanmak istiyorsunuz 
  - Bazı ölçümleri tamamladınız ve normalde bu hizmetin birincil kopyasının "ConnectionCount" 20 birim aldığını öğrendiniz
  - İkincislik 5 birim "ConnectionCount" kullanır
  - "ConnectionCount" öğesinin bu hizmetin performansını yönetme açısından en önemli ölçüm olduğunu bilirsiniz
  - Hala birincil çoğaltmaların dengelenmesi istiyorsunuz. Birincil çoğaltmaların dengelenmesi genellikle önemli bir fikirdir. Bu, bazı düğüm veya hata etki alanının büyük bir kısmını ve onunla birlikte etkilememelerini önlemeye yardımcı olur. 
  - Aksi takdirde, varsayılan ölçümler iyidir

Bu ölçüm yapılandırmasına sahip bir hizmet oluşturmak için yazdığınız kod aşağıda verilmiştir:

Kod:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription connectionMetric = new StatefulServiceLoadMetricDescription();
connectionMetric.Name = "ConnectionCount";
connectionMetric.PrimaryDefaultLoad = 20;
connectionMetric.SecondaryDefaultLoad = 5;
connectionMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(connectionMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> Yukarıdaki örnekler ve bu belgenin geri kalanı, ölçümleri adlandırılmış hizmet temelinde yönetmeyi anlatmaktadır. Hizmet _türü_ düzeyinde hizmetleriniz için ölçüm tanımlamak da mümkündür. Bu, hizmet bildirimlerinizde belirtilerek yapılır. Tür düzeyi ölçümlerinin tanımlanması çeşitli nedenlerle önerilmez. İlk neden, ölçüm adlarının genellikle ortama özgüdür. Bir kesin sözleşme olmadığı takdirde, bir ortamdaki "çekirdekler" ölçüsünün başkalarının "Milfreres" veya "çekirdek" olmadığından emin olabilirsiniz. Ölçümleriniz bildiriminiz tanımlıysa, ortam başına yeni bildirimler oluşturmanız gerekir. Bu, genellikle düşük farklılığı olan farklı bildirimlerin tek bir şekilde oluşturulmasına yol açar ve bu da yönetim zorluklara yol açabilir.  
>
> Ölçüm yüklemeleri genellikle adlandırılmış hizmet örneği temelinde atanır. Örneğin, bu hizmetin yalnızca kendi kullanımını planlayan müşterinin bir örneğini oluşturduğunuzu varsayalım. Ayrıca, daha büyük bir iş yüküne sahip olan CustomerB için bir tane oluşturun. Bu durumda, büyük olasılıkla bu hizmetler için varsayılan yüklemeleri ince ayar isteyeceksiniz. Bildirimler aracılığıyla tanımlanan ölçümler ve yükleriniz varsa ve bu senaryoyu desteklemek istiyorsanız, her müşteri için farklı uygulama ve hizmet türleri gerekir. Hizmet oluşturma zamanında tanımlanan değerler bildirimde tanımlı olanları geçersiz kılar, bu nedenle söz konusu varsayılanları ayarlamak için kullanabilirsiniz. Ancak bunun yapılması, bildirimde belirtilen değerlerin, hizmet ile birlikte çalıştığı hizmetle eşleşmesine neden olur. Bu, karışıklıklara yol açabilir. 
>

Anımsatıcı olarak: yalnızca varsayılan ölçümleri kullanmak istiyorsanız, ölçüm koleksiyonuna her şeyi dokunarak veya hizmetinizi oluştururken özel bir şey yapmanız gerekmez. Varsayılan ölçümler, diğerleri tanımlanmadığında otomatik olarak kullanılır. 

Şimdi bu ayarların her birini daha ayrıntılı bir şekilde inceleyelim ve etkilediği davranış hakkında konuşalım.

## <a name="load"></a>Yükleme
Ölçümlerin tanımlanmasıyla bir bütün yük temsil edilir. *Yük* , belirli bir düğümdeki bazı hizmet örneği veya çoğaltma tarafından tüketilen bir metriğin ne kadarının tüketildiğini yükler. Yükleme, neredeyse her noktada yapılandırılabilir. Örnek:

  - Yükleme, bir hizmet oluşturulduğunda tanımlanabilir. Bu tür yük yapılandırması _varsayılan yükleme_ olarak adlandırılır.
  - Hizmet için varsayılan yüklemeler dahil ölçüm bilgileri, hizmet oluşturulduktan sonra güncelleştirilebilen olabilir. Bu ölçüm güncelleştirmesi _bir hizmet güncelleştirilerek_ yapılır.
  - Belirli bir bölümün yükleri bu hizmet için varsayılan değerlere sıfırlanabilir. Bu ölçüm güncelleştirmesi, _bölüm yükünü sıfırlama_ olarak adlandırılır.
  - Yük, çalışma zamanında dinamik olarak bir hizmet nesnesi başına rapor edilebilir. Bu ölçüm güncelleştirmesine _Raporlama yükü_ denir.
  - Bölümün çoğaltmaları veya örnekleri için yük, yapı API çağrısı aracılığıyla yük değerleriyle de yapılandırılabilir. Bu ölçüm güncelleştirmesine _bir bölüm için raporlama yükü_ denir.

Bu stratejilerin hepsi ömrü boyunca aynı hizmet içinde kullanılabilir.

## <a name="default-load"></a>Varsayılan yükleme
*Varsayılan Yük* , bu hizmetin her bir hizmet nesnesi (durum bilgisiz örneği veya durum bilgisi olmayan çoğaltma) tarafından tükettiği ölçüdür. Küme Kaynak Yöneticisi, bu numarayı, bir dinamik yük raporu gibi diğer bilgileri alıncaya kadar hizmet nesnesinin yükü için kullanır. Daha basit hizmetler için, varsayılan yükleme statik bir tanımdır. Varsayılan yükleme hiçbir şekilde güncellenmez ve hizmetin kullanım süresi boyunca kullanılır. Varsayılan Yüklemeler, belirli miktardaki kaynakların farklı iş yüklerine ayrılmasının ve değişmediğinden basit kapasite planlama senaryolarında harika bir şekilde çalışmaktadır.

> [!NOTE]
> Kapasite yönetimi hakkında daha fazla bilgi edinmek ve kümenizdeki düğümlere yönelik kapasiteleri tanımlamak için lütfen [Bu makaleye](service-fabric-cluster-resource-manager-cluster-description.md#capacity)bakın.
> 

Küme Kaynak Yöneticisi, durum bilgisi olan hizmetlerin, kendi ve Ikincil öğeler için farklı bir varsayılan Yük belirlemesine izin verir. Durum bilgisi olmayan hizmetler, yalnızca tüm örneklere uygulanan bir değer belirtebilir. Durum bilgisi olan hizmetler için, birincil ve Ikincil çoğaltmalara yönelik varsayılan Yük genellikle farklıdır çünkü çoğaltmalar her rolde farklı iş türlerine sahiptir. Örneğin, bu durumlar genellikle hem okuma hem de yazma işlemleri yapar ve ikincil öğeler olmasa da çoğu işlem yükünü işler. Genellikle birincil çoğaltma için varsayılan Yük, ikincil çoğaltmalar için varsayılan yükün daha yüksektir. Gerçek sayıların kendi ölçümlerinize bağlı olması gerekir.

## <a name="dynamic-load"></a>Dinamik yük
Hizmetinizi bir süredir çalıştırdığınızı varsayalım. Bazı izlemelerle şunları fark etmiş olursunuz:

1. Belirli bir hizmetin bazı bölümleri veya örnekleri diğerlerinden daha fazla kaynak tüketir
2. Bazı hizmetlerde zamana göre farklılık gösteren yükleme vardır.

Bu tür yük dalgalanmalarına neden olabilecek birçok şey vardır. Örneğin, farklı hizmetler veya bölümler farklı gereksinimlere sahip farklı müşterilerle ilişkilendirilir. Ayrıca, hizmetin gün boyunca gösterdiği iş miktarı farklılık gösterdiğinden, yükleme da değişebilir. Nedeninden bağımsız olarak, genellikle varsayılan olarak kullanabileceğiniz tek bir sayı yoktur. Kümeden en fazla kullanımı sağlamak istiyorsanız bu özellikle doğrudur. Varsayılan yükleme için seçtiğiniz herhangi bir değer, bazı zamandan dolayı yanlış. Hatalı varsayılan Yüklemeler, kaynak ayırma üzerinde veya altında küme Kaynak Yöneticisi sonucunu elde edebilir. Sonuç olarak, Küme Kaynak Yöneticisi kümenin dengeli olmasına rağmen, daha fazla veya daha fazla düğüm vardır. İlk yerleştirme için bazı bilgiler sağladıklarından varsayılan yüklemeler hala iyidir, ancak gerçek iş yükleri için tamamen bir hikaye değildir. Değişen kaynak gereksinimlerini doğru bir şekilde yakalamak için Küme Kaynak Yöneticisi, her bir hizmet nesnesinin çalışma zamanı sırasında kendi yükünü güncelleştirmesine izin verir. Bu, dinamik yük raporlama olarak adlandırılır.

Dinamik yük raporları, çoğaltmalarının/rapor verme sürelerinin kullanım ömrü boyunca kendilerine ait ayırma/raporlama yükünü ayarlamasına olanak tanır. Soğuk olan ve herhangi bir iş yapmamayan bir hizmet çoğaltması veya örneği genellikle belirli bir metriğin düşük miktarlarda kullandığını bildirir. Meşgul bir çoğaltma veya örnek, daha fazla kullandıklarından rapor verebilir.

Çoğaltma veya örnek başına raporlama yükü Küme Kaynak Yöneticisi kümedeki tek tek hizmet nesnelerini yeniden düzenleyecek şekilde izin verir. Hizmetleri yeniden düzenlemek, ihtiyaç duydukları kaynakları aldığından emin olmanıza yardımcı olur. Meşgul hizmetler, diğer çoğaltmalardan veya şu anda soğuk veya daha az iş yapan örneklerden "geri kazanma" kaynaklarını etkin bir şekilde alır.

Reliable Services içinde, yükü dinamik olarak raporlamak için kod şöyle görünür:

Kod:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

Bir hizmet, oluşturma sırasında kendisi için tanımlanan ölçümleri rapor edebilir. Bir hizmet, kullanılmak üzere yapılandırılmadığı bir ölçüm için yük bildirirse Service Fabric, bu raporu yoksayar. Aynı anda geçerli olan başka ölçümler varsa, bu raporlar kabul edilir. Hizmet kodu, sahip olduğu tüm ölçümleri ölçebilir ve rapor verebilir ve işleçler hizmet kodunu değiştirmek zorunda kalmadan kullanılacak ölçüm yapılandırmasını belirtebilir. 

## <a name="reporting-load-for-a-partition"></a>Bölüm için rapor yükleme
Önceki bölümde, hizmet çoğaltmalarının veya örneklerinin rapor yükünün nasıl çalıştığı açıklanmaktadır. Load komutunu FabricClient ile dinamik olarak rapor etmek için ek bir seçenek mevcuttur. Bir bölüm için yük raporlama yaparken, bir seferde birden çok bölüm rapor edebilirsiniz.

Bu raporlar, çoğaltmalardan veya örneklerden gelen yükleme raporlarının tam olarak aynı şekilde kullanılacaktır. Bildirilen değerler, yeni yükleme değerleri, çoğaltma veya örnek tarafından bildirilene veya bir bölüm için yeni bir yükleme değeri raporlanana kadar geçerli olacaktır.

Bu API ile, kümedeki yükü güncelleştirmek için birden çok yol vardır:

  - Durum bilgisi olan bir hizmet bölümü, birincil çoğaltma yükünü güncelleştirebilir.
  - Hem durum bilgisi olmayan hem de durum bilgisi olan hizmetler, tüm ikincil çoğaltmalarının veya örneklerinin yükünü güncelleştirebilir.
  - Hem durum bilgisi olmayan hem de durum bilgisi olan hizmetler bir düğümdeki belirli bir çoğaltmanın veya örneğin yükünü güncelleştirebilir.

Aynı zamanda her bölüm için bu güncelleştirmelerden herhangi birini birleştirmek mümkündür.

Birden çok bölüm için yükleme yüklemeleri, tek bir API çağrısıyla yapılabilir ve bu durumda çıktı her bölüm için bir yanıt içerecektir. Bölüm güncelleştirmesi herhangi bir nedenle başarıyla uygulanmadıysa, bu bölüm için güncelleştirmeler atlanır ve hedeflenen bir bölüm için karşılık gelen hata kodu sağlanır:

  - PartitionNotFound-belirtilen bölüm KIMLIĞI yok.
  - ReconfigurationPending-bölüm şu anda yeniden yapılandırıyor.
  - Invalidforstatelessservices-durum bilgisiz hizmetine ait olan bir bölüm için birincil çoğaltmanın yükünü değiştirme girişiminde bulunuldu.
  - Replicayok notexist-belirtilen düğümde Ikincil çoğaltma veya örnek yok.
  - InvalidOperation-iki durumda gerçekleşebilir: sistem uygulamasına ait olan veya tahmin edilen yükü güncelleştiren bir bölümün yükünü güncelleştirme etkin değil.

Bu hatalardan bazıları döndürülürse, belirli bir bölümün girişini güncelleştirebilir ve belirli bir bölüm için güncelleştirmeyi yeniden deneyebilirsiniz.

Kod:

```csharp
Guid partitionId = Guid.Parse("53df3d7f-5471-403b-b736-bde6ad584f42");
string metricName0 = "CustomMetricName0";
List<MetricLoadDescription> newPrimaryReplicaLoads = new List<MetricLoadDescription>()
{
    new MetricLoadDescription(metricName0, 100)
};

string nodeName0 = "NodeName0";
List<MetricLoadDescription> newSpecificSecondaryReplicaLoads = new List<MetricLoadDescription>()
{
    new MetricLoadDescription(metricName0, 200)
};

OperationResult<UpdatePartitionLoadResultList> updatePartitionLoadResults =
    await this.FabricClient.UpdatePartitionLoadAsync(
        new UpdatePartitionLoadQueryDescription
        {
            PartitionMetricLoadDescriptionList = new List<PartitionMetricLoadDescription>()
            {
                new PartitionMetricLoadDescription(
                    partitionId,
                    newPrimaryReplicaLoads,
                    new List<MetricLoadDescription>(),
                    new List<ReplicaMetricLoadDescription>()
                    {
                        new ReplicaMetricLoadDescription(nodeName0, newSpecificSecondaryReplicaLoads)
                    })
            }
        },
        this.Timeout,
        cancellationToken);
```

Bu örnekte, _53df3d7f-5471-403b-B736-bde6ad584f42_ bölümünde bildirilen son yükün güncelleştirilmesini gerçekleştirirsiniz. Ölçüm _CustomMetricName0_ için birincil çoğaltma yükü 100 değeriyle güncelleştirilir. Aynı zamanda, _NodeName0_ düğümünde bulunan belirli bir ikincil çoğaltma için aynı ölçüm için yük, 200 değeriyle güncelleştirilir.

### <a name="updating-a-services-metric-configuration"></a>Hizmetin ölçüm yapılandırmasını güncelleştirme
Hizmetle ilişkili ölçümlerin listesi ve bu ölçümlerin özellikleri, hizmet canlı olduğu sürece dinamik olarak güncellenebilir. Bu, deneme ve esneklik sağlar. Bunun yararlı olması için bazı örnekler şunlardır:

  - belirli bir hizmet için önemlidir Report ile bir ölçümü devre dışı bırakma
  - ölçüm ağırlıklarını istenen davranışa göre yeniden yapılandırma
  - yalnızca kod dağıtıldıktan ve diğer mekanizmalar aracılığıyla doğrulandıktan sonra yeni bir ölçüm etkinleştiriliyor
  - gözlemlenen davranış ve tüketim temelinde bir hizmetin varsayılan yükünü değiştirme

Ölçüm yapılandırmasını değiştirmek için ana API 'Ler `FabricClient.ServiceManagementClient.UpdateServiceAsync` C# ve PowerShell içinde bulunur `Update-ServiceFabricService` . Bu API 'lerle belirttiğiniz bilgiler, hizmet için mevcut ölçüm bilgilerinin hemen yerini alır. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Varsayılan yükleme değerlerini ve dinamik yükleme raporlarını karıştırma
Varsayılan yükleme ve dinamik yüklemeler aynı hizmet için kullanılabilir. Bir hizmet hem varsayılan yükleme hem de dinamik yükleme raporlarını kullanırsa, dinamik raporların görünmesi için varsayılan yükleme tahmini olarak görev yapar. Varsayılan yükleme iyi bir şeydir çünkü kümeye birlikte çalışmak üzere Kaynak Yöneticisi. Varsayılan yükleme, Küme Kaynak Yöneticisi, hizmet nesnelerini oluşturuldukları sırada iyi konumlara yerleştirmesini sağlar. Varsayılan yükleme bilgisi sağlanmazsa, hizmetlerin yerleştirilmesi etkili bir şekilde rasgele olur. Yükleme raporları daha sonra geldiğinde ilk rastgele yerleştirme genellikle yanlıştır ve Küme Kaynak Yöneticisi Hizmetleri taşımak zorunda olur.

Daha önceki örneğimize bakalım ve bazı özel ölçümler ve dinamik yük raporlama eklediğimiz zaman ne olacağını görün. Bu örnekte, örnek ölçüm olarak "Memorınmb" kullanırız.

> [!NOTE]
> Bellek, Service Fabric [kaynak](service-fabric-resource-governance.md)tarafından okunabilir ve kendi kendinize bildirimde bulunan sistem ölçümlerinden biridir. Bellek tüketimini raporlamak için gerçekten beklenmezsiniz; Bellek, burada Küme Kaynak Yöneticisi özellikleri hakkında bilgi edinmeye yönelik bir yardım olarak kullanılır.
>

İlk olarak aşağıdaki komutla durum bilgisi olan hizmeti oluşturduğumuz kabul edelim:

PowerShell

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Anımsatıcı olarak, bu söz dizimi ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad") ' dir.

Olası bir küme düzeninin nasıl görünebileceklerini görelim:

<center>

![Küme hem varsayılan hem de özel ölçümler ile dengeli][Image2]
</center>

Dikkat edilmesi gereken bazı şeyler:

* Bir bölüm içindeki ikincil çoğaltmaların her biri kendi yüküne sahip olabilir
* Genel ölçümler dengeli şekilde görünür. Bellek için, en yüksek ve en düşük yük arasındaki oran 1,75 ' dir (en fazla yükün bulunduğu düğüm N3, en az N2 ve 28/16 = 1,75).

Yine de açıklanbilmemiz gereken bazı şeyler vardır:

* 1,75 oranının makul olup olmadığını ne belirler? Kümenin yeterince iyi olduğunu veya daha fazla iş olduğunu belirten küme Kaynak Yöneticisi nasıl bilir?
* Dengeleme ne zaman gerçekleşir?
* Belleğin "yüksek" ağırlıklı olduğunu ne anlama geliyor?

## <a name="metric-weights"></a>Ölçüm ağırlıkları
Farklı hizmetler genelinde aynı ölçümleri izlemek önemlidir. Bu genel görünüm, Küme Kaynak Yöneticisi kümedeki tüketimi izlemelerine, düğümlerde tüketim dengelemeye ve düğümlerin kapasiteye gitmemesini sağlamaya olanak tanır. Ancak, hizmetler aynı ölçümün önem derecesine göre farklı görünümlere sahip olabilir. Ayrıca, çok sayıda ölçüm ve çok hizmet içeren bir kümede, tüm ölçümler için kusursuz dengelenmiş çözümler bulunmayabilir. Küme Kaynak Yöneticisi bu durumları nasıl işleymelidir?

Ölçüm ağırlıkları, tam yanıt olmadığında Küme Kaynak Yöneticisi kümenin nasıl dengelemeye karar vermesini sağlar. Ölçüm ağırlıkları, Küme Kaynak Yöneticisi belirli hizmetleri farklı şekilde dengelemenize de olanak tanır. Ölçümler dört farklı ağırlık düzeyine sahip olabilir: sıfır, düşük, orta ve yüksek. Sıfır ağırlığa sahip bir ölçüm, öğelerin dengelenmesi düşünüldüğünde hiçbir şey yapmaz. Ancak, yükü hala kapasite yönetimine katkıda bulunur. Sıfır ağırlığa sahip ölçümler hala yararlı olur ve genellikle hizmet davranışı ve performans izlemenin bir parçası olarak kullanılır. [Bu makalede](service-fabric-diagnostics-event-generation-infra.md) , hizmetlerinizin izlenmesi ve tanılanmasına yönelik ölçümlerin kullanımı hakkında daha fazla bilgi verilmektedir. 

Kümedeki farklı ölçüm ağırlıklarının gerçek etkisi, Küme Kaynak Yöneticisi farklı çözümler üretmeleridir. Ölçüm ağırlıkları, belirli ölçümlerin diğerlerinden daha önemli olduğunu Kaynak Yöneticisi kümeye bildirir. Kusursuz çözüm olmadığında Küme Kaynak Yöneticisi, daha yüksek ağırlıklı ölçümleri dengelemeye yönelik çözümleri tercih edebilir. Belirli bir ölçümü önemli olmayan bir hizmet, bu ölçüm 'in kullanımını bulabilir. Bu, başka bir hizmetin, sizin için önemli olan bazı ölçüm için eşit bir dağıtım almasına olanak tanır.

Bazı yükleme raporlarının bir örneğine ve farklı ölçümün kümedeki farklı ayırmalara neden olduğunu inceleyelim. Bu örnekte, ölçümlerin göreli ağırlığını değiştirme, Küme Kaynak Yöneticisi farklı hizmet düzenlemeleri oluşturmasına neden olur.

<center>

![Ölçüm ağırlığı örneği ve BT çözümleri üzerindeki etkisi][Image3]
</center>

Bu örnekte, hepsi iki farklı ölçüm, MetricA ve Metrıcb için farklı değerler bildiren dört farklı hizmet vardır. Tek bir durumda, MetricA 'yı tanımlayan tüm hizmetler en önemli bir (ağırlık = yüksek) ve metrik ICB 'nin önemli olmayan (ağırlık = düşük) olduğunu tanımlar. Sonuç olarak,, metriyonun Metrıcb 'den daha iyi dengeli olması için kümenin Kaynak Yöneticisi Hizmetleri yerleştirdiğinin görüyoruz. "Daha iyi dengeli", Metrıca 'ın daha düşük bir standart sapma olan Metrıcb 'den daha düşük bir sapmasıdır. İkinci durumda, ölçüm ağırlıklarını tersine çevriyoruz. Sonuç olarak, Küme Kaynak Yöneticisi A ve B hizmetlerini, Metrıcb 'nin MetricA 'dan daha iyi dengelendiği bir ayırma ile birlikte gelmesini sağlar.

> [!NOTE]
> Ölçüm ağırlıkları, Küme Kaynak Yöneticisi dengelenmesinin ne zaman dengelenmesi gerektiğini, ancak dengelemenin ne zaman yapılacağını belirleyebilir. Dengeleme hakkında daha fazla bilgi için [Bu makaleye](service-fabric-cluster-resource-manager-balancing.md) göz atın
>

### <a name="global-metric-weights"></a>Küresel ölçüm ağırlıkları
Diyelim ki, metrika 'ı yüksek ağırlık olarak tanımlıyor ve ServiceB, metrika ağırlığını düşük veya sıfır olarak ayarlıyor. Kullanılan gerçek ağırlık ne kadar doluyor?

Her ölçüm için izlenen birden çok ağırlık vardır. İlk ağırlık, hizmet oluşturulduğunda ölçüm için tanımlanan bir tanımdır. Diğer ağırlık, otomatik olarak hesaplanan küresel bir ağırlıkdır. Küme Kaynak Yöneticisi, Puanlama çözümlerinde bu ağırlıkları kullanır. Her iki ağırlıkların de hesaba alınması önemlidir. Bu, Küme Kaynak Yöneticisi her hizmeti kendi önceliklerine göre dengeleyebilir ve Ayrıca kümenin bir bütün olarak doğru bir şekilde ayrıldığından emin olmanızı sağlar.

Küme Kaynak Yöneticisi hem genel hem de yerel bakiyeyle ilgilenmemişse ne olur? Ayrıca, genel olarak dengeli çözümler oluşturmak kolaydır, ancak bireysel hizmetler için düşük kaynak dengelemeye neden olur. Aşağıdaki örnekte, yalnızca varsayılan ölçümler ile yapılandırılmış bir hizmete bakalım ve yalnızca genel bakiye dikkate alındıklarında ne olacağını görebilirsiniz:

<center>

![Yalnızca genel bir çözümün etkisi][Image4]
</center>

En üstteki örnekte, yalnızca küresel dengelemeye göre kümenin tamamı dengeli olur. Tüm düğümler aynı sayıda baş ve toplam çoğaltma sayısına sahiptir. Ancak, bu ayırmadan ilgili gerçek etki bölümüne bakarsanız, bu durum iyi değildir: herhangi bir düğümün kaybı, belirli bir iş yükünü olumsuz şekilde etkiler, çünkü tüm alt özelliklerini açığa çıkar. Örneğin, ilk düğüm, Circle hizmetinin üç farklı bölümünün üç için de başarısız olursa, hepsi kaybedilir. Buna karşılık, üçgenin ve altıgen hizmetlerin bölümleri bir çoğaltmayı kaybeder. Bu, düşük çoğaltmayı kurtarmak zorunda kalmadan kesintiye neden olmaz.

Alt örnekte, Küme Kaynak Yöneticisi çoğaltmaları hem genel hem de hizmet başına bakiyeye göre dağıtmıştır. Çözümün puanı hesaplanırken genel çözüme ağırlığı ve bireysel hizmetlere (yapılandırılabilir) bir bölümü verir. Bir ölçüm için küresel Bakiye, her bir hizmetten ölçüm ağırlıklarının ortalaması temel alınarak hesaplanır. Her hizmet kendi tanımlı ölçüm ağırlıklarına göre dengelenir. Bu, hizmetlerin kendi ihtiyaçlarına uygun olarak kendi içinde dengelenmesini sağlar. Sonuç olarak, aynı ilk düğüm başarısız olursa, hata tüm hizmetlerin tüm bölümlerine dağıtılır. Her birinin etkisi aynı olur.

## <a name="next-steps"></a>Sonraki adımlar
- Hizmetleri yapılandırma hakkında daha fazla bilgi için, [Hizmetleri yapılandırma hakkında bilgi edinin](service-fabric-cluster-resource-manager-configure-services.md)(Service-Fabric-Cluster-Resource-Manager-configure-Services.MD)
- Birleştirme ölçümlerini tanımlamak yerine düğümlerin yükünü birleştirmek için bir yoldur. Birleştirme yapılandırma hakkında bilgi edinmek için [Bu makaleye](service-fabric-cluster-resource-manager-defragmentation-metrics.md) bakın
- Kaynak Yöneticisi kümenin, kümedeki yükü nasıl yönettiğini ve dengelemediğini öğrenmek için [Yük Dengeleme](service-fabric-cluster-resource-manager-balancing.md) sayfasındaki makaleye göz atın
- Baştan başlayın ve [Service Fabric kümesine giriş yapın Kaynak Yöneticisi](service-fabric-cluster-resource-manager-introduction.md)
- Taşıma maliyeti, belirli hizmetlerin diğerlerine göre daha pahalı olduğunu Kaynak Yöneticisi küme sinyalinden bir yoldur. Taşıma maliyeti hakkında daha fazla bilgi edinmek için [Bu makaleye](service-fabric-cluster-resource-manager-movement-cost.md) bakın

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
