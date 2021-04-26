---
title: VMware karbon siyah bulut uç noktası standart verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: VMware karbon siyah bulut uç noktası standart verilerini Azure Sentinel 'e bağlamayı öğrenin.
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
ms.openlocfilehash: 2c26931db617109c36c19b73bd3ec9263f4b40cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100096455"
---
# <a name="connect-your-vmware-carbon-black-cloud-endpoint-standard-to-azure-sentinel-with-azure-function"></a>Azure Işleviyle VMware karbon siyah bulut uç noktası standardını Azure Sentinel 'e bağlama

> [!IMPORTANT]
> Azure Sentinel 'de VMware karbon siyah bulut uç noktası standart veri Bağlayıcısı Şu anda genel önizlemededir.
> Bu özellik, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

VMware karbon siyah bulut uç noktası standart Bağlayıcısı, Azure Sentinel ile tüm [VMware karbon siyah uç nokta standart](https://www.carbonblack.com/products/endpoint-standard/) güvenlik çözüm günlüklerinizi kolayca bağlamanıza olanak tanır, panoları görüntüleyebilir, özel uyarılar oluşturabilir ve araştırmayı geliştirebilirsiniz. VMware karbon Black ve Azure Sentinel arasında tümleştirme, REST API kullanarak günlük verileri çekmek için Azure Işlevleri kullanımını sağlar.


> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="configure-and-connect-vmware-carbon-black-cloud-endpoint-standard"></a>VMware karbon siyah bulut uç noktası standardını yapılandırma ve bağlama

Azure Işlevleri, olayları ve günlükleri doğrudan VMware karbon siyah bulut uç nokta standardına tümleştirmenize ve çekebilir ve bunları Azure Sentinel 'e iletebilir.

1. Azure Sentinel portalında, **veri bağlayıcıları** ' na tıklayın ve **VMware karbon Black** Bağlayıcısı ' nı seçin.
2. **Bağlayıcı sayfasını aç**' ı seçin.
3. **VMware karbon Black** sayfasındaki yönergeleri izleyin.


## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, **CarbonBlackAuditLogs_CL**, **CarbonBlackNotifications_CL** ve * * * * CarbonBlackEvents_CL * * * * tablolarında Log Analytics veriler görüntülenir.

## <a name="validate-connectivity"></a>Bağlantıyı doğrula
Günlüklerinizin Log Analytics görünmeye başlaması 20 dakikaya kadar sürebilir. 


## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Azure Işlev uygulamalarını kullanarak VMware karbon siyah bulut uç noktası standardını Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .

