---
title: Azure Güvenlik Duvarı nedir?
description: Azure Güvenlik Duvarı, Azure Sanal Ağ kaynaklarınızı koruyan yönetilen, bulut tabanlı bir güvenlik hizmetidir.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperf-fy21q1
ms.date: 04/20/2021
ms.author: victorh
ms.openlocfilehash: 9ed46af7e4c62b2b7213b9e7a3d71c1b4776c806
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835353"
---
# <a name="what-is-azure-firewall"></a>Azure Güvenlik Duvarı nedir?

![ICSA sertifikası](media/overview/icsa-cert-firewall-small.png)

Azure Güvenlik Duvarı, Azure Sanal Ağ kaynaklarınızı koruyan yönetilen, bulut tabanlı bir güvenlik hizmetidir. Yerleşik yüksek kullanılabilirliğe ve sınırsız bulut ölçeklenebilirliğine sahip, tam durum bilgisi olan bir hizmet olarak güvenlik duvarıdır.

![Güvenlik duvarına genel bakış](media/overview/firewall-threat.png)

Aboneliklerle sanal ağlarda uygulama ve ağ bağlantısı ilkelerini merkezi olarak oluşturabilir, zorlayabilir ve günlüğe alabilirsiniz. Azure Güvenlik Duvarı, dış güvenlik duvarlarının sanal ağınızdan kaynaklanan trafiği tanımlamasına olanak tanımak amacıyla sanal ağ kaynaklarınız için statik genel bir IP adresi kullanır.  Hizmet, günlük ve analiz için Azure İzleyici ile tamamen tümleşik çalışır.

Azure Güvenlik duvarı özellikleri hakkında bilgi edinmek için bkz. [Azure Güvenlik Duvarı Özellikleri](features.md).

## <a name="azure-firewall-premium-preview"></a>Azure Güvenlik Duvarı Premium önizleme

Azure Güvenlik Duvarı Premium önizleme, yüksek düzeyde hassas ve düzenlenmiş ortamlar için gereken özellikleri içeren bir sonraki nesil güvenlik duvarıdır. Bu yetenekler TLS incelemesi, ıDPS, URL filtrelemesi ve Web kategorilerini içerir.

Azure Güvenlik Duvarı Premium Önizleme özellikleri hakkında bilgi edinmek için bkz. [Azure Güvenlik Duvarı Premium Önizleme özellikleri](premium-features.md).


Güvenlik Duvarı Premium önizlemenin Azure portal nasıl yapılandırıldığını görmek için [Azure Portal Azure Güvenlik Duvarı Premium önizlemesi](premium-portal.md)' ne bakın.


## <a name="pricing-and-sla"></a>Fiyatlandırma ve SLA

Azure Güvenlik Duvarı fiyatlandırma bilgileri için bkz. [Azure Güvenlik Duvarı fiyatlandırması](https://azure.microsoft.com/pricing/details/azure-firewall/).

Azure Güvenlik Duvarı SLA bilgileri için bkz. [Azure Güvenlik Duvarı SLA 'sı](https://azure.microsoft.com/support/legal/sla/azure-firewall/).

## <a name="whats-new"></a>Yenilikler

Azure Güvenlik Duvarı yenilikleri hakkında bilgi edinmek için bkz. [Azure Updates](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Firewall).


## <a name="known-issues"></a>Bilinen sorunlar

Azure Güvenlik Duvarındaki bilinen sorunlar şunlardır:

|Sorun  |Description  |Risk azaltma  |
|---------|---------|---------|
|TCP/UDP dışı protokollere (örneğin ICMP) yönelik ağ filtreleme kuralları İnternet'e bağlı trafik için çalışmaz|TCP/UDP olmayan protokoller için ağ filtreleme kuralları, SNAT ile genel IP adresiniz arasında çalışmaz. TCP/UDP dışı protokoller, uç alt ağlarla sanal ağlar arasında desteklenir.|Azure Güvenlik Duvarı, [bugün IP protokolleri için SNAT desteği olmayan](../load-balancer/load-balancer-overview.md) Standart Load Balancer kullanır. Gelecekteki bir sürümde bu senaryoyu desteklemeye yönelik seçenekleri araştırıyoruz.|
|ICMP için eksik PowerShell ve CLI desteği|Azure PowerShell ve CLı, ağ kurallarında geçerli bir protokol olarak ıCMP 'yi desteklemez.|Portal ve REST API aracılığıyla bir protokol olarak ıCMP kullanmak yine de mümkündür. PowerShell ve CLı için yakında ıCMP eklemek üzere çalışıyoruz.|
|FQDN etiketleri bir protokol: bağlantı noktası ayarlamayı gerektirir|FQDN etiketleriyle uygulama kuralları için bağlantı noktası: protokol tanımı gerekir.|Bağlantı noktası:protokol değeri olarak **https** kullanabilirsiniz. FQDN etiketleri kullanıldığında bu alanı isteğe bağlı hale getirmek için çalışıyoruz.|
|Bir güvenlik duvarını farklı bir kaynak grubuna veya aboneliğe taşıma desteklenmiyor|Bir güvenlik duvarının farklı bir kaynak grubuna veya aboneliğe taşınması desteklenmez.|Bu işlevi desteklemek, yol haritamız üzerinde. Bir güvenlik duvarını başka bir kaynak grubuna veya aboneliğe taşımak için geçerli örneği silmeniz ve yeni kaynak grubunda veya abonelikte yeniden oluşturmanız gerekir.|
|Tehdit bilgileri uyarıları maskeli olabilir|Giden filtreleme maskeleri için hedef 80/443 olan ağ kuralları yalnızca uyarı moduna yapılandırıldığında tehdit bilgileri uyarılarını arar.|Uygulama kurallarını kullanarak 80/443 için giden filtreleme oluşturun. Ya da tehdit zekası modunu **uyarı ve reddetme** olarak değiştirin.|
|Azure Güvenlik Duvarı DNAT, özel IP hedefleri için çalışmıyor|Azure Güvenlik Duvarı DNAT desteği Internet çıkış/giriş ile sınırlıdır. DNAT Şu anda özel IP hedefleri için çalışmıyor. Örneğin, bağlı bileşene bağlı olarak.|Bu geçerli bir kısıtlamadır.|
|İlk genel IP yapılandırması kaldırılamıyor|Her bir Azure Güvenlik Duvarı genel IP adresi bir *IP yapılandırmasına* atanır.  İlk IP yapılandırması, güvenlik duvarı dağıtımı sırasında atanır ve genellikle güvenlik duvarı alt ağına (bir şablon dağıtımı aracılığıyla açıkça farklı şekilde yapılandırılmamışsa) bir başvuru içerir. Güvenlik duvarını serbest bırakacağından bu IP yapılandırmasını silemezsiniz. Güvenlik duvarının kullanılabilir en az bir genel IP adresi varsa, bu IP yapılandırmasıyla ilişkili genel IP adresini değiştirmeye veya kaldırmaya devam edebilirsiniz.|Bu tasarım gereğidir.|
|Kullanılabilirlik alanları yalnızca dağıtım sırasında yapılandırılabilir.|Kullanılabilirlik alanları yalnızca dağıtım sırasında yapılandırılabilir. Bir güvenlik duvarı dağıtıldıktan sonra Kullanılabilirlik Alanları yapılandıramazsınız.|Bu tasarım gereğidir.|
|Gelen bağlantılarda SNAT|DNAT 'nin yanı sıra, güvenlik duvarı genel IP adresi (gelen) ile kurulan bağlantılar, güvenlik duvarı özel IP 'lerinden birine karşı denetlenir. Simetrik yönlendirmeyi sağlamak için bugün bu gereksinim (etkin/etkin NVA 'lar için de).|HTTP/S için özgün kaynağı korumak üzere [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) üst bilgilerini kullanmayı göz önünde bulundurun. Örneğin, [Azure ön kapısı](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) veya [Azure Application Gateway](../application-gateway/rewrite-http-headers.md) gibi bir hizmeti güvenlik duvarının önünde kullanın. Ayrıca, Azure ön kapısının parçası olarak WAF 'yi ve güvenlik duvarını de zincirde ekleyebilirsiniz.
|SQL FQDN filtrelemesi yalnızca Proxy modunda desteklenir (bağlantı noktası 1433)|Azure SQL veritabanı, Azure SYNAPSE Analytics ve Azure SQL yönetilen örneği için:<br><br>SQL FQDN filtrelemesi yalnızca Proxy modunda desteklenir (bağlantı noktası 1433).<br><br>Azure SQL IaaS için:<br><br>Standart olmayan bağlantı noktaları kullanıyorsanız, bu bağlantı noktalarını uygulama kurallarında belirtebilirsiniz.|SQL yeniden yönlendirme modunda (Azure içinden bağlanıyorsanız varsayılan), Azure Güvenlik Duvarı ağ kurallarının bir parçası olarak SQL hizmeti etiketini kullanarak erişimi filtreleyebilirsiniz.
|TCP bağlantı noktası 25 ' i giden trafiğe izin verilmez| TCP bağlantı noktası 25 kullanan giden SMTP bağlantıları engellenir. Bağlantı noktası 25 öncelikle kimliği doğrulanmamış e-posta teslimi için kullanılır. Bu, sanal makineler için varsayılan platform davranışıdır. Daha fazla bilgi için bkz. [Azure 'da gıden SMTP bağlantısı sorunlarını giderme](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Ancak, sanal makinelerden farklı olarak, Azure Güvenlik duvarında bu işlevselliği etkinleştirmek mümkün değildir. Not: kimliği doğrulanmış SMTP (bağlantı noktası 587) veya SMTP 'nin 25 dışında bir bağlantı noktası üzerinden izin vermek için, SMTP incelemesi Şu anda desteklenmediğinden bir ağ kuralı ve uygulama kuralı yapılandırmadığınızdan emin olun.|SMTP sorun giderme makalesinde belirtildiği gibi, e-posta göndermek için önerilen yöntemi izleyin. Ya da, giden SMTP erişimine gereken sanal makineyi varsayılan yönlendirinizden güvenlik duvarına dışlayın. Bunun yerine, giden erişimi doğrudan internet 'e yapılandırın.
|SNAT bağlantı noktası tükenmesi|Azure Güvenlik Duvarı şu anda arka uç sanal makine ölçek kümesi örneği başına genel IP adresi başına 1024 bağlantı noktasını desteklemektedir. Varsayılan olarak, iki VMSS örneği vardır.|Bu bir SLB kısıtlamasıdır ve limitleri arttırmaya yönelik fırsatları sürekli arıyor. Bu sırada, SNAT tükenme saldırılarına açık olan dağıtımlar için en az beş genel IP adresi ile Azure Güvenlik Duvarı dağıtımlarını yapılandırmak önerilir. Bu, beş kez kullanılabilir SNAT bağlantı noktalarını arttırır. Aşağı akış izinlerini basitleştirmek için bir IP adresi önekinden ayırın.|
|Zorunlu tünelleme etkinken DNAT desteklenmez|Zorlamalı tünelleme etkinken dağıtılan güvenlik duvarları, asimetrik yönlendirme nedeniyle Internet 'ten gelen erişimi destekleyemez.|Asimetrik yönlendirme nedeniyle bu tasarıma sahiptir. Gelen bağlantılar için dönüş yolu, kurulan bağlantıyı görmeyen şirket içi güvenlik duvarı aracılığıyla geçer.
|Giden Pasif FTP, FTP sunucunuzun yapılandırmasına bağlı olarak birden çok genel IP adresi ile güvenlik duvarları için çalışmayabilir.|Pasif FTP, denetim ve veri kanalları için farklı bağlantılar oluşturur. Birden çok genel IP adresine sahip bir güvenlik duvarı giden verileri gönderdiğinde, kaynak IP adresi için genel IP adreslerinden birini rastgele seçer. Veriler ve denetim kanalları, FTP sunucunuzun yapılandırmasına bağlı olarak farklı kaynak IP adresleri kullandıklarında, FTP başarısız olabilir.|Açık bir SNAT yapılandırması planlanmaktadır. Bu sırada, FTP sunucunuzu verileri kabul edecek şekilde yapılandırabilir ve farklı kaynak IP adreslerinden kanalları kontrol edebilirsiniz (bkz. [IIS için bir örnek](/iis/configuration/system.applicationhost/sites/sitedefaults/ftpserver/security/datachannelsecurity)). Alternatif olarak, bu durumda tek bir IP adresi kullanmayı göz önünde bulundurun.|
|Gelen Pasif FTP, FTP sunucusu yapılandırmanıza bağlı olarak çalışmayabilir |Pasif FTP, denetim ve veri kanalları için farklı bağlantılar oluşturur. Azure Güvenlik duvarında gelen bağlantılar, simetrik yönlendirmeyi sağlamak için güvenlik duvarı özel IP adreslerinden birine yöneliktir. Veriler ve denetim kanalları, FTP sunucunuzun yapılandırmasına bağlı olarak farklı kaynak IP adresleri kullandıklarında, FTP başarısız olabilir.|Özgün kaynak IP adresinin korunması araştırılır. Bu sırada, FTP sunucunuzu verileri kabul edecek şekilde yapılandırabilir ve farklı kaynak IP adreslerinden kanalları kontrol edebilirsiniz.|
|NetworkRuleHit ölçümünde protokol boyutu eksik|ApplicationRuleHit metrik, filtreleme tabanlı protokole izin veriyor, ancak ilgili NetworkRuleHit ölçümünde bu yetenek yok.|Bir düzelme araştırılır.|
|64000 ve 65535 arasındaki bağlantı noktalarıyla NAT kuralları desteklenmez|Azure Güvenlik Duvarı, ağ ve uygulama kurallarında 1-65535 aralığında bulunan tüm bağlantı noktalarına izin verir, ancak NAT kuralları yalnızca 1-63999 aralığındaki bağlantı noktalarını destekler.|Bu geçerli bir kısıtlamadır.
|Yapılandırma güncelleştirmeleri ortalama beş dakika sürebilir|Bir Azure Güvenlik Duvarı yapılandırma güncelleştirmesi ortalama üç ila beş dakika sürebilir ve paralel güncelleştirmeler desteklenmez.|Bir düzelme araştırılır.|
|Azure Güvenlik Duvarı, HTTPS ve MSSQL trafiğini filtrelemek için SNı TLS üst bilgilerini kullanır|Tarayıcı veya sunucu yazılımı sunucu adı göstergesi (SNı) uzantısını desteklemiyorsa, Azure Güvenlik Duvarı üzerinden bağlanamazsınız.|Tarayıcı veya sunucu yazılımı SNı desteklemiyorsa, bağlantıyı bir uygulama kuralı yerine bir ağ kuralı kullanarak kontrol edebilirsiniz. SNı 'yi destekleyen yazılımlar için bkz. [sunucu adı belirtme](https://wikipedia.org/wiki/Server_Name_Indication) .|
|Özel DNS zorlamalı tünelleme ile çalışmıyor|Zorlamalı tünel etkinleştirilirse, özel DNS çalışmaz.|Bir düzelme araştırılır.|
|Başlat/Durdur, Zorlamalı tünel modunda yapılandırılmış bir güvenlik duvarı ile çalışmıyor|Başlat/Durdur, Zorlamalı tünel modunda yapılandırılmış Azure Güvenlik Duvarı ile çalışmıyor. Zorlamalı tünel yapılandırılmış olarak Azure Güvenlik Duvarı 'Nı başlatma girişimi şu hata ile sonuçlanır:<br><br>*Set-AzFirewall: AzureFirewall FW-xx yönetim IP yapılandırması mevcut bir güvenlik duvarına eklenemiyor. Zorlamalı tünel desteğini kullanmak istiyorsanız bir yönetim IP yapılandırması ile yeniden dağıtın. <br> StatusCode: 400 <br> ReasonPhrase: Hatalı istek*|İnceleme altında.<br><br>Geçici bir çözüm olarak, mevcut güvenlik duvarını silebilir ve aynı parametrelerle yeni bir tane oluşturabilirsiniz.|
|Portal kullanılarak güvenlik duvarı ilkesi etiketleri eklenemiyor|Azure Güvenlik Duvarı Ilkesinde, Azure portal kullanarak etiket eklemelerini önleyen bir yama destek sınırlaması vardır. Şu hata oluşturuldu: *kaynak için Etiketler kaydedilemedi*.|Bir düzelme araştırılır. Veya, etiketleri güncelleştirmek için Azure PowerShell cmdlet 'ini kullanabilirsiniz `Set-AzFirewallPolicy` .|
|IPv6 henüz desteklenmiyor|Bir kurala bir IPv6 adresi eklerseniz güvenlik duvarı başarısız olur.|Yalnızca IPv4 adreslerini kullanın. IPv6 desteği araştırma aşamasındadır.|
|Birden çok IP grubunu güncelleştirme, çakışma hatasıyla başarısız oluyor.|Aynı güvenlik duvarına bağlı iki veya daha fazla IP grubunu güncelleştirdiğinizde, kaynaklardan biri başarısız durumuna geçer.|Bu bilinen bir sorundur/kısıtlamadır. <br><br>Bir ıpgroup 'u güncelleştirdiğinizde, ıpgroup 'un eklendiği tüm güvenlik duvarlarındaki bir güncelleştirmeyi tetikler. Güvenlik duvarı hala *güncelleştirme* durumunda olduğu sürece Ikinci bir ıpgroup güncelleştirmesi başlatılırsa, ıpgroup güncelleştirmesi başarısız olur.<br><br>Bu hatadan kaçınmak için aynı güvenlik duvarına bağlı olan ıpgroups birer birer güncellenmelidir. Güvenlik duvarının *güncelleştirme* durumundan çıkmasına izin vermek için güncelleştirmeler arasında yeterli zamana izin verin.| 


## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç: Azure Güvenlik Duvarı ve güvenlik duvarı ilkesi oluşturma-ARM şablonu](../firewall-manager/quick-firewall-policy.md)
- [Hızlı başlangıç: Kullanılabilirlik Alanları ARM şablonuyla Azure Güvenlik duvarını dağıtma](deploy-template.md)
- [Öğretici: Azure portalını kullanarak Azure Güvenlik Duvarı'nı dağıtma ve yapılandırma](tutorial-firewall-deploy-portal.md)