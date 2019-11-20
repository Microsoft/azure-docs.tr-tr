---
title: 'Sanal WAN: NVA: Azure portal sanal hub yol tablosu oluşturma'
description: Portalı kullanarak bir ağ sanal gerecine trafiği yönlendiren sanal WAN sanal hub yol tablosu.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 8f24b94226daffb769993c9f6659909fdff039b6
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014977"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>NVA 'lar için bir sanal WAN hub yol tablosu oluşturma: Azure portal

Bu makalede, bir hub 'dan ağ sanal gerecine (NVA) nasıl trafik Bubir şekilde gösterilmektedir.

![Sanal WAN diyagramı](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Başlamadan önce

Aşağıdaki ölçütleri karşıladığınızı doğrulayın:

*  Bir ağ sanal gereci (NVA) vardır. Bir ağ sanal gereci, genellikle bir sanal ağdaki Azure Marketi 'nden sağlanan üçüncü taraf bir yazılımdır.

    * NVA ağ arabirimine özel bir IP adresinin atanması gerekir.

    * NVA sanal hub 'da dağıtılmadı. Ayrı bir sanal ağa dağıtılması gerekir.

    *  NVA VNet 'e bağlı bir veya daha fazla sanal ağ olabilir. Bu makalede, NVA VNet 'e ' dolaylı bağlı bileşen VNet ' olarak başvurduk. VNet eşlemesi kullanılarak bu sanal ağlar NVA VNet 'e bağlanabilir.
*  2 sanal ağ oluşturdunuz. Bunlar, bağlı olan VNET 'ler olarak kullanılacaktır.

    * Bu alıştırmada, VNet 'in bağlı olduğu adres alanları şunlardır: VNet1:10.0.2.0/24 ve VNet2:10.0.3.0/24. VNet oluşturma hakkında bilgi için bkz. [sanal ağ oluşturma](../virtual-network/quick-create-portal.md).

    * VNET 'lerden hiçbirinde sanal ağ geçidi olmadığından emin olun.
    * Bu yapılandırma için bu sanal ağlar bir ağ geçidi alt ağı gerektirmez.

## <a name="signin"></a>1. oturum aç

Bir tarayıcıdan [Azure portalına](https://portal.azure.com) gidin ve Azure hesabınızla oturum açın.

## <a name="vwan"></a>2. sanal WAN oluşturun

Sanal WAN oluşturun. Bu alıştırmanın amaçları doğrultusunda, aşağıdaki değerleri kullanabilirsiniz:

* **Sanal WAN adı:** myvirtualwan
* **Kaynak grubu:** testrg
* **Konum:** Batı ABD

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. bir hub oluşturun

Hub 'ı oluşturun. Bu alıştırmanın amaçları doğrultusunda, aşağıdaki değerleri kullanabilirsiniz:

* **Konum:** Batı ABD
* **Ad:** westushub
* **Hub özel adres alanı:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. bir hub yol tablosu oluşturun ve uygulayın

Hub 'ı bir hub yol tablosuyla güncelleştirin. Bu alıştırmanın amaçları doğrultusunda, aşağıdaki değerleri kullanabilirsiniz:

* **Dolaylı bağlı olan VNET adres alanları:** (VNet1 ve VNet2) 10.0.2.0/24 ve 10.0.3.0/24
* **DMZ NVA ağ arabirimi özel IP adresi:** 10.0.4.5

1. Sanal WAN 'nize gidin.
2. Yol tablosu oluşturmak istediğiniz hub 'a tıklayın.
3. **...** Öğesine ve ardından **sanal hub 'ı Düzenle**' ye tıklayın.
4. **Sanal hub 'ı Düzenle** sayfasında, aşağı kaydırın ve yönlendirme için onay kutusu **kullan tablosunu**seçin.
5. **IF Destination öneki** sütununda, adres alanlarını ekleyin. **Sonraki atlamaya gönder** sütununda, DMZ NVA ağ ARABIRIMI özel IP adresini ekleyin.
6. Merkez kaynağını rota tablosu ayarlarıyla güncelleştirmek için **Onayla** ' ya tıklayın.

## <a name="connections"></a>5. VNet bağlantıları oluşturma

Her bir dolaylı bağlı bileşen VNet 'ten (VNet1 ve VNet2) hub 'a bir bağlantı oluşturun. Daha sonra, NVA VNet 'ten hub 'a bir bağlantı oluşturun.

 Bu adım için aşağıdaki değerleri kullanabilirsiniz:

| VNet adı| Bağlantı adı|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Bağlanmak istediğiniz her VNet için aşağıdaki yordamı tekrarlayın.

1. Sanal WAN'ınızın sayfasında **Sanal ağ bağlantıları**'na tıklayın.
2. Sanal ağ bağlantısı sayfasında **+Bağlantı ekle**'ye tıklayın.
3. **Bağlantı ekle** sayfasında aşağıdaki alanları doldurun:

    * **Bağlantı adı**: Bağlantınıza bir ad verin.
    * **Hub'lar**: Bu bağlantıyla ilişkilendirmek istediğiniz hub'ı seçin.
    * **Abonelik**: Aboneliği doğrulayın.
    * **Sanal ağ**: Bu hub'a bağlamak istediğiniz sanal ağı seçin. Sanal ağda önceden var olan bir sanal ağ geçidi bulunamaz.
4. Bağlantıyı oluşturmak için **Tamam** ' ı tıklatın.

## <a name="next-steps"></a>Sonraki adımlar

Sanal WAN hakkında daha fazla bilgi için [Sanal WAN'a Genel Bakış](virtual-wan-about.md) sayfasına bakın.