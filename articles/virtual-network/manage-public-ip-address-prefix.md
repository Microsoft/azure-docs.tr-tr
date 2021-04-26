---
title: Azure genel IP adresi öneki oluşturma, değiştirme veya silme
titlesuffix: Azure Virtual Network
description: Genel IP adresi önekleri ve bunların nasıl oluşturulacağı, değiştirileceği veya silineceği hakkında bilgi edinin. Daha fazla bilgi bulmak için bkz..
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: 173fa3a8288ccceb07048e83fcec35d67b2fd35f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783440"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Genel IP adresi ön eki oluşturma, değiştirme veya silme

Genel IP adresi ön eki ve oluşturma, değiştirme ve silme hakkında bilgi edinin. Genel IP adresi ön eki, belirttiğiniz genel IP adresi sayısına göre bitişik bir adres aralığıdır. Adresler aboneliğinize atanır. Genel bir IP adresi kaynağı oluşturduğunuzda, önekten statik bir genel IP adresi atayabilir ve adresi sanal makineler, yük dengeleyiciler veya diğer kaynaklarla ilişkilendirerek internet bağlantısını etkinleştirebilirsiniz. Genel IP adresi öneklerine alışkın değilseniz bkz. [genel IP adresi ön ekine genel bakış](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu makalenin herhangi bir bölümündeki adımları tamamlamadan önce aşağıdaki görevleri doldurun:

- Henüz bir Azure hesabınız yoksa [ücretsiz deneme hesabı](https://azure.microsoft.com/free)için kaydolun.
- Portalı kullanıyorsanız, açın https://portal.azure.com ve Azure hesabınızla oturum açın.
- Bu makaledeki görevleri tamamlamaya yönelik PowerShell komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/powershell)komutları çalıştırın veya PowerShell 'i bilgisayarınızdan çalıştırarak çalıştırın. Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Bu öğretici, Azure PowerShell modülü sürümü 1.0.0 veya üstünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.
- Bu makaledeki görevleri gerçekleştirmek için Azure komut satırı arabirimi (CLı) komutlarını kullanıyorsanız, [Azure Cloud Shell](https://shell.azure.com/bash)komutları çalıştırın ya da bilgisayarınızdan CLI 'yı çalıştırarak. Bu öğretici, Azure CLı sürüm 2.0.41 veya üstünü gerektirir. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI 2.0 yükleme](/cli/azure/install-azure-cli). Azure CLı 'yi yerel olarak çalıştırıyorsanız, `az login` Azure ile bir bağlantı oluşturmak için çalıştırmanız da gerekir.

Oturum açtığınızda veya Azure 'a bağlanmak için kullandığınız hesap, [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya [izinlerde](#permissions)listelenen uygun eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atanmalıdır.

Genel IP adresi ön eklerinin ücreti vardır. Ayrıntılar için bkz. [fiyatlandırma](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Genel IP adresi öneki oluşturma

1. Portalın sol üst köşesinde **+ kaynak oluştur**' u seçin.
2. *Market 'Te ara* kutusuna *genel IP öneki* girin. Arama sonuçlarında **genel IP adresi ön eki** göründüğünde, bunu seçin.
3. **Genel IP adresi ön eki** altında **Oluştur**' u seçin.
4. Aşağıdaki ayarlara ait değerleri girin veya seçin, **genel IP adresi öneki oluştur** altında, **Oluştur**' u seçin.

   |Ayar|Gerekli mi?|Ayrıntılar|
   |---|---|---|
   |Abonelik|Yes|Genel IP adresini ilişkilendirmek istediğiniz kaynakla aynı [abonelikte](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) bulunmalıdır.|
   |Kaynak grubu|Yes|, Genel IP adresini ilişkilendirmek istediğiniz kaynakla aynı veya farklı bir [kaynak grubunda](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) bulunabilir.|
   |Name|Yes|Ad, seçtiğiniz kaynak grubu içinde benzersiz olmalıdır.|
   |Region|Yes|, Aralıktan adres atayacağımız genel IP adresleriyle aynı [bölgede](https://azure.microsoft.com/regions)bulunmalıdır.|
   |Ön ek boyutu|Yes| İhtiyaç duyduğunuz ön ek boyutu. Varsayılan değer A/28 veya 16 IP adresleridir.

**Komutlar**

|Araç|Komut|
|---|---|
|CLI|[az Network public-ip Create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>Kullanılabilirlik alanları olan bölgelerde, bir genel IP adresi ön eki oluşturmak için PowerShell veya CLı komutlarını kullanabilirsiniz: Bu, belirli bir bölgeyle ilişkili olan veya bölge artıklığı kullanma.  API sürüm 2020-08-01 veya üzeri için bir bölge parametresi sağlanmazsa, bir genel IP adresi ön eki oluşturulur. API 'nin 2020-08-01 'den eski sürümleri için, bölgesel olarak yedekli bir genel IP adresi ön eki oluşturulur. 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Önekten statik bir genel IP adresi oluşturma
Ön ek oluşturduktan sonra, önekten statik IP adresleri oluşturmanız gerekir. Bunu yapmak için aşağıdaki adımları izleyin.

1. Azure portal üst kısmında bulunan metin *arama kaynaklarını* içeren kutuya *genel IP adresi ön eki* yazın. **Genel IP adresi önekleri** arama sonuçlarında görüntülendiğinde, bunu seçin.
2. Genel IP 'Leri oluşturmak istediğiniz ön eki seçin.
3. Arama sonuçlarında göründüğünde, bunu seçin ve genel bakış bölümünde **+ IP adresi ekle** ' ye tıklayın.
4. **Genel IP adresi oluştur** altında aşağıdaki ayarlara ait değerleri girin veya seçin. Bir önek standart SKU, IPv4 ve statik olduğundan, yalnızca aşağıdaki bilgileri sağlamanız gerekir:

   |Ayar|Gerekli mi?|Ayrıntılar|
   |---|---|---|
   |Name|Yes|Genel IP adresinin adı, seçtiğiniz kaynak grubu içinde benzersiz olmalıdır.|
   |Boşta kalma zaman aşımı (dakika)|No|Bir TCP veya HTTP bağlantısının istemcilere bağlı kalmadan açık tutulması için, etkin tut iletileri göndermek için kaç dakika sürer. |
   |DNS ad etiketi|No|İçinde adını oluşturduğunuz Azure bölgesi içinde benzersiz olmalıdır (tüm abonelikler ve tüm müşteriler genelinde). Azure adı ve IP adresini DNS 'ye otomatik olarak kaydeder, böylece ada sahip bir kaynağa bağlanabilirsiniz. Azure, tam DNS adını oluşturmak için *Location.cloudapp.Azure.com* gibi bir varsayılan alt ağ (seçtiğiniz konum sizin seçtiğiniz konumdur), sağladığınız ada ekler. Daha fazla bilgi için bkz. [Azure genel IP adresi ile Azure DNS kullanma](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

Alternatif olarak, genel bir IP adresi kaynağı oluşturmak için aşağıdaki CLı ve PS komutlarını--public-IP-prefix (CLı) ve-PublicIpPrefix (PS) parametreleriyle birlikte kullanabilirsiniz. 

|Araç|Komut|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)|
|PowerShell|[New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-or-delete-a-prefix"></a>Ön eki görüntüleme veya silme

1. Azure portal üst kısmında bulunan metin *arama kaynaklarını* içeren kutuya *genel IP adresi ön eki* yazın. **Genel IP adresi önekleri** arama sonuçlarında görüntülendiğinde, bunu seçin.
2. Görüntülemek istediğiniz genel IP adresi ön ekinin adını seçin, ayarları değiştirin veya listeden silin.
3. Genel IP adresi önekini görüntüleme, silme veya değiştirme seçeneklerine bağlı olarak, aşağıdaki seçeneklerden birini doldurun.
   - **Görünüm**: **genel bakış** bölümü önek gibi genel IP adresi ön eki için anahtar ayarlarını gösterir.
   - **Sil**: genel IP adresi önekini silmek Için **genel bakış** bölümünde **Sil** ' i seçin. Ön ek içindeki adresler genel IP adresi kaynaklarıyla ilişkiliyse, önce genel IP adresi kaynaklarını silmeniz gerekir. Bkz. [genel IP adresi silme](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

**Komutlar**

|Araç|Komut|
|---|---|
|CLI|Genel IP adreslerini listelemek için [az Network public-ip önek listesi](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_list) , [az Network public-ip prefix](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show) ayarları göstermek için gösterir; güncelleştirmek için [az Network public-ip prefix Update](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_update) ; [az Network public-IP öneki](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete) silinecek Sil|
|PowerShell|Bir genel IP adresi nesnesi almak ve ayarlarını görüntülemek için [Get-azpublicıpprefix](/powershell/module/az.network/get-azpublicipprefix) , Update Settings olarak [set-azpublicipprefix](/powershell/module/az.network/set-azpublicipprefix) ; Silmek için [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix)|

## <a name="permissions"></a>İzinler

Genel IP adresi öneklerinde görevler gerçekleştirmek için, hesabınız [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya aşağıdaki tabloda listelenen uygun eylemlere atanmış [özel](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) bir role atanmalıdır:

| Eylem                                                            | Name                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft. Network/Publicipönekleri/okuma                           | Genel IP adresi ön ekini oku                                |
| Microsoft. Network/Publicipöneklerini/yazma                          | Genel IP adresi öneki oluşturma veya güncelleştirme                    |
| Microsoft. Network/Publicipöneklerini/silmeyi                         | Genel IP adresi önekini silme                              |
|Microsoft. Network/Publicipöneklerini/JOIN/Action                     | Önekten genel IP adresi oluşturma |

## <a name="next-steps"></a>Sonraki adımlar

- [Genel IP öneki](public-ip-address-prefix.md) kullanmanın senaryoları ve avantajları hakkında bilgi edinin
