---
title: Orca güvenlik uyarılarını Azure Sentinel 'e bağlama | Microsoft Docs
description: Orca güvenlik uyarısı verilerini Azure Sentinel 'e bağlamayı, panoları görüntülemeyi, özel uyarılar oluşturmayı ve araştırmayı geliştirmeyi öğrenin.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 18a50e3d06135f2c17b7e7c5b2969be33f408c62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100093089"
---
# <a name="connect-your-orca-security-alerts-to-azure-sentinel"></a>Orca güvenlik uyarılarınızı Azure Sentinel 'e bağlama 

> [!IMPORTANT]
> Azure Sentinel 'deki Orca güvenlik uyarıları Bağlayıcısı Şu anda genel önizlemededir.
> Bu özellik, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Orca güvenlik uyarıları Bağlayıcısı, [Orca uyarılarınızı](https://orca.security/) güvenlik çözüm uyarılarınızı Azure Sentinel 'e kolayca getirmenize olanak tanır, böylece bunları çalışma kitaplarında görüntüleyebilir, özel uyarılar oluşturmak için kullanabilir ve araştırmayı geliştirebilirsiniz. Orca güvenlik uyarıları ve Azure Sentinel arasında tümleştirme, REST API kullanımını sağlar.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="configure-and-connect-orca-security-alerts"></a>Orca güvenlik uyarılarını yapılandırma ve bağlama

Orca güvenlik uyarıları, günlükleri doğrudan Azure Sentinel 'e tümleştirebilir ve dışarı aktarabilir.

1. Azure Sentinel portalında, **veri bağlayıcıları** ' na tıklayın ve **Orca güvenlik uyarıları** ' nı seçin ve ardından **bağlayıcı sayfası**' nı açın.

2. https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integrationOrca platformundan tümleştirmeyi tamamen gerçekleştirmek için bölümüne bakın.

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, **OrcaAlerts_CL** tablosundaki **customlogs** altında Log Analytics veriler görüntülenir.
Orca uyarıları için Log Analytics ilgili şemayı kullanmak için arama yapın `OrcaAlerts_CL` .

## <a name="validate-connectivity"></a>Bağlantıyı doğrula
Günlüklerinizin Log Analytics görünmeye başlaması 20 dakikaya kadar sürebilir. 


## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Orca güvenlik uyarılarını Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .

