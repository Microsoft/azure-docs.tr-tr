---
title: Azure Işlevleri güvenilir olay işleme
description: Azure Işlevlerinde eksik olay hub 'ı iletilerinden kaçının
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: cshoe
ms.openlocfilehash: d38ef46abae12886fb04a30f5efc26992cde4443
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955542"
---
# <a name="azure-functions-reliable-event-processing"></a>Azure Işlevleri güvenilir olay işleme

Olay işleme, sunucusuz mimariyle ilişkili en yaygın senaryolardan biridir. Bu makalede, iletilerin kaybedilmesini önlemek için Azure Işlevleri ile güvenilir bir ileti işlemcisinin nasıl oluşturulacağı açıklanır.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Dağıtılmış sistemlerdeki olay akışlarıyla ilgili sorunlar

Saniyede 100 olaydan oluşan sabit bir hızda olay gönderen bir sistemi düşünün. Bu hızda, dakikalar içinde birden çok paralel Işlev örneğinin her saniye gelen 100 olayını tüketebileceği.

Ancak, aşağıdaki daha az en iyi koşullar mümkün değildir:

- Olay Yayımcısı bozuk bir olay gönderiyorsa ne olacak?
- Işlevleriniz işlenmemiş özel durumlarla karşılaştığında ne olacak?
- Bir aşağı akış sistemi çevrimdışı kalırsa ne olacak?

Uygulamanızın verimini korurken bu durumları nasıl işleyirsiniz?

Kuyruklar sayesinde, güvenilir mesajlaşma doğal olarak gelir. Bir Işlevler tetikleyicisi ile eşlendiğinde, işlev kuyruk iletisinde bir kilit oluşturur. İşlem başarısız olursa, başka bir örneğin işlemeyi yeniden denemesini sağlamak için kilit serbest bırakılır. İşlem tamamlandıktan sonra ileti başarıyla değerlendirilene veya bir Poison kuyruğuna eklenene kadar devam eder.

Tek bir kuyruk iletisi yeniden deneme çevriminde kalabilse de, diğer paralel yürütmeler kalan iletilerin sırasını kaldırma işlemine devam eder. Sonuç, genel üretilen işin büyük ölçüde bir hatalı iletiyle etkilenmemesinin sonucudur. Ancak, depolama kuyrukları sıralamayı garanti etmez ve Event Hubs için gereken yüksek verimlilik talepleri için en iyi duruma getirilmemektedir.

Buna karşılık, Azure Event Hubs bir kilitleme kavramı içermez. Yüksek aktarım hızı, birden çok tüketici grubu ve yeniden yürütme özelliği gibi özelliklere izin vermek için Event Hubs olaylar video oynatıcı gibi davranır. Olaylar, her bölüm için akıştaki tek bir noktadan okunurdur. İşaretçinizi bu konumdan ileri veya geri okuyabilirsiniz, ancak olayları işlemek için işaretçiyi taşımayı seçmeniz gerekir.

Bir akışta hata oluştuğunda, işaretçiyi aynı nokta içinde tutmaya karar verirseniz, işaretçi gelişmiş olana kadar olay işleme engellenir. Diğer bir deyişle, işaretçi tek bir olayı işlerken sorunlarla başa çıkmak üzere durdurulmuşsa, işlenmemiş olaylar çalışmaya başlar.

Azure Işlevleri, başarılı veya başarısız olursa olsun akışın işaretçisini ilerleerek kilitlenmeleri önler. İşaretçi ilerleme yaptığından, işlevlerinizin hatalarla uygun şekilde uğraşmanız gerekir.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Azure Işlevleri Event Hubs olaylarını nasıl kullanır?

Azure Işlevleri aşağıdaki adımlarla geçiş yaparken Olay Hub olaylarını kullanır:

1. Olay Hub 'ının her bölümü için Azure depolama 'da bir işaretçi oluşturulur ve kalıcı hale getirilir.
2. Yeni iletiler alındığında (varsayılan olarak bir toplu işte), ana bilgisayar işlevi ileti toplu işi ile tetiklemeye çalışır.
3. İşlev yürütmeyi (özel durum olmadan veya hariç) tamamlarsa, işaretçi ilerler ve bir denetim noktası depolama hesabına kaydedilir.
4. Koşullar işlev yürütmenin tamamlanmasını engelliyorsa, ana bilgisayar işaretçiyi ilerleyemez. İşaretçi gelişmiş değilse, daha sonra aynı iletileri işlemeye son bir denetim gerçekleştirir.
5. 2 – 4 arasındaki adımları yineleyin

Bu davranış birkaç önemli noktayı ortaya çıkarır:

- *İşlenmemiş özel durumlar iletileri kaybetmenize neden olabilir.* Bir özel durumla sonuçlanan yürütmeler, işaretçinin devam etmesine devam edecektir.
- *İşlevler, en az bir kez teslim garantisi verir.* Kodunuz ve bağımlı sistemleriniz [, aynı iletinin iki kez alınabilmesi için hesaba](./functions-idempotent.md)sahip olabilir.

## <a name="handling-exceptions"></a>Özel durumları işleme

Genel bir kural olarak, her işlev en yüksek kod düzeyinde bir [try/catch bloğu](./functions-bindings-error-pages.md) içermelidir. Özellikle, Event Hubs olaylarını kullanan tüm işlevler `catch` bloğuna sahip olmalıdır. Bu şekilde, bir özel durum ortaya çıktığında, catch bloğu bu hatayı işaretçi ilerlene kadar işler.

### <a name="retry-mechanisms-and-policies"></a>Yeniden deneme mekanizmaları ve ilkeleri

Bazı özel durumlar geçici olarak geçicidir ve bir işlem daha sonra tekrar denendiğinde yeniden görünmez. İlk adımın işlemi her zaman yeniden denemesi budur. Yeniden deneme oluşturma kurallarını kendiniz yazabilirsiniz, ancak bu çok sayıda araç kullanılabilir. Bu kitaplıkların kullanılması, işlem sırasının korunmasına yardımcı olabilecek güçlü yeniden deneme ilkeleri tanımlamanızı sağlar.

İşlevleriniz için hata işleme kitaplıklarını tanıtma hem temel hem de gelişmiş yeniden deneme ilkelerini tanımlamanızı sağlar. Örneğin, aşağıdaki kurallara göre gösterilen bir iş akışını izleyen bir ilke uygulayabilirsiniz:

- Üç kez bir ileti eklemeyi deneyin (büyük olasılıkla denemeler arasındaki gecikme süresi ile).
- Tüm yeniden denemeler için nihai sonuç bir hata ise, işleme akışta devam edebilmesi için bir kuyruğa ileti ekleyin.
- Daha sonra bozuk veya işlenmemiş iletiler daha sonra işlenir.

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) , uygulamalar için C# esnekliği ve geçici hata işleme kitaplığı örneğidir.

Önceden karmaşıklu C# sınıf kitaplıklarıyla çalışırken, [özel durum filtreleri](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-catch) işlenmeyen bir özel durum oluştuğunda kodu çalıştırmanızı sağlar.

Özel durum filtrelerinin nasıl kullanılacağını gösteren örnekler, [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) deposunda bulunabilir.

## <a name="non-exception-errors"></a>Özel durum olmayan hatalar

Bir hata mevcut olmadığında bile bazı sorunlar oluşur. Örneğin, yürütmenin ortasında oluşan bir hata düşünün. Bu durumda, bir işlev yürütmeyi tamammazsa, fark işaretçisi hiçbir şekilde ilerlemedi. İşaretçi ilermezse, başarısız bir yürütme sonrasında çalışan tüm örnekleri aynı iletileri okumaya devam eder. Bu durum "en az bir kez" garantisi sağlar.

Her iletinin en az bir kez işlendiği güvencesi, bazı iletilerin birden çok kez işlenebilir olabileceğini belirtir. İşlevinizin uygulamalarınızın bu olasılığa karşı farkında olması gerekir ve bu durumun üstesinden [gelmelidir.](./functions-idempotent.md)

## <a name="stop-and-restart-execution"></a>Yürütmeyi Durdur ve yeniden Başlat

Birkaç hata kabul edilebilir olsa da, uygulamanız önemli hatalardan karşılaşırsa ne olur? Sistem sağlıklı bir duruma ulaşıncaya kadar olaylar üzerinde tetiklenmenizi durdurmak isteyebilirsiniz. Fırsatın duraklamasını duraklatma, genellikle devre kesici düzeniyle elde edilir. Devre kesici stili, uygulamanızın olay işleminin "devresini kesmesine" ve daha sonra sürdürülmesine izin verir.

