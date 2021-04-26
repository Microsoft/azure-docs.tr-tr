---
title: Cisco Birleşik Bilgi Işlem sistemi (UCS) verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Cisco UCS günlüklerini Azure Sentinel 'e çekmek için Cisco UCS veri bağlayıcısını nasıl kullanacağınızı öğrenin. Çalışma kitaplarında Cisco UCS verilerini görüntüleyin, uyarılar oluşturun ve araştırmayı geliştirebilirsiniz.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 15e31b8dc5ac6db5861e3ea0fb5390ddf0a8c945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100530680"
---
# <a name="connect-your-cisco-unified-computing-system-ucs-to-azure-sentinel"></a>Cisco Birleşik Bilgi Işlem sisteminizi (UCS) Azure Sentinel 'e bağlama

> [!IMPORTANT]
> Cisco UCS Bağlayıcısı Şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

Bu makalede Cisco Birleşik Bilgi Işlem sistemi (UCS) gerecinizin Azure Sentinel 'e nasıl bağlanacağı açıklanır. Cisco UCS veri Bağlayıcısı, çalışma kitaplarında verileri görüntüleyebilmeniz, özel uyarılar oluşturmak için onu kullanarak ve araştırmayı iyileştirebilmeniz için Azure Sentinel ile UCS günlüklerinizi kolayca bağlamanıza olanak tanır. Cisco UCS ve Azure Sentinel arasındaki tümleştirme Syslog kullanımını kullanır.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="prerequisites"></a>Önkoşullar

- Azure Sentinel çalışma alanında okuma ve yazma izninizin olması gerekir.

- Cisco UCS çözümünüz Syslog aracılığıyla günlükleri dışarı aktarmak için yapılandırılmalıdır.

## <a name="forward-cisco-ucs-logs-to-the-syslog-agent"></a>Cisco UCS günlüklerini Syslog aracısına ilet  

Syslog Aracısı aracılığıyla syslog iletilerini Azure Sentinel çalışma alanınıza iletmek için Cisco UCS 'ı yapılandırın.

1. Azure Sentinel gezinti menüsünde **veri bağlayıcıları**' nı seçin.

1. **Veri bağlayıcıları** Galerisi ' nden **Cisco UCS (Önizleme)** bağlayıcısını seçin ve ardından **bağlayıcı sayfası**' nı açın.

1. **CISCO UCS** bağlayıcı sayfasındaki yönergeleri izleyin:

    1. Linux için aracıyı yükleyip ekleme

        - Azure Linux VM veya Azure olmayan bir Linux makinesi (fiziksel veya sanal) seçin.

    1. Toplanacak günlükleri yapılandırın

        - Çalışma alanı aracıları yapılandırmasındaki tesisleri ve önem derecelerine seçin.

    1. Cisco UCS 'ı yapılandırma ve bağlama

        - Cisco UCS 'ı Syslog 'yi iletecek şekilde yapılandırmak için [Bu yönergeleri](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog) izleyin. Uzak sunucu için, Linux aracısını yüklediğiniz Linux makinenin IP adresini kullanın.

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, veriler Syslog altında Log Analytics görüntülenir.

Bazı yararlı örnek sorgular için bağlayıcı sayfasındaki **sonraki adımlar** sekmesine bakın.

## <a name="validate-connectivity"></a>Bağlantıyı doğrula

Günlüklerinizin Log Analytics görünmeye başlaması 20 dakikaya kadar sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede Cisco UCS 'ı Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .
