---
title: Azure siteden siteye VPN bağlantısı ile bağlantı kurmak için sorun giderme | Microsoft Docs
description: Aniden çalışmayı durduran ve yeniden bağlanılamaz olan siteden siteye VPN bağlantısı sorunlarını nasıl giderebileceğinizi öğrenin.
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
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: e196c4b512de3fac97347e4c252b697ed6818227
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058865"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Sorunu Bir Azure siteden siteye VPN bağlantısı bağlanamaz ve çalışmayı durduramaz

Şirket içi ağ ve Azure sanal ağı arasında siteden siteye VPN bağlantısı yapılandırdıktan sonra, VPN bağlantısı aniden çalışmayı durduruyor ve yeniden bağlanamaz. Bu makalede, bu sorunu çözmenize yardımcı olacak sorun giderme adımları sunulmaktadır. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Sorun giderme adımları

Sorunu çözmek için öncelikle [Azure VPN Gateway 'i sıfırlamayı](vpn-gateway-resetgw-classic.md) ve ŞIRKET içi VPN cihazından tüneli sıfırlamayı deneyin. Sorun devam ederse, sorunun nedenini belirlemek için aşağıdaki adımları izleyin.

### <a name="prerequisite-step"></a>Önkoşul adımı

Azure VPN ağ geçidinin türünü denetleyin.

1. [Azure Portal](https://portal.azure.com) gidin.

2. Tür bilgileri için VPN ağ geçidinin **genel bakış** sayfasını kontrol edin.
    
    ![Ağ geçidine genel bakış](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>1\.Adım Şirket içi VPN cihazının doğrulanıp onaylanmadığını denetleyin

1. [Doğrulanan BIR VPN cihazı ve işletim sistemi sürümü](vpn-gateway-about-vpn-devices.md#devicetable)kullanıp kullanmayacağınızı denetleyin. Cihaz, doğrulanan bir VPN aygıtı değilse, bir uyumluluk sorunu olup olmadığını görmek için cihaz üreticisine başvurmanız gerekebilir.

2. VPN cihazının doğru yapılandırıldığından emin olun. Daha fazla bilgi için bkz. [cihaz yapılandırma örneklerini düzenleme](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>2\.Adım Paylaşılan anahtarı doğrulama

Anahtarların eşleştiğinden emin olmak için şirket içi VPN cihazının paylaşılan anahtarını Azure sanal ağ VPN ile karşılaştırın. 

Azure VPN bağlantısının paylaşılan anahtarını görüntülemek için aşağıdaki yöntemlerden birini kullanın:

**Azure portal**

1. Oluşturduğunuz VPN Gateway siteden siteye bağlantısına gidin.

2. **Ayarlar** bölümünde, **paylaşılan anahtar**' a tıklayın.
    
    ![Paylaşılan anahtar](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Resource Manager dağıtım modeli için:

    Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Klasik dağıtım modeli için:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Adım 3. VPN eşi IP 'lerini doğrulama

-   Azure 'daki **yerel ağ geçidi** nesnesindeki IP tanımı, şirket içi cihaz IP 'si ile eşleşmelidir.
-   Şirket içi cihazda ayarlanan Azure ağ geçidi IP tanımı, Azure Gateway IP 'si ile eşleşmelidir.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>4\. adımı. Ağ geçidi alt ağındaki UDR ve NSG 'leri denetleyin

Ağ geçidi alt ağında Kullanıcı tanımlı yönlendirmeyi (UDR) veya ağ güvenlik gruplarını (NSG 'ler) denetleyip kaldırın ve ardından sonucu test edin. Sorun çözümlenirse, UDR veya NSG 'nin uyguladığı ayarları doğrulayın.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>5\. adımı. Şirket içi VPN cihazı dış arabirim adresini denetleyin

- VPN cihazının Internet 'e yönelik IP adresi, Azure 'daki **yerel ağ** tanımına dahil ise, tek biçimli, tek bağlantılarla karşılaşabilirsiniz.
- Cihazın dış arabirimi doğrudan Internet üzerinde olmalıdır. Internet ile cihaz arasında ağ adresi çevirisi veya güvenlik duvarı olmaması gerekir.
- Güvenlik Duvarı kümelemesini bir sanal IP 'ye sahip olacak şekilde yapılandırmak için, kümeyi kesmeniz ve VPN gerecini doğrudan ağ geçidinin arabirim oluşturup gönderebileceği ortak bir arabirim üzerinde kullanıma sunmalısınız.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>6\. adım. Alt ağların tam olarak eşleştiğini doğrulama (Azure ilke tabanlı ağ geçitleri)

-   Sanal ağ adres alanının Azure sanal ağı ile şirket içi tanımlar arasında tam olarak eşleştiğinden emin olun.
-   Alt ağların **yerel ağ geçidi** ile şirket içi ağ için şirket içi tanımlar arasında tam olarak eşleştiğini doğrulayın.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>7\. Adım. Azure ağ geçidi sistem durumu araştırmasını doğrulama

1. Aşağıdaki URL 'ye giderek sistem durumu araştırmasını açın:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Sertifika Uyarısı ' na tıklayın.
3. Yanıt alırsanız VPN Gateway sağlıklı olarak değerlendirilir. Yanıt almazsanız, ağ geçidi sağlıklı olmayabilir veya ağ geçidi alt ağındaki bir NSG soruna neden olabilir. Aşağıdaki metin bir örnek yanıttır:

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>8\. Adım Şirket içi VPN cihazında kusursuz iletme gizliliği özelliğinin etkin olup olmadığını denetleyin

Kusursuz iletme gizliliği özelliği, bağlantı kesme sorunlarına neden olabilir. VPN cihazında kusursuz iletme gizliliği etkinse, özelliği devre dışı bırakın. Ardından VPN Gateway IPSec ilkesini güncelleştirin.

## <a name="next-steps"></a>Sonraki adımlar

-   [Bir sanal ağa siteden siteye bağlantı yapılandırma](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Siteden siteye VPN bağlantıları için bir IPSec/ıKE ilkesi yapılandırma](vpn-gateway-ipsecikepolicy-rm-powershell.md)
