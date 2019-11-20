---
title: Tablo depolama-Azure depolama için performans ve ölçeklenebilirlik denetim listesi
description: Yüksek performanslı uygulamalar geliştirirken tablo depolamayla kullanılmak üzere kanıtlanmış uygulamaların denetim listesi.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: b36ed2cac7e5009a0581091252b36dcd5af81bd7
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389983"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>Tablo depolaması için performans ve ölçeklenebilirlik denetim listesi

Microsoft, tablo depolama ile yüksek performanslı uygulamalar geliştirmeye yönelik bir dizi kanıtlanmış uygulama geliştirmiştir. Bu denetim listesi, geliştiricilerin performansı iyileştirmek için izleyebildiği önemli uygulamaları tanımlar. Uygulamanızı tasarlarken ve işlem boyunca bu uygulamaları göz önünde bulundurun.

Azure depolama kapasitesi, işlem hızı ve bant genişliği için ölçeklenebilirlik ve performans hedefleri içerir. Azure depolama ölçeklenebilirlik hedefleri hakkında daha fazla bilgi için bkz. [depolama hesapları Için Azure Storage ölçeklenebilirlik ve performans hedefleri](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

## <a name="checklist"></a>Denetim Listesi

Bu makale, tablo depolama uygulamanızı geliştirirken izleyebileceğiniz bir denetim listesi halinde performans için kanıtlanmış uygulamaları düzenler.

| Bitti | Kategori | Tasarım değerlendirmesi |
| --- | --- | --- |
| &nbsp; |Ölçeklenebilirlik hedefleri |[Uygulamanızı en fazla depolama hesabı sayısından daha fazla kullanmak üzere tasarlayabilmeniz gerekebilir mi?](#maximum-number-of-storage-accounts) |
| &nbsp; |Ölçeklenebilirlik hedefleri |[Kapasite ve işlem sınırlarına yaklaşmaktan kaçınıyorsunuz musunuz?](#capacity-and-transaction-targets) |
| &nbsp; |Networking (Ağ İletişimi) |[İstemci tarafı cihazlarda gereken performansa ulaşmak için yeterli yüksek bant genişliği ve düşük gecikme süresi var mı?](#throughput) |
| &nbsp; |Networking (Ağ İletişimi) |[İstemci tarafı cihazların yüksek kaliteli bir ağ bağlantısı var mı?](#link-quality) |
| &nbsp; |Networking (Ağ İletişimi) |[İstemci uygulaması, depolama hesabıyla aynı bölgede mi?](#location) |
| &nbsp; |Doğrudan Istemci erişimi |[Azure Storage 'a doğrudan erişim sağlamak için paylaşılan erişim imzaları (SAS) ve çıkış noktaları arası kaynak paylaşımı (CORS) kullanıyor musunuz?](#sas-and-cors) |
| &nbsp; |Toplu İşleme |[Uygulamanızın varlık grubu işlemlerini kullanarak güncelleştirmeleri toplu işleme mi?](#batch-transactions) |
| &nbsp; |.NET yapılandırması |[En iyi performans için .NET Core 2,1 veya üstünü mi kullanıyorsunuz?](#use-net-core) |
| &nbsp; |.NET yapılandırması |[İstemcinizi yeterli sayıda eşzamanlı bağlantı kullanacak şekilde yapılandırdınız mı?](#increase-default-connection-limit) |
| &nbsp; |.NET yapılandırması |[.NET uygulamaları için, .NET 'i yeterli sayıda iş parçacığı kullanacak şekilde yapılandırdınız mı?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralellik |[Paralellik 'in, istemci yeteneklerini aşırı yüklemeden veya ölçeklenebilirlik hedeflerine yaklaşımak için uygun şekilde bağlanmış olduğunu merak etmeniz gerekir mi?](#unbounded-parallelism) |
| &nbsp; |Araçlar |[Microsoft tarafından sağlanmış istemci kitaplıklarının ve araçlarının en son sürümlerini kullanıyor musunuz?](#client-libraries-and-tools) |
| &nbsp; |Yeniden deneme sayısı |[Daraltma hataları ve zaman aşımları için üstel geri alma ile yeniden deneme İlkesi kullanıyor musunuz?](#timeout-and-server-busy-errors) |
| &nbsp; |Yeniden deneme sayısı |[Uygulamanız yeniden denenmeyen hatalara karşı yeniden denemeyi önler mi?](#non-retryable-errors) |
| &nbsp; |Ölçeklenebilirlik hedefleri |[Saniye başına ölçeklenebilirlik hedeflerine yaklaşıyor musunuz?](#table-specific-scalability-targets) |
| &nbsp; |Yapılandırma |[Tablo istekleriniz için JSON kullanıyor musunuz?](#use-json) |
| &nbsp; |Yapılandırma |[Küçük isteklerin performansını artırmak için Nagle algoritmasını kapattık mı?](#disable-nagle) |
| &nbsp; |Tablolar ve bölümler |[Verilerinizi düzgün şekilde bölümlensin mi?](#schema) |
| &nbsp; |Sık kullanılan bölümler |[Yalnızca Append ve yalnızca bitiş desenlerinin önüne mi sahip olabilirsiniz?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Sık kullanılan bölümler |[Eklemi/güncelleştirmeleriniz birçok bölüme yayılıyor?](#high-traffic-data) |
| &nbsp; |Sorgu kapsamı |[Şemanızı, nokta sorgularının çoğu durumda kullanılmasına izin vermek ve tablo sorguları gelişigüzel şekilde kullanılmak üzere tasarlamış musunuz?](#query-scope) |
| &nbsp; |Sorgu yoğunluğu |[Sorgularınız genellikle uygulamanızın kullanacağı satırları tarar ve döndürür mi?](#query-density) |
| &nbsp; |Döndürülen verileri sınırlama |[Gerekli olmayan varlıkların döndürülmemek için filtreleme kullanıyor musunuz?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Döndürülen verileri sınırlama |[Gerekli olmayan özellikleri döndürmemek için projeksiyon kullanıyor musunuz?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Normalleştirilmemiş |[Verileri almaya çalışırken verimsiz sorgulardan veya birden çok okuma isteğinin oluşmasını önlemenize izin veriyor musunuz?](#denormalization) |
| &nbsp; |Ekle, Güncelleştir ve Sil |[İşlem olması gereken veya gidiş dönüş sayısını azaltmak için aynı anda yapılabilecek istekleri toplu olarak gerçekleştirebilirsiniz mu?](#batching) |
| &nbsp; |Ekle, Güncelleştir ve Sil |[Yalnızca ekleme veya güncelleştirme çağrısı yapılıp yapılmayacağını öğrenmek için bir varlığı alma](#upsert) |
| &nbsp; |Ekle, Güncelleştir ve Sil |[Sık sık tek bir varlıkta birden çok varlık yerine özellikler olarak bir araya getirilen veri serisini mi saklayacaksınız?](#storing-data-series-in-a-single-entity) |
| &nbsp; |Ekle, Güncelleştir ve Sil |[Her zaman birlikte alınacak ve toplu işlemlere (örneğin, zaman serisi verileri) yazılabilecekleri varlıklar için tablolar yerine blob 'ları kullanarak göz önünde bulundurmanız gerekir mi?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Ölçeklenebilirlik hedefleri

Uygulamanız ölçeklenebilirlik hedeflerinin herhangi birini yaklaşırsa veya aşarsa, daha fazla işlem gecikmeleri veya azaltmasıyla karşılaşabilirler. Azure Storage uygulamanızı kısıtsalken, hizmet 503 (sunucu meşgul) veya 500 (Işlem zaman aşımı) hata kodları döndürmeye başlar. Ölçeklenebilirlik hedefleri sınırları içinde kalarak bu hatalardan kaçınmak, uygulamanızın performansını artırmanın önemli bir parçasıdır.

Tablo hizmeti için ölçeklenebilirlik hedefleri hakkında daha fazla bilgi için bkz. [depolama hesapları Için Azure Storage ölçeklenebilirlik ve performans hedefleri](/azure/storage/common/storage-scalability-targets?toc=%2fazure%2fstorage%2ftables%2ftoc.json#azure-table-storage-scale-targets).

### <a name="maximum-number-of-storage-accounts"></a>En fazla depolama hesabı sayısı

Belirli bir abonelik/bölge birleşimi için izin verilen en fazla depolama hesabı sayısına yaklaşdıysanız, parça, çıkış, saniye başına g/ç işlemi (ıOPS) veya kapasiteyi artırmak için birden fazla depolama hesabı kullanıyor musunuz? Bu senaryoda, Microsoft, mümkünse iş yükünüz için gereken depolama hesabı sayısını azaltmak üzere depolama hesapları için artan limitlerin avantajlarından yararlanmanızı öneriyor. Depolama Hesabınıza yönelik daha fazla limit istemek için [Azure desteğine](https://azure.microsoft.com/support/options/) başvurun. Daha fazla bilgi için bkz. daha [büyük, daha yüksek ölçekli depolama hesapları duyurusu](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Kapasite ve işlem hedefleri

Uygulamanız tek bir depolama hesabı için ölçeklenebilirlik hedeflerine yaklaşıyorsa, aşağıdaki yaklaşımlardan birini benimsede düşünün:  

- Uygulamanızın ölçeklenebilirlik hedefini yaklaşımını veya aşmasına neden olan iş yükünü yeniden değerlendirin. Daha az bant genişliği veya kapasite veya daha az işlem kullanmak için farklı bir şekilde tasarlayabilirsiniz mi?
- Uygulamanız ölçeklenebilirlik hedeflerinin birini aşmanız gerekiyorsa, birden çok depolama hesabı oluşturun ve uygulama verilerinizi bu birden çok depolama hesabı genelinde bölümleyin. Bu kalıbı kullanırsanız, daha sonra yük dengelemeye yönelik daha fazla depolama hesabı ekleyebilmeniz için uygulamanızı tasarlayadığınızdan emin olun. Depolama hesaplarının kendileri, depolanan veri, işlem yapılan veya aktarılan veri açısından kullanımınız dışında bir ücret içermez.
- Uygulamanız bant genişliği hedeflerine yaklaşıyorsa, verileri Azure depolama 'ya göndermek için gereken bant genişliğini azaltmak için istemci tarafındaki verileri sıkıştırmayı göz önünde bulundurun.
    Verilerin sıkıştırılması bant genişliğini kaydedebilir ve ağ performansını iyileştireken performansı olumsuz etkileyebilir. İstemci tarafında veri sıkıştırma ve açma için ek işleme gereksinimlerinin performans etkisini değerlendirin. Sıkıştırılmış verilerin depolanması sorun gidermeyi daha zor hale getirir, çünkü verileri standart araçlar kullanarak görüntülemek daha zor olabilir.
- Uygulamanız ölçeklenebilirlik hedeflerine yaklaşıyorsa, yeniden denemeler için bir üstel geri alma kullandığınızdan emin olun. Bu makalede açıklanan önerileri uygulayarak ölçeklenebilirlik hedeflerine ulaşmaktan kaçınmak en iyisidir. Ancak, yeniden denemeler için bir üstel geri alma kullanılması, uygulamanızın hızlı bir şekilde yeniden denenmesini engelleyecek ve bu da azaltmayı daha kötüleşmektedir. Daha fazla bilgi için [zaman aşımı ve sunucu meşgul hataları](#timeout-and-server-busy-errors)başlıklı bölüme bakın.

## <a name="table-specific-scalability-targets"></a>Tabloya özgü ölçeklenebilirlik hedefleri

Tüm depolama hesabının bant genişliği kısıtlamalarına ek olarak, tablolarda aşağıdaki belirli ölçeklenebilirlik limiti vardır. Trafiğiniz arttıkça sistem yük dengelemeye başlayacaktır, ancak trafiğiniz ani bursts içeriyorsa, bu üretilen iş hacmini hemen alamıyoruz. Düzeniniz bursts içeriyorsa, depolama hizmeti otomatik olarak tablonuzun dengeleneceği sırada, veri bloğu sırasında azaltma ve/veya zaman aşımlarını görmeyi beklemelisiniz. Yavaş yavaş artırma işlemi, sistem zamanının uygun şekilde yük dengelemesine verdiği için genellikle daha iyi sonuçlar elde etmenizi sağlar.

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

## <a name="batch-transactions"></a>Toplu işlemler

Tablo hizmeti aynı tablodaki ve aynı bölüm grubuna ait olan varlıklarda toplu işlemleri destekler. Daha fazla bilgi için bkz. [varlık grubu Işlemleri gerçekleştirme](/rest/api/storageservices/performing-entity-group-transactions).

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

## <a name="configuration"></a>Yapılandırma

Bu bölümde, tablo hizmetinde önemli performans iyileştirmeleri yapmak için kullanabileceğiniz çeşitli hızlı yapılandırma ayarları listelenmektedir:

### <a name="use-json"></a>JSON kullanma

Storage hizmeti sürüm 2013-08-15 ' den başlayarak tablo hizmeti, tablo verilerini aktarmak için XML tabanlı AtomPub biçimi yerine JSON kullanımını destekler. JSON kullanımı, yük boyutunu %75 kadar azaltabilir ve uygulamanızın performansını önemli ölçüde iyileştirebilir.

Daha fazla bilgi için bkz. Gönderi [Microsoft Azure tabloları:](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) [tablo HIZMETI işlemleri Için JSON ve yük biçimine](https://msdn.microsoft.com/library/azure/dn535600.aspx)giriş.

### <a name="disable-nagle"></a>Nagle 'ı devre dışı bırak

Nagle 'ın algoritması, TCP/IP ağlarında yaygın olarak, ağ performansının geliştirilmesi için bir yol olarak uygulanır. Ancak, tüm koşullarda en uygun değildir (yüksek oranda etkileşimli ortamlar gibi). Nagle 'ın algoritması, Azure Tablo hizmetine yapılan isteklerin performansına yönelik olumsuz bir etkiye sahiptir ve mümkünse devre dışı bırakmanız gerekir.

## <a name="schema"></a>Şema

Verilerinizi nasıl temsil edersiniz ve sorgular, tablo hizmetinin performansını etkileyen en büyük tek etmendir. Her uygulama farklı olsa da, bu bölümde ilgili genel olarak kanıtlanmış bazı yöntemler özetlenmektedir:

- Tablo tasarımı
- Etkili sorgular
- Verimli veri güncelleştirmeleri

### <a name="tables-and-partitions"></a>Tablolar ve bölümler

Tablolar bölümlere ayrılmıştır. Bir bölümde depolanan her varlık aynı bölüm anahtarını paylaşır ve bu bölüm içinde tanımlamak için benzersiz bir satır anahtarına sahiptir. Bölümler avantajlar sağlar, ancak ölçeklenebilirlik sınırları da getirir.

- Avantajlar: en fazla 100 ayrı depolama işlemini (Toplam 4 MB 'lık sınır) içeren tek bir atomik, toplu işlemde aynı bölümdeki varlıkları güncelleştirebilirsiniz. Aynı sayıda varlık alınması varsayıldığında, tek bir bölümdeki verileri bölümlere yayılan verilerden daha verimli bir şekilde sorgulayabilirsiniz (ancak tablo verilerini sorgulama hakkında daha fazla öneri için okumaya devam edebilirsiniz).
- Ölçeklenebilirlik sınırı: bölümler atomik toplu işlem işlemlerini desteklediği için tek bir bölümde depolanan varlıklara erişim yük dengeli olamaz. Bu nedenle, tek bir tablo bölümünün ölçeklenebilirlik hedefi tablo hizmeti 'nin tamamına göre daha düşüktür.

Bu tablo ve bölümlerin bu özellikleri nedeniyle, aşağıdaki tasarım ilkelerini benimsemelisiniz:

- İstemci uygulamanızın aynı bölümde bulunan aynı mantıksal birim çalışma biriminde sıkça güncelleştirdiği verileri veya sorguları bulun. Örneğin, uygulamanız yazma işlemi yapıyorsa veya atomik toplu işlem işlemleri uyguluyorsanız aynı bölümdeki verileri bulun. Ayrıca, tek bir bölümdeki veriler, bölümler genelinde verilerin tek bir sorgusunda daha verimli bir şekilde sorgulanmasını sağlar.
- İstemci uygulamanızın aynı mantıksal iş biriminde (yani, tek bir sorgu veya toplu güncelleştirmede), ayrı bölümlerde yer aldığı, güncelleştirmediğinden veya sorgu içermediği verileri bulun. Tek bir tablodaki bölüm anahtarlarının sayısıyla ilgili bir sınır olmadığını unutmayın, bu nedenle milyonlarca bölüm anahtarı bir sorun değildir ve performansı etkilemez. Örneğin, uygulamanız kullanıcı oturum açma içeren popüler bir Web sitesidir, bölüm anahtarı olarak kullanıcı KIMLIĞINI kullanmak iyi bir seçenek olabilir.

#### <a name="hot-partitions"></a>Sık kullanılan bölümler

Etkin bir bölüm, bir hesaba giden trafiğin orantısız yüzdesini alan ve tek bir bölüm olduğundan yük dengeli bir bölümdür. Genel olarak, sık kullanılan bölümler iki şekilde oluşturulur:

#### <a name="append-only-and-prepend-only-patterns"></a>Yalnızca sonuna ekle ve yalnızca önüne Ekle desenleri

"Yalnızca Append" deseninin belirli bir bölüm anahtarı için trafiğin tümünün (veya neredeyse hepsi) geçerli saate göre arttığı ve azaldığı bir tane vardır. Örneğin, uygulamanızın günlük verileri için bir bölüm anahtarı olarak geçerli tarihi kullandığını varsayalım. Bu tasarım, tablodaki son bölüme giden tüm eklemelerin ve sistemin doğru şekilde yük dengelenemez. Bu bölüme giden trafik hacmi bölüm düzeyi ölçeklenebilirlik hedefini aşarsa, azaltma ile sonuçlanır. Trafiğin, isteklerin tablo genelinde yük dengelenmesi için birden çok bölüme gönderilmesini sağlamak daha iyidir.

#### <a name="high-traffic-data"></a>Yüksek trafik verileri

Bölümleme düzeniniz yalnızca diğer bölümlerden daha fazla kullanılan verilerin bulunduğu tek bir bölüm ile sonuçlanırsa, bu bölüm tek bir bölüm için ölçeklenebilirlik hedefine yaklaşırsa azaltma da görebilirsiniz. Bölüm şemanızın ölçeklenebilirlik hedeflerine yaklaştığı tek bir bölüm içermediğinden emin olmak daha iyidir.

### <a name="querying"></a>Sorgulama

Bu bölümde, tablo hizmetini sorgulamak için kanıtlanmış uygulamalar açıklanmaktadır.

#### <a name="query-scope"></a>Sorgu kapsamı

Sorgulanacak varlık aralığını belirtmek için birkaç yol vardır. Aşağıdaki listede sorgu kapsamı için her bir seçenek açıklanır.

- **Nokta sorguları:** -nokta sorgusu, alınacak varlığın bölüm anahtarını ve satır anahtarını belirterek tam olarak bir varlık alır. Bu sorgular verimlidir ve bunları mümkün olan yerlerde kullanmanız gerekir.
- **Bölüm sorguları:** Bölüm sorgusu, ortak bir bölüm anahtarını paylaşan bir veri kümesini alan bir sorgudur. Genellikle sorgu, bölüm anahtarına ek olarak, bazı varlık özelliklerine ilişkin bir dizi satır anahtarı veya bir değer aralığı belirtir. Bu sorgular nokta sorgularından daha az verimlidir ve gelişigüzel kullanılmalıdır.
- **Tablo sorguları:** Tablo sorgusu, ortak bir bölüm anahtarını paylaşmayan bir varlık kümesini alan bir sorgudur. Bu sorgular etkili değildir ve mümkünse bunları kullanmaktan kaçının.

Genel olarak, taramalardan kaçının (tek bir varlıktan daha büyük olan sorgular), ancak tarama yapmanız gerekirse, taramalarınızın ihtiyaç duymadığınız önemli miktarda varlığı taramadan veya döndürmeksizin ihtiyacınız olan verileri alabilmesi için verilerinizi düzenlemeyi deneyin.

#### <a name="query-density"></a>Sorgu yoğunluğu

Sorgu verimliliği ' nda başka bir anahtar faktörü, döndürülen kümeyi bulmak için taranan varlık sayısına kıyasla döndürülen varlıkların sayısıdır. Uygulamanız veri paylaşımlarının yalnızca %1 ' i olan özellik değeri için filtre içeren bir tablo sorgusu gerçekleştirdiğinde, sorgu döndürdüğü her bir varlık için 100 varlıklarını tarar. Daha önce ele alınan tablo ölçeklenebilirliği hedefleri, döndürülen varlıkların sayısıyla değil, taranan varlık sayısıyla ilgilidir: düşük bir sorgu yoğunluğu, tablo hizmetinin uygulamanızı daha fazla sayıda varlık taraması gerektiği için kolayca genişletmesine neden olabilir. Aradığınız varlığı alın. Azaltmayı önlemek hakkında daha fazla bilgi için, bkz [..](#denormalization)

#### <a name="limiting-the-amount-of-data-returned"></a>Döndürülen veri miktarını sınırlama

Bir sorgunun istemci uygulamada ihtiyaç duymayacak varlıkları döndürmeyeceğini bildiğiniz zaman, döndürülen küme boyutunu azaltmak için bir filtre kullanmayı düşünün. İstemciye döndürülmediği varlıklar hala ölçeklenebilirlik sınırlarına yaklaşmaya devam ederken, daha az ağ yükü boyutu ve istemci uygulamanızın işlemesi gereken daha az sayıda varlık nedeniyle uygulama performansından iyileşecek. Ölçeklenebilirlik hedeflerinin taranan varlık sayısıyla ilgili olduğunu aklınızda bulundurun. bu nedenle, çok sayıda varlığı filtreleyen bir sorgu, birkaç varlık döndürülse bile yine de kısıtlama sağlayabilir. Sorguları verimli hale getirme hakkında daha fazla bilgi için, [sorgu yoğunluğu](#query-density)başlıklı bölüme bakın.

İstemci uygulamanız, tablonuzdaki varlıklardan yalnızca sınırlı bir özellik kümesine ihtiyaç duyuyorsa, döndürülen veri kümesinin boyutunu sınırlamak için projeksiyonu kullanabilirsiniz. Filtreleme sırasında, projeksiyon ağ yükü ve istemci işlemesini azaltmaya yardımcı olur.

#### <a name="denormalization"></a>Normalleştirilmemiş

İlişkisel veritabanlarıyla çalışmaktan farklı olarak, tablo verilerinin etkin bir şekilde sorgulanmasına yönelik kanıtlanmış uygulamalar, verilerinizin normalleştirilmesi için Diğer bir deyişle, bir sorgunun,, uygulamanızın verileri bulmak için çok sayıda varlığı taramak zorunda kalmadan, istemci ihtiyacı olan verileri bulmak için taraması gereken varlıkların sayısını en aza indirmek için, aynı verileri birden çok varlıkta çoğaltma (verileri bulmak için kullanabileceğiniz her anahtar için bir tane) uygulaması gerekiyor. Örneğin, bir e-ticaret Web sitesinde, hem müşteri KIMLIĞI hem de (bu müşterinin emirlerini bana) ve tarihe göre (bir tarih için bana sipariş siparişi ver) bir sipariş bulmak isteyebilirsiniz. Tablo depolama alanında, müşteri KIMLIĞINE göre bulmayı kolaylaştırmak için bir kez tablo adı, PK ve RK ile her iki kez bir kez (veya bir başvuru) depolamak en iyi yöntemdir.  

### <a name="insert-update-and-delete"></a>Ekle, Güncelleştir ve Sil

Bu bölümde, tablo hizmetinde depolanan varlıkları değiştirmeye yönelik kanıtlanmış uygulamalar açıklanmaktadır.  

#### <a name="batching"></a>Toplu İşleme

Toplu işlemler, Azure depolama 'da varlık grubu işlemleri olarak bilinir. Bir varlık grubu işleminin içindeki tüm işlemler tek bir tabloda tek bir bölümde olmalıdır. Mümkün olduğunda, toplu iş ekleme, güncelleştirme ve silme işlemlerini gerçekleştirmek için varlık grubu işlemlerini kullanın. Varlık grubu işlemlerinin kullanılması, istemci uygulamanızdan sunucuya gidiş dönüş sayısını azaltır, faturalandırılabilir işlem sayısını azaltır (bir varlık grubu işlem sayıları Faturalandırma amacıyla tek bir işlem olarak ve en fazla 100 olabilir depolama işlemleri) ve atomik güncelleştirmeleri (tüm işlemler başarılı veya bir varlık grubu işlemi içinde başarısız olur) sunar. Mobil cihazlar gibi yüksek gecikme sürelerine sahip ortamlar, varlık grubu işlemlerini kullanmanın büyük ölçüde avantajına sahiptir.  

#### <a name="upsert"></a>Upsert

Mümkün olan her yerde tablo ön **Ekle** işlemlerini kullanın. Her ikisi de geleneksel bir **ekleme** ve **güncelleştirme** işlemlerinden daha verimli olabilecek iki tür **upsert**vardır:  

- **Insertormerge**: varlığın özelliklerinin bir alt kümesini karşıya yüklemek istediğinizde bu işlemi kullanın, ancak varlığın zaten var olup olmadığından emin olun. Varlık varsa, bu çağrı, **upsert** işleme dahil olan özellikleri güncelleştirir ve tüm mevcut özellikleri olduğu gibi bırakır; varlık yoksa, yeni varlığı ekler. Bu, bir sorguda projeksiyon kullanılmasına benzerdir, ancak yalnızca değişen özellikleri karşıya yüklemeniz yeterlidir.
- **Insertorreplace**: tamamen yeni bir varlık yüklemek istediğinizde bu işlemi kullanın, ancak zaten var olup olmadığını doğrulayın. Yeni yüklenen varlığın tamamen doğru olduğunu bildiğiniz ve eski varlığın üzerine yazmadığı için bu işlemi kullanın. Örneğin, uygulamanın daha önce Kullanıcı için konum verilerini depoladığına bakılmaksızın kullanıcının geçerli konumunu depolayan varlığı güncelleştirmek istersiniz; Yeni konum varlığı tamamlanmıştır ve önceki varlıklardan herhangi bir bilgi almanız gerekmez.

#### <a name="storing-data-series-in-a-single-entity"></a>Veri serisini tek bir varlıkta depolama

Bazen, bir uygulama, sıklıkla tümünü bir kez almak için ihtiyaç duyduğunu bir veri serisini depolar: Örneğin, bir uygulama, son 24 saat içindeki verilerin sıralı bir grafiğini çizmek için zaman içinde CPU kullanımını izleyebilir. Tek bir yaklaşım, her bir varlığa belirli bir saati temsil eden ve bu saat için CPU kullanımını depolayan her bir bir saat için bir adet tablo varlığına sahiptir. Bu verileri çizmek için, uygulamanın verileri en son 24 saatten tutan varlıkları alması gerekir.  

Alternatif olarak, uygulamanız her saat için CPU kullanımını tek bir varlığın ayrı bir özelliği olarak saklayabilir: her saat güncelleştirmek için, uygulamanız en son saatin değerini güncelleştirmek üzere tek bir **ınsertormerge upsert** çağrısı kullanabilir. Verilerin çizilmesi için, uygulamanın yalnızca 24 yerine tek bir varlık alması gerekir, verimli bir sorgu için. Sorgu verimliliği hakkında daha fazla bilgi için, bkz. [sorgu kapsamı](#query-scope)başlıklı Bölüm).

#### <a name="storing-structured-data-in-blobs"></a>Yapılandırılmış verileri bloblarda depolama

Batch ekleme ve daha sonra varlık aralıklarını birlikte alma işlemi yapıyorsanız, tablolar yerine blob 'ları kullanmayı düşünün. Bir günlük dosyası iyi bir örnektir. Birkaç dakikalık günlüğe yığın ekleyebilir ve bunları ekleyebilir ve aynı zamanda birkaç dakika günlük alabilirsiniz. Bu durumda, bir veya daha fazla yazılan nesne sayısını önemli ölçüde azaltabileceğinizden ve muhtemelen yapılması gereken isteklerin sayısı önemli ölçüde azaldığından, tablolar yerine blob 'lar kullanırsanız performans daha iyidir.  

## <a name="next-steps"></a>Sonraki adımlar

- [Depolama hesapları için Azure depolama ölçeklenebilirlik ve performans hedefleri](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Durum ve hata kodları](/rest/api/storageservices/Status-and-Error-Codes2)
