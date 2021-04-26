---
title: Kısıtlama sorunlarını giderme veya ' 429-çok sayıda istek hatası
description: Azure Logic Apps azaltma sorunlarını giderme veya ' HTTP 429 çok fazla istek ' hatası oluşturma
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: ea153b1927a337be29c2eb69e2417cc250abf5e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94366062"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Azure Logic Apps kısıtlama sorunlarını giderme (429-"çok fazla istek" hata)

[Azure Logic Apps](../logic-apps/logic-apps-overview.md), mantıksal uygulamanız, azaltma sırasında ["http 429 çok fazla istek" hatası](https://developer.mozilla.org/docs/Web/HTTP/Status/429) döndürür. bu durum, istek sayısı hedefin belirli bir süre boyunca işleyebileceği oranı aştığında meydana gelir. Azaltma, Gecikmeli veri işleme, düşük performans hızı ve belirtilen yeniden deneme ilkesini aşma gibi hatalar gibi sorunlar oluşturabilir.

![SQL Server bağlayıcısında daraltma](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Mantıksal uygulamanızın deneyimolabileceği bazı yaygın azaltma türleri şunlardır:

* [Mantıksal uygulama](#logic-app-throttling)
* [Bağlayıcı](#connector-throttling)
* [Hedef hizmet veya sistem](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Mantıksal uygulama azaltma

Azure Logic Apps hizmetinin kendi [verimlilik limitleri](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)vardır. Bu nedenle, mantıksal uygulamanız bu sınırları aşarsa, mantıksal uygulama kaynağınız yalnızca belirli bir örneği değil veya Çalıştır olarak kısıtlanacaktır.

Daraltma olaylarını bu düzeyde bulmak için, Azure portal mantıksal uygulamanızın **ölçüm** bölmesini denetleyin.

1. [Azure Portal](https://portal.azure.com)mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

1. Mantıksal uygulama menüsünde, **izleme** altında **ölçümler**' i seçin.

1. **Grafik başlığı** altında, mevcut bir ölçüm eklemek Için **ölçüm Ekle** ' yi seçin.

1. İlk ölçüm çubuğunda, **ölçüm** listesinden, **eylem kısıtlanmış olaylar**' ı seçin. İkinci ölçüm çubuğunda, **ölçüm** listesinden, **Kısıtlanmış Olayları Tetikle**' yi seçin.

Bu düzeyde azaltmayı işlemek için şu seçeneklere sahipsiniz:

* Aynı anda çalışabilecek mantıksal uygulama örneklerinin sayısını sınırlayın.

  Varsayılan olarak, mantıksal uygulamanızın Tetikleme koşulu aynı anda birden çok kez karşılanırsa, mantıksal uygulamanız için birden çok tetikleyici örneği aynı anda veya *paralel olarak* çalışır. Bu davranış, her tetikleyici örneğinin, önceki iş akışı örneği çalışmayı bitmeden önce tetiklendiği anlamına gelir.

  Eşzamanlı olarak çalışabilecek tetikleyici örneklerinin varsayılan sayısı [sınırsız](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)olsa da, bu sayıyı [tetikleyicinin eşzamanlılık ayarını açarak](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)sınırlayabilir ve gerekirse varsayılan değer dışında bir sınır seçin.

* Yüksek verimlilik modunu etkinleştirin.

  Mantıksal uygulama [, 5 dakikalık bir toplama aralığı boyunca çalışabilen eylem sayısı için varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)sahiptir. Bu sınırı en fazla eylem sayısına yükseltmek için mantıksal uygulamanızda [yüksek verimlilik modunu](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode) açın.

* Tetiklerde dizi ayırmayı devre dışı bırakma ("bölme açık") davranışı.

  Bir tetikleyici kalan iş akışı eylemlerinin işlemek için bir dizi döndürürse, tetikleyicinin [ **bölünmesi** ayarı](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) dizi öğelerini böler ve her bir dizi öğesi için bir iş akışı örneği başlatır; bu işlem, birden çok eş zamanlı olarak [ **ayırma** sınırına](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)kadar çalışır. Azaltmayı denetlemek için, **bölme** davranışını devre dışı bırakın ve mantıksal uygulamanızın, her çağrı için tek bir öğeyi işlemek yerine tek bir çağrı ile tüm diziyi işlemesini sağlayabilirsiniz.

* Eylemleri daha küçük mantık uygulamalarına yeniden düzenleyin.

  Daha önce belirtildiği gibi, bir mantıksal uygulama [5 dakikalık bir dönemde çalışabilen varsayılan eylem sayısıyla](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)sınırlıdır. [Yüksek verimlilik modunu](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode)etkinleştirerek bu sınırı artırabilseniz de, her bir mantıksal uygulamada çalışan eylemlerin sayısı sınırın altında kalacak şekilde mantıksal uygulamanızın eylemlerini daha küçük Logic Apps 'e bölmek isteyip istemediğinizi de düşünebilirsiniz. Böylece, tek bir mantıksal uygulama kaynağı üzerindeki yükü azaltır ve yük birden çok Logic Apps üzerinden dağıtılır. Bu çözüm, büyük veri kümelerini işleyen veya çok sayıda eşzamanlı çalışan eylemi, döngü yinelemelerini veya her döngü yinelemesindeki eylemleri eylem yürütme sınırını aşacak şekilde gerçekleştiren eylemler için daha iyi çalışır.

  Örneğin, bu mantıksal uygulama tüm işleri bir SQL Server veritabanından tabloları almak ve her tablodan satırları almak için yapar. Her döngü Için, **satırları al** eyleminin her tablo için satırları döndürmesi **için** her tablo üzerinden eşzamanlı olarak yinelenir. Bu tablolardaki veri miktarına bağlı olarak, bu eylemler eylem yürütmelerinin sınırını aşabilir.

  ![Mantıksal uygulama "önce" yeniden düzenleme](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  Yeniden düzenleme yapıldıktan sonra mantıksal uygulama artık bir üst ve alt mantıksal uygulamadır. Üst öğe SQL Server tabloları alır ve sonra satırları almak için her tablo için bir alt mantıksal uygulamayı çağırır:

  ![Tek bir eylem için mantıksal uygulama oluşturma](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  Her tablo için satırları almak üzere üst mantıksal uygulama tarafından çağrılan alt mantıksal uygulama aşağıda verilmiştir:

  ![İkinci bir eylem için başka bir mantıksal uygulama oluşturma](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Bağlayıcı azaltma

Her bağlayıcının kendi azaltma sınırları vardır ve bu, bağlayıcının teknik başvuru sayfasında bulabilirsiniz. Örneğin, [Azure Service Bus Bağlayıcısı](/connectors/servicebus/) , dakikada 6.000 çağrıya kadar izin veren bir azaltma sınırına sahiptir ve SQL Server bağlayıcı, [işlem türüne göre farklılık gösteren azaltma sınırlarını](/connectors/sql/)içerir.

HTTP gibi bazı Tetikleyiciler ve Eylemler, özel durum işlemeyi uygulamak için [yeniden deneme ilkesi sınırlarına](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) göre özelleştirebileceğiniz bir ["yeniden deneme ilkesi"](../logic-apps/logic-apps-exception-handling.md#retry-policies) vardır. Bu ilke, bir tetikleyicinin veya eylemin, özgün istek başarısız olduğunda ya da zaman aşımına uğradığında ve 408, 429 ya da 5xx yanıtı ile sonuçlanıp bir isteği ne sıklıkta yeniden deneyeceğini belirtir. Bu nedenle, daraltma başladığında ve 429 hatası döndürdüğünde, Logic Apps yeniden deneme ilkesi desteklenir.

Bir tetikleyicinin veya eylemin yeniden deneme ilkelerini destekleyip desteklemediğini öğrenmek için tetikleyiciyi veya eylemin ayarlarını kontrol edin. Bir tetikleyiciyi veya eylemin yeniden deneme girişimlerini görüntülemek için, mantıksal uygulamanızın çalışma geçmişi ' ne gidin, gözden geçirmek istediğiniz çalıştırmayı seçin ve girişler, çıktılar ve tüm denemeler hakkındaki ayrıntıları görüntülemek için bu tetikleyiciyi veya eylemi genişletin, örneğin:

![Eylemin çalışma geçmişi, yeniden denemeler, girişler ve çıkışları görüntüleme](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

Yeniden deneme geçmişi hata bilgilerini sağlasa da, bağlayıcı azaltma ve [hedef azaltma](#destination-throttling)arasında ayrım fark edebilirsiniz. Bu durumda, kaynağı tanımlamak için Yanıtın ayrıntılarını gözden geçirmeniz veya bazı azaltma aralığı hesaplamaları yapmanız gerekebilir.

Küresel, çok kiracılı Azure Logic Apps hizmetindeki Logic Apps için, kısıtlama *bağlantı* düzeyinde gerçekleşir. Bu nedenle, örneğin, bir [tümleştirme hizmeti ortamında (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)çalışan Logic Apps için, genel, çok kiracılı Logic Apps hizmetinde ÇALıŞTıRıLDıKLARıNDAN Ise bağlantıları için azaltma devam etmektedir. Ancak, Ise bağlayıcıları tarafından oluşturulan ıSE bağlantıları, ıSE 'de çalıştıkları için kısıtlanmıyor.

Bu düzeyde azaltmayı işlemek için şu seçeneklere sahipsiniz:

* Mantıksal uygulamanın verileri işlenmek üzere bölümlemeleri için tek bir eylem için birden çok bağlantı ayarlayın.

  Bu seçenek için, aynı kimlik bilgilerini kullanarak bir eylemin isteklerini birden çok bağlantı üzerinden aynı hedefe bölerek iş yükünü dağıtabilir.

  Örneğin, mantıksal uygulamanızın tabloları bir SQL Server veritabanından aldığından ve sonra her tablodan satırları aldığından emin olmanız gerekir. İşlemek istediğiniz satır sayısına göre, **her döngü için** birden çok bağlantı ve toplam satır sayısını işleme için daha küçük kümelere bölmek üzere birden çok bağlantı kullanabilirsiniz. Bu senaryo, toplam satır sayısını yarı olarak ayırmak için **her döngü için** iki kullanır. **Her** döngüden ilki ilk yarısı alan bir ifade kullanır. **Her** döngünün diğeri ikinci yarısı alan farklı bir ifade kullanır, örneğin:<p>

    * İfade 1: `take()` işlev bir koleksiyonun önünü alır. Daha fazla bilgi için [ **`take()`** işlevine](workflow-definition-language-functions-reference.md#take)bakın.

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * İfade 2: `skip()` işlev bir koleksiyonun önünü kaldırır ve diğer tüm öğeleri döndürür. Daha fazla bilgi için [ **`skip()`** işlevine](workflow-definition-language-functions-reference.md#skip)bakın.

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Aşağıda, bu ifadeleri nasıl kullanabileceğinizi gösteren bir görsel örnek verilmiştir:

    ![Tek bir eylem için birden çok bağlantı oluşturma ve kullanma](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Her eylem için farklı bir bağlantı ayarlayın.

  Bu seçenek için, eylemler aynı hizmete veya sisteme bağlandığında ve aynı kimlik bilgilerini kullanırken bile, her eylemin isteklerini kendi bağlantıları üzerinden yayarak iş yükünü dağıtabilir olup olmayacağını göz önünde bulundurun.

  Örneğin, mantıksal uygulamanızın tabloları bir SQL Server veritabanından aldığından ve her bir tablodaki her satırı aldığından emin olmanız gerekir. Tabloların tek bir bağlantı kullanmasını sağlamak, ancak her satırı başka bir bağlantı kullanması durumunda ayrı bağlantılar kullanabilirsiniz.

  ![Her eylem için farklı bir bağlantı oluşturun ve kullanın](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* ["For each" döngüsünde](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)eşzamanlılık veya paralellik ' i değiştirin.

  Varsayılan olarak, "for each" döngüsü yinelemeleri [eşzamanlılık sınırına](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)kadar aynı zamanda çalışır. "For each" döngüsü içinde Daraltılan bir bağlayıcınız varsa, paralel olarak çalışan döngü yinelemesi sayısını azaltabilirsiniz. Daha fazla bilgi için şu konulara bakın:
  
  * ["For each" döngüleri-eşzamanlılık değiştirin veya sırayla çalıştırın](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [İş akışı tanımı dil şeması-her döngü Için](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [İş akışı tanımı dil şeması-"for each" döngüsü eşzamanlılık](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [İş akışı Tanım Dili şeması-"for each" döngülerini sırayla çalıştır](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>Hedef hizmet veya sistem azaltma

Bağlayıcının kendi azaltma sınırları olsa da, bağlayıcı tarafından çağrılan hedef hizmet veya sistem kısıtlama sınırlarına de sahip olabilir. Örneğin, Microsoft Exchange Server 'daki bazı API 'Ler Office 365 Outlook bağlayıcısından daha sıkı azaltma sınırlarına sahiptir.

Varsayılan olarak, bir mantıksal uygulamanın örnekleri ve bu örneklerin içindeki tüm döngüler veya dallar *paralel olarak* çalışır. Bu davranış, birden çok örneğinin aynı anda aynı uç noktayı çağırabileceği anlamına gelir. Her örnek diğer öğesinin mevcut olup olmadığını bilmez, bu yüzden yeniden denemeye yönelik girişimler, birden çok çağrının aynı anda çalışmaya çalışacağı [yarış koşullarını](https://en.wikipedia.org/wiki/Race_condition) oluşturabilir, ancak başarılı olmak için, azaltma işleminin gerçekleşmesi için bu çağrıların hedef hizmette veya sistemde gelmesi gerekir.

Örneğin, 100 öğe içeren bir diziye sahip olduğunuzu varsayalım. Dizi boyunca yinelemek için "for each" döngüsünü kullanırsınız ve paralel yineleme sayısını 20 ' ye veya [geçerli varsayılan sınıra](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)sınırlayabilmeniz için döngünün eşzamanlılık denetimini açmanız gerekir. Bu döngünün içinde, bir eylem dizideki bir öğeyi bir SQL Server veritabanına ekler ve bu da saniyede yalnızca 15 çağrıya izin verir. Bu senaryo, yeniden deneme biriktirme listesi oluşturulduğundan ve hiç çalıştırılmadığından dolayı azaltma sorununa neden olur.

Bu tablo, eylemin yeniden deneme aralığı 1 saniye olduğunda döngüde ne olacağı için zaman çizelgesini açıklar:

| Zaman noktası | Çalışan eylem sayısı | Başarısız olan eylem sayısı | Bekleyen yeniden deneme sayısı |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 saniye | 20 ekleme | 5 başarısız, SQL sınırı nedeniyle | 5 yeniden deneme |
| T + 0,5 saniye | önceki 5 yeniden deneme nedeniyle 15 ekleme bekliyor | Önceki SQL sınırının diğer 0,5 saniye içinde hala yürürlükte olması nedeniyle, tüm 15 başarısız olur | 20 yeniden deneme <br>(önceki 5 + 15 yeni) |
| T + 1 saniye | 20 ekleme | 5 hata ve önceki 20 yeniden deneme, SQL sınırı nedeniyle | 25 yeniden deneme (önceki 20 + 5 yeni)
|||||

Bu düzeyde azaltmayı işlemek için şu seçeneklere sahipsiniz:

* Her birinin tek bir işlemi işleyeceği mantıksal uygulamalar oluşturun.

  * Bu bölümde örnek SQL Server senaryoya devam etmek için, dizi öğelerini bir sıraya ( [Azure Service Bus kuyruğu](../connectors/connectors-create-api-servicebus.md)gibi) yerleştiren bir mantıksal uygulama oluşturabilirsiniz. Daha sonra bu kuyruktaki her öğe için yalnızca ekleme işlemini gerçekleştiren başka bir mantıksal uygulama oluşturursunuz. Bu şekilde, yalnızca bir Logic App örneği, ekleme işlemini tamamlar ve sıradaki bir sonraki öğeye devam eder, ancak örnek 429 hata alıyor ancak üretken olmayan yeniden denemeleri denenmez.

  * Her eylem için bir alt veya iç içe mantıksal uygulama çağıran bir üst mantıksal uygulama oluşturun. Üst öğenin üst sonucuna bağlı olarak farklı alt uygulamaları çağırması gerekiyorsa, hangi alt uygulamanın çağrılacağını belirleyen bir koşul eylemi veya geçiş eylemi kullanabilirsiniz. Bu model, çağrı veya işlem sayısını azaltmanıza yardımcı olabilir.

    Örneğin, her biri "başarı" veya "hata" gibi belirli bir konu için e-posta hesabınızı her dakikada denetleyen bir yoklama tetikleyicisi olan iki Logic App 'e sahip olduğunuzu varsayalım. Bu kurulum, saat başına 120 çağrı sonucu olarak sonuçlanır. Bunun yerine, her dakikayı yoklayan, ancak konunun "başarılı" veya "hata" olup olmadığını temel alan bir alt mantıksal uygulama çağıran tek bir üst mantıksal uygulama oluşturursanız, bu durumda yarı veya 60 için yoklama denetimlerinin sayısını keserseniz.

* Toplu işlemeyi ayarlayın.

  Hedef hizmet toplu işlemleri destekliyorsa, grupları veya toplu işteki öğeleri tek tek yerine işleyerek azaltmayı ele alabilirsiniz. Toplu işleme çözümünü uygulamak için bir "Batch alıcısı" mantıksal uygulaması ve bir "Batch sender" mantıksal uygulaması oluşturursunuz. Toplu gönderici, belirtilen ölçütleriniz karşılanana kadar iletileri veya öğeleri toplar ve ardından bu iletileri veya öğeleri tek bir grupta gönderir. Toplu iş alıcısı bu grubu kabul eder ve bu iletileri veya öğeleri işler. Daha fazla bilgi için bkz. [gruplardaki toplu işlem iletileri](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

* Yoklama sürümleri yerine Tetikleyiciler ve eylemler için Web kancası sürümlerini kullanın.

  Neden? Bir yoklama tetikleyicisi, hedef hizmeti veya sistemi belirli aralıklarla denetlemeye devam eder. Her saniye gibi çok sık kullanılan bir Aralık, kısıtlama sorunları oluşturabilir. Ancak, [http Web kancası](../connectors/connectors-native-webhook.md)gibi bir Web kancası tetikleyicisi veya eylemi, yalnızca hedef hizmet veya sisteme tek bir çağrı oluşturur; Bu, abonelik zamanında gerçekleşir ve hedefin yalnızca bir olay gerçekleştiğinde tetikleyiciyi veya eyleme bildirimde bulunduğunu bildirir. Bu şekilde, tetikleyici veya eylemin hedefi sürekli denetlemek zorunda değildir.
  
  Bu nedenle, hedef hizmet veya sistem Web kancalarını destekliyorsa veya Web kancası sürümüne sahip bir bağlayıcı sağlıyorsa, bu seçenek yoklama sürümünü kullanmaktan daha iyidir. Web kancası Tetikleyicileri ve eylemlerini belirlemek için, bu `ApiConnectionWebhook` tür olduğunu veya bir yinelenme belirtmemenizi gerektirdiklerini doğrulayın. Daha fazla bilgi için bkz. [Apiconnectionweb kancası tetikleyicisi](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) ve [apiconnectionweb kancası eylemi](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action).

## <a name="next-steps"></a>Sonraki adımlar

* [Logic Apps sınırları ve yapılandırması](../logic-apps/logic-apps-limits-and-config.md) hakkında daha fazla bilgi edinin
