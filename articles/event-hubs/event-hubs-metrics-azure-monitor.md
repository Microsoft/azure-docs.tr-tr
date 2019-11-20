---
title: Azure Izleyici 'de ölçümler-Azure Event Hubs | Microsoft Docs
description: Bu makale, Azure Event Hubs'ı izlemek için Azure izleme kullanımı konusunda bilgi sağlar
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/18/2019
ms.author: shvija
ms.openlocfilehash: 788f0647bec11184c2a85d87d0dfde2cb6c5744c
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266296"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Azure Izleyici 'de Azure Event Hubs ölçümleri

Event Hubs ölçümler, Azure aboneliğinizdeki Event Hubs kaynaklarının durumunu sağlar. Ölçüm verileri zengin ile event hubs'ınız değil yalnızca ad alanı düzeyinde, aynı zamanda varlık düzeyinde genel durumunu değerlendirebilirsiniz. Bu istatistikler, olay hub'larınız durumunu izlemek için yardımcı önemli olabilir. Ölçümler, Azure desteğine başvurun gerek kalmadan kök neden sorunlarını da yardımcı olabilir.

Azure İzleyici, çeşitli Azure Hizmetleri genelinde izleme için birleştirilmiş bir kullanıcı arabirimi sağlar. Daha fazla bilgi için [Microsoft Azure'da izleme](../monitoring-and-diagnostics/monitoring-overview.md) ve [almak Azure İzleyici ölçümleri .NET ile](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) GitHub üzerinde örnek.

## <a name="access-metrics"></a>Erişim ölçümleri

Azure İzleyici ölçümlerine erişim birden çok yol sağlar. Ölçümlere [Azure Portal](https://portal.azure.com)aracılığıyla erişebilir veya Azure Izleyici API 'LERINI (REST ve .net) ve Log Analytics ve Event Hubs gibi çözümleme çözümlerini kullanabilirsiniz. Daha fazla bilgi için [İzleme verilerine Azure İzleyicisi tarafından toplanan](../azure-monitor/platform/data-platform.md).

Ölçümler, varsayılan olarak etkindir ve en son 30 Günün verilerini erişebilir. Uzun bir süre saklamak istiyorsanız ölçüm verileri bir Azure depolama hesabına arşivleyebilir. Bu yapılandırılan [tanılama ayarları](../azure-monitor/platform/diagnostic-settings.md) Azure İzleyici'de.


## <a name="access-metrics-in-the-portal"></a>Portalda erişim ölçümlerini

Zaman içinde ölçümleri izleyebilirsiniz [Azure portalında](https://portal.azure.com). Aşağıdaki örnek, başarılı istekleri ve hesap düzeyinde gelen istekleri görüntülemek gösterilmektedir:

![Başarılı ölçümlerini görüntüleme][1]

Ad alanı aracılığıyla doğrudan ölçümleri de erişebilirsiniz. Bunu yapmak için ad alanınızı seçin ve ardından **ölçümler**' e tıklayın. Olay Hub 'ının kapsamına filtrelenen ölçümleri göstermek için Olay Hub 'ını seçin ve ardından **ölçümler**' e tıklayın.

Ölçümleri boyutlarını desteklemek için aşağıdaki örnekte gösterildiği gibi istenen boyut değerine sahip filtre gerekir:

![Boyut değeri ile Filtrele][2]

## <a name="billing"></a>Faturalandırma

Azure Izleyici 'de ölçümlerin kullanılması şu anda ücretsizdir. Ölçüm verilerini alma, ek çözümleri kullanırsanız, ancak bu çözümler tarafından faturalandırılırsınız. Örneğin, ölçüm verileri bir Azure depolama hesabına arşivleme, Azure Depolama tarafından faturalandırılır. Ayrıca, ölçüm verilerini gelişmiş analizler için Azure Izleyici günlüklerine akıdıysanız Azure tarafından da faturalandırılırsınız.

Aşağıdaki ölçümler size sistem hizmetinizin genel bakış sunar. 

> [!NOTE]
> Farklı bir adla geçildiği size çeşitli ölçümleri kullanımdan. Bu, başvurularını güncelleştirmek gerektirebilir. "Kullanım dışı" anahtar sözcüğüyle işaretli ölçümleri, bundan sonra desteklenmeyecek.

Azure İzleyici, tüm ölçüm değerleri dakikada gönderilir. Zaman ayrıntı düzeyi ölçüm değerleri sunulduğu zaman aralığını tanımlar. Tüm Event Hubs ölçümler için desteklenen zaman aralığı 1 dakikadır.

## <a name="request-metrics"></a>İstek ölçümleri

Veri ve yönetim işlemleri istek sayısını sayar.

| Ölçüm Adı | Açıklama |
| ------------------- | ----------------- |
| Gelen İstekler  | Belirtilen bir süredeki Azure Event Hubs hizmetine yapılan isteklerin sayısı. <br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName |
| Başarılı İstekler    | Azure Event Hubs hizmeti için belirtilen bir süredeki iletilen başarılı istek sayısı. <br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName |
| Sunucu Hataları  | Azure Event Hubs hizmetinde bir hata nedeniyle belirtilen bir süredeki işlenmedi istek sayısı. <br/><br/>Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName |
|Kullanıcı hataları |Kullanıcı hataları nedeniyle, belirtilen bir süredeki işlenmedi istek sayısı.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|Kotanın sayısı aşıldı |Kullanılabilir kota isteklerinin sayısı aşıldı. Bkz: [bu makalede](event-hubs-quotas.md) Event Hubs kotaları hakkında daha fazla bilgi.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|

## <a name="throughput-metrics"></a>Verimlilik metriklerini

| Ölçüm Adı | Açıklama |
| ------------------- | ----------------- |
|Kısıtlanmış Istekler |Aktarım hızı birimi kullanım aşıldığından bulunduğu için kısıtlanan istek sayısı.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|

## <a name="message-metrics"></a>İleti ölçümleri

| Ölçüm Adı | Açıklama |
| ------------------- | ----------------- |
|Gelen İletiler |Olay veya olay hub'ları için belirtilen bir süredeki gönderilen iletilerin sayısı.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|Giden İletiler |Olayları veya ileti sayısını belirtilen bir süredeki Event Hubs'dan alınan.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|Gelen bayt sayısı |Azure Event Hubs hizmeti için belirtilen bir süredeki gönderilen bayt sayısı.<br/><br/> Birim Bayt <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|Giden bayt sayısı |Bayt sayısı, belirtilen bir süredeki Azure Event Hubs hizmetinden alınır.<br/><br/> Birim Bayt <br/> Toplama türü: Toplam <br/> Boyut EntityName|

## <a name="connection-metrics"></a>Bağlantı ölçümü

| Ölçüm Adı | Açıklama |
| ------------------- | ----------------- |
|ActiveConnection sayısı |Bir varlığın yanı sıra bir ad alanı etkin bağlantı sayısı.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|Açılan bağlantılar |Açık bağlantıları sayısı.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|Kapalı bağlantılar |Kapalı bağlantılarının sayısı.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|

## <a name="event-hubs-capture-metrics"></a>Event Hubs yakalama ölçümleri

Yakalama özelliği, event hubs için etkinleştirdiğinizde, Event Hubs yakalama ölçümleri izleyebilirsiniz. Aşağıdaki ölçümler etkin yakalama ile izleyebilirsiniz açıklanmaktadır.

| Ölçüm Adı | Açıklama |
| ------------------- | ----------------- |
|Biriktirme listesi |Seçtiğiniz hedefe Yakalanacak henüz olan bayt sayısı.<br/><br/> Birim Bayt <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|Yakalanan Iletiler |Seçtiğiniz hedefe, belirtilen bir süredeki yakalanır olay sayısı.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|Yakalanan baytlar |Seçilen hedef için belirtilen bir süredeki yakalanan bayt sayısı.<br/><br/> Birim Bayt <br/> Toplama türü: Toplam <br/> Boyut EntityName|

## <a name="metrics-dimensions"></a>Ölçümleri boyutları

Azure Event Hubs, Azure İzleyicisi'nde ölçümler için aşağıdaki boyutlarını destekler. Boyutları için ölçümlerinizi eklemek isteğe bağlıdır. Ölçümleri, boyutları eklemezseniz, ad alanı düzeyinde belirtilir. 

| Ölçüm Adı | Açıklama |
| ------------------- | ----------------- |
|EntityName| Event Hubs ad alanı altında olay hub'ı varlıkları destekler.|

## <a name="azure-monitor-integration-with-siem-tools"></a>SıEM araçlarıyla Azure Izleyici tümleştirmesi
İzleme verilerinizi (etkinlik günlükleri, tanılama günlükleri vb.) Azure Izleyici ile bir olay hub 'ına yönlendirmek, güvenlik bilgileri ve olay yönetimi (SıEM) araçlarıyla kolayca tümleştirmenize olanak sağlar. Daha fazla bilgi için aşağıdaki makalelere/blog postalarına bakın:

- [Dış bir araçla tüketim için Azure izleme verilerini bir olay hub 'ına akış](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Azure günlük tümleştirmesine giriş](../security/fundamentals/azure-log-integration-overview.md)
- [SIEM araçlarıyla tümleştirmek için Azure İzleyici'yi kullanma](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

Bir SıEM aracının bir olay hub 'ından günlük verilerini tükettiği senaryoda, hiçbir gelen ileti görmüyorsanız veya ölçüm grafiğinde giden iletiler görmüyorsanız, şu adımları izleyin:

- **Gelen ileti**yoksa, Azure izleyici hizmetinin denetim/tanılama günlüklerini Olay Hub 'ına taşımayacağı anlamına gelir. Bu senaryoda Azure Izleyici ekibi ile bir destek bileti açın. 
- gelen iletiler varsa, ancak **giden iletiler yoksa**SIEM uygulamasının iletileri okumamasından dolayı olur. Olay Hub 'ının yapılandırmasının doğru olup olmadığını öğrenmek için SıEM sağlayıcısına başvurun.


## <a name="next-steps"></a>Sonraki adımlar

* Bkz: [Azure izlemeye genel bakış](../monitoring-and-diagnostics/monitoring-overview.md).
* [.NET ile Azure İzleyici ölçümleri alma](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) GitHub üzerinde örnek. 

Event Hubs hakkında daha fazla bilgi için şu bağlantıları ziyaret edin:

* [Event Hubs öğreticisi](event-hubs-dotnet-standard-getstarted-send.md) ile çalışmaya başlayın
* [Event Hubs ile ilgili SSS](event-hubs-faq.md)
* [Event Hubs kullanan örnek uygulamalar](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



