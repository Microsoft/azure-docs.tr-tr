---
title: Kuruluşunuzun Azure Iletişim Hizmetleri için ağını hazırlama
titleSuffix: An Azure Communication Services concept document
description: Azure Iletişim Hizmetleri ses ve video çağırma için ağ gereksinimleri hakkında bilgi edinin
author: nmurav
manager: jken
services: azure-communication-services
ms.author: nmurav
ms.date: 3/23/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 22bf8af89771e57621a0d8851ca31bb750b7cda2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108358"
---
# <a name="ensure-high-quality-media-in-azure-communication-services"></a>Azure Iletişim hizmetlerinde yüksek kaliteli medya sağlayın

Bu belgede, Azure Iletişim Hizmetleri ile yüksek kaliteli multimedya iletişim deneyimleri oluştururken göz önünde bulundurmanız gereken faktörlere ve en iyi yöntemlere genel bakış sunulmaktadır.

## <a name="factors-that-affect-media-quality-and-reliability"></a>Medya kalitesini ve güvenilirliği etkileyen faktörler

Azure Iletişim Hizmetleri gerçek zamanlı medya (ses, video ve uygulama paylaşımı) kalitesine katkıda bulunan birçok farklı faktör vardır. Bunlar arasında ağ kalitesi, bant genişliği, güvenlik duvarı, ana bilgisayar ve cihaz yapılandırması vardır.


### <a name="network-quality"></a>Ağ kalitesi

IP üzerinden gerçek zamanlı medyanın kalitesi, temel alınan ağ bağlantısının kalitesindeki önemli ölçüde etkilenir, ancak özellikle bu miktara göre:
* **Gecikme süresi**. Bu, ağdaki A noktasından B 'ye bir IP paketi almak için gereken süredir. Bu ağ yayma gecikmesi, iki noktası ve trafiğinizin akışını yaptığı cihazlar tarafından oluşan ek yük arasındaki fiziksel uzaklığa göre belirlenir. Gecikme süresi tek yönlü veya gidiş dönüş süresi (RTT) olarak ölçülür.
* **Paket kaybı**. Belirli bir zaman penceresinde kaybolan paketlerin yüzdesi. Paket kaybı, neredeyse hiçbir etkisi olmayan küçük, tek bir kayıp paketten, tam ses kesilmesi oluşmasına neden olacak şekilde geri dönüş kayıplarına kadar ses kalitesini doğrudan etkiler.
* **Paket içi varış değişimi veya yalnızca değişim**. Bu, birbirini izleyen paketler arasındaki gecikmede ortalama değişikdir. Azure Iletişim Hizmetleri, arabelleğe alma yoluyla bazı değişim düzeylerine uyum sağlayabilir. Bu yalnızca, bir katılımcının etkilerini fark ettiğini belirten bir kullanıcının arabelleği aşması durumunda olur.

### <a name="network-bandwidth"></a>Ağ bant genişliği

Ağınızın eşzamanlı Azure Iletişim Hizmetleri medya oturumları ve diğer iş uygulamaları için gereken bant genişliğini destekleyecek şekilde yapılandırıldığından emin olun. Bant genişliği performansı için uçtan uca ağ yolunu test etmek, multimedya Iletişim Hizmetleri çözümünüzün başarılı dağıtımı açısından önemlidir.

JavaScript SDK 'Ları için bant genişliği gereksinimleri aşağıda verilmiştir:

|Bant genişliği|Senaryolar|
|:--|:--|
|40 kbps|Uçtan uca ses çağırma|
|500 kbps|Uçtan uca ses çağırma ve ekran paylaşımı|
|500 kbps|30 fps 'de 360p 'yi çağıran uçtan uca kaliteli video|
|1,2 Mbps|Eşler arası HD kalitesinde video, HD 720p çözünürlükte, 30fps 'de çağrılıyor|
|500 kbps|30 fps 'de 360p çağıran videoyu Gruplandır|
|1,2 Mbps|HD grup video çağrısı, HD 720p ile 30fps çözünürlükte| 

Yerel Android ve iOS SDK 'Ları için bant genişliği gereksinimleri aşağıda verilmiştir:

|Bant genişliği|Senaryolar|
|:--|:--|
|30 KB/sn|Uçtan uca ses çağırma |
|130 Kbps|Uçtan uca ses çağırma ve ekran paylaşımı|
|500 kbps|30 fps 'de 360p 'yi çağıran uçtan uca kaliteli video|
|1,2 Mbps|Eşler arası HD kalitesinde video, HD 720p çözünürlükte, 30fps 'de çağrılıyor|
|1,5 Mbps|En fazla 30fps 'de HD 1080p çözünürlüklü eşler arası HD kalitede video |
|500 kbps/1Mbps|Grup Videosu çağırma|
|1Mbps/2Mbps|HD Group video çağırma (1080p ekranında 5 40p video)|

