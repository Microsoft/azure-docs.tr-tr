---
title: "Azure ExpressRoute: genel eşlemeyi Microsoft eşlemesi 'ne taşıma"
description: Bu makalede ExpressRoute eşlemesi, genel eşleme Microsoft'a taşıma adımları gösterilmektedir.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: f9ed841d347d99a2208971d164bbd7b9044e3b09
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031595"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Microsoft eşlemesi için genel eşleme Taşı

Bu makalede Microsoft kesinti yaşamadan eşleme için bir ortak eşleme yapılandırmasını taşımanıza yardımcı olur. ExpressRoute, Azure depolama ve Azure SQL Veritabanı gibi Azure PaaS hizmetleri için rota filtreleriyle Microsoft eşlemesinin kullanılmasını sağlar. Artık Microsoft PaaS ve SaaS hizmetlerine erişmek için tek bir yönlendirme etki alanına ihtiyacınız vardır. Kullanmak istediğiniz Azure bölgeleri için PaaS hizmeti ön eklerini tanıtmak için rota filtreleri kullanabilirsiniz.

Azure genel eşleme, her BGP oturumuyla ilişkili 1 NAT IP adresine sahiptir. Microsoft eşleme, kendi NAT ayırmalarınızı yapılandırmanıza ve seçmeli ön ek tanıtımları için yol filtrelerini kullanmanıza olanak sağlar. Genel eşleme, her zaman WAN 'dan Microsoft Azure hizmetlere hangi bağlantının başlatıldığını kullanan tek yönlü bir hizmettir. Microsoft Azure Hizmetleri, ağınızda bu yönlendirme etki alanı aracılığıyla yönelik bağlantıları başlatmasını mümkün olmayacaktır.

Genel eşdüzey hizmet sağlama etkinleştirildikten sonra tüm Azure hizmetlerine bağlanabilirsiniz. Biz size yolları tanıtmak Hizmetleri seçmeli olarak çekmek izin vermiyor. Microsoft eşlemesi, bağlantının WAN ile birlikte Microsoft Azure hizmetten başlatılabileceği iki yönlü bir bağlantıdır. Yönlendirme etki alanları ve eşleme hakkında daha fazla bilgi için bkz. [ExpressRoute devreleri ve yönlendirme etki alanları](expressroute-circuit-peerings.md).

## <a name="before"></a>Başlamadan önce

Microsoft eşlemesi için bağlanmak için ayarlama ve yönetme NAT gerekir Bağlantı sağlayıcınız ayarlayın ve NAT yönetilen bir hizmet olarak yönetme. Azure PaaS ve Microsoft eşlemesi üzerinde Azure SaaS hizmetlerine erişmek planlıyorsanız, NAT IP havuzu doğru şekilde boyutlandırmak önemlidir. ExpressRoute için NAT hakkında daha fazla bilgi için bkz. [Microsoft eşlemesi için NAT gereksinimleri](expressroute-nat.md#nat-requirements-for-microsoft-peering). Azure ExpressRoute (Microsoft eşlemesi) üzerinden Microsoft 'a bağlandığınızda, Microsoft 'a yönelik birden fazla bağlantı vardır. Bağlantıların biri, var olan İnternet bağlantınız diğeri de ExpressRoute aracılığıyla olan bağlantı. Microsoft’a yönelik trafiğin bir kısmı İnternet üzerinden gidip ExpressRoute üzerinden dönebilir ya da tam tersi gerçekleşebilir.

![Çift yönlü bağlantı](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> Microsoft'a tanıtılan NAT IP havuzu İnternet'e tanıtılmamalıdır. Bu, diğer Microsoft hizmetlerine bağlantıyı keser.

Microsoft eşlemesini yapılandırmadan önce asimetrik yönlendirme uyarıları için [birden çok ağ yoluyla asimetrik yönlendirmeye](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) bakın.

* Ortak eşleme kullanıyorsanız ve şu anda kullanılan genel IP adresleri için IP ağ kuralları sahip erişimi [Azure depolama](../storage/common/storage-network-security.md) veya [Azure SQL veritabanı](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), NAT IP havuzu yapılandırdığınızı emin olmanız gerekir Microsoft eşlemesi Azure depolama hesabına veya Azure SQL hesabı için genel IP adresleri listesi dahil edilir.<br>
* Microsoft eşleme kapalı kalma süresi ile taşımak için verildikleri sırada bu makaledeki adımları kullanın.

## <a name="create"></a>1. Microsoft eşlemesi oluştur

Microsoft eşlemesi oluşturulmadı, Microsoft eşlemesi oluşturmak için aşağıdaki makalelerden birini kullanın. Bağlantı sağlayıcısı tekliflerinizi yönetilen Katman 3 Hizmetleri, Microsoft, bağlantı hattı için eşleme etkinleştirmek için bağlantı sağlayıcısı isteyebilirsiniz.

Katman 3 ' ü yönetilmiyorsa, devam etmeden önce aşağıdaki bilgiler gereklidir:

* Birincil bağlantı için bir /30 alt ağı. Bu size ait ve bir RIR / IRR içinde kayıtlı bir geçerli ortak IPv4 ön eki olmalıdır. Microsoft, yönlendirici için ikinci kalmayacak IP kullandığından bu alt ağından yönlendiriciniz için ilk kullanılabilir IP adresi atar.<br>
* İkincil bağlantı için bir /30 alt ağı. Bu size ait ve bir RIR / IRR içinde kayıtlı bir geçerli ortak IPv4 ön eki olmalıdır. Microsoft, yönlendirici için ikinci kalmayacak IP kullandığından bu alt ağından yönlendiriciniz için ilk kullanılabilir IP adresi atar.<br>
* Bu eşlemenin kurulacak geçerli bir VLAN kimliği. Bağlantı hattındaki başka bir eşlemenin aynı VLAN kimliğini kullanmadığından emin olun. Birincil ve ikincil bağlantı için aynı VLAN kimliğini kullanmanız gerekir<br>
* Eşleme için AS numarası. 2 bayt ve 4 bayt AS numaralarını kullanabilirsiniz.<br>
* Tanıtılan önekler: BGP oturumunda tanıtmayı planladığınız tüm öneklerin bir listesini sağlamanız gerekir. Yalnızca ortak IP adresi ön ekleri kabul edilir. Ön ek kümesi göndermeyi planlıyorsanız, virgülle ayrılmış bir liste gönderebilirsiniz. Bu ön ekler size bir RIR / IRR içinde kaydedilmiş olmalıdır.<br>
* Yönlendirme Kayıt Defteri Adı: AS numarası ve öneklerinin kaydedildiği RIR / IRR’yi belirtebilirsiniz.

* **Isteğe bağlı** -müşteri ASN: eşleme numarasına kayıtlı olmayan ön ekler varsa, BUNLARıN kaydedildiği as numarasını belirtebilirsiniz.<br>
* **Isteğe bağlı** -birini kullanmayı SEÇERSENIZ bir MD5 karma değeri.

Aşağıdaki makalelerde Microsoft eşlemesini etkinleştirmeye yönelik ayrıntılı yönergeler bulabilirsiniz:

* [Azure portalını kullanarak Microsoft eşlemesi oluşturma](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Azure Powershell kullanarak Microsoft eşlemesi oluşturma](expressroute-howto-routing-arm.md#msft)<br>
* [Azure CLI kullanarak Microsoft eşlemesi oluşturma](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Microsoft eşlemesini doğrulama etkin

Microsoft eşdüzey hizmet sağlamanın etkinleştirildiğinden ve tanıtılan genel önekler yapılandırılmış durumda olduğunu doğrulayın.

* [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. devreye bir yol filtresi yapılandırın ve ekleyin

Varsayılan olarak, yeni Microsoft eşlemesi, bir yol filtresi devresine iliştirilene kadar herhangi bir önek duyurmaz. Bir rota filtresi kuralı oluşturduğunuzda, Azure PaaS hizmetleri için kullanmak istediğiniz Azure bölgelerinin hizmet topluluklarıyla ilgili listeyi belirtebilirsiniz. Bu, aşağıdaki ekran görüntüsünde gösterildiği gibi, bu yolları gereksiniminize göre filtreleme esnekliği sağlar:

![Ortak eşleme Birleştir](./media/how-to-move-peering/routefilter.jpg)

Rota filtreleri aşağıdaki makalelerden birini kullanarak yapılandırın:

* [Azure portalını kullanarak Microsoft eşlemesi için rota filtreleri yapılandırma](how-to-routefilter-portal.md)<br>
* [Azure PowerShell kullanarak Microsoft eşlemesi için rota filtreleri yapılandırma](how-to-routefilter-powershell.md)<br>
* [Azure CLI kullanarak Microsoft eşlemesi için rota filtreleri yapılandırma](how-to-routefilter-cli.md)

## <a name="delete"></a>4. genel eşlemeyi silme

Microsoft eşlemesi yapılandırılmış ve Microsoft eşlemesi üzerinde kullanmak istediğiniz ön ekleri doğru tanıtıldığından doğruladıktan sonra genel eşleme sonra silebilirsiniz. Ortak eşlemesini silmek için aşağıdaki makalelerden birini kullanın:

* [Azure portalını kullanarak Azure ortak eşlemesini Sil](expressroute-howto-routing-portal-resource-manager.md#deletepublic)<br>
* [Azure PowerShell kullanarak Azure ortak eşlemesini Sil](expressroute-howto-routing-arm.md#deletepublic)<br>
* [CLI kullanarak Azure ortak eşlemesini Sil](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. eşlemeleri görüntüleme
  
Tüm ExpressRoute devreleri ve Azure portalında eşlemeleri listesini görebilirsiniz. Daha fazla bilgi için [görünümü Microsoft eşleme ayrıntılarını](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Sonraki adımlar

ExpressRoute hakkında daha fazla bilgi için, bkz. [ExpressRoute SSS](expressroute-faqs.md).
