---
title: Azure VMware çözümünde genel IP işlevselliğini kullanma
description: Bu makalede, Azure sanal WAN 'da genel IP işlevlerinin nasıl kullanılacağı açıklanmaktadır.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 794e24e531d464adf58d5a06b5a411ada18c4a60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023665"
---
# <a name="how-to-use-the-public-ip-functionality-in-azure-vmware-solution"></a>Azure VMware çözümünde genel IP işlevselliğini kullanma

Genel IP, Azure VMware Çözüm bağlantısı 'ndaki yeni bir özelliktir. Web sunucuları, sanal makineler (VM 'Ler) ve genel bir ağ üzerinden erişilebilen konaklar gibi kaynakları oluşturur. 

Genel internet erişimini iki şekilde etkinleştirirsiniz. 

- Uygulamalar, HTTP/HTTPS trafiği için Application Gateway yük dengeleyici kapsamında barındırılabilir ve yayımlanabilir.
- Azure sanal WAN 'da genel IP özellikleri aracılığıyla yayımlandı.

Azure VMware Çözüm özel bulut dağıtımının bir parçası olarak, genel IP işlevselliğini etkinleştirdikten sonra, Otomasyon get ve Enabled ile gerekli bileşenler:

-  Sanal WAN

-  ExpressRoute bağlantısı olan sanal WAN hub 'ı

-  Genel IP ile Azure Güvenlik Duvarı hizmetleri

Bu makalede, sanal WAN 'da genel IP işlevselliğini nasıl kullanabileceğiniz hakkında bilgi bulabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Azure VMware Çözüm ortamı
- Azure VMware Çözüm ortamında çalışan bir Web sunucusu.
- Sanal WAN hub dağıtımı için genellikle bir olan, çakışmayan yeni bir IP aralığı `/24` .

## <a name="reference-architecture"></a>Başvuru mimarisi

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="Genel IP mimarisi diyagramı" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

Mimari diyagramı, Azure VMware Çözüm ortamında barındırılan ve RFC1918 özel IP adresleriyle yapılandırılmış bir Web sunucusunu gösterir.  Web hizmeti, sanal WAN genel IP işlevselliği aracılığıyla internet için kullanılabilir hale getirilir.  Genel IP, genellikle Azure Güvenlik Duvarı 'nda çevrilen bir hedef NAT 'dir. DNAT kuralları ile, güvenlik duvarı ilkesi genel IP adresi isteklerini bir bağlantı noktası ile özel bir adrese (Web sunucusu) çevirir.

Kullanıcı istekleri, Azure Güvenlik duvarında DNAT kuralları kullanılarak özel IP 'ye çevrilen genel bir IP 'deki güvenlik duvarından isabet ediyor. Güvenlik Duvarı NAT tablosunu denetler ve istek bir girdiyle eşleşiyorsa, trafiği Azure VMware Çözüm ortamındaki çevrilmiş adrese ve bağlantı noktasına iletir.

Web sunucusu isteği alır ve istenen bilgilerle veya sayfayla yanıt verir. güvenlik duvarı, bilgileri genel IP adresindeki kullanıcıya iletir.

