---
title: 'Senaryo: sanal ağlar için özel yalıtım'
titleSuffix: Azure Virtual WAN
description: Yönlendirme senaryoları-seçili VNET 'lerin birbirlerine ulaşabilmesi önlenir
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ca1ee8418bc08d70a031d81a15dc1b4ace2f1a3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92461830"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Senaryo: sanal ağlar için özel yalıtım

Sanal WAN sanal hub 'ı yönlendirme ile çalışırken, kullanılabilecek oldukça az sayıda senaryo vardır. Sanal ağlar için özel bir yalıtım senaryosunda, amaç belirli bir sanal ağ kümesinin diğer belirli sanal ağlar kümesine ulaşabilme zorunluluğunu önlemektir. Ancak, tüm dallara ulaşmak için sanal ağlar gereklidir (VPN/ER/Kullanıcı VPN). Sanal hub yönlendirmesi hakkında daha fazla bilgi için bkz. [sanal hub yönlendirmesi hakkında](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Tasarım

Kaç tane yol tablosunun gerekli olacağını anlamak için bir bağlantı matrisi oluşturabilirsiniz. Bu senaryo için, her hücrenin bir kaynağın (satır) bir hedefle (sütun) iletişim kurup kuramayacağını temsil ettiği aşağıdaki gibi görünür:

| Kaynak | Hedef:| *Mavi VNET 'ler* | *Kırmızı VNET 'ler* | *Dallar*|
|---|---|---|---|---|
| **Mavi VNET 'ler** |   &#8594;|   Direct     |           |  Direct |
| **Kırmızı VNET 'ler**  |   &#8594;|              |   Direct  |  Direct |
| **Dallar**   |   &#8594;|   Direct     |   Direct  |  Direct |

Önceki tablodaki hücrelerin her biri, bir sanal WAN bağlantısının (akışın "Kimden" tarafı, satır başlıkları) bir hedefle (akışın "to" tarafı, italik olan sütun üst bilgileri) iletişim kuracağını açıklar. Bu senaryoda, güvenlik duvarı veya ağ sanal gereçleri yoktur, bu nedenle iletişimler doğrudan sanal WAN üzerinden akar (Bu nedenle tablodaki "doğrudan" sözcüğü).

Farklı satır desenlerinin sayısı, bu senaryoda ihtiyacımız olacak yol tablolarının sayısı olacaktır. Bu durumda, sanal ağlar için **RT_BLUE** ve **RT_RED** çağıracağız üç yol yolu tablosu ve dallar için **varsayılan** . Dalların her zaman varsayılan yönlendirme tablosuyla ilişkilendirilmesi gerektiğini unutmayın.

Dalların hem kırmızı hem de mavi VNET 'lerden gelen öneklerini öğrenmeleri gerekir. bu nedenle, tüm VNET 'lerin varsayılana yayılması gerekir ( **RT_BLUE** veya **RT_RED** ek olarak). Dallar ön eklerini öğrenmeleri için mavi ve kırmızı VNET 'ler, **RT_BLUE** ve **RT_RED** her iki yol tablosuna yayılır. Sonuç olarak, bu son tasarımdır:

* Mavi sanal ağlar:
  * İlişkili yol tablosu: **RT_BLUE**
  * Yol tablolarına yayma: **RT_BLUE** ve **varsayılan**
* Kırmızı sanal ağlar:
  * İlişkili yol tablosu: **RT_RED**
  * Yol tablolarına yayma: **RT_RED** ve **varsayılan**
* Dallar
  * İlişkili yol tablosu: **varsayılan**
  * Yol tablolarına yayma: **RT_BLUE**, **RT_RED** ve **varsayılan**

> [!NOTE]
> Tüm dalların varsayılan yol tablosuyla ilişkilendirilmesi ve aynı yönlendirme tabloları kümesine yayılması gerektiğinden, tüm dallar aynı bağlantı profiline sahip olur. Diğer bir deyişle, sanal ağlar için kırmızı/mavi kavramı dallara uygulanamaz.

> [!NOTE]
> Sanal WAN 'ınız birden çok bölgeye dağıtılmışsa, her hub 'da **RT_BLUE** ve **RT_RED** Route tabloları oluşturmanız ve her VNET bağlantısının, yayma etiketleri kullanılarak her sanal hub 'daki yol tablolarına yayılması gerekir.

Sanal hub yönlendirmesi hakkında daha fazla bilgi için bkz. [sanal hub yönlendirmesi hakkında](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="architecture"></a>İş akışı

**Şekil 1**' de mavi ve Red VNET bağlantıları vardır.

* Mavi bağlantılı VNET 'ler birbirine ulaşabilir ve tüm dallar (VPN/ER/P2S) bağlantılarına ulaşabilirler.
* Kırmızı VNET 'ler birbirine ulaşabilir ve tüm dallar (VPN/ER/P2S) bağlantılarına ulaşabilirler.

Yönlendirmeyi ayarlarken aşağıdaki adımları göz önünde bulundurun.

1. Azure portal, **RT_BLUE** ve **RT_RED** iki özel yol tablosu oluşturun.
2. Yol tablosu için **RT_BLUE**, aşağıdaki ayarlar için:
   * **İlişkilendirme**: tüm mavi VNET 'leri seçin.
   * **Yayma**: dallar için, dalların seçeneğini belirleyin, ımpber DALı (VPN/er/P2S) bağlantıları bu yol tablosuna yolları yayar.
3. Kırmızı sanal ağlar ve dallar (VPN/ER/P2S) için **RT_RED** yol tablosu için aynı adımları yineleyin.

Bu, aşağıdaki şekilde görüldüğü gibi yönlendirme yapılandırması değişikliklerine neden olur

**Şekil 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="Şekil 1":::

## <a name="next-steps"></a>Sonraki adımlar

* Sanal WAN hakkında daha fazla bilgi için bkz. [SSS](virtual-wan-faq.md).
* Sanal hub yönlendirmesi hakkında daha fazla bilgi için bkz. [sanal hub yönlendirmesi hakkında](about-virtual-hub-routing.md).
