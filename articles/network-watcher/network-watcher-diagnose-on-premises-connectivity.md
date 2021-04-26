---
title: VPN Gateway aracılığıyla şirket Içi bağlantıyı tanılama
titleSuffix: Azure Network Watcher
description: Bu makalede, Azure ağ Izleyicisi kaynak sorunlarını giderme ile VPN ağ geçidi aracılığıyla şirket içi bağlantının nasıl tanılanacağı açıklanır.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 3b8b53446799eec3473d63c89672393a35f9787a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104670964"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>VPN ağ geçitleri aracılığıyla şirket içi bağlantıyı tanılama

Azure VPN Gateway, şirket içi ağınız ile Azure sanal ağınız arasında güvenli bir bağlantı gereksinimini karşılayan karma çözüm oluşturmanıza olanak sağlar. Gereksinimleriniz benzersiz olduğundan, şirket içi VPN cihazı tercih edilir. Azure Şu anda cihaz satıcılarıyla iş ortaklığı içinde sürekli olarak doğrulanan [ÇEŞITLI VPN cihazlarını](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) desteklemektedir. Şirket içi VPN cihazınızı yapılandırmadan önce cihaza özgü yapılandırma ayarlarını gözden geçirin. Benzer şekilde, Azure VPN Gateway, bağlantı kurmak için kullanılan [desteklenen bir IPSec parametreleri](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) kümesiyle yapılandırılır. Şu anda Azure VPN Gateway IPsec parametrelerinin belirli bir birleşimini belirtmenin veya seçmenin bir yolu yoktur. Şirket içi ve Azure arasında başarılı bir bağlantı kurmak için şirket içi VPN cihaz ayarlarının Azure VPN Gateway tarafından belirtilen IPSec parametrelerine uygun olması gerekir. Ayarlar yanlışsa, bağlantı kaybı olur ve bu sorunların giderilmesi önemsiz değildi ve genellikle sorunu tanımlamak ve çözmek için saat sürdü.

Azure ağ Izleyicisi sorun giderme özelliği sayesinde, ağ geçidiniz ve bağlantılarınız ile ilgili sorunları tanılayabilir ve dakikalar içinde sorunu düzeltmeye yönelik bilinçli bir karar vermek için yeterli bilgiye sahip olabilirsiniz.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Senaryo

Şirket içi VPN Gateway olarak FortiGate kullanarak Azure ile şirket içi arasında siteden siteye bağlantı yapılandırmak istiyorsunuz. Bu senaryoya ulaşmak için aşağıdaki kuruluma ihtiyacınız vardır:

