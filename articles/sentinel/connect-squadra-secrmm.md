---
title: Squadra Technologies secRMM verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Squadra Technologies secRMM verilerini Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: a00b4b1e81c0d644cf1475aa46dda3848fda1365
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632910"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Squadra Technologies secRMM verilerinizi Azure Sentinel 'e bağlama 

Squadra Technologies secRMM Bağlayıcısı, Squadra Technologies secRMM güvenlik çözüm günlüklerinizi Azure Sentinel ile kolayca bağlamanıza olanak tanır. Panoları görüntülemenizi, özel uyarılar oluşturmanızı ve araştırmanızı sağlar. Bu bağlayıcı, USB çıkarılabilir depolama olayları hakkında öngörüler sağlar. Squadra Technologies secRMM ve Azure Sentinel arasındaki tümleştirme REST API kullanır.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Squadra Technologies secRMM 'yi yapılandırma ve bağlama 

Squadra Technologies secRMM, günlükleri doğrudan Azure Sentinel 'e tümleştirebilir ve dışarı aktarabilir.
1. Azure Sentinel portalında, veri bağlayıcıları ' na tıklayın ve Squadra Technologies secRMM ' yi seçin ve ardından bağlayıcı sayfası ' nı açın.

2. Azure Sentinel 'de Squadra secRMM verileri almak için [Azure Sentinel Için Squadra teknolojileri ekleme Kılavuzu](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) 'nda açıklanan adımları izleyin.   

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, veriler, tablodaki **customlogs** bölümünün altında **Günlükler**' de görünür `secRMM_CL` .

Squadra Technologies secRMM günlüklerini sorgulamak için, sorgu penceresinin üst kısmına tablo adını girin.

## <a name="validate-connectivity"></a>Bağlantıyı doğrula

Günlüklerinizin Log Analytics görünmeye başlaması 20 dakikaya kadar sürebilir. 

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Squadra Technologies secRMM 'yi Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .
