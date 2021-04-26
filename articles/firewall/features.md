---
title: Azure Güvenlik Duvarı özellikleri
description: Azure Güvenlik duvarı özellikleri hakkında bilgi edinin
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/02/2021
ms.author: victorh
ms.openlocfilehash: 8dbfb23d4314f8ceb13ad36ca9733e446e176090
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278193"
---
# <a name="azure-firewall-features"></a>Azure Güvenlik Duvarı özellikleri

[Azure Güvenlik Duvarı](overview.md) , Azure sanal ağ kaynaklarınızı koruyan, yönetilen, bulut tabanlı bir ağ güvenlik hizmetidir.

![Güvenlik duvarına genel bakış](media/overview/firewall-threat.png)

Azure Güvenlik Duvarı aşağıdaki özellikleri içerir:

- Yerleşik yüksek kullanılabilirlik
- Kullanılabilirlik Alanları
- Kısıtlamasız bulut ölçeklenebilirliği
- Uygulama FQDN filtreleme kuralları
- Ağ trafiği filtreleme kuralları
- FQDN etiketleri
- Hizmet etiketleri
- Tehdit bilgileri
- Giden SNAT desteği
- Gelen DNAT desteği
- Birden çok genel IP adresi
- Azure İzleyici günlükleri
- Zorlamalı tünel oluşturma
- Web kategorileri (Önizleme)
- Sertifikalar

## <a name="built-in-high-availability"></a>Yerleşik yüksek kullanılabilirlik

Yüksek kullanılabilirlik yerleşik olarak bulunur, bu nedenle ek yük dengeleyiciler gerekli değildir ve yapılandırmanız gereken bir şey yoktur.

## <a name="availability-zones"></a>Kullanılabilirlik Alanları

Azure Güvenlik Duvarı, daha fazla kullanılabilirlik için dağıtım sırasında birden fazla Kullanılabilirlik Alanları yayılarak yapılandırılabilir. Kullanılabilirlik Alanları, kullanılabilirliği% 99,99 çalışma süresine kadar artar. Daha fazla bilgi için bkz. Azure Güvenlik Duvarı [hizmet düzeyi sözleşmesi (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). İki veya daha fazla Kullanılabilirlik Alanları seçildiğinde% 99,99 çalışma süresi SLA 'Sı sunulur.

Ayrıca, Azure Güvenlik duvarını belirli bir bölgeye yalnızca yakınlık nedenleriyle, hizmet standardı% 99,95 SLA ile ilişkilendirebilirsiniz.

Bir kullanılabilirlik alanında dağıtılan bir güvenlik duvarı için ek maliyet yoktur. Ancak, Kullanılabilirlik Alanları ilişkili gelen ve giden veri aktarımları için ek maliyetler mevcuttur. Daha fazla bilgi için bkz. [bant genişliği fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/bandwidth/).

Azure Güvenlik Duvarı Kullanılabilirlik Alanları, Kullanılabilirlik Alanları destekleyen bölgelerde kullanılabilir. Daha fazla bilgi için bkz. [Azure 'da kullanılabilirlik alanları destekleyen bölgeler](../availability-zones/az-region.md)

> [!NOTE]
> Kullanılabilirlik Alanları, yalnızca dağıtım sırasında yapılandırılabilir. Mevcut bir güvenlik duvarını Kullanılabilirlik Alanları içerecek şekilde yapılandıramazsınız.

Kullanılabilirlik Alanları hakkında daha fazla bilgi için bkz. [Azure 'Da bölgeler ve kullanılabilirlik alanları](../availability-zones/az-overview.md).

## <a name="unrestricted-cloud-scalability"></a>Kısıtlamasız bulut ölçeklenebilirliği

Azure Güvenlik Duvarı, değişen ağ trafiği akışlarıyla başa çıkmak için gerek duyduğunuz kadar ölçeklenebilir, bu sayede trafiğinizin en yoğun olduğu durum için bütçe yapmak zorunda kalmazsınız.

## <a name="application-fqdn-filtering-rules"></a>Uygulama FQDN filtreleme kuralları

Giden HTTP/S trafiğini veya Azure SQL trafiğini, joker karakterler dahil olmak üzere, tam etki alanı adları (FQDN) ile sınırlayabilirsiniz. Bu özellik TLS sonlandırmasını gerektirmez.

## <a name="network-traffic-filtering-rules"></a>Ağ trafiği filtreleme kuralları

Kaynak ve hedef IP adresi, bağlantı noktası ve protokole göre merkezi olarak *izin ver* veya *Reddet* ağ filtreleme kuralları oluşturabilirsiniz. Azure Güvenlik Duvarı tamamen durum bilgisine sahiptir; bu nedenle, farklı türden bağlantıların geçerli paketlerini tanıyabilir. Kurallar, birden çok abonelik ve sanal ağda zorlanır ve günlüğe kaydedilir.

## <a name="fqdn-tags"></a>FQDN etiketleri

[FQDN etiketleri](fqdn-tags.md) , güvenlik duvarınız aracılığıyla Iyi bilinen Azure hizmeti ağ trafiğine izin vermeyi kolaylaştırır. Örneğin Windows Update ağ trafiğine güvenlik duvarınızda izin vermek istediğiniz varsayalım. Bir uygulama kuralı oluşturup Windows Update etiketini eklersiniz. Artık Windows Update’in ağ trafiği, güvenlik duvarınızdan geçebilir.

## <a name="service-tags"></a>Hizmet etiketleri

[Hizmet etiketi](service-tags.md) , güvenlik kuralı oluşturma karmaşıklığını en aza indirmeye yardımcı olmak IÇIN bir IP adresi ön eki grubunu temsil eder. Kendi hizmet etiketinizi oluşturamaz ve bir etiket içinde hangi IP adreslerinin ekleneceğini belirtebilirsiniz. Hizmet etiketine dahil olan adres ön ekleri Microsoft tarafından yönetilir ve hizmet etiketi adresler değiştikçe otomatik olarak güncelleştirilir.

## <a name="threat-intelligence"></a>Tehdit bilgileri

Güvenlik duvarınızın, bilinen kötü amaçlı IP adreslerinden ve etki alanlarından gelen/giden trafiği uyarmasını ve reddetmesi için [tehdit zekası](threat-intel.md)tabanlı filtreleme etkinleştirilebilir. IP adresleri ve etki alanları, Microsoft Tehdit Analizi akışından alınır.

## <a name="outbound-snat-support"></a>Giden SNAT desteği

Tüm giden sanal ağ trafiği IP adresleri Azure Güvenlik Duvarı genel IP’sine çevrilir (Kaynak Ağ Adresi Çevirisi). Sanal ağınızdan kaynaklanan uzak İnternet hedeflerine yönelik trafiği tanımlayabilir ve buna izin verebilirsiniz. Hedef IP, [ıANA RFC 1918](https://tools.ietf.org/html/rfc1918)başına özel bir IP aralığı olduğunda Azure GÜVENLIK duvarı SNAT değildir. 

Kuruluşunuz özel ağlar için genel bir IP adresi aralığı kullanıyorsa, Azure Güvenlik Duvarı, trafiği AzureFirewallSubnet içindeki güvenlik duvarı özel IP adreslerinden birine karşı bir şekilde SNAT olarak kullanır. Azure Güvenlik duvarını, genel IP adresi aralığınızı **SNAT olarak** yapılandırmak için yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure Güvenlik DUVARı SNAT özel IP adresi aralıkları](snat-private-range.md).

Azure Güvenlik Duvarı ölçümlerinde SNAT bağlantı noktası kullanımını izleyebilirsiniz. Daha fazla bilgi edinin ve [güvenlik duvarı günlüklerimiz ve ölçüm belgelerimizde](logs-and-metrics.md#metrics), SNAT bağlantı noktası kullanımı önerimize bakın.

## <a name="inbound-dnat-support"></a>Gelen DNAT desteği

Güvenlik duvarınızın genel IP adresine gelen Internet ağ trafiği çevrilir (hedef ağ adresi çevirisi) ve sanal ağlarınızdaki özel IP adreslerine filtrelenir.

## <a name="multiple-public-ip-addresses"></a>Birden çok genel IP adresi

Güvenlik duvarınızla [birden çok genel IP adresini](deploy-multi-public-ip-powershell.md) (250 'e kadar) ilişkilendirebilirsiniz.

Bu, aşağıdaki senaryolara izin vermez:

- **DNAT** -birden çok standart bağlantı noktası örneğini arka uç sunucularınıza çevirebilirsiniz. Örneğin iki genel IP adresini varsa 3389 (RDP) numaralı TCP bağlantı noktasını iki IP adresi için çevirebilirsiniz.
- **SNAT** -giden SNAT bağlantıları için daha fazla bağlantı noktası kullanılabilir ve bu, SNAT bağlantı noktası tükenmesi potansiyelini azaltır. Azure Güvenlik Duvarı şu anda bir bağlantı için kullanılacak kaynak genel IP adresini rastgele seçer. Ağınızda aşağı akış filtresi varsa güvenlik duvarınızla ilişkilendirilmiş olan tüm genel IP adreslerine izin vermeniz gerekir. Bu yapılandırmayı basitleştirmek için bir [genel IP adresi ön eki](../virtual-network/public-ip-address-prefix.md) kullanmayı düşünün.

## <a name="azure-monitor-logging"></a>Azure İzleyici günlükleri

Tüm olaylar Azure Izleyici ile tümleşiktir ve günlükleri bir depolama hesabında arşivlemenize, Olay Hub 'ınıza olayları akışlarınıza veya Azure Izleyici günlüklerine gönderebilmenizi sağlar. Azure Izleyici günlüğü örnekleri için bkz. Azure [güvenlik duvarı Için Azure izleyici günlükleri](./firewall-workbook.md).

Daha fazla bilgi için bkz. [öğretici: Azure Güvenlik Duvarı günlüklerini ve ölçümlerini izleme](./firewall-diagnostics.md). 

Azure Güvenlik Duvarı çalışma kitabı, Azure Güvenlik Duvarı veri analizi için esnek bir tuval sağlar. Azure portal içinde zengin görsel raporlar oluşturmak için kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Güvenlik Duvarı çalışma kitabını kullanarak günlükleri izleme](firewall-workbook.md).

## <a name="forced-tunneling"></a>Zorlamalı tünel oluşturma

Azure Güvenlik Duvarı 'nı, Internet 'e yönelik tüm trafiği doğrudan Internet 'e gitmek yerine belirlenen bir sonraki atlamaya yönlendirmek üzere yapılandırabilirsiniz. Örneğin, Internet 'e geçirilmeden önce ağ trafiğini işlemek için şirket içi bir uç güvenlik duvarına veya diğer ağ sanal gerecine (NVA) sahip olabilirsiniz. Daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Zorlamalı tünel](forced-tunneling.md).

## <a name="web-categories-preview"></a>Web kategorileri (Önizleme)

Web kategorileri, yöneticilerin kumar web siteleri, sosyal medya web siteleri ve diğerleri gibi web sitesi kategorilerine Kullanıcı erişimine izin verebilir veya erişimi reddetmesini sağlar. Web kategorileri Azure Güvenlik Duvarı standardına dahildir, ancak Azure Güvenlik Duvarı Premium önizlemesinde daha ince ayarlanmıştır. Bir FQDN tabanlı kategori ile eşleşen standart SKU 'daki Web kategorileri yeteneğinin aksine, Premium SKU, hem HTTP hem de HTTPS trafiği için tüm URL 'ye göre kategori ile eşleşir. Azure Güvenlik Duvarı Premium önizleme hakkında daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Premium Önizleme özellikleri](premium-features.md).

Örneğin, Azure Güvenlik Duvarı için bir HTTPS isteği alıyorsa `www.google.com/news` , aşağıdaki kategorilere ayırma beklenmektedir: 

- Güvenlik Duvarı standardı – yalnızca FQDN bölümü incelenir, `www.google.com` Bu nedenle *arama altyapısı* olarak kategorilere ayrılır. 

- Güvenlik Duvarı Premium – URL 'nin tamamı incelenir, bu nedenle `www.google.com/news` *haber* olarak kategorilere ayrılır.

Kategoriler, **yükümlülük**, **yüksek bant genişliği**, **iş kullanımı**, **verimlilik kaybı**, **Genel gezinme** ve **kategorilere ayrılmamış** önem derecesine göre düzenlenmiştir.

### <a name="categorization-change"></a>Kategori değişikliği

Şunları yaparsanız bir kategori değişikliği isteyebilirsiniz:

 - bir FQDN veya URL 'nin farklı bir kategori altında olması gerektiğini düşünün 
 
veya 

- kategorilere ayrılmamış bir FQDN veya URL için önerilen kategorili

' De bir istek göndermek için hoş geldiniz [https://aka.ms/azfw-webcategories-request](https://aka.ms/azfw-webcategories-request) .

### <a name="category-exceptions"></a>Kategori özel durumları

Web kategorisi kurallarınız için özel durumlar oluşturabilirsiniz. Kural koleksiyonu grubu dahilinde daha yüksek önceliğe sahip bir izin verme veya reddetme kuralı koleksiyonu oluşturun. Örneğin, öncelik `www.linkedin.com` 200 Ile **sosyal ağı** engelleyen bir kural koleksiyonu ile öncelik 100 ile izin veren bir kural koleksiyonu yapılandırabilirsiniz. Bu, önceden tanımlanmış **sosyal ağ** Web kategorisi için özel durum oluşturur.



## <a name="certifications"></a>Sertifikalar

Azure Güvenlik Duvarı, ödeme kartı sektör (PCI), hizmet organizasyonu denetimleri (SOC), Uluslararası Standardizasyon Teşkilatı (ISO) ve ıCSA Labs ile uyumludur. Daha fazla bilgi için bkz. [Azure Güvenlik Duvarı uyumluluk sertifikaları](compliance-certifications.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Güvenlik Duvarı Premium Önizleme özellikleri](premium-features.md)