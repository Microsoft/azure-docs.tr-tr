---
title: Azure ExpressRoute Direct hakkında
description: Azure ExpressRoute Direct 'in temel özellikleri ve kullanılabilir SKU 'Lar gibi ExpressRoute Direct 'e eklemek için gereken bilgiler ve teknik gereksinimler hakkında bilgi edinin.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: duau
ms.openlocfilehash: 56d6a76991c4386be45b2c18f4edb3d363e58fa5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027151"
---
# <a name="about-expressroute-direct"></a>ExpressRoute Direct hakkında

ExpressRoute Direct, dünyanın dört bir yanındaki doğrudan dağıtım konumlarında doğrudan Microsoft 'un küresel ağına doğrudan bağlanabilmenizi sağlar. ExpressRoute Direct, ölçek üzerinde etkin/etkin bağlantıyı destekleyen çift 100 Gbps veya 10 Gbps bağlantı sağlar. ER doğrudan için herhangi bir hizmet sağlayıcısıyla çalışabilirsiniz.

ExpressRoute Direct 'in sağladığı temel özellikler dahil, ancak bunlarla sınırlı değildir:

* Depolama ve Cosmos DB gibi hizmetler için Büyük Veri Alımı özelliği
* Düzenlenmiş ve şunun gibi adanmış ve yalıtılmış bağlantı gerektiren sektörler için fiziksel yalıtım: bankacılık, kamu ve perakende
* Bağlantı hattı dağıtımının iş birimine dayalı detaylı denetimi

## <a name="onboard-to-expressroute-direct"></a>ExpressRoute Direct 'e ekleme

ExpressRoute Direct kullanmadan önce aboneliğinizi kaydetmeniz gerekir. Kaydolmak için Azure PowerShell kullanarak aşağıdaki komutları çalıştırın:

1.  Azure 'da oturum açın ve kaydetmek istediğiniz aboneliği seçin.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. Aşağıdaki komutu kullanarak aboneliğinizi genel önizleme için kaydedin:
1. 
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

Kaydolduktan sonra, **Microsoft. Network** kaynak sağlayıcısının aboneliğinize kayıtlı olduğunu doğrulayın. Kaynak sağlayıcısı kaydı, aboneliğinizi kaynak sağlayıcısıyla çalışacak şekilde yapılandırır.

1. [Azure kaynak sağlayıcıları ve türleri](../azure-resource-manager/management/resource-providers-and-types.md)bölümünde açıklandığı gibi abonelik ayarlarınıza erişin.

1. Aboneliğinizde, **kaynak sağlayıcıları** Için, **Microsoft. Network** sağlayıcısı 'nın **kayıtlı** bir durumu olduğunu doğrulayın. Microsoft. Network kaynak sağlayıcısı kayıtlı sağlayıcılar listesinde yoksa, ekleyin.

ExpressRoute Direct kullanmaya başlarsanız ve seçtiğiniz eşleme konumunda kullanılabilir bağlantı noktası olmadığından, ExpressRouteDirect@microsoft.com daha fazla sayım istemek için e-posta gönderin.

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>Bir hizmet sağlayıcısı ve ExpressRoute Direct kullanarak ExpressRoute

| **Bir hizmet sağlayıcısı kullanarak ExpressRoute** | **ExpressRoute Direct** | 
| --- | --- |
| Mevcut altyapıya hızlı ekleme ve bağlantı sağlamak için hizmet sağlayıcılarını kullanır | 100 Gbps/10 Gbps altyapısı ve tüm katmanların tam yönetimi gerekir
| Ethernet ve MPLS dahil yüzlerce sağlayıcı ile tümleşir | Düzenlenen sektörler ve çok büyük veri alımı için doğrudan/ayrılmış kapasite |
| 50 Mbps 'den 10 Gbps 'ye devre SKU 'Ları | Müşteri, 100 Gbps ExpressRoute Direct üzerinde aşağıdaki devre SKU 'Larının birleşimini seçebilir: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> Müşteri, 10 Gbps ExpressRoute Direct üzerinde aşağıdaki devre SKU 'Larının birleşimini seçebilir:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Tek kiracı için iyileştirildi | Birden çok iş birimi ve birden çok iş ortamı ile tek bir kiracı için iyileştirildi

## <a name="expressroute-direct-circuits"></a>ExpressRoute doğrudan devreleri

Microsoft Azure ExpressRoute, şirket içi ağınızı bir bağlantı sağlayıcısı tarafından daha kolay hale getirilen özel bir bağlantı üzerinden Microsoft bulutuna genişletmenizi sağlar. ExpressRoute ile Microsoft Azure ve Microsoft 365 gibi Microsoft bulut hizmetleriyle bağlantı kurabilirsiniz.

