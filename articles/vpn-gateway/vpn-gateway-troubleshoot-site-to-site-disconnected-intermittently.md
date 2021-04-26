---
title: Azure siteden siteye VPN bağlantısını zaman aralıklı olarak giderme
description: Siteden siteye VPN bağlantısının düzenli olarak bağlantısı kesilmesinde sorun giderme hakkında bilgi edinin.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/22/2021
ms.author: genli
ms.openlocfilehash: 38846bbe717912092ccfe2b236b717770b79302f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867262"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Sorun giderme: Azure Siteden Siteye VPN bağlantısı aralıklı olarak kesiliyor

Yeni veya mevcut bir Microsoft Azure siteden siteye VPN bağlantısının kararlı olmaması veya düzenli olarak kesilme sorunu yaşayabilirsiniz. Bu makalede, sorunun nedenini belirlemenize ve çözmenize yardımcı olmak için sorun giderme adımları sunulmaktadır. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Sorun giderme adımları

### <a name="prerequisite-step"></a>Önkoşul adımı

Azure sanal ağ geçidi türünü denetleyin:

1. [Azure Portal](https://portal.azure.com)gidin.
2. Tür bilgileri için sanal ağ geçidinin **genel bakış** sayfasını kontrol edin.
    
    ![Ağ geçidine genel bakış](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>1. adım şirket içi VPN cihazının doğrulanıp onaylanmadığını denetleyin

1. [Doğrulanan BIR VPN cihazı ve işletim sistemi sürümü](vpn-gateway-about-vpn-devices.md#devicetable)kullanıp kullanmayacağınızı denetleyin. VPN cihazı doğrulanmamış ise, herhangi bir uyumluluk sorunu olup olmadığını görmek için cihaz üreticisine başvurmanız gerekebilir.
2. VPN cihazının doğru yapılandırıldığından emin olun. Daha fazla bilgi için bkz. [cihaz yapılandırma örneklerini Düzenle](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>2. adım güvenlik Ilişkisi ayarlarını denetleme (ilke tabanlı Azure sanal ağ geçitleri için)

1. Microsoft Azure içindeki **yerel ağ geçidi** tanımındaki sanal ağın, alt ağların ve aralıklarının, ŞIRKET içi VPN cihazındaki yapılandırmayla aynı olduğundan emin olun.
2. Güvenlik Ilişkilendirmesi ayarlarının eşleştiğini doğrulayın.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>3. adım ağ geçidi alt ağında User-Defined yollar veya ağ güvenlik grupları denetimi

Ağ geçidi alt ağında Kullanıcı tanımlı bir yol, bazı trafiği kısıtlıyor ve diğer trafiğe izin verebilir. Bu, VPN bağlantısının bazı trafik için güvenilir olmadığı ve başkaları için iyi hale gelmesini sağlar. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>4. adım "alt ağ çifti başına bir VPN tüneli" ayarını denetleyin (ilke tabanlı sanal ağ geçitleri için)

Şirket içi VPN cihazının, ilke tabanlı sanal ağ geçitleri için **alt ağ çifti başına BIR VPN tüneline** ayarlanmış olduğundan emin olun.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>5. adım güvenlik Ilişkisi sınırlaması denetimi (ilke tabanlı sanal ağ geçitleri için)

Ilke tabanlı sanal ağ geçidi 200 alt ağ güvenlik Ilişkisi çifti sınırına sahiptir. Azure sanal ağ alt ağlarının sayısı, yerel alt ağların sayısıyla çarpıldığı zaman 200 ' den büyükse, sporlı alt ağların bağlantısını kesebilirsiniz görürsünüz.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Adım 6 şirket içi VPN cihazı dış arabirim adresini denetle

VPN cihazının Internet 'e yönelik IP adresi, Azure 'daki **yerel ağ geçidi** tanımına dahil ise, tek tek, tek bir ADIC bağlantısı yaşayabilirsiniz.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>7. adım şirket içi VPN cihazında kusursuz Iletme gizliliği 'nin etkin olup olmadığını denetleyin

**Kusursuz Iletme gizliliği** özelliği, bağlantı kesme sorunlarına neden olabilir. VPN cihazında **kusursuz Iletme gizliliği** etkinse, özelliği devre dışı bırakın. Ardından [sanal ağ geçidi IPSec ilkesini güncelleştirin](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Sonraki adımlar

- [Bir sanal ağa siteden siteye bağlantı yapılandırma](./tutorial-site-to-site-portal.md)
- [Siteden siteye VPN bağlantıları için IPSec/ıKE ilkesini yapılandırma](vpn-gateway-ipsecikepolicy-rm-powershell.md)
