---
title: Azure Işlevleri için en iyi uygulamalar | Microsoft Docs
description: Azure Işlevleri için en iyi uygulamaları ve desenleri öğrenin.
author: ggailey777
manager: gwallace
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/16/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 87071b8e1102067110baae70c424aa74a5e0702c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73570818"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Azure Işlevlerinin performansını ve güvenilirliğini iyileştirin

Bu makale, [sunucusuz](https://azure.microsoft.com/solutions/serverless/) işlev uygulamalarınızın performansını ve güvenilirliğini artırmaya yönelik rehberlik sağlar.  

## <a name="general-best-practices"></a>Genel en iyi uygulamalar

Azure Işlevleri 'ni kullanarak sunucusuz çözümlerinizi oluşturma ve mimarinizi geliştirme konusunda en iyi yöntemler aşağıda verilmiştir.

### <a name="avoid-long-running-functions"></a>Uzun süre çalışan işlevlerden kaçının

Büyük ve uzun süre çalışan işlevler, beklenmeyen zaman aşımı sorunlarına neden olabilir. Belirli bir barındırma planının zaman aşımları hakkında daha fazla bilgi edinmek için bkz. [işlev uygulaması zaman aşımı süresi](functions-scale.md#timeout). 

Birçok Node. js bağımlılığı nedeniyle bir işlev büyük olabilir. Bağımlılıkları içeri aktarmak, beklenmedik zaman aşımları ile sonuçlanan daha fazla yükleme süresi de oluşmasına neden olabilir. Bağımlılıklar hem açık hem de örtük olarak yüklenir. Kodunuz tarafından yüklenen tek bir modül kendi ek modüllerini yükleyebilir. 

Mümkün olduğunda, büyük işlevleri birlikte çalışarak daha küçük işlev kümelerine yeniden düzenleyin ve yanıtları hızlı bir şekilde geri döndürün. Örneğin, bir Web kancası veya HTTP tetikleyici işlevi belirli bir zaman sınırı içinde bir bildirim yanıtı gerektirebilir; Web kancalarının anında yanıt gerektirmesi için yaygındır. HTTP tetikleyici yükünü bir kuyruk tetikleyicisi işlevi tarafından işlenmek üzere bir kuyruğa geçirebilirsiniz. Bu yaklaşım, gerçek işi ertelemenizi ve anında yanıt döndürmenizi sağlar.


### <a name="cross-function-communication"></a>Çapraz işlev iletişimi

[Dayanıklı işlevler](durable/durable-functions-overview.md) ve [Azure Logic Apps](../logic-apps/logic-apps-overview.md) , durum geçişlerini ve birden çok işlev arasındaki iletişimi yönetmek için oluşturulmuştur.

Birden çok işlevle tümleştirilecek Dayanıklı İşlevler veya Logic Apps kullanmıyorsanız, bu işlemler arası iletişim için depolama kuyruklarını kullanmak en iyisidir. Ana neden, depolama sıralarının diğer depolama seçeneklerinden daha kolay sağlanması ve sağlanması çok daha kolaydır. 

Depolama sırasındaki tek tek mesajlar boyut olarak 64 KB ile sınırlıdır. İşlevler arasında daha büyük iletiler geçirmeniz gerekiyorsa, Standart katmanda 256 KB 'ye kadar olan ileti boyutlarını desteklemek için bir Azure Service Bus kuyruğu ve Premium katmanında en fazla 1 MB kullanılabilir.

Service Bus konular, işlemeden önce ileti filtrelemesini gerekli kıldıysanız yararlıdır.

Olay Hub 'ları, yüksek hacimli iletişimleri desteklemek için faydalıdır.


### <a name="write-functions-to-be-stateless"></a>İşlevleri durum bilgisiz olacak şekilde yaz 

İşlevler, mümkünse durum bilgisiz ve ıdempotent olmalıdır. Gerekli durum bilgilerini verileriniz ile ilişkilendirin. Örneğin, işlenmekte olan bir sıra ilişkili bir `state` üyesine sahip olabilir. İşlev durum bilgisiz olmaya devam ederken bir işlev bu duruma göre bir sırayı işleyebilir. 

Idempotent işlevleri, özellikle Zamanlayıcı tetikleyicilerle önerilir. Örneğin, her gün bir kez çalışması gereken bir şeydir varsa, aynı sonuçlarla her zaman çalışacak şekilde bu şekilde yazın. Belirli bir gün için iş olmadığında işlev çıkabilir. Ayrıca, önceki bir çalıştırmanın tamamlanmadıysa, sonraki çalıştırmanın kaldığınız yerden devam etmesi gerekir.


### <a name="write-defensive-functions"></a>Savunma işlevlerini yaz

İşlevinizin istediğiniz zaman bir özel durumla karşılaşdığını varsayın. Bir sonraki yürütme sırasında işlevlerinizi önceki bir başarısızlık noktasından devam etme özelliğiyle tasarlayın. Aşağıdaki eylemleri gerektiren bir senaryo düşünün:

1. Veritabanında 10.000 satırı sorgulayın.
2. Bu satırların her biri için çizgi üzerinde daha fazla işlem yapmak üzere bir kuyruk iletisi oluşturun.
 
Sisteminizin ne kadar karmaşıkdığına bağlı olarak, bu durum, hatalı, ağ kesintileri veya kota limitlerinin ulaştığı, vb. gibi davranan aşağı akış Hizmetleri olabilir. Tüm bunlar, işlevinizi dilediğiniz zaman etkileyebilir. İşlevlerinizi hazırlanmakta olacak şekilde tasarlamanız gerekir.

İşlenmek üzere bu öğelerin 5.000 ' i bir sıraya ekledikten sonra bir hata oluşursa, kodunuz nasıl tepki veriyor? Tamamladığınız bir küme içindeki öğeleri izleyin. Aksi halde, bir dahaki sefer yeniden ekleyebilirsiniz. Bu çift ekleme iş akışınız üzerinde ciddi bir etkiye sahip olabilir, bu nedenle [işlevlerinizi ıdempotent yapın](functions-idempotent.md). 

Bir kuyruk öğesi zaten işlendiyse, işlevinizin işlem dışı çalışmasına izin verin.

Azure Işlevleri platformunda kullandığınız bileşenler için zaten sağlanmış olan savunma ölçülerinin avantajlarından yararlanın. Örneğin, bkz. [Azure depolama kuyruğu Tetikleyicileri ve bağlamaları](functions-bindings-storage-queue.md#trigger---poison-messages)belgelerinde **Poison Queue iletilerini işleme** . 

## <a name="scalability-best-practices"></a>Ölçeklenebilirlik en iyi uygulamaları

İşlev uygulamanızın nasıl ölçeklenmesi etkileyebilecek bir dizi etken vardır. Ayrıntılar, [işlev ölçeklendirmeyle](functions-scale.md)ilgili belgelerde verilmiştir.  Aşağıda, bir işlev uygulamasının en iyi şekilde ölçeklenebilirliğini sağlamak için bazı en iyi yöntemler verilmiştir.

### <a name="share-and-manage-connections"></a>Bağlantıları paylaşma ve yönetme

Mümkün olduğunda dış kaynaklarla bağlantıları yeniden kullanın.  Bkz. [Azure işlevlerinde bağlantıları yönetme](./manage-connections.md).

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Aynı işlev uygulamasında test ve üretim kodunu karıştırmayın

İşlev uygulaması içindeki işlevler kaynakları paylaşır. Örneğin, bellek paylaşılır. Üretimde bir işlev uygulaması kullanıyorsanız, bu uygulamaya test ile ilgili işlevler ve kaynaklar eklemeyin. Üretim kodu yürütme sırasında beklenmeyen yüke neden olabilir.

Üretim işlevi uygulamalarınızda ne yüklediklerinizi dikkatli olun. Uygulamadaki her bir işlevde bellek ortalaması.

Çoklu .NET işlevlerinde başvurulan paylaşılan bir derlemeniz varsa, bunu ortak bir paylaşılan klasöre koyun. Aksi halde, işlevler arasında farklı şekilde davranan aynı ikilinin yanlışlıkla birden çok sürümünü dağıtabilirsiniz.

Üretim kodunda, olumsuz bir performans etkisi olan ayrıntılı günlük kullanmayın.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Zaman uyumsuz kod kullan ancak çağrı engellemeyi önleyin

Zaman uyumsuz programlama önerilen en iyi uygulamadır. Ancak, her zaman `Result` özelliğine başvurmaktan kaçının veya bir `Task` örneğine `Wait` metodunu çağırarak. Bu yaklaşım iş parçacığı tükenmesine yol açabilir.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>Mümkün olduğunda toplu iş içinde ileti alma

Olay Hub 'ı gibi bazı Tetikleyiciler tek bir çağrıdan bir toplu ileti almayı sağlar.  Toplu işlem iletilerinin çok daha iyi performansı vardır.  `host.json` dosyasındaki en büyük toplu iş boyutunu [Host. JSON başvuru belgelerinde](functions-host-json.md) açıklandığı gibi yapılandırabilirsiniz.

İşlevler C# için türü türü kesin belirlenmiş bir dizi olarak değiştirebilirsiniz.  Örneğin, `EventData sensorEvent` değil Yöntem imzası `EventData[] sensorEvent`olabilir.  Diğer diller için, [burada gösterildiği gibi](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10), toplu işi etkinleştirmek için `function.json` kardinalite özelliğini `many` olarak ayarlamanız gerekir.

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Eşzamanlılık daha iyi işlemek için konak davranışlarını yapılandırın

İşlev uygulamasındaki `host.json` dosyası, ana bilgisayar çalışma zamanı ve tetikleyici davranışları yapılandırmasına izin verir.  İşleme davranışlarına ek olarak, bir dizi tetikleyici için eşzamanlılık yönetebilirsiniz. Genellikle bu seçeneklerdeki değerlerin ayarlanması, her bir örneğin, çağrılan işlevlerin taleplerine uygun şekilde ölçeklendirilmesine yardımcı olabilir.

Host. JSON dosyasındaki ayarlar, işlevin *tek bir örneği* içinde, uygulamadaki tüm işlevler arasında geçerlidir. Örneğin, iki HTTP işlevi olan bir işlev uygulamanız varsa ve [`maxConcurrentRequests`](functions-bindings-http-webhook.md#hostjson-settings) istekleri 25 olarak AYARLANDıYSA, http tetikleyicisine yönelik bir istek paylaşılan 25 eşzamanlı istek için doğru sayılır.  Bu işlev uygulaması 10 örneğe ölçeklenirse, iki işlev 250 eşzamanlı istek (10 örnek * örnek başına 25 eşzamanlı istek) etkin bir şekilde izin verir. 

Diğer konak yapılandırma seçenekleri [Host. JSON yapılandırma makalesinde](functions-host-json.md)bulunur.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Azure Işlevlerinde bağlantıları yönetme](manage-connections.md)
* [En iyi Azure App Service uygulamalar](../app-service/app-service-best-practices.md)
