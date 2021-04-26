---
title: Sophos XG güvenlik duvarı verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Sophos XG güvenlik duvarı verilerini Azure Sentinel 'e bağlamayı öğrenin.
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
ms.openlocfilehash: 0b8b6247a735bceaf98029740bf9d4f7e233069d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097577"
---
# <a name="connect-your-sophos-xg-firewall-to-azure-sentinel"></a>Sophos XG güvenlik duvarınızı Azure Sentinel 'e bağlama

> [!IMPORTANT]
> Azure Sentinel 'deki Sophos XG güvenlik duvarı veri Bağlayıcısı Şu anda genel önizlemededir.
> Bu özellik, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bu makalede, [Sophos XG güvenlik duvarı](https://www.sophos.com/products/next-gen-firewall.aspx) gerecinizi Azure Sentinel 'e nasıl bağlayabileceğiniz açıklanır. Sophos XG güvenlik duvarı veri Bağlayıcısı, Azure Sentinel ile Sophos XG güvenlik duvarı günlüklerinizi kolayca bağlamanıza olanak tanır, panoları görüntüleyebilir, özel uyarılar oluşturabilir ve araştırmayı geliştirebilirsiniz. Sophos XG güvenlik duvarı ve Azure Sentinel arasındaki tümleştirme Syslog kullanımını kullanır.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="forward-sophos-xg-firewall-logs-to-the-syslog-agent"></a>Sophos XG güvenlik duvarı günlüklerini Syslog aracısına ilet  

Syslog iletilerini Syslog Aracısı aracılığıyla Azure çalışma alanınıza iletmek için Sophos XG güvenlik duvarını yapılandırın.

1. Azure Sentinel portalında, **veri bağlayıcıları** ' na tıklayın ve **Sophos XG güvenlik duvarı** Bağlayıcısı ' nı seçin.

1. **Bağlayıcı sayfasını aç**' ı seçin.

1. **Sophos XG güvenlik duvarı** sayfasındaki yönergeleri izleyin.

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, veriler Syslog altında Log Analytics görüntülenir.

## <a name="validate-connectivity"></a>Bağlantıyı doğrula

Günlüklerinizin Log Analytics görünmeye başlaması 20 dakikaya kadar sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Sophos XG güvenlik duvarını Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .