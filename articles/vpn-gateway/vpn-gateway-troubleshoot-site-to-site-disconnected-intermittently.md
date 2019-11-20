---
title: Azure siteden siteye VPN bağlantısını zaman aralıklı olarak giderme | Microsoft Docs
description: Siteden siteye VPN bağlantısının düzenli olarak bağlantısı kesilmesinde sorun giderme hakkında bilgi edinin.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 394062257f09bb0b8cfa6875795b88d35404f87c
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058831"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Sorunu Azure siteden siteye VPN bağlantısı zaman aralıklı

Yeni veya mevcut bir Microsoft Azure siteden siteye VPN bağlantısının kararlı olmaması veya düzenli olarak kesilme sorunu yaşayabilirsiniz. Bu makalede, sorunun nedenini belirlemenize ve çözmenize yardımcı olmak için sorun giderme adımları sunulmaktadır. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Sorun giderme adımları

### <a name="prerequisite-step"></a>Önkoşul adımı

Azure sanal ağ geçidi türünü denetleyin:

1. [Azure Portal](https://portal.azure.com)gidin.
2. Tür bilgileri için sanal ağ geçidinin **genel bakış** sayfasını kontrol edin.
    
    ![Ağ geçidine genel bakış](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>1\. adım şirket içi VPN cihazının doğrulanıp onaylanmadığını denetleyin

1. [Doğrulanan BIR VPN cihazı ve işletim sistemi sürümü](vpn-gateway-about-vpn-devices.md#devicetable)kullanıp kullanmayacağınızı denetleyin. VPN cihazı doğrulanmamış ise, herhangi bir uyumluluk sorunu olup olmadığını görmek için cihaz üreticisine başvurmanız gerekebilir.
2. VPN cihazının doğru yapılandırıldığından emin olun. Daha fazla bilgi için bkz. [cihaz yapılandırma örneklerini Düzenle](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>2\. adım güvenlik Ilişkisi ayarlarını denetleme (ilke tabanlı Azure sanal ağ geçitleri için)

1. Microsoft Azure içindeki **yerel ağ geçidi** tanımındaki sanal ağın, alt ağların ve aralıklarının, ŞIRKET içi VPN cihazındaki yapılandırmayla aynı olduğundan emin olun.
2. Güvenlik Ilişkilendirmesi ayarlarının eşleştiğini doğrulayın.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>3\. adım ağ geçidi alt ağında Kullanıcı tanımlı yollar veya ağ güvenlik grupları denetimi

Ağ geçidi alt ağında Kullanıcı tanımlı bir yol, bazı trafiği kısıtlıyor ve diğer trafiğe izin verebilir. Bu, VPN bağlantısının bazı trafik için güvenilir olmadığı ve başkaları için iyi hale gelmesini sağlar. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>4\. adım "alt ağ çifti başına bir VPN tüneli" ayarını denetleyin (ilke tabanlı sanal ağ geçitleri için)

Şirket içi VPN cihazının, ilke tabanlı sanal ağ geçitleri için **alt ağ çifti başına BIR VPN tüneline** ayarlanmış olduğundan emin olun.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>5\. adım güvenlik Ilişkisi sınırlaması denetimi (ilke tabanlı sanal ağ geçitleri için)

Ilke tabanlı sanal ağ geçidi 200 alt ağ güvenlik Ilişkisi çifti sınırına sahiptir. Azure sanal ağ alt ağlarının sayısı, yerel alt ağların sayısıyla çarpıldığı zaman 200 ' den büyükse, sporlı alt ağların bağlantısını kesebilirsiniz görürsünüz.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Adım 6 şirket içi VPN cihazı dış arabirim adresini denetle

- VPN cihazının Internet 'e yönelik IP adresi, Azure 'daki **yerel ağ geçidi** tanımına dahil ise, tek tek, tek bir ADIC bağlantısı yaşayabilirsiniz.
- Cihazın dış arabirimi doğrudan Internet üzerinde olmalıdır. Internet ile cihaz arasında ağ adresi çevirisi (NAT) veya güvenlik duvarı olmaması gerekir.
-  Güvenlik Duvarı kümelemesini bir sanal IP 'ye sahip olacak şekilde yapılandırırsanız, kümeyi kesmeniz ve VPN gerecini doğrudan ağ geçidinin arabirim oluşturup gönderebileceği bir ortak arabirime kullanıma sunmalısınız.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>7\. adım şirket içi VPN cihazında kusursuz Iletme gizliliği 'nin etkin olup olmadığını denetleyin

**Kusursuz Iletme gizliliği** özelliği, bağlantı kesme sorunlarına neden olabilir. VPN cihazında **kusursuz Iletme gizliliği** etkinse, özelliği devre dışı bırakın. Ardından [sanal ağ geçidi IPSec ilkesini güncelleştirin](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Sonraki adımlar

- [Bir sanal ağa siteden siteye bağlantı yapılandırma](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Siteden siteye VPN bağlantıları için IPSec/ıKE ilkesini yapılandırma](vpn-gateway-ipsecikepolicy-rm-powershell.md)