Her eşleme konumu Microsoft 'un küresel ağına erişimi vardır ve varsayılan olarak, coğrafi olmayan bir bölgedeki herhangi bir bölgeye erişebilir. Tüm genel bölgelere Premium devresi ile erişebilirsiniz.  

Çoğu senaryonun işlevselliği, çalıştırmak için bir ExpressRoute hizmet sağlayıcısı kullanan devrelere eşdeğerdir. ExpressRoute Direct kullanılarak sunulan daha fazla ayrıntı düzeyi ve yeni özellikleri desteklemek için, ExpressRoute doğrudan devrelerde mevcut olan bazı önemli yetenekler vardır.

## <a name="circuit-skus"></a>Devre SKU 'Ları

ExpressRoute Direct, Azure depolama ve diğer büyük veri Hizmetleri için geniş kapsamlı veri alma senaryolarını destekler. 100-Gbps ExpressRoute Direct üzerindeki ExpressRoute devreleri artık **40 Gbps** ve * * 100-Gbps devre SKU 'larını desteklemektedir. Fiziksel bağlantı noktası çiftleri yalnızca **100 GB/sn veya 10 Gbps** olabilir ve birden çok sanal devreye sahip olabilir. Devre boyutları:

| **100-Gbps ExpressRoute Direct** | **10 Gbps ExpressRoute Direct** | 
| --- | --- |
| **Abone olunan bant genişliği**: 200 Gbps | **Abone olunan bant genişliği**: 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Teknik gereksinimler

* Microsoft Kurumsal kenar yönlendirici (MSEE) arabirimleri:
    * İkili 10 Gigabit veya 100-yalnızca yönlendirici çifti arasında Gigabit Ethernet bağlantı noktaları
    * Tek modlu LR fiber bağlantı
    * IPv4 ve IPv6
    * IP MTU 1500 bayt

* Anahtar/yönlendirici katman 2/katman üç bağlantı:
    * 1 802.1 Q (Dot1Q) etiketi veya iki etiket 802.1 Q (QinQ) etiketi kapsüllemeyi desteklemelidir
    * EtherType = 0x8100
    * Microsoft tarafından belirtilen VLAN KIMLIğINE bağlı olarak dış VLAN etiketi (STAG) eklenmelidir ve *yalnızca QinQ üzerinde geçerlidir*
    * Bağlantı noktası ve cihaz başına birden çok BGP oturumu (VLAN) desteklemelidir
    * IPv4 ve IPv6 bağlantısı. *IPv6 için ek bir alt arabirim oluşturulmaz. IPv6 adresi var olan bir alt arabirim 'e eklenecektir*. 
    * İsteğe bağlı: ExpressRoute devrelerinde tüm özel eşlemelerde varsayılan olarak yapılandırılan [çift yönlü Iletme algılaması (BFD)](./expressroute-bfd.md) desteği

## <a name="vlan-tagging"></a>VLAN etiketleme

ExpressRoute Direct, Qınq ve Dot1Q VLAN etiketlemesini destekler.

* **Qınq VLAN etiketleme** , ExpressRoute bağlantı hattı başına yalıtılmış yönlendirme etki alanlarına izin verir. Azure, devre oluşturma sırasında S-Tag ' i dinamik olarak sağlar ve değiştirilemez. Devre üzerindeki her bir eşleme (özel ve Microsoft) VLAN olarak benzersiz bir C-Tag kullanacaktır. C etiketinin, ExpressRoute doğrudan bağlantı noktalarında devreler arasında benzersiz olması gerekmez.

* **DOT1Q VLAN etiketleme** , ExpressRoute doğrudan bağlantı noktası çiftliğine göre tek etiketli VLAN için izin verir. Eşleme üzerinde kullanılan bir C etiketi, ExpressRoute doğrudan bağlantı noktası çiftinin tüm devrelerde ve eşleştirmelerinde benzersiz olmalıdır.

## <a name="workflow"></a>İş akışı

[![akışıyla](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct, Microsoft Global Network 'e etkin/etkin yedekli bağlantılarla aynı kurumsal düzeyde SLA sağlar. ExpressRoute altyapısı gereksizdir ve Microsoft küresel ağı ile bağlantı, daha fazla ve farklı ve müşteri gereksinimleriyle doğru şekilde ölçeklendirilir. 

## <a name="next-steps"></a>Sonraki adımlar

[ExpressRoute Direct'i yapılandırma](expressroute-howto-erdirect.md)
