---
title: Tanılama günlüklerini kullanarak Azure VPN Gateway sorunlarını giderme
description: Tanılama günlüklerini kullanarak Azure VPN Gateway sorunlarını giderme
services: vpn-gateway
author: stegag
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/15/2021
ms.author: stegag
ms.openlocfilehash: 232e084e44696c6aa88a9dd33092c48a96e35f85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772017"
---
# <a name="troubleshoot-azure-vpn-gateway-using-diagnostic-logs"></a>Tanılama günlüklerini kullanarak Azure VPN Gateway sorunlarını giderme

Bu makale, VPN Gateway tanılamaları için kullanılabilen farklı günlükleri anlamanıza ve VPN Gateway sorunlarını etkin bir şekilde gidermek için bunların nasıl kullanılacağını anlamanıza yardımcı olur.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Aşağıdaki Günlükler Azure 'da kullanılabilir:

|***Ad** _ | _ *_Açıklaması_** |
|---        | ---               |
|**GatewayDiagnosticLog** | Ağ Geçidi yapılandırma olayları, birincil değişiklikler ve bakım olayları için tanılama günlükleri içerir. |
|**Tüneldiagnosticlog** | Tünel durumu değişikliği olaylarını içerir. Tünel bağlantısı/bağlantı kesme olayları, uygunsa durum değişikliği için özetlenen bir nedene sahiptir. |
|**RouteDiagnosticLog** | Ağ geçidinde gerçekleşen statik rotalara ve BGP olaylarına yapılan değişiklikleri günlüğe kaydeder. |
|**Ikediagnosticlog** | Ağ geçidinde ıKE denetim iletilerini ve olaylarını günlüğe kaydeder. |
|**P2SDiagnosticLog** | Ağ geçidinde Noktadan siteye denetim iletilerini ve olayları günlüğe kaydeder. |

Bu tablolarda birkaç sütun kullanılabilir olduğuna dikkat edin. Bu makalede, daha kolay günlük tüketimine yönelik yalnızca en ilgili olanları sunuyoruz.

## <a name="set-up-logging"></a><a name="setup"></a>Günlüğe kaydetmeyi ayarlama

Azure Log Analytics kullanarak Azure VPN Gateway tanılama günlüğü olaylarını ayarlama hakkında bilgi edinmek için, bkz. [VPN Gateway tanılama günlüğü olayları üzerinde uyarıları ayarlama](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log).

## <a name="gatewaydiagnosticlog"></a><a name="GatewayDiagnosticLog"></a>GatewayDiagnosticLog

Yapılandırma değişiklikleri **Gatewaydiagnosticlog** tablosunda denetlenir. Yürütmeniz gereken değişikliklerin günlüklere yansıtılması birkaç dakika sürebilir.

Burada, başvuru olarak örnek bir sorgunuz vardır.

```
AzureDiagnostics  
| where Category == "GatewayDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup  
| sort by TimeGenerated asc
```

**Gatewaydiagnosticlog** üzerinde bu sorgu size birden çok sütun gösterecektir.

|***Ad** _ | _ *_Açıklaması_** |
|---        | ---               |
|**TimeGenerated** | UTC saat diliminde her olayın zaman damgası.|
|**OperationName** |meydana gelen olay. *Setgatewayconfiguration, SetConnectionConfiguration, HostMaintenanceEvent, GatewayTenantPrimaryChanged, MigrateCustomerSubscription, GatewayResourceMove, ValidateGatewayConfiguration* seçeneklerinden biri olabilir.|
|**İleti** | işlemin ayrıntıları ve başarılı/başarısız sonuçları listeler.|

Aşağıdaki örnekte, yeni bir yapılandırma uygulandığında günlüğe kaydedilen etkinlik gösterilmektedir:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-26-set-gateway.png" alt-text="GatewayDiagnosticLog içinde görülen bir set Gateway Işlemi örneği.":::


Bir VPN Gateway veya yerel ağ geçidi üzerinde her yapılandırma değiştirildiğinde bir SetGatewayConfiguration 'ın günlüğe kaydedileceğini görürsünüz.
**Gatewaydiagnosticlog** tablosunun sonuçları **, tünelde** bir yapılandırma değiştiği veya bir bakımın gerçekleştiği anda, bir tünel bağlantı hatasının başlatıldığını belirlememize yardımcı olabilir. Bu durumda, olası kök nedenine yönelik harika bir işaretçimiz vardır.

## <a name="tunneldiagnosticlog"></a><a name="TunnelDiagnosticLog"></a>Tüneldiagnosticlog

Tünelle bağlantı durumlarını denetlemek için **Tunneldiagnosticlog** tablosu çok yararlı olur.

Burada, başvuru olarak örnek bir sorgunuz vardır.

```
AzureDiagnostics
| where Category == "TunnelDiagnosticLog"
| project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup
| sort by TimeGenerated asc
```

