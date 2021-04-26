---
title: 'Senaryo: sanal WAN için Azure Güvenlik Duvarı özel yönlendirmesi'
titleSuffix: Azure Virtual WAN
description: Doğrudan sanal ağlar arasındaki yönlendirme yönlendirmesi trafiği için senaryolar->Internet/dal ve VNet trafik akışları için Azure Güvenlik duvarı kullanma
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: d083607782f96744ecbd7d23976f77ee53fec49d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94515578"
---
# <a name="scenario-azure-firewall---custom"></a>Senaryo: Azure Güvenlik Duvarı-özel

Sanal WAN sanal hub 'ı yönlendirme ile çalışırken, kullanılabilecek oldukça az sayıda senaryo vardır. Bu senaryoda, hedef, trafiği doğrudan sanal ağlar arasında yönlendirmektir, ancak sanal ağdan Internete/dalına ve daldan VNet trafik akışları için Azure Güvenlik Duvarı 'nı kullanır.

## <a name="design"></a><a name="design"></a>Tasarım

Kaç tane yol tablosunun gerekli olacağını anlamak için, her hücrenin bir kaynağın (satır) bir hedefle (sütun) iletişim kurup kuramayacağını temsil ettiği bir bağlantı matrisi oluşturabilirsiniz. Bu senaryodaki bağlantı matrisi önemsiz, ancak diğer senaryolarla tutarlı olmaya devam edebilir.

**Bağlantı matrisi**

| Kaynak           | Hedef:      | *Sanal ağlar*      | *Dallar*    | *İnternet*   |
|---             |---       |---           |---            |---           |
| **Sanal ağlar**      |   &#8594;|    Direct    |     AzFW      |     AzFW     |
| **Dallar**   |   &#8594;|    AzFW      |    Direct     |    Direct    |

Önceki tabloda, "doğrudan" sanal WAN 'daki Azure güvenlik duvarından geçen trafik olmadan iki bağlantı arasındaki doğrudan bağlantıyı temsil eder ve "AzFW", akışın Azure güvenlik duvarından geçmeyeceğini gösterir. Matriste iki ayrı bağlantı deseni olduğundan, aşağıdaki şekilde yapılandırılacak iki yol tablosuna ihtiyacımız olacak:

* Sanal ağlar:
  * İlişkili yol tablosu: **RT_VNet**
  * Yol tablolarına yayma: **RT_VNet**
* Dallar
  * İlişkili yol tablosu: **varsayılan**
  * Yol tablolarına yayma: **varsayılan**

> [!NOTE]
> Her bölgede tek bir güvenli sanal hub ile ayrı bir sanal WAN örneği oluşturabilir ve sonra her bir sanal WAN 'ı siteden siteye VPN aracılığıyla birbirlerine bağlayabilirsiniz.

Sanal hub yönlendirmesi hakkında daha fazla bilgi için bkz. [sanal hub yönlendirmesi hakkında](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>İş akışı

Bu senaryoda, VNet 'Ten Internet 'e, VNet 'Ten dala veya şubelerden VNET 'e trafik için Azure Güvenlik Duvarı üzerinden trafiği yönlendirmek, ancak VNet 'ten VNet 'e trafik için doğrudan gitmek istiyor. Azure Güvenlik Duvarı Yöneticisi kullandıysanız, yol ayarları otomatik olarak **varsayılan yol tablosuna** doldurulur. Özel trafik, VNet ve dallar için geçerlidir, Internet trafiği 0.0.0.0/0 için geçerlidir.

VPN, ExpressRoute ve kullanıcı VPN bağlantıları toplu olarak dallar olarak adlandırılır ve aynı (varsayılan) yol tablosuyla ilişkilendirir. Tüm VPN, ExpressRoute ve kullanıcı VPN bağlantıları, rotaları aynı yol tabloları kümesine yayar. Bu senaryoyu yapılandırmak için aşağıdaki adımları göz önünde bulundurun:

1. Özel bir yol tablosu oluşturun **RT_VNet**.
1. VNet 'Ten Internete ve sanal ağa VNet 'i etkinleştirmek için bir yol oluşturun: Azure Güvenlik Duvarı 'nı işaret eden bir sonraki atlamada 0.0.0.0/0. Yayma bölümünde, sanal ağların seçili olduğundan emin olmak için, daha belirli yollar sağlar ve böylece VNet-VNet doğrudan trafik akışına izin verilir.

   * **İlişkilendirme bölümünde:** sanal ağların bu yol tablosunun yollarına göre hedefe ulaşacak olduğunu belirten sanal ağlar ' ı seçin.
   * **Yayılmada:** sanal ağların bu yol tablosuna yaymasını belirten VNET 'leri seçin; diğer bir deyişle, bu yol tablosuna daha fazla özel yol yayılır ve bu sayede VNet ile VNet arasındaki doğrudan trafik akışını güvence altına alırsınız.

1. Azure Güvenlik Duvarı aracılığıyla daldan VNet akışını etkinleştirmek için **varsayılan yol tablosuna** sanal ağlar için bir toplu statik yol ekleyin.

   * Dalların ilişkilendirildiğini ve varsayılan yol tablosuna yayildiğini unutmayın.
   * Dallar RT_VNet yol tablosuna yayılmaz. Bu, Azure Güvenlik Duvarı aracılığıyla VNet 'ten dala trafiği akışa almanızı sağlar.

Bu, **Şekil 1**' de gösterildiği gibi yönlendirme yapılandırması değişikliklerine neden olur.

**Şekil 1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="Şekil 1":::

> [!NOTE]
> Sanal WAN hub 'ları ve bağlı sanal ağlar aynı Azure bölgesinde olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

* Sanal WAN hakkında daha fazla bilgi için bkz. [SSS](virtual-wan-faq.md).
* Sanal hub yönlendirmesi hakkında daha fazla bilgi için bkz. [sanal hub yönlendirmesi hakkında](about-virtual-hub-routing.md).
* Sanal hub yönlendirmeyi yapılandırma hakkında daha fazla bilgi için bkz. [sanal hub yönlendirmeyi yapılandırma](how-to-virtual-hub-routing.md).
