---
title: Azure ön kapı Standart/Premium (Önizleme) günlüğe kaydetme
description: Bu makalede, Azure ön kapı Standart/Premium 'da günlüğe kaydetmenin nasıl çalıştığı açıklanır.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 03/15/2021
ms.author: duau
ms.openlocfilehash: 531f4a9c9f535779e451ca316a8a5867f6cdaba5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573906"
---
# <a name="azure-front-door-standardpremium-preview-logging"></a>Azure ön kapı Standart/Premium (Önizleme) günlüğe kaydetme

> [!Note]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Burada](../front-door-overview.md)görüntüleyin.

Azure ön kapısı, ön kapılarınızı izlemenize, izlemenize ve hata ayıklamanıza yardımcı olmak için farklı günlük kaydı sağlar. 

* Erişim günlükleri, AFD tarafından alınan her istek hakkında ayrıntılı bilgiler verir ve erişim düzenlerini analiz edip izlemenize ve hata ayıklamanıza yardımcı olur. 
* Etkinlik günlükleri, Azure kaynakları üzerinde gerçekleştirilen işlemlere görünürlük sağlar.  
* Durum araştırma günlükleri, her başarısız yoklama için günlüklere yönelik Günlükler sağlar. 
* Web uygulaması güvenlik duvarı (WAF) günlükleri, bir Azure ön kapısının algılama veya önleme modundan birinde günlüğe kaydedilen isteklerin ayrıntılı bilgilerini sağlar. WAF ile yapılandırılan özel bir etki alanı, bu Günlükler aracılığıyla da görüntülenebilir.

