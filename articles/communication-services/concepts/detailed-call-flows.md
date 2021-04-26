---
title: Azure Iletişim hizmetlerinde çağrı akışı topolojileri
titleSuffix: An Azure Communication Services concept document
description: Azure Iletişim hizmetlerinde çağrı akışı topolojileri hakkında bilgi edinin.
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 526e3a1e4eeb6ef6a31a33498241d9a7443cca35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103490645"
---
# <a name="call-flow-topologies"></a>Çağrı akışı topolojileri
Bu makalede, Azure Communication Services çağrı akışı topolojileri açıklanır. Bu, yönettiğiniz bir ağ içinde Iletişim hizmetlerini tümleştirerek kurumsal bir müşteri olduğunuzu gözden geçirmek için harika bir makaledir. Iletişim Hizmetleri çağrı akışlarına giriş için, [çağrı akışları kavramsal belgelerini](./call-flows.md)ziyaret edin.

## <a name="background"></a>Arka Plan

### <a name="network-concepts"></a>Ağ kavramları

Çağrı akışı topolojilerini gözden geçirmeden önce belge genelinde başvurulan bazı terimleri tanımlayacağız.

Bir **müşteri ağı** yönettiğiniz tüm ağ kesimlerini içerir. Bu, ofisinizdeki kablolu ve kablosuz ağları ya da ofisler, veri merkezleri ve internet hizmet sağlayıcıları arasında bulunabilir.

Bir müşteri ağı genellikle güvenlik duvarları ve/veya kuruluşunuzun güvenlik ilkelerini zorlayan proxy sunucularıyla birkaç ağ perimetrelerini içerir. İletişim çözümünüzün en iyi performansını ve kalitesini sağlamak için [kapsamlı bir ağ değerlendirmesi](/microsoftteams/3-envision-evaluate-my-environment) gerçekleştirmenizi öneririz.

**Iletişim Hizmetleri ağı** , Azure iletişim hizmetlerini destekleyen ağ kesimidir. Bu ağ Microsoft tarafından yönetiliyor ve çoğu müşteri ağına yakın kenarlarla birlikte dünya genelinde dağıtılır. Bu ağ aktarım geçişinden, grup çağrıları için medya işlemeden ve zengin gerçek zamanlı medya iletişimini destekleyen diğer bileşenlere sorumludur.

### <a name="types-of-traffic"></a>Trafik türleri

İletişim Hizmetleri, birincil olarak iki tür trafik üzerinde oluşturulmuştur: **gerçek zamanlı medya** ve **sinyal**.

Gerçek zamanlı **medya** gerçek zamanlı Aktarım Protokolü (RTP) kullanılarak iletilir. Bu protokol ses, video ve ekran paylaşımı veri aktarımını destekler. Bu veriler ağ gecikmesi sorunlarına duyarlıdır. TCP veya HTTP kullanarak gerçek zamanlı medya iletilmesi mümkün olsa da, yüksek performanslı son kullanıcı deneyimlerini desteklemek için Aktarım katmanı protokolü olarak UDP kullanılmasını öneririz. RTP üzerinden aktarılan medya yükleri SRTP kullanılarak güvenlidir.

Iletişim Hizmetleri çözümünüzün kullanıcıları, kendi istemci cihazlarından hizmetlerinize bağlanacaktır. Bu cihazlarla sunucularınız arasındaki iletişim **sinyal** ile işlenir. Örneğin: çağrı başlatma ve gerçek zamanlı sohbet, cihazlar ve hizmetiniz arasındaki sinyalde desteklenir. Çoğu sinyal trafiği HTTPS REST kullanır, ancak bazı senaryolarda SIP, sinyal trafiği protokolü olarak kullanılabilir. Bu tür trafik gecikme süresine daha az duyarlıysa, düşük gecikme süreli sinyal, çözümünüzün kullanıcılarına bir son kullanıcı deneyimi sağlar.

### <a name="interoperability-restrictions"></a>Birlikte çalışabilirlik kısıtlamaları

Iletişim hizmetlerinden medya akışı aşağıdaki gibi kısıtlanmıştır:

**Üçüncü taraf medya geçişleri.** Üçüncü taraf medya geçişleri ile birlikte çalışabilirlik desteklenmez. Medya uç noktalarınızdan birinde Iletişim Hizmetleri varsa, ortamınız yalnızca Microsoft ekiplerini ve Skype Kurumsal 'ı destekleenler dahil olmak üzere Microsoft tarafından yerel medya geçişine çapraz geçiş yapabilir.

PSTN ile sınırındaki bir üçüncü taraf oturum sınır denetleyicisi (SBC), SRTP kullanılarak güvenliği sağlanan RTP/RTCP akışını sonlanabilir ve bir sonraki atlamaya geçirmemelidir. Akışı bir sonraki atlamaya geçirçalışırsanız, bu, anlaşılmayabilir.

**Üçüncü taraf SIP Proxy sunucuları.** Üçüncü taraf bir SBC ve/veya Gateway 'e sahip bir Iletişim Hizmetleri sinyal iletişim kutusu, Microsoft yerel SIP Proxy 'leri (tıpkı takımlar gibi) ile çapraz geçiş yapar. Üçüncü taraf SIP Proxy 'leriyle birlikte çalışabilirlik desteklenmez.

**Üçüncü taraf B2BUA (veya SBC).** PSTN 'den ve bu bilgisayardan gelen iletişim hizmetleri medya akışı, üçüncü taraf bir SBC tarafından sonlandırılır. Iletişim Hizmetleri ağı içindeki bir üçüncü taraf SBC ile birlikte çalışabilirlik (bir üçüncü taraf SBC 'nin iki Iletişim hizmeti uç noktasını desteklediği) desteklenmez.

### <a name="unsupported-technologies"></a>Desteklenmeyen teknolojiler

**VPN ağı.** İletişim Hizmetleri VPN üzerinden medya aktarımını desteklemez. Kullanıcılarınız VPN istemcileri kullanıyorsa, istemci, [BIR VPN tüneli atlamak Için Lync medyasını etkinleştirme](https://techcommunity.microsoft.com/t5/skype-for-business-blog/enabling-lync-media-to-bypass-a-vpn-tunnel/ba-p/620210) bölümünde belirtildiği gibi, medya trafiğini VPN olmayan bir bağlantı üzerinden bölüder ve yönlendirmelidir.

*Notun. Başlık Lync 'i gösteriyorsa, Azure Iletişim Hizmetleri ve takımlar için de geçerlidir.*

**Paket mil.** Paket kırpma, paket denetleme veya paket şekillendirme cihazları desteklenmez ve kalite önemli ölçüde düşebilir.

### <a name="call-flow-principles"></a>Çağrı akışı ilkeleri

Iletişim Hizmetleri çağrı akışlarını sabitlemek için dört genel ilke vardır:

* **Bir Iletişim Hizmetleri Grup çağrısının ilk katılımcısı, çağrının barındırıldığı bölgeyi belirler**. Aşağıda açıklanan bazı topolojilerde bu kuralın özel durumları vardır.
* **Iletişim Hizmetleri çağrısını desteklemek için kullanılan medya uç noktası, medya işleme ihtiyaçlarına göre seçilir** ve bir çağrı üzerindeki katılımcı sayısından etkilenmez. Örneğin, noktadan noktaya çağrı, medyayı döküm veya kaydetme amacıyla işlemek için bulutta bir medya uç noktası kullanabilir, ancak iki katılımcı içeren bir çağrı herhangi bir medya uç noktası kullanmayabilir. Grup çağrıları, karıştırma ve yönlendirme amacıyla bir medya uç noktası kullanır. Bu uç nokta, çağrının barındırıldığı bölgeye göre belirlenir. Bir istemciden medya uç noktasına gönderilen medya trafiği doğrudan yönlendirilebilir veya müşteri ağ güvenlik duvarı kısıtlamaları gerektiriyorsa Azure 'da bir taşıma geçişi kullanabilir.
* **Eşler arası çağrılar Için medya trafiği**,, çağrının buluttaki bir medya uç noktasına ihtiyaç duymayın kabul edilen en doğrudan yolu alır. Tercih edilen yol, uzak eşe (istemci) doğrudan yapılır. Bir doğrudan yol yoksa, bir veya daha fazla aktarım geçişi trafiği geçirir. Medya trafiği, paket shayt, VPN sunucuları veya işleme ve son kullanıcı deneyimini geciktirebilecek diğer işlevler gibi davranan sunucuları enine içermemelidir.
* **Sinyal trafiği her zaman kullanıcıya en yakın olan sunucuya gider.**

Seçilen medya yolundaki ayrıntılar hakkında daha fazla bilgi edinmek için [çağrı akışları kavramsal belgelerini](./call-flows.md)inceleyin.


## <a name="call-flows-in-various-topologies"></a>Çeşitli topolojilerde akış çağırma

### <a name="communication-services-internet"></a>İletişim Hizmetleri (internet)

Bu topoloji, SIP arabirimi gibi şirket içi dağıtımlar olmadan buluttan Iletişim hizmetlerini kullanan müşteriler tarafından kullanılır. Bu topolojide, Iletişim hizmetlerinden gelen ve giden trafik Internet üzerinden akar.

:::image type="content" source="./media/call-flows/detailed-flow-general.png" alt-text="Azure Iletişim Hizmetleri topolojisi.":::

*Şekil 1-Iletişim Hizmetleri topolojisi*

Yukarıdaki diyagramda okların yönü, kurumsal perimeters bağlantısını etkileyen iletişimin başlatma yönünü yansıtır. Medya için UDP söz konusu olduğunda, ilk paket ters yönde akabilir, ancak diğer yönlerdeki paketler akana kadar bu paketler engellenebilir.

Akış açıklamaları:
* Flow 2 * – kullanıcının Iletişim Hizmetleri deneyiminin bir parçası olarak müşteri ağındaki bir kullanıcı tarafından başlatılan bir akışı temsil eder. Bu akışlara örnek olarak DNS ve eşler arası medya iletimi sayılabilir.
* Flow 2 – bir uzak mobil Iletişim Hizmetleri kullanıcısı tarafından başlatılan ve müşteri ağına VPN içeren bir akışı temsil eder.
* Flow 3 – uzak bir mobil Iletişim Hizmetleri kullanıcısı tarafından hizmet uç noktalarına Iletişim kurmak için başlatılan bir akışı temsil eder.
* Flow 4 – Hizmetleri Iletişim için müşteri ağındaki bir kullanıcı tarafından başlatılan bir akışı temsil eder.
* Flow 5: bir Iletişim Hizmetleri kullanıcısı ve müşteri ağı içindeki bir diğeri arasındaki eşler arası medya akışını temsil eder.
* Flow 6 – Internet üzerinden uzak bir mobil Iletişim Hizmetleri kullanıcısı ve başka bir uzak mobil Iletişim Hizmetleri kullanıcısı arasındaki eşler arası medya akışını temsil eder.

### <a name="use-case-one-to-one"></a>Kullanım örneği: bire bir

Bire bir çağrı, arayanın yerel, geçiş ve yansımalı (geçiş tarafından görülen genel IP adresi) adayları dahil IP adreslerinden/bağlantı noktalarından oluşan bir aday kümesi edineceği ortak bir model kullanır. Çağıran, bu adayları bilinen tarafa gönderir; çağrılan taraf Ayrıca benzer bir aday kümesi alır ve bunları çağırana gönderir. Şirket dışı bağlantı denetimi iletileri, hangi çağıran/bilinen taraf medya yollarının çalıştığını ve en iyi çalışma yolunun seçili olduğunu bulmak için kullanılır. Medya (yani, SRTP kullanılarak güvenliği sağlanmış olan RTP/RTCP paketleri), daha sonra seçili aday çifti kullanılarak gönderilir. Aktarım geçişi, Azure Iletişim Hizmetleri 'nin bir parçası olarak dağıtılır.

Yerel IP adresi/bağlantı noktası adayları veya yansımalı adayların bağlantısı varsa, istemciler arasındaki doğrudan yol (veya bir NAT kullanarak) medya için seçilir. İstemciler her ikisi de müşteri ağı üzerinde ise, doğrudan yol seçilmelidir. Bu, müşteri ağı dahilinde doğrudan UDP bağlantısı gerektirir. İstemcilerin ikisi de Nomadic bulut kullanıcılardır, NAT/güvenlik duvarına bağlı olarak, medya doğrudan bağlantı kullanabilir.

Müşteri ağında bir istemci dahili ise ve bir istemci harici ise (örneğin, bir mobil bulut kullanıcısı), yerel veya yansımalı adaylar arasındaki doğrudan bağlantının çalışıyor olması çok düşüktür. Bu durumda, bir seçenek, herhangi bir istemciden aktarım geçiş adaylarından birini kullanmaktır (örneğin, iç istemci Azure 'daki aktarım geçişinin bir geçiş adayını aldı; dış istemcinin, aktarım geçişine STUN/RTP/RTCP paketleri gönderebilmesi gerekir). Diğer bir seçenek de, iç istemci mobil bulut istemcisi tarafından edinilen geçiş adayını gönderir. Medya için UDP bağlantısı önemle önerilir, ancak TCP desteklenir.

**Üst düzey adımlar:**
1.  İletişim hizmetleri kullanıcısı A, Flow 2 kullanarak URL etki alanı adını (DNS) çözümler.
2.  A kullanıcısı, akış 4 ' ü kullanarak takımlar aktarım geçişine bir medya geçiş bağlantı noktası ayırır.
3.  İletişim hizmetleri kullanıcısı A, Flow 4 ' ü kullanarak Iletişim hizmetlerine bir "davet" gönderir.
4.  İletişim Hizmetleri, Flow 4 ' ü kullanarak B kullanıcısına bildirir.
5.  B kullanıcısı, akış 4 ' ü kullanarak takımlar aktarım geçişi üzerinde bir medya geçiş bağlantı noktası ayırır.
6.  B kullanıcısı, Flow 4 ' ü kullanarak Kullanıcı A 'ya iletilen Flow 4 ' ü kullanarak "Yanıtla" yanıtını gönderir.
7.  Kullanıcı A ve Kullanıcı B, buz bağlantısı testlerini çağırır ve kullanılabilir en iyi medya yolu seçilidir (çeşitli kullanım durumları için aşağıdaki diyagramlara bakın).
8.  Her iki kullanıcı da Flow 4 kullanarak Iletişim hizmetlerine telemetri gönderir.

### <a name="customer-network-intranet"></a>Müşteri ağı (intranet)

:::image type="content" source="./media/call-flows/one-to-one-internal.png" alt-text="Müşteri ağı içindeki trafik akışı.":::

*Şekil 2-müşteri ağı dahilinde*

7. adımda eşler arası medya akışı 5 seçilidir.

Bu medya iletimi çift yönlü. Flow 5 ' in yönü, bir tarafın bir bağlantı perspektifinden iletişimi başlatdığını gösterir. Bu durumda, her iki uç nokta de müşteri ağı dahilinde olduğu için hangi yönün kullanılması gerektiği konusunda değildir.

### <a name="customer-network-to-external-user-media-relayed-by-teams-transport-relay"></a>Müşteri ağını dış Kullanıcı (takımlar tarafından geçirilen Medya aktarım geçişi)

:::image type="content" source="./media/call-flows/one-to-one-via-relay.png" alt-text="Bir geçiş aracılığıyla bir adet çağrı akışına.":::

*Şekil 3-dışarıdan kullanıcıya ait müşteri ağı (Azure aktarım geçişine göre aktarılan medya)*

7. adımda, Flow 4 (müşteri ağından Iletişim hizmetlerine) ve Flow 3 (uzak bir mobil Iletişim Hizmetleri kullanıcıdan Azure Iletişim hizmetlerine kadar) seçilir.

Bu akışlar, Azure 'da takımlar aktarım geçişi tarafından aktarılır.

Bu medya iletimi çift yönlü. Yön, bir bağlantı perspektifinden iletişimin hangi tarafına başladığını gösterir. Bu durumda, bu akışlar farklı aktarım protokolleri ve adresler kullanılarak sinyal ve medya için kullanılır.

### <a name="customer-network-to-external-user-direct-media"></a>Müşteri ağından dış kullanıcıya (doğrudan medya)

:::image type="content" source="./media/call-flows/one-to-one-with-extenal.png" alt-text="Bir dış kullanıcıyla tek bir çağrı akışına.":::

*Şekil 4-dış kullanıcıya (doğrudan medya) müşteri ağı*

7. adımda, Flow 2 ' de (müşteri ağından istemciye Internet üzerinden istemci eşi) seçilidir.

Uzak mobil kullanıcıyla (Azure üzerinden geçirilmediğinden) doğrudan medya isteğe bağlıdır. Diğer bir deyişle, Azure 'da bir aktarım geçişi aracılığıyla medya yolunu zorlamak için bu yolu engelleyebilirsiniz.

Bu medya iletimi çift yönlü. Flow 2 ' nin uzak mobil kullanıcıya yönü, bir tarafın bir bağlantı perspektifinden iletişimi başlatdığını gösterir.

### <a name="vpn-user-to-internal-user-media-relayed-by-teams-transport-relay"></a>İç kullanıcıya VPN kullanıcısı (ekipler aktarım geçişine göre geçirilen medya)

:::image type="content" source="./media/call-flows/vpn-to-internal-via-relay.png" alt-text="Bir VPN kullanıcısına geçiş yoluyla tek bir çağrı akışına.":::

*Şekil 5-iç kullanıcıya VPN kullanıcısı (Azure Relay tarafından geçirilen medya*

Müşteri ağına VPN arasındaki sinyal, Flow 2 * ' i kullanır. Müşteri ağı ile Azure arasındaki sinyal, Flow 4 ' ü kullanır. Ancak medya VPN 'i atlar ve Azure Medya geçişi aracılığıyla 3 ve 4. akışlar kullanılarak yönlendirilir.

### <a name="vpn-user-to-internal-user-direct-media"></a>VPN kullanıcısını iç kullanıcıya (doğrudan medya)

:::image type="content" source="./media/call-flows/vpn-to-internal-direct-media.png" alt-text="Doğrudan medya içeren bir VPN ile tek bir çağrı akışına (iç Kullanıcı)":::

*Şekil 6-iç kullanıcıya VPN kullanıcısı (doğrudan medya)*

Müşteri ağına VPN arasındaki sinyal, Flow 2 ' i kullanır. Müşteri ağı ile Azure arasında sinyal verme, Flow 4 ' ü kullanıyor. Ancak medya VPN 'i atlar ve müşteri ağından Internet 'e akış 2 kullanılarak yönlendirilir.

Bu medya iletimi çift yönlü. Flow 2 ' nin uzak mobil kullanıcıya yönü, bir tarafın bir bağlantı perspektifinden iletişimi başlatdığını gösterir.

### <a name="vpn-user-to-external-user-direct-media"></a>VPN kullanıcısını dış kullanıcıya (doğrudan medya)

:::image type="content" source="./media/call-flows/vpn-user-to-external-user.png" alt-text="Doğrudan medya içeren VPN ile bir adet çağrı akışına (Dış Kullanıcı)":::

*Şekil 7-dış kullanıcıya VPN kullanıcısı (doğrudan medya)*

VPN kullanıcısı ile müşteri ağı arasındaki sinyal, Flow 2 * ve Flow 4 ' ü Azure 'da kullanır. Ancak medya VPN 'i atlar ve Flow 6 kullanılarak yönlendirilir.

Bu medya iletimi çift yönlü. Flow 6 ' nın uzak mobil kullanıcıya yönü, bir tarafın bir bağlantı perspektifinden iletişimi başlatdığını gösterir.

### <a name="use-case-communication-services-client-to-pstn-through-communication-services-trunk"></a>Kullanım örneği: iletişim Hizmetleri istemcisi ile PSTN arası iletişim hizmetleri santral

İletişim Hizmetleri, genel anahtarlı telefon ağından (PSTN) çağrıların alınmasına ve alınmasına izin verir. PSTN santral Iletişim Hizmetleri tarafından sunulan telefon numaraları kullanılarak bağlıysa, bu kullanım örneği için özel bir bağlantı gereksinimi yoktur. Kendi şirket içi PSTN santral 'yı Azure Communication Services 'a bağlamak istiyorsanız, SIP arabirimini (CY2021 ' de kullanılabilir) kullanabilirsiniz.

:::image type="content" source="./media/call-flows/acs-to-pstn.png" alt-text="Bir PSTN katılımcısı ile tek bir çağrıya":::

*Şekil 8 – Iletişim Hizmetleri kullanıcısı ile Azure santral arasında Kullanıcı*

Bu durumda, müşteri ağından Azure 'a sinyal ve medya kullanma Flow 4.

### <a name="use-case-communication-services-group-calls"></a>Kullanım örneği: Iletişim Hizmetleri Grup çağrıları

Ses/video/ekran paylaşımı (VBSS) hizmeti, Azure Iletişim hizmetlerinin bir parçasıdır. Müşterinin ağından erişilebilir olması gereken genel bir IP adresi vardır ve bir Nomadic bulut istemcisinden ulaşılabilir olmalıdır. Her bir istemci/uç noktasının hizmete bağlanabilmesi gerekir.

İç istemciler yerel, yansımalı ve geçiş adaylarını bire bir çağrılar için açıklandığı gibi aynı şekilde elde eder. İstemciler bu adayları bir davette hizmete gönderir. Hizmet, genel olarak erişilebilen bir IP adresine sahip olduğundan bir geçiş kullanmaz, bu nedenle yerel IP adresi adayı ile yanıt verir. İstemci ve hizmet bağlantıyı bire bir çağrılar için açıklanan şekilde denetler.

:::image type="content" source="./media/call-flows/acs-group-calls.png" alt-text="OACS grup çağrısı":::

*Şekil 9 – Iletişim Hizmetleri Grup çağrıları*

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Çağırma ile çalışmaya başlayın](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Aşağıdaki belgeler sizin için ilginç olabilir:

- [Çağrı türleri](../concepts/voice-video-calling/about-call-types.md) hakkında daha fazla bilgi edinin
- [İstemci-sunucu mimarisi](./client-and-server-architecture.md) hakkında bilgi edinin