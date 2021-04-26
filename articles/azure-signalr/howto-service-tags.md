---
title: Hizmet etiketlerini kullanma
titleSuffix: Azure SignalR Service
description: Azure SignalR hizmetinize giden trafiğe izin vermek için hizmet etiketlerini kullanma
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: aaa97c2cb062f30b1260ec7f80f85a3caccf932f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92152319"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Azure SignalR hizmeti için hizmet etiketleri kullanma

[Ağ güvenlik grubunu](../virtual-network/network-security-groups-overview.md#network-security-groups)yapılandırırken Azure SignalR hizmeti Için [hizmet etiketleri](../virtual-network/network-security-groups-overview.md#service-tags) kullanabilirsiniz. Azure SignalR hizmeti uç noktalarına giden ağ güvenliği kuralını, sabit kod IP adreslerine sahip olmadan tanımlamanızı sağlar.

Azure SignalR hizmeti bu hizmet etiketlerini yönetir. Kendi hizmet etiketinizi oluşturamaz veya var olan bir tane değiştiremezsiniz. Microsoft, hizmet etiketiyle eşleşen bu adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir.

## <a name="use-service-tag-on-portal"></a>Portalda hizmet etiketi kullan

Yeni bir giden ağ güvenlik kuralı ekleyerek Azure SignalR hizmetine giden trafiğe izin verebilirsiniz:

1. Ağ güvenlik grubuna gidin.

1. **Giden güvenlik kuralları** adlı ayarlar menüsüne tıklayın.

1. Üstteki düğme **+ Ekle** ' ye tıklayın.

1. **Hedef** altında **hizmet etiketi** seçin.

1. **Hedef hizmet etiketi** altında **AzureSignalR** öğesini seçin.

1. **Hedef bağlantı noktası aralıklarında** **443** ' i girin.

    ![Giden güvenlik kuralı oluşturma](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. Gereksinimlerinize göre diğer alanları ayarlayın.

1. **Ekle**'ye tıklayın.


## <a name="next-steps"></a>Sonraki adımlar

- [Ağ güvenlik grupları: hizmet etiketleri](../virtual-network/network-security-groups-overview.md#security-rules)