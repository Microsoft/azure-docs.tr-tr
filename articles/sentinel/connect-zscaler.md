---
title: Zscaler verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Zscaler verilerini Azure Sentinel 'e bağlamayı öğrenin.
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
ms.openlocfilehash: d788219e36ac94b1d13b2f4819c3e546622ddff1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655264"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Zscaler Internet erişimini Azure Sentinel 'e bağlama

Bu makalede, Zscaler Internet erişim gerecinizi Azure Sentinel 'e nasıl bağlayabileceğiniz açıklanır. Zscaler veri Bağlayıcısı, Azure Sentinel ile Zscaler Internet erişimi (ZIA) günlüklerinizi kolayca bağlamanıza, panoları görüntülemenize, özel uyarılar oluşturmaya ve araştırmaya olanak tanır. Azure Sentinel 'de Zscaler 'ın kullanılması, kuruluşunuzun Internet kullanımı hakkında daha fazla öngörü sağlar ve güvenlik işlemi yeteneklerini geliştirir. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Zscaler 'nizi CEF iletileri gönderecek şekilde yapılandırma

1. Zscaler aracısında, gerecin gerekli günlükleri Log Analytics aracısına bağlı olarak Azure Sentinel Syslog aracısına göndermesi için bu değerleri ayarlamanız gerekir. Bu parametreleri Azure Sentinel aracısında Syslog arka plan programı 'nda da değiştirdiğiniz sürece, aracısında değiştirebilirsiniz.
    - Protokol = TCP
    - Bağlantı noktası = 514
    - Biçim = CEF
    - IP adresi-CEF iletilerini bu amaçla ayrıldığınızdan sanal makinenin IP adresine gönderdiğinizden emin olun.
 Daha fazla bilgi için bkz. [Zscaler ve Azure Sentinel dağıtım kılavuzu](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Bu çözüm Syslog RFC 3164 veya RFC 5424 ' ü destekler.


1. CEF olayları için Log Analytics ilgili şemayı kullanmak için, araması yapın `CommonSecurityLog` .
1. 3. [Adım: bağlantıyı doğrulama adımına](connect-cef-verify.md)geçin.


## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Zscaler Internet erişimini Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](./tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .