---
title: S2S VPN bağlantıları için VPN cihaz yapılandırma betiklerini indirme
description: Bu makalede, Azure Resource Manager kullanarak Azure VPN ağ geçitleri ile S2S VPN bağlantıları için VPN cihaz yapılandırma betiklerini indirme işlemi adım adım açıklanmaktadır.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: d2593c656e17310c5afb4a897b94cc8c12a618f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879503"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>S2S VPN bağlantıları için VPN cihaz yapılandırma betiklerini indirme

Bu makalede, Azure Resource Manager kullanarak Azure VPN ağ geçitleri ile S2S VPN bağlantıları için VPN cihaz yapılandırma betiklerini indirme işlemi adım adım açıklanmaktadır. Aşağıdaki diyagramda üst düzey iş akışı gösterilmektedir.

![İndir-betik](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Aşağıdaki cihazlarda kullanılabilir betikler var:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about-vpn-device-configuration-scripts"></a><a name="about"></a>VPN cihaz yapılandırması betikleri hakkında

Şirketler arası VPN bağlantısı, bir Azure VPN ağ geçidinden, şirket içi VPN cihazından ve iki bağlantı kurarak bir IPSec S2S VPN tünelinden oluşur. Tipik iş akışı aşağıdaki adımları içerir:

1. Azure VPN ağ geçidi oluşturma ve yapılandırma (sanal ağ geçidi)
2. Şirket içi ağınızı ve VPN cihazınızı temsil eden bir Azure yerel ağ geçidi oluşturun ve yapılandırın
3. Azure VPN ağ geçidi ve yerel ağ geçidi arasında bir Azure VPN bağlantısı oluşturma ve yapılandırma
4. Azure VPN ağ geçidi ile gerçek S2S VPN tüneli oluşturmak için yerel ağ geçidi tarafından temsil edilen şirket içi VPN cihazını yapılandırın

Azure [Portal](./tutorial-site-to-site-portal.md), [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)veya [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)kullanarak 1 ile 3 arasındaki adımları tamamlayabilirsiniz. Son adım, şirket içi VPN cihazlarının Azure dışında yapılandırılmasını içerir. Bu özellik, Azure VPN ağ geçidinizin, sanal ağınız ve şirket içi ağ adresi öneklerinizin ve VPN bağlantı özelliklerinin, vb. için karşılık gelen değerleriyle VPN cihazınız için bir yapılandırma betiği indirmelerini sağlar. Betiği bir başlangıç noktası olarak kullanabilir veya komut dosyasını yapılandırma konsolu aracılığıyla şirket içi VPN cihazlarınıza doğrudan uygulayabilirsiniz.

> [!IMPORTANT]
> * Her VPN cihaz yapılandırma betiği için sözdizimi farklıdır ve modeller ve bellenim sürümlerine yoğun bir şekilde bağımlıdır. Kullanılabilir şablonlara karşı cihaz modelinize ve sürüm bilgilerine özel dikkat edin.
> * Bazı parametre değerleri cihazda benzersiz olmalıdır ve cihaza erişilmeden belirlenemez. Azure tarafından oluşturulan yapılandırma betikleri bu değerleri önceden doldurabilir, ancak sunulan değerlerin cihazınızda geçerli olduğundan emin olmanız gerekir. Örnekler için:
>    * Arabirim numaraları
>    * Erişim denetim listesi numaraları
>    * İlke adları veya numaraları, vb.
> * Betiği uygulamadan önce doğrulamanız gereken parametreleri bulmak için komut dosyasına katıştırılmış "**Değiştir**" anahtar sözcüğünü arayın.
> * Bazı şablonlar, yapılandırmaların kaldırılması için uygulayabileceğiniz bir "**Temizleme**" bölümü içerir. Temizleme bölümleri varsayılan olarak açıklama oluşturulur.

## <a name="download-the-configuration-script-from-azure-portal"></a>Yapılandırma betiğini Azure portal indirin

Bir Azure VPN ağ geçidi, yerel ağ geçidi ve ikisini bağlayan bir bağlantı kaynağı oluşturun. Aşağıdaki sayfa adımlarda size rehberlik eder:

* [Azure portalında Siteden Siteye bağlantı oluşturma](./tutorial-site-to-site-portal.md)

Bağlantı kaynağı oluşturulduktan sonra, VPN cihaz yapılandırma betiklerini indirmek için aşağıdaki yönergeleri izleyin:

1. Bir tarayıcıdan [Azure Portal](https://portal.azure.com) gidin ve gerekirse Azure hesabınızla oturum açın
2. Oluşturduğunuz bağlantı kaynağına gidin. "Tüm hizmetler", ardından "ağ" ve "bağlantılar" ' a tıklayarak tüm bağlantı kaynaklarının listesini bulabilirsiniz.

    ![bağlantı listesi](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Yapılandırmak istediğiniz bağlantıya tıklayın.

    ![bağlantı-genel bakış](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Bağlantıya Genel Bakış sayfasında, kırmızı renkle vurgulanmış şekilde "yapılandırmayı karşıdan yükle" bağlantısına tıklayın; "yapılandırmayı karşıdan yükle" sayfası açılır.

    ![İndir-komut dosyası-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. VPN cihazınız için model aile ve bellenim sürümünü seçin ve ardından "yapılandırmayı Indir" düğmesine tıklayın.

    ![download66-betik-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. İndirdiğiniz betiği (bir metin dosyası) tarayıcınızdan kaydetmeniz istenir.
7. Yapılandırma betiğini indirdikten sonra, bir metin düzenleyici ile açın ve değiştirilmesini gerekebilecek parametreleri belirlemek ve incelemek için "REPLACE" anahtar sözcüğünü arayın.

    ![betiği Düzenle](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Yapılandırma betiğini Azure PowerShell kullanarak İndir



Ayrıca, aşağıdaki örnekte gösterildiği gibi Azure PowerShell kullanarak yapılandırma betiğini indirebilirsiniz:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Yapılandırma betiğini VPN cihazınıza uygulama

Yapılandırma betiğini indirip doğruladıktan sonra, bir sonraki adım, betiği VPN cihazınıza uygulamaktır. Gerçek yordam, VPN cihazınızın yaptığı ve modellerinize göre farklılık gösterir. VPN cihazlarınızın işlem kılavuzlarına veya yönerge sayfalarına bakın.

## <a name="next-steps"></a>Sonraki adımlar

[Siteden siteye bağlantınızı](./tutorial-site-to-site-portal.md)yapılandırmaya devam edin.