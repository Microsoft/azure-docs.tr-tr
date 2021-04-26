---
title: Fortinet verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Panoları görüntülemek, özel uyarılar oluşturmak ve araştırmayı geliştirmek için Fortinet gerecinizi Azure Sentinel 'e bağlayın.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 60be22f439547d006f54e489833b63171e617e3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92914002"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Fortinet 'ı Azure Sentinel 'e bağlama



Bu makalede, Fortinet gerecinizi Azure Sentinel 'e nasıl bağlayabileceğiniz açıklanır. Fortinet Data Connector, Azure Sentinel ile Fortinet logs 'larınızı kolayca bağlamanıza, panoları görüntülemenize, özel uyarılar oluşturmanıza ve araştırmaya olanak tanır. Azure Sentinel üzerinde Fortinet kullanmak, kuruluşunuzun Internet kullanımı hakkında daha fazla öngörü sağlar ve güvenlik işlemi yeteneklerini geliştirir. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Fortinet günlüklerini Syslog aracısına ilet

Fortinet Agent aracılığıyla syslog iletilerini CEF biçiminde Azure çalışma alanınıza iletmek için Fortinet 'ı yapılandırın.

1. Fortinet gerecinizde CLı 'yı açın ve aşağıdaki komutları çalıştırın:

    ```console
    config log syslogd setting
    set status enable
    set format cef
    set port 514
    set server <ip_address_of_Receiver>
    end
    ```

    - Sunucu **IP adresini** aracının IP adresiyle değiştirin.
    - **Syslog bağlantı noktasını** **514** veya aracıda ayarlanan bağlantı noktası olarak ayarlayın.
    - Erken FortiOS sürümlerinde CEF biçimini etkinleştirmek için, **CSV 'yi devre dışı bırak** komut kümesini çalıştırmanız gerekebilir.
 
   > [!NOTE] 
   > Daha fazla bilgi için, [Fortinet belge kitaplığına](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)gidin. Sürümünüzü seçin ve **el kitabı** ve **log ileti başvurusunu** kullanın.

1. Azure Izleyici 'de ilgili şemayı, Fortinet olayları için Log Analytics kullanmak için arama yapın `CommonSecurityLog` .

1. 3. [Adım: bağlantıyı doğrulama adımına](connect-cef-verify.md)geçin.


## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, Fortinet gereçlerini Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .


