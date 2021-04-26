---
title: 'Sanal WAN: NVA: Azure portal sanal hub yol tablosu oluşturma'
description: Portalı kullanarak bir ağ sanal gerecine trafiği yönlendiren sanal WAN sanal hub yol tablosu.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 409858b2a9005592d895f6044d028221769144cc
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058519"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>NVA 'lar için bir sanal WAN hub yol tablosu oluşturma: Azure portal

Bu makalede, sanal WAN hub 'ına bir ağ sanal gereci (NVA) üzerinden bir bağlı bileşen sanal ağına (VNet) bağlı bir daldan (Şirket içi site) giden trafiğin nasıl yapılacağı gösterilmektedir.

![Sanal WAN diyagramı](./media/virtual-wan-route-table-nva/vwanroute.png)

## <a name="before-you-begin"></a>Başlamadan önce

Aşağıdaki ölçütleri karşıladığınızı doğrulayın:

*  Bir ağ sanal gereci (NVA) vardır. Bir ağ sanal gereci, genellikle bir sanal ağdaki Azure Marketi 'nden sağlanan üçüncü taraf bir yazılımdır.

    * NVA ağ arabirimine özel bir IP adresinin atanması gerekir.

    * NVA sanal hub 'da dağıtılmadı. Ayrı bir sanal ağda dağıtılması gerekir.

    *  NVA sanal ağında bir veya birden çok sanal ağ bağlantısı olabilir. Bu makalede, NVA sanal ağına ' dolaylı bağlı bileşen VNet ' olarak değineceğiz. Bu sanal ağlar, VNet eşlemesi kullanılarak NVA VNet 'e bağlanabilir. VNet eşleme bağlantıları, VNet 1, VNet 2 ve NVA VNet arasında yukarıdaki şekilde siyah oklara göre gösterilmiştir.
*  İki sanal ağ oluşturdunuz. Bunlar, bağlı olan VNET 'ler olarak kullanılacaktır.

    * VNet bağlı bileşen adres alanları şunlardır: VNet1:10.0.2.0/24 ve VNet2:10.0.3.0/24. Sanal ağ oluşturma hakkında bilgilere ihtiyacınız varsa bkz. [sanal ağ oluşturma](../virtual-network/quick-create-portal.md).

    * VNET 'lerden hiçbirinde sanal ağ geçidi olmadığından emin olun.

    * VNET 'ler bir ağ geçidi alt ağı gerektirmez.

## <a name="1-sign-in"></a><a name="signin"></a>1. oturum aç

Bir tarayıcıdan [Azure portalına](https://portal.azure.com) gidin ve Azure hesabınızla oturum açın.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. sanal WAN oluşturun

Sanal WAN oluşturun. Aşağıdaki örnek değerleri kullanın:

* **Sanal WAN adı:** myvirtualwan
* **Kaynak grubu:** testrg
* **Konum:** Batı ABD

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. bir hub oluşturun

Hub 'ı oluşturun. Aşağıdaki örnek değerleri kullanın:

* **Konum:** Batı ABD
* **Ad:** westushub
* **Hub özel adres alanı:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. bir hub yol tablosu oluşturun ve uygulayın

Hub 'ı bir hub yol tablosuyla güncelleştirin. Aşağıdaki örnek değerleri kullanın:

* **Bağlı ağ VNET adres alanları:** (VNet1 ve VNet2) 10.0.2.0/24 ve 10.0.3.0/24
* **DMZ NVA ağ arabirimi özel IP adresi:** 10.0.4.5

1. Sanal WAN 'nize gidin.
2. Yol tablosu oluşturmak istediğiniz hub 'a tıklayın.
3. **...** Öğesine ve ardından **sanal hub 'ı Düzenle**' ye tıklayın.
4. **Sanal hub 'ı Düzenle** sayfasında, aşağı kaydırın ve yönlendirme için onay kutusu **kullan tablosunu** seçin.
5. **IF Destination öneki** sütununda, adres alanlarını ekleyin. **Sonraki atlamaya gönder** sütununda, DMZ NVA ağ ARABIRIMI özel IP adresini ekleyin.

   > [!NOTE]
   > DMZ NVA ağı yerel Hub için geçerlidir.
   
6. Merkez kaynağını rota tablosu ayarlarıyla güncelleştirmek için **Onayla** ' ya tıklayın.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. VNet bağlantıları oluşturma

Her bir dolaylı bağlı bileşen VNet 'ten (VNet1 ve VNet2) hub 'a bir sanal ağ bağlantısı oluşturun. Bu sanal ağ bağlantıları, yukarıdaki şekilde mavi oklara göre gösterilmiştir. Ardından, NVA VNet 'ten hub 'a VNet bağlantısı oluşturun (şekildeki siyah ok tuşuna basın).

 Bu adım için aşağıdaki değerleri kullanabilirsiniz:

| Sanal ağın adı| Bağlantı adı|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Bağlanmak istediğiniz her sanal ağ için aşağıdaki yordamı tekrarlayın.

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
