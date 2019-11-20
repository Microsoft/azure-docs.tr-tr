---
title: Service Fabric Services ölçeklenebilirliği | Microsoft Docs
description: Service Fabric hizmetlerini nasıl ölçeklendirebileceğinizi açıklar
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/26/2019
ms.author: masnider
ms.openlocfilehash: f44a44c0923374b2f6024903213305f1defb3b94
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035931"
---
# <a name="scaling-in-service-fabric"></a>Service Fabric ölçeklendirme
Azure Service Fabric, bir kümenin düğümlerinde Hizmetleri, bölümleri ve çoğaltmaları yöneterek ölçeklenebilir uygulamalar oluşturmayı kolaylaştırır. Aynı donanımda birçok iş yükünün çalıştırılması maksimum kaynak kullanımını sağlar, ancak aynı zamanda iş yüklerinizi ölçeklendirmeye nasıl seçeceğiniz konusunda esneklik sağlar. Bu Channel 9 videosu, ölçeklenebilir mikro hizmet uygulamaları oluşturmayı açıklar:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

Service Fabric ölçeklendirme birkaç farklı şekilde gerçekleştirilir:

1. Durum bilgisi olmayan hizmet örnekleri oluşturarak veya kaldırarak ölçekleme
2. Yeni adlandırılmış hizmetler oluşturarak veya kaldırarak ölçekleme
3. Yeni adlandırılmış uygulama örnekleri oluşturarak veya kaldırarak ölçekleme
4. Bölümlenmiş Hizmetleri kullanarak ölçeklendirme
5. Kümeden düğüm ekleyerek ve kaldırarak ölçekleme 
6. Küme Kaynak Yöneticisi ölçümlerini kullanarak ölçeklendirme

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Durum bilgisi olmayan hizmet örnekleri oluşturarak veya kaldırarak ölçekleme
Service Fabric içinde ölçeklendirmenin en basit yöntemlerinden biri, durum bilgisi olmayan hizmetlerle birlikte çalışmaktadır. Durum bilgisi olmayan bir hizmet oluşturduğunuzda, tanımlama `InstanceCount`şansı elde edersiniz. `InstanceCount`hizmet başlatıldığında hizmetin kodunun kaç tane çalışan kopyasının oluşturulduğunu tanımlar. Örneğin, kümede 100 düğüm olduğunu varsayalım. Ayrıca, 10 ' un bir `InstanceCount` hizmetin oluşturulduğunu de söylayalım. Çalışma zamanı sırasında kodun çalışan 10 kopyası hepsi çok meşgul hale gelebilir (veya yeterince meşgul olmayabilir). Bu iş yükünü ölçeklendirmenin bir yolu, örnek sayısını değiştirmektir. Örneğin, bazı izleme veya yönetim kodu parçaları, iş yükünün yük temelinde ölçeklendirilmesine veya kullanıma hazır olmasına bağlı olarak, mevcut örnek sayısını 50 veya 5 olarak değiştirebilir. 

C# İÇİN:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Dinamik örnek sayısı kullanma
Özel durum bilgisi olmayan hizmetler için, Service Fabric örnek sayısını değiştirmek için otomatik bir yol sunar. Bu, hizmetin kullanılabilir düğüm sayısıyla dinamik olarak ölçeklendirilmesine olanak tanır. Bu davranışı kabul etmenin yolu, örnek sayısı =-1 ' i ayarlamanıza olanak sağlar. InstanceCount =-1, "Bu durum bilgisiz hizmeti her düğümde Çalıştır" diyen Service Fabric bir yönergedir. Düğüm sayısı değişirse Service Fabric, hizmetin tüm geçerli düğümlerde çalıştığından emin olmak için örnek sayısını eşleşecek şekilde otomatik olarak değiştirir. 

C# İÇİN:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Yeni adlandırılmış hizmetler oluşturarak veya kaldırarak ölçekleme
Adlandırılmış bir hizmet örneği, kümedeki bazı adlandırılmış uygulama örnekleri içinde hizmet türünün belirli bir örneğidir (bkz. [uygulama yaşam döngüsü Service Fabric](service-fabric-application-lifecycle.md)). 

Hizmetler daha fazla veya daha az hale geldiği için yeni adlandırılmış hizmet örnekleri oluşturulabilir (veya kaldırılabilir). Bu, isteklerin daha fazla hizmet örneğine yayılmasını sağlar, genellikle mevcut hizmetlerde yükün azaltılmasına izin verir. Hizmet oluştururken, Service Fabric kümesi Kaynak Yöneticisi Hizmetleri dağıtılmış bir biçimde kümeye koyar. Tam kararlar, kümedeki [ölçümlere](service-fabric-cluster-resource-manager-metrics.md) ve diğer yerleştirme kurallarına tabidir. Hizmetler birkaç farklı şekilde oluşturulabilir, ancak en yaygın olarak, biri çağıran [`New-ServiceFabricService`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)ya da kod çağırarak [`CreateServiceAsync`](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet)yönetim eylemleridir. `CreateServiceAsync`, kümede çalışan diğer hizmetlerden da çağrılabilir.

Hizmetlerin dinamik olarak oluşturulması, her tür senaryoda kullanılabilir ve ortak bir modeldir. Örneğin, belirli bir iş akışını temsil eden bir durum bilgisi olan hizmeti düşünün. İşi temsil eden çağrılar bu hizmete kadar görünür ve bu hizmet bu iş akışına yönelik adımları yürütecek ve ilerlemeyi kaydetmeye devam etmektedir. 

Bu belirli hizmet ölçeğini nasıl yaparsınız? Hizmet, bazı bir biçimde birden çok kiracı olabilir ve aynı iş akışının birçok farklı örneği için aynı anda birden çok farklı örnek için çağrıları kabul edebilir ve adımları başlatabilir. Ancak, artık aynı iş akışının birçok farklı örneğine, farklı aşamalara ve farklı müşterilere kadar endişelenmek zorunda olduğundan, bu kodu daha karmaşık hale getirebilirsiniz. Aynı zamanda birden çok iş akışını aynı anda işlemek, ölçek sorununu çözmez. Bunun nedeni, bazı bir noktada bu hizmetin belirli bir makineye sığması için çok fazla kaynak tüketmesi olacaktır. İlk yerde bu model için derlenmedi birçok hizmet, kendi kodlarında bazı ilgili performans veya yavaşlama nedeniyle zorluk gösterebilir. Bu tür sorunlar, bir hizmetin, izlenen eşzamanlı iş akışı sayısı daha büyük olduğunda da çalışmamasına neden olur.  

Bir çözüm, izlemek istediğiniz iş akışının her farklı örneği için bu hizmetin bir örneğini oluşturmaktır. Bu harika bir modeldir ve hizmetin durum bilgisiz veya durum bilgisi olup olmadığı konusunda çalışmaktadır. Bu düzenin çalışması için genellikle "Iş yükü Yöneticisi hizmeti" olarak davranan başka bir hizmet vardır. Bu hizmetin işi istekleri almak ve bu istekleri diğer hizmetlere yönlendirmekte. Yönetici iletiyi aldığında iş yükü hizmetinin bir örneğini dinamik olarak oluşturabilir ve istekleri bu hizmetlere geçirebilir. Belirli bir iş akışı hizmeti işini tamamladığında, yönetici hizmeti de geri çağrılar alabilir. Yönetici bu geri çağırmaları aldığında, iş akışı hizmetinin bu örneğini silebilir veya daha fazla çağrı bekleniyorsa onu bırakabilirsiniz. 

Bu tür bir yöneticinin gelişmiş sürümleri, yönettiği hizmetlerin havuzlarını bile oluşturabilir. Havuz, yeni bir istek geldiğinde hizmetin dönmesini beklemek zorunda olmadığından emin olmanıza yardımcı olur. Bunun yerine, yönetici yalnızca havuzdan meşgul olmayan bir iş akışı hizmeti seçebilir ya da rastgele yol açabilir. Bir hizmet havuzunun kullanılabilir tutulması, isteğin yeni bir hizmetin sonlanmasını beklemek gerektiğinden, yeni istekleri daha hızlı işlemesini sağlar. Yeni hizmetler oluşturmak hızlı, ancak ücretsiz veya anlık değildir. Havuz, isteğin hizmet vermeden önce beklemesi gereken süreyi en aza indirmenize yardımcı olur. En çok yanıt süresi en fazla olduğunda bu yöneticiyi ve havuz modelini görürsünüz. İsteği kuyruğa alma ve hizmeti arka planda oluşturma ve _daha sonra_ , hizmetin Şu anda bekleyen iş miktarının bir izlemeye göre hizmetleri oluşturup silen gibi popüler bir yönetici de vardır. 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Yeni adlandırılmış uygulama örnekleri oluşturarak veya kaldırarak ölçekleme
Tüm uygulama örneklerinin oluşturulması ve silinmesi, hizmet oluşturma ve silme düzenine benzer. Bu model için, görmekte olan isteklere ve küme içindeki diğer hizmetlerden aldığı bilgilere göre karar veren bir yönetici hizmeti vardır. 

Ne zaman var olan bir uygulamada yeni bir adlandırılmış hizmet örnekleri oluşturmak yerine yeni bir adlandırılmış uygulama örneği oluşturulması gerekir? Birkaç durum vardır:

  * Yeni uygulama örneği, kodu belirli bir kimlik veya güvenlik ayarları altında çalışması gereken bir müşteri içindir.
    * Service Fabric, farklı kod paketlerinin belirli kimlikler altında çalışacak şekilde tanımlanmasını sağlar. Farklı kimlikler altında aynı kod paketini başlatmak için, etkinleştirmelerin farklı uygulama örneklerinde gerçekleşmesi gerekir. Mevcut bir müşterinin iş yüklerinin dağıtıldığı bir durumu göz önünde bulundurun. Bunlar, uzak veritabanları veya diğer sistemler gibi diğer kaynaklara erişimi izleyip denetleyebilmeniz için belirli bir kimlik altında çalışıyor olabilir. Bu durumda, yeni bir müşteri kaydolduğunda, büyük olasılıkla kodlarını aynı bağlamda (işlem alanı) etkinleştirmek istemezsiniz. Mümkün olsa da, bu, servis kodunuzun belirli bir kimlik bağlamı dahilinde çalışmasını zorlaştırır. Genellikle daha fazla güvenlik, yalıtım ve kimlik yönetim kodu olmalıdır. Aynı uygulama örneği içinde farklı adlandırılmış hizmet örnekleri kullanmak ve bu nedenle aynı işlem alanına sahip olmak yerine farklı adlandırılmış Service Fabric uygulama örnekleri kullanabilirsiniz. Bu, farklı kimlik bağlamlarının tanımlanması kolaylaşır.
  * Yeni uygulama örneği aynı zamanda bir yapılandırma yolu görevi görür
    * Varsayılan olarak, bir uygulama örneği içindeki belirli bir hizmet türünün tüm adlandırılmış hizmet örnekleri, belirli bir düğümdeki aynı işlemde çalışır. Bunun anlamı, her bir hizmet örneğini farklı şekilde yapılandırabilirken, bu durum karmaşıktır. Hizmetlerin bir yapılandırma paketi içinde yapılandırmalarını aramak için kullandıkları bazı belirteçleri olmalıdır. Genellikle bu yalnızca hizmetin adıdır. Bu sorunsuz bir şekilde çalışıyor, ancak yapılandırmayı bu uygulama örneği içindeki bireysel adlandırılmış hizmet örneklerinin adlarına bağar. Yapılandırma normalde uygulama örneğine özgü değerlerle tasarım zamanı yapıtı olduğundan, bu kafa karıştırıcı ve yönetimi zor olabilir. Her zaman daha fazla hizmet oluşturmak, yapılandırma paketlerindeki bilgileri değiştirmek veya yeni hizmetlerin belirli bilgilerini aramak üzere yenilerini dağıtmak için daha fazla uygulama yükseltmesi anlamına gelir. Yeni bir adlandırılmış uygulama örneğinin tümüyle oluşturulması genellikle daha kolay. Ardından, hizmetler için hangi yapılandırmanın gerekli olduğunu ayarlamak üzere uygulama parametrelerini kullanabilirsiniz. Bu şekilde, bu adlandırılmış uygulama örneği içinde oluşturulan tüm hizmetler belirli yapılandırma ayarlarını alabilir. Örneğin, her müşterinin gizli dizi ayarları ve özelleştirmeleri gibi tek bir yapılandırma dosyasına sahip olmak yerine, bu ayarlara sahip her müşteri için farklı bir uygulama örneği olması gerekir. geçersiz kılınan. 
  * Yeni uygulama bir yükseltme sınırı görevi görür
    * Service Fabric içinde farklı adlandırılmış uygulama örnekleri yükseltme için sınır olarak görev yapar. Adlandırılmış bir uygulama örneğinin bir yükseltmesi, başka bir adlandırılmış uygulama örneğinin çalıştırıldığı kodu etkilemez. Farklı uygulamalar aynı düğümlerin farklı sürümlerini aynı düğümlerde çalıştırmaya sona acaktır. Yeni kodun başka bir hizmet ile aynı yükseltmelere göre izlenmesi gerekip gerekmediğini seçebilmeniz için bir ölçeklendirme kararı oluşturmanız gerektiğinde bu bir etken olabilir. Örneğin, bir çağrının, belirli bir müşterinin iş yüklerini ölçeklendirmekten sorumlu Yönetim hizmetine, Hizmetleri dinamik olarak oluşturup silerek sorumlu olduğunu varsayalım. Ancak bu durumda, çağrı _Yeni_ bir müşteriyle ilişkili bir iş yüküne yöneliktir. Çoğu müşteri, yalnızca daha önce listelenen güvenlik ve yapılandırma nedenleriyle değil, ancak yazılımın belirli sürümlerini çalıştırmanın ve ne zaman yükseltildikleri gibi daha fazla esneklik sağladığından daha fazla esneklik sağladığından daha fazla esneklik sağlar. Ayrıca, bir yükseltmenin dokunmasına yönelik hizmetlerinizin miktarını daha fazla bölümlemek için yeni bir uygulama örneği oluşturabilir ve bu hizmeti oluşturabilirsiniz. Ayrı uygulama örnekleri, uygulama yükseltmeleri yaparken daha fazla ayrıntı düzeyi sağlar ve ayrıca bir/B testini ve mavi/yeşil dağıtımları etkinleştirir. 
  * Mevcut uygulama örneği dolu
    * Service Fabric, [uygulama kapasitesi](service-fabric-cluster-resource-manager-application-groups.md) , belirli uygulama örnekleri için kullanılabilir kaynak miktarını denetlemek için kullanabileceğiniz bir kavramdır. Örneğin, belirli bir hizmetin ölçeklendirmek için başka bir örneğin oluşturulmasını gerektiren bir hizmet olduğuna karar verebilirsiniz. Ancak, bu uygulama örneği belirli bir ölçüm için kapasite dışında. Bu müşteriye veya iş yüküne hala daha fazla kaynak verilmesi gerekiyorsa, bu uygulama için mevcut kapasiteyi artırabilir ya da yeni bir uygulama oluşturabilirsiniz. 

## <a name="scaling-at-the-partition-level"></a>Bölüm düzeyinde ölçekleme
Service Fabric bölümlemeyi destekler. Bölümlendirme bir hizmeti, her biri bağımsız olarak çalışan birkaç mantıksal ve fiziksel bölüme ayırır. Tek bir çoğaltma kümesinin tüm çağrıları işlemesi ve tüm durumu tek seferde işlemesi gerektiğinden, bu durum bilgisi olmayan hizmetlerle yararlıdır. [Bölümlendirme genel bakışı](service-fabric-concepts-partitioning.md) , desteklenen bölümleme şemaları türleri hakkında bilgi sağlar. Her bölümün çoğaltmaları bir kümedeki düğümlere yayılır, bu hizmetin yükü dağıtılır ve hizmetin tamamı ya da herhangi bir bölüm için tek bir hata noktası olmadığı doğrulanıyor. 

Düşük anahtar 0, yüksek bir 99 ve bölüm sayısı 4 olan bir ranşlı bölümlendirme şeması kullanan bir hizmet düşünün. Üç düğümlü bir kümede, hizmet burada gösterildiği gibi her bir düğümdeki kaynakları paylaşan dört çoğaltmalarla Genişletilebilir:

<center>

![Üç düğüm ile bölüm düzeni](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Düğüm sayısını artırdıysanız, Service Fabric var olan çoğaltmalardan bazılarını buraya taşıyacaktır. Örneğin, düğüm sayısının dört olarak arttığını ve çoğaltmaların yeniden dağıtılması gerektiğini varsayalım. Artık hizmette, her biri farklı bölüme ait olan her düğüm üzerinde çalışan üç çoğaltma vardır. Bu, yeni düğüm soğuk olmadığından daha iyi kaynak kullanımına olanak tanır. Genellikle, her bir hizmetin kullanılabilir kaynakları daha fazla kaynağa sahip olduğu için performansı de artırır.

<center>

![Dört düğüm ile bölüm düzeni](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Service Fabric kümesi Kaynak Yöneticisi ve ölçümleri kullanarak ölçeklendirme
[Ölçümler](service-fabric-cluster-resource-manager-metrics.md) , hizmetlerin kaynak tüketimini Service Fabric için nasıl ifade edin. Ölçüm kullanımı, kümenin yerleşimini yeniden düzenlemek ve iyileştirmek için bir fırsat Kaynak Yöneticisi sağlar. Örneğin, kümede çok fazla kaynak olabilir, ancak şu anda iş yapmakta olan hizmetlere ayrılmayabilir. Ölçüm kullanımı, hizmetlerin kullanılabilir kaynaklara erişiminin olduğundan emin olmak için kümeyi yeniden düzenleyecek Kaynak Yöneticisi sağlar. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Kümeden düğüm ekleyerek ve kaldırarak ölçekleme 
Service Fabric ile ölçeklendirmeye yönelik başka bir seçenek de kümenin boyutunu değiştirir. Küme boyutunun değiştirilmesi, kümedeki düğüm türlerinin bir veya daha fazlası için düğüm ekleme veya kaldırma anlamına gelir. Örneğin, kümedeki tüm düğümlerin etkin olduğu bir durumu göz önünde bulundurun. Bu, kümenin kaynaklarının neredeyse hepsi tüketildiği anlamına gelir. Bu durumda, ölçeklendirmek için en iyi yol, kümeye daha fazla düğüm eklemektir. Yeni düğümler kümeye katılırsanız Service Fabric kümesi Kaynak Yöneticisi Hizmetleri bunlara taşıdıkça, mevcut düğümlerde daha az toplam yük elde edilir. Örnek sayısı =-1 olan durum bilgisi olmayan hizmetler için, daha fazla hizmet örneği otomatik olarak oluşturulur. Bu, bazı çağrıların varolan düğümlerden yeni düğümlere taşınmasını sağlar. 

Daha fazla bilgi için bkz. [küme ölçeklendirme](service-fabric-cluster-scaling.md).

## <a name="choosing-a-platform"></a>Platform seçme

İşletim sistemleri arasındaki uygulama farklılıkları nedeniyle, Windows veya Linux ile Service Fabric kullanmayı seçme, uygulamanızı ölçeklendirmenin önemli bir parçası olabilir. Olası bir engel, aşamalı günlüğün gerçekleştirilme şekli olur. Windows üzerinde Service Fabric, durum bilgisi olan hizmet çoğaltmaları arasında paylaşılan bir makine başına günlük için çekirdek sürücü kullanır. Bu günlük, yaklaşık 8 GB ile ücretlendirilir. Linux, diğer yandan her bir çoğaltma için 256 MB 'lik bir hazırlama günlüğü kullanır ve bu, belirli bir düğümde çalışan hafif hizmet çoğaltmaları sayısını en üst düzeye çıkarmak isteyen uygulamalar için daha az ideal hale getirir. Geçici depolama gereksinimlerindeki bu farklılıklar, Service Fabric küme dağıtımı için istenen platformu bilgilendirebilecek.

## <a name="putting-it-all-together"></a>Hepsini bir araya getirme
Burada tartışıldığı ve bir örnek ile konuşduğumuz tüm fikirleri inceleyelim. Aşağıdaki hizmeti göz önünde bulundurun: adres defteri olarak davranan, adlara ve iletişim bilgilerine sahip bir hizmet oluşturmaya çalışıyorsunuz. 

Sağ tarafta, ölçeklendirmeye ilişkin bir dizi sorunuz var: Kaç Kullanıcı var? Her Kullanıcı için kaç kişi depolanacak? Hizmetinizi ilk kez doldururken bu tümünü belirlemeye çalışmak zordur. Belirli bir bölüm sayısı ile tek bir statik hizmetle gideceğim diyelim. Yanlış bölüm sayısını kaldırmanın sonuçları, daha sonra ölçek sorunları oluşmasına neden olabilir. Benzer şekilde, doğru sayıyı seçmiş olsanız bile, ihtiyacınız olan tüm bilgilere sahip olmayabilirsiniz. Örneğin, küme boyutunun önüne, hem düğüm sayısı hem de boyutlarına göre karar vermeniz gerekir. Genellikle bir hizmetin yaşam süresi boyunca tüketmesi için kaç kaynak olduğunu tahmin etmek zordur. Ayrıca, hizmetin gerçekten gördüğü trafik deseninin önünde haberdar olmak zor olabilir. Örneğin, insanlar kişileri yalnızca sabah ilk bir kez ekleyebilir ve kaldırabilir ya da gün boyunca eşit olarak dağıtılır. Bunu temel alarak, dinamik olarak ve dinamik olarak ölçeklendirmeniz gerekebilir. Büyük olasılıkla, ne zaman ölçeği, ne kadar ölçeklendirmeniz gerektiğini, ancak hizmetinize göre kaynak tüketimini değiştirmeye yanıt vermek için ne kadar iyi bir şekilde bilgi edinebilirsiniz. Bu, mevcut kaynakların kullanımını yeniden düzenleme yeterli olmadığında daha fazla kaynak sağlamak için kümenin boyutunun değiştirilmesini içerebilir. 

Ancak neden tüm kullanıcılar için tek bir bölüm düzeni seçmeyi denemenize de çalışıyor? Neden tek bir hizmetle ve tek bir statik kümeyle sınırlandırım? Gerçek durum genellikle daha dinamik bir durumdur. 

Ölçek için derleme yaparken, aşağıdaki dinamik kalıbı göz önünde bulundurun. Durumunuza uyarlamanız gerekebilir:

1. Herkes için bir bölümleme şeması seçmeyi denemek yerine "Yönetici hizmeti" oluşturun.
2. Yönetici hizmeti 'nin işi, hizmetinize kaydolduklarında müşteri bilgilerine bakabilmenizdir. Daha sonra bu bilgilere bağlı olarak, yönetici hizmeti _gerçek_ iletişim depolama hizmetinizin bir örneğini oluşturur. Belirli yapılandırma, yalıtım veya yükseltmeler gerektiriyorsa, bu müşteri için bir uygulama örneği çalıştırmaya da karar verebilirsiniz. 

Bu dinamik oluşturma deseninin birçok avantajı:

  - Tüm kullanıcılar için doğru bölüm sayısını tahmin etmeye veya kendi kendine sınırsız bir şekilde ölçeklenebilir tek bir hizmet oluşturmaya çalışmayız. 
  - Farklı kullanıcıların aynı bölüm sayısı, çoğaltma sayısı, yerleştirme kısıtlamaları, ölçümler, varsayılan Yüklemeler, hizmet adları, DNS ayarları veya hizmet veya uygulama düzeyinde belirtilen diğer özelliklerden herhangi biri olması gerekmez. 
  - Ek veri segmentlemesini elde edersiniz. Her müşterinin hizmetin bir kopyası vardır
    - Her müşteri hizmeti farklı şekilde yapılandırılabilir ve beklenen ölçeğe bağlı olarak daha fazla veya daha az bölüm ya da çoğaltmalarla daha fazla veya daha az kaynak vermiş olabilir.
      - Örneğin, müşterinin "altın" katman için ödendiğini varsayalım. daha fazla çoğaltma veya daha fazla bölüm sayısı ve potansiyel kaynaklar ölçüm ve uygulama kapasiteleri aracılığıyla hizmetlerine ayrılmıştır.
      - Ya da gerek duydukları kişilerin sayısını belirten bilgileri "küçük" olarak belirttiğinizi varsayalım. yalnızca birkaç bölüm alırlar veya diğer müşterilerle paylaşılan bir hizmet havuzuna de yerleştirilebilir.
  - Müşterilerin gösterilmesini beklerken bir veya daha fazla hizmet örneği veya çoğaltması çalıştırılıyorsunuz
  - Bir müşteri ayrıldıktan sonra, kendi bilgilerini hizmetinizden kaldırmak, yöneticinin oluşturduğu hizmeti veya uygulamayı silmesini sağlayan basit bir işlemdir.

## <a name="next-steps"></a>Sonraki adımlar
Service Fabric kavramları hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Service Fabric hizmetlerinin kullanılabilirliği](service-fabric-availability-services.md)
* [Service Fabric Hizmetleri bölümlendirme](service-fabric-concepts-partitioning.md)