Bir olay işleminde devre kesici uygulamak için iki parça gereklidir:

- Devrenin durumunu izlemek ve izlemek için tüm örnekler genelinde paylaşılan durum
- Devre durumunu yönetebilen ana işlem (açık veya kapalı)

Uygulama ayrıntıları farklılık gösterebilir, ancak örnekler arasında durum paylaşmak için bir depolama mekanizmanız gerekir. Durumu Azure Storage 'da, Redsıs önbelleğinde veya bir işlevler koleksiyonu tarafından erişilebilen başka bir hesaba depolamayı seçebilirsiniz.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) veya [dayanıklı varlıklar](./durable/durable-functions-overview.md) , iş akışını ve devre durumunu yönetmek için doğal bir uyum sağlar. Diğer hizmetler de yalnızca çalışabilir, ancak bu örnek için Logic Apps kullanılır. Logic Apps 'i kullanarak, bir işlevin yürütmesini duraklatabilir ve yeniden başlatarak devre kesici düzeninin uygulanması için gerekli denetimi yapabilirsiniz.

### <a name="define-a-failure-threshold-across-instances"></a>Örnekler arasında bir hata eşiği tanımlayın

Aynı anda birden çok örnek işleme olayını hesaba eklemek için, devrenin durumunu izlemek üzere kalıcı paylaşılan dış durum gereklidir.

Uygulamayı seçebileceğiniz bir kural şunları uygulayabilir:

- Tüm örneklerde 30 saniye içinde 100 ' den fazla sorun oluşursa, devreyi bölün ve yeni iletilerde tetiklemeyi durdurun.

Uygulama ayrıntıları gereksinimlerinize göre farklılık gösterir, ancak genel olarak şu şekilde bir sistem oluşturabilirsiniz:

1. Bir depolama hesabına (Azure depolama, Redsıs, vb.) yönelik hataların günlüğünü tut
1. Yeni hata günlüğe kaydedildiğinde, eşiğin karşılanıp karşılanmadığını görmek için toplama sayısını inceleyin (örneğin, son 30 saniye içinde 100 ' den fazla).
1. Eşik karşılanıyorsa, sisteme devreyi bozmasını söyleyen Azure Event Grid bir olay gösterin.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>Azure Logic Apps ile devre durumunu yönetme

Aşağıdaki açıklamada bir Işlevler uygulamasının işlemesini durdurmak için bir Azure mantıksal uygulaması oluşturabileceğiniz bir yol vurgulanmıştır.

Azure Logic Apps, farklı hizmetlere yönelik yerleşik bağlayıcılarla, durum bilgisi bulunan özelliklerle birlikte gelir ve devre durumunu yönetmek için doğal bir seçimdir. Devre 'nın kesintiye uğraması gerektiğini algıladıktan sonra, aşağıdaki iş akışını uygulamak için bir mantıksal uygulama oluşturabilirsiniz:

1. Bir Event Grid iş akışı tetikleyin ve Azure Işlevini (Azure Kaynak Bağlayıcısı ile) durdurun
1. İş akışını yeniden başlatma seçeneği içeren bir bildirim e-postası gönderin

E-posta alıcısı, devrenin durumunu araştırabilir ve uygunsa, bildirim e-postasında bir bağlantı aracılığıyla devre dışı yeniden başlatılır. İş akışı işlevi yeniden başlattığı için iletiler son olay hub 'ı denetim noktasından işlenir.

Bu yaklaşımı kullanarak hiçbir ileti kaybedilmez, tüm iletiler sırayla işlenir ve gerekli olduğu sürece devreyi kesebilirsiniz.

## <a name="resources"></a>Kaynaklar

- [Güvenilir olay işleme örnekleri](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Azure Dayanıklı İşlevler devre kesici](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

- [Azure Işlevleri hata işleme](./functions-bindings-error-pages.md)
- [Event Grid kullanarak karşıya yüklenen görüntüleri yeniden boyutlandırmayı otomatikleştirme](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Azure Logic Apps ile tümleşen bir işlev oluşturma](./functions-twitter-email.md)