## <a name="test-case"></a>Test çalışması
Bu senaryoda, IIS Web sunucusu ' nu Internet 'te yayımlayacaksınız. Web sitesini genel bir IP adresinde yayımlamak için Azure VMware çözümünde genel IP özelliğini kullanın.  Ayrıca güvenlik duvarında NAT kuralları yapılandırıp Azure VMware Çözüm kaynağına (bir Web sunucusu olan VM 'Ler) genel IP ile erişin.

>[!TIP]
>Çıkış trafiğini etkinleştirmek için, güvenlik yapılandırması > Internet trafiğini **Azure Güvenlik Duvarı**'na ayarlamanız gerekir.

## <a name="deploy-virtual-wan"></a>Sanal WAN dağıtma

1. Azure portal oturum açın ve **Azure VMware çözümünü** arayıp seçin.

1. Azure VMware Çözüm özel bulutu ' nı seçin.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Azure VMware Çözüm özel bulutu 'nın ekran görüntüsü." border="true" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. **Yönet** altında **bağlantı**' yı seçin.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="Bağlantı bölümünün ekran görüntüsü." border="true" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. **Genel IP** sekmesini seçin ve ardından **Yapılandır**' ı seçin.

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="Genel IP 'yi yapılandırmaya nereden başlayabileceğiniz gösteren ekran görüntüsü" border="true" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

1. Varsayılan değerleri kabul edin veya değiştirin ve ardından **Oluştur**' u seçin.

   - Sanal WAN kaynak grubu

   - Sanal WAN adı

   - Sanal hub adres bloğu (yeni çakışmayan IP aralığı kullanılarak)

   - Genel IP sayısı (1-100)

Tüm bileşenlerin dağıtımını tamamlaması yaklaşık bir saat sürer. Bu dağıtımın, bu Azure VMware Çözüm ortamı için gelecekteki tüm genel IP 'Leri desteklemesi için yalnızca bir kez gerçekleşmesi yeterlidir.  

>[!TIP]
>Durumu **bildirim** alanından izleyebilirsiniz. 

## <a name="view-and-add-public-ip-addresses"></a>Genel IP adreslerini görüntüle ve Ekle

Aşağıdaki adımları izleyerek daha fazla genel IP adresi denetleyebilir ve ekleyebiliriz.

1. Azure portal, **güvenlik duvarı**' nı arayıp seçin.

1. Dağıtılmış bir güvenlik duvarı seçin ve ardından **Bu güvenlik duvarını yapılandırmak ve yönetmek Için Azure Güvenlik Duvarı Yöneticisi 'Ni ziyaret** et 'i

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Güvenlik duvarını yapılandırma ve yönetme seçeneğini gösteren ekran görüntüsü" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. **Güvenli sanal hub 'ları** seçin ve listeden bir sanal hub seçin.

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="Güvenlik Duvarı Yöneticisi ekran görüntüsü" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. Sanal hub sayfasında **ortak IP yapılandırması**' nı seçin ve daha fazla genel IP adresi eklemek için **Ekle**' yi seçin. 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="Güvenlik Duvarı Yöneticisi 'nde genel IP yapılandırması ekleme ekranının ekran görüntüsü" border="true" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. Gereken IP sayısını sağlayın ve **Ekle**' yi seçin.

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="Belirtilen sayıda genel IP yapılandırması eklemek için ekran görüntüsü" border="true":::


## <a name="create-firewall-policies"></a>Güvenlik Duvarı ilkeleri oluşturma

Tüm bileşenler dağıtıldıktan sonra, bunları eklenen kaynak grubunda görebilirsiniz. Bir sonraki adım, bir güvenlik duvarı ilkesi eklemektir.

1. Azure portal, **güvenlik duvarı**' nı arayıp seçin.

1. Dağıtılmış bir güvenlik duvarı seçin ve ardından **Bu güvenlik duvarını yapılandırmak ve yönetmek Için Azure Güvenlik Duvarı Yöneticisi 'Ni ziyaret** et 'i

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Güvenlik duvarını yapılandırma ve yönetme seçeneğini gösteren ekran görüntüsü" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. **Azure Güvenlik Duvarı ilkelerini** seçin ve ardından **Azure Güvenlik Duvarı İlkesi Oluştur**' u seçin.

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="Güvenlik Duvarı Yöneticisi 'nde güvenlik duvarı ilkesi oluşturma ekranının ekran görüntüsü" border="true" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. **Temel bilgiler** sekmesinde, gerekli ayrıntıları sağlayın ve ileri ' yi SEÇIN **: DNS ayarları**. 

1. **DNS** sekmesi altında **devre dışı bırak**' ı seçin ve ardından **İleri: kurallar**' ı seçin.

1. **Kural koleksiyonu Ekle**' yi seçin, aşağıdaki ayrıntıları sağlayın ve **Ekle** ' yi seçin ve ardından İleri ' yi seçin **: tehdit bilgileri**.

   -  Name
   -  Kural koleksiyonu türü-DNAT
   -  Öncelik
   -  Kural toplama eylemi – Izin ver
   -  Kural adı
   -  Kaynak türü- **IPAddress**
   -  Kaynaktaki **\***
   -  Protokol – **TCP**
   -  Hedef bağlantı noktası – **80**
   -  Hedef türü – **IP adresi**
   -  Hedef – **genel IP adresi**
   -  Çevrilmiş adres – **Azure VMware Çözüm Web sunucusu özel IP adresi**
   -  Çevrilen bağlantı noktası- **Azure VMware Çözüm Web sunucusu bağlantı noktası**

1. Varsayılan değeri bırakın ve ardından **İleri: hub**' ı seçin.

1. **Sanal hub 'ı ilişkilendir**' i seçin.

1. Listeden bir hub seçin ve **Ekle**' yi seçin.

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="Güvenli sanal hub 'Lara dönüştürülecek seçili hub 'ları gösteren ekran görüntüsü." border="true" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. **Sonraki: Etiketler**' i seçin. 

1. Seçim Kaynaklarınızı kategorilere ayırmak için ad ve değer çiftleri oluşturun. 

1. **İleri** ' yi seçin ve Oluştur ' a ve ardından **Oluştur**' u seçin.

## <a name="limitations"></a>Sınırlamalar

Özel bulut başına 100 genel IP 'si olabilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure VMware çözümünde genel IP işlevselliğinin nasıl kullanılacağını kapsadığınıza göre, şunları öğrenmek isteyebilirsiniz:

- [Azure sanal WAN](../virtual-wan/virtual-wan-about.md)Ile genel IP adreslerini kullanma.
- [Azure VMware çözümüne bir IPSec tüneli oluşturma](create-ipsec-tunnel.md).
