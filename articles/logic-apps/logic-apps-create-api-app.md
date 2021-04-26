---
title: Azure Logic Apps için REST API 'Leri & Web API 'Leri oluşturma
description: API 'leri, hizmetlerinizi veya sistemlerinizi Azure Logic Apps içindeki Sistem tümleştirmeleri için çağırmak üzere Web API 'leri & REST API 'leri oluşturun
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/26/2017
ms.openlocfilehash: 3fe98160cc10eb3607b8309a9a263d63380dcfb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89073225"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Azure Logic Apps çağırabilmeniz için özel API 'Ler oluşturun

Azure Logic Apps, mantıksal uygulama iş akışlarında kullanabileceğiniz [yüzlerce bağlayıcı](../connectors/apis-list.md) sunmakla birlikte, bağlayıcı olarak kullanılamayan API 'leri, sistemleri ve Hizmetleri çağırmak isteyebilirsiniz. Logic Apps 'te kullanılacak eylemler ve Tetikleyiciler sağlayan kendi API 'lerinizi oluşturabilirsiniz. Mantıksal uygulama iş akışlarından çağırabilmeniz için kendi API 'lerinizi oluşturmak isteyebileceğiniz diğer nedenler şunlardır:

* Geçerli sistem tümleştirmenizi ve veri tümleştirme iş akışlarınızı genişletin.
* Müşterilerin profesyonel veya kişisel görevleri yönetmek için hizmetinizi kullanmasına yardımcı olun.
* Hizmetiniz için erişim, keşfedilebilirlik ve kullanım alanını genişletin.

Temel olarak bağlayıcılar, eklenebilir arabirimler için REST, belgeler için [Swagger meta veri biçimi](https://swagger.io/specification/) ve veri değişim BIÇIMI olarak JSON kullanan Web API 'lardır. Bağlayıcılar HTTP uç noktaları üzerinden iletişim kuran REST API 'Leri olduğundan, bağlayıcılar oluşturmak için .NET, Java, Python veya Node.js gibi herhangi bir dili kullanabilirsiniz. API 'lerinizi, API barındırma için en iyi, en kolay ve en ölçeklenebilir yollarla bir hizmet olarak platform (PaaS) sunan [Azure App Service](../app-service/overview.md)de barındırabilirsiniz. 

Özel API 'Lerin Logic Apps ile çalışması için API 'niz mantıksal uygulama iş akışlarında belirli görevleri gerçekleştiren [*Eylemler*](./logic-apps-overview.md#logic-app-concepts) sağlayabilir. API 'niz Ayrıca, yeni veriler veya bir olay belirtilen bir koşulu karşıladığında bir mantıksal uygulama iş akışı Başlatan bir [*tetikleyici*](./logic-apps-overview.md#logic-app-concepts) işlevi görür. Bu konu, API 'nizin sağlamasını istediğiniz davranışa bağlı olarak API 'inizdeki eylemleri ve Tetikleyicileri oluşturmak için izleyebileceğiniz ortak desenleri açıklar.

API 'lerinizi, yüksek düzeyde ölçeklenebilir ve kolay API barındırma sağlayan bir hizmet olarak platform (PaaS) teklifi olan [Azure App Service](../app-service/overview.md)barındırabilirsiniz.

> [!TIP] 
> API 'lerinizi Web Apps olarak dağıtabilir olsanız da, API 'lerinizi bulutta ve şirket içinde API 'Ler oluştururken, barındırdığınızda ve kullandığınızda işinizi daha kolay hale getirmek için API Apps olarak dağıtmayı göz önünde bulundurun. API 'lerinizin herhangi bir kodunu değiştirmeniz gerekmez; kodunuzu bir API uygulamasına dağıtmanız yeterlidir. Örneğin, bu dillerle oluşturulan API uygulamalarının nasıl oluşturulacağını öğrenin: 
> 
> * [ASP.net](../app-service/quickstart-dotnetcore.md). 
> * [Java](../app-service/quickstart-java.md)
> * [Node.js](../app-service/quickstart-nodejs.md)
> * [PHP](../app-service/quickstart-php.md)
> * [Python](../app-service/quickstart-python.md)
> * [Ruby](../app-service/quickstart-ruby.md)
>
> Logic Apps için derlenmiş API uygulama örnekleri için [Azure Logic Apps GitHub deposunu](https://github.com/logicappsio)ziyaret edin.

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Özel API 'Lerin özel bağlayıcılardan farkı nedir?

Özel API 'ler ve [özel bağlayıcılar](../logic-apps/custom-connector-overview.md) , eklenebilir ARABIRIMLER için REST, belgeler için [Swagger meta veri biçimi](https://swagger.io/specification/) ve VERI değişim biçimi olarak JSON kullanan Web API 'lerdedir. Bu API 'Ler ve bağlayıcılar HTTP uç noktaları üzerinden iletişim kuran REST API 'Leri olduğundan, özel API 'Ler ve bağlayıcılar oluşturmak için .NET, Java, Python veya Node.js gibi herhangi bir dili kullanabilirsiniz.

Özel API 'ler, bağlayıcılar olmayan API 'Leri çağırmasına ve HTTP + Swagger, Azure API Management veya App Services ile çağırabilmeniz için uç noktalar sağlamanıza olanak tanır. Özel Bağlayıcılar özel API 'Ler gibi çalışır, ancak aynı zamanda şu özniteliklere sahiptir:

* Azure 'da Logic Apps bağlayıcı kaynakları olarak kaydedilir.
* Logic Apps tasarımcısında Microsoft tarafından yönetilen bağlayıcılar yanında simgelerle birlikte görüntülenir.
* Yalnızca, mantıksal uygulamaların dağıtıldığı bölgede aynı Azure Active Directory kiracıya ve Azure aboneliğine sahip olan bağlayıcılar ve Logic App kullanıcıları tarafından kullanılabilir.

Ayrıca, Microsoft sertifikası için kayıtlı bağlayıcılar da aday ekleyebilirsiniz. Bu işlem, kayıtlı bağlayıcıların genel kullanım ölçütlerini karşıladığını doğrular ve bu bağlayıcıları Power otomatikleştir ve Microsoft Power Apps 'teki kullanıcılar için kullanılabilir hale getirir.

Özel Bağlayıcılar hakkında daha fazla bilgi için bkz. 

* [Özel bağlayıcılara genel bakış](../logic-apps/custom-connector-overview.md)
* [Web API 'Lerinden özel bağlayıcılar oluşturma](/connectors/custom-connectors/create-web-api-connector)
* [Azure Logic Apps özel bağlayıcıları kaydetme](/connectors/custom-connectors/)

## <a name="helpful-tools"></a>Faydalı araçlar

API 'nin aynı zamanda API 'nin işlemlerini ve parametrelerini açıklayan bir [Swagger belgesi](https://swagger.io/specification/) olduğunda, özel bir API, Logic Apps ile en iyi şekilde çalışmaktadır.
[Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)gibi birçok kitaplık, Swagger dosyasını sizin için otomatik olarak oluşturabilir. Ekran adları, özellik türleri vb. için Swagger dosyasına ek açıklama eklemek için [trex](https://github.com/nihaue/TRex) ' i de kullanarak Swagger dosyanızın Logic Apps ile düzgün bir şekilde çalışmasına olanak sağlayabilirsiniz.

<a name="actions"></a>

## <a name="action-patterns"></a>Eylem desenleri

Mantıksal uygulamaların görevleri gerçekleştirmesi için özel API 'niz [*eylem*](./logic-apps-overview.md#logic-app-concepts)sağlamalıdır. API 'inizdeki her işlem bir eyleme eşlenir. Temel eylem, HTTP isteklerini kabul eden ve HTTP yanıtlarını döndüren bir denetleyiciden yapılır. Örneğin, bir mantıksal uygulama Web uygulamanıza veya API uygulamanıza HTTP isteği gönderir. Daha sonra uygulamanız, mantıksal uygulamanın işleyebilmesine yönelik içerikle birlikte bir HTTP yanıtı döndürür.

Standart bir eylem için, API 'nize bir HTTP istek yöntemi yazabilir ve bu yöntemi Swagger dosyasında tanımlayabilirsiniz. Daha sonra API 'nizi doğrudan bir [http eylemiyle](../connectors/connectors-native-http.md) veya bir [http + Swagger](../connectors/connectors-native-http-swagger.md) eylemiyle çağırabilirsiniz. Varsayılan olarak, yanıtların [istek zaman aşımı sınırı](./logic-apps-limits-and-config.md)içinde döndürülmesi gerekir. 

![Standart eylem deseninin](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> API 'niz uzun süre çalışan görevleri bitirirken bir mantıksal uygulamanın beklemesini sağlamak için API 'niz, [zaman uyumsuz yoklama düzenine](#async-pattern) veya bu konuda açıklanan [zaman uyumsuz Web kancası düzenine](#webhook-actions) göre değişiklik yapabilir. Bu desenlerin farklı davranışlarını görselleştirmenize yardımcı olan bir benzerleme vurguladı için bir bakışta özel bir pasta siparişi verme sürecini düşünün. Yoklama deseninin, pastanın bir tane olup olmadığını denetlemek için 20 dakikada bir fırın çağrısı yaptığınız davranış yansıtılıdır. Web kancası stili, pastanın telefon numaranızı sorduğu, pastanın sizi arayabilmesi için sizi telefon numaranız istediğinde davranışını yansıtır.

Örnekler için [Logic Apps GitHub deposunu](https://github.com/logicappsio)ziyaret edin. Ayrıca, [Eylemler için kullanım ölçümü](logic-apps-pricing.md)hakkında daha fazla bilgi edinin.

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Yoklama eylemi düzeniyle uzun süre çalışan görevler gerçekleştirme

API 'nizin [istek zaman aşımı sınırından](./logic-apps-limits-and-config.md)daha uzun bir süre çalışabilecek görevler gerçekleştirmesini sağlamak için zaman uyumsuz yoklama modelini kullanabilirsiniz. Bu düzenin API 'niz ayrı bir iş parçacığında çalışır, ancak Logic Apps altyapısına etkin bir bağlantıyı saklayın. Bu şekilde, mantıksal uygulama zaman aşımına uğrar veya API çalışmaya başlamadan önce iş akışındaki bir sonraki adımla devam etmez.

Genel desenler aşağıda verilmiştir:

1. Altyapının API 'nizin isteği kabul ettiğini ve çalışmaya başlatıldığını bildiğinden emin olun.
2. Motor iş durumu için sonraki istekleri yaptığında, API 'niz görevi bitirdiğinde altyapıya izin verir.
3. Mantıksal uygulama iş akışının devam edebilmesi için ilgili verileri altyapıya döndürün.

<a name="bakery-polling-action"></a> Şimdi önceki fırın benzerleme vurguladı ' i yoklama düzenine uygulayın ve bir fırın çağırdığınızı ve teslim için özel bir pasta sipariş etdiğinizi düşünün. Pastayı oluşturma işlemi zaman alır ve Pastanda pasta üzerinde çalışırken telefonda beklemek istemezsiniz. Fırının siparişiniz onaylanır ve pastanın durumu için her 20 dakikada bir çağıralım. 20 dakika geçtikten sonra, fırının çağırın, ancak Pastanıza bir 20 dakika sonra çağrı yapmanız gerektiğini söyler. Bu geri ve ileriye doğru işlem, çağrılana kadar devam eder ve siz de siparişinizin size ait olduğunu ve pastanızı teslim ettiğini söyler. 

Şimdi bu yoklama düzenlerini geri eşleyelim. Bu, özel API 'nizi temsil ederken, pasta müşterisiyseniz Logic Apps altyapısını temsil eder. Motor API 'nizi bir istek ile çağırdığında API 'niz isteği onaylar ve altyapının iş durumunu kontrol edip zaman aralığı ile yanıt verir. Motor iş durumunu denetlemeye devam eder ve bu, iş akışına devam eden verileri mantıksal uygulamanıza gönderir. 

![Yoklama eylemi kriteri](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

API 'nizin, API 'nin perspektifinden açıklanacak özel adımlar aşağıda verilmiştir:

1. API 'niz çalışmaya başlamak için bir HTTP isteği aldığında, `202 ACCEPTED` `location` Bu adımda daha sonra açıklanan üst BILGIYLE bir http yanıtı döndürür. Bu yanıt, Logic Apps altyapısının API 'nizin isteği aldığı, istek yükünü kabul ettiğini (veri girişi) ve şimdi işleme olduğunu bilmesini sağlar. 
   
   `202 ACCEPTED`Yanıt şu üstbilgileri içermelidir:
   
   * *Gerekli*: `location` Logic Apps altyapısının API 'nizin iş durumunu kontrol ettiği bir URL 'nin mutlak yolunu belirten bir üst bilgi

   * *Isteğe bağlı*: `retry-after` altyapının `location` iş durumu için URL 'yi denetlemeden önce bekleyeceği saniye sayısını belirten bir üstbilgi. 

     Varsayılan olarak motor her 20 saniyede bir kontrol eder. Farklı bir Aralık belirtmek için, `retry-after` sonraki yoklamaya kadar üstbilgiyi ve saniye sayısını ekleyin.

2. Belirtilen süre geçtikten sonra Logic Apps altyapısı, `location` iş durumunu denetlemek için URL 'yi yoklar. API 'niz bu denetimleri gerçekleştirmelidir ve bu yanıtları döndürmelidir:
   
   * İş yapıldığında, `200 OK` Yanıt yükünün yanı da (bir sonraki adım için giriş) BIR http yanıtı döndürün.

   * İş hala işlenmeye devam ediyorsa, başka bir HTTP `202 ACCEPTED` yanıtı döndürün, ancak özgün Yanıtla aynı üst bilgilere sahip olur.

API 'niz bu kalıbı izlediğinde, iş durumunu denetlemeye devam etmek için mantıksal uygulama iş akışı tanımında herhangi bir şey yapmanız gerekmez. Motor bir HTTP `202 ACCEPTED` yanıtı ve geçerli bir `location` üst bilgi aldığında, altyapı zaman uyumsuz düzene uyar ve `location` API 'niz 202 olmayan bir yanıt döndürünceye kadar üstbilgiyi denetler.

> [!TIP]
> Örnek bir zaman uyumsuz model için [GitHub 'daki bu zaman uyumsuz denetleyici yanıtı örneğini](https://github.com/logicappsio/LogicAppsAsyncResponseSample)inceleyin.

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Web kancası eylem düzeniyle uzun süre çalışan görevler gerçekleştirme

Alternatif olarak, uzun süre çalışan görevler ve zaman uyumsuz işleme için Web kancası modelini kullanabilirsiniz. Bu düzende, mantıksal uygulama duraklamıştır ve iş akışına devam etmeden önce API 'nizden bir "geri çağırma" işleminin bitmesini bekleyin. Bu geri çağırma, bir olay gerçekleştiğinde bir URL 'ye ileti gönderen bir HTTP GÖNDERMESIDIR. 

<a name="bakery-webhook-action"></a> Şimdi önceki fırın benzerleme vurguladı 'i Web kancası düzenine uygulayın ve bir fırın çağırdığınızı ve teslim için özel bir pastanın sipariş edildiğini düşünün. Pastayı oluşturma işlemi zaman alır ve Pastanda pasta üzerinde çalışırken telefonda beklemek istemezsiniz. Bu durumda siparişiniz onaylanır, ancak bu kez, pasta tamamlandığında sizi arayabilmesi için telefon numaranızı bize verirsiniz. Bu kez, daha sonra siparişiniz için ne zaman hazırlandığını ve pastanızı teslim eder.

Bu Web kancası kalıbını geri eşliyoruz, fırın özel API 'nizi temsil ederken, pasta müşterisiyseniz Logic Apps altyapısını temsil etmektedir. Motor, API 'nizi bir istek ile çağırır ve "geri arama" URL 'sini içerir.
İş tamamlandığında, API, altyapıyı bilgilendirmek ve mantıksal uygulamanıza veri döndürmek için URL 'YI kullanır ve sonra iş akışına devam eder. 

Bu düzende, denetleyicinizde iki uç nokta ayarlayın: `subscribe` ve `unsubscribe`

*  `subscribe` uç noktası: yürütme, iş akışındaki API 'nin eylemine ulaştığında, Logic Apps altyapısı `subscribe` bitiş noktasını çağırır. Bu adım mantıksal uygulamanın API 'nizin depolayacağı bir geri çağırma URL 'SI oluşturmasına ve sonra iş tamamlandığında API 'nizden geri çağırma işlemini beklemesine neden olur. API 'niz daha sonra URL 'ye bir HTTP gönderimiyle geri çağrı yapar ve döndürülen içerik ve üst bilgileri mantıksal uygulamaya girdi olarak geçirir.

* `unsubscribe` uç nokta: mantıksal uygulama çalıştırması iptal edilirse, Logic Apps altyapısı `unsubscribe` bitiş noktasını çağırır. API 'niz daha sonra geri arama URL 'sini kaydedebilir ve gereken her türlü işlemi durdurabilir.

![Web kancası eylem kalıbı](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

Şu anda Logic App Designer, Swagger aracılığıyla Web kancası uç noktalarının bulunmasını desteklemez. Bu nedenle, bu düzende bir [ **Web kancası** eylemi](../connectors/connectors-native-webhook.md) eklemenız ve isteğiniz için URL, üst bilgiler ve gövde belirtmeniz gerekir. Ayrıca bkz. [Iş akışı eylemleri ve Tetikleyicileri](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Örnek bir Web kancası düzeniyle ilgili bu [Web kancası tetikleyicisi örneğini GitHub '](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)da inceleyin.

Diğer ipuçları ve notlar aşağıda verilmiştir:

* Geri çağırma URL 'sini geçirmek için, `@listCallbackUrl()` bir önceki alanda bulunan iş akışı işlevini gerekli şekilde kullanabilirsiniz.

* Hem mantıksal uygulamanın hem de abone olunan hizmetin sahibiyseniz, `unsubscribe` geri çağırma URL 'si çağrıldıktan sonra uç noktayı çağırmanız gerekmez. Aksi takdirde, Logic Apps çalışma zamanının `unsubscribe` daha fazla çağrının beklenmediği ve sunucu tarafında kaynak temizlemeye izin verilmesi için uç noktayı çağırması gerekir.

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Tetikleyici desenleri

Özel API 'niz, yeni veriler veya bir olay belirtilen bir koşulu karşıladığında bir mantıksal uygulama başlatan bir [*tetikleyici*](./logic-apps-overview.md#logic-app-concepts) olarak davranabilir. Bu tetikleyici, hizmet uç noktanıza yeni veriler veya olaylar için düzenli olarak denetleyebilir ya da bekleyip dinleyebilirler. Yeni veriler veya bir olay belirtilen koşulu karşılıyorsa, tetikleyici ateşlenir ve bu tetikleyiciyi dinleyen mantıksal uygulamayı başlatır. Logic Apps 'i bu şekilde başlatmak için, API 'niz [*yoklama tetikleyicisini*](#polling-triggers) veya [*Web kancası tetikleyici*](#webhook-triggers) modelini izleyebilir. Bu desenler, [yoklama eylemleri](#async-pattern) ve [Web kancası eylemleri](#webhook-actions)için karşılıklarına benzerdir. Ayrıca, [Tetikleyiciler için kullanım ölçümü](logic-apps-pricing.md)hakkında daha fazla bilgi edinin.

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Yoklama tetikleme düzeniyle düzenli olarak yeni verileri veya olayları denetleyin

*Yoklama tetikleyicisi* , bu konuda daha önce açıklanan [yoklama eylemine](#async-pattern) benzer şekilde davranır. Logic Apps altyapısı, yeni veriler veya olaylar için tetikleyici uç noktasını düzenli aralıklarla çağırır ve denetler. Motor, belirtilen koşullarınızı karşılayan yeni veriler veya bir olay bulursa, tetikleyici ateşlenir. Daha sonra, altyapı verileri girdi olarak işleyen bir mantıksal uygulama örneği oluşturur. 

![Yoklama tetikleyicisi kriteri](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Her yoklama isteği, mantıksal uygulama örneği oluşturulmasa bile, eylem yürütmesi olarak sayılır. Aynı verinin birden çok kez işlenmesini engellemek için, Tetikleyiciniz zaten okunmuş ve mantıksal uygulamaya geçirilen verileri temizlemelidir.

Aşağıda, API 'nin perspektifinden tanımlanan bir yoklama tetikleyicisi için özel adımlar verilmiştir:

| Yeni veri veya olay bulundu mu?  | API yanıtı | 
| ------------------------- | ------------ |
| Bulundu | `200 OK`Yanıt yüküne (sonraki adım için giriş) sahip BIR http durumu döndürün. <br/>Bu yanıt bir mantıksal uygulama örneği oluşturur ve iş akışını başlatır. | 
| Bulunamadı | `202 ACCEPTED` `location` Üst bilgi ve üst bilgi IÇEREN bir HTTP durumu döndürür `retry-after` . <br/>Tetikleyiciler için `location` üst bilgi, `triggerState` genellikle "timestamp" olan bir sorgu parametresi de içermelidir. API 'niz mantıksal uygulamanın tetiklendiği son saati izlemek için bu tanımlayıcıyı kullanabilir. | 
||| 

Örneğin, hizmetinizi yeni dosyalara düzenli olarak denetlemek için, bu davranışları içeren bir yoklama tetikleyicisi oluşturabilirsiniz:

| İstek şunları içerir `triggerState` ? | API yanıtı | 
| -------------------------------- | -------------| 
| No | Bir HTTP `202 ACCEPTED` durumu `location` `triggerState` ve geçerli saate ayarlanmış bir üstbilgi ve 15 saniyeye kadar olan bir üst bilgi döndürür `retry-after` . | 
| Yes | İçin ' den sonra eklenen dosyalar için hizmetinizi kontrol edin `DateTime` `triggerState` . | 
||| 

| Bulunan dosya sayısı | API yanıtı | 
| --------------------- | -------------| 
| Tek dosya | Bir HTTP `200 OK` durumu ve içerik yükü döndürün, `triggerState` `DateTime` döndürülen dosya için ' a güncelleştirin ve `retry-after` aralığı 15 saniyeye ayarlayın. | 
| Birden çok dosya | Tek seferde bir dosya ve bir HTTP durumu döndürün `200 OK` , güncelleştirin `triggerState` ve `retry-after` aralığı 0 saniyeye ayarlayın. </br>Bu adımlar, altyapının daha fazla verinin kullanılabildiğini ve altyapının, üstbilgideki URL 'den verileri hemen istemesi gerektiğini bilmesini sağlar `location` . | 
| Dosya yok | Bir HTTP `202 ACCEPTED` durumu döndürün, değiştirmeyin `triggerState` ve `retry-after` aralığı 15 saniyeye ayarlayın. | 
||| 

> [!TIP]
> Örnek bir yoklama tetikleyici deseninin, [GitHub 'daki bu yoklama tetikleme denetleyicisi örneğini](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs)gözden geçirin.

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Web kancası tetikleyici düzeniyle yeni verileri veya olayları bekleme ve dinleme

Web kancası tetikleyicisi, hizmet uç noktanıza yeni verileri veya olayları bekleyen ve dinleyen bir *gönderim tetikleyicisine* sahiptir. Yeni veriler veya bir olay belirtilen koşulu karşılıyorsa, tetikleyici ateşlenir ve bir mantıksal uygulama örneği oluşturur ve daha sonra verileri girdi olarak işler.
Web kancası Tetikleyicileri, bu konuda daha önce açıklanan [Web kancası eylemlerine](#webhook-actions) benzer bir şekilde davranır ve `subscribe` ve `unsubscribe` uç noktaları ile ayarlanır. 

* `subscribe` uç nokta: mantıksal uygulamanızda bir Web kancası tetikleyicisi eklediğinizde ve kaydettiğinizde, Logic Apps altyapısı `subscribe` uç noktasını çağırır. Bu adım mantıksal uygulamanın API 'nizin depoladığını belirten bir geri çağırma URL 'SI oluşturmasına neden olur. Yeni veriler veya belirtilen koşulu karşılayan bir olay olduğunda, API 'niz URL 'ye bir HTTP gönderimiyle geri çağrı yapılır. İçerik yükü ve üstbilgiler, Logic App 'e giriş olarak geçer.

* `unsubscribe` uç noktası: Web kancası tetikleyicisi veya tüm mantıksal uygulama silinirse, Logic Apps altyapısı `unsubscribe` uç noktasını çağırır. API 'niz daha sonra geri arama URL 'sini kaydedebilir ve gereken her türlü işlemi durdurabilir.

![Web kancası tetikleyici stili](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

Şu anda Logic App Designer, Swagger aracılığıyla Web kancası uç noktalarının bulunmasını desteklemez. Bu nedenle, bu düzende bir [ **Web kancası** tetikleyicisi](../connectors/connectors-native-webhook.md) eklemenız ve isteğiniz için URL, üst bilgiler ve gövde belirtmeniz gerekir. Ayrıca bkz. [Httpweb kancası tetikleyicisi](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Örnek bir Web kancası düzeniyle ilgili bu [Web kancası tetikleyici denetleyicisi örneğini GitHub '](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)da gözden geçirin.

Diğer ipuçları ve notlar aşağıda verilmiştir:

* Geri çağırma URL 'sini geçirmek için, `@listCallbackUrl()` bir önceki alanda bulunan iş akışı işlevini gerekli şekilde kullanabilirsiniz.

* Aynı verinin birden çok kez işlenmesini engellemek için, Tetikleyiciniz zaten okunmuş ve mantıksal uygulamaya geçirilen verileri temizlemelidir.

* Hem mantıksal uygulamanın hem de abone olunan hizmetin sahibiyseniz, `unsubscribe` geri çağırma URL 'si çağrıldıktan sonra uç noktayı çağırmanız gerekmez. Aksi takdirde, Logic Apps çalışma zamanının `unsubscribe` daha fazla çağrının beklenmediği ve sunucu tarafında kaynak temizlemeye izin verilmesi için uç noktayı çağırması gerekir.

## <a name="improve-security-for-calls-to-your-apis-from-logic-apps"></a>Mantıksal uygulamalardan API 'lerinize yapılan çağrılar için güvenliği geliştirme

Özel API 'lerinizi oluşturduktan sonra, mantıksal uygulamalardan güvenli bir şekilde çağırabilmeniz için API 'niz için kimlik doğrulaması ayarlayın. [Mantıksal uygulamalardan özel API 'lere yapılan çağrılar için Güvenliği geliştirmeyi](../logic-apps/logic-apps-custom-api-authentication.md)öğrenin.

## <a name="deploy-and-call-your-apis"></a>API 'lerinizi dağıtın ve çağırın

Kimlik doğrulamasını ayarladıktan sonra API 'niz için dağıtımı ayarlayın. [Logic Apps 'ten özel API 'leri dağıtmayı ve çağırmayı](../logic-apps/logic-apps-custom-api-host-deploy-call.md)öğrenin.

## <a name="publish-custom-apis-to-azure"></a>Azure 'da özel API 'Ler yayımlama

Özel API 'lerinizi Azure 'daki diğer Logic Apps kullanıcıları için kullanılabilir hale getirmek için güvenlik eklemeniz ve bunları mantıksal uygulama bağlayıcıları olarak kaydetmeniz gerekir. Daha fazla bilgi için bkz. [Özel bağlayıcılara genel bakış](../logic-apps/custom-connector-overview.md). 

Özel API 'lerinizi Logic Apps, Power otomatikleştirmede ve Microsoft Power Apps 'teki tüm kullanıcılar için kullanılabilir hale getirmek için, güvenlik eklemeniz, API 'lerinizi Logic App bağlayıcıları olarak kaydetmeniz ve [Microsoft Azure Sertifikalı program](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/)için bağlayıcılarınızı aday hale getirmeniz gerekir. 

## <a name="get-support"></a>Destek alın

* Özel API 'lerle ilgili belirli yardım için, ile iletişim kurun [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com) .

* Sorular için, [Azure Logic Apps Için Microsoft Q&soru sayfasını](/answers/topics/azure-logic-apps.html)ziyaret edin.

* Logic Apps’in geliştirilmesine yardımcı olmak için, [Logic Apps kullanıcı geri bildirim sitesinde](https://aka.ms/logicapps-wish) oy kullanın veya fikirlerinizi paylaşın. 

## <a name="next-steps"></a>Sonraki adımlar

* [Hataları ve özel durumları işleme](../logic-apps/logic-apps-exception-handling.md)
* [HTTP uç noktaları ile çağrı, tetikleyici veya iç içe mantıksal uygulamalar](../logic-apps/logic-apps-http-endpoint.md)
* [Eylemler ve Tetikleyiciler için kullanım ölçümü](../logic-apps/logic-apps-pricing.md)
