---
title: Service Fabric Reliable Actors
description: Reliable Actors Reliable Services nasıl katmanlanmakta olduğunu ve Service Fabric platformunun özelliklerinin nasıl kullanıldığını açıklar.
ms.topic: conceptual
ms.date: 3/9/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: e204b47b7fa9f528341507c315c926159d524e13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789591"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Reliable Actors Service Fabric platformunu kullanma
Bu makalede Reliable Actors Azure Service Fabric platformunda nasıl çalıştığı açıklanmaktadır. Reliable Actors, *aktör hizmeti* olarak adlandırılan durum bilgisi olan güvenilir bir hizmetin uygulamasında barındırılan bir çerçevede çalışır. Aktör hizmeti, Aktörlerinizin yaşam döngüsünü ve ileti dağıtımını yönetmek için gerekli tüm bileşenleri içerir:

* Aktör çalışma zamanı yaşam döngüsünü ve çöp toplamayı yönetir ve tek iş parçacıklı erişim uygular.
* Aktör hizmeti uzaktan iletişim dinleyicisi, aktöre yönelik uzaktan erişim çağrılarını kabul eder ve uygun aktör örneğine yönlendirmek üzere bunları bir dağıtıcıya gönderir.
* Aktör durumu sağlayıcısı durum sağlayıcılarını (güvenilir koleksiyonlar durum sağlayıcısı gibi) sarmalayan ve aktör durumu yönetimi için bir bağdaştırıcı sağlar.

Bu bileşenler birlikte güvenilir aktör çerçevesini oluşturur.

## <a name="service-layering"></a>Hizmet katmanlama
Aktör hizmeti güvenilir bir hizmet olduğundan, Reliable Services tüm [uygulama modeli](service-fabric-application-model.md), yaşam döngüsü, [paketleme](service-fabric-package-apps.md), [dağıtım](service-fabric-deploy-remove-applications.md), yükseltme ve ölçekleme kavramları, aktör Hizmetleri için aynı şekilde geçerlidir.

![Aktör hizmeti katmanlama][1]

Yukarıdaki diyagramda Service Fabric uygulama çerçeveleri ve Kullanıcı kodu arasındaki ilişki gösterilmektedir. Mavi öğeler Reliable Services uygulama çerçevesini temsil eder, turuncu güvenilir aktör çerçevesini temsil eder ve yeşil Kullanıcı kodunu temsil eder.

Reliable Services, hizmetiniz `StatefulService` sınıfını devralır. Bu sınıf, `StatefulServiceBase` (veya `StatelessService` durum bilgisi olmayan hizmetler için) öğesinden türetilir. Reliable Actors, aktör hizmetini kullanırsınız. Aktör hizmeti, `StatefulServiceBase` Aktörlerinizin çalıştırıldığı aktör modelini uygulayan sınıfının farklı bir uygulamasıdır. Aktör hizmeti yalnızca bir uygulaması olduğu için `StatefulServiceBase` , `ActorService` Devralma sırasında aynı şekilde hizmet düzeyi özelliklerden oluşan kendi hizmetinizi yazabilirsiniz `StatefulService` , örneğin:

* Hizmet yedekleme ve geri yükleme.
* Tüm aktörler için paylaşılan işlevsellik; Örneğin, devre kesici.
* Aktör hizmetinin kendisinde ve her bir aktör üzerinde uzak yordam çağrıları.

Daha fazla bilgi için bkz. [aktör hizmetinizde hizmet düzeyi özellikleri uygulama](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Uygulama modeli
Aktör Hizmetleri Reliable Services, bu nedenle uygulama modeli aynı. Ancak aktör Framework derleme araçları sizin için bazı uygulama modeli dosyalarını oluşturur.

### <a name="service-manifest"></a>Hizmet bildirimi
Aktör Framework derleme araçları, aktör hizmetinizin ServiceManifest.xml dosyasının içeriğini otomatik olarak oluşturur. Bu dosya şunları içerir:

* Aktör hizmeti türü. Tür adı, aktörin proje adına göre oluşturulur. Aktör içindeki Kalıcılık özniteliğine bağlı olarak, HasPersistedState bayrağı da buna göre ayarlanır.
* Kod paketi.
* Yapılandırma paketi.
* Kaynaklar ve uç noktalar.

### <a name="application-manifest"></a>Uygulama bildirimi
Aktör altyapısı derleme araçları, aktör hizmetiniz için otomatik olarak varsayılan bir hizmet tanımı oluşturur. Yapı araçları varsayılan hizmet özelliklerini doldurur:

* Çoğaltma kümesi sayısı aktördeki Kalıcılık özniteliğiyle belirlenir. Aktör içindeki Kalıcılık özniteliği her değiştiğinde, varsayılan hizmet tanımındaki çoğaltma kümesi sayısı buna göre sıfırlanır.
* Bölüm şeması ve aralığı, tam Int64 anahtar aralığı ile Tekdüzen Int64 olarak ayarlanır.

## <a name="service-fabric-partition-concepts-for-actors"></a>Aktör için Service Fabric bölümü kavramları
Aktör Hizmetleri bölümlenmiş durum bilgisi olan hizmetlerdir. Aktör hizmetinin her bölümü bir aktör kümesi içerir. Hizmet bölümleri Service Fabric içindeki birden çok düğüme otomatik olarak dağıtılır. Aktör örnekleri sonuç olarak dağıtılır.

![Aktör bölümlendirme ve dağıtım][5]

Reliable Services, farklı bölüm şemaları ve bölüm anahtarı aralıklarıyla oluşturulabilir. Aktör hizmeti, aktörlerin bölümlerle eşleşmesini sağlamak için tam Int64 anahtar aralığıyla Int64 bölümlendirme şemasını kullanır.

### <a name="actor-id"></a>Aktör KIMLIĞI
Hizmette oluşturulan her aktörle ilişkili benzersiz bir KIMLIĞI vardır ve sınıfı tarafından temsil edilir `ActorId` . `ActorId` Rastgele kimlikler oluşturarak hizmet bölümleri genelinde aktörlerin Tekdüzen dağılımı için kullanılabilen donuk bir KIMLIK değeridir:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Her `ActorId` bir bir Int64 için karma hale getirilir. Bu, aktör hizmetinin tam Int64 anahtar aralığıyla bir Int64 bölümlendirme şeması kullanması nedenidir. Ancak, `ActorID` GUID/UUID 'ler, dizeler ve Int64s dahil olmak üzere özel kimlik değerleri için kullanılabilir.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

GUID/UUID 'ler ve dizeler kullanırken, değerler bir Int64 için karma hale getirilir. Ancak, açıkça bir Int64 belirttiğinizde `ActorId` , int64 daha fazla karma olmadan doğrudan bir bölüme eşlenir. Aktörlerin hangi bölüme yerleştirileceğini denetlemek için bu tekniği kullanabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar
* [Aktör durumu yönetimi](service-fabric-reliable-actors-state-management.md)
* [Aktör yaşam döngüsü ve çöp toplama](service-fabric-reliable-actors-lifecycle.md)
* [Aktör API 'SI başvuru belgeleri](/dotnet/api/microsoft.servicefabric.actors)
* [.NET örnek kodu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java örnek kodu](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
