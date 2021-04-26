---
title: Azure Logic Apps için yerleşik Tetikleyiciler ve eylemler
description: Uygulamaları, verileri, hizmetleri ve sistemleri tümleştiren otomatik iş akışları oluşturmak, iş akışlarını denetlemek ve Azure Logic Apps kullanarak verileri yönetmek için yerleşik Tetikleyiciler ve eylemler kullanın.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: 045d7391c9c3c2870efddc0aed4ae7590db938d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797185"
---
# <a name="built-in-triggers-and-actions-for-logic-apps"></a>Logic Apps için yerleşik Tetikleyiciler ve eylemler


[Yerleşik Tetikleyiciler ve eylemler](apis-list.md) , [iş akışınızın zamanlamasını ve yapısını denetlemenize](#control-workflow), [kendi kodunuzu çalıştırmanıza](#run-code-from-workflows), [verileri yönetmenize veya yönetmenize](#manage-or-manipulate-data)ve iş akışlarınızda diğer görevleri tamamlamanıza yönelik yollar sağlar. [Yönetilen bağlayıcılardan](managed.md)farklı olarak, birçok yerleşik işlem belirli bir hizmet, sistem veya protokole bağlı değildir. Örneğin, yinelenme tetikleyicisini kullanarak bir zamanlamaya göre neredeyse her iş akışını başlatabilirsiniz. Ya da iş akışınızın Istek tetikleyicisi kullanılarak çağrılana kadar beklemesini sağlayabilirsiniz. Tüm yerleşik işlemler, Logic Apps hizmetinde yerel olarak çalışır ve çoğu kullanılmadan önce bir bağlantı oluşturmanız gerekmez. 

Logic Apps, Azure Service Bus, Azure Işlevleri, SQL, AS2 vb. gibi daha az sayıda hizmet, sistem ve protokol için yerleşik işlemler de sağlar. Sayı ve Aralık, çok kiracılı bir mantıksal uygulama veya tek kiracılı mantıksal uygulama oluşturup oluşturmadığına göre farklılık gösterir. Birkaç durumda, hem yerleşik bir sürüm hem de bir yönetilen bağlayıcı sürümü mevcuttur. Çoğu durumda, yerleşik sürüm daha iyi performans, özellik, fiyatlandırma vb. sağlar. Örneğin, [AS2 protokolünü kullanarak B2B iletileri alışverişi](../logic-apps/logic-apps-enterprise-integration-as2.md)yapmak için, yalnızca (kullanım dışı) yönetilen bağlayıcı sürümünde kullanılabilen izleme özelliklerine ihtiyacınız yoksa yerleşik sürümü seçin.

Aşağıdaki listede, [yerleşik Tetikleyiciler ve eylemlerle](#understand-triggers-and-actions)gerçekleştirebileceğiniz görevlerden bazıları açıklanmaktadır:

- Özel ve gelişmiş zamanlamalar kullanarak iş akışlarını çalıştırın. Zamanlama hakkında daha fazla bilgi için, [Logic Apps bağlayıcıya genel bakış ' da yinelenme davranışı bölümüne](apis-list.md#recurrence-behavior)bakın.
- İş akışınızın yapısını, örneğin döngüleri ve koşulları kullanarak düzenleyin ve denetleyin.
- Değişkenler, tarihler, veri işlemleri, içerik dönüştürmeleri ve toplu işlemlerle çalışın.
- HTTP Tetikleyicileri ve eylemleri kullanarak diğer uç noktalarla iletişim kurun.
- İstekleri alın ve yanıtlayın.
- Kendi işlevlerinizi (Azure Işlevleri), Web uygulamalarınızı (Azure Uygulama Hizmetleri), API 'Leri (Azure API Management), istekleri alabilen diğer Logic Apps iş akışlarını ve benzerlerini çağırın.

## <a name="understand-triggers-and-actions"></a>Tetikleyicileri ve eylemleri anlama

Logic Apps aşağıdaki yerleşik Tetikleyicileri ve eylemleri sağlar:

:::row:::
    :::column:::
        [![Logic Apps 'de zamanlama simgesi][schedule-icon]][schedule-doc]
        \
        \
        [**Çizelgesini**][schedule-doc]
        \
        \
        [**Yinelenme**][schedule-recurrence-doc]: belirtilen tekrara göre iş akışını tetikler.
        \
        \
        [**Kayan pencere**][schedule-sliding-window-doc]: sürekli öbeklerdeki verileri işlemek için gereken bir iş akışını tetikler.
        \
        \
        [**Gecikme**][schedule-delay-doc]: iş akışınızı belirtilen süre için duraklatın.
        \
        \
        Şu [**kadar geciktir**][schedule-delay-until-doc]: iş akışınızı belirtilen tarih ve saate kadar duraklatın.
    :::column-end:::
    :::column:::
        [![Logic Apps toplu işlem simgesi][batch-icon]][batch-doc]
        \
        \
        [**İşlemini**][batch-doc]
        \
        \
        [**Batch iletileri**][batch-doc]: toplu olarak iletileri işleyen bir iş akışı tetikleyin.
        \
        \
        [**Batch 'e Ileti gönderme**][batch-doc]: Şu anda bir **Batch iletileri** tetikleyicisi ile başlayan mevcut bir iş akışını çağırın.
    :::column-end:::
    :::column:::
        [![Logic Apps HTTP simgesi][http-icon]][http-doc]
        \
        \
        [**HTTP**][http-doc]
        \
        \
        HTTP tetikleyicisi veya eylemini kullanarak bir HTTP ya da HTTPS uç noktası çağırın. 
        \
        \
        Bu diğer yerleşik HTTP tetikleyicilerini ve eylemlerini de kullanabilirsiniz: 
        - [HTTP + Swagger][http-swagger-doc]
        - [HTTP + Web kancası][http-webhook-doc]
    :::column-end:::
    :::column:::
        [![İstek simgesi][http-request-icon]][http-request-doc]
        \
        \
        [**İsteyen**][http-request-doc]
        \
        \
        [**BIR http isteği alındığında**][http-request-doc]: başka bir iş akışı, uygulama veya hizmetten istek için bekleyin. Bu tetikleyici bir zamanlamaya göre denetlenmesi veya yoklanması gerekmeden iş akışınızı çağrılabilir hale getirir.
        \
        \
        [**Yanıt**][http-request-doc]: aynı iş AKıŞıNDA **bir http isteği alındığında** tetiklenir tarafından alınan bir isteğe yanıt verir.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps Azure API Management simgesi][azure-api-management-icon]][azure-api-management-doc]
        \
        \
        [**Azure API Management**][azure-api-management-doc]
        \
        \
        [Azure API Management](../api-management/api-management-key-concepts.md)kullanarak tanımladığınız, yönettiğiniz ve yayımladığınız API 'lerde kendi tetikleyicinizi ve eylemlerinizi çağırın. <p><p>**Not**: [API Management için tüketim katmanı](../api-management/api-management-features.md)kullanılırken desteklenmez.
    :::column-end:::
    :::column:::
        [![Logic Apps 'de Azure Uygulama Hizmetleri simgesi][azure-app-services-icon]][azure-app-services-doc]
        \
        \
        [**Azure Uygulama Hizmetleri**][azure-app-services-doc]
        \
        \
        [Azure App Service](../app-service/overview.md)oluşturduğunuz ve üzerinde barındırmanıza yönelik uygulamaları (örneğin, API Apps ve Web Apps) çağırın.
        \
        \
        Swagger dahil edildiğinde, bu uygulamalar tarafından tanımlanan Tetikleyiciler ve Eylemler, Azure Logic Apps diğer birinci sınıf Tetikleyicileri ve eylemleri gibi görünür.
    :::column-end:::
    :::column:::
        [![Logic Apps Azure Logic Apps simgesi][azure-logic-apps-icon]][nested-logic-app-doc]
        \
        \
        [**Azure Logic Apps**][nested-logic-app-doc]
        \
        \
        **BIR http isteği alındığında** adlı istek tetikleyicisiyle başlayan diğer iş akışlarını çağırın.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="run-code-from-workflows"></a>İş akışlarından kod çalıştırma

Logic Apps, kendi kodunuzu iş akışınızda çalıştırmaya yönelik yerleşik eylemler sağlar:

:::row:::
    :::column:::
        [![Logic Apps 'de Azure Işlevleri simgesi][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure işlevleri**][azure-functions-doc]
        \
        \
        Kendi *kod parçacıklarınızı* (C# veya Node.js) iş akışınız dahilinde çalıştırmak için [Azure 'da barındırılan işlevleri](../azure-functions/functions-overview.md) çağırın.
    :::column-end:::
    :::column:::
        [![Logic Apps satır içi kod simgesi][inline-code-icon]][inline-code-doc]
        \
        \
        [**Satır içi kod**][inline-code-doc]
        \
        \
        [**JavaScript kodunu Yürüt**][inline-code-doc]: iş akışınız içinde kendi satır içi JavaScript *kod parçacıklarını* ekleyin ve çalıştırın.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="control-workflow"></a>Denetim iş akışı

Logic Apps, iş akışınızda eylemleri yapılandırmak ve denetlemek için yerleşik eylemler sağlar:

:::row:::
    :::column:::
        [![Logic Apps koşul eylemi simgesi][condition-icon]][condition-doc]
        \
        \
        [**Koşul**][condition-doc]
        \
        \
        Koşulu değerlendirin ve koşulun doğru veya yanlış olduğunu temel alarak farklı eylemleri çalıştırın.
    :::column-end:::
    :::column:::
        [![Logic Apps her eylem simgesi için][for-each-icon]][for-each-doc]
        \
        \
        [**Her biri için**][for-each-doc]
        \
        \
        Bir dizideki her öğe için aynı eylemleri gerçekleştirin.
    :::column-end:::
    :::column:::
        [![Logic Apps kapsam eylemi simgesi][scope-icon]][scope-doc]
        \
        \
        [**Ada**][scope-doc]
        \
        \
        Kapsamdaki eylemlerin çalışmasını bitirdikten sonra kendi durumlarını içeren, eylemleri *kapsamlara* gruplandırın.
    :::column-end:::
    :::column:::
        [![Logic Apps eylem simgesine geç][switch-icon]][switch-doc]
        \
        \
        [**Değiştirebilirsiniz**][switch-doc]
        \
        \
        Eylemleri, varsayılan durum dışında benzersiz değerler atanmış *durumlarına* göre gruplandırın. Yalnızca atanmış değeri bir ifade, nesne veya belirteçten sonuçla eşleşen bu durumu çalıştırın. Hiçbir eşleşme yoksa, varsayılan durumu çalıştırın.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps sonlandırma eylemi simgesi][terminate-icon]][terminate-doc]
        \
        \
        [**Sonlandırmayı**][terminate-doc]
        \
        \
        Etkin bir şekilde çalışan mantıksal uygulama iş akışını durdurun. 
    :::column-end:::
    :::column:::
        [![Logic Apps eylem simgesine kadar][until-icon]][until-doc]
        \
        \
        [**Until**][until-doc]
        \
        \
        Belirtilen koşul doğru olana veya bir durum değiştirilene kadar eylemleri tekrarlayın.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-or-manipulate-data"></a>Verileri yönetme veya işleme

Logic Apps, veri çıktıları ve biçimleri ile çalışmaya yönelik yerleşik eylemler sağlar:

:::row:::
    :::column:::
        [![Logic Apps veri Işlemleri eylem simgesi][data-operations-icon]][data-operations-doc]
        \
        \
        [**Veri Işlemleri**][data-operations-doc]
        \
        \
        Verilerle işlemler gerçekleştirin. 
        \
        \
        **Oluştur**: çeşitli türlerde birden çok girişe ait tek bir çıktı oluşturun. 
        \
        \
        **CSV tablosu oluştur**: JSON nesneleriyle bir diziden virgülle ayrılmış değer (CSV) tablosu oluşturun. 
        \
        \
        **HTML tablosu oluştur**: JSON nesneleriyle BIR diziden HTML tablosu oluşturma. 
        \
        \
        **Diziyi filtrele**: ölçütlerinizi karşılayan başka bir dizideki öğelerden bir dizi oluşturun. 
        \
        \
        **Birleştir**: dizideki tüm öğelerden bir dize oluşturun ve bu öğeleri belirtilen sınırlayıcıyla ayırın. 
        \
        \
        **JSON Ayrıştır**: iş akışınızda bu özellikleri kullanabilmeniz IÇIN, JSON içeriğindeki özelliklerden ve bunların değerlerinden Kullanıcı dostu belirteçler oluşturun. 
        \
        \
        Şunları **seçin**: öğeleri veya değerleri başka bir dizide dönüştürerek ve bu öğeleri belirtilen ÖZELLIKLERLE eşleyerek JSON nesneleriyle bir dizi oluşturun.
    :::column-end:::
    :::column:::
        ![Logic Apps tarih saat eylem simgesi][date-time-icon]
        \
        \
        **Tarih saat**
        \
        \
        Zaman damgalarına sahip işlemler gerçekleştirin.
        \
        \
        **Zamana Ekle**: belirtilen birim sayısını bir zaman damgasına ekleyin. 
        \
        \
        **Saat dilimini Dönüştür**: kaynak saat dilimindeki bir zaman damgasını hedef saat dilimine dönüştürür. 
        \
        \
        **Geçerli zaman**: geçerli zaman damgasını bir dize olarak döndürür. 
        \
        \
        **Gelecek zamanı al**: geçerli zaman damgasını ve belirtilen zaman birimlerini döndürün. 
        \
        \
        **Son saati al**: geçerli zaman damgasını belirtilen zaman birimleriyle döndürün. 
        \
        \
        **Çıkarma zamanı**: zaman damgasından bir dizi zaman birimi çıkar.
    :::column-end:::
    :::column:::
        [![Logic Apps değişkenler eylem simgesi][variables-icon]][variables-doc]
        \
        \
        [**Değişkenlerinin**][variables-doc]
        \
        \
        Değişkenlerle işlemler gerçekleştirin.
        \
        \
        **Dizi değişkenine Ekle**: bir değişken tarafından depolanan bir dizideki son öğe olarak bir değer ekleyin. 
        \
        \
        **Dize değişkenine Ekle**: bir değişken tarafından depolanan bir dizedeki son karakter olarak bir değer ekleyin.
        \
        \
        **Değişken azaltma**: bir değişkeni sabit bir değere küçültün.
        \
        \
        **Artış değişkeni**: bir değişkeni sabit bir değerle artırın. 
        \
        \
        **Değişkeni Başlat**: bir değişken oluşturun ve veri türünü ve başlangıç değerini bildirin. 
        \
        \
        **Set değişkeni**: var olan bir değişkene farklı bir değer atayın. 
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Logic Apps çağırabilmeniz için özel API 'Ler oluşturun](/logic-apps/logic-apps-create-api-app)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png


<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "API 'lerinizi yönetmek ve yayımlamak için bir Azure API Management hizmet örneği oluşturma"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Mantıksal uygulamaları App Service API Apps ile tümleştirin"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Mantıksal uygulamaları Azure Işlevleri ile tümleştirme"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Gruplardaki iletileri veya toplu işlem olarak işleme"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Koşulu değerlendirin ve koşulun doğru veya yanlış olduğunu temel alarak farklı eylemleri çalıştırın"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Bir dizideki her öğe için aynı eylemleri gerçekleştirin"
[http-doc]: ./connectors-native-http.md "Mantıksal uygulamalarınızdan HTTP veya HTTPS uç noktalarını çağırma"
[http-request-doc]: ./connectors-native-reqres.md "Mantıksal uygulamalarınızda HTTP istekleri alma"
[http-response-doc]: ./connectors-native-reqres.md "Mantıksal uygulamalarınızdan gelen HTTP isteklerine yanıt verme"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Mantıksal uygulamalarınızdan REST uç noktalarını çağırma"
[http-webhook-doc]: ./connectors-native-webhook.md "HTTP veya HTTPS uç noktalarından gelen belirli olayları bekle"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "Mantıksal uygulamalarınızdan JavaScript kod parçacıklarını ekleyin ve çalıştırın"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Mantıksal uygulamaları iç içe geçmiş iş akışları ile tümleştirin"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Sorgu eylemi ile dizileri seçip filtreleyin"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Mantıksal uygulamaları zamanlamaya göre Çalıştır"
[schedule-delay-doc]: ./connectors-native-delay.md "Sonraki eylemi çalıştırmayı geciktir"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Sonraki eylemi çalıştırmayı geciktir"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Mantıksal uygulamaları yinelenen bir zamanlamaya göre çalıştırma"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Bitişik öbeklerdeki verileri işlemesi gereken Logic Apps 'i çalıştırma"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Gruptaki eylemler çalışmaya başladıktan sonra kendi durumlarını almak için eylemleri gruplar halinde düzenleyin"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Eylemleri, benzersiz değerler atanmış durumlarda düzenleyin. Yalnızca değeri bir ifade, nesne veya belirteçten sonuçla eşleşen bir durum çalıştırın. Hiçbir eşleşme yoksa, varsayılan durumu çalıştırın"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Mantıksal uygulamanız için etkin olarak çalışan bir iş akışını durdurma veya iptal etme"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Belirtilen koşul doğru olana veya bir durum değiştirilene kadar eylemleri tekrarlayın"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Dizileri filtreleme veya CSV ve HTML tabloları oluşturma gibi veri işlemleri gerçekleştirin"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Başlatma, ayarlama, artırma, azaltma ve dize veya dizi değişkenine ekleme gibi değişkenlerle işlemleri gerçekleştirin"
