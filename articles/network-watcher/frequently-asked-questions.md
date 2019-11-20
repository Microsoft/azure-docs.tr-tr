---
title: Azure ağ Izleyicisi hakkında sık sorulan sorular (SSS) | Microsoft Docs
description: Bu makalede, Azure ağ Izleyicisi hizmeti hakkında sık sorulan sorular yanıtlanmaktadır.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: 3305590f2d8abf0d894bc1df42b84edcc96a2b2d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598233"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Azure ağ Izleyicisi hakkında sık sorulan sorular (SSS)
[Azure Ağ İzleyicisi](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) hizmeti, bir Azure sanal ağındaki kaynaklara yönelik günlükleri izlemeye, tanılamaya, görüntülemeye ve etkinleştirmeye ve devre dışı bırakacak bir araç paketi sağlar. Bu makalede hizmetle ilgili yaygın soruların yanıtları vardır.

## <a name="general"></a>Genel

### <a name="what-is-network-watcher"></a>Ağ Izleyicisi nedir?
Ağ Izleyicisi, sanal makineler, sanal ağlar, uygulama ağ geçitleri, yük dengeleyiciler ve bir Azure sanal ağındaki diğer kaynakları içeren IaaS (hizmet olarak altyapı) bileşenlerinin ağ durumunu izlemek ve onarmak üzere tasarlanmıştır. PaaS (hizmet olarak platform) altyapısını izlemeye veya Web/Mobil Analiz almaya yönelik bir çözüm değildir.