**Tüneldiagnosticlog** üzerinde bu sorgu size birden çok sütun gösterecektir.


|***Ad** _ | _ *_Açıklaması_** |
|---        | ---               |
|**TimeGenerated** | UTC saat diliminde her olayın zaman damgası.|
|**OperationName** | meydana gelen olay. Bu, *tünelden bağlı* veya *tünelden bağlantısı kesilebilir*.|
| **Örnek \_ s** | olayı tetikleyen Ağ Geçidi rol örneği. Bu, \_ \_ \_ \_ ağ geçidinin iki örneğinin adı olan 0 veya GatewayTenantWorker ' da gatewaytenantworker olabilir.|
| **Kaynak** | VPN ağ geçidinin adını gösterir. |
| **Kaynak** | ağ geçidinin bulunduğu kaynak grubunu gösterir.|


Örnek çıktı:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-16-tunnel-connected.png" alt-text="Tüneldiagnosticlog içinde görülen tünel bağlantılı olay örneği.":::


**Tüneldiagnosticlog** , beklenmeyen VPN bağlantısı ile ilgili geçmiş olayların giderilmesi için çok faydalı olur. Hafif doğası, büyük zaman aralıklarını birkaç güne kadar az çabayla analiz etme olasılığını sunar.
Yalnızca bir bağlantının zaman damgasını tanımladıktan sonra, IPSec ile ilgili olan bağlantıları garanti altına almak için **ıkediagnosticlog** tablosunun daha ayrıntılı çözümlemesine geçiş yapabilirsiniz.


Bazı sorun giderme ipuçları:
- Bir ağ geçidi örneğinde bir bağlantı kesilmesi olayı görürseniz, **farklı** ağ geçidi örneğindeki bir bağlantı olayı, bir ağ geçidi yük devretmeye bakıyorsunuzdur. Bu, genellikle bir ağ geçidi örneğindeki bakım nedeniyle beklenen bir davranıştır. Bu davranış hakkında daha fazla bilgi edinmek için bkz. [Azure VPN Gateway artıklığı hakkında](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable#about-azure-vpn-gateway-redundancy).
- Azure tarafında kasıtlı olarak bir ağ geçidi sıfırlamayı çalıştırırsanız aynı davranış gözlenir. Bu, etkin ağ geçidi örneğinin yeniden başlatılmasına neden olur. Bu davranış hakkında daha fazla bilgi edinmek için [VPN Gateway sıfırlama](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-resetgw-classic)bölümüne bakın.
- Tek bir ağ geçidi örneğinde bir bağlantı olayı ve ardından **aynı** ağ geçidi örneği üzerinde birkaç saniye içinde bir bağlantı olayı görürseniz, bir ağ hatası nedeniyle bir DPD zaman aşımı veya şirket içi cihaz tarafından hatalı bağlantı kesilmesi olabilir.

## <a name="routediagnosticlog"></a><a name="RouteDiagnosticLog"></a>RouteDiagnosticLog

**Routediagnosticlog** tablosu, statik olarak değiştirilen YOLLARıN veya BGP aracılığıyla alınan yolların etkinliğini izler.

Burada, başvuru olarak örnek bir sorgunuz vardır.

```
AzureDiagnostics
| where Category == "RouteDiagnosticLog"
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

**Routediagnosticlog** üzerinde bu sorgu size birden çok sütun gösterecektir.

|***Ad** _ | _ *_Açıklaması_** |
|---        | ---               |
|**TimeGenerated** | UTC saat diliminde her olayın zaman damgası.|
|**OperationName** | meydana gelen olay. *Staticrouteupdate, BgpRouteUpdate, BgpConnectedEvent, BgpDisconnectedEvent* olabilir.|
| **İleti** | işlemin ayrıntıları.|

Çıktı, bağlı/bağlantısı kesilen ve alınan rotalar ile ilgili yararlı bilgileri gösterir.

Örnek:


:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-31-bgp-route.png" alt-text="RouteDiagnosticLog içinde görülen BGP Route Exchange etkinliğinin örneği.":::


## <a name="ikediagnosticlog"></a><a name="IKEDiagnosticLog"></a>Ikediagnosticlog

**Ikediagnosticlog** tablosu, Ike/IPSec için ayrıntılı hata ayıklama günlüğü sağlar. Bu, bağlantıları giderirken veya VPN senaryolarına bağlanma başarısızlığının incelenmesi için çok yararlıdır.

Burada, başvuru olarak örnek bir sorgunuz vardır.

```
AzureDiagnostics  
| where Category == "IKEDiagnosticLog" 
| extend Message1=Message
| parse Message with * "Remote " RemoteIP ":" * "500: Local " LocalIP ":" * "500: " Message2
| extend Event = iif(Message has "SESSION_ID",Message2,Message1)
| project TimeGenerated, RemoteIP, LocalIP, Event, Level 
| sort by TimeGenerated asc
```

**Ikediagnosticlog** üzerinde bu sorgu size birden çok sütun gösterecektir.


|***Ad** _ | _ *_Açıklaması_** |
|---        | ---               |
|**TimeGenerated** | UTC saat diliminde her olayın zaman damgası.|
| **RemoteIP** | Şirket içi VPN cihazının IP adresi. Gerçek dünya senaryolarında, ilgili şirket içi cihazın IP adresine göre filtre uygulamak yararlı olacaktır. |
|**LocalIP** | sorun giderdiğimiz VPN Gateway IP adresi. Gerçek dünya senaryolarında, ilgili VPN ağ geçidinin IP adresine göre filtrelemeniz, aboneliğinizde birden fazla olması gerekir. |
|**Olay** | sorun giderme için yararlı bir tanılama iletisi içerir. Genellikle bir anahtar sözcükle başlar ve Azure Gateway tarafından gerçekleştirilen eylemlere başvurur: **\[ Send \]** , Azure ağ geçidi tarafından gönderilen bir IPsec paketinin neden olduğu bir olayı gösterir.  **\[ Alındı \]** , şirket içi cihazdan alınan bir paketin sonucunda bir olay olduğunu gösterir.  **\[ Yerel \]** , Azure ağ geçidi tarafından yerel olarak gerçekleştirilen bir eylemi gösterir. |


AzureDiagnostics veritabanındaki özgün sütun listesinde Remoteıp, Localıp ve olay sütunlarının nasıl mevcut olmadığı, ancak analizine basitleşmesi için "Ileti" sütununun çıktısı ayrıştırılırken sorguya Eklenme hakkında dikkat edin.

Sorun giderme ipuçları:

- IPSec görüşmesinin başlangıcını belirlemek için ilk SA \_ INIT iletisini bulmanız gerekir. Bu tür bir ileti tünelin her iki tarafında da gönderilebilir. İlk paketi, IPSec terminolojisinde "başlatıcı" olarak adlandırılır, diğer taraftan "Yanıtlayıcı" olur. İlk SA \_ INIT iletisi her zaman rCookie = 0 ' dır.

- IPSec tüneli kuramazsa, Azure birkaç saniyede bir yeniden denemeye devam eder. Bu nedenle, sorunu yeniden oluşturmak için beklemek zorunda olmadığınızdan, "VPN azaltma" sorunlarını giderme, ıkediagnosticlog üzerinde çok uygundur. Ayrıca, her deneyişinizde hata her zaman aynı olur, böylece her zaman bir "örnek" başarısız anlaşmaya yaklaşmanız yeterlidir.

- SA \_ INIT, eşin bu IPSec anlaşması için kullanmak Istediği IPSec parametrelerini içerir. Resmi belge   
[Varsayılan IPSec/IKE parametreleri](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec) varsayılan ayarlarla Azure ağ geçidi tarafından desteklenen IPSec parametrelerini listeler.


## <a name="p2sdiagnosticlog"></a><a name="P2SDiagnosticLog"></a>P2SDiagnosticLog

VPN Tanılama için kullanılabilir son tablo **P2SDiagnosticLog**' dir. Bu tablo, Noktadan siteye yönelik etkinliği izler.

Burada, başvuru olarak örnek bir sorgunuz vardır.

```
AzureDiagnostics  
| where Category == "P2SDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

**P2SDiagnosticLog** üzerinde bu sorgu size birden çok sütun gösterir.

|***Ad** _ | _ *_Açıklaması_** |
|---        | ---               |
|**TimeGenerated** | UTC saat diliminde her olayın zaman damgası.|
|**OperationName** | meydana gelen olay. *P2SLogEvent* olacaktır.|
| **İleti** | işlemin ayrıntıları.|

Çıktı, ağ geçidinin uygulandığı site ayarlarının yanı sıra IPSec ilkelerinin de yer aldığı tüm noktaları gösterir.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-28-p2s-log-event.png" alt-text="P2SDiagnosticLog içinde görülen site bağlantısı noktası örneği.":::

Ayrıca, bir istemci Ikev2 veya OpenVPN noktası üzerinden sitesine her bağlandığında, bu tablo paket etkinliğini, EAP/RADIUS konuşmalarını ve başarılı/başarısız sonuçlarını kullanıcıya göre günlüğe kaydeder.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-29-eap.png" alt-text="P2SDiagnosticLog içinde görülen EAP kimlik doğrulaması örneği.":::

## <a name="next-steps"></a>Sonraki Adımlar

Tünel kaynak günlüklerinde uyarıları yapılandırmak için bkz. [VPN Gateway kaynak günlüklerinde uyarıları ayarlama](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).