> [!IMPORTANT]
> Azure ön kapı Standart/Premium (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Erişim günlükleri, sistem durumu araştırma günlükleri ve WAF günlükleri varsayılan olarak etkin değildir. Günlüğe kaydetmeyi etkinleştirmek için aşağıdaki adımları kullanın. Etkinlik günlüğü girişleri varsayılan olarak toplanır ve bunları Azure portalda görüntüleyebilirsiniz. Günlüklerde birkaç dakikaya kadar gecikme olabilir. 

Günlüklerinizi depolamak için kullanabileceğiniz üç seçenek vardır: 

* **Depolama hesabı:** Depolama hesapları, günlüklerin daha uzun bir süre boyunca depolandığı ve gerektiğinde İncelenme senaryolarında en iyi şekilde kullanılır. 
* **Olay Hub 'ları:** Olay Hub 'ları, diğer güvenlik bilgileri ve olay yönetimi (SıEM) araçları veya dış veri depolarıyla tümleştirmek için harika bir seçenektir. Örneğin: splunk/Dataköpek/Sumo. 
* **Azure Log Analytics:** Azure Izleyici 'de Azure Log Analytics, Azure ön kapısının genel gerçek zamanlı izleme ve analiz açısından en iyi şekilde kullanılır.

## <a name="configure-logs"></a>Günlükleri yapılandırma

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure ön kapısının Standart/Premium aramasını yapın ve Azure ön kapısının profilini seçin.

1. Profilde, **izleme**' ye gidin, **Tanılama ayarı**' nı seçin. **Tanılama ayarı ekle**’yi seçin.

   :::image type="content" source="../media/how-to-logging/front-door-logging-1.png" alt-text="Tanılama ayarları giriş sayfasının ekran görüntüsü.":::

1. **Tanılama ayarları**' nın altında, **Tanılama ayarları adı** için bir ad girin.

1. **FrontDoorAccessLog**, **FrontDoorHealthProbeLog** ve **FrontDoorWebApplicationFirewallLog** **günlüğünü** seçin.

1.  **Hedef ayrıntılarını** seçin. Hedef seçenekleri şunlardır: 

    * **Log Analytics’e gönderme**
        * *Aboneliği* ve *Log Analytics çalışma alanını* seçin.
    * **Bir depolama hesabına Arşivle**
        * *Aboneliği* ve *Depolama hesabını* seçin. ve **bekletme (gün)** ayarlayın.
    * **Olay hub’ına akış yapma**
        *  *Abonelik, Olay Hub 'ı ad alanı, Olay Hub 'ı adı (isteğe bağlı)* ve *Olay Hub 'ı ilke adı*' nı seçin. 

     :::image type="content" source="../media/how-to-logging/front-door-logging-2.png" alt-text="Tanılama Ayarları sayfasının ekran görüntüsü.":::

1. **Kaydet**'e tıklayın.

## <a name="access-log"></a>Erişim günlüğü

Azure ön kapısı şu anda her bir girdiyle aşağıdaki şemaya sahip ve JSON biçiminde oturum açan her bir API isteğini aşağıda gösterildiği gibi sunmaktadır. 

| Özellik | Açıklama |
|----------|-------------| 
| TrackingReference | AFD tarafından sunulan ve ayrıca istemciye X-Azure-ref üst bilgisi olarak gönderilen bir isteği tanımlayan benzersiz başvuru dizesi. Belirli bir istek için erişim günlüklerindeki ayrıntıları aramak için gereklidir. |
| Saat | AFD kenarının istemciye (UTC olarak) istek aldığı tarih ve saat. |
| HttpMethod | İstek tarafından kullanılan HTTP yöntemi: DELETE, GET, HEAD, OPTIONS, PATCH, POST veya PUT. |
| HttpVersion | İzleyicinin istekte belirtilen HTTP sürümü. |
| RequestUri | Alınan isteğin URI 'SI. Bu alan tam bir düzen, bağlantı noktası, etki alanı, yol ve sorgu dizesidir |
| HostName | İstemciden gelen istekteki ana bilgisayar adı. Özel etki alanlarını etkinleştirir ve joker (*. contoso.com) etki alanına sahipseniz, ana bilgisayar adı a.contoso.com ' dir. Azure ön kapı etki alanı (contoso.azurefd.net) kullanıyorsanız, ana bilgisayar adı contoso.azurefd.net ' dir. |
| RequestBytes | İstek üst bilgileri ve istek gövdesi dahil olmak üzere HTTP isteği iletisinin bayt cinsinden boyutu. Üst bilgiler de dahil olmak üzere, isteğin dahil olduğu verilerin bayt sayısı. |
| ResponseBytes | Yanıt olarak arka uç sunucusu tarafından gönderilen bayt. |
| Kullanıcı | İstemcinin kullandığı tarayıcı türü. |
| ClientIp | Özgün isteği yapan istemcinin IP adresi. İstekte bir X-Iletilmiş-for üstbilgisi varsa, Istemci IP 'si aynı üzerinden çekilir. |
| SocketIp | AFD Edge 'e doğrudan bağlantının IP adresi. İstemci isteği göndermek için bir HTTP proxy veya yük dengeleyici kullandıysanız, Socketıp değeri proxy veya yük dengeleyicinin IP adresidir. |
| Gecikme süresi | AFD sınır sunucusundan geçen sürenin süresi, bir istemcinin istemci isteğini, milisaniye cinsinden son yanıt baytını gönderdiği zamana göre alır. Bu alan, hesap ağ gecikmesi ve TCP arabelleğe alma özelliğini almaz. |
| RequestProtocol | İstemcinin istekte belirttiği protokol: HTTP, HTTPS. |
| SecurityProtocol | İstek tarafından kullanılan TLS/SSL protokol sürümü veya şifreleme yoksa null. Olası değerler şunlardır: SSLv3, TLSv1, TLSv 1.1, TLSv 1.2 |
| SecurityCipher | Istek Protokolü değeri HTTPS olduğunda, bu alan istemci tarafından anlaşılan TLS/SSL şifrelemesini ve şifreleme için AFD ' ı belirtir. |
| Uç Nokta | AFD uç noktasının etki alanı adı, örneğin, contoso.z01.azurefd.net |
| HttpStatusCode | AFD 'dan döndürülen HTTP durum kodu. |
| Cağımız | Kullanıcı isteğine yanıt veren kenar açılır.  |
| Önbellek durumu | İsteğin önbelleğe alma sırasında CDN hizmeti tarafından nasıl işlendiği hakkında durum kodu sağlar. Olası değerler: HTTP isteği AFD Edge POP önbelleğinden sağlandı. <br> **Isabetsizlik**: http isteği kaynaktan sunulandı. <br/> **PARTIAL_HIT**: bir isteğin bazı baytları, nesne parçalama senaryoları için kaynaktan sunulan bazı baytlar AFD Edge pop önbelleğinden sunuldu. <br> **CACHE_NOCONFIG**: atlama senaryosu dahil olmak üzere istekleri önbelleğe alma olmadan iletme. <br/> **PRIVATE_NOSTORE**: müşteriler tarafından önbelleğe alma ayarlarında yapılandırılmış önbellek yok. <br> **REMOTE_HIT**: istek üst düğüm önbelleği tarafından sağlandı. <br/> **N/A**:* * imzalı URL ve kural kümesi tarafından reddedilen istek. |
| Matchedkurallarını Kümesiadı | İşlenen kuralların adları. |
| Routetablename | İsteğin eşleştiği yolun adı. |
| Istemci bağlantı noktası | İsteği yapan istemcinin IP bağlantı noktası. |
| Başvuran | İsteğin kaynaklandığı sitenin URL 'SI. |
| TimetoFirstByte | AFD 'dan milisaniye olarak geçen sürenin uzunluğu, Azure ön kapısının ölçüldüğü şekilde istemciye ilk baytın gönderildiği zamana kadar zaman alır. Bu özellik istemci verilerini ölçmez. |
| ErrorInfo | Bu alan, her yanıt için hata belirtecinin ayrıntılı bilgilerini sağlar. <br> **NOERROR**: herhangi bir hata bulunamadığını gösterir. <br> **Sertifikahatası**: genel SSL sertifikası hatası. <br> **Certificatenamecheckfailed**: SSL sertifikasındaki konak adı geçersiz veya eşleşmiyor. <br> **Clientconnected**: istemci ağ bağlantısı nedeniyle hata isteme. <br> **Clientgeobkilitlendi**: ISTEMCI, IP 'nin coğrafi konumu nedeniyle engellendi. <br> **Unbelirtilmediclienterror**: genel istemci hatası. <br> **Invalidrequest**: geçersiz istek. Hatalı biçimlendirilmiş üst bilgi, gövde ve URL nedeniyle meydana gelebilir. <br> **Dnsfailure**: DNS hatası. <br> **Dnstimeout**: arka ucu çözecek DNS sorgusu zaman aşımına uğradı. <br> **Dnsnamenotresolved**: sunucu adı veya adresi çözümlenemedi. <br> **Originconnectiondurdurulan**: kaynak bağlantısı anormal olarak kesildi. <br> **Originconnectionerror**: genel kaynak bağlantı hatası. <br> **Originconnectionreddedildi**: kaynağa bağlantı kurulmadı. <br> **Originerror**: genel kaynak hatası. <br> **Originınvalidrequest**: kaynağa geçersiz bir istek gönderildi. <br> **Responseheadertoobıg**: kaynak, yanıt üst bilgisinin çok büyük bir kısmını döndürdü. <br> **Originınvalidresponse**:* * Origin geçersiz veya tanınmayan bir yanıt döndürdü. <br> **Origintimeout**: kaynak isteği için zaman aşımı süresi doldu. <br> **Responseheadertoobıg**: kaynak, yanıt üst bilgisinin çok büyük bir kısmını döndürdü. <br> **Kısıttedıp**: kısıtlanmış IP nedeniyle istek engellendi. <br> **Sslhandshakeerror**: SSL birlikte sallama hatası nedeniyle Origin ile bağlantı kurulamıyor. <br> **Sslinvalidrootca**: RootCA geçersizdi. <br> **SSLInvalidCipher**: ŞIFRE, https bağlantısının kurulduğu için geçersizdi. <br> **Originconnectiondurdurulan**: kaynak bağlantısı anormal olarak kesildi. <br> **Originconnectionreddedildi**: kaynağa bağlantı kurulmadı. <br> **UnspecifiedError**: tablodaki hatalara uymayan bir hata oluştu. |
| OriginURL | İsteklerin gönderilme noktasının tam URL 'SI. Scheme, ana bilgisayar üst bilgisi, bağlantı noktası, yol ve sorgu dizesinden oluşur. <br> **URL yeniden yazma**: kural KÜMESINDE bir URL yeniden yazma kuralı varsa, yol yeniden yazma yolunu ifade eder. <br> **Uç açılır penceresinde önbellek** Kenar AÇıSıNıN önbellek okuması ise, kaynak yok. <br> **Büyük istek** İstenen içerik, kaynağa geri dönerek birden çok öbekli istek ile büyükse, bu alan kaynağa ilk isteği karşılıyorum. Daha fazla bilgi için bkz. daha fazla ayrıntı için nesne parçalama. |
| Originıp | İsteği sunan kaynak IP 'si. <br> **Edge pop üzerinde önbellek okuması** Kenar AÇıSıNıN önbellek okuması ise, kaynak yok. <br> **Büyük istek** İstenen içerik, kaynağa geri dönerek birden çok öbekli istek ile büyükse, bu alan kaynağa ilk isteği karşılıyorum. Daha fazla bilgi için bkz. daha fazla ayrıntı için nesne parçalama. |
| OriginName| Kaynağa tam DNS adı (kaynak URL 'de ana bilgisayar adı). <br> **Edge pop üzerinde önbellek okuması** Kenar AÇıSıNıN önbellek okuması ise, kaynak yok. <br> **Büyük istek** İstenen içerik, kaynağa geri dönerek birden çok öbekli istek ile büyükse, bu alan kaynağa ilk isteği karşılıyorum. Daha fazla bilgi için bkz. daha fazla ayrıntı için nesne parçalama. |

## <a name="health-probe-log"></a>Durum araştırma günlüğü

Durum araştırma günlükleri, kaynağı tanılamanıza yardımcı olması için başarısız olan her araştırma için günlüğe kaydetme sağlar.Günlükler size kaynağı hizmete geri getirmek için kullanabileceğiniz bilgiler sağlar. Bu günlüğün yararlı olabilecek bazı senaryolar şunlardır:

* Azure ön kapısı trafiğinin bazı kaynaklardan bazılarına gönderildiğini fark etmiş olursunuz. Örneğin, dört kaynağa yalnızca üç çıkış trafiği alıyor. Kaynakları yoklamaların alıp almadığını ve hatanın nedeni olmadığını bilmeniz gerekir.  

* % Kaynak sistem durumunun beklenenden düşük olduğunu fark etmişsinizdir ve hangi kaynağın başarısız olduğunu ve hatanın nedenini bilmeniz gerekir.

### <a name="health-probe-log-properties"></a>Durum araştırma günlüğü özellikleri

Her durum araştırma günlüğü, aşağıdaki şemaya sahiptir.

| Özellik | Açıklama |
| --- | --- |
| Healthprobeıd  | İsteği tanımlamak için benzersiz bir KIMLIK. |
| Saat | Araştırma tamamlanma süresi |
| HttpMethod | Durum araştırma isteği tarafından kullanılan HTTP yöntemi. Değerler, durum araştırma yapılandırmalarına bağlı olarak GET ve HEAD değerlerini içerir. |
| Sonuç | Durum araştırma durumu, değer başarı ve diğer hata metni. |
| HttpStatusCode  | Kaynaktan döndürülen HTTP durum kodu. |
| ProbeURL (hedef) | İsteklerin gönderilme noktasının tam URL 'SI. Düzen, ana bilgisayar üst bilgisi, yol ve sorgu dizesinden oluşur. |
| OriginName  | İsteklerin gönderilme kaynağı. Bu alan, kaynak FDQN olarak yapılandırılmışsa ilgilenilen kaynakları bulmaya yardımcı olur. |
| POP | Araştırma isteğini gönderen kenar açılır öğesi. |
| Kaynak IP | Hedef kaynak IP 'si. Bu alan, FDQN kullanarak kaynak yapılandırırsanız ilgilenilen kaynakları bulmak için yararlıdır. |
| TotolaLatency | AFDX Edge 'ten gelen süre, isteği kaynağa, AFDX Edge 'e yapılan son yanıtı gönderir. |
| ConnectionLatency| HTTP araştırma isteğini kaynağa göndermek için TCP bağlantısını ayarlarken harcanan süre. | 
| DNSResolution gecikmesi | Kaynak, IP yerine bir FDQN olarak yapılandırıldıysa, DNS çözümlemesi sırasında harcanan süre süresi. Kaynak IP olarak yapılandırıldıysa, yok. |

### <a name="health-probe-log-sample-in-json"></a>JSON 'da durum araştırma günlüğü örneği

`{ "records": [ { "time": "2021-02-02T07:15:37.3640748Z",
      "resourceId": "/SUBSCRIPTIONS/27CAFCA8-B9A4-4264-B399-45D0C9CCA1AB/RESOURCEGROUPS/AFDXPRIVATEPREVIEW/PROVIDERS/MICROSOFT.CDN/PROFILES/AFDXPRIVATEPREVIEW-JESSIE",
      "category": "FrontDoorHealthProbeLog",
      "operationName": "Microsoft.Cdn/Profiles/FrontDoorHealthProbeLog/Write",
      "properties": { "healthProbeId": "9642AEA07BA64675A0A7AD214ACF746E",
        "POP": "MAA",
        "httpVerb": "HEAD",
        "result": "OriginError",
        "httpStatusCode": "400",
        "probeURL": "http://afdxprivatepreview.blob.core.windows.net:80/",
        "originName": "afdxprivatepreview.blob.core.windows.net",
        "originIP": "52.239.224.228:80",
        "totalLatencyMilliseconds": "141",
        "connectionLatencyMilliseconds": "68",
        "DNSLatencyMicroseconds": "1814" } } ]
} `

## <a name="activity-logs"></a>Etkinlik Günlükleri

Etkinlik günlükleri, Azure ön kapısı Standart/Premium üzerinde gerçekleştirilen işlemler hakkında bilgi sağlar. Günlükler, Azure ön Kapıında yazma işleminin ne zaman ve ne zaman yapıldığı hakkında ayrıntılı bilgiler içerir. 

> [!NOTE]
> Etkinlik günlükleri Al işlemlerini içermez. Ayrıca, Azure portal veya orijinal yönetim API 'sini kullanarak gerçekleştirdiğiniz işlemleri de içermez. 

Ön kapıdaki etkinlik günlüklerine veya Azure Izleyici 'deki Azure kaynaklarınızın tüm günlüklerine erişin.

Etkinlik günlüklerini görüntülemek için:

1. Ön kapı profilinizi seçin.

1. **Etkinlik günlüğü** ' nü seçin.

1. Bir filtreleme kapsamı seçin ve ardından **Uygula**' yı seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ön kapısının Standart/Premium (Önizleme) raporları](how-to-reports.md)hakkında bilgi edinin.
- [Azure ön kapısı Standart/Premium (Önizleme) gerçek zamanlı izleme ölçümleri](how-to-monitor-metrics.md)hakkında bilgi edinin.
