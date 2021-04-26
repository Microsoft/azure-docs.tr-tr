---
title: F5 ASM verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: F5 asm günlüklerini Azure Sentinel 'e çekmek için F5 ASM veri bağlayıcısını nasıl kullanacağınızı öğrenin. Çalışma kitaplarında F5 ASM verilerini görüntüleyin, uyarı oluşturun ve araştırmayı geliştirebilirsiniz.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/20/2020
ms.author: yelevin
ms.openlocfilehash: 81a0f0e01827c48518f2eb1fba062e9e6536d9df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655706"
---
# <a name="connect-f5-asm-to-azure-sentinel"></a>F5 ASM 'yi Azure Sentinel 'e bağlama

Bu makalede, F5 asm günlüklerinizi Azure Sentinel 'e kolayca çekmek için F5 ASM veri bağlayıcısının nasıl kullanılacağı açıklanmaktadır. Bu, çalışma kitaplarında F5 ASM verilerini görüntülemenize, özel uyarılar oluşturmak için bu verileri kullanmanıza ve araştırmayı iyileştirecek şekilde eklemenize olanak tanır. Azure Sentinel 'de F5 ASM verilerinin olması, kuruluşunuzun Web uygulaması güvenliğine daha fazla öngörü sağlar ve güvenlik işlemleri yeteneklerini geliştirir. 

## <a name="configure-your-f5-asm-to-send-cef-messages"></a>F5 ASM 'nizi CEF iletileri gönderecek şekilde yapılandırma

1. Aşağıdaki yönergeleri kullanarak, uzaktan günlüğe kaydetmeyi ayarlamak için [F5 'Teki uygulama güvenliği olay günlüğünü yapılandırma](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html) bölümündeki yönergeleri izleyin:
   - **Uzak depolama türünü** **CEF** olarak ayarlayın.
   - **Protokolü** **TCP** olarak ayarlayın.
   - **IP adresini** Syslog sunucusu IP adresi olarak ayarlayın.
   - **Bağlantı noktası numarasını** **514** olarak veya aracıyı kullanmak için ayarladığınız bağlantı noktasını ayarlayın.
   - **En büyük sorgu dizesi boyutunu** , aracısında ayarladığınız boyuta ayarlayabilirsiniz.

1. CEF olayları için Log Analytics ilgili şemayı kullanmak için arama yapın `CommonSecurityLog` .

1. 3. [Adım: bağlantıyı doğrulama adımına](connect-cef-verify.md)geçin.


## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, F5 ASM 'yi Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](./tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .