---
title: Azure 'da VM 'Ler ve NSG 'lerle çalışma
description: Azure ile ağ güvenlik grupları 'nı kullanabilirsiniz. Bu yapılandırma için gereken alt ağlar hakkında bilgi edinin.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: b6a0dee4c3fef1be4f4b9f910b4c6256b4924a2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700227"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>NSG erişimiyle ve Azure ile çalışma

Azure savunma ile çalışırken ağ güvenlik gruplarını (NSG 'ler) kullanabilirsiniz. Daha fazla bilgi için bkz. [güvenlik grupları](../virtual-network/network-security-groups-overview.md).

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="NSG":::

Bu diyagramda:

* Savunma ana bilgisayarı sanal ağa dağıtılır.
* Kullanıcı herhangi bir HTML5 tarayıcısı kullanarak Azure portal bağlanır.
* Kullanıcı Azure sanal makinesine RDP/SSH 'ye gider.
* Tümleştirme-tarayıcı içinde tek tıklamayla RDP/SSH oturumu bağlayın
* Azure VM 'de genel IP gerekli değildir.

## <a name="network-security-groups"></a><a name="nsg"></a>Ağ güvenlik grupları

Bu bölümde, Kullanıcı ve Azure savunma arasındaki ağ trafiği ve sanal ağınızdaki VM 'Leri hedeflemek için:

> [!IMPORTANT]
> Azure savunma kaynağınız ile bir NSG kullanmayı seçerseniz, aşağıdaki giriş ve çıkış trafiği kurallarını da oluşturmanız **gerekir** . NSG 'inizdeki aşağıdaki kuralların herhangi birini dışarıda bırakmak, Azure savunma kaynağınızın gelecekte gerekli güncelleştirmeleri almasını engeller ve bu nedenle kaynağınızı gelecekteki güvenlik açıklarına karşı açın.
> 

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Azure savunma, özellikle ***AzureBastionSubnet***'e dağıtılır.

* **Giriş trafiği:**

   * **Genel İnternet 'ten gelen trafik:** Azure savunma, giriş trafiği için genel IP üzerinde bağlantı noktası 443 ' ün etkin olmasını gerektiren bir genel IP oluşturur. 3389/22 numaralı bağlantı noktası, AzureBastionSubnet açık olması gerekmez.
   * **Azure savunma denetim düzleminin giriş trafiği:** Denetim düzlemi bağlantısı için **Gatewaymanager** hizmet etiketinden gelen bağlantı noktası 443 ' yı etkinleştirin. Bu, denetim düzlemi, diğer bir deyişle, ağ geçidi yöneticisinin Azure ile iletişim kurabilmesine olanak sağlar.
   * **Azure savunma veri düzleminin giriş trafiği:** Temel Azure savunma bileşenleri arasındaki veri düzlemi iletişimi için, **VirtualNetwork** hizmeti etiketinden gelen 8080, 5701 bağlantı noktalarını **VirtualNetwork** hizmet etiketine etkinleştirin. Bu, Azure savunma bileşenlerinin birbirleriyle iletişim kurmasını sağlar.
   * **Azure Load Balancer giriş trafiği:** Sistem durumu araştırmaları için **AzureLoadBalancer** hizmet etiketinden gelen bağlantı noktası 443 ' yı etkinleştirin. Bu, Azure Load Balancer bağlantıyı algılamasını sağlar


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="Ekran görüntüsü, Azure savunma bağlantısı için gelen güvenlik kurallarını gösterir.":::

* **Çıkış trafiği:**

   * **Hedef VM 'Lere giden çıkış trafiği:** Azure savunma, hedef VM 'Lere özel IP üzerinden ulaşacaktır. NSG 'ler, bağlantı noktası 3389 ve 22 için diğer hedef VM alt ağlarına giden trafiğe izin vermek için gerekir.
   * **Azure savunma veri düzlemine çıkış trafiği:** Temel Azure savunma bileşenleri arasındaki veri düzlemi iletişimi için, **VirtualNetwork** hizmeti etiketinden giden 8080, 5701 giden bağlantı noktalarını **VirtualNetwork** hizmet etiketine etkinleştirin. Bu, Azure savunma bileşenlerinin birbirleriyle iletişim kurmasını sağlar.
   * **Azure 'da diğer genel uç noktalara giden trafik çıkışı:** Azure savunma 'nın Azure 'daki çeşitli genel uç noktalara bağlanabililmesi gerekir (örneğin, tanılama günlüklerini ve ölçüm günlüklerini depolamak için). Bu nedenle, Azure savunma 'nın 443 'e giden ve **Azurecı** hizmeti etiketi için çıkış yapması gerekir.
   * **Internet 'e giden trafik:** Azure savunma 'nın oturum ve sertifika doğrulaması için Internet ile iletişim kurabilmesi gerekir. Bu nedenle, Internet 'e giden bağlantı noktası 80 ' i etkinleştirmenizi öneririz **.**


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="Ekran görüntüsü, Azure savunma bağlantısı için giden güvenlik kurallarını gösterir.":::

### <a name="target-vm-subnet"></a>Hedef VM alt ağı
Bu, RDP/SSH yapmak istediğiniz hedef sanal makineyi içeren alt ağıdır.

   * **Azure 'dan giriş trafiği:** Azure savunma, hedef VM 'ye özel IP üzerinden ulaşacaktır. RDP/SSH bağlantı noktaları (sırasıyla 3389/22), özel IP üzerinden hedef VM tarafında açılmalıdır. En iyi uygulama olarak, bu bağlantı noktalarını hedef VM alt ağınızdaki hedef VM 'lerde açabiliyor olması için Azure savunma alt ağı IP adresi aralığını bu kurala ekleyebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

Azure savunma hakkında daha fazla bilgi için bkz. [SSS](bastion-faq.md).