### <a name="what-tools-does-network-watcher-provide"></a>Ağ Izleyicisi hangi araçları sağlar?
Ağ Izleyicisi üç önemli özellik kümesi sağlar
* İzleme
  * [Topoloji görünümü](https://docs.microsoft.com/azure/network-watcher/view-network-topology) , sanal ağınızdaki kaynakları ve bunlar arasındaki ilişkileri gösterir.
  * [Bağlantı İzleyicisi](https://docs.microsoft.com/azure/network-watcher/connection-monitor) , bir VM ile başka bir ağ kaynağı arasındaki bağlantıyı ve gecikme süresini izlemenizi sağlar.
  * [Ağ performansı İzleyicisi](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) , karma ağ mimarilerinde, ExpressRoute devrelerine ve hizmet/uygulama uç noktalarında bağlantıyı ve gecikme sürelerini izlemenize olanak sağlar.  
* Tanılama
  * [IP akışı doğrulama](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) , bir VM düzeyinde trafik filtreleme sorunlarını algılamanıza olanak tanır.
  * [Sonraki atlama](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) , trafik yollarını doğrulamanıza ve yönlendirme sorunlarını tespit etmenize yardımcı olur.
  * [Bağlantı sorunlarını giderme](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) , bir VM ile başka bir ağ kaynağı arasında tek seferlik bir bağlantı ve gecikme denetimi sunar.
  * [Paket yakalama](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) , sanal AĞıNıZDAKI bir VM 'deki tüm trafiği yakalamanızı sağlar.
  * [VPN sorun giderme](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) , VPN ağ geçitleriniz ve bağlantılarında hata ayıklamanıza yardımcı olan birden çok tanılama denetimi çalıştırır.
* Günlüğe kaydetme
  * [NSG akış günlükleri](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) , [ağ güvenlik gruplarındaki (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) tüm trafiği günlüğe kaydetmenize olanak tanır
  * [Trafik Analizi](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) NSG akış günlüğü verilerinizi işleyerek ağ trafiğinizi görselleştirmenizi, sorgulamanızı, çözümlemenize ve anlamanıza olanak tanır.


Daha ayrıntılı bilgi için bkz. [Ağ İzleyicisi 'ne genel bakış sayfası](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).


### <a name="how-does-network-watcher-pricing-work"></a>Ağ Izleyicisi fiyatlandırması nasıl çalışır?
Ağ Izleyicisi bileşenleri için [fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/network-watcher/) ve bunların fiyatlandırmasını ziyaret edin.

### <a name="which-regions-is-network-watcher-available-in"></a>Ağ Izleyicisi hangi bölgelerde kullanılabilir?
[Azure hizmet kullanılabilirliği sayfasında](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher) en son bölgesel kullanılabilirliği görüntüleyebilirsiniz

### <a name="what-are-resource-limits-on-network-watcher"></a>Ağ izleyicisinden kaynak sınırları nelerdir?
Tüm sınırlar için [hizmet limitleri](https://docs.microsoft.com/azure/azure-subscription-service-limits#network-watcher-limits) sayfasına bakın.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Bölge başına yalnızca bir ağ Izleyicisi örneğine izin veriliyor mu?
Bu özelliğin çalışması için bir abonelik için ağ Izleyicisi 'nin etkinleştirilmesi yeterlidir, bu bir hizmet sınırı değildir.

## <a name="nsg-flow-logs"></a>NSG akış günlükleri

### <a name="what-does-nsg-flow-logs-do"></a>NSG akış günlükleri ne yapar?
Azure ağ kaynakları, [ağ güvenlik grupları (NSG 'ler)](https://docs.microsoft.com/azure/virtual-network/security-overview)ile birleştirilebilir ve yönetilebilir. NSG akış günlükleri, NSG 'larınız aracılığıyla tüm trafikle ilgili 5 demet akış bilgilerini günlüğe Kaydetetkinleştirmenizi sağlar. Ham akış günlükleri, gerektikçe daha fazla işlenebileceği, çözümlenebildiği, sorgulanan veya verilebilecekleri bir Azure depolama hesabına yazılır.

### <a name="are-there-any-caveats-to-using-nsg-flow-logs"></a>NSG akış günlüklerinin kullanılması için herhangi bir uyarılar var mı?
NSG akış günlüklerinin kullanılması için önkoşul yoktur. Ancak, iki kısıtlama vardır
- **Hizmet uç noktaları VNET 'iniz üzerinde mevcut**olmamalıdır: NSG akış günlükleri, sanal makinelerinizdeki aracılardan depolama hesaplarına dağıtılır. Ancak bugün, günlükleri yalnızca depolama hesaplarına doğrudan gönderebilir ve sanal ağınıza eklenen bir hizmet uç noktası kullanamaz.

- **Depolama hesabı güvenlik duvarı olmamalıdır**: iç sınırlamalar nedeniyle, NSG akış günlüklerinin bunlarla birlikte çalışması için genel Internet üzerinden depolama hesaplarına erişilebilir olması gerekir. Trafik yine de Azure üzerinden yönlendirilmeye devam eder ve ek çıkış ücretleri de olmayacaktır.

Bu sorunları geçici olarak çözmek için izlenecek yönergeler için sonraki iki soruya bakın. Bu sınırlamaların her ikisinin de Ocak 2020 tarafından giderilmesi beklenir.

### <a name="how-do-i-use-nsg-flow-logs-with-service-endpoints"></a>Nasıl yaparım? NSG akış günlüklerini hizmet uç noktaları ile kullanmak mı istiyorsunuz?

*Seçenek 1: NSG akış günlüklerini VNET uç noktaları olmadan Azure Storage hesabı 'na yaymak için yeniden yapılandırın*

* Uç noktaları olan alt ağları bulma:

    - Azure portalında, en üstteki genel aramada **Kaynak Grupları** için arama yapın
    - Üzerinde çalıştığınız NSG'yi içeren Kaynak Grubuna gidin
    - Türe göre filtrelemek ve **sanal ağları** seçmek için ikinci açılan menüyü kullanın
    - Hizmet Uç Noktalarını içeren Sanal Ağa tıklayın
    - Sol bölmedeki **Ayarlar**'ın altında **Hizmet uç noktaları**'nı seçin
    - **Microsoft.Storage** öğesinin etkinleştirildiği alt ağları not alın

* Hizmet uç noktalarını devre dışı bırak:

    - Yukarıdan devam ederek, sol bölmedeki **Ayarlar**'ın altında **Alt ağlar**'ı seçin
    * Hizmet Uç Noktalarını içeren alt ağa tıklayın
    - **Hizmet uç noktaları** bölümündeki **Hizmetler**'in altında **Microsoft.Storage** öğesinin işaretini kaldırın

Birkaç dakika sonra depolama günlüklerini denetleyebilirsiniz; güncelleştirilmiş bir TimeStamp veya yeni oluşturulmuş bir JSON dosyası görmelisiniz.

*Seçenek 2: NSG akış günlüklerini devre dışı bırak*

Microsoft.Storage hizmet uç noktaları zorunluysa NSG Akış Günlüklerini devre dışı bırakmanız gerekecektir.

### <a name="how-do-i-disable-the--firewall-on-my-storage-account"></a>Depolama hesabımın güvenlik duvarını devre dışı Nasıl yaparım? mi?

Bu sorun, depolama hesabına erişmek için "tüm ağlar" etkinleştirilerek çözülür:

* [NSG Akış Günlükleri genel bakış sayfasında](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) NSG'nin yerini belirleyerek depolama hesabının adını bulun
* Portaldaki genel aramaya depolama hesabının adını yazarak depolama hesabına gidin
* **AYARLAR** bölümünün altında **Güvenlik duvarları ve sanal ağlar**'ı seçin
* **Tüm ağlar**'ı seçin ve bunu kaydedin. Zaten seçiliyse, hiçbir değişiklik yapmanız gerekmez.  

### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Akış günlükleri sürümleri 1 & 2 arasındaki fark nedir?
Akış günlükleri sürüm 2, *akış durumu* kavramını tanıtır & aktarılan bayt ve paketler hakkında bilgi depolar. [Daha fazla bilgi edinin](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Sonraki Adımlar
 - Ağ izleyicisine başlamanıza yönelik bazı öğreticiler için [belgelerimize genel bakış sayfasına](https://docs.microsoft.com/azure/network-watcher/) gidin.
