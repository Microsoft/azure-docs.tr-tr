---
title: "Azure ExpressRoute: genel eşlemeyi Microsoft eşlemesi 'ne taşıma"
description: Bu makalede, ExpressRoute üzerinde genel eşinizi Microsoft eşlemesi 'ne taşıma adımları gösterilmektedir.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/12/2019
ms.author: duau
ms.openlocfilehash: 608d6c87442821e904fde16c6b75841fe792e3f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206297"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Genel eşlemeden Microsoft eşlemesine geçiş yapma

Bu makale, bir genel eşleme yapılandırmasını kapalı kalma süresi olmadan Microsoft eşlemesi 'ne taşımanıza yardımcı olur. ExpressRoute, Azure depolama ve Azure SQL Veritabanı gibi Azure PaaS hizmetleri için rota filtreleriyle Microsoft eşlemesinin kullanılmasını sağlar. Artık Microsoft PaaS ve SaaS hizmetlerine erişmek için tek bir yönlendirme etki alanına ihtiyacınız vardır. Kullanmak istediğiniz Azure bölgeleri için PaaS hizmeti ön eklerini tanıtmak için rota filtreleri kullanabilirsiniz.

Azure genel eşleme, her BGP oturumuyla ilişkili 1 NAT IP adresine sahiptir. Microsoft eşleme, kendi NAT ayırmalarınızı yapılandırmanıza ve seçmeli ön ek tanıtımları için yol filtrelerini kullanmanıza olanak sağlar. Genel eşleme, her zaman WAN 'dan Microsoft Azure hizmetlere hangi bağlantının başlatıldığını kullanan tek yönlü bir hizmettir. Microsoft Azure hizmetler, bu yönlendirme etki alanı üzerinden ağınıza bağlantı başlatamaz.

Ortak eşleme etkinleştirildikten sonra tüm Azure hizmetlerine bağlanabilirsiniz. Yolların tanıtıldığı Hizmetleri seçmeli olarak seçmenizi vermedik. Microsoft eşlemesi, bağlantının WAN ile birlikte Microsoft Azure hizmetten başlatılabileceği iki yönlü bir bağlantıdır. Yönlendirme etki alanları ve eşleme hakkında daha fazla bilgi için bkz. [ExpressRoute devreleri ve yönlendirme etki alanları](expressroute-circuit-peerings.md).

## <a name="before-you-begin"></a><a name="before"></a>Başlamadan önce

Microsoft eşlemesiyle bağlantı kurmak için NAT ayarlamanız ve yönetmeniz gerekir. Bağlantı sağlayıcınız NAT 'ı yönetilen bir hizmet olarak ayarlayabilir ve yönetebilir. Azure PaaS ve Azure SaaS hizmetlerine Microsoft eşlemesi üzerinde erişmeyi planlıyorsanız, NAT IP havuzunu doğru bir şekilde boyutlandırmamak önemlidir. ExpressRoute için NAT hakkında daha fazla bilgi için bkz. [Microsoft eşlemesi Için NAT gereksinimleri](expressroute-nat.md#nat-requirements-for-microsoft-peering). Azure ExpressRoute (Microsoft eşlemesi) üzerinden Microsoft 'a bağlandığınızda, Microsoft 'a yönelik birden fazla bağlantı vardır. Bağlantıların biri, var olan İnternet bağlantınız diğeri de ExpressRoute aracılığıyla olan bağlantı. Microsoft’a yönelik trafiğin bir kısmı İnternet üzerinden gidip ExpressRoute üzerinden dönebilir ya da tam tersi gerçekleşebilir.

![Çift yönlü bağlantı](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> Microsoft'a tanıtılan NAT IP havuzu İnternet'e tanıtılmamalıdır. Bu, diğer Microsoft hizmetlerine bağlantıyı keser.

Microsoft eşlemesini yapılandırmadan önce asimetrik yönlendirme uyarıları için [birden çok ağ yoluyla asimetrik yönlendirmeye](./expressroute-asymmetric-routing.md) bakın.

* Ortak eşleme kullanıyorsanız ve şu anda [Azure depolama](../storage/common/storage-network-security.md) veya [Azure SQL veritabanı](../azure-sql/database/vnet-service-endpoint-rule-overview.md)'na erışmek için kullanılan genel IP adresleri için IP ağ kurallarına sahipseniz, MICROSOFT EŞLEMESIYLE yapılandırılmış NAT IP havuzunun Azure depolama hesabı veya Azure SQL hesabı için genel IP adresleri listesine eklendiğinden emin olmanız gerekir.<br>
* Kapalı kalma süresi olmadan Microsoft eşlemesine geçiş yapmak için, bu makaledeki adımları sunuldukları sırada kullanın.

## <a name="1-create-microsoft-peering"></a><a name="create"></a>1. Microsoft eşlemesi oluştur

Microsoft eşlemesi oluşturulmadıysa, Microsoft eşlemesi oluşturmak için aşağıdaki makalelerden birini kullanın. Bağlantı sağlayıcınız yönetilen katman 3 hizmetleri sunuyorsa, bağlantı sağlayıcısına bağlantı sağlayıcıya, devrenizin için Microsoft eşlemeyi etkinleştirmesini isteyebilirsiniz.

Katman 3 ' ü yönetilmiyorsa, devam etmeden önce aşağıdaki bilgiler gereklidir:

* Birincil bağlantı için bir /30 alt ağı. Bu size ait ve bir RIR / IRR içinde kayıtlı bir geçerli ortak IPv4 ön eki olmalıdır. Bu alt ağdan, Microsoft 'un yönlendiricisi için kullanılan ikinci IP adresini kullandığından, ilk yapılandırılmış IP adresini yönlendiricinize atayacaksınız.<br>
* İkincil bağlantı için bir /30 alt ağı. Bu size ait ve bir RIR / IRR içinde kayıtlı bir geçerli ortak IPv4 ön eki olmalıdır. Bu alt ağdan, Microsoft 'un yönlendiricisi için kullanılan ikinci IP adresini kullandığından, ilk yapılandırılmış IP adresini yönlendiricinize atayacaksınız.<br>
* Bu eşlemenin kurulacağı geçerli bir VLAN kimliği. Bağlantı hattındaki başka bir eşlemenin aynı VLAN kimliğini kullanmadığından emin olun. Birincil ve Ikincil bağlantılar için aynı VLAN KIMLIĞINI kullanmanız gerekir.<br>
* Eşleme için AS numarası. 2 bayt ve 4 bayt AS numaralarını kullanabilirsiniz.<br>
* Tanıtılan önekler: BGP oturumunda tanıtmayı planladığınız tüm öneklerin bir listesini sağlamanız gerekir. Yalnızca ortak IP adresi ön ekleri kabul edilir. Bir önek kümesi gönderilmesini planlıyorsanız, virgülle ayrılmış bir liste gönderebilirsiniz. Bu önekler size bir RIR / IRR içinde kaydedilmiş olmalıdır.<br>
* Yönlendirme Kayıt Defteri Adı: AS numarası ve öneklerinin kaydedildiği RIR / IRR’yi belirtebilirsiniz.

* **Isteğe bağlı** -müşteri ASN: eşleme numarasına kayıtlı olmayan ön ekler varsa, BUNLARıN kaydedildiği as numarasını belirtebilirsiniz.<br>
* **Isteğe bağlı** -birini kullanmayı SEÇERSENIZ bir MD5 karma değeri.

Aşağıdaki makalelerde Microsoft eşlemesini etkinleştirmeye yönelik ayrıntılı yönergeler bulabilirsiniz:

* [Azure portal kullanarak Microsoft eşlemesi oluşturma](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Azure PowerShell kullanarak Microsoft eşlemesi oluşturma](expressroute-howto-routing-arm.md#msft)<br>
* [Azure CLı kullanarak Microsoft eşlemesi oluşturma](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a><a name="validate"></a>2. Microsoft eşlemesini doğrulama etkin

Microsoft eşlemesinin etkinleştirildiğini ve tanıtılan genel ön eklerin yapılandırılmış durumda olduğunu doğrulayın.

* [Azure portalındaki](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a><a name="routefilter"></a>3. devreye bir yol filtresi yapılandırın ve ekleyin

Varsayılan olarak, yeni Microsoft eşlemesi, bir yol filtresi devresine iliştirilene kadar herhangi bir önek duyurmaz. Bir rota filtresi kuralı oluşturduğunuzda, Azure PaaS hizmetleri için kullanmak istediğiniz Azure bölgelerinin hizmet topluluklarıyla ilgili listeyi belirtebilirsiniz. Bu, aşağıdaki ekran görüntüsünde gösterildiği gibi, bu yolları gereksiniminize göre filtreleme esnekliği sağlar:

![Ortak eşlemeyi Birleştir](./media/how-to-move-peering/routefilter.jpg)

Aşağıdaki makalelerden birini kullanarak yol filtrelerini yapılandırın:

* [Azure portal kullanarak Microsoft eşlemesi için yol filtrelerini yapılandırma](how-to-routefilter-portal.md)<br>
* [Azure PowerShell kullanarak Microsoft eşlemesi için yol filtrelerini yapılandırma](how-to-routefilter-powershell.md)<br>
* [Azure CLı kullanarak Microsoft eşlemesi için rota filtrelerini yapılandırma](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a><a name="delete"></a>4. genel eşlemeyi silme

Microsoft eşlemesinin yapılandırıldığını doğruladıktan ve kullanmak istediğiniz ön ekler Microsoft eşlemesi üzerinde doğru şekilde tanıtıldıktan sonra, genel eşlemeyi silebilirsiniz. Ortak eşlemeyi silmek için aşağıdaki makalelerden birini kullanın:

* [Azure PowerShell kullanarak Azure ortak eşlemesini silme](about-public-peering.md#powershell)
* [CLı kullanarak Azure ortak eşlemesini silme](about-public-peering.md#cli)
  
## <a name="5-view-peerings"></a><a name="view"></a>5. eşlemeleri görüntüleme
  
Azure portal tüm ExpressRoute devreleri ve eşlerinin bir listesini görebilirsiniz. Daha fazla bilgi için bkz. [Microsoft eşleme ayrıntılarını görüntüleme](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Sonraki adımlar

ExpressRoute hakkında daha fazla bilgi için, bkz. [ExpressRoute SSS](expressroute-faqs.md).