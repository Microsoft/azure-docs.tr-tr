---
title: Azure ön kapılarında ölçümleri ve günlükleri izleme | Microsoft Docs
description: Bu makalede, Azure ön kapısının desteklediği farklı ölçümler ve erişim günlükleri açıklanmaktadır
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: a972123604cafbfc4c6753c1fe3ad21147b53b2d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550668"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Azure ön kapılarında ölçümleri ve günlükleri izleme

Azure ön kapısını kullanarak kaynakları aşağıdaki yollarla izleyebilirsiniz:

- **Ölçümler**. Azure ön kapısının Şu anda performans sayaçlarını görüntülemek için sekiz ölçüm vardır.
- **Günlükler**. Etkinlik ve tanılama günlükleri, izleme amacıyla bir kaynaktan performans, erişim ve diğer verilerin kaydedilmesine veya kullanılmasına olanak tanır.

##  <a name="metrics"></a><a name="metrics"></a>Ölçümler

Ölçümler, portalda performans sayaçlarını görüntülemenize olanak tanıyan belirli Azure kaynaklarına yönelik bir özelliktir. Aşağıda kullanılabilir ön kapı ölçümleri verilmiştir:

| Metric | Ölçüm görünen adı | Birim | Boyutlar | Açıklama |
| --- | --- | --- | --- | --- |
| Istek sayısı | İstek Sayısı | Count | Http durumu</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Ön kapı tarafından sunulan istemci isteklerinin sayısı.  |
| RequestSize | İstek boyutu | Bayt | Http durumu</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | İstemcilerden ön kapıya istek olarak gönderilen bayt sayısı. |
| Yanıt boyutu | Yanıt boyutu | Bayt | Http durumu</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Ön kapıdan istemcilere yanıt olarak gönderilen bayt sayısı. |
| TotalLatency | Toplam gecikme süresi | Mayacak | Http durumu</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | AFD 'dan istemciye gönderilen Son Yanıt baytı kadar, ön kapıdan alınan istemci isteğinden geçen toplam süre. |
| BackendRequestCount | Arka uç Istek sayısı | Count | Http durumu</br>HttpStatusGroup</br>Arka uç | Ön kapıdan arka uçlara gönderilen isteklerin sayısı. |
| BackendRequestLatency | Arka uç Isteği gecikmesi | Mayacak | Arka uç | Ön kapı arka uca Son Yanıt baytı aldığından, isteğin ön uca gönderildiği süre. |
| BackendHealthPercentage | Arka uç sistem durumu yüzdesi | Yüzde | Arka uç</br>BackendPool | Ön kapıdan arka uçlara yapılan başarılı sistem durumu araştırmalarının yüzdesi. |
| WebApplicationFirewallRequestCount | Web uygulaması güvenlik duvarı Istek sayısı | Count | PolicyName</br>RuleName</br>Eylem | Ön kapısının uygulama katmanı güvenliği tarafından işlenen istemci isteklerinin sayısı. |

## <a name="activity-logs"></a><a name="activity-log"></a>Etkinlik günlükleri

Etkinlik günlükleri, ön kapıda yapılan işlemler hakkında bilgi sağlar. Bunlar ayrıca, ön kapıda herhangi bir yazma işlemi (put, post veya delete) için ne zaman, kim ve ne zaman alındığını da saptayacaktır.

>[!NOTE]
>Etkinlik günlükleri okuma (Get) işlemlerini içermez. Ayrıca, Azure portal veya orijinal yönetim API 'sini kullanarak gerçekleştirdiğiniz işlemleri de içermez.

Ön kapıdaki etkinlik günlüklerine veya Azure Izleyici 'deki Azure kaynaklarınızın tüm günlüklerine erişin. Etkinlik günlüklerini görüntülemek için:

1. Ön kapı örneğinizi seçin.
2. **Etkinlik günlüğü**' nü seçin.

    :::image type="content" source="./media/front-door-diagnostics/activity-log.png" alt-text="Etkinlik günlüğü":::

3. Bir filtreleme kapsamı seçin ve ardından **Uygula**' yı seçin.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Tanılama günlükleri
Tanılama günlükleri, denetim ve sorun giderme için önemli olan işlemler ve hatalar hakkında zengin bilgiler sağlar. Tanılama günlükleri, etkinlik günlüklerinden farklıdır.

Etkinlik günlükleri, Azure kaynakları üzerinde gerçekleştirilen işlemlere yönelik öngörüler sağlar. Tanılama günlükleri, kaynağınızın yapıldığı işlemlere ilişkin öngörüler sağlar. Daha fazla bilgi için bkz. [Azure izleyici tanılama günlükleri](../azure-monitor/essentials/platform-logs-overview.md).

:::image type="content" source="./media/front-door-diagnostics/diagnostic-log.png" alt-text="Tanılama günlükleri":::

Ön kapılarınız için tanılama günlüklerini yapılandırmak için:

1. Azure ön Kapıınızı seçin.

2. **Tanılama ayarları**' nı seçin.

3. **Tanılamayı aç '** ı seçin. Tanılama günlüklerini bir depolama hesabı ölçümleriyle birlikte arşivleyin, bunları bir olay hub 'ına akıtır veya Azure Izleyici günlüklerine gönderin.

Ön kapı şu anda tanılama günlükleri sağlıyor. Tanılama günlükleri, her giriş için aşağıdaki şemaya sahip tek API istekleri sağlar:

| Özellik  | Açıklama |
| ------------- | ------------- |
| BackendHostname | İstek bir arka uca iletilirse Bu alan, arka ucun ana bilgisayar adını temsil eder. İstek yeniden yönlendirilirse veya bir bölgesel önbelleğe iletilirse (yönlendirme kuralı için önbelleğe alma etkinleştirildiğinde) Bu alan boş olur. |
| CacheStatus | Önbelleğe alma senaryolarında bu alan, POP 'ta önbelleğin isabet/isabetsizlik sını tanımlar |
| ClientIp | İsteği yapan istemcinin IP adresi. İstekte bir X-Iletilmiş-for üstbilgisi varsa, Istemci IP 'si aynı üzerinden çekilir. |
| Istemci bağlantı noktası | İsteği yapan istemcinin IP bağlantı noktası. |
| HttpMethod | İstek tarafından kullanılan HTTP yöntemi. |
| HttpStatusCode | Proxy 'den döndürülen HTTP durum kodu. |
| HttpStatusDetails | İstek üzerine sonuçtaki durum. Bu dize değerinin anlamı, bir durum başvuru tablosunda bulunabilir. |
| HttpVersion | İstek veya bağlantı türü. |
| POP | İsteğin bulunduğu kenarın kısa adı. |
| RequestBytes | İstek üst bilgileri ve istek gövdesi dahil olmak üzere HTTP isteği iletisinin bayt cinsinden boyutu. |
| RequestUri | Alınan isteğin URI 'SI. |
| ResponseBytes | Yanıt olarak arka uç sunucusu tarafından gönderilen bayt.  |
| RoutingRuleName | İsteğin eşleştiği yönlendirme kuralının adı. |
| RulesEngineMatchNames | İsteğin eşleştiği kuralların adları. |
| SecurityProtocol | İstek tarafından kullanılan TLS/SSL protokol sürümü veya şifreleme yoksa null. |
| SentToOriginShield </br> (kullanım dışı) * **aşağıdaki bölümde kullanımdan kaldırılması için notlara bakın.**| True ise, isteğin kenar pop 'u yerine kaynak kalkan önbelleğinden yanıtlandığını gösterir. Kaynak kalkan, önbellek isabet oranını artırmak için kullanılan bir üst önbelleğidir. |
| ıreceived Fromclient | Doğru ise, isteğin istemciden geldiği anlamına gelir. Yanlış ise, istek kenarda (alt POP) bir isabetsizlik olur ve kaynak kalkanı (üst POP) ile yanıt verilir. |
| TimeTaken | İsteğin ilk baytının, saniye cinsinden son bayta geçen bayt olan sürenin uzunluğu. |
| TrackingReference | Ön kapıya yönelik olarak sunulan ve istemciye X-Azure-ref üst bilgisi olarak gönderilen bir isteği tanımlayan benzersiz başvuru dizesi. Belirli bir istek için erişim günlüklerindeki ayrıntıları aramak için gereklidir. |
| Kullanıcı | İstemcinin kullandığı tarayıcı türü. |
| ErrorInfo | Bu alan, daha fazla sorun giderme için belirli hata türünü içerir. </br> Olası değerler şunlardır: </br> **NOERROR**: herhangi bir hata bulunamadığını gösterir. </br> **Sertifikahatası**: genel SSL sertifikası hatası.</br> **Certificatenamecheckfailed**: SSL sertifikasındaki konak adı geçersiz veya eşleşmiyor. </br> **Clientconnected**: istemci ağ bağlantısı nedeniyle hata isteme. </br> **Unbelirtilmediclienterror**: genel istemci hatası. </br> **Invalidrequest**: geçersiz istek. Hatalı biçimlendirilmiş üst bilgi, gövde ve URL nedeniyle meydana gelebilir. </br> **Dnsfailure**: DNS hatası. </br> **Dnsnamenotresolved**: sunucu adı veya adresi çözümlenemedi. </br> **Originconnectiondurdurulan**: kaynak bağlantısı aniden durduruldu. </br> **Originconnectionerror**: genel kaynak bağlantı hatası. </br> **Originconnectionreddedildi**: kaynağa olan bağlantı oluşturulamadı. </br> **Originerror**: genel kaynak hatası. </br> **Originınvalidresponse**: Origin geçersiz veya tanınmayan bir yanıt döndürdü. </br> **Origintimeout**: kaynak isteği için zaman aşımı süresi doldu. </br> **Responseheadertoobıg**: kaynak bir yanıt üst bilgisi için çok büyük döndürdü. </br> **Kısıttedıp**: kısıtlanmış IP nedeniyle istek engellendi. </br> **Sslhandshakeerror**: SSL birlikte sallama hatası nedeniyle Origin ile bağlantı kurulamıyor. </br> **UnspecifiedError**: tablodaki hatalara uymayan bir hata oluştu. |

### <a name="sent-to-origin-shield-deprecation"></a>Kaynak kalkan kullanım dışı bırakılmasıyla gönderildi
Ham günlük özelliği **ısenttooriginshield** kullanım dışıdır ve yeni bir alan tarafından alınmış bir **fromclient** tarafından değiştirildi. Zaten kullanım dışı olan alanı kullanıyorsanız, yeni alanı kullanın. 

Ham günlüklerde hem CDN Edge (alt POP) hem de kaynak kalkanı tarafından oluşturulan Günlükler bulunur. Kaynak kalkan, dünya genelinde stratejik olarak bulunan üst düğümlere başvurur. Bu düğümler, kaynak sunucularıyla iletişim kurar ve kaynak yükünü kaynak olarak azaltır. 

Kaynak Shield 'a giden her istek için 2 günlük girişi vardır:

* Kenar düğümleri için bir tane
* Kaynak kalkan için bir tane. 

Uç düğümlerinden ve kaynak kalanlardan çıkış ya da yanıtları ayırt etmek için, uygun verileri almak üzere **ıreceived Fromclient** alanını kullanabilirsiniz. 

Değer false ise, isteğin kaynak Shield 'dan kenar düğümlerine yanıt verdiği anlamına gelir. Bu yaklaşım, ham günlükleri faturalandırma verileriyle karşılaştırmak için geçerlidir. Ücret, çıkış durumundan kenar düğümlerine çıkış için tahakkuk etmemektedir. Ücretler, uç düğümlerinden istemcilere çıkış için ücretlendirilir. 

**Log Analytics kaynak kalkanı üzerinde oluşturulan günlüklerin hariç tutulmasını sağlamak için sorgu örneği kusto.**

`AzureDiagnostics 
| where Category == "FrontdoorAccessLog" and isReceivedFromClient_b == true`

> [!NOTE]
> Çeşitli yönlendirme yapılandırmalarına ve trafik davranışlarına yönelik olarak backendHostname, cacheStatus, ıreceived Fromclient ve POP alanı gibi bazı alanlar farklı değerlerle yanıt verebilir. Aşağıdaki tabloda, bu alanların çeşitli senaryolar için sahip olacağı farklı değerler açıklanmaktadır:

| Senaryolar | Günlük girişi sayısı | POP | BackendHostname | ıreceived Fromclient | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Önbelleğe alma etkin olmayan yönlendirme kuralı | 1 | Edge POP kodu | İsteğin iletildiği arka uç | Doğru | CONFIG_NOCACHE |
| Önbelleğe alma özelliği etkin yönlendirme kuralı. Uç POP 'ta isabetli önbellek okuması | 1 | Edge POP kodu | Olmamalıdır | Doğru | SıNAMASıNA |
| Önbelleğe alma özelliği etkin yönlendirme kuralı. Uç POP 'ta önbellek isabetsizliği, ancak üst önbellek AÇıSıNDA önbellek okuması | 2 | 1. Edge POP kodu</br>2. üst önbellek POP kodu | 1. üst önbellek POP ana bilgisayar adı</br>2. boş | 1. doğru</br>2. yanlış | 1. ISABETSIZLIK</br>2. ISABET |
| Önbelleğe alma özelliği etkin yönlendirme kuralı. Uç açılır penceresinde önbellek isabetsizliği, ancak üst önbellek AÇıSıNı kısmı önbellek okuması | 2 | 1. Edge POP kodu</br>2. üst önbellek POP kodu | 1. üst önbellek POP ana bilgisayar adı</br>2. önbelleğin doldurulmaya yardımcı olan arka uç | 1. doğru</br>2. yanlış | 1. ISABETSIZLIK</br>2. PARTIAL_HIT |
| Önbelleğe alma özelliği etkin yönlendirme kuralı. Önbellek PARTIAL_HIT uç açılır, ancak üst önbellek AÇıSıNDA önbellek okuması | 2 | 1. Edge POP kodu</br>2. üst önbellek POP kodu | 1. Edge POP kodu</br>2. üst önbellek POP kodu | 1. doğru</br>2. yanlış | 1. PARTIAL_HIT</br>2. ISABET |
| Önbelleğe alma özelliği etkin yönlendirme kuralı. Hem Edge hem de üst önbellekteki önbellek isabetsiz önbellek POPP | 2 | 1. Edge POP kodu</br>2. üst önbellek POP kodu | 1. Edge POP kodu</br>2. üst önbellek POP kodu | 1. doğru</br>2. yanlış | 1. ISABETSIZLIK</br>2. ISABETSIZLIK |

> [!NOTE]
> Önbelleğe alma senaryolarında, bir isteğin bazı baytları ön kapı kenarından veya kaynak kalkan önbelleğinden sunulduğunda, bazı baytlar büyük nesneler için kaynaktan sunulduğunda, önbellek durumu değeri partial_hit olur.

Ön kapı, nesne parçalama adlı bir teknik kullanır. Büyük bir dosya istendiğinde, ön kapı dosyanın kaynaktan daha küçük parçalarını alır. Ön kapı POP sunucusu, istenen dosyanın tam veya bayt aralıklarını aldıktan sonra, ön kapı uç sunucusu, dosyayı sıfırdan 8 MB 'lık öbeklerdeki kaynaktan ister.

Öbek, ön kapılı bir kenara ulaştıktan sonra önbelleğe alınır ve anında kullanıcıya sunulur. Ön kapı daha sonra bir sonraki öbeği paralel olarak önceden getirir. Bu önceden getirme, içeriğin kullanıcının önüne bir öbek kalmasını sağlar ve gecikme süresini azaltır. Bu işlem, dosyanın tamamı indirilene kadar devam eder (isteniyorsa), tüm bayt aralıkları kullanılabilir (isteniyorsa) veya istemci bağlantıyı kapatır. Bayt aralığı isteği hakkında daha fazla bilgi için bkz. RFC 7233. Ön kapı, alındıkları tüm öbekleri önbelleğe alır. Tüm dosyanın ön kapı önbelleğinde önbelleğe alınması gerekmez. Dosya veya bayt aralıklarına yönelik istekleri, ön kapı önbelleğinden sunulur. Tüm parçalar ön kapıda önbelleğe alınmamışsa, kaynaktan öbekleri istemek için önceden getirme kullanılır. Bu iyileştirme, kaynak sunucunun, bayt aralığı isteklerini destekleme özelliğine bağlıdır. Kaynak sunucu, bayt aralığı isteklerini desteklemiyorsa, bu iyileştirme etkili olmaz.

## <a name="next-steps"></a>Sonraki adımlar

- [Ön kapı profili oluşturma](quickstart-create-front-door.md)
- [Ön kapı nasıl kullanılır?](front-door-routing-architecture.md)
