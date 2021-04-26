---
title: Azure Stack Edge Mini R Wi-Fi yönetimi
description: Azure Stack Kenarlarınızın Wi-Fi yönetimi için Azure portal nasıl kullanılacağını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: a2cc0707c344c3ca537795666a3f60f648026596
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043776"
---
# <a name="use-the-local-web-ui-to-manage-wireless-connectivity-on-your-azure-stack-edge-mini-r"></a>Azure Stack Edge Mini R 'nizin kablosuz bağlantısını yönetmek için yerel Web Kullanıcı arabirimini kullanma

Bu makalede Azure Stack Edge Mini Cihazınızda kablosuz ağ bağlantısının nasıl yönetileceği açıklanır. Wi-Fi profillerini eklemek, bağlanmak ve silmek için Azure Stack Edge Mini R cihazınızda yerel Web Kullanıcı arabirimini kullanabilirsiniz.

## <a name="about-wi-fi"></a>Wi-Fi hakkında

Azure Stack Edge Mini R cihazınız, ağa veya kablosuz ağ üzerinden her ikisini de işleyebilir. Cihazın kablosuz ağa bağlanmasına izin vermek için etkinleştirilmesi gereken Wi-Fi bağlantı noktası vardır. 

Cihazınız beş bağlantı noktasına, bağlantı noktası 4 ' ten ve bir beşinci Wi-Fi bağlantı noktasına sahiptir. Bir kablosuz ağa bağlıyken bir mini R cihazının arka düzlemi diyagramı aşağıda verilmiştir.

![Wi-Fi için kablolama](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)


## <a name="add-connect-to-wi-fi-profile"></a>Wi-Fi profile ekleme, bağlanma

Wi-Fi Profile eklemek ve bu profili bağlamak için cihazınızın yerel kullanıcı arabiriminde aşağıdaki adımları uygulayın.

1. Cihazınızın yerel Web Kullanıcı arabiriminde **Başlarken** sayfasına gidin. **Ağ** kutucuğunda **Yapılandır**' ı seçin.  
    
    Fiziksel cihazınızda beş ağ arabirimi vardır. PORT 1 ve PORT 2, 1 Gb/sn'lik ağ arabirimleridir. Bağlantı noktası 3 ve bağlantı noktası 4 tüm 10 Gbps ağ arabirimlerdir. Beşinci bağlantı noktası Wi-Fi bağlantı noktasıdır. 

    [![Yerel Web Kullanıcı arabirimi "ağ ayarları" sayfa 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)  
    
    Wi-Fi bağlantı noktasını seçin ve bağlantı noktası ayarlarını yapılandırın. 
    
    > [!IMPORTANT]
    > Wi-Fi bağlantı noktası için bir statik IP adresi yapılandırmanız önemle önerilir.  

    ![Yerel Web Kullanıcı arabirimi "ağ ayarları" sayfa 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    Wi-Fi bağlantı noktası ayarlarını uyguladıktan sonra **ağ** sayfası güncellenir.

    ![Yerel Web Kullanıcı arabirimi "ağ ayarları" sayfa 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)

   
2. **Wi-Fi profili Ekle** ' yi seçin ve Wi-Fi profilinizi karşıya yükleyin. 

    ![Yerel Web Kullanıcı arabirimi "bağlantı noktası WiFi ağ ayarları" 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    Bir kablosuz ağ profili, bir kablosuz ağa bağlanabilmek için SSID (ağ adı), parola anahtarı ve güvenlik bilgilerini içerir. Ortamınızın Wi-Fi profilini ağ yöneticinizden edinebilirsiniz.

    Wi-Fi profillerinizi hazırlama hakkında daha fazla bilgi için bkz. [Azure Stack Edge Mini R cihazlarıyla Wi-Fi profillerini kullanma](azure-stack-edge-mini-r-use-wifi-profiles.md).

    ![Yerel Web Kullanıcı arabirimi "bağlantı noktası WiFi ağ ayarları" 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    Profil eklendikten sonra, Wi-Fi profillerinin listesi yeni profili yansıtacak şekilde güncelleştirilir. Profilde **bağlantı durumu** bağlantısı **kesik** olarak gösterilmelidir. 

    ![Yerel Web Kullanıcı arabirimi "bağlantı noktası WiFi ağ ayarları" 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)
    
3. Kablosuz ağ profili başarıyla yüklendikten sonra bu profile bağlanın. **Wi-Fi Profile Bağlan**' ı seçin. 

    ![Yerel Web Kullanıcı arabirimi "bağlantı noktası Wi-Fi ağ ayarları" 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

4. Önceki adımda eklediğiniz Wi-Fi profilini seçin ve **Uygula**' yı seçin. 

    ![Yerel Web Kullanıcı arabirimi "bağlantı noktası Wi-Fi ağ ayarları" 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    **Bağlantı durumu** **bağlı** olarak güncelleştirilecek. Sinyal gücü, sinyalin kalitesini göstermek için güncelleştirilir. 

    ![Yerel Web Kullanıcı arabirimi "bağlantı noktası Wi-Fi ağ ayarları" 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Büyük miktarlardaki verileri aktarmak için kablosuz ağ yerine kablolu bağlantı kullanmanızı öneririz. 


## <a name="download-wi-fi-profile"></a>Wi-Fi profilini indir

Kablosuz ağ bağlantısı için kullanmakta olduğunuz bir Wi-Fi profilini indirebilirsiniz.

1. Cihazınızın yerel Web Kullanıcı arabiriminde, **yapılandırma > ağ ' a** gidin. 

2. Wi-Fi profili ayarları altında **Profili İndir**' i seçin. Bu, şu anda kullanmakta olduğunuz Wi-Fi profilini indirmelidir.


## <a name="delete-wi-fi-profile"></a>Wi-Fi profilini Sil

Kablosuz ağ bağlantısı için kullanmakta olduğunuz bir Wi-Fi profilini silebilirsiniz.


1. Cihazınızın yerel Web Kullanıcı arabiriminde, **yapılandırma > ağ ' a** gidin. 

2. Wi-Fi profili ayarları altında **Wi-Fi profilini Sil**' i seçin.

3. **Wi-Fi profilini Sil** dikey penceresinde, silmek istediğiniz profili seçin. **Uygula**’yı seçin.


## <a name="configure-cisco-wi-fi-profile"></a>Cisco Wi-Fi profilini yapılandırma

İşte, cihazınızda bir Cisco Kablosuz denetleyicisi ve erişim noktasını yönetme ve yapılandırma ile ilgili bazı yönergeler aşağıda verilmiştir. 

### <a name="dhcp-bridging-mode"></a>DHCP köprü oluşturma modu

Cihazınız için bir Cisco Kablosuz Denetleyici kullanmak istiyorsanız, Kablosuz LAN denetleyicisinde (WLC) dinamik ana bilgisayar Yapılandırma Protokolü (DHCP) köprü oluşturma modunu etkinleştirmeniz gerekir.

Daha fazla bilgi için bkz. [DHCP Köprüleme modu](https://www.cisco.com/c/en/us/support/docs/wireless/4400-series-wireless-lan-controllers/110865-dhcp-wlc.html#anc9).

#### <a name="bridging-configuration-example"></a>Köprü oluşturma yapılandırması örneği

Denetleyicide DHCP köprü oluşturma işlevini etkinleştirmek için, denetleyicideki DHCP proxy özelliğini devre dışı bırakmanız gerekir. Komut satırını kullanarak DHCP köprülemeyi etkinleştirmek için:

```powershell
(Cisco Controller) > config dhcp proxy disable
(Cisco Controller) > show dhcp proxy
DHCP Proxy Behaviour: disabled
```

DHCP sunucusu istemcisiyle aynı katman 2 (L2) ağında yoksa, yayının bir IP Yardımcısı kullanılarak istemci ağ geçidinde DHCP sunucusuna iletilmesi gerekir. Bu, bu yapılandırmanın bir örneğidir:

```powershell
Switch#conf t
Switch(config)#interface vlan <client vlan #>
Switch(config-if)#ip helper-address <dhcp server IP>
```

DHCP köprü oluşturma özelliği genel bir ayardır, bu nedenle Denetleyici içindeki tüm DHCP işlemlerini etkiler. Denetleyicideki tüm gerekli sanal yerel ağlar (VLAN 'lar) için kablolu altyapıya IP Yardımcısı deyimleri eklemeniz gerekir.

### <a name="enable-the-passive-client-for-wlan"></a>WLAN için pasif istemciyi etkinleştirin

Cisco Kablosuz denetleyicisinde kablosuz yerel ağ (WLAN) için pasif istemci özelliğini etkinleştirmek için:

* WLAN ile ilişkili arabirim bir VLAN etiketlemesinin etkinleştirilmiş olması gerekir.
* WLAN için çok noktaya yayın VLAN 'ı etkinleştirilmelidir.
* WLC üzerinde GARP iletme etkinleştirilmelidir.

Daha fazla bilgi için bkz. çok [noktaya yayın Iyileştirmesi hakkında çok noktaya yayın VLAN bilgileri](https://www.cisco.com/c/en/us/td/docs/wireless/controller/8-5/config-guide/b_cg85/wlan_interfaces.html)

### <a name="troubleshoot"></a>Sorun giderme

Azure Stack Edge Mini R cihazında çalışan VM 'lerde IP adresi ayırmaları ile ilgili sorunlarla karşılaşırsanız, ağ ortamınızda yukarıdaki yapılandırma ayarlarının doğrulanması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Mini R cihazının nasıl dağıtılacağını](azure-stack-edge-mini-r-deploy-prep.md)öğrenin.
