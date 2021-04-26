---
title: Cisco şemsiye 'ı Azure Sentinel 'e bağlama | Microsoft Docs
description: Azure Sentinel 'e şemsiye verileri çekmek için Cisco şemsiye veri bağlayıcısını nasıl kullanacağınızı öğrenin. Çalışma kitaplarında şemsiye verilerini görüntüleyin, uyarılar oluşturun ve araştırmayı yapın.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 22796134dae5c345e3f915e47bc1300affb9f60e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99566946"
---
# <a name="connect-your-cisco-umbrella-to-azure-sentinel"></a>Cisco şemsiye 'nizi Azure Sentinel 'e bağlama

> [!IMPORTANT]
> Cisco şemsiye Connector Şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

Cisco şemsiye Connector, tüm Cisco şemsiye güvenlik çözüm günlüklerinizi Azure Sentinel ile kolayca bağlamanıza olanak tanır, panoları görüntüleyebilir, özel uyarılar oluşturabilir ve araştırmayı geliştirebilirsiniz. Cisco şemsiye ve Azure Sentinel arasında tümleştirme, REST API kullanımını sağlar.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="configure-and-connect-cisco-umbrella"></a>Cisco şemsiye 'ı yapılandırma ve bağlama

Cisco şemsiye, günlükleri doğrudan Azure Sentinel 'e tümleştirebilir ve dışarı aktarabilir.

1. Azure Sentinel gezinti menüsünde **veri bağlayıcıları**' nı seçin.

1. **Veri bağlayıcıları** galerisinden **Cisco şemsiye (Önizleme)** öğesini seçin ve ardından **bağlayıcı sayfasını açın**.

1. Bağlayıcı sayfasının **yapılandırma** bölümünde açıklanan adımları izleyin.

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, veriler **günlüklerde**, **customlogs** bölümünün altında aşağıdaki tablolardan bir veya daha fazlasına görünür:
- `Cisco_Umbrella_dns_CL`
- `Cisco_Umbrella_proxy_CL`
- `Cisco_Umbrella_ip_CL`
- `Cisco_Umbrella_cloudfirewall_CL`

## <a name="validate-connectivity"></a>Bağlantıyı doğrula

Günlüklerinizin Log Analytics görünmeye başlaması 20 dakikaya kadar sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Cisco şemsiye verilerini Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .
