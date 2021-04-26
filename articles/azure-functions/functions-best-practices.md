---
title: Azure Işlevleri için en iyi uygulamalar
description: Azure Işlevleri için en iyi uygulamaları ve desenleri öğrenin.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5783f8092a6435b43ab8720df18cc5200e390d46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378256"
---
# <a name="best-practices-for-performance-and-reliability-of-azure-functions"></a>Azure Işlevlerinin performansı ve güvenilirliği için en iyi uygulamalar

Bu makale, [sunucusuz](https://azure.microsoft.com/solutions/serverless/) işlev uygulamalarınızın performansını ve güvenilirliğini artırmaya yönelik rehberlik sağlar.  

Azure Işlevleri 'ni kullanarak sunucusuz çözümlerinizi oluşturma ve mimarinizi geliştirme konusunda en iyi yöntemler aşağıda verilmiştir.

## <a name="avoid-long-running-functions"></a>Uzun süre çalışan işlevlerden kaçının

Büyük ve uzun süre çalışan işlevler, beklenmeyen zaman aşımı sorunlarına neden olabilir. Belirli bir barındırma planının zaman aşımları hakkında daha fazla bilgi edinmek için bkz. [işlev uygulaması zaman aşımı süresi](functions-scale.md#timeout).

Birçok Node.js bağımlılığı nedeniyle bir işlev büyük olabilir. Bağımlılıkları içeri aktarmak, beklenmedik zaman aşımları ile sonuçlanan daha fazla yükleme süresi de oluşmasına neden olabilir. Bağımlılıklar hem açık hem de örtük olarak yüklenir. Kodunuz tarafından yüklenen tek bir modül kendi ek modüllerini yükleyebilir.

Mümkün olduğunda, büyük işlevleri birlikte çalışarak daha küçük işlev kümelerine yeniden düzenleyin ve yanıtları hızlı bir şekilde geri döndürün. Örneğin, bir Web kancası veya HTTP tetikleyici işlevi belirli bir zaman sınırı içinde bir bildirim yanıtı gerektirebilir; Web kancalarının anında yanıt gerektirmesi için yaygındır. HTTP tetikleyici yükünü bir kuyruk tetikleyicisi işlevi tarafından işlenmek üzere bir kuyruğa geçirebilirsiniz. Bu yaklaşım, gerçek işi ertelemenizi ve anında yanıt döndürmenizi sağlar.

## <a name="cross-function-communication"></a>Çapraz işlev iletişimi

[Dayanıklı işlevler](durable/durable-functions-overview.md) ve [Azure Logic Apps](../logic-apps/logic-apps-overview.md) , durum geçişlerini ve birden çok işlev arasındaki iletişimi yönetmek için oluşturulmuştur.

Birden çok işlevle tümleştirilecek Dayanıklı İşlevler veya Logic Apps kullanmıyorsanız, bu işlemler arası iletişim için depolama kuyruklarını kullanmak en iyisidir. Ana neden, depolama sıralarının diğer depolama seçeneklerinden daha kolay sağlanması ve sağlanması çok daha kolaydır.

Depolama sırasındaki tek tek mesajlar boyut olarak 64 KB ile sınırlıdır. İşlevler arasında daha büyük iletiler geçirmeniz gerekiyorsa, Standart katmanda 256 KB 'ye kadar olan ileti boyutlarını desteklemek için bir Azure Service Bus kuyruğu ve Premium katmanında en fazla 1 MB kullanılabilir.

Service Bus konular, işlemeden önce ileti filtrelemesini gerekli kıldıysanız yararlıdır.

Olay Hub 'ları, yüksek hacimli iletişimleri desteklemek için faydalıdır.

## <a name="write-functions-to-be-stateless"></a>İşlevleri durum bilgisiz olacak şekilde yaz

İşlevler, mümkünse durum bilgisiz ve ıdempotent olmalıdır. Gerekli durum bilgilerini verilerinizle ilişkilendirin. Örneğin, işlenen bir sıra, muhtemelen ilişkili bir üyeye sahip olabilir `state` . İşlev durum bilgisiz olmaya devam ederken bir işlev bu duruma göre bir sırayı işleyebilir.

Idempotent işlevleri, özellikle Zamanlayıcı tetikleyicilerle önerilir. Örneğin, her gün bir kez çalışması gereken bir şeydir varsa, aynı sonuçlarla her zaman çalışacak şekilde bu şekilde yazın. Belirli bir gün için iş olmadığında işlev çıkabilir. Ayrıca, önceki bir çalıştırmanın tamamlanmadıysa, sonraki çalıştırmanın kaldığınız yerden devam etmesi gerekir.

## <a name="write-defensive-functions"></a>Savunma işlevlerini yaz

İşlevinizin istediğiniz zaman bir özel durumla karşılaşdığını varsayın. Bir sonraki yürütme sırasında işlevlerinizi önceki bir başarısızlık noktasından devam etme özelliğiyle tasarlayın. Aşağıdaki eylemleri gerektiren bir senaryo düşünün:

1. Veritabanında 10.000 satırı sorgulayın.
2. Bu satırların her biri için çizgi üzerinde daha fazla işlem yapmak üzere bir kuyruk iletisi oluşturun.

Sisteminizin ne kadar karmaşıkdığına bağlı olarak, bu durum, hatalı, ağ kesintileri veya kota limitlerinin ulaştığı, vb. gibi davranan aşağı akış Hizmetleri olabilir. Tüm bunlar, işlevinizi dilediğiniz zaman etkileyebilir. İşlevlerinizi hazırlanmakta olacak şekilde tasarlamanız gerekir.

İşlenmek üzere bu öğelerin 5.000 ' i bir sıraya ekledikten sonra bir hata oluşursa, kodunuz nasıl tepki veriyor? Tamamladığınız bir küme içindeki öğeleri izleyin. Aksi halde, bir dahaki sefer yeniden ekleyebilirsiniz. Bu çift ekleme iş akışınız üzerinde ciddi bir etkiye sahip olabilir, bu nedenle [işlevlerinizi ıdempotent yapın](functions-idempotent.md). 

Bir kuyruk öğesi zaten işlendiyse, işlevinizin işlem dışı çalışmasına izin verin.

Azure Işlevleri platformunda kullandığınız bileşenler için zaten sağlanmış olan savunma ölçülerinin avantajlarından yararlanın. Örneğin, bkz. [Azure depolama kuyruğu Tetikleyicileri ve bağlamaları](functions-bindings-storage-queue-trigger.md#poison-messages)belgelerinde **Poison Queue iletilerini işleme** .

## <a name="function-organization-best-practices"></a>İşlev organizasyonu en iyi uygulamaları

Çözümünüzün bir parçası olarak, birden çok işlev geliştirebilir ve yayımlayabilirsiniz. Bu işlevler genellikle tek bir işlev uygulamasında birleştirilir, ancak aynı zamanda ayrı işlev uygulamalarında da çalışabilir. Premium ve adanmış (App Service) barındırma planlarında, birden çok işlev uygulaması aynı kaynakları aynı planda çalıştırarak da paylaşabilir. İşlevlerinizi ve işlev uygulamalarınızı nasıl gruplandırdığı, genel çözümünüzün performansını, ölçeklendirilmesini, yapılandırmasını, dağıtımını ve güvenliğini etkileyebilir. Her senaryo için uygulanan kurallar yok, bu nedenle işlevlerinizi planlarken ve geliştirirken bu bölümdeki bilgileri göz önünde bulundurun.

### <a name="organize-functions-for-performance-and-scaling"></a>Performans ve ölçeklendirme işlevlerini düzenleme

Oluşturduğunuz her işlevin bir bellek ayak izi vardır. Bu ayak izi genellikle küçük olsa da, bir işlev uygulaması içinde çok fazla işlev olması, uygulamanızın yeni örneklerde daha yavaş başlatılmasını sağlayabilir. Ayrıca, işlev uygulamanızın genel bellek kullanımının daha yüksek olabileceği anlamına gelir. Çok sayıda işlevin tek bir uygulamada olması gerektiğini söylemek zordur ve bu, belirli iş yüküne bağlıdır. Ancak, işleviniz bellekte çok fazla veri depoluyorsa, tek bir uygulamada daha az işlev olmasını düşünün.

Birden çok işlev uygulamalarını tek bir Premium planda veya adanmış (App Service) planda çalıştırırsanız, bu uygulamaların hepsi birlikte ölçeklendirilir. Diğerlerinden çok daha yüksek bellek gereksinimlerine sahip bir işlev uygulamanız varsa, uygulamanın dağıtıldığı her örnek üzerinde orantısız miktarda bellek kaynağı kullanır. Bu, her örnekteki diğer uygulamalar için daha az bellek bırakılamadığından, kendi ayrı barındırma planında bunun gibi yüksek bellekli bir işlev uygulaması çalıştırmak isteyebilirsiniz.

> [!NOTE]
> [Tüketim planını](./functions-scale.md)kullanırken, uygulamalar yine de bağımsız olarak ölçeklendirildiğinden her uygulamayı her zaman kendi planına yerleştirmeniz önerilir.

İşlevleri farklı yük profilleriyle gruplandırmak isteyip istemediğinizi göz önünde bulundurun. Örneğin, çok sayıda sıra iletisini işleyen bir işleviniz varsa ve yalnızca zaman içinde çağrılan ancak yüksek bellek gereksinimlerine sahip olan başka bir işleviniz varsa, bunları ayrı işlev uygulamalarında dağıtmak isteyebilirsiniz, bu sayede kendi kaynak kümelerini alırlar ve bunların birbirinden bağımsız olarak ölçeklendirilmesini sağlayabilirsiniz.

### <a name="organize-functions-for-configuration-and-deployment"></a>Yapılandırma ve dağıtım için işlevleri düzenleme

İşlev uygulamalarının bir `host.json` dosyası vardır ve bu, işlev Tetikleyicileri ve Azure işlevleri çalışma zamanının gelişmiş davranışlarını yapılandırmak için kullanılır. `host.json`Dosyadaki değişiklikler uygulama içindeki tüm işlevlere uygulanır. Özel yapılandırmalara ihtiyacı olan bazı işlevleriniz varsa, bunları kendi işlev uygulamasına taşımayı göz önünde bulundurun.

Yerel projenizdeki tüm işlevler, Azure 'daki işlev uygulamanıza bir dosya kümesi olarak birlikte dağıtılır. Bağımsız işlevleri ayrı ayrı dağıtmanız veya bazı işlevler için [dağıtım yuvaları](./functions-deployment-slots.md) gibi özellikleri kullanmanız gerekebilir. Böyle durumlarda, bu işlevleri (ayrı kod projelerinde) farklı işlev uygulamalarına dağıtmanız gerekir.

### <a name="organize-functions-by-privilege"></a>İşlevleri ayrıcalığa göre düzenleme

Uygulama ayarlarında depolanan bağlantı dizeleri ve diğer kimlik bilgileri, işlev uygulamasındaki tüm işlevleri ilişkili kaynakta aynı izin kümesi sağlar. Bu kimlik bilgilerini kullanmayan işlevleri ayrı bir işlev uygulamasına taşıyarak belirli kimlik bilgilerine erişimi olan işlev sayısını en aza indirgemeniz önerilir. Farklı işlev uygulamalarındaki işlevler arasında veri geçirmek için her zaman [işlev zinciri](/learn/modules/chain-azure-functions-data-using-bindings/) gibi teknikler kullanabilirsiniz.  

## <a name="scalability-best-practices"></a>Ölçeklenebilirlik en iyi uygulamaları

İşlev uygulamanızın nasıl ölçeklenmesi etkileyebilecek bir dizi etken vardır. Ayrıntılar, [işlev ölçeklendirmeyle](functions-scale.md)ilgili belgelerde verilmiştir.  Aşağıda, bir işlev uygulamasının en iyi şekilde ölçeklenebilirliğini sağlamak için bazı en iyi yöntemler verilmiştir.

### <a name="share-and-manage-connections"></a>Bağlantıları paylaşma ve yönetme

Mümkün olduğunda dış kaynaklarla bağlantıları yeniden kullanın. Bkz. [Azure işlevlerinde bağlantıları yönetme](./manage-connections.md).

### <a name="avoid-sharing-storage-accounts"></a>Depolama hesaplarını paylaşmayı önleyin

Bir işlev uygulaması oluşturduğunuzda, bunu bir depolama hesabıyla ilişkilendirmeniz gerekir. Depolama hesabı bağlantısı [AzureWebJobsStorage uygulama ayarında](./functions-app-settings.md#azurewebjobsstorage)tutulur.

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Aynı işlev uygulamasında test ve üretim kodunu karıştırmayın

İşlev uygulaması içindeki işlevler kaynakları paylaşır. Örneğin, bellek paylaşılır. Üretimde bir işlev uygulaması kullanıyorsanız, bu uygulamaya test ile ilgili işlevler ve kaynaklar eklemeyin. Üretim kodu yürütme sırasında beklenmeyen yüke neden olabilir.

Üretim işlevi uygulamalarınızda ne yüklediklerinizi dikkatli olun. Uygulamadaki her bir işlevde bellek ortalaması.

Çoklu .NET işlevlerinde başvurulan paylaşılan bir derlemeniz varsa, bunu ortak bir paylaşılan klasöre koyun. Aksi halde, işlevler arasında farklı şekilde davranan aynı ikilinin yanlışlıkla birden çok sürümünü dağıtabilirsiniz.

Üretim kodunda, olumsuz bir performans etkisi olan ayrıntılı günlük kullanmayın.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Zaman uyumsuz kod kullan ancak çağrı engellemeyi önleyin

Zaman uyumsuz programlama, özellikle ı/O işlemlerini engellemeye yönelik önerilen en iyi uygulamadır.

C# dilinde her zaman `Result` bir örnek üzerinde özelliğe veya çağırma yöntemine başvurulmaktan kaçının `Wait` `Task` . Bu yaklaşım iş parçacığı tükenmesine yol açabilir.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Birden çok çalışan işlemi kullanma

Varsayılan olarak, Işlevler için herhangi bir konak örneği tek bir çalışan işlemi kullanır. Özellikle Python gibi tek iş parçacıklı Çalışma zamanları ile performansı artırmak için, ana bilgisayar başına çalışan işlem sayısını (10 ' a kadar) artırmak için [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) kullanın. Azure Işlevleri daha sonra bu çalışanlar genelinde aynı anda eşzamanlı işlev etkinleştirmeleri dağıtmaya çalışır.

FUNCTIONS_WORKER_PROCESS_COUNT, uygulamanızın talebi karşılamak üzere ölçeklenmesi sırasında oluşturduğu her bir konak için geçerlidir.

### <a name="receive-messages-in-batch-whenever-possible"></a>Mümkün olduğunda toplu iş içinde ileti alma

Olay Hub 'ı gibi bazı Tetikleyiciler tek bir çağrıdan bir toplu ileti almayı sağlar.  Toplu işlem iletilerinin çok daha iyi performansı vardır.  Dosyadaki en büyük toplu iş boyutunu `host.json` [başvuru belgelerindekihost.js](functions-host-json.md) ayrıntılı şekilde yapılandırabilirsiniz.

C# işlevleri için türü türü kesin belirlenmiş bir dizi olarak değiştirebilirsiniz.  Örneğin, `EventData sensorEvent` Yöntem imzası yerine olabilir `EventData[] sensorEvent` .  Diğer diller için, `function.json` `many` [burada gösterildiği gibi](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10)toplu işi etkinleştirmek için ' de kardinalite özelliğini açık olarak ayarlamanız gerekir.

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Eşzamanlılık daha iyi işlemek için konak davranışlarını yapılandırın

`host.json`İşlev uygulamasındaki dosya, ana bilgisayar çalışma zamanı ve tetikleyici davranışları yapılandırmasına izin verir.  İşleme davranışlarına ek olarak, bir dizi tetikleyici için eşzamanlılık yönetebilirsiniz. Genellikle bu seçeneklerdeki değerlerin ayarlanması, her bir örneğin, çağrılan işlevlerin taleplerine uygun şekilde ölçeklendirilmesine yardımcı olabilir.

Dosyadaki host.jsayarları, işlevin *tek bir örneği* içinde, uygulamadaki tüm işlevler arasında geçerlidir. Örneğin, iki HTTP işlevi olan bir işlev uygulamanız varsa ve [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) istekler 25 olarak ayarlandıysa, http tetikleyicisine yönelik bir istek paylaşılan 25 eşzamanlı istek için doğru sayılır.  Bu işlev uygulaması 10 örneğe ölçeklenirse on işlevleri 250 eşzamanlı istek (10 örnek * örnek başına 25 eşzamanlı istek) etkin bir şekilde izin verir. 

Diğer konak yapılandırma seçenekleri, [host.jsyapılandırma makalesinde](functions-host-json.md)bulunur.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Azure Işlevlerinde bağlantıları yönetme](manage-connections.md)
* [En iyi Azure App Service uygulamalar](../app-service/app-service-best-practices.md)
