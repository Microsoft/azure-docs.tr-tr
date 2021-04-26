---
title: 'Azure ExpressRoute: Azure portal kullanarak devre eşayarlarını sıfırlayın'
description: Azure portal kullanarak bir Azure ExpressRoute bağlantı hattı eşayarlarını devre dışı bırakmayı ve etkinleştirmeyi öğrenin.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: 432ecedbbb8965926499380eb1165fdf43018426
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680272"
---
# <a name="reset-expressroute-circuit-peerings-by-using-the-azure-portal"></a>Azure portal kullanarak ExpressRoute devre eşayarlarını sıfırlayın

Bu makalede, Azure portal kullanarak bir Azure ExpressRoute bağlantı hattı eşayarlarını devre dışı bırakma ve etkinleştirme açıklanmaktadır. Bir eşlemeyi devre dışı bıraktığınızda, ExpressRoute devreniz için hem birincil hem de ikincil bağlantı için Sınır Ağ Geçidi Protokolü (BGP) oturumu kapatılır. Bir eşlemeyi etkinleştirdiğinizde, ExpressRoute bağlantı hattının hem birincil hem de ikincil bağlantısı üzerindeki BGP oturumu geri yüklenir.

> [!Note]
> ExpressRoute bağlantı hattınızı ilk kez yapılandırdığınızda, eşlemeler varsayılan olarak etkindir.

ExpressRoute eşlerinizin sıfırlanması aşağıdaki senaryolarda yararlı olabilir:

* Olağanüstü durum kurtarma tasarımınızı ve uygulamanızı test eteceğiz. Örneğin, iki ExpressRoute devreniz olduğunu varsayalım. Bir devrenin eşayarlarını devre dışı bırakabilir ve ağ trafiğinizi diğer devreyi kullanacak şekilde zorlayabilirsiniz.

* Azure özel eşleme veya Microsoft eşlemesi üzerinde çift yönlü Iletme algılamasını (BFD) etkinleştirmek istiyorsunuz. ExpressRoute bağlantı hattı 1 Ağustos 2018 ' den önce, Azure özel eşleme 'de veya 10 Ocak 2020 ' den önce oluşturulduysa, Microsoft eşlemesi 'nde BFD varsayılan olarak etkinleştirilmemiştir. BFD 'yi etkinleştirmek için eşlemeyi sıfırlayın.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Bir tarayıcıdan [Azure Portal](https://portal.azure.com)gidin ve Azure hesabınızla oturum açın.

## <a name="reset-a-peering"></a>Eşlemeyi sıfırlama

ExpressRoute devresinde Microsoft eşlemesini ve Azure özel eşlemesini bağımsız olarak sıfırlayabilirsiniz.

1. Değiştirmek istediğiniz devreyi seçin.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="ExpressRoute bağlantı hattı listesinde bir devre seçmeyi gösteren ekran görüntüsü.":::

1. Sıfırlamak istediğiniz eşleme yapılandırmasını seçin.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="ExpressRoute devresine genel bakış 'da eşleme seçmeyi gösteren ekran görüntüsü.":::

1. Eşlemeyi **Etkinleştir** onay kutusunun işaretini kaldırın ve ardından eşleme yapılandırmasını devre dışı bırakmak için **Kaydet** ' i seçin.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Eşlemeyi etkinleştir onay kutusunun işaretini temizlemeyi gösteren ekran görüntüsü.":::

1. Eşlemeyi **Etkinleştir** onay kutusunu seçin ve ardından eşleme yapılandırmasını yeniden etkinleştirmek için **Kaydet** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

ExpressRoute sorunlarını gidermek için aşağıdaki makalelere bakın:

* [ExpressRoute bağlantısını doğrulama](expressroute-troubleshooting-expressroute-overview.md)
* [Ağ performansı sorunlarını giderme](expressroute-troubleshooting-network-performance.md)
