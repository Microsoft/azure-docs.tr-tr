---
title: Azure ön kapı hizmeti 'nde ölçümleri ve günlükleri izleme | Microsoft Docs
description: Bu makalede, Azure ön kapı hizmeti 'nin desteklediği farklı ölçümler ve erişim günlükleri açıklanmaktadır
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 229706ff91b776363d3e9de080e02cee5edf9c77
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677907"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Azure ön kapı hizmeti 'nde ölçümleri ve günlükleri izleme

Azure ön kapı hizmeti 'ni kullanarak kaynakları aşağıdaki yollarla izleyebilirsiniz:

- **Ölçümler**. Azure ön kapısının Şu anda performans sayaçlarını görüntülemek için yedi ölçümü vardır.
- **Günlükler**. Etkinlik ve tanılama günlükleri, izleme amacıyla bir kaynaktan performans, erişim ve diğer verilerin kaydedilmesine veya kullanılmasına olanak tanır.

### <a name="metrics"></a>Ölçümler

Ölçümler, portalda performans sayaçlarını görüntülemenize olanak tanıyan belirli Azure kaynaklarına yönelik bir özelliktir. Aşağıda kullanılabilir ön kapı ölçümleri verilmiştir:

| Ölçüm | Ölçüm görünen adı | Birim | Boyutlar | Açıklama |
| --- | --- | --- | --- | --- |
| Istek sayısı | İstek Sayısı | Sayı | Http durumu</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Ön kapı tarafından sunulan istemci isteklerinin sayısı.  |
| RequestSize | İstek boyutu | Sayacının | Http durumu</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | İstemcilerden ön kapıya istek olarak gönderilen bayt sayısı. |
| Yanıt boyutu | Yanıt boyutu | Sayacının | Http durumu</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Ön kapıdan istemcilere yanıt olarak gönderilen bayt sayısı. |
| TotalLatency | Toplam gecikme süresi | Mayacak | Http durumu</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | İstemci, ön kapıdan gelen son yanıt baytını kabul edene kadar ön kapıdan alınan istemci isteğinden hesaplanan süredir. |
| BackendRequestCount | Arka uç Istek sayısı | Sayı | Http durumu</br>HttpStatusGroup</br>Sunucusundan | Ön kapıdan arka uçlara gönderilen isteklerin sayısı. |
| BackendRequestLatency | Arka uç Isteği gecikmesi | Mayacak | Sunucusundan | Ön kapı arka uca Son Yanıt baytı aldığından, isteğin ön uca gönderildiği süre. |
| BackendHealthPercentage | Arka uç sistem durumu yüzdesi | Yüzde | Sunucusundan</br>BackendPool | Ön kapıdan arka uçlara yapılan başarılı sistem durumu araştırmalarının yüzdesi. |
| WebApplicationFirewallRequestCount | Web uygulaması güvenlik duvarı Istek sayısı | Sayı | PolicyName</br>RuleName</br>Eylem | Ön kapısının uygulama katmanı güvenliği tarafından işlenen istemci isteklerinin sayısı. |

## <a name="activity-log"></a>Etkinlik günlükleri

Etkinlik günlükleri, ön kapı hizmetinde gerçekleştirilen işlemler hakkında bilgi sağlar. Ayrıca, ön kapı hizmetinde ne, kim ve ne zaman bir yazma işlemi (put, post veya silme) alındığını da tespit ederler.

>[!NOTE]
>Etkinlik günlükleri okuma (Get) işlemlerini içermez. Ayrıca, Azure portal veya orijinal yönetim API 'sini kullanarak gerçekleştirdiğiniz işlemleri de içermez.

Ön kapı hizmetinizde etkinlik günlüklerine veya Azure Izleyici 'deki Azure kaynaklarınızın tüm günlüklerine erişin. Etkinlik günlüklerini görüntülemek için:

1. Ön kapı örneğinizi seçin.
2. **Etkinlik günlüğü**' nü seçin.

    ![Etkinlik günlüğü](./media/front-door-diagnostics/activity-log.png)

3. Bir filtreleme kapsamı seçin ve ardından **Uygula**' yı seçin.

## <a name="diagnostic-logging"></a>Tanılama günlükleri
Tanılama günlükleri, denetim ve sorun giderme için önemli olan işlemler ve hatalar hakkında zengin bilgiler sağlar. Tanılama günlükleri, etkinlik günlüklerinden farklıdır.

Etkinlik günlükleri, Azure kaynakları üzerinde gerçekleştirilen işlemlere yönelik öngörüler sağlar. Tanılama günlükleri, kaynağınızın gerçekleştirdiği işlemlere ilişkin öngörüler sağlar. Daha fazla bilgi için bkz. [Azure izleyici tanılama günlükleri](../azure-monitor/platform/resource-logs-overview.md).

![Tanılama günlükleri](./media/front-door-diagnostics/diagnostic-log.png)

Ön kapı hizmetiniz için tanılama günlüklerini yapılandırmak için:

1. Azure ön kapı hizmetinizi seçin.

2. **Tanılama ayarları**' nı seçin.

3. **Tanılamayı aç '** ı seçin. Tanılama günlüklerini bir depolama hesabı ölçümleriyle birlikte arşivleyin, bunları bir olay hub 'ına akıtır veya Azure Izleyici günlüklerine gönderin.

Ön kapı hizmeti şu anda tanılama günlükleri sağlıyor (toplu olarak saatlik). Tanılama günlükleri, her giriş için aşağıdaki şemaya sahip tek API istekleri sağlar:

| Özellik  | Açıklama |
| ------------- | ------------- |
| ClientIP | İsteği yapan istemcinin IP adresi. |
| Istemci bağlantı noktası | İsteği yapan istemcinin IP bağlantı noktası. |
| httpMethod | İstek tarafından kullanılan HTTP yöntemi. |
| HttpStatusCode | Proxy 'den döndürülen HTTP durum kodu. |
| HttpStatusDetails | İstek üzerine sonuçtaki durum. Bu dize değerinin anlamı, bir durum başvuru tablosunda bulunabilir. |
| httpVersion | İstek veya bağlantı türü. |
| RequestBytes | İstek üst bilgileri ve istek gövdesi dahil olmak üzere HTTP isteği iletisinin bayt cinsinden boyutu. |
| RequestUri | Alınan isteğin URI 'SI. |
| ResponseBytes | Yanıt olarak arka uç sunucusu tarafından gönderilen bayt.  |
| RoutingRuleName | İsteğin eşleştiği yönlendirme kuralının adı. |
| SecurityProtocol | İstek tarafından kullanılan TLS/SSL protokol sürümü veya şifreleme yoksa null. |
| TimeTaken | İşlemin gerçekleştiği sürenin milisaniye cinsinden uzunluğu. |
| Kullanıcı | İstemcinin kullandığı tarayıcı türü |
| TrackingReference | Ön kapıya yönelik olarak sunulan ve istemciye X-Azure-ref üst bilgisi olarak gönderilen bir isteği tanımlayan benzersiz başvuru dizesi. Belirli bir istek için erişim günlüklerindeki ayrıntıları aramak için gereklidir. |

## <a name="next-steps"></a>Sonraki adımlar

- [Ön kapı profili oluşturma](quickstart-create-front-door.md)
- [Ön kapı nasıl kullanılır?](front-door-routing-architecture.md)
