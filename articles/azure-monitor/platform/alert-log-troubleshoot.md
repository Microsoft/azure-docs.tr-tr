---
title: Azure Izleyici 'de günlük uyarıları sorunlarını giderme | Microsoft Docs
description: Azure 'da günlük uyarısı kuralları için genel sorunlar, hatalar ve çözümler.
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 794f4ad5bba46af53280d35b55b762b9eef8e1a1
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71675241"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Azure Izleyici 'de günlük uyarıları sorunlarını giderme  

Bu makalede, Azure Izleyici 'de günlük uyarılarını ayarlarken oluşabilecek yaygın sorunların nasıl çözümleneceği gösterilmektedir. Ayrıca, işlevlerle ilgili sık karşılaşılan sorunlara veya günlük uyarılarının yapılandırmasına yönelik çözümler sağlar. 

*Günlük uyarıları* , [Azure Log Analytics çalışma](../learn/tutorial-viewdata.md) alanındaki veya [Azure Application Insights](../../azure-monitor/app/analytics.md)bir günlük sorgusuna dayalı olarak başlatılan kuralları anlatmaktadır. [Azure izleyici 'de günlük uyarılarında](../platform/alerts-unified-log.md)işlevsellik, terminoloji ve türler hakkında daha fazla bilgi edinin.

> [!NOTE]
> Bu makalede, Azure portal tetiklenen bir uyarı kuralı gösterdiği ve ilişkili bir eylem grubu tarafından bir bildirim gerçekleştirilmediği durumlar dikkate almaz. Bu gibi durumlarda, [Azure Portal eylem grupları oluşturma ve yönetme](../platform/action-groups.md)içindeki ayrıntılara bakın.

## <a name="log-alert-didnt-fire"></a>Günlük uyarısı tetikmedi

[Azure izleyici 'de yapılandırılmış bir günlük uyarısı kuralının](../platform/alerts-log.md) durumunun [beklendiğinde *tetiklenen* olarak gösterilmediği](../platform/alerts-managing-alert-states.md)bazı yaygın nedenler aşağıda verilmiştir. 

### <a name="data-ingestion-time-for-logs"></a>Günlükler için veri alımı süresi

Bir günlük uyarısı, sorgunuzu [Log Analytics](../learn/tutorial-viewdata.md) veya [Application Insights](../../azure-monitor/app/analytics.md)göre düzenli olarak çalıştırır. Azure Izleyici, dünyanın dört bir yanındaki binlerce müşterinin birçok terabayta veri işlediği için hizmet, değişen zaman gecikmelerine açıktır. Daha fazla bilgi için bkz. [Azure izleyici günlüklerinde veri alma süresi](../platform/data-ingestion-time.md).

Gecikme süresini azaltmak için sistem, gerekli verileri bulmadığında uyarı sorgusunu birden çok kez bekler ve yeniden dener. Sistem, bir bekleme süresi kümesini üstel olarak artırır. Günlük uyarısı yalnızca veriler kullanılabilir olduktan sonra tetiklenir. bu nedenle gecikme, günlük verilerinin yavaş alımı nedeniyle olabilir. 

### <a name="incorrect-time-period-configured"></a>Yanlış zaman aralığı yapılandırıldı

[Günlük uyarılarına yönelik terminoloji](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)makalesinde açıklandığı gibi, yapılandırmada belirtilen zaman aralığı sorgunun zaman aralığını belirtir. Sorgu yalnızca bu Aralık içinde oluşturulmuş kayıtları döndürür. 

Zaman aralığı, bir günlük sorgusu için getirilen verileri kötüye kullanımı engelleyecek şekilde kısıtlar ve bir günlük sorgusunda kullanılan herhangi bir zaman komutunu ( **kullanım gibi)** atladık. Örneğin, zaman aralığı 60 dakikaya ayarlanmışsa ve sorgu 1:15 PM 'de çalıştırıldığında, günlük sorgusu için yalnızca 12:15 PM ve 1:15 PM arasında oluşturulan kayıtlar kullanılır. Günlük sorgusu **önce (1D)** gibi bir Time komutu kullanıyorsa, sorgu yalnızca 12:15 pm ve 1:15 PM arasındaki verileri kullanır, çünkü zaman aralığı bu aralığa ayarlanır.

Yapılandırmadaki zaman döneminin Sorgunuzla eşleşip eşleşmediğini denetleyin. Daha önce gösterilen örnek için, günlük sorgusu yeşil işaret ile **önce (1D)** kullanıyorsa, zaman aralığı 24 saat veya 1.440 dakika (kırmızı renkte gösterilir) olarak ayarlanmalıdır. Bu ayar sorgunun istendiği gibi çalışmasını sağlar.

![Süre](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Uyarıları gösterme seçeneği ayarlandı

[Azure Portal günlük uyarı kuralı oluşturma](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)makalesinin adım 8 ' de açıklandığı gibi, günlük uyarıları, yapılandırılan bir süre boyunca tetiklenme ve bildirim eylemlerinin görüntülenmesini sağlamak Için **uyarıları bastır** seçeneğini sağlar. Sonuç olarak, bir uyarının tetiklemeyebilir. Aslında, tetikledi ancak gösterilmedi.  

![Uyarıları bastır](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Ölçüm ölçümü uyarı kuralı yanlış

*Ölçüm Ölçüm günlüğü uyarıları* , özel yeteneklere ve kısıtlanmış bir uyarı sorgu sözdizimine sahip olan günlük uyarılarının bir alt türüdür. Ölçüm Ölçüm günlüğü uyarısı için bir kural, sorgu çıktısının ölçüm zaman serisi olmasını gerektirir. Diğer bir deyişle, çıkış, karşılık gelen toplanmış değerlerle birlikte farklı, eşit ölçekli zaman dönemlerine sahip bir tablodur. 

Tablodaki **aggregişsiz değerin**yanı sıra ek değişkenler de seçebilirsiniz. Bu değişkenler tabloyu sıralamak için kullanılabilir. 

Örneğin, bir ölçüm Ölçüm günlüğü uyarısı için bir kural yapılandırıldığını varsayalım:

- @No__t sorgusu-0  
- 6 saatlik zaman aralığı
- 50 eşiği
- Art arda üç binden uyarı mantığı
- **$Table** olarak seçili **olduğunda toplama**

Komut **Özet içerdiğinden... ve,** iki değişken (**zaman damgası** ve **$Table**) sağlar, sistem **toplama**için **$Table** seçer. Sistem sonuç tablosunu, aşağıdaki ekran görüntüsünde gösterildiği gibi **$Table** alana göre sıralar. Ardından, üç veya daha fazla ardışık ihlal olup olmadığını görmek için her tablo türü için birden fazla **aggregte değer** örneğine bakar (örneğin, **kullanılabilirlik sonuçları**gibi).

![Birden çok değerle ölçüm ölçümü sorgu yürütme](media/alert-log-troubleshoot/LogMMQuery.png)

**Toplama** , **$Table**üzerinde tanımlandığından, veriler bir **$Table** sütununda sıralanır (kırmızı renkte gösterilir). Ardından, alan **üzerinde toplama** türlerini gruplarız ve arayacaktır. 

Örneğin, **$Table**Için, **kullanılabilirlik sonuçları** için değerler tek bir çizim/varlık olarak kabul edilir (turuncu olarak gösterilir). Bu değer çiziminde/varlıkta, uyarı hizmeti art arda üç ihlal olup olmadığını denetler (yeşil renkte gösterilir). Binler, tablo değeri **kullanılabilirliği sonuçları**için bir uyarı tetikler. 

Benzer şekilde, başka bir **$Table**değeri için art arda üç BIN gerçekleşiyorsa, aynı şey için başka bir uyarı bildirimi tetiklenir. Uyarı hizmeti, bir çizim/varlıktaki değerleri (turuncu olarak gösterilir) zamana göre otomatik olarak sıralar.

Artık Ölçüm Ölçüm günlüğü uyarısı kuralının değiştirildiğini ve sorgunun `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` olduğunu varsayalım. Yapılandırmanın geri kalanı, art arda üç ihlal için uyarı mantığı da dahil olmak üzere önceki ile aynı şekilde kaldı. Bu örnekte **toplama** seçeneği varsayılan olarak **zaman damgasıdır** . Özetleme sorgusunda yalnızca bir değer verilmiştir **... (yani** , **zaman damgası**). Önceki örnekte olduğu gibi, yürütmenin sonundaki çıktı aşağıdaki gibi gösterilmektedir.

   ![Ölçüm ölçümü sorgu yürütme tekil değer ile](media/alert-log-troubleshoot/LogMMtimestamp.png)

Üzerinde **toplama** , **zaman damgasında**tanımlandığından, veriler **zaman damgası** sütununda sıralanır (kırmızı renkte gösterilir). **Zaman damgasına**göre gruplarız. Örneğin, `2018-10-17T06:00:00Z` değerleri, tek bir çizim/varlık olarak değerlendirilir (turuncu olarak gösterilir). Bu değer çiziminde/varlıkta, uyarı hizmeti ardışık ihlaller bulmayacak (her **zaman damgası** değeri yalnızca bir girdiye sahip olduğundan). Bu nedenle, uyarı hiçbir şekilde tetiklenmez. Böyle bir durumda, kullanıcının şunlardan biri olmalıdır:

- **Toplama** alanını kullanarak doğru şekilde sıralamak için bir kukla değişken veya var olan bir değişken ( **$Table**gibi) ekleyin.
- Uyarı kuralını, bunun yerine **Toplam ihlaline** dayanarak uyarı mantığını kullanacak şekilde yeniden yapılandırın.

## <a name="log-alert-fired-unnecessarily"></a>Günlük uyarısı gereksiz şekilde tetiklendi

Azure [izleyici 'de bir yapılandırılmış günlük uyarı kuralı](../platform/alerts-log.md) , [Azure uyarıları](../platform/alerts-managing-alert-states.md)'nda görüntülediğinizde beklenmedik şekilde tetiklenebilir. Aşağıdaki bölümlerde bazı yaygın nedenler açıklanmaktadır.

### <a name="alert-triggered-by-partial-data"></a>Kısmi verilerin tetiklediği uyarı

Log Analytics ve Application Insights alma gecikmeleri ve işleme tabidir. Bir günlük uyarı sorgusu çalıştırdığınızda, hiçbir veri bulunmadığını veya yalnızca bazı verilerin kullanılabildiğini fark edebilirsiniz. Daha fazla bilgi için bkz. [Azure izleyici 'de günlük verisi alma süresi](../platform/data-ingestion-time.md).

Uyarı kuralını nasıl yapılandırdığınıza bağlı olarak, uyarı yürütme sırasında günlüklerde veri veya kısmi veri yoksa hatalı başlatma gerçekleşebilir. Bu gibi durumlarda, uyarı sorgusu veya yapılandırmasını değiştirmenizi tavsiye ederiz. 

Örneğin, bir analiz sorgusunun sonuç sayısı 5 ' ten az olduğunda, günlük uyarı kuralını tetiklenecek şekilde yapılandırırsanız, hiçbir veri (sıfır kayıt) veya kısmi sonuç (bir kayıt) olmadığında uyarı tetiklenir. Ancak veri alma gecikmeden sonra, tam verilerle aynı sorgu 10 kayıt sonucu verebilir.

### <a name="alert-query-output-is-misunderstood"></a>Uyarı sorgusu çıkışı yanlış anlaşıldı

Bir analiz sorgusunda günlük uyarıları mantığını sağlarsınız. Analiz sorgusu çeşitli büyük verileri ve matematik işlevlerini kullanabilir. Uyarı hizmeti, belirli bir dönemdeki verilerle belirtilen aralıklarla sorgunuzu çalıştırır. Uyarı hizmeti, uyarı türüne göre sorgu üzerinde ince değişiklikler yapar. Bu değişikliği, **sinyal mantığını Yapılandır** ekranında **yürütülecek sorgu** bölümünde görüntüleyebilirsiniz:

![Yürütülecek sorgu](media/alert-log-troubleshoot/LogAlertPreview.png)

**Yürütülecek sorgu** , günlük uyarı hizmetinin çalışma çalışmasıdır. Uyarıyı oluşturmadan önce uyarı sorgusu çıkışının ne olabileceğini anlamak istiyorsanız, belirtilen sorguyu ve TimeSpan 'yi [analiz portalı](../log-query/portals.md) veya [analiz API 'si](https://docs.microsoft.com/rest/api/loganalytics/)aracılığıyla çalıştırabilirsiniz.

## <a name="log-alert-was-disabled"></a>Günlük uyarısı devre dışı bırakıldı

Aşağıdaki bölümlerde, Azure Izleyici 'nin [günlük uyarı kuralını](../platform/alerts-log.md)devre dışı bırakabileceği bazı nedenler listelenmektedir.

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Uyarının oluşturulduğu kaynak artık yok

Azure Izleyici 'de oluşturulan günlük uyarı kuralları, Azure Log Analytics çalışma alanı, Azure Application Insights uygulaması ve Azure kaynağı gibi belirli bir kaynağı hedefleyin. Günlük uyarı hizmeti daha sonra belirtilen hedef için kuralda sağlanmış bir analiz sorgusu çalıştırır. Ancak, kural oluşturulduktan sonra kullanıcılar genellikle Azure 'dan silme veya Azure içinde taşıma (günlük uyarı kuralının hedefi) ile devam etmek için yapılır. Uyarı kuralının hedefi artık geçerli olmadığından, kuralın yürütülmesi başarısız olur.

Bu gibi durumlarda, Azure Izleyici günlük uyarısını devre dışı bırakır ve kural, boyutlandırılabilir süre (hafta gibi) için sürekli olarak çalıştırıldığında gereksiz faturalandırılmamasını sağlar. Azure Izleyici, [Azure etkinlik günlüğü](../../azure-resource-manager/resource-group-audit.md)aracılığıyla günlük uyarısını devre dışı bıraktığınız zaman tam zamanı bulabilirsiniz. Azure etkinlik günlüğünde, Azure Izleyici günlük uyarı kuralını devre dışı bıraktığında bir olay eklenir.

Azure etkinlik günlüğünde aşağıdaki örnek olay, sürekli hata nedeniyle devre dışı bırakılmış bir uyarı kuralına yöneliktir.

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Günlük uyarısında kullanılan sorgu geçerli değil

Azure Izleyici 'de yapılandırmanın bir parçası olarak oluşturulan her günlük uyarısı kuralı, uyarı hizmetinin düzenli olarak çalışacağı bir analiz sorgusu belirtmelidir. Analiz sorgusunun kural oluşturma veya güncelleştirme sırasında doğru sözdizimi olabilir. Ancak bazen bir süre boyunca, günlük uyarı kuralında belirtilen sorgu, sözdizimi sorunları geliştirebilir ve kural yürütmenin başarısız olmasına neden olabilir. Bir günlük uyarı kuralında sağlanmış analiz sorgusunun hata geliştirebileceği bazı yaygın nedenler şunlardır:

- Sorgu [birden fazla kaynak arasında çalışacak](../log-query/cross-workspace-query.md)şekilde yazılmıştır. Ve belirtilen kaynaklardan bir veya daha fazlası artık yok.
- [Ölçüm Ölçüm türü günlük uyarısıyla](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) yapılandırılan bir uyarı sorgusu, Norms sözdizimiyle uyumlu değil
- Analiz platformunda veri akışı yoktu. Sorgu yürütme, belirtilen sorgu için veri bulunmadığından [bir hata veriyor](https://dev.loganalytics.io/documentation/Using-the-API/Errors) .
- [Sorgu dilindeki](https://docs.microsoft.com/azure/kusto/query/) değişiklikler, komutlar ve işlevler için düzeltilmiş bir biçim içerir. Bu nedenle, daha önce bir uyarı kuralında sağlanmış olan sorgu artık geçerli değildir.

[Azure Advisor](../../advisor/advisor-overview.md) bu davranışla ilgili olarak sizi uyarır. Orta etki ile yüksek kullanılabilirlik kategorisi ve "izlemeyi sağlamak için günlük uyarı kuralınızı onarma" açıklaması altında, Azure Advisor 'daki belirli günlük uyarısı kuralı için bir öneri eklenmiştir. Azure Advisor yedi gün için bir öneri sağladıktan sonra günlük uyarı kuralında bir uyarı sorgusu yoksa, Azure Izleyici günlük uyarısını devre dışı bırakır ve kural boyutlandırılabilir bir süre için sürekli olarak çalışmazsa gereksiz yere faturalandırılmazsınız. bir hafta gibi).

Azure Izleyici günlük uyarı kuralını [Azure etkinlik günlüğünde](../../azure-resource-manager/resource-group-audit.md)bir olay arayarak devre dışı bıraktığınız zaman tam zamanı bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure 'da günlük uyarıları](../platform/alerts-unified-log.md)hakkında bilgi edinin.
- [Application Insights](../../azure-monitor/app/analytics.md)hakkında daha fazla bilgi edinin.
- [Günlük sorguları](../log-query/log-query-overview.md)hakkında daha fazla bilgi edinin.
