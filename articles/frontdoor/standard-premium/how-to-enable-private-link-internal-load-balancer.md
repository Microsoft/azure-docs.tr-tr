---
title: Azure ön kapısı Premium 'u özel bağlantıyla iç yük dengeleyici kaynağına bağlama
titleSuffix: Azure Private Link
description: Azure ön kapılarınızı bir iç yük dengeleyiciye bağlamayı öğrenin.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/16/2021
ms.author: tyao
ms.openlocfilehash: 6876692bcf752570ecdc5d42b65da81992ad3743
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803825"
---
# <a name="connect-azure-front-door-premium-to-an-internal-load-balancer-origin-with-private-link"></a>Azure ön kapısı Premium 'u özel bağlantıyla iç yük dengeleyici kaynağına bağlama

Bu makalede, Azure ön kapısının Azure özel bağlantı hizmeti kullanılarak iç yük dengeleyici kaynağınıza bağlanması için nasıl yapılandırılacağı açıklanır.

## <a name="prerequisites"></a>Önkoşullar

Özel bir [bağlantı hizmeti](../../private-link/create-private-link-service-portal.md)oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="enable-private-link-to-an-internal-load-balancer"></a>İç yük dengeleyiciye özel bağlantıyı etkinleştir
 
Bu bölümde, özel bağlantı hizmetini Azure ön kapısının özel ağında oluşturulan özel bir uç nokta ile eşlersiniz. 

1. Azure ön kapısı Premium profilinizde, *Ayarlar*' ın altında, **kaynak grupları**' nı seçin.

1. İç yük dengeleyici için özel bağlantıyı etkinleştirmek istediğiniz kaynak grubunu seçin.

1. İç yük dengeleyici kaynağı eklemek için **+ Kaynak Ekle** ' yi seçin.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-internal-load-balancer.png" alt-text="İç yük dengeleyiciye özel bağlantıyı etkinleştirme ekran görüntüsü.":::

1. **Azure kaynağı seç** için **dizinimde** öğesini seçin. Azure ön kapı Premium 'un özel olarak bağlanmasını istediğiniz siteyi yapılandırmak için aşağıdaki ayarları seçin veya girin.

    | Ayar | Değer |
    | ------- | ----- |
    | Region | Aynı veya kaynağınıza en yakın bölgeyi seçin. |
    | Kaynak türü | **Microsoft. Network/privateLinkServices** öğesini seçin. |
    | Kaynak | İç yük dengeleyiciye bağlı özel bağlantıyı seçin. |
    | Hedef alt kaynak | Boş bırakın. |
    | İstek iletisi | İletiyi özelleştirin veya varsayılanı seçin. |

1. Ardından **Ekle** ' yi ve ardından **Güncelleştir** ' i seçerek yapılandırmayı kaydedin.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Depolama hesabından özel uç nokta bağlantısını onayla

1. Özel bağlantı merkezine gidin ve **özel bağlantı Hizmetleri**' ni seçin. Ardından özel bağlantı adınızı seçin.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/list.png" alt-text="Özel bağlantı listesinin ekran görüntüsü.":::

1. *Ayarlar* altında **Özel uç nokta bağlantıları** ' nı seçin.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/overview.png" alt-text="Özel bağlantıya Genel Bakış sayfasının ekran görüntüsü.":::

1. Azure ön kapısı Premium 'dan *bekleyen* özel uç nokta isteği ' ni seçin ve **Onayla**' yı seçin.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-pending-approval.png" alt-text="Özel bağlantı için bekleyen onay ekran görüntüsü.":::

1. Onaylandığında, aşağıdaki ekran görüntüsü gibi görünmelidir. Bağlantının tam olarak kurması birkaç dakika sürer. Artık Azure ön kapısı Premium 'dan iç yük dengeleyiciye erişebilirsiniz.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Onaylanan özel bağlantı isteğinin ekran görüntüsü.":::

## <a name="next-steps"></a>Sonraki adımlar

[Özel bağlantı hizmeti](../../private-link/private-link-service-overview.md)hakkında bilgi edinin.
