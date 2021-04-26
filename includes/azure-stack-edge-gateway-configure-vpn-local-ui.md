---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96467975"
---
Cihazınızın yerel Web Kullanıcı arabiriminde aşağıdaki adımları uygulayın. Bu adım, VPN yapılandırma dosyasının (veya hizmet etiketi dosyasının) karşıya yüklenmesi sırasında yaklaşık 15 dakika sürer. 

1. **Yapılandırma > VPN**'ye gidin. **Yapılandır**'ı seçin.

    ![Yerel Kullanıcı arabirimini yapılandırma 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. **VPN Yapılandır** dikey penceresinde:

    - **VPN ayarlarını** etkinleştirin.
    - **VPN paylaşılan gizliliğini** sağlayın. Bu, Azure VPN bağlantı kaynağını oluştururken verdiğiniz paylaşılan anahtardır.
    - **VPN ağ GEÇIDI IP** adresini sağlayın. Bu, Azure yerel ağ geçidi IP adresidir.
    - **PFS Grubu** için **hiçbiri**' ni seçin. 
    - **DH grubu** için **grup2**' yi seçin.
    - **IPSec bütünlüğü yöntemi** için **SHA256** öğesini seçin.
    - **IPSec şifre dönüştürme sabitleri** için **GCMAES256** öğesini seçin.
    - **IPSec kimlik doğrulaması dönüştürme sabitleri** için **GCMAES256** öğesini seçin.
    - **Ike şifreleme yöntemi** için **AES256** öğesini seçin.
    - **Uygula**’yı seçin.

        ![Yerel Kullanıcı arabirimini yapılandırma 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    Desteklenen şifreleme algoritmaları hakkında daha fazla bilgi için [Şifreleme gereksinimleri ve Azure VPN ağ geçitleri hakkında](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq)bölümüne gidin. 

3. VPN yolu yapılandırma dosyasını karşıya yüklemek için **karşıya yükle**' yi seçin. 

    ![Yerel Kullanıcı arabirimini yapılandırma 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - Önceki adımda yerel sisteminizde indirdiğiniz Service Tags *JSON* dosyasına gidin.
    - Cihaz, sanal ağ ve ağ geçitleri ile ilişkili Azure bölgesi olarak bölgeyi seçin.
    - **Uygula**’yı seçin.

        ![Yerel Kullanıcı arabirimini yapılandırma 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    Karşıya yükleme cihazda yaklaşık 7-8 dakika sürer.

4. İstemciye özgü yollar eklemek için IP adresi aralıklarını yalnızca VPN kullanarak erişilecek şekilde yapılandırın. 

    - **Yalnızca VPN kullanılarak erişilecek IP adresi aralıkları** altında **Yapılandır**' ı seçin.
    - Geçerli bir IPv4 aralığı sağlayın ve **Ekle**' yi seçin. Diğer aralıkları eklemek için adımları tekrarlayın.
    - **Uygula**’yı seçin.

        ![Yerel Kullanıcı arabirimi 5 ' i yapılandırma](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

