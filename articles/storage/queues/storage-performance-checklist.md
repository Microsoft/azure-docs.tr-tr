---
title: Kuyruk depolama-Azure depolama için performans ve ölçeklenebilirlik denetim listesi
description: Yüksek performanslı uygulamalar geliştirirken kuyruk depolamada kullanılmak üzere kanıtlanmış uygulamaların denetim listesi.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: 8ab4cb6b06f0f023a8f6368dac633a97afe29fd4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390019"
---
# <a name="performance-and-scalability-checklist-for-queue-storage"></a>Kuyruk depolama için performans ve ölçeklenebilirlik denetim listesi

Microsoft, kuyruk depolama ile yüksek performanslı uygulamalar geliştirmeye yönelik bir dizi kanıtlanmış uygulama geliştirmiştir. Bu denetim listesi, geliştiricilerin performansı iyileştirmek için izleyebildiği önemli uygulamaları tanımlar. Uygulamanızı tasarlarken ve işlem boyunca bu uygulamaları göz önünde bulundurun.

Azure depolama kapasitesi, işlem hızı ve bant genişliği için ölçeklenebilirlik ve performans hedefleri içerir. Azure depolama ölçeklenebilirlik hedefleri hakkında daha fazla bilgi için bkz. [depolama hesapları Için Azure Storage ölçeklenebilirlik ve performans hedefleri](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

## <a name="checklist"></a>Denetim Listesi

Bu makale, sıra depolama uygulamanızı geliştirirken izleyebileceğiniz bir denetim listesi halinde performans için kanıtlanmış uygulamaları düzenler.

| Bitti | Kategori | Tasarım değerlendirmesi |
| --- | --- | --- |
| &nbsp; |Ölçeklenebilirlik hedefleri |[Uygulamanızı en fazla depolama hesabı sayısından daha fazla kullanmak üzere tasarlayabilmeniz gerekebilir mi?](#maximum-number-of-storage-accounts) |
| &nbsp; |Ölçeklenebilirlik hedefleri |[Kapasite ve işlem sınırlarına yaklaşmaktan kaçınıyorsunuz musunuz?](#capacity-and-transaction-targets) |
| &nbsp; |Networking (Ağ İletişimi) |[İstemci tarafı cihazlarda gereken performansa ulaşmak için yeterli yüksek bant genişliği ve düşük gecikme süresi var mı?](#throughput) |
| &nbsp; |Networking (Ağ İletişimi) |[İstemci tarafı cihazların yüksek kaliteli bir ağ bağlantısı var mı?](#link-quality) |
| &nbsp; |Networking (Ağ İletişimi) |[İstemci uygulaması, depolama hesabıyla aynı bölgede mi?](#location) |
| &nbsp; |Doğrudan Istemci erişimi |[Azure Storage 'a doğrudan erişim sağlamak için paylaşılan erişim imzaları (SAS) ve çıkış noktaları arası kaynak paylaşımı (CORS) kullanıyor musunuz?](#sas-and-cors) |
| &nbsp; |.NET yapılandırması |[En iyi performans için .NET Core 2,1 veya üstünü mi kullanıyorsunuz?](#use-net-core) |
| &nbsp; |.NET yapılandırması |[İstemcinizi yeterli sayıda eşzamanlı bağlantı kullanacak şekilde yapılandırdınız mı?](#increase-default-connection-limit) |
| &nbsp; |.NET yapılandırması |[.NET uygulamaları için, .NET 'i yeterli sayıda iş parçacığı kullanacak şekilde yapılandırdınız mı?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralellik |[Paralellik 'in, istemci yeteneklerini aşırı yüklemeden veya ölçeklenebilirlik hedeflerine yaklaşımak için uygun şekilde bağlanmış olduğunu merak etmeniz gerekir mi?](#unbounded-parallelism) |
| &nbsp; |Araçlar |[Microsoft tarafından sağlanmış istemci kitaplıklarının ve araçlarının en son sürümlerini kullanıyor musunuz?](#client-libraries-and-tools) |
| &nbsp; |Yeniden deneme sayısı |[Daraltma hataları ve zaman aşımları için üstel geri alma ile yeniden deneme İlkesi kullanıyor musunuz?](#timeout-and-server-busy-errors) |
| &nbsp; |Yeniden deneme sayısı |[Uygulamanız yeniden denenmeyen hatalara karşı yeniden denemeyi önler mi?](#non-retryable-errors) |
| &nbsp; |Yapılandırma |[Küçük isteklerin performansını artırmak için Nagle algoritmasını kapattık mı?](#disable-nagle) |
| &nbsp; |İleti boyutu |[İletiniz, sıranın performansını geliştirmek için sıkıştırmı?](#message-size) |
| &nbsp; |Toplu alma |[Tek bir GET işleminde birden çok ileti alıyor musunuz?](#batch-retrieval) |
| &nbsp; |Yoklama sıklığı |[Uygulamanızın algılanan gecikmesini azaltmak için yeterince sık yoklanıyor musunuz?](#queue-polling-interval) |
| &nbsp; |Güncelleştirme Iletisi |[Bir hata oluşursa iletinin tamamını yeniden işlemek zorunda kalmamak için ileti işleme sürecini depolamak üzere iletileri güncelleştirme işlemini kullanıyorsunuz musunuz?](#use-update-message) |
| &nbsp; |Mimari |[Uzun süre çalışan iş yüklerini kritik yoldan tutarak ve sonra bağımsız olarak ölçeklendirerek, tüm uygulamanızı daha ölçeklenebilir hale getirmek için kuyrukları kullanıyor musunuz?](#application-architecture) |

## <a name="scalability-targets"></a>Ölçeklenebilirlik hedefleri

Uygulamanız ölçeklenebilirlik hedeflerinin herhangi birini yaklaşırsa veya aşarsa, daha fazla işlem gecikmeleri veya azaltmasıyla karşılaşabilirler. Azure Storage uygulamanızı kısıtsalken, hizmet 503 (sunucu meşgul) veya 500 (Işlem zaman aşımı) hata kodları döndürmeye başlar. Ölçeklenebilirlik hedefleri sınırları içinde kalarak bu hatalardan kaçınmak, uygulamanızın performansını artırmanın önemli bir parçasıdır.

Kuyruk hizmeti ölçeklenebilirlik hedefleri hakkında daha fazla bilgi için bkz. [Azure Storage ölçeklenebilirlik ve performans hedefleri](/azure/storage/common/storage-scalability-targets?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#azure-queue-storage-scale-targets).

### <a name="maximum-number-of-storage-accounts"></a>En fazla depolama hesabı sayısı

Belirli bir abonelik/bölge birleşimi için izin verilen en fazla depolama hesabı sayısına yaklaşdıysanız, parça, çıkış, saniye başına g/ç işlemi (ıOPS) veya kapasiteyi artırmak için birden fazla depolama hesabı kullanıyor musunuz? Bu senaryoda, Microsoft, mümkünse iş yükünüz için gereken depolama hesabı sayısını azaltmak üzere depolama hesapları için artan limitlerin avantajlarından yararlanmanızı öneriyor. Depolama Hesabınıza yönelik daha fazla limit istemek için [Azure desteğine](https://azure.microsoft.com/support/options/) başvurun. Daha fazla bilgi için bkz. daha [büyük, daha yüksek ölçekli depolama hesapları duyurusu](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Kapasite ve işlem hedefleri

Uygulamanız tek bir depolama hesabı için ölçeklenebilirlik hedeflerine yaklaşıyorsa, aşağıdaki yaklaşımlardan birini benimsede düşünün:  

- Kuyruklar için ölçeklenebilirlik hedefleri uygulamanız için yeterli değilse, birden çok kuyruk kullanın ve iletileri bunlar arasında dağıtın.
- Uygulamanızın ölçeklenebilirlik hedefini yaklaşımını veya aşmasına neden olan iş yükünü yeniden değerlendirin. Daha az bant genişliği veya kapasite veya daha az işlem kullanmak için farklı bir şekilde tasarlayabilirsiniz mi?
- Uygulamanız ölçeklenebilirlik hedeflerinin birini aşmanız gerekiyorsa, birden çok depolama hesabı oluşturun ve uygulama verilerinizi bu birden çok depolama hesabı genelinde bölümleyin. Bu kalıbı kullanırsanız, daha sonra yük dengelemeye yönelik daha fazla depolama hesabı ekleyebilmeniz için uygulamanızı tasarlayadığınızdan emin olun. Depolama hesaplarının kendileri, depolanan veri, işlem yapılan veya aktarılan veri açısından kullanımınız dışında bir ücret içermez.
- Uygulamanız bant genişliği hedeflerine yaklaşıyorsa, verileri Azure depolama 'ya göndermek için gereken bant genişliğini azaltmak için istemci tarafındaki verileri sıkıştırmayı göz önünde bulundurun.
    Verilerin sıkıştırılması bant genişliğini kaydedebilir ve ağ performansını iyileştireken performansı olumsuz etkileyebilir. İstemci tarafında veri sıkıştırma ve açma için ek işleme gereksinimlerinin performans etkisini değerlendirin. Sıkıştırılmış verilerin depolanması sorun gidermeyi daha zor hale getirir, çünkü verileri standart araçlar kullanarak görüntülemek daha zor olabilir.
- Uygulamanız ölçeklenebilirlik hedeflerine yaklaşıyorsa, yeniden denemeler için bir üstel geri alma kullandığınızdan emin olun. Bu makalede açıklanan önerileri uygulayarak ölçeklenebilirlik hedeflerine ulaşmaktan kaçınmak en iyisidir. Ancak, yeniden denemeler için bir üstel geri alma kullanılması, uygulamanızın hızlı bir şekilde yeniden denenmesini engelleyecek ve bu da azaltmayı daha kötüleşmektedir. Daha fazla bilgi için [zaman aşımı ve sunucu meşgul hataları](#timeout-and-server-busy-errors)başlıklı bölüme bakın.

## <a name="networking"></a>Networking (Ağ İletişimi)

Uygulamanın fiziksel ağ kısıtlamalarının performans üzerinde önemli bir etkisi olabilir. Aşağıdaki bölümlerde, kullanıcıların karşılaşabileceği bazı sınırlamalar açıklanır.  

### <a name="client-network-capability"></a>İstemci ağ özelliği

Bant genişliği ve ağ bağlantısının kalitesi, aşağıdaki bölümlerde açıklandığı gibi uygulama performansı açısından önemli rolleri oynar.

#### <a name="throughput"></a>İşleme

Bant genişliği için genellikle bu sorun istemcinin yeteneklerine yöneliktir. Daha büyük Azure örneklerinin NIC 'Leri daha fazla kapasiteye sahip olduğundan, tek bir makineden daha yüksek ağ sınırlarına ihtiyaç duyuyorsanız daha büyük bir örnek veya daha fazla VM kullanmayı düşünmelisiniz. Azure depolama 'yı şirket içi bir uygulamadan erişiyorsanız, aynı kural geçerlidir: istemci cihazının ağ yeteneklerini ve Azure depolama konumuna ağ bağlantısını anlayın ve bunları gerektiği gibi geliştirebilirsiniz veya uygulamanın özellikleri içinde çalışması.

#### <a name="link-quality"></a>Bağlantı kalitesi

Her türlü ağ kullanımında olduğu gibi, ağ koşullarının hatalara ve paket kaybına neden olacağını aklınızda bulundurun.  WireShark veya NetMon kullanmak bu sorunu tanılamanıza yardımcı olabilir.  

### <a name="location"></a>Konum

Dağıtılmış bir ortamda, istemciyi sunucuya eklemek en iyi performansı sağlar. En düşük gecikme süresi ile Azure depolama erişimi için, istemciniz için en iyi konum aynı Azure bölgesi içindedir. Örneğin, Azure Storage kullanan bir Azure Web uygulamanız varsa, bunları her ikisi de ABD Batı veya Asya Güneydoğu gibi tek bir bölge içinde bulun. Ortak bulma kaynakları, tek bir bölgedeki bant genişliği kullanımı ücretsizdir gecikme süresini ve maliyeti azaltır.  

İstemci uygulamaları Azure depolama 'ya erişebilse ancak mobil cihaz uygulamaları veya şirket içi kurumsal hizmetler gibi Azure 'da barındırılandıklarında, bu istemcilere yakın bir bölgede depolama hesabını bulma gecikme süresini azaltabilir. İstemcileriniz büyük ölçüde dağıtılırsa (örneğin, Kuzey Amerika ve bazıları Avrupa 'da), her bölge için bir depolama hesabı kullanmayı düşünün. Uygulamanın depoladığı veriler bireysel kullanıcılara özgü ise ve depolama hesapları arasında veri çoğaltmayı gerektirmiyorsa, bu yaklaşım daha kolay bir şekilde uygulanır.

## <a name="sas-and-cors"></a>SAS ve CORS

Kullanıcının Web tarayıcısında veya mobil telefon uygulamasında çalışan JavaScript gibi bir kodu, Azure depolama 'daki verilere erişmek için yetkilendirmeniz gerektiğini varsayalım. Bir yaklaşım, proxy görevi gören bir hizmet uygulaması oluşturmaktır. Kullanıcının cihazının kimliği, hizmet ile doğrulanır, bu da Azure depolama kaynaklarına erişim yetkisi verir. Bu şekilde, depolama hesabı anahtarlarınızı güvenli olmayan cihazlarda açığa çıkarmaktan kaçınabilirsiniz. Ancak, bu yaklaşım, kullanıcının cihazı ile Azure depolama arasında aktarılan tüm verilerin hizmet uygulamasından geçmesi gerektiğinden, hizmet uygulamasına önemli bir ek yük koyar.

Paylaşılan erişim imzalarını (SAS) kullanarak Azure depolama için bir hizmet uygulaması proxy 'si kullanmaktan kaçınabilirsiniz. SAS kullanarak, sınırlı erişim belirtecini kullanarak, kullanıcının cihazını doğrudan Azure depolama 'ya istek yapmasını sağlayabilirsiniz. Örneğin, bir Kullanıcı uygulamanıza fotoğraf yüklemek isterse, hizmet uygulamanız SAS oluşturup kullanıcının cihazına gönderebilir. SAS belirteci, belirli bir zaman aralığı için bir Azure depolama kaynağına yazma izni verebilir ve sonrasında SAS belirtecinin süresi dolar. SAS hakkında daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](../common/storage-sas-overview.md).  

Genellikle, bir Web tarayıcısı, başka bir etki alanına yazma işlemleri gibi belirli işlemleri gerçekleştirmek için bir etki alanında Web sitesi tarafından barındırılan bir sayfada JavaScript 'e izin vermez. Aynı-kaynak ilkesi olarak bilinen bu ilke, bir sayfadaki kötü amaçlı kodun başka bir Web sayfasındaki verilere erişim sağlamasını önler. Ancak, bulutta bir çözüm oluşturulurken aynı kaynak ilkesi bir kısıtlama olabilir. Çıkış noktaları arası kaynak paylaşımı (CORS), hedef etki alanının kaynak etki alanında yer alan isteklere güvendiği tarayıcıyla iletişim kurmasını sağlayan bir tarayıcı özelliğidir.

Örneğin, Azure 'da çalışan bir Web uygulamasının bir kaynak için bir Azure depolama hesabına yönelik bir istek oluşturduğunu varsayalım. Web uygulaması, kaynak etki alanıdır ve depolama hesabı hedef etki alanıdır. Azure depolama hizmetlerinden herhangi biri için CORS 'yi, kaynak etki alanından gelen istekleri Azure depolama tarafından güvenilen Web tarayıcısıyla iletişim kuracak şekilde yapılandırabilirsiniz. CORS hakkında daha fazla bilgi için bkz. [Azure depolama için çıkış noktaları arası kaynak paylaşımı (CORS) desteği](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Hem SAS hem de CORS, Web uygulamanızda gereksiz yükün oluşmasını önlemenize yardımcı olabilir.  

## <a name="net-configuration"></a>.NET yapılandırması

.NET Framework kullanılıyorsa, bu bölümde önemli performans iyileştirmeleri yapmak için kullanabileceğiniz çeşitli hızlı yapılandırma ayarları listelenir.  Diğer dilleri kullanıyorsanız, seçtiğiniz dilde benzer kavramların uygulayıp uygulamamın olup olmadığını kontrol edin.  

### <a name="use-net-core"></a>.NET Core kullanın

Performans geliştirmelerinden faydalanmak için .NET Core 2,1 veya sonraki bir sürümü ile Azure depolama uygulamalarınızı geliştirin. Mümkünse .NET Core 3. x kullanılması önerilir.

.NET Core 'da performans iyileştirmeleri hakkında daha fazla bilgi için aşağıdaki blog gönderilerine bakın:

- [.NET Core 3,0 ' de performans Iyileştirmeleri](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [.NET Core 2,1 ' de performans Iyileştirmeleri](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Varsayılan bağlantı sınırını artır

.NET ' te aşağıdaki kod, varsayılan bağlantı sınırını (genellikle bir istemci ortamında 2 veya bir sunucu ortamında 10 ' a) 100 olarak artırır. Genellikle, değerini uygulamanız tarafından kullanılan iş parçacığı sayısı için yaklaşık olarak ayarlamanız gerekir.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Herhangi bir bağlantıyı açmadan önce bağlantı sınırını ayarlayın.  

Diğer programlama dilleri için, bağlantı sınırının nasıl ayarlanacağını öğrenmek için bu dilin belgelerine bakın.  

Daha fazla bilgi için [Web Hizmetleri: eşzamanlı bağlantılar](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/)' a bakın.  

### <a name="increase-minimum-number-of-threads"></a>En az iş parçacığı sayısını artır

Zaman uyumsuz görevlerle birlikte zaman uyumlu çağrılar kullanıyorsanız, iş parçacığı havuzundaki iş parçacığı sayısını artırmak isteyebilirsiniz:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Daha fazla bilgi için bkz [. ThreadPool. SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads) metodu.  

## <a name="unbounded-parallelism"></a>Sınırlandırılmamış paralellik

Paralellik performansı performans için harika olsa da, sınırsız paralellik kullanma konusunda dikkatli olun, yani iş parçacığı sayısı veya paralel istekler üzerinde hiçbir sınır uygulanmaz. Verileri karşıya yükleme veya indirme, aynı depolama hesabındaki birden çok bölüme erişmek veya aynı bölümdeki birden çok öğeye erişmek için paralel istekleri sınırlandırdığınızdan emin olun. Paralellik sınırsız ise, uygulamanız istemci cihazının yeteneklerini veya depolama hesabının ölçeklenebilirlik hedeflerini aşacağından, daha uzun gecikme süreleri ve azaltma olanakları elde edebilir.  

## <a name="client-libraries-and-tools"></a>İstemci kitaplıkları ve araçları

En iyi performans için, her zaman Microsoft tarafından sunulan en son istemci kitaplıklarını ve araçları kullanın. Azure depolama istemci kitaplıkları, çeşitli diller için kullanılabilir. Azure depolama, PowerShell ve Azure CLı 'yı da destekler. Microsoft bu istemci kitaplıklarını ve araçları göz önünde bulundurularak etkin bir şekilde geliştirir, en son hizmet sürümleriyle güncel tutar ve kendini kanıtlamış performans uygulamalarının çoğunu dahili olarak işlemesini sağlar. Daha fazla bilgi için bkz. [Azure depolama başvurusu belgeleri](/azure/storage/#reference).

## <a name="handle-service-errors"></a>Hizmet hatalarını işleme

Hizmet bir isteği işleye, Azure Storage bir hata döndürüyor. Belirli bir senaryoda Azure depolama tarafından döndürülebilecek hataları anlamak performansı iyileştirmek için yararlıdır.

### <a name="timeout-and-server-busy-errors"></a>Zaman aşımı ve sunucu meşgul hataları

Ölçeklenebilirlik sınırlarına yaklaşırsa, Azure Storage uygulamanızı kısıtlayabilir. Bazı durumlarda, Azure Storage bazı geçici bir durum nedeniyle isteği işleyemeyebilir. Her iki durumda da hizmet 503 (sunucu meşgul) veya 500 (zaman aşımı) hatası döndürebilir. Bu hatalar, hizmet daha yüksek aktarım hızına izin vermek için veri bölümlerinin yeniden dengelenmesi durumunda da meydana gelebilir. İstemci uygulaması genellikle bu hatalardan birine neden olan işlemi yeniden dener. Bununla birlikte, ölçeklenebilirlik hedeflerini aştığından Azure Storage uygulamanızı azaltsa veya hizmet isteği başka bir nedenle hizmet veremese bile, agresif yeniden denemeler sorunu kötüleşme edebilir. Bir üstel geri deneme ilkesi kullanılması önerilir ve istemci kitaplıkları varsayılan olarak bu davranışa sahiptir. Örneğin, uygulamanız 2 saniye sonra, 4 saniye, sonra 10 saniye, 30 saniye sonra yeniden deneyebilir ve ardından tamamen daha fazla verebilir. Bu şekilde, uygulamanız exacerbating davranışı yerine hizmetin yükünü önemli ölçüde azaltır.  

Bağlantı hataları, azaltma sonucu olmadığı ve geçici olması beklenen için hemen yeniden denenebilir.  

### <a name="non-retryable-errors"></a>Yeniden denenmeyen hatalar

İstemci kitaplıkları, hangi hataların yeniden deneneceği ve ne işleyebileceği hakkında bir tanıma yeniden denemeler gerçekleştirir. Ancak, Azure Storage REST API doğrudan arıyorsanız, yeniden denenmemelisiniz bazı hatalar vardır. Örneğin, 400 (Hatalı Istek) hatası, istemci uygulamanın beklenen biçimde olmadığı için işlenemeyen bir istek gönderdiğini gösterir. Bu istek her seferinde aynı yanıtı elde eder, bu nedenle yeniden denenme noktası yoktur. Azure Storage REST API doğrudan arıyorsanız, olası hatalardan haberdar olun ve yeniden denenip denenmeyeceğini unutmayın.

Azure Storage hata kodları hakkında daha fazla bilgi için bkz. [durum ve hata kodları](/rest/api/storageservices/status-and-error-codes2).

## <a name="disable-nagle"></a>Nagle 'ı devre dışı bırak

Nagle 'ın algoritması, TCP/IP ağlarında yaygın olarak, ağ performansının geliştirilmesi için bir yol olarak uygulanır. Ancak, tüm koşullarda en uygun değildir (yüksek oranda etkileşimli ortamlar gibi). Nagle 'ın algoritması, Azure Tablo hizmetine yapılan isteklerin performansına yönelik olumsuz bir etkiye sahiptir ve mümkünse devre dışı bırakmanız gerekir.

## <a name="message-size"></a>İleti boyutu

İleti boyutu arttıkça kuyruk performansı ve ölçeklenebilirlik azalmasını azaltır. Yalnızca alıcının ihtiyacı olan bilgileri bir ileti içine alın.  

## <a name="batch-retrieval"></a>Toplu iş alımı

Tek bir işlemde bir kuyruktan en fazla 32 ileti alabilirsiniz. Toplu iş alımı, özellikle mobil cihazlar gibi yüksek gecikme süresine sahip olan ortamlar için yararlı olan istemci uygulamasından gelen gidiş dönüş sayısını azaltabilir.  

## <a name="queue-polling-interval"></a>Sıra yoklama aralığı

Çoğu uygulama, bu uygulama için en büyük işlem kaynaklarından biri olabilen bir kuyruktaki iletileri yoklamalıdır. Yoklama sıklığından daha seyrek seçim yapın: çok sık yoklama, uygulamanızın sıraya yönelik ölçeklenebilirlik hedeflerine yaklaşımına neden olabilir. Ancak, $0,01 (yazma sırasında) için 200.000 işlem sırasında, ayda her saniye bir kez tek bir işlemci yoklaması 15 aldan düşük bir değer olduğundan, maliyet genellikle yoklama aralığı seçiminizi etkileyen bir faktör değildir.  

Güncel maliyet bilgileri için bkz. [Azure Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="use-update-message"></a>Güncelleştirme Iletisi kullan

Hatalı bir zaman aşımını artırmak veya bir iletinin durum bilgilerini güncelleştirmek için **Ileti güncelleştirme** işlemini kullanabilirsiniz. **Güncelleştirme iletisi** kullanmak, işin her adımı tamamlandıktan sonra bir sıradan bir işi geçen bir iş akışı kullanmaktan daha verimli bir yaklaşım olabilir. Uygulamanız iş durumunu iletiye kaydedebilir ve sonra bir adım tamamlandığında işin bir sonraki adımına ilişkin iletiyi requeuing yerine çalışmaya devam edebilir. Her **güncelleştirme iletisi** işleminin ölçeklenebilirlik hedefine doğru olduğunu aklınızda bulundurun.

## <a name="application-architecture"></a>Uygulama mimarisi

Uygulama mimarinizi ölçeklenebilir hale getirmek için kuyrukları kullanın. Aşağıda, uygulamanızı daha ölçeklenebilir hale getirmek için kuyrukları kullanabileceğiniz bazı yollar listelenmiştir:  

- Uygulamanızdaki iş yüklerini işlemek ve düzgünleştirmek için biriktirme listeleri oluşturmak üzere kuyrukları kullanabilirsiniz. Örneğin, karşıya yüklenen görüntüleri yeniden boyutlandırma gibi işlemci yoğunluklu işleri gerçekleştirmek için kullanıcılardan gelen istekleri sıraya alabilirsiniz.
- Sıralarını bağımsız olarak ölçeklendirebilmeniz için, uygulamanızın parçalarını ayırmak için kuyrukları kullanabilirsiniz. Örneğin, bir Web ön ucu, daha sonra analiz ve depolama için kullanıcılardan anket sonuçlarını bir kuyruğa yerleştirebilir. Sıra verilerini gerektiği gibi işlemek için daha fazla çalışan rolü örneği ekleyebilirsiniz.  

## <a name="next-steps"></a>Sonraki adımlar

- [Depolama hesapları için Azure depolama ölçeklenebilirlik ve performans hedefleri](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Durum ve hata kodları](/rest/api/storageservices/Status-and-Error-Codes2)
