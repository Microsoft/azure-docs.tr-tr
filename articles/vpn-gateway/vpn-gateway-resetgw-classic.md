---
title: IPSec tünellerini yeniden kurmak için bir Azure VPN ağ geçidini sıfırlayın | Microsoft Docs
description: Bu makalede, IPSec tünellerini yeniden kurmak üzere Azure VPN Gateway sıfırlamayı adım adım açıklanmaktadır. Makale hem klasik hem de Kaynak Yöneticisi dağıtım modelleriyle VPN ağ geçitleri için geçerlidir.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/05/2019
ms.author: cherylmc
ms.openlocfilehash: 92978815af22e3ce1a549b9ca3e335befca8c918
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563039"
---
# <a name="reset-a-vpn-gateway"></a>VPN Gateway sıfırlama

Bir veya daha fazla Siteden Siteye VPN tünelinde şirketler arası VPN bağlantısını kaybederseniz bir Azure VPN ağ geçidinin sıfırlanması yararlıdır. Bu durumda şirket içi VPN cihazlarınızın tümü düzgün çalışır, ancak Azure VPN ağ geçitleriyle IPsec tünelleri kuramaz. Bu makale, VPN ağ geçidinizi sıfırlamanıza yardımcı olur.

### <a name="what-happens-during-a-reset"></a>Sıfırlama sırasında ne olur?

VPN ağ geçidi, etkin bekleme yapılandırmasında çalışan iki sanal makine örneklerinden oluşur. Ağ geçidini sıfırladıktan sonra, ağ geçidini yeniden başlatır ve ardından Şirket içi konfigürasyonları buna yeniden uygular. Ağ geçidi, sahip olduğu genel IP adresini tutar. Bu durum VPN yönlendirici yapılandırmasını Azure VPN ağ geçidi için yeni bir ortak IP adresi ile güncelleştirmenizin gerekli olmadığı anlamına gelir.

Ağ geçidini sıfırlamak için komutunu verdiğinizde, geçerli etkin Azure VPN ağ geçidi örneği hemen yeniden başlatılır. Etkin örnekten yük devretme sırasında (yeniden başlatıldığında), bekleme örneğine yük devretme sırasında kısa bir boşluk olacaktır. Boşluk bir dakikadan az olmalıdır.

İlk yeniden başlatma sonrasında bağlantı geri kazanılmazsa ikinci sanal makine örneğini (yeni etkin ağ geçidi) yeniden başlatmak için aynı komutu yeniden verin. Arka arkaya iki yeniden başlatma istenirse her iki sanal makine örneğinin (etkin ve bekleme) yeniden başlatılma süresi biraz daha uzun olur. Bu, VPN bağlantısında daha uzun bir boşluğa neden olur ve VM 'Ler için en fazla 30 ila 45 dakika sonra yeniden başlatmalar tamamlanır.

İki yeniden başlatıldıktan sonra, şirket içi bağlantı sorunlarıyla karşılaşmaya devam ediyorsanız lütfen Azure portal bir destek isteği açın.

## <a name="before"></a>Başlamadan önce

Ağ geçidinizi sıfırlamadan önce her bir IPsec Siteden Siteye (S2S) VPN tüneli için aşağıda listelenen anahtar öğeleri doğrulayın. Öğelerdeki uyuşmazlık S2S VPN tünellerinin bağlantısının kesilmesiyle sonuçlanır. Şirket içi ve Azure VPN ağ geçitleriniz için yapılandırmaların doğrulanması ve düzeltilmesi, ağ geçitlerinde bulunan diğer çalışma bağlantıları için gereksiz yeniden başlatmalar ve kesintilerden tasarruf sağlar.

Ağ geçidinizi sıfırlamadan önce aşağıdaki öğeleri doğrulayın:

* Hem Azure VPN ağ geçidi hem de şirket içi VPN ağ geçidi için İnternet IP adresleri (VIP) hem Azure hem de şirket içi VPN ilkelerinde doğru şekilde yapılandırılmıştır.
* Önceden paylaşılan anahtar Azure ve şirket içi VPN ağ geçitlerinde aynı olmalıdır.
* Şifreleme, karma algoritmaları ve PFS (Kusursuz İletme Gizliliği) gibi belirli IPsec/IKE yapılandırmalarını uygularsanız hem Azure hem de şirket içi VPN ağ geçitlerinin aynı yapılandırmalara sahip olduğundan emin olun.

## <a name="portal"></a>Azure portal

Azure portal kullanarak Kaynak Yöneticisi VPN ağ geçidini sıfırlayabilirsiniz. Klasik bir ağ geçidini sıfırlamak istiyorsanız, bkz. [PowerShell](#resetclassic) adımları.

### <a name="resource-manager-deployment-model"></a>Resource Manager dağıtım modeli

1. [Azure Portal](https://portal.azure.com) açın ve sıfırlamak istediğiniz kaynak yöneticisi sanal ağ geçidine gidin.
2. Sanal ağ geçidi dikey penceresinde ' Sıfırla ' seçeneğine tıklayın.

   ![VPN Gateway dikey penceresini Sıfırla](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. Sıfırla dikey penceresinde **Sıfırla** düğmesine tıklayın.

## <a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Resource Manager dağıtım modeli

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bir ağ geçidini sıfırlamaya yönelik cmdlet **sıfırlama-AzVirtualNetworkGateway**' dir. Sıfırlama işlemini gerçekleştirmeden önce, [PowerShell az cmdlet](https://docs.microsoft.com/powershell/module/az.network)'lerinin en son sürümüne sahip olduğunuzdan emin olun. Aşağıdaki örnek, TestRG1 kaynak grubundaki VNet1GW adlı bir sanal ağ geçidini sıfırlar:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Kaynaklanan

Bir dönüş sonucu aldığınızda, ağ geçidi sıfırlamasının başarılı olduğunu varsayabilirsiniz. Ancak, dönüş sonucunda, sıfırlama işleminin başarılı olduğunu açıkça belirten hiçbir şey yoktur. Tam olarak ağ geçidi sıfırlamasının gerçekleştiği sırada bakmak isterseniz, bu bilgileri [Azure Portal](https://portal.azure.com)görüntüleyebilirsiniz. Portalda **' GatewayName '-> kaynak durumu '** a gidin.

### <a name="resetclassic"></a>Klasik dağıtım modeli

Bir ağ geçidini sıfırlamaya yönelik cmdlet **Reset-AzureVNetGateway**. Hizmet yönetimi için Azure PowerShell cmdlet 'lerinin yerel olarak masaüstünüze yüklenmesi gerekir. Azure Cloud Shell kullanamazsınız. Sıfırlama işlemini gerçekleştirmeden önce, [hizmet yönetimi (SM) PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets)'lerinin en son sürümüne sahip olduğunuzdan emin olun. Bu komutu kullanırken, sanal ağın tam adını kullandığınızdan emin olun. Portal kullanılarak oluşturulan klasik VNET 'ler, PowerShell için gereken uzun bir ada sahiptir. ' Get-AzureVNetConfig-ExportToFile C:\Myfoldername\NetworkConfig.xml ' kullanarak uzun adı görüntüleyebilirsiniz.

Aşağıdaki örnek, "Group TestRG1 TestVNet1" adlı bir sanal ağ için ağ geçidini sıfırlar (Bu, portalda yalnızca "TestVNet1" olarak gösterilir):

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
```

Kaynaklanan

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="cli"></a>Azure CLI

Ağ geçidini sıfırlamak için [az Network VNET-Gateway Reset](https://docs.microsoft.com/cli/azure/network/vnet-gateway) komutunu kullanın. Aşağıdaki örnek, TestRG5 kaynak grubundaki VNet5GW adlı bir sanal ağ geçidini sıfırlar:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Kaynaklanan

Bir dönüş sonucu aldığınızda, ağ geçidi sıfırlamasının başarılı olduğunu varsayabilirsiniz. Ancak, dönüş sonucunda, sıfırlama işleminin başarılı olduğunu açıkça belirten hiçbir şey yoktur. Tam olarak ağ geçidi sıfırlamasının gerçekleştiği sırada bakmak isterseniz, bu bilgileri [Azure Portal](https://portal.azure.com)görüntüleyebilirsiniz. Portalda **' GatewayName '-> kaynak durumu '** a gidin.