1. Sanal ağ geçidi-Azure 'da VPN Gateway
1. Yerel ağ geçidi- [Şirket içi (FortiGate)](../vpn-gateway/tutorial-site-to-site-portal.md#LocalNetworkGateway) Azure bulutundaki gösterimi VPN Gateway
1. Siteden siteye bağlantı (rota tabanlı)- [VPN Gateway ve şirket içi yönlendirici arasında bağlantı](../vpn-gateway/tutorial-site-to-site-portal.md#CreateConnection)
1. [FortiGate yapılandırma](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Siteden siteye yapılandırma yapılandırmaya yönelik ayrıntılı adım adım yönergeler, ziyaret ederek bulunabilir: [Azure Portal kullanarak siteden siteye bağlantı Ile VNET oluşturma](../vpn-gateway/tutorial-site-to-site-portal.md).

Kritik yapılandırma adımlarından biri IPSec iletişim parametrelerini yapılandırıyor, herhangi bir yanlış yapılandırma, şirket içi ağ ile Azure arasında bağlantı kaybına neden olur. Şu anda Azure VPN ağ geçitleri, 1. aşama için aşağıdaki IPSec parametrelerini destekleyecek şekilde yapılandırılmıştır. Aşağıdaki tabloda görebileceğiniz gibi, Azure VPN Gateway tarafından desteklenen şifreleme algoritmaları AES256, AES128 ve 3DES ' dir.

### <a name="ike-phase-1-setup"></a>IKE Aşama 1 kurulumu

| **Özellik** | **PolicyBased** | **RouteBased ve Standard veya High-Performance VPN Gateway** |
| --- | --- | --- |
| IKE Sürümü |IKEv1 |IKEv2 |
| Diffie-Hellman Grubu |Grup 2 (1024 bit) |Grup 2 (1024 bit) |
| Kimlik Doğrulama Yöntemi |Önceden Paylaşılan Anahtar |Önceden Paylaşılan Anahtar |
| Şifreleme Algoritmaları |AES256 AES128 3DES |AES256 3DES |
| Karma Algoritma |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Aşama 1 Güvenlik İlişkisi (SA) Yaşam Süresi (Zaman) |28.800 saniye |28.800 saniye |

Bir kullanıcı olarak, bir örnek yapılandırmanın [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt)'da bulunması Için FortiGate 'i yapılandırmanız gerekir. FortiGate 'i karma algoritma olarak SHA-512 kullanacak şekilde yapılandırdınız. Bu algoritma, ilke tabanlı bağlantılar için desteklenen bir algoritma olmadığından, VPN bağlantınız çalışır.

Bu sorunların giderilmesi zordur ve kök nedenler genellikle sezgisel değildir. Bu durumda, sorunu çözmeye yönelik yardım almak için bir destek bileti açabilirsiniz. Ancak, Azure ağ Izleyicisi ile ilgili sorun giderme API 'SI ile bu sorunları kendi kendinize tanımlayabilirsiniz.

## <a name="troubleshooting-using-azure-network-watcher"></a>Azure ağ Izleyicisi 'ni kullanarak sorun giderme

Bağlantınızı tanılamak için Azure PowerShell bağlanın ve `Start-AzNetworkWatcherResourceTroubleshooting` cmdlet 'ini başlatın. Bu cmdlet 'i kullanarak ilgili ayrıntıları, [sanal ağ geçidi ve bağlantılar-PowerShell sorunlarını gidermek](network-watcher-troubleshoot-manage-powershell.md)için bulabilirsiniz. Bu cmdlet 'in tamamlanması birkaç dakika sürebilir.

Cmdlet tamamlandıktan sonra, sorun ve Günlükler hakkında ayrıntılı bilgi almak için cmdlet 'inde belirtilen depolama konumuna gidebilirsiniz. Azure ağ Izleyicisi, aşağıdaki günlük dosyalarını içeren bir zip klasörü oluşturur:

![1][1]

IKEErrors.txt adlı dosyayı açın ve şirket içi ıKE ayarı yanlış yapılandırma ile ilgili bir sorun olduğunu belirten aşağıdaki hatayı görüntüler.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Hata hakkında Scrubbed-wfpdiag.txt ayrıntılı bilgiler elde edebilirsiniz. Bu durumda, `ERROR_IPSEC_IKE_POLICY_MATCH` bağlantının düzgün şekilde çalışmadığını belirten bir bahsetmektedir.

Diğer bir yaygın yanlış yapılandırma yanlış paylaşılan anahtarlar belirtmektir. Yukarıdaki örnekte farklı paylaşılan anahtarlar belirttiyseniz, IKEErrors.txt aşağıdaki hatayı gösterir: `Error: Authentication failed. Check shared key` .

Azure ağ Izleyicisi sorun giderme özelliği, VPN Gateway ve bağlantınızı basit bir PowerShell cmdlet 'i ile tanılamanıza ve gidermenize olanak sağlar. Şu anda aşağıdaki koşulları tanılamayı destekliyoruz ve daha fazla koşul eklemeye yönelik çalışıyor.

### <a name="gateway"></a>Ağ geçidi

| Hata Türü | Nedeni | Günlük|
|---|---|---|
| NoFault | Hata saptanmadı. |Evet|
| GatewayNotFound | Ağ Geçidi bulunamıyor veya ağ geçidi sağlanmadı. |Hayır|
| PlannedMaintenance |  Ağ geçidi örneği bakım aşamasında.  |Hayır|
| UserDrivenUpdate | Bir Kullanıcı güncelleştirmesi devam ediyor. Bu bir yeniden boyutlandırma işlemi olabilir. | Hayır |
| VipUnResponsive | Ağ geçidinin birincil örneğine ulaşılamıyor. Bu durum araştırma başarısız olduğunda gerçekleşir. | Hayır |
| PlatformInActive | Platform ile ilgili bir sorun var. | No|
| ServiceNotRunning | Temel alınan hizmet çalışmıyor. | No|
| Noconnectionsdökümforgateway | Ağ geçidinde hiçbir bağlantı yok. Bu yalnızca bir uyarıdır.| No|
| ConnectionsNotConnected | Bağlantılardan hiçbiri bağlanmadı. Bu yalnızca bir uyarıdır.| Yes|
| Gatewaycpuusageaşıldı | Geçerli ağ geçidi kullanım CPU kullanımı %95 >. | Yes |

### <a name="connection"></a>Bağlantı

| Hata Türü | Nedeni | Günlük|
|---|---|---|
| NoFault | Hata saptanmadı. |Evet|
| GatewayNotFound | Ağ Geçidi bulunamıyor veya ağ geçidi sağlanmadı. |Hayır|
| PlannedMaintenance | Ağ geçidi örneği bakım aşamasında.  |Hayır|
| UserDrivenUpdate | Bir Kullanıcı güncelleştirmesi devam ediyor. Bu bir yeniden boyutlandırma işlemi olabilir.  | Hayır |
| VipUnResponsive | Ağ geçidinin birincil örneğine ulaşılamıyor. Durum araştırması başarısız olduğunda gerçekleşir. | No |
| ConnectionEntityNotFound | Bağlantı yapılandırması eksik. | No |
| Connectionımarkedconnected | Bağlantı "bağlantısı kesildi" olarak işaretlenir. |No|
| ConnectionNotConfiguredOnGateway | Temeldeki hizmette bağlantı yapılandırılmamış. | Yes |
| ConnectionMarkedStandby | Temel alınan hizmet bekleme olarak işaretlendi.| Yes|
| Kimlik Doğrulaması | Önceden paylaşılan anahtar uyumsuzluğu. | Yes|
| Peerulaşılabilirlik | Eş ağ geçidine erişilemiyor. | Yes|
| Ikepolicyuyuşmazlığıdır | Eş ağ geçidinde Azure tarafından desteklenmeyen ıKE ilkeleri vardır. | Yes|
| WfpParse hatası | WFP günlüğü ayrıştırılırken bir hata oluştu. |Evet|

## <a name="next-steps"></a>Sonraki adımlar

[Azure Ağ İzleyicisi sorunlarını giderme Ile VPN ağ geçitlerini](network-watcher-monitor-with-azure-automation.md) ziyaret ederek PowerShell ve Azure otomasyonu ile VPN Gateway bağlantısını denetlemeyi öğrenin

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png