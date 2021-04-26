---
title: 'Azure VPN Gateway: P2S VPN istemci profilleri hakkında'
description: Bir VPN istemci profili için ihtiyaç duyduğunuz bilgileri bulmak için bu makaleyi kullanın.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: 9bb66363d525648df08f32451842402ad1d0d93b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99979083"
---
# <a name="working-with-p2s-vpn-client-profile-files"></a>P2S VPN istemci profili dosyalarıyla çalışma

Profil dosyaları, bir VPN bağlantısını yapılandırmak için gereken bilgileri içerir. Bu makale, bir VPN istemci profili için gereken bilgileri elde etmenize ve anlamanıza yardımcı olur.

## <a name="generate-and-download-profile"></a>Profil oluştur ve indir

PowerShell kullanarak veya Azure portal kullanarak istemci yapılandırma dosyaları oluşturabilirsiniz. Her iki yöntem de aynı ZIP dosyasını döndürür.

### <a name="portal"></a>Portal

1. Azure portal, bağlanmak istediğiniz sanal ağ için sanal ağ geçidine gidin.
1. Sanal ağ geçidi sayfasında, **Noktadan siteye yapılandırma**' yı seçin.
1. Noktadan siteye yapılandırma sayfasının en üstünde **VPN Istemcisini indir**' i seçin. İstemci yapılandırma paketinin oluşturması birkaç dakika sürer.
1. Tarayıcınız, bir istemci yapılandırması ZIP dosyasının kullanılabilir olduğunu gösterir. Bu, ağ geçidiniz ile aynı adı taşır. Klasörleri görüntülemek için dosyayı sıkıştırmayı açın.

### <a name="powershell"></a>PowerShell

PowerShell kullanarak oluşturmak için aşağıdaki örneği kullanabilirsiniz:

1. VPN istemci yapılandırma dosyalarını oluştururken, '-AuthenticationMethod ' değeri ' EapTls ' olur. Aşağıdaki komutu kullanarak VPN istemci yapılandırma dosyalarını oluşturun:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Zip dosyasını indirmek için URL 'YI tarayıcınıza kopyalayın ve ardından klasörleri görüntülemek için dosyayı açın.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **OpenVPN klasörü** , anahtarı ve sertifikayı içerecek şekilde değiştirilmesi gereken *ovpn* profilini içerir. Daha fazla bilgi için bkz. [Azure Için OpenVPN Istemcilerini yapılandırma VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). VPN ağ geçidinde Azure AD kimlik doğrulaması seçilirse, bu klasör ZIP dosyasında mevcut değildir. Bunun yerine AzureVPN klasörüne gidin ve azurevpnconfig.xml bulun.

## <a name="next-steps"></a>Sonraki adımlar

Noktadan siteye hakkında daha fazla bilgi için bkz. [Noktadan siteye hakkında](point-to-site-about.md).
