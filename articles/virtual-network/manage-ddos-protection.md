---
title: Azure portal kullanarak Azure DDoS koruması standardını yönetme
titlesuffix: Azure Virtual Network
description: Bir saldırıyı azaltmak için Azure Izleyici 'de Azure DDoS koruması standart telemetrisini nasıl kullanacağınızı öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 37a8799ca1ea986d5b47dad6e17781d7dfbacfab
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261692"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Azure portal kullanarak Azure DDoS koruması standardını yönetme

Dağıtılmış hizmet reddi (DDoS) korumasını etkinleştirmeyi ve devre dışı bırakmayı öğrenin ve Azure DDoS koruma standardı ile DDoS saldırılarını azaltmak için telemetri ' i kullanın. DDoS koruma standardı, sanal makineler, yük dengeleyiciler ve kendisine atanmış bir Azure [genel IP adresi](virtual-network-public-ip-address.md) olan uygulama ağ geçitleri gibi Azure kaynaklarını korur. DDoS koruma standardı ve özellikleri hakkında daha fazla bilgi edinmek için bkz. [DDoS koruması standardına genel bakış](ddos-protection-overview.md).

Bu öğreticide herhangi bir adımı tamamlamadan önce, [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne atanan bir https://portal.azure.com hesapla veya [izinlerde](#permissions)listelenen uygun eylemlere atanmış [özel bir rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ile Azure Portal 'de oturum açın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-a-ddos-protection-plan"></a>Bir DDoS koruması planı oluşturma

DDoS koruma planı, aboneliklerde DDoS koruma standardı etkin olan bir sanal ağ kümesini tanımlar. Kuruluşunuz için bir DDoS koruma planı yapılandırabilir ve sanal ağları birden çok abonelikten aynı plana bağlayabilirsiniz. DDoS koruma planının kendisi de planın oluşturulması sırasında seçtiğiniz bir abonelikle ilişkilendirilir. DDoS koruma planı bölgeler ve abonelikler arasında çalışmaktadır. Örnek-planı Doğu ABD bölgesinde oluşturabilir ve kiracınızdaki abonelik #1 bağlantı oluşturabilirsiniz. Aynı plan, kiracınız genelinde farklı bölgelerdeki diğer aboneliklerdeki sanal ağlara bağlanabilir. Planın ilişkilendirildiği abonelik, plan için aylık yinelenen faturanızı ve korunan genel IP adreslerinin sayısının 100 ' i aşması durumunda fazla kullanım ücreti sağlar. DDoS fiyatlandırması hakkında daha fazla bilgi için bkz. [fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/ddos-protection/).

Çoğu kuruluş için birden fazla planın oluşturulması gerekli değildir. Bir plan abonelikler arasında taşınamaz. Bir planın bulunduğu aboneliği değiştirmek istiyorsanız, [var olan planı silmeniz](#work-with-ddos-protection-plans) ve yeni bir tane oluşturmanız gerekir.

1. Azure portal sol üst köşesinde **kaynak oluştur** ' u seçin.
2. *DDoS*araması yapın. Ara sonuçlarda **DDoS koruma planı** göründüğünde, bunu seçin.
3. **Oluştur**’u seçin.
4. Kendi değerlerinizi girin veya seçin ya da aşağıdaki örnek değerleri girin veya seçin ve ardından **Oluştur**' u seçin:

    |Ayar        |Value                                              |
    |---------      |---------                                          |
    |Name           | myDdosProtectionPlan                              |
    |Subscription   | Aboneliğinizi seçin.                         |
    |Resource group | **Yeni oluştur** ' u seçin ve *myresourcegroup* girin |
    |Location       | East US                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Yeni bir sanal ağ için DDoS 'ı etkinleştir

1. Azure portal sol üst köşesinde **kaynak oluştur** ' u seçin.
2. **Ağ**’ı ve sonra **Sanal ağ**’ı seçin.
3. Kendi değerlerinizi girin veya seçin, aşağıdaki örnek değerleri girin veya seçin, kalan Varsayılanları kabul edin ve **Oluştur**' u seçin:

    | Ayar         | Value                                                        |
    | ---------       | ---------                                                    |
    | Name            | myVirtualNetwork                                             |
    | Subscription    | Aboneliğinizi seçin.                                    |
    | Resource group  | **Var olanı kullan**’ı seçin ve sonra **myResourceGroup** seçeneğini belirleyin |
    | Location        | East US                                                      |
    | DDos koruması | **Standart** ' ı seçin ve **DDoS koruması**altında **myddosprotectionplan**' ı seçin. Seçtiğiniz plan, sanal ağdan aynı veya farklı bir abonelikte olabilir, ancak her iki aboneliğin de aynı Azure Active Directory kiracısıyla ilişkilendirilmesi gerekir.|

Sanal ağ için DDoS standardı etkinleştirildiğinde bir sanal ağı başka bir kaynak grubuna veya aboneliğe taşıyamazsınız. DDoS standart özellikli bir sanal ağı taşımanız gerekiyorsa, önce DDoS standardını devre dışı bırakın, sanal ağı taşıyın ve ardından DDoS standardını etkinleştirin. Taşıma işleminden sonra, sanal ağ içindeki tüm korumalı genel IP adresleri için otomatik olarak ayarlanan ilke eşikleri sıfırlanır.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Mevcut bir sanal ağ için DDoS 'ı etkinleştir

1. DDoS koruma planı [oluşturma](#create-a-ddos-protection-plan)bölümündeki adımları tamamlayarak bir DDoS koruma planı oluşturun.
2. Azure portal sol üst köşesinde **kaynak oluştur** ' u seçin.
3. Portalın üst kısmındaki **kaynakları, hizmetleri ve belgeleri ara kutusuna** Için DDoS koruması standardını etkinleştirmek istediğiniz sanal ağın adını girin. Arama sonuçlarında sanal ağın adı göründüğünde seçin.
4. **Ayarlar**altında **DDoS koruması**' nı seçin.
5. **Standart**' ı seçin. **DDoS koruma planı**altında var olan bir DDoS koruma planı veya 1. adımda oluşturduğunuz planı seçin ve ardından **Kaydet**' i seçin. Seçtiğiniz plan, sanal ağdan aynı veya farklı bir abonelikte olabilir, ancak her iki aboneliğin de aynı Azure Active Directory kiracısıyla ilişkilendirilmesi gerekir.

## <a name="disable-ddos-for-a-virtual-network"></a>Sanal ağ için DDoS 'ı devre dışı bırakma

1. Portalın üst kısmındaki **kaynakları, hizmetleri ve belgeleri arayın kutusuna** Için DDoS koruması standardını devre dışı bırakmak istediğiniz sanal ağın adını girin. Arama sonuçlarında sanal ağın adı göründüğünde seçin.
2. **Ayarlar**altında **DDoS koruması**' nı seçin.
3. **DDoS koruma planı** altında **temel** ' yı seçin ve ardından **Kaydet**' i seçin.

## <a name="work-with-ddos-protection-plans"></a>DDoS koruma planlarıyla çalışma

1. Portalın sol tarafındaki **tüm hizmetler** ' i seçin.
2. **Filtre** kutusuna *DDoS* girin. **DDoS koruma planları** sonuçlarda görüntülendiğinde, bunu seçin.
3. Listeden görüntülemek istediğiniz koruma planını seçin.
4. Planla ilişkili tüm sanal ağlar listelenir.
5. Bir planı silmek istiyorsanız, önce tüm sanal ağların ilişkisini kaldırmanız gerekir. Bir planın bir sanal ağla ilişkilendirmesini kaldırmak için bkz. [sanal ağ Için DDoS 'ı devre dışı bırakma](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>DDoS koruma ölçümleri için uyarıları yapılandırma

Azure Izleyici uyarı yapılandırması kullanılarak saldırı sırasında etkin bir risk azaltma olduğunda sizi uyarmak için kullanılabilir DDoS koruma ölçümlerinden herhangi birini seçebilirsiniz. Koşullar karşılandığında, belirtilen adres bir uyarı e-postası alır:

1. Portalın sol tarafındaki **tüm hizmetler** ' i seçin.
2. **Filtre** kutusuna *izleyiciyi* girin. Sonuçlarda **izleyici** göründüğünde seçin.
3. **PAYLAŞıLAN hizmetler**altında **ölçümler** ' i seçin.
4. Kendi değerlerinizi girin veya seçin veya aşağıdaki örnek değerleri girip, kalan Varsayılanları kabul edin ve ardından **Tamam**' ı seçin:

    |Ayar                  |Value                                                                                               |
    |---------                |---------                                                                                           |
    |Name                     | myDdosAlert                                                                                        |
    |Subscription             | Uyarılarını almak istediğiniz genel IP adresini içeren aboneliği seçin.        |
    |Resource group           | Uyarılarını almak istediğiniz genel IP adresini içeren kaynak grubunu seçin.      |
    |Resource                 | Uyarıları almak istediğiniz genel IP adresini içeren genel IP adresini seçin. DDoS, bir sanal ağ içindeki kaynaklara atanan genel IP adreslerini izler. Sanal ağda genel IP adresleri olan kaynaklarınız yoksa, önce genel IP adresine sahip bir kaynak oluşturmanız gerekir. Azure App Service ortamları ve Azure VPN Gateway dışında [Azure hizmetleri Için sanal ağda](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)listelenen Kaynak Yöneticisi (klasik değil) aracılığıyla dağıtılan tüm KAYNAKLARıN genel IP adresini izleyebilirsiniz. Bu öğreticiye devam etmek için hızlı bir şekilde [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makinesi oluşturabilirsiniz.                   |
    |Ölçüm                   | DDoS saldırısı altında                                                                            |
    |Eşik                | 1- **1** saldırı altında olduğunuz anlamına gelir. **0** saldırı altında olmayan anlamına gelir.                         |
    |Süre                   | Seçtiğiniz değeri seçin.                                                                   |
    |E-postayla bildir         | Onay kutusunu işaretleyin                                                                                  |
    |Ek yönetici | Abonelik için bir e-posta sahibi, katkıda bulunan veya okuyucu değilseniz e-posta adresinizi girin. |

    Birkaç dakika içinde saldırı algılama sırasında, Azure Izleyici ölçümlerinden aşağıdaki resme benzer bir e-posta alırsınız:

    ![Saldırı Uyarısı](./media/manage-ddos-protection/ddos-alert.png)


Uyarı doğrulamanızı sağlamak için DDoS saldırısının benzetimini yapmak için bkz. [DDoS algılamayı doğrulama](#validate-ddos-detection).

Ayrıca, uyarı oluşturmak için [Web kancaları](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [Logic Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) yapılandırma hakkında daha fazla bilgi edinebilirsiniz.

## <a name="use-ddos-protection-telemetry"></a>DDoS koruması telemetrisini kullanma

Bir saldırının telemetrisi, Azure Izleyici aracılığıyla gerçek zamanlı olarak sağlanır. Telemetri yalnızca genel bir IP adresinin risk altında olduğu süre boyunca kullanılabilir. Bir saldırının etkilerinin azaltılmadan önce veya sonra telemetri görmezsiniz.

1. Portalın sol tarafındaki **tüm hizmetler** ' i seçin.
2. **Filtre** kutusuna *izleyiciyi* girin. Sonuçlarda **izleyici** göründüğünde seçin.
3. **PAYLAŞıLAN hizmetler**altında **ölçümler**' i seçin.
4. Telemetriyi istediğiniz genel IP adresini içeren **aboneliği** ve **kaynak grubunu** seçin.
5. **Kaynak türü**IÇIN **genel IP adresi** ' ni seçin ve ardından Telemetriyi istediğiniz belirli genel IP adresini seçin.
6. Ekranın sol tarafında bir dizi **kullanılabilir ölçüm** görüntülenir. Bu ölçümler seçildiğinde, genel bakış ekranında **Azure Izleyici ölçümleri grafiğinde** grafiği kaldırılır.
7. **Toplama** türünü **en fazla** olarak seçin

Ölçüm adları farklı paket türleri ve bayt ve paketlerin yanı sıra, her ölçüm üzerinde aşağıdaki gibi temel bir etiket adı vardır:

- **Bırakılan etiket adı** (örneğin, **gelen paketler, DDoS bırakıldı**): DDoS koruma sistemi tarafından bırakılan/itilen paketlerin sayısı.
- **İletilen etiket adı** (örneğin, **DDoS Ileten gelen paketler**): DDoS sistemi tarafından, filtrelenmemiş hedef VIP 'ye iletilen paketlerin sayısı.
- **Etiket adı yok** (örneğin, **gelen paketler DDoS**): Bırakılan ve iletilen paketlerin toplamını temsil eden, temizleme sistemine gelen toplam paket sayısı.

Telemetrinin doğrulanması için DDoS saldırısının benzetimini yapmak için bkz. [DDoS algılamayı doğrulama](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>DDoS risk azaltma ilkelerini görüntüleme

DDoS koruması standardı, DDoS 'ın etkinleştirildiği sanal ağdaki korumalı kaynağın her genel IP adresi için otomatik olarak ayarlanmış üç hafifletme ilkesi (TCP SYN, TCP & UDP) uygular. Aşağıdaki resimde gösterildiği gibi, **toplama** türü olan DDoS azaltma ölçümlerini ' Max ' olarak tetiklemek Için, DDoS azaltma ve **gelen UDP paketlerini** **tetiklemek üzere gelen TCP paketlerini** seçerek ilke eşiklerini görüntüleyebilirsiniz:

![Risk azaltma ilkelerini görüntüleme](./media/manage-ddos-protection/view-mitigation-policies.png)

İlke eşikleri, Azure Machine Learning tabanlı ağ trafiği profili oluşturma yöntemiyle otomatik olarak yapılandırılır. Yalnızca ilke eşiğine ulaşıldığında, saldırı altındaki IP adresi için DDoS risk azaltma meydana gelir.

## <a name="configure-ddos-attack-analytics"></a>DDoS saldırı analizlerini yapılandırma
Azure DDoS koruması standardı, DDoS saldırı analiziyle ayrıntılı saldırı öngörüleri ve görselleştirmeleri sağlar. Sanal ağlarını DDoS saldırılarına karşı koruyan müşteriler, saldırı saldırılarına karşı saldırı ve risk azaltma & raporları aracılığıyla saldırının etkilerini azaltmak için gerçekleştirilen saldırı ve eylemler hakkında ayrıntılı görünürlük sağlar. 

## <a name="configure-ddos-attack-mitigation-reports"></a>DDoS saldırı risk azaltma raporlarını yapılandırma
Saldırı risk azaltma raporları, kaynak üzerinde saldırı hakkında ayrıntılı bilgi sağlamak için toplanan Netflow protokol verilerini kullanır. Genel bir IP kaynağı saldırı altında olduğunda, risk azaltma başladıktan sonra rapor oluşturma başlatılır. Her 5 dakikada bir ve risk azaltma süresi boyunca bir azaltma sonrası raporuyla oluşturulan bir artımlı rapor olacaktır. Bu, DDoS saldırısının daha uzun bir süre boyunca devam ettiğinden emin olmak için, risk azaltma süresi bittikten sonra, her 5 dakikada bir azaltma raporunun en güncel anlık görüntüsünü ve tüm Özeti görüntüleyebileceksiniz. 

1. Portalın sol tarafındaki **tüm hizmetler** ' i seçin.
2. **Filtre** kutusuna *izleyiciyi* girin. Sonuçlarda **izleyici** göründüğünde seçin.
3. **Ayarlar**altında **Tanılama ayarları**' nı seçin.
4. Günlüğe kaydetmek istediğiniz genel IP adresini içeren **aboneliği** ve **kaynak grubunu** seçin.
5. **Kaynak türü**IÇIN **genel IP adresi** ' ni seçin ve ardından ölçümlerini günlüğe kaydetmek istediğiniz belirli genel IP adresini seçin.
6. **DDoSMitigationReports günlüğünü toplamak için tanılamayı aç** ' ı seçin ve ardından ihtiyacınız olan aşağıdaki seçeneklerden birçoğunu seçin:

    - **Bir depolama hesabına Arşivle**: Veriler bir Azure depolama hesabına yazılır. Bu seçenek hakkında daha fazla bilgi edinmek için bkz. [Arşiv tanılama günlükleri](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Bir olay hub 'ına akış**: Bir Azure Olay Hub 'ı kullanarak günlük alıcısının günlükleri seçmesine izin verir. Olay Hub 'ları, splunk veya diğer SıEM sistemleriyle tümleştirmeyi etkinleştirir. Bu seçenek hakkında daha fazla bilgi edinmek için bkz. [bir olay hub 'ına tanılama günlüklerini akışa](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)alma.
    - **Log Analytics gönder**: Günlükleri Azure Izleyici hizmetine yazar. Bu seçenek hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici günlüklerinde kullanılacak günlükleri toplama](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Hem artımlı & saldırı sonrası risk azaltma raporları aşağıdaki alanları içerir
- Saldırı vektörleri
- Trafik istatistikleri
- Bırakılan paketlerin nedeni
- Dahil edilen protokoller
- İlk 10 kaynak ülke veya bölge
- İlk 10 kaynak ASNs

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>DDoS saldırısı risk azaltma akış günlüklerini yapılandırma
Saldırı risk azaltma akış günlükleri, etkin bir DDoS saldırısı sırasında bırakılan trafiği, iletilen trafiği ve diğer ilgi çekici veri noktalarını neredeyse gerçek zamanlı olarak incelemenizi sağlar. Bu verilerin sabit akışını, neredeyse gerçek zamanlı izleme için Olay Hub 'ı aracılığıyla SıEM sistemlerinize alabilir, olası eylemleri gerçekleştirebilir ve savunma operasyonlarınızın gereksinimini ortadan kaldırabilirsiniz. 

1. Portalın sol tarafındaki **tüm hizmetler** ' i seçin.
2. **Filtre** kutusuna *izleyiciyi* girin. Sonuçlarda **izleyici** göründüğünde seçin.
3. **Ayarlar**altında **Tanılama ayarları**' nı seçin.
4. Günlüğe kaydetmek istediğiniz genel IP adresini içeren **aboneliği** ve **kaynak grubunu** seçin.
5. **Kaynak türü**IÇIN **genel IP adresi** ' ni seçin ve ardından ölçümlerini günlüğe kaydetmek istediğiniz belirli genel IP adresini seçin.
6. **DDoSMitigationFlowLogs günlüğünü toplamak için tanılamayı aç** ' ı seçin ve ardından ihtiyacınız olan aşağıdaki seçeneklerden birçoğunu seçin:

    - **Bir depolama hesabına Arşivle**: Veriler bir Azure depolama hesabına yazılır. Bu seçenek hakkında daha fazla bilgi edinmek için bkz. [Arşiv tanılama günlükleri](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Bir olay hub 'ına akış**: Bir Azure Olay Hub 'ı kullanarak günlük alıcısının günlükleri seçmesine izin verir. Olay Hub 'ları, splunk veya diğer SıEM sistemleriyle tümleştirmeyi etkinleştirir. Bu seçenek hakkında daha fazla bilgi edinmek için bkz. [bir olay hub 'ına tanılama günlüklerini akışa](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)alma.
    - **Log Analytics gönder**: Günlükleri Azure Izleyici hizmetine yazar. Bu seçenek hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici günlüklerinde kullanılacak günlükleri toplama](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Azure Analytics panosu 'nda akış günlüğü verilerini görüntülemek için, örnek panoyu şuradan içeri aktarabilirsiniz https://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

Akış günlükleri aşağıdaki alanlara sahip olur: 
- Kaynak IP
- Hedef IP
- Kaynak Bağlantı Noktası 
- Hedef bağlantı noktası 
- Protokol türü 
- Risk azaltma sırasında gerçekleştirilecek eylem



## <a name="validate-ddos-detection"></a>DDoS algılamayı doğrulama

Microsoft, simülasyon için DDoS koruması etkinleştirilmiş genel IP adreslerine karşı trafik oluşturabileceğiniz bir arabirim oluşturmak üzere [BreakingPoint bulutu](https://www.ixiacom.com/products/breakingpoint-cloud) ile işbirliği yaptı. Kesme noktası bulutu benzetimi şunları yapmanıza olanak sağlar:

- DDoS korumasının Microsoft Azure Azure kaynaklarınızı DDoS saldırılarına karşı koruduğunu doğrulayın
- DDoS saldırısı kapsamındaki olay yanıt sürecinizi iyileştirin
- DDoS uyumluluğunu belgele
- Ağ Güvenlik ekiplerinizi eğitme

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde DDoS koruma uyarılarını görüntüleme

Azure Güvenlik Merkezi, sorunları araştırmaya ve düzeltmeye yardımcı olacak bilgiler içeren [güvenlik uyarılarının](/azure/security-center/security-center-managing-and-responding-alerts)bir listesini sağlar. Bu özellikle, DDoS saldırılarıyla ilgili uyarılar ve saldırıyı neredeyse güncel olarak azaltmak için gerçekleştirilecek eylemler dahil olmak üzere uyarıların birleştirilmiş bir görünümünü alırsınız.
Tüm DDoS saldırıları algılama ve risk azaltma için göreceğiniz iki özel uyarı vardır:

- **Genel IP Için DDoS saldırısı algılandı**: Bu uyarı, DDoS Koruması hizmeti, genel IP adreslerinizin bir DDoS saldırısı hedefi olduğunu algıladığında üretilir.
- **Genel IP Için DDoS saldırısı azaltıldığında**: Bu uyarı, genel IP adresindeki bir saldırı azaltıldığında oluşturulur.
Uyarıları görüntülemek için Azure portal **Güvenlik Merkezi** 'ni açın. **Tehdit koruması**altında **güvenlik uyarıları**' nı seçin. Aşağıdaki ekran görüntüsünde, DDoS saldırı uyarılarının bir örneği gösterilmektedir.

![Azure Güvenlik Merkezi 'nde DDoS uyarısı](./media/manage-ddos-protection/ddos-alert-asc.png)

Uyarılar, saldırı altında genel IP adresi, coğrafi ve tehdit bilgileri ve düzeltmeler adımları hakkında genel bilgiler içerir.

## <a name="permissions"></a>İzinler

DDoS koruma planlarıyla çalışmak için, hesabınız [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya aşağıdaki tabloda listelenen uygun eylemlere atanmış [özel](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) bir role atanmalıdır:

| Action                                            | Name                                     |
| ---------                                         | -------------                            |
| Microsoft. Network/Ddosprotectionplanlar/okuma        | DDoS koruma planını okuyun              |
| Microsoft. Network/Ddosprotectionplanlar/yazma       | DDoS koruma planı oluşturma veya güncelleştirme  |
| Microsoft. Network/Ddosprotectionplanlar/silme      | DDoS koruma planını silme            |
| Microsoft. Network/Ddosprotectionplanlar/JOIN/Action | DDoS koruma planına ekleme              |

Bir sanal ağ için DDoS korumasını etkinleştirmek üzere hesabınıza [sanal ağlar için uygun eylemler](manage-virtual-network.md#permissions)de atanmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

- Sanal ağlar için [Azure ilkesi](policy-samples.md) oluşturma ve uygulama