---
title: Azure endüstriyel IoT bileşenlerini yapılandırma
description: Bu öğreticide, yapılandırmanın varsayılan değerlerini değiştirmeyi öğreneceksiniz.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 348276a71b2227063374da6455445118fcb00fcf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787752"
---
# <a name="tutorial-configure-the-industrial-iot-components"></a>Öğretici: Endüstriyel IoT bileşenlerini yapılandırma

Dağıtım betiği, varsayılan değerleri kullanarak tüm bileşenlerin birbirleriyle çalışacak şekilde otomatik olarak yapılandırır. Ancak, varsayılan değerlerin ayarları gereksinimlerinizi karşılayacak şekilde değiştirilebilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bileşenlerin yapılandırmasını özelleştirme


Bileşenler için daha ilgili özelleştirme ayarlarından bazıları aşağıda verilmiştir:
* IoT Hub
    * Ağ → genel erişimi: Internet erişimini yapılandırma (örneğin, IP filtreleri)
    * Networking → özel uç nokta bağlantıları: Internet üzerinden erişilemeyen ve diğer Azure hizmetleri veya şirket içi cihazlar tarafından dahili olarak tüketilen bir uç nokta oluşturun (örneğin, bir VPN bağlantısı üzerinden)
    * IoT Edge: OPC UA sunucularına bağlı olan Edge cihazlarının yapılandırmasını yönetme 
* Cosmos DB
    * Verileri genel olarak çoğaltma: verileri yapılandırma-artıklık
    * Güvenlik Duvarı ve sanal ağlar: Internet ve VNET erişimini ve IP filtrelerini yapılandırma
    * Özel uç nokta bağlantıları: Internet üzerinden erişilemeyen bir uç nokta oluşturma 
* Key Vault
    * Gizlilikler: platform ayarlarını yönetme
    * Erişim ilkeleri: hangi uygulamaların ve kullanıcıların Key Vault veriye erişebileceğini ve ağ, güvenlik duvarı, VNET ve özel uç noktalar üzerinde gerçekleştirmesine izin verilen işlemleri (örneğin, okuma, yazma, listeleme, silme) yönetme
* Azure Active Directory (AAD) → Uygulama kayıtları
    * <APP_NAME>-Web → Authentication: kimlik doğrulaması başarılı olduktan sonra giriş sayfası olarak kullanılabilecek URI 'lerin listesi olan yanıt URI 'Lerini yönetin. Dağıtım betiği, AAD yönetici haklarının olmaması gibi belirli senaryolar altında otomatik olarak yapılandıramayabilir. Web uygulamasının ana bilgisayar adını değiştirirken URI 'Leri eklemek veya değiştirmek isteyebilirsiniz, örneğin, hata ayıklama için localhost tarafından kullanılan bağlantı noktası numarası
* App Service
    * Yapılandırma: Hizmetleri veya Kullanıcı arabirimini denetleyen ortam değişkenlerini yönetme
* Sanal makine
    * Ağ: desteklenen ağları ve güvenlik duvarı kurallarını yapılandırma
    * Seri konsol: öngörü almak veya hata ayıklama için SSH erişimi, dağıtım betiği çıktısından kimlik bilgilerini alın veya parolayı sıfırlayın
* IoT Hub → IoT Edge
    * Hub 'a erişebilen IoT Edge cihazların kimliklerini yönetin, hangi modüllerin yüklü olduğunu ve hangi yapılandırmayı kullandıkları, örneğin OPC yayımcısı için kodlama parametrelerini yapılandırın
* IoT Hub → IoT Edge → \<DEVICE> → set modules → OpcPublisher (yalnızca tek başına OPC yayımcı işlemi için)


## <a name="configuration-options"></a>Yapılandırma seçenekleri

|Yapılandırma seçeneği (toplu/tam ad)    |    Description   |
|----------------------------------------------|------------------|
PF/publishfile |Yayımlanacak düğümleri yapılandırmak için dosya adı. Bu seçenek belirtilmişse OPC yayımcısını tek başına moda koyar.
LF/logfile |Kullanılacak günlük dosyasının dosya adı.
ll/LogLevel |Kullanılacak günlük düzeyi (izin verilen: önemli, hata, uyarı, bilgi, hata ayıklama, ayrıntılı).
Me/MessageEncoding |Giden iletiler için mesajlaşma kodlaması izin verilen değerler: JSON, UADP
aa/messagingmode |Giden iletiler için mesajlaşma modu izin verilen değerler: PubSub, Samples
FM/fullfeaturedmessage |İletiler için tam özellikli mod (tüm alanlar doldurulur). Varsayılan değer ' true ', eski uyumluluk için ' false ' kullanımı
aa/oto kabul et |Yayımcı, bir bağlantısı olan tüm sunucuları güvenilir
BS/BatchSize |Toplu işlem için önbelleğe alınacak OPC UA veri değiştirme iletilerinin sayısı.
sı/iothubsendınterval |Saniye cinsinden tetikleyici toplu işlem aralığı.
MS/iothubmessagesize |(IoT D2C) iletisinin en büyük boyutu.
Om/maxoutgressmessages |(IoT D2C) ileti çıkış arabelleğinin en büyük boyutu.
di/diagnosticsınterval |Yayımcı tanılama bilgilerini belirtilen aralıkta saniye cinsinden gösterir (günlük düzeyi bilgisi gerekir). -1 Uzaktan tanılama günlüğü ve tanılama çıkışını devre dışı bırakır
lt/logflogtimespan |Günlük dosyası boşaltılırken saniye cinsinden zaman aralığı.
IH/ıothubprotocol |Hub ile iletişim kurmak için kullanılacak protokol. İzin verilen değerler: AmqpOverTcp, AmqpOverWebsocket, MqttOverTcp, MqttOverWebsocket, AMQP, MQTT, TCP, WebSocket, any
HB/heartbeatinterval |Yayımcı, sinyal aralığı ayarı olmayan düğümlerin sinyal aralığı ayarında varsayılan değer olarak bu değeri kullanıyor.
ot/OperationTimeout |MS 'de yayımcı OPC UA istemcisinin işlem zaman aşımı.
ol/opcmaxstringlen |OPC dizesinin en fazla uzunluğu, iletme/alma olabilir.
Oi/opcsamplingınterval |Sunucuları örnek değerlere istemek için milisaniye cinsinden varsayılan değer
Op/opcpublishingınterval |OPC UA sunucusuna karşı aboneliklerin yayımlama aralığı ayarı için milisaniye cinsinden varsayılan değer.
CT/createsessiontimeout |Yayımcının göndermesinin zaman aralığı, bağlı olduğu uç noktalarda OPC sunucularına Saniyeler içinde canlı iletiler gönderir.
KT/keepalivethresholt |Oturumun bağlantısı kesilmeden önce bir sunucunun kaçırılan etkin tutma paketlerinin sayısını belirtin.
TM/trustken |Yayımcı sertifikası, güvenilen depoya otomatik olarak konur.
at/appcertstoyeniden yazma |Kendi uygulama sertifikası depolama türü (izin verilen: Dizin, X509Store).


## <a name="next-steps"></a>Sonraki adımlar
Artık yapılandırmanın varsayılan değerlerini değiştirme hakkında daha fazla öğrendiğinize göre, 

> [!div class="nextstepaction"]
> [ADX 'e IIoT verisi çekme](tutorial-industrial-iot-azure-data-explorer.md)

> [!div class="nextstepaction"]
> [Time Series Insights kullanarak verileri görselleştirme ve çözümleme](tutorial-visualize-data-time-series-insights.md)
