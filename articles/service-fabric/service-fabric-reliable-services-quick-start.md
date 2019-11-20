---
title: İlk Service Fabric uygulamanızı | içinde C# oluşturun Microsoft Docs
description: Durum bilgisiz ve durum bilgisi olan hizmetlerle Microsoft Azure Service Fabric uygulama oluşturmaya giriş.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: d9b44d75-e905-468e-b867-2190ce97379a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: vturecek
ms.openlocfilehash: f3b3d5c3dcea7d190724ae946a27c47b34a26c31
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68225020"
---
# <a name="get-started-with-reliable-services"></a>Reliable Services özelliğini kullanmaya başlayın
> [!div class="op_single_selector"]
> * [Windows üzerinde C#](service-fabric-reliable-services-quick-start.md)
> * [Linux üzerinde Java](service-fabric-reliable-services-quick-start-java.md)
> 
> 

Azure Service Fabric uygulaması, kodunuzu çalıştıran bir veya daha fazla hizmet içerir. Bu kılavuzda, [Reliable Services](service-fabric-reliable-services-introduction.md)ile hem durum bilgisi olmayan hem de durum bilgisi olan Service Fabric uygulamalarının nasıl oluşturulacağı gösterilmektedir.  

## <a name="basic-concepts"></a>Temel kavramlar
Reliable Services kullanmaya başlamak için yalnızca birkaç temel kavramları anlamanız gerekir:

* **Hizmet türü**: Bu hizmet uygulamanız. Bu, yazdığınız sınıf tarafından, bir ad ve sürüm `StatelessService` numarası ile birlikte kullanılan diğer kod veya bağımlılıkları genişletir.
* **Adlandırılmış hizmet örneği**: Hizmetinizi çalıştırmak için, bir sınıf türünün nesne örneklerini oluştururken olduğu gibi, hizmet türünden adlandırılmış örnekler oluşturursunuz. Hizmet örneği, "Fabric:/" kullanılarak URI biçiminde bir ada sahiptir Düzen, örneğin "Fabric:/MyApp/hizmetim".
* **Hizmet ana bilgisayarı**: Oluşturduğunuz adlandırılmış hizmet örneklerinin bir konak işlemi içinde çalışması gerekir. Hizmet ana bilgisayarı yalnızca hizmetinizin örneklerinin çalıştırılabileceği bir işlemdir.
* **Hizmet kaydı**: Kayıt her şeyi bir araya getirir. Service Fabric çalıştırmak için hizmet türü, bir hizmet ana bilgisayarındaki Service Fabric çalışma zamanına kaydedilmelidir.  

## <a name="create-a-stateless-service"></a>Durum bilgisi olmayan hizmet oluşturma
Durum bilgisi olmayan bir hizmet, şu anda bulut uygulamalarında norm olan bir hizmet türüdür. Hizmetin kendisi, güvenilir bir şekilde depolanması veya yüksek oranda kullanılabilir olması gereken veriler içermediğinden durum bilgisiz olarak değerlendirilir. Durum bilgisi olmayan bir hizmetin bir örneği kapatılırsa, tüm iç durumları kaybedilir. Bu tür bir hizmette durum, yüksek oranda kullanılabilir ve güvenilir hale getirilme için Azure tabloları veya bir SQL veritabanı gibi bir dış depoya kalıcı olmalıdır.

Visual Studio 2017 veya Visual Studio 2019 ' yi yönetici olarak başlatın ve *HelloWorld*adlı yeni bir Service Fabric uygulama projesi oluşturun:

![Yeni bir Service Fabric uygulaması oluşturmak için yeni proje iletişim kutusunu kullanın](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Daha sonra, *Merhaba worlddurumsuz*adlı **.NET Core 2,0** kullanarak durum bilgisi olmayan bir hizmet projesi oluşturun:

![İkinci iletişim kutusunda, durum bilgisi olmayan bir hizmet projesi oluşturun](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Çözümünüz artık iki proje içerir:

* *HelloWorld*. Bu, *hizmetlerinizi*içeren *uygulama* projem. Ayrıca, uygulamayı tanımlayan uygulama bildirimini ve uygulamanızı dağıtmanıza yardımcı olan birçok PowerShell komut dosyasını da içerir.
* *Merhaba Dünya durum bilgisiz*. Bu, hizmet projem. Durum bilgisi olmayan hizmet uygulamasını içerir.

## <a name="implement-the-service"></a>Hizmeti uygulama
**HelloWorldStateless.cs** dosyasını hizmet projesinde açın. Service Fabric, bir hizmet herhangi bir iş mantığını çalıştırabilir. Service API 'SI, kodunuz için iki giriş noktası sağlar:

* Uzun süre çalışan işlem iş yükleri dahil olmak üzere herhangi bir iş yükünü yürütmeye başlayabileceğiniz *RunAsync*adlı bir açık uçlu giriş noktası yöntemi.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* ASP.NET Core gibi istediğiniz iletişim yığınınızı ekleyebileceğiniz iletişim giriş noktası. Bu, kullanıcılardan ve diğer hizmetlerden istek almaya başlayabileceğiniz yerdir.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

Bu öğreticide, `RunAsync()` giriş noktası yöntemine odaklanacağız. Bu, kodunuzun hemen çalıştırılmasına başlayabileceğiniz yerdir.
Proje şablonu, bir toplama sayısını artıran örnek `RunAsync()` bir uygulamasını içerir.

> [!NOTE]
> İletişim yığınıyla çalışma hakkında ayrıntılı bilgi için bkz. [OWIN Self-hosting Ile Web API hizmetlerini Service Fabric](service-fabric-reliable-services-communication-webapi.md)
> 
> 

### <a name="runasync"></a>RunAsync
```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    long iterations = 0;

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

Bir hizmetin bir örneği yerleştirildiğinde ve yürütülmeye hazırlanıyorsa, platform bu yöntemi çağırır. Durum bilgisi olmayan bir hizmet için hizmet örneği açıldığı zaman anlamına gelir. Hizmet örneğinizin kapatılması gerektiğinde koordine etmek için bir iptal belirteci sağlanır. Service Fabric, bir hizmet örneğinin bu açık/kapalı döngüsü, hizmetin kullanım ömrü boyunca bir bütün olarak çok fazla zaman alabilir. Bu, aşağıdakiler dahil çeşitli nedenlerden kaynaklanabilir:

* Sistem, hizmet örneklerinizi kaynak Dengeleme için taşıdıkça.
* Hatalar kodunuzda oluşur.
* Uygulama veya sistem yükseltilir.
* Temel alınan donanım bir kesinti yaşıyorsa.

Bu düzenleme sistem tarafından, hizmetinizi yüksek oranda kullanılabilir ve düzgün şekilde dengeli tutmak için yönetilir.

`RunAsync()`zaman uyumlu olarak engellenmemelidir. RunAsync uygulamanız, çalışma zamanının devam etmesine izin vermek için uzun süre çalışan veya engelleme işlemlerinde bir görev veya await döndürmelidir. Bir önceki `await Task.Delay()` örnekteki döngüdebirgörevdöndürmesinin`while(true)` kullanıldığını aklınızda edin. İş yükünüz zaman uyumlu olarak engellenirse, `Task.Run()` `RunAsync` uygulamanızda ile yeni bir görev zamanlamanız gerekir.

İş yükünüzün iptali, belirtilen iptal belirteci tarafından düzenlenen bir işbirliği çabadır. Sistem, başlamadan önce görevin bitmesini bekler (başarılı tamamlama, iptal veya hata ile). İptal belirtecini dikkate almak, tüm işleri tamamlamak ve sistem iptali istediğinde mümkün olduğunca hızlı `RunAsync()` bir şekilde çıkmak önemlidir.

Bu durum bilgisi olmayan hizmet örneğinde, sayı yerel bir değişkende depolanır. Ancak bu durum bilgisiz olmayan bir hizmet olduğundan, depolanan değer yalnızca hizmet örneğinin geçerli yaşam döngüsü için mevcut olur. Hizmet taşınsa veya yeniden başlatıldığında, değer kaybedilir.

## <a name="create-a-stateful-service"></a>Durum bilgisi olan hizmet oluşturma
Service Fabric, durum bilgisi olan yeni bir hizmet türü sunar. Durum bilgisi olan bir hizmet, hizmetin kendisi içinde güvenilir bir şekilde durumunu koruyabilir ve onu kullanan kodla birlikte bulunur. Durum, bir dış depoya durumunun kalıcı hale getirilmesi gerekmeden Service Fabric tarafından yüksek oranda kullanılabilir hale getirilir.

Bir sayaç değerini durum bilgisiz durumundan yüksek oranda kullanılabilir ve kalıcı olarak dönüştürmek için, hizmet taşınsa veya yeniden başlatıldığında bile, durum bilgisi olan bir hizmete ihtiyacınız vardır.

Aynı *HelloWorld* uygulamasında, uygulama projesindeki hizmetler başvurularına sağ tıklayıp **> yeni Service Fabric hizmeti**' ni seçerek yeni bir hizmet ekleyebilirsiniz.

![Service Fabric uygulamanıza hizmet ekleme](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

**.NET Core 2,0-> durum bilgisi olan hizmeti** seçin ve *Merhaba Dünya*dışı olduğunu adlandırın. **Tamam** düğmesine tıklayın.

![Yeni proje iletişim kutusunu kullanarak yeni bir Service Fabric durum bilgisi olan hizmet oluşturun](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Uygulamanızın Şu anda iki hizmeti olmalıdır: durum bilgisi olmayan hizmet *Merhaba Dünya durum* bilgisi ve durum bilgisi olan hizmet *Merhaba Dünya durum*bilgisi.

Durum bilgisi olan bir hizmet, durum bilgisi olmayan bir hizmetle aynı giriş noktalarına sahiptir. Temel fark, durumu güvenilir bir şekilde depolayabilen bir *durum sağlayıcısının* kullanılabilirliğinden oluşur. Service Fabric, güvenilir [koleksiyonlar](service-fabric-reliable-services-reliable-collections.md)adlı bir durum sağlayıcısı uygulamasıyla birlikte gelir, bu da güvenilir durum Yöneticisi aracılığıyla çoğaltılan veri yapıları oluşturmanızı sağlar. Durum bilgisi olan güvenilir bir hizmet, varsayılan olarak bu durum sağlayıcısını kullanır.

Aşağıdaki RunAsync metodunu içeren *Merhaba Dünya durum bilgisi*içinde **HelloWorldStateful.cs** açın:

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");

            ServiceEventSource.Current.ServiceMessage(this.Context, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter", 0, (key, value) => ++value);

            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is saved to the secondary replicas.
            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
```

### <a name="runasync"></a>RunAsync
`RunAsync()`durum bilgisiz ve durum bilgisi içermeyen hizmetlerde benzer şekilde çalışır. Ancak, durum bilgisi olan bir hizmette, platform yürütmeden `RunAsync()`önce sizin adınıza ek iş gerçekleştirir. Bu iş, güvenilir durum Yöneticisi ve güvenilir koleksiyonların kullanıma hazırlandığından emin olabilir.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Güvenilir koleksiyonlar ve güvenilir durum Yöneticisi
```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[Ireliabledictionary](https://msdn.microsoft.com/library/dn971511.aspx) , durumu hizmette güvenilir bir şekilde depolamak için kullanabileceğiniz bir sözlük uygulamasıdır. Service Fabric ve güvenilir koleksiyonlar sayesinde, bir dış kalıcı mağazaya gerek duymadan verileri doğrudan hizmetinize kaydedebilirsiniz. Güvenilir koleksiyonlar, verilerinizi yüksek oranda kullanılabilir hale getirir. Service Fabric, hizmetinizin birden fazla *çoğaltmasını* oluşturup yöneterek bunu gerçekleştirir. Ayrıca, bu çoğaltmaları ve bunların durum geçişlerini yönetmenin karmaşıklıklarını soyutlayan bir API sağlar.

Güvenilir koleksiyonlar, özel türleriniz dahil olmak üzere her türlü .NET türünü, birkaç uyarılarla saklayabilir:

* Service Fabric, durumları düğümler arasında çoğaltarak  ve güvenilir koleksiyonlar verilerinizi her çoğaltmada yerel diske depolar. Bu, güvenilir koleksiyonlar içinde depolanan her şeyin *seri hale getirilebilir*olması gerektiği anlamına gelir. Varsayılan olarak, güvenilir koleksiyonlar serileştirme için [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) kullanır, bu nedenle varsayılan serileştirici kullandığınızda türlerinizi [veri sözleşmesi serileştiricisi tarafından desteklendiğinden](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) emin olmak önemlidir.
* İşlemler, güvenilir koleksiyonlar üzerinde işlem gerçekleştirdiğinizde yüksek kullanılabilirlik için çoğaltılır. Güvenilir koleksiyonlar ' da depolanan nesneler, hizmetinizdeki yerel bellekte tutulur. Bu, nesnesine yerel bir başvurunuz olduğu anlamına gelir.
  
   Bir işlemdeki güvenilir koleksiyonda bir güncelleştirme işlemi gerçekleştirmeden, bu nesnelerin yerel örneklerini muanmamak önemlidir. Bunun nedeni, yerel nesne örneklerine yapılan değişikliklerin otomatik olarak çoğaltılmaması olabilir. Nesneyi sözlüğe yeniden eklemeniz veya sözlükteki *güncelleştirme* yöntemlerinden birini kullanmanız gerekir.

Güvenilir durum Yöneticisi sizin için güvenilir koleksiyonları yönetir. Güvenilir durum yöneticisinden, her zaman ve hizmetinize dilediğiniz yerde güvenilir bir koleksiyon adını sorabilirsiniz. Güvenilir durum Yöneticisi bir başvuru geri almanızı sağlar. Sınıf üyesi değişkenlerinde veya özelliklerde güvenilir koleksiyon örneklerine başvuruları kaydetmenizi öneririz. Başvurunun hizmet yaşam döngüsünün her zaman bir örneğe ayarlandığından emin olmak için özel dikkatli olunmalıdır. Güvenilir durum Yöneticisi bu çalışmayı sizin için işler ve yineleme ziyaretleri için en iyi duruma getirilmiştir.

### <a name="transactional-and-asynchronous-operations"></a>İşlem ve zaman uyumsuz işlemler
```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Güvenilir koleksiyonlar, LINQ dışında, `System.Collections.Generic` ve `System.Collections.Concurrent` karşılıkları tarafından gerçekleştirilen işlemlerin çoğuna sahiptir. Güvenilir koleksiyonlardaki işlemler zaman uyumsuzdur. Bunun nedeni, güvenilir Koleksiyonlar içeren yazma işlemlerinin verileri diske çoğaltmak ve diskte kalıcı hale getirmek için g/ç işlemleri gerçekleştirmesini sağlar.

Güvenilir koleksiyon işlemleri *işlem yapılabilir*ve bu sayede durum, birden çok güvenilir koleksiyonlar ve işlemler arasında tutarlı tutulabilmenizi sağlayabilir. Örneğin, bir iş öğesini güvenilir bir kuyruktan sıradan sıradan alabilir, üzerinde bir işlem gerçekleştirebilir ve sonucu tek bir işlem içinde güvenilir bir sözlükte kaydedebilirsiniz. Bu bir atomik işlem olarak değerlendirilir ve tüm işlemin başarılı olacağını veya işlemin tamamının geri alınmayacağını garanti eder. Öğeyi sıradan çıktıktan sonra bir hata oluşursa, ancak sonucu kaydetmeden önce, tüm işlem geri alınır ve öğe işlenmek üzere kuyrukta kalır.

## <a name="run-the-application"></a>Uygulamayı çalıştırma
Şimdi *HelloWorld* uygulamasına geri dönebiliyoruz. Artık hizmetlerinizi oluşturabilir ve dağıtabilirsiniz. **F5**tuşuna bastığınızda uygulamanız oluşturulup yerel kümenize dağıtılır.

Hizmetler çalışmaya başladıktan sonra, Windows için oluşturulan olay Izleme (ETW) olaylarını bir **Tanılama olayları** penceresinde görüntüleyebilirsiniz. Görüntülenen olayların hem durum bilgisi olmayan hizmetten hem de uygulamadaki durum bilgisi olan hizmetten olduğunu unutmayın. **Duraklat** düğmesine tıklayarak akışı duraklatabilirsiniz. Daha sonra bu iletiyi genişleterek bir iletinin ayrıntılarını inceleyebilirsiniz.

> [!NOTE]
> Uygulamayı çalıştırmadan önce, çalışan bir yerel geliştirme kümeniz olduğundan emin olun. Yerel ortamınızı ayarlama hakkında bilgi edinmek için Başlarken [kılavuzuna](service-fabric-get-started.md) göz atın.
> 
> 

![Visual Studio 'da Tanılama olaylarını görüntüleme](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Sonraki adımlar
[Visual Studio 'da Service Fabric uygulamanızda hata ayıklama](service-fabric-debugging-your-application.md)

[Kullanmaya başlayın: OWıN Self-hosting ile Web API hizmetlerini Service Fabric](service-fabric-reliable-services-communication-webapi.md)

[Güvenilir Koleksiyonlar hakkında daha fazla bilgi edinin](service-fabric-reliable-services-reliable-collections.md)

[Uygulama dağıtma](service-fabric-deploy-remove-applications.md)

[Uygulama yükseltme](service-fabric-application-upgrade.md)

[Reliable Services için geliştirici başvurusu](https://msdn.microsoft.com/library/azure/dn706529.aspx)

