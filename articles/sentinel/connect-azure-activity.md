---
title: Azure etkinlik verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Azure etkinlik verilerini Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 807a5ede3feee115b1a8dc51fe14966731fc7784
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240783"
---
# <a name="connect-data-from-azure-activity-log"></a>Azure etkinlik günlüğünden veri bağlama



Günlükleri [Azure etkinlik günlüğünden](../azure-monitor/platform/activity-logs-overview.md) Azure Sentinel 'e tek bir tıklama ile akışla aktarabilirsiniz. Etkinlik günlüğü, Azure 'da oluşan abonelik düzeyindeki olaylara ilişkin Öngörüler sağlayan bir abonelik günlüğü. Bu, Azure Resource Manager işlemsel verilerden hizmet durumu olaylarında güncelleştirmelere kadar olan bir veri aralığı içerir. Etkinlik günlüğünü kullanarak, aboneliğinizdeki kaynaklar üzerinde herhangi bir yazma işlemi (PUT, POST, SILME) için ' ne, kim ve ne zaman ' i belirleyebilirsiniz. Ayrıca, işlemi ve ilgili diğer özellikleri durumunu anlayabilirsiniz. Etkinlik günlüğü, klasik/"RDFE" modelini kullanan kaynaklara yönelik okuma (GET) işlemlerini veya işlemlerini içermez. 


## <a name="prerequisites"></a>Önkoşullar

- Genel yönetici veya Güvenlik Yöneticisi izinlerine sahip Kullanıcı


## <a name="connect-to-azure-activity-log"></a>Azure etkinlik günlüğüne bağlanma

1. Azure Sentinel 'de, **veri bağlayıcıları** ' nı seçin ve ardından **Azure etkinlik günlüğü** kutucuğuna tıklayın.

2. Azure etkinlik günlüğü bölmesinde, Azure Sentinel 'e göndermek istediğiniz abonelikleri seçin. 

3. **Bağlan**'a tıklayın.

4. Azure etkinlik uyarıları için Log Analytics ilgili şemayı kullanmak için, **AzureActivity**aratın.


 

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede Azure etkinlik günlüğü 'nü Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
