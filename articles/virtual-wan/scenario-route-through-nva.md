---
title: 'Senaryo: trafiği bir ağ sanal gereci (NVA) aracılığıyla yönlendirme'
titleSuffix: Azure Virtual WAN
description: Trafiği NVA üzerinden yönlendirme
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 24671a34214864e253d96c356dc8b2853bf6d560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100519805"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Senaryo: Trafiği NVA üzerinden yönlendirme

Sanal WAN sanal hub 'ı yönlendirme ile çalışırken, kullanılabilecek oldukça az sayıda senaryo vardır. Bu NVA senaryosunda, hedef, trafiği bir NVA (ağ sanal gereci) yoluyla VNet 'e ve VNet 'e Dala Dala yönlendirmektir. Sanal hub yönlendirmesi hakkında daha fazla bilgi için bkz. [sanal hub yönlendirmesi hakkında](about-virtual-hub-routing.md).

> [!NOTE]
> Yeni yeteneklerden daha önce olan yollarla zaten bir kurulum yaptıysanız, [sanal hub yönlendirmeyi yapılandırma hakkında](how-to-virtual-hub-routing.md) daha önce sunulan aşağıdaki makale sürümlerindeki adımları kullanın:
>* [Azure portal makalesi](virtual-wan-route-table-nva-portal.md)
>* [PowerShell makalesi](virtual-wan-route-table-nva.md)
>

## <a name="design"></a><a name="design"></a>Tasarım

Bu senaryoda adlandırma kuralını kullanacağız:

* Kullanıcıların bir NVA dağıttığmış olduğu ve diğer sanal ağlara bağlı bileşen (VNet 2 ve VNet 4 ' **te, makalede daha sonra** yer aldığı sanal ağlar IÇIN "NVA VNET").
* Bir NVA VNet 'e (VNet 5, VNet 6, VNet 7 ve VNet 8 ' **de, makalede daha sonra** ) bağlı sanal ağlar IÇIN "NVA ışınsal 'ler".
* Sanal WAN 'a bağlı sanal ağlar için "NVA olmayan VNET 'ler", bir NVA veya diğer VNET 'ler (Bu makalede daha sonra **Şekil 2** ' de yer alan 1 VNET 1 ve VNET 3) yok.
* NVA VNET 'lerin bağlandığı Microsoft tarafından yönetilen sanal WAN hub 'Ları için "hub 'lar". NVA bağlı uç sanal ağları, yalnızca NVA VNET 'lere sanal WAN hub 'larına bağlanmalıdır.

Aşağıdaki bağlantı matrisi, bu senaryoda desteklenen akışları özetler:

**Bağlantı matrisi**

| Kaynak             | Hedef:|   *NVA tekerlek*|*NVA sanal ağları*|*NVA dışı VNET 'ler*|*Dallar*|
|---|---|---|---|---|---|
| **NVA tekerlek**   | &#8594; | NVA VNet üzerinden | Eşleme | NVA VNet üzerinden | NVA VNet üzerinden |
| **NVA sanal ağları**    | &#8594; | Eşleme | Direct | Direct | Direct |
| **NVA dışı VNET 'ler**| &#8594; | NVA VNet üzerinden | Direct | Direct | Direct |
| **Dallar**     | &#8594; | NVA VNet üzerinden | Direct | Direct | Direct |

Bağlantı matrisindeki her bir hücre, VNet veya dalın (akışın "Kimden" tarafı, tablodaki satır başlıkları) bir hedef VNet veya dal (akışın "to" tarafı, tablodaki sütun üst bilgileri) ile iletişim kurar. "Doğrudan", bağlantının sanal WAN tarafından yerel olarak sağlandığı anlamına gelir, "eşleme", bağlantının VNet 'teki bir User-Defined yolu tarafından sağlandığı anlamına gelir, "NVA VNet üzerinden" bağlantısı, bağlantının NVA VNet 'te dağıtılan NVA 'nın altına geçtiği anlamına gelir. Aşağıdaki topluluklara bir göz atın:

* NVA ışınsal 'ler sanal WAN tarafından yönetilmez. Sonuç olarak, diğer VNET 'ler veya dallarla iletişim kurdukları mekanizmalar Kullanıcı tarafından korunur. NVA VNet bağlantısı, VNet eşlemesi tarafından sağlanır ve bir sonraki atlama, Internet bağlantısını, diğer bağlı şubelere ve dallara kapsamalıdır, NVA 'ya işaret eden 0.0.0.0/0 için varsayılan rota
* NVA VNET 'ler, kendi NVA bağlı kuruluşları hakkında bilgilendirir, ancak diğer NVA sanal ağlarına bağlı NVA ışınsal 'ler hakkında bilgi sahibi olur. Örneğin, bu makalede daha sonra gelen şekil 2 ' de, VNET 2, VNET 7 ve VNET 8 gibi diğer bağlı bileşen hakkında değil, VNet 5 ve VNet 6 hakkında bilgi sahibi değildir. Diğer bağlı bileşen öneklerini NVA VNET 'lere eklemek için bir statik yol gerekir
* Benzer şekilde, dallar ve NVA olmayan VNET 'ler, NVA bağlı bilgileri sanal WAN hub 'larına bağlı olmadığından, her NVA ana ağı hakkında bilgi vermez. Sonuç olarak, burada statik yollar da gerekecektir.

NVA ışınsal 'ler sanal WAN tarafından yönetilmediği hesaba katılarak, diğer tüm satırlar aynı bağlantı modelini gösterir. Sonuç olarak, tek bir yol tablosu (varsayılan bir) şunları yapacaktır:

* Sanal ağlar (hub olmayan VNET 'ler ve Kullanıcı-hub sanal ağları):
  * İlişkili yol tablosu: **varsayılan**
  * Yol tablolarına yayma: **varsayılan**
* Dallar
  * İlişkili yol tablosu: **varsayılan**
  * Yol tablolarına yayma: **varsayılan**

Ancak, bu senaryoda hangi statik yolların yapılandırılacağını düşünmemiz gerekir. Her bir statik yol iki bileşene, sanal WAN hub 'ında her bir bağlı bileşen için kullanılacak bağlantı olduğunu ve söz konusu bağlantının, NVA 'ya atanan somut IP adresini (veya birden çok NVA 'lar önünde bir yük dengeleyicisine işaret eden bir yük dengeleyiciye) işaret eden, **Şekil 1** ' i gösterdiği şekilde bir parçası olacaktır:

**Şekil 1**

:::image type="content" source="media/routing-scenarios/nva/nva-static-concept.png" alt-text="Şekil 1":::

Bu şekilde, NVA VNet 'in arkasındaki NVA ışınsal 'ler arasında trafik göndermek için varsayılan tabloda ihtiyaç duyduğumuz statik yollar aşağıdaki gibidir:

| Description | Yol tablosu | Statik yol              |
| ----------- | ----------- | ------------------------- |
| VNet 2       | Varsayılan     | 10.2.0.0/16-> eastusconn |
| VNet 4       | Varsayılan     | 10.4.0.0/16-> weconn     |

Artık sanal WAN, paketlerin gönderileceği bağlantıyı bilir, ancak bağlantının bu paketleri alırken ne yapılacağını bilmesi gerekir: bağlantı yolu tablolarının kullanıldığı yer. Burada, bu yolların NVA sanal ağları (VNet 2 ve VNet 4) tarafından içeri aktarılan yolların üzerinde tercih olduğundan emin olmak için daha kısa ön ekleri (daha uzun/16 yerine/24) kullanacağız:

| Description | Bağlantı | Statik yol            |
| ----------- | ---------- | ----------------------- |
| VNet 5       | eastusconn | 10.2.1.0/24-> 10.2.0.5 |
| VNet 6       | eastusconn | 10.2.2.0/24-> 10.2.0.5 |
| VNet 7       | weconn     | 10.4.1.0/24-> 10.4.0.5 |
| VNet 8       | weconn     | 10.4.2.0/24-> 10.4.0.5 |

Artık NVA sanal ağları, NVA olmayan VNET 'ler ve dallar tüm NVA 'ya nasıl ulaşabileceğinizi bilir. Sanal hub yönlendirmesi hakkında daha fazla bilgi için bkz. [sanal hub yönlendirmesi hakkında](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Mimari

**Şekil 2**' de iki hub vardır; **Hub1** ve **Hub2**.

* **Hub1** ve **Hub2** , NVA sanal ağları **VNET 2** ve **VNET 4**' e doğrudan bağlanır.

* VNET **5** ve **VNET 6** VNET **2** ile eşlenmez.

* VNET **7** ve VNET **8** , **VNET 4** ile eşlenmez.

* **Vnetme 5, 6, 7, 8** , bir sanal hub 'a doğrudan bağlı değil dolaylı ışınsal

**Şekil 2**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="Şekil 2" lightbox="./media/routing-scenarios/nva/nva.png":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Senaryo iş akışı

NVA aracılığıyla yönlendirmeyi ayarlamak için şunları göz önünde bulundurmanız gereken adımlar şunlardır:

1. NVA bağlı VNet bağlantısını tanımla. **Şekil 2**' de, **VNET 2 bağlantısı (Eastusconn)** ve **VNET 4 bağlantısı (weconn)**.

   UDRs 'nin ayarlanmış olduğundan emin olun:
   * VNet 5 ve VNet 6 ' dan VNet 2 NVA IP 'si
   * VNet 7 ve VNet 8 ' den VNet 4 NVA IP 'si 
   
   VNET 'leri 5, 6, 7, 8 ' e doğrudan sanal hub 'lara bağlamanız gerekmez. Vnetme 5, 6, 7, 8 ' deki NSG 'lerin şube (VPN/ER/P2S) veya uzak sanal ağlarına bağlı sanal ağlar için trafiğe izin verildiğinden emin olun. Örneğin, Vnetme 5, 6, NSG 'lerin şirket içi adres önekleri ve sanal ağlar 7, uzak hub 2 ' ye bağlı trafik için trafiğe izin vermemesini sağlamalıdır.

Sanal WAN, Vnettir 5, 6 sanal hub 'a bağlanıp VNet 2 NVA IP aracılığıyla iletişim kuran bir senaryoyu desteklemez; Bu nedenle, VI 5, 6-VNet2 ve benzer VNet 7, 8 ile VNet 4 arasında bağlantı kurmak gerekir.

2. Vnetme 2, 5, 6 ' dan hub 1 ' in varsayılan yol tablosuna toplanan bir statik yol girişi ekleyin.

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Örnek":::

3. VNet 2 ' in sanal ağ bağlantısı içinde Vnetme 5, 6 için statik bir yol yapılandırın. Bir sanal ağ bağlantısı için yönlendirme yapılandırmasını ayarlamak için bkz. [sanal hub yönlendirme](how-to-virtual-hub-routing.md#routing-configuration).

4. Vnetme 4, 7, 8 ' in hub 1 ' in varsayılan yol tablosuna bir toplu statik yol girişi ekleyin.

5. Merkez 2 ' nin varsayılan yol tablosu için 2, 3 ve 4. adımları yineleyin.

Bu, aşağıdaki **Şekil 3**' te gösterildiği gibi yönlendirme yapılandırması değişikliklerine neden olur.

**Şekil 3**

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Şekil 3" lightbox="./media/routing-scenarios/nva/nva-result.png":::

## <a name="next-steps"></a>Sonraki adımlar

* Sanal WAN hakkında daha fazla bilgi için bkz. [SSS](virtual-wan-faq.md).
* Sanal hub yönlendirmesi hakkında daha fazla bilgi için bkz. [sanal hub yönlendirmesi hakkında](about-virtual-hub-routing.md).