### <a name="firewalls-configuration"></a>Güvenlik Duvarı (ler) yapılandırma

Azure Iletişim Hizmetleri bağlantıları, yüksek kaliteli multimedya deneyimleri sunmak için belirli bağlantı noktalarına ve IP adreslerine internet bağlantısı gerektirir. Azure Iletişim Hizmetleri, bu bağlantı noktalarına ve IP adreslerine erişim olmadan çalışmaya devam edebilir. Ancak, önerilen bağlantı noktaları ve IP aralıkları açıkken en iyi deneyim sağlanır.

| Kategori | IP aralıkları veya FQDN | Bağlantı noktaları | 
| :-- | :-- | :-- |
| Medya trafiği | [Azure genel bulut IP adresleri aralığı](https://www.microsoft.com/download/confirmation.aspx?id=56519) | UDP 3478 ila 3481, TCP bağlantı noktaları 443 |
| Sinyal, telemetri, kayıt| *. skype.com, *. microsoft.com, *. azure.net, *. azureedge.net, *. office.com, *. trouter.io | TCP 443, 80 |

### <a name="network-optimization"></a>Ağ iyileştirmesi

Aşağıdaki görevler isteğe bağlıdır ve Azure Iletişim hizmetlerini kullanıma almak için gerekli değildir. Ağınızı ve Azure Iletişim Hizmetleri performansınızı iyileştirmek için veya bazı ağ sınırlamalarına sahip olduğunuzu biliyorsanız bu kılavuzu kullanın.
Şu durumlarda daha fazla iyileştirmek isteyebilirsiniz:
* Azure Iletişim Hizmetleri yavaş çalışıyor (Belki de bant genişliği yetersiz olabilir)
* Çağrı devam ediyor (güvenlik duvarı veya proxy engelleyiciler olabilir)
* Çağrılar statik ve kesilen veya robots gibi ses seslerine sahiptir (değişim veya paket kaybı olabilir)

| Ağ iyileştirme görevi | Ayrıntılar |
| :-- | :-- |
| Ağınızı planlayın | Bu belgelerde, aramalar için ağınıza en düşük gereksinimleri bulabilirsiniz. [Ağınızı planlamak Için takımlar örneğine](https://docs.microsoft.com/microsoftteams/tutorial-network-planner-example) başvurun |
| Dış ad çözümlemesi | Azure iletişim hizmetleri SDK 'Larını çalıştıran tüm bilgisayarların, Azure Iletişim servicers tarafından sunulan hizmetleri ve güvenlik duvarlarının erişimi engellemediğini önlemek için dış DNS sorgularını çözümleyebildiğinden emin olun. Lütfen SDK 'ların adresleri çözümleyediğinden emin olun *. skype.com, *. microsoft.com, *. azure.net, *. azureedge.net, *. office.com, *. trouter.io  |
| Oturum kalıcılığını koruyun | Güvenlik duvarınızın, UDP için eşlenen ağ adresi çevirisi (NAT) adreslerini veya bağlantı noktalarını değiştirmediğinden emin olun
NAT havuz boyutunu doğrula | Kullanıcı bağlantısı için gereken ağ adresi çevirisi (NAT) havuz boyutunu doğrulayın. Birden çok kullanıcı ve cihaz, [ağ adresi çevirisi (NAT) veya bağlantı noktası adres çevirisi (Pat)](https://docs.microsoft.com/office365/enterprise/nat-support-with-office-365)kullanarak Azure iletişim hizmetlerine erişebiliyorsa, her genel yönlendirilebilir IP adresinin arkasında gizlenen cihazların desteklenen sayıyı aşmadığından emin olun. Bağlantı noktası tükenmesi 'ni engellemek için NAT havuzlarına yeterli genel IP adresleri atandığından emin olun. Bağlantı noktası tükenmesi, iç kullanıcılara ve cihazlara Azure Iletişim hizmetlerine bağlanamamaya katkıda bulunur |
| Yetkisiz giriş algılama ve önleme Kılavuzu | Ortamınızda giden bağlantılar için ek bir güvenlik katmanı için dağıtılan bir [Izinsiz giriş algılama](https://docs.microsoft.com/azure/network-watcher/network-watcher-intrusion-detection-open-source-tools) veya önleme SISTEMI (kimlikler/IP 'ler) varsa, tüm Azure Iletişim Hizmetleri URL 'lerine izin ver |
| Bölünmüş tünel VPN 'yi yapılandırma | Genellikle [Bölünmüş tünel VPN](https://docs.microsoft.com/windows/security/identity-protection/vpn/vpn-routing)olarak bilinen sanal özel ağı (VPN) atlayan takımlar trafiği için alternatif bir yol sağlamanızı öneririz. Bölünmüş tünel, Azure Iletişim Hizmetleri trafiğinin VPN üzerinden gitmediği, bunun yerine doğrudan Azure 'a gitmeyeceği anlamına gelir. VPN 'nizi atlamak medya kalitesinde olumlu bir etkiye sahip olur ve VPN cihazlarından ve kuruluşun ağının yükünü azaltır. Bölünmüş tünel VPN 'i uygulamak için VPN satıcınız ile çalışın. VPN 'yi atlama önerdiğimiz diğer nedenler: <ul><li> VPN 'Ler genellikle gerçek zamanlı medyayı destekleyecek şekilde tasarlanmamıştır veya yapılandırılmamıştır.</li><li> VPN 'Ler aynı zamanda UDP 'yi de desteklemiyor (Azure Iletişim Hizmetleri için gereklidir)</li><li>VPN 'Ler, zaten şifrelenmiş olan medya trafiğinin üzerine ek bir şifreleme katmanı da sunar.</li><li>Azure Iletişim hizmetlerine bağlantı, VPN cihazından gelen artı sabitleme trafiği nedeniyle etkili olmayabilir.</li></ul>|
| QoS uygulama | Paket önceliklendirmesini yapılandırmak için [hizmet kalitesi (QoS) kullanın](https://docs.microsoft.com/microsoftteams/qos-in-teams) . Bu, çağrı kalitesini iyileştirir ve arama kalitesini izlemenize ve sorunlarını gidermenize yardımcı olur. QoS, yönetilen bir ağın tüm kesimlerinde uygulanmalıdır. Bant genişliği için yeterince bir ağ sağlandığı halde QoS, beklenmeyen ağ olayları durumunda risk azaltma olanağı sunar. QoS ile, bu beklenmeyen olayların kalite olumsuz şekilde etkilenmemesi için sesli trafik önceliklendirilir. | 
| WiFi 'yi iyileştirme | VPN 'ye benzer şekilde, WiFi ağları gerçek zamanlı medyayı destekleyecek şekilde tasarlanamaz veya yapılandırılmış değildir. Azure Iletişim hizmetlerini desteklemek için bir WiFi ağını planlama veya iyileştirme, yüksek kaliteli bir dağıtım için önemli bir konudur. Şu faktörleri göz önünde bulundurun: <ul><li>Ağ veya WiFi multimedyayı (WMM) uygulayıp medya trafiğinin WiFi ağlarınızda uygun şekilde öncelik aldığından emin olun.</li><li>WiFi bantlarını ve erişim noktası yerleşimini planlayın ve iyileştirin. 2,4 GHz aralığı, erişim noktası yerleşimine bağlı olarak yeterli bir deneyim verebilir, ancak erişim noktaları genellikle bu aralıkta çalışan diğer tüketici cihazlarından etkilenir. 5 GHz aralığı, yoğun aralığı nedeniyle gerçek zamanlı medyaya daha uygundur, ancak yeterli kapsama almak için daha fazla erişim noktası gerektirir. Uç noktaların Ayrıca bu aralığı desteklemesi ve bu bantların uygun şekilde kullanılması için yapılandırılması gerekir.</li><li>Çift bantlı WiFi ağları kullanıyorsanız, bant oluşturma 'yı düşünün. Bant oluşturma, çift bantlı istemcileri 5 GHz aralığını kullanacak şekilde etkilemek için WiFi satıcıları tarafından uygulanan bir tekniktir.</li><li>Aynı kanalın erişim noktaları birbirine çok yakın olduğunda, sinyal çakışmasına ve istemeyerek rekabet eden bir kullanıcı deneyimine yol açabilir. Birbirini izleyen erişim noktalarının örtüşmeyen kanallarda olduğundan emin olun.</li></ul> Her kablosuz satıcının, kablosuz çözümünü dağıtmaya yönelik bir önerisi vardır. Belirli rehberlik için WiFi satıcınıza başvurun.|



### <a name="operating-system-and-browsers-for-javascript-sdks"></a>İşletim sistemi ve tarayıcılar (JavaScript SDK 'Ları için)

Azure Iletişim Hizmetleri sesli/video SDK 'Ları belirli işletim sistemlerini ve tarayıcıları destekler.
Çağıran SDK 'ların çağıran [kavramsal belgelerde](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features)desteklediği işletim sistemleri ve tarayıcılar hakkında bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki belgeler sizin için ilginç olabilir:

- [Kitaplık çağırma](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) hakkında daha fazla bilgi edinin
- [İstemci-sunucu mimarisi](https://docs.microsoft.com/azure/communication-services/concepts/client-and-server-architecture) hakkında bilgi edinin
- [Çağrı akışı topolojileri](https://docs.microsoft.com/azure/communication-services/concepts/call-flows) hakkında bilgi edinin
