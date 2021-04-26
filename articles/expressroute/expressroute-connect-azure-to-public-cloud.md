---
title: Azure 'ı genel bulutlara bağlama | Microsoft Docs
description: Azure 'un diğer genel bulutlara bağlanmasına yönelik çeşitli yollar açıklanır
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: duau
ms.openlocfilehash: fb4c34345ab902e243f22eae06fd5ea174fd2992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202200"
---
# <a name="connecting-azure-with-public-clouds"></a>Azure 'ı genel bulutlarla bağlama

Birçok kuruluş, iş ve teknik hedefler nedeniyle çok bulut stratejisini karşılayabilir. Bunlar arasında maliyet, esneklik, özellik kullanılabilirliği, artıklığı, veri argemenlik vb. dahildir. Bu strateji, her iki bulutun en iyi şekilde yararlanmasını sağlar. 

Bu yaklaşım ayrıca, ağ ve uygulama mimarisi açısından kuruluş için zorluk da ortadan çıkarabilir. Bu güçlüklerden bazıları gecikme süresi ve veri işleme. Bu güçlükleri karşılamak için müşterilerin birden çok bulutla doğrudan bağlanmasını istiyorsunuz. Bazı hizmet sağlayıcıları, müşteriler için birden çok bulut sağlayıcısını bağlamaya yönelik bir çözüm sağlar. Diğer durumlarda Müşteri, birden çok genel bulutu bağlamak için kendi yönlendiricilerini dağıtabilir.
## <a name="connectivity-via-expressroute"></a>ExpressRoute üzerinden bağlantı
ExpressRoute, müşterilerin Şirket içi ağlarını bir bağlantı sağlayıcısı tarafından kolaylaştırarak özel bir bağlantı üzerinden Microsoft bulutuna genişletmesine imkan tanır. ExpressRoute ile müşteriler, Microsoft bulut hizmetleriyle bağlantı kurabilir.

ExpressRoute aracılığıyla bağlanmanın üç yolu vardır.

1. Layer3 sağlayıcısı
2. Layer2 sağlayıcısı
3. Doğrudan bağlantı

### <a name="layer3-provider"></a>Layer3 sağlayıcısı

Layer3 sağlayıcıları genellikle IP VPN veya MPLS VPN sağlayıcıları olarak bilinir. Müşteriler, veri merkezleri, dallar ve bulut arasında MultiPoint bağlantısı için bu sağlayıcılardan faydalanır. Müşteriler, L3 sağlayıcısına BGP aracılığıyla veya statik varsayılan yol aracılığıyla bağlanır. Hizmet sağlayıcısı, müşteri siteleri, veri merkezleri ve genel bulut arasındaki rotaları tanıtır. 
 
Microsoft, Layer3 sağlayıcısı üzerinden bağlanılırken, müşteri VNET yollarını BGP üzerinden hizmet sağlayıcısına duyuracaktır. Sağlayıcı iki farklı uygulama içerebilir.

![Bir Layer3 sağlayıcısı gösteren diyagram.](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

Tüm bulut sağlayıcılarından gelen trafik müşteri yönlendiricisinde iletişime geçecektir, sağlayıcı her bir bulut sağlayıcısında ayrı bir VRF 'de iniş olabilir. Müşteri BGP 'yi hizmet sağlayıcısıyla çalıştırıyorsa, bu yollar varsayılan olarak diğer bulut sağlayıcılarına yeniden tanıtılabilir. 

Hizmet sağlayıcısı, aynı VRF içindeki tüm bulut sağlayıcılarına giriş alıyorsa, rotalar hizmet sağlayıcısından doğrudan diğer bulut sağlayıcılarına tanıtılabilir. Bu, eBGP yollarının varsayılan olarak diğer eBGP komşuları 'na tanıtıldığı standart BGP işleminin kabul edildiği varsayılır.

Her genel bulutun farklı ön ek sınırı vardır, bu nedenle yollar dağıtılırken rotalar hizmeti sağlayıcısı, yolları dağıtırken dikkatli olmalıdır.

### <a name="layer2-provider-and-direct-connection"></a>Layer2 sağlayıcısı ve doğrudan bağlantı

Her iki modelde da fiziksel bağlantı farklı olsa da, ancak Layer3 BGP 'de doğrudan MSEE ve müşteri yönlendiricisi arasında oluşturulmuştur. ExpressRoute Direct müşterisi için doğrudan MSEE 'a bağlanır. Layer2 durumda servis sağlayıcı, VLAN 'ı müşteri Şirket içinden buluta genişletir. Müşteriler, DC 'leri buluta bağlamak için Layer2 ağı üzerinde BGP çalıştırır.
![Bir Layer2 sağlayıcısı ve doğrudan bağlantı gösteren diyagram.](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
Her iki durumda da müşterinin Genel bulutların her birine noktadan noktaya bağlantıları olacaktır. Müşteri, her genel buluta ayrı BGP bağlantısı kurar. Bir bulut sağlayıcısı tarafından alınan yollar, varsayılan olarak diğer bulut sağlayıcısına tanıtılabilir. Her bulut sağlayıcısının farklı ön ek sınırı vardır. bu nedenle, rotalar müşterisi Bu limitlerin ilgilenmelidir. Müşteri, diğer Genel bulutların yollarını tanılarken Microsoft ile her zamanki BGP düğmelerini kullanabilir.

## <a name="direct-connection-with-expressroute"></a>ExpressRoute ile doğrudan bağlantı

Müşteriler ExpressRoute 'u doğrudan bulut sağlayıcısının doğrudan bağlantı teklifine bağlamayı tercih edebilir. İki bulut sağlayıcısı geri bağlanacak ve BGP doğrudan yönlendiricileri arasında kurulacaktır. Bu bağlantı türü, bugün Oracle ile kullanılabilir.

## <a name="site-to-site-vpn"></a>Siteler arası VPN

Müşteriler, Azure 'daki örneklerini diğer genel bulutlarla bağlamak için Internet 'Ten yararlanabilir. Neredeyse tüm bulut sağlayıcıları siteden siteye VPN özellikleri sunar. Ancak, belirli varyantlar olmadığı için uyumsuzluklar olabilir. Örneğin, bazı bulut sağlayıcıları yalnızca bu bulutta bir VPN sonlandırma uç noktası olması için IKEv1 'yi destekler. Ikev2 'yi destekleyen bulut sağlayıcıları için, her iki bulut sağlayıcılarındaki VPN ağ geçitleri arasında doğrudan bir tünel oluşturulabilir.

Siteden siteye VPN, yüksek aktarım hızı ve düşük gecikme süreli çözüm olarak kabul edilmez. Ancak, fiziksel bağlantı için bir yedekleme olarak kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar
ExpressRoute ve sanal ağ bağlantısı hakkında daha fazla soru için bkz. [EXPRESSROUTE SSS][ER-FAQ] .

Azure ile Oracle arasında bağlantı için bkz. [Azure Ile Oracle bulutu arasında doğrudan bağlantı kurma][ER-OCI]

<!--Link References-->
[ER-FAQ]: ./expressroute-faqs.md
[ER-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md