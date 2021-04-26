---
title: Barbcuda CloudGen güvenlik duvarını Azure Sentinel 'e bağlama | Microsoft Docs
description: Barbcuda CloudGen güvenlik duvarını Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 9e0aa186e742318ab5793fa8390251d94327bf08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632716"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>Barracuda CloudGen Firewall'u bağlama

Barbcuda CloudGen güvenlik duvarı (CGFW) Bağlayıcısı, Barmcuda CGFW günlüklerinizi Azure Sentinel ile kolayca bağlamanıza, panoları görüntülemenize, özel uyarılar oluşturmanıza ve araştırmaya imkan tanır. Bu, kuruluşunuzun ağı hakkında daha ayrıntılı bilgi verir ve güvenlik işlemi yeteneklerini geliştirir.

## <a name="prerequisites"></a>Önkoşullar

- Azure Sentinel çalışma alanına yönelik okuma ve yazma izinleri.

- Barbcuda CloudGen güvenlik duvarının günlükleri Syslog aracılığıyla dışarı aktarmak için yapılandırılması gerekir.

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>Azure Sentinel 'e Barbcuda CloudGen güvenlik duvarı 'na bağlanma

1. Azure Portal **Azure Sentinel**  >  **veri bağlayıcıları** ' na gidin ve ardından **barraycuda cloudgen güvenlik duvarı** bağlayıcısını seçin.

1. **Bağlayıcı sayfasını aç**' ı seçin.

1. **Barrampada CloudGen güvenlik duvarı** sayfasındaki yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede Barbcuda CloudGen güvenlik duvarını Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .
