---
title: Juniper Networks SRX verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Juniper SRX günlüklerini Azure Sentinel 'e çekmek için Juniper SRX veri bağlayıcısını nasıl kullanacağınızı öğrenin. Çalışma kitaplarında Juniper SRX verilerini görüntüleyin, uyarılar oluşturun ve araştırmayı geliştirebilirsiniz.
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
ms.openlocfilehash: b10c47a31bf1be10c278d4d9e0dce633bc7bff6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100530646"
---
# <a name="connect-your-juniper-srx-firewall-to-azure-sentinel"></a>Juniper SRX güvenlik duvarınızı Azure Sentinel 'e bağlama

> [!IMPORTANT]
> Juniper SRX Bağlayıcısı Şu anda **önizlemededir**. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

Bu makalede Juniper SRX güvenlik duvarı gerecinizi Azure Sentinel 'e nasıl bağlayabileceğiniz açıklanır. Juniper SRX veri Bağlayıcısı, çalışma kitaplarında verileri görüntüleyebilmeniz, özel uyarılar oluşturmak için onu kullanarak ve araştırmayı iyileştirebilmeniz için, Azure Sentinel ile SRX günlüklerinizi kolayca bağlamanıza olanak tanır. Juniper SRX ve Azure Sentinel arasındaki tümleştirme Syslog kullanımını sağlar.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="prerequisites"></a>Önkoşullar

- Azure Sentinel çalışma alanında okuma ve yazma izninizin olması gerekir.

- Juniper SRX çözümünüz, günlükleri Syslog aracılığıyla dışarı aktarmak için yapılandırılmış olmalıdır.

## <a name="forward-juniper-srx-logs-to-the-syslog-agent"></a>Juniper SRX günlüklerini Syslog aracısına ilet  

Syslog iletilerini Syslog Aracısı aracılığıyla Azure Sentinel çalışma alanınıza iletmek için Juniper SRX 'i yapılandırın.

1. Azure Sentinel gezinti menüsünde **veri bağlayıcıları**' nı seçin.

1. **Veri bağlayıcıları** Galerisi ' nden **Juniper SRX (Önizleme)** bağlayıcısını seçin ve ardından **bağlayıcı sayfası**' nı açın.

1. **Juniper SRX** bağlayıcı sayfasındaki yönergeleri izleyin:

    1. Linux için aracıyı yükleyip ekleme

        - Azure Linux VM veya Azure olmayan bir Linux makinesi (fiziksel veya sanal) seçin.

    1. Toplanacak günlükleri yapılandırın

        - Çalışma alanı aracıları yapılandırmasındaki tesisleri ve önem derecelerine seçin.

    1. Juniper SRX 'i yapılandırma ve bağlama

        - Juniper SRX ' i, syslog öğesini iletecek şekilde yapılandırmak için bu yönergeleri izleyin.
            - [Trafik günlükleri (güvenlik ilkesi günlükleri)](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)
            - [Sistem günlükleri](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)
        - Uzak sunucu için, Linux aracısını yüklediğiniz Linux makinenin IP adresini kullanın.

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, veriler Syslog altında Log Analytics görüntülenir.

Bazı yararlı örnek sorgular için bağlayıcı sayfasındaki **sonraki adımlar** sekmesine bakın.

## <a name="validate-connectivity"></a>Bağlantıyı doğrula

Günlüklerinizin Log Analytics görünmeye başlaması 20 dakikaya kadar sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Juniper SRX ' i Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .
