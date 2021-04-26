---
title: Reliable Actors durum yönetimi
description: Reliable Actors durumunun nasıl yönetildiğini, kalıcı olduğunu ve yüksek kullanılabilirlik için çoğaltıldığını açıklar.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: badfc490f26b71881e7970c2c0be3472abfec25a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575610"
---
# <a name="reliable-actors-state-management"></a>Reliable Actors durum yönetimi
Reliable Actors hem mantığı hem de durumu kapsüllemek için tek iş parçacıklı nesnelerdir. Aktör Reliable Services çalıştığı için, aynı kalıcılık ve çoğaltma mekanizmalarını kullanarak durumu güvenilir bir şekilde koruyabilirler. Bu şekilde, aktörler hatalardan sonra, çöp toplamadan sonra yeniden etkinleştirme sonrasında veya kaynak Dengeleme ya da yükseltmeler nedeniyle bir kümedeki düğümler arasında taşındıktan sonra durumlarını kaybeder.

## <a name="state-persistence-and-replication"></a>Durum kalıcılığı ve çoğaltma
Her aktör örneği benzersiz bir KIMLIĞE eşlendiğinden tüm Reliable Actors *durum bilgisi* olarak değerlendirilir. Bu, aynı aktör KIMLIĞINE yapılan yinelenen çağrıların aynı aktör örneğine yönlendirildiği anlamına gelir. Durum bilgisiz bir sistemde, buna karşılık istemci çağrılarının her seferinde aynı sunucuya yönlendirilmesi garanti edilmez. Bu nedenle, aktör hizmetleri her zaman durum bilgisi olan hizmetlerdir.

Aktör durum bilgisi olarak düşünülse de bu, durumu güvenilir bir şekilde depolamaları anlamına gelmez. Aktör, veri depolama gereksinimlerine bağlı olarak durum kalıcılığı ve çoğaltma düzeyini seçebilir:

* **Kalıcı durum**: durum diske kalıcıdır ve üç veya daha fazla çoğaltmaya çoğaltılır. Kalıcı durum en dayanıklı durum depolama seçeneğidir; burada durum, tüm küme kesintisi ile devam edebilir.
* **Geçici durum**: durum üç veya daha fazla çoğaltmaya çoğaltılır ve yalnızca bellekte tutulur. Geçici durum, düğüm hatasına ve aktör hatasına karşı, yükseltmeler ve kaynak dengeleme sırasında esnekliği sağlar. Ancak, durum diske kalıcı değil. Bu nedenle, tüm çoğaltmalar aynı anda kaybedilmişse durum da kaybedilir.
* **Kalıcı durum yok**: durum çoğaltılmaz veya diske yazılmadı, yalnızca durumu güvenilir bir şekilde sürdürmek zorunda olmayan aktörler için kullanın.

Her bir kalıcılık düzeyi, hizmetinizin yalnızca farklı bir *durum sağlayıcısı* ve *çoğaltma* yapılandırması olur. Durumunun diske yazılıp yazılmayacağı durum sağlayıcısına bağlıdır--durumu depolayan güvenilir bir hizmette bileşen. Çoğaltma, bir hizmetin kaç tane çoğaltmasını dağıttığınıza bağlıdır. Reliable Services olduğu gibi, hem durum sağlayıcısı hem de çoğaltma sayısı el ile kolayca ayarlanabilir. Aktör çerçevesi, bir aktör üzerinde kullanıldığında otomatik olarak varsayılan bir durum sağlayıcısını seçen ve bu üç Kalıcılık ayarlarından birini elde etmek üzere çoğaltma sayısı ayarlarını otomatik olarak oluşturan bir öznitelik sağlar. Statekalıcılık özniteliği türetilmiş sınıf tarafından devralınmaz, her aktör türünün Statekalıcılık düzeyini sağlaması gerekir.

### <a name="persisted-state"></a>Kalıcı durum
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Bu ayar, verileri diskte depolayan ve hizmet çoğaltma sayısını otomatik olarak 3 olarak ayarlayan bir durum sağlayıcısı kullanır.

### <a name="volatile-state"></a>Geçici durum
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Bu ayar yalnızca bellek içi bir durum sağlayıcısı kullanır ve çoğaltma sayısını 3 olarak ayarlar.

### <a name="no-persisted-state"></a>Kalıcı durum yok
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Bu ayar yalnızca bellek içi bir durum sağlayıcısı kullanır ve çoğaltma sayısını 1 olarak ayarlar.

### <a name="defaults-and-generated-settings"></a>Varsayılanlar ve oluşturulan ayarlar
`StatePersistence`Özniteliğini kullanırken, aktör hizmeti başladığında çalışma zamanında sizin için bir durum sağlayıcısı otomatik olarak seçilir. Ancak çoğaltma sayısı, Visual Studio aktör derleme araçları tarafından derleme sırasında ayarlanır. Yapı araçları, ApplicationManifest.xml ' deki aktör hizmeti için otomatik olarak *varsayılan bir hizmet* oluşturur. **En az çoğaltma kümesi boyutu** ve **hedef çoğaltma kümesi boyutu** için parametreler oluşturulur.

Bu parametreleri el ile değiştirebilirsiniz. Ancak, öznitelik her `StatePersistence` değiştirildiğinde parametreler seçili öznitelik için varsayılan çoğaltma kümesi boyut değerlerine ayarlanır `StatePersistence` ve önceki değerleri geçersiz kılar. Diğer bir deyişle, ServiceManifest.xml ' de ayarladığınız değerler *yalnızca* öznitelik değerini değiştirdiğinizde derleme zamanında geçersiz kılınır `StatePersistence` .

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Durum Yöneticisi
Her aktör örneğinin kendi durum Yöneticisi vardır: anahtar/değer çiftlerini güvenilir bir şekilde depolayan sözlük benzeri bir veri yapısı. Durum Yöneticisi, bir durum sağlayıcısının etrafındaki bir sarmalayıcıdır. Bu uygulamayı, hangi Kalıcılık ayarının kullanıldığı bağımsız olarak verileri depolamak için kullanabilirsiniz. Çalışan bir aktör hizmetinin geçici (yalnızca bellek içi) durum ayarından, verileri korurken sıralı bir durum ayarı olarak değiştirileceğiyle ilgili hiçbir garanti sağlamaz. Ancak, çalışan bir hizmetin çoğaltma sayısını değiştirmek mümkündür.

Durum Yöneticisi anahtarları dize olmalıdır. Değerler geneldir ve özel türler dahil olmak üzere herhangi bir tür olabilir. Durum Yöneticisi 'nde depolanan değerler, çoğaltma sırasında ağ üzerinden diğer düğümlere iletilebilecek ve bir aktörün durum kalıcılığı ayarına bağlı olarak diske yazılabileceği için veri anlaşması seri hale getirilebilir olmalıdır.

Durum Yöneticisi, güvenilir sözlükte bulunanlara benzer şekilde durum yönetimi için ortak Sözlük yöntemleri sunar.

Aktör durumunu yönetme örnekleri, okuma [erişimi, kaydetme ve Reliable Actors durumu](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>En iyi uygulamalar
Aktör durumunu yönetmeye yönelik bazı önerilen uygulamalar ve sorun giderme ipuçları aşağıda verilmiştir.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Aktör durumunu mümkün olduğunca ayrıntılı yapın
Bu, uygulamanızın performans ve kaynak kullanımı açısından önemlidir. Bir aktörün "adlandırılmış durumunda" herhangi bir yazma/güncelleştirme yapıldığında, "adlandırılmış duruma" karşılık gelen tüm değer serileştirilir ve ağ üzerinden ikincil çoğaltmalara gönderilir.  İkincil öğeler, yerel diske yazılır ve birincil çoğaltmaya geri yanıt verebilir. Birincil, ikincil çoğaltmaların bir çekirdekte bildirimleri aldığında, durumu yerel diskine yazar. Örneğin, değerin 20 üye ve 1 MB boyutundaki bir sınıf olduğunu varsayalım. Yalnızca 1 KB boyutundaki sınıf üyelerinden birini değiştirseniz bile, tam 1 MB için serileştirme ve ağ ve disk yazma maliyetlerine göre ödeme yaparsınız. Benzer şekilde, değer bir koleksiyon (bir liste, dizi veya sözlük gibi) ise, üyelerinden birini değiştirseniz bile, tüm koleksiyon için maliyeti ödersiniz. Aktör sınıfının StateManager arabirimi bir sözlük gibidir. Bu sözlüğün en üstünde aktör durumunu temsil eden veri yapısını her zaman modelleyebilirsiniz.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Aktörün yaşam döngüsünü doğru bir şekilde yönetin
Aktör hizmetinin her bölümünde durum boyutunu yönetme hakkında açık ilkeniz olmalıdır. Aktör hizmetiniz sabit sayıda aktör içermelidir ve bunları mümkün olduğunca yeniden kullanır. Sürekli olarak yeni aktör oluşturuyorsanız, işleri ile işiniz bittiğinde silmeniz gerekir. Aktör çerçevesi, var olan her aktör hakkında bazı meta verileri depolar. Bir aktörün tüm durumunun silinmesi, bu aktör hakkındaki meta verileri kaldırmaz. Sistemde depolanan tüm bilgileri kaldırmak için aktör ( [aktörlerin ve bunların durumunu silme](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)konusuna bakın) silmeniz gerekir. Ek bir denetim olarak, aktör sayısının beklenen aralıkta olduğundan emin olmak için aktör hizmetini (bkz. [aktörleri numaralandırma](service-fabric-reliable-actors-enumerate.md)) bir kez sorgulayın.
 
Bir aktör hizmetinin veritabanı dosyası boyutunun beklenen boyutun ötesinde arttığını görürseniz, yukarıdaki yönergeleri takip ettiğinizden emin olun. Bu yönergeleri takip ediyorsanız ve veritabanı dosya boyutu sorunları yaşamaya devam ediyorsanız, yardım almak için ürün ekibine [bir destek bileti açmanız](service-fabric-support.md) gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Reliable Actors depolanan durum, diske yazılmadan ve yüksek kullanılabilirlik için çoğaltılmadan önce serileştirilmelidir. [Aktör türü serileştirme](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)hakkında daha fazla bilgi edinin.

Ardından, [aktör tanılama ve performans izleme](service-fabric-reliable-actors-diagnostics.md)hakkında daha fazla bilgi edinin.
