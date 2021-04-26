---
title: Azure yol sunucusu sorunlarını giderme
description: Azure Route Server sorunlarını giderme hakkında bilgi edinin.
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 03/15/2021
ms.author: duau
ms.openlocfilehash: 83f1e83653c5674988cadcb5b54d3c675ae0b8b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489449"
---
# <a name="troubleshooting-azure-route-server-issues"></a>Azure yol sunucusu sorunlarını giderme

> [!IMPORTANT]
> Azure yol sunucusu (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connectivity-issues"></a>Bağlantı sorunları

### <a name="why-does-my-nva-lose-internet-connectivity-after-it-advertises-the-default-route-00000-to-azure-route-server"></a>Neden NVA, varsayılan yolu (0.0.0.0/0) Azure yol sunucusuna duyurduktan sonra Internet bağlantısını kaybeder mi?
NVA 'niz varsayılan yolu duyurduğu zaman, Azure Route sunucusu, sanal ağ içindeki tüm VM 'Ler için NVA dahil olmak üzere bunu programkılar. Bu varsayılan yol, NVA 'yı Internet 'e yönelik tüm trafik için sonraki atlama olarak ayarlar. NVA 'niz Internet bağlantısına ihtiyaç duyuyorsa, bu varsayılan yolu NVA 'dan geçersiz kılmak için [Kullanıcı tanımlı bir yol](../virtual-network/virtual-networks-udr-overview.md) yapılandırmanız ve UDR 'YI NVA 'nın barındırıldığı alt ağa eklemeniz gerekir (aşağıdaki örneğe bakın). Aksi takdirde, NVA ana makinesi, NVA tarafından gönderilen biri dahil olmak üzere Internet 'e ait trafiği NVA 'ya göndermeye devam eder.

| Yol | Sonraki atlama |
|-------|----------|
| 0.0.0.0/0 | İnternet |


### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-why-does-the-bgp-peering-go-down"></a>Azure yol sunucusunda NVA 'dan BGP eşi IP 'ye neden ping yapabilirim, ancak aralarında BGP eşlemesini ayarladıktan sonra aynı IP 'yi artık ping yapamıyorum? BGP eşlemesi neden geçer?

Bazı NVA 'da, Azure Route sunucusu alt ağı için bir statik yol eklemeniz gerekir. Örneğin, Azure Route sunucusu 10.0.255.0/27 ' de ise ve NVA 10.0.1.0/24 ' de ise, NVA 'daki yönlendirme tablosuna aşağıdaki yolu eklemeniz gerekir:

| Yol | Sonraki atlama |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

10.0.1.1, alt ağdaki, NVA (veya daha fazla, NIC 'lerden biri) barındırıldığı varsayılan ağ geçidi IP 'si.

### <a name="why-do-i-lose-connectivity-to-my-on-premises-network-over-expressroute-andor-azure-vpn-when-im-deploying-azure-route-server-to-a-virtual-network-that-already-has-expressroute-gateway-andor-azure-vpn-gateway"></a>Azure yol sunucusunu ExpressRoute ağ geçidi ve/veya Azure VPN ağ geçidi zaten bulunan bir sanal ağa dağıtmazken ExpressRoute ve/veya Azure VPN üzerinden şirket içi ağmın bağlantısını neden yitim?
Azure Route Server 'ı bir sanal ağa dağıttığınızda, ağ geçitleri ve sanal ağ arasındaki denetim düzlemini güncelleştirmemiz gerekir. Bu güncelleştirme sırasında, sanal ağdaki VM 'Lerin şirket içi ağla bağlantısını kaybedecek bir süre vardır. Azure Route Server 'ı üretim ortamınıza dağıtmak için bir bakım zamanlamanızı kesinlikle öneririz.  

## <a name="control-plane-issues"></a>Denetim düzlemi sorunları

### <a name="why-is-the-bgp-peering-between-my-nva-and-the-azure-route-server-going-up-and-down-flapping"></a>NVA ve Azure Route sunucusu arasında BGP eşlemesi neden ("flaştır").

Flama nedeni BGP Zamanlayıcı ayarından dolayı olabilir. Varsayılan olarak, Azure yol sunucusundaki canlı tut süreölçeri 60 saniyeye ayarlanır ve bekletme süreölçeri 180 saniyedir.

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>BGP eşlemesi çalışıyor olsa da, NVA neden Azure yol sunucusundan yollar almıyor?

Azure Route sunucusu 'nun kullandığı ASN 65515 ' dir. NVA ve Azure Route sunucunuz arasında "eBGP" oturumunun kurulabilmesi için, yol yayılımını otomatik olarak gerçekleşebilmesi için NVA için farklı bir ASN 'yi ayarladığınızdan emin olun. NVA ve Azure Route sunucunuz sanal ağdaki farklı alt ağlarda yer aldığı için BGP yapılandırmanızda "çoklu atlama" özelliğini etkinleştirdiğinizden emin olun.

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>NVA ve Azure Route sunucusu arasındaki BGP eşlemesi çalışır. Aralarında doğru değiş tokuş edilen yolları görebiliyorum. Sanal makinem etkin yönlendirme tablosunda NVA yolları neden değil? 

* SANAL ağınız NVA ve Azure Route sunucunuz ile aynı VNet 'de ise:

     Azure rota sunucusu, yolları sanal ağınızda çalışan diğer tüm VM 'lere gönderme sorumluluğunu paylaşan iki VM 'de barındırılan iki BGP eş IP 'si sunar. NVA 'larınızın her biri, sanal ağdaki VM 'nizin Azure yol sunucusundan tutarlı yönlendirme bilgileri almasını sağlamak için iki özdeş BGP oturumu (örneğin, aynı numarayı, yol ile aynı ve aynı rota kümesini duyurur) iki VM 'ye ayarlamış olmalıdır. Aşağıdaki diyagrama bakın.

    ![Yönlendirme sunucusuyla bir ağ sanal gereci gösteren diyagram.](./media/faq/network-virtual-appliances.png)

    NVA 'nın iki veya daha fazla örneği varsa, tek bir NVA örneğini etkin ve diğer pasif olarak belirlemek istiyorsanız farklı NVA örneklerinden aynı rota için farklı AS yolları *tanıtabilirsiniz* .

* VM 'niz, NVA ve Azure Route sunucunuzu barındırıp 'den farklı bir sanal ağda bulunuyorsa. İki sanal ağ arasında VNet eşlemesinin etkinleştirilip etkinleştirilmediğini *ve* VM 'nin VNET 'Iniz üzerinde uzak yol sunucusu kullan ' ın etkin olup olmadığını denetleyin.

## <a name="next-steps"></a>Sonraki adımlar

[Azure rota sunucusu yapılandırma](quickstart-configure-route-server-powershell.md) hakkında bilgi edinin
