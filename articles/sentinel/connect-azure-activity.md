---
title: Azure etkinlik verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Azure etkinlik günlüklerini tek bir tıklama ile Azure Sentinel 'e akışla birleştirin. Etkinlik günlüğü, Azure genelinde abonelik düzeyindeki olayları kaydeder ve görüntüler.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 8f0bac363e020826e34df3cb0b4e3d3582990302
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595447"
---
# <a name="connect-data-from-azure-activity-log"></a>Azure etkinlik günlüğünden veri bağlama

Günlükleri [Azure etkinlik günlüğünden](../azure-monitor/essentials/platform-logs-overview.md) Azure Sentinel 'e tek bir tıklama ile akışla aktarabilirsiniz. Etkinlik günlüğü, Azure 'da abonelik düzeyindeki olayları kaydeden ve hizmet durumu olaylarında güncelleştirmelere Azure Resource Manager işlem verilerinden görüntüleyen bir abonelik günlüğleridir. Etkinlik günlüğünü kullanarak, aboneliğinizdeki kaynaklarda gerçekleştirilen herhangi bir yazma işlemi için (PUT, POST, SIL) ' ne, kim ve ne zaman ' i belirleyebilirsiniz. Ayrıca, işlemin durumunu ve ilgili diğer özellikleri de öğrenebilirsiniz. Etkinlik günlüğü, klasik/"RDFE" modelini kullanan kaynaklara yönelik okuma (GET) işlemlerini veya işlemlerini içermez. 

## <a name="prerequisites"></a>Önkoşullar

- Kullanıcı Log Analytics çalışma alanına katkıda bulunan izinlerine sahip olmalıdır.
- Kullanıcılarınızın Azure Sentinel 'e akışını sağlamak istediğiniz abonelikler için okuyucu izinlerine sahip olması gerekir.

## <a name="set-up-the-azure-activity-connector"></a>Azure etkinlik bağlayıcısını ayarlama

1. Azure Sentinel gezinti menüsünde **veri bağlayıcıları**' nı seçin. Bağlayıcılar listesinden **Azure etkinliği**' ne tıklayın ve ardından sağ alt köşedeki **bağlayıcı sayfası** ' nı açın.

2. **Yönergeler** sekmesinde, **Azure etkinlik günlüklerini Yapılandır >** bağlantısına tıklayın.

3. **Azure etkinlik günlüğü** bölmesinde, günlüklerini Azure Sentinel 'e aktarmak istediğiniz abonelikler ' i seçin. 

4. Sağ tarafta açılan abonelik bölmesinde, **Bağlan**' a tıklayın.

5. Azure etkinlik uyarıları için Log Analytics ilgili şemayı kullanmak için, `AzureActivity` sorgu penceresine yazın.

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede Azure etkinlik günlüğü 'nü Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Yerleşik](tutorial-detect-threats-built-in.md) veya [özel](tutorial-detect-threats-custom.md) kurallar kullanarak Azure Sentinel ile tehditleri algılamaya başlayın.
