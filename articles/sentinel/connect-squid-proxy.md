---
title: SQUID Proxy verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: SQUID Proxy veri bağlayıcısını kullanarak Azure Sentinel 'e SQUID proxy günlüklerini çekme hakkında bilgi edinin. Çalışma kitaplarında SQUID Proxy verilerini görüntüleyin, uyarılar oluşturun ve araştırmayı geliştirebilirsiniz.
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
ms.openlocfilehash: eec88bf85f1b7a2ec8db2bf23c43629d84cc5106
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100090454"
---
# <a name="connect-your-squid-proxy-to-azure-sentinel"></a>SQUID ara sunucusunu Azure Sentinel 'e bağlama

> [!IMPORTANT]
> SQUID Proxy Bağlayıcısı Şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

Bu makalede, SQUID Proxy gerecinizi Azure Sentinel 'e bağlama açıklanmaktadır. SQUID Proxy Data Connector, SQUID günlüklerinizi kolayca Azure Sentinel 'e bağlamanıza olanak tanır. böylece, verileri çalışma kitaplarında görüntüleyebilir, özel uyarılar oluşturmak için kullanabilir ve araştırmayı geliştirebilirsiniz. SQUID Proxy ile Azure Sentinel arasındaki tümleştirme, Log Analytics Aracısı tarafından yerel dosya işlemeyi kullanır.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="prerequisites"></a>Önkoşullar

- Azure Sentinel çalışma alanında okuma ve yazma izninizin olması gerekir.

## <a name="forward-squid-proxy-logs-to-the-log-analytics-agent"></a>SQUID proxy günlüklerini Log Analytics aracısına ilet  

SQUID Proxy 'yi, Log Analytics Aracısı aracılığıyla Azure çalışma alanınıza günlük dosyaları gönderecek şekilde yapılandırın.

1. Azure Sentinel gezinti menüsünde **veri bağlayıcıları**' nı seçin.

1. **Veri bağlayıcıları** galerisinden, **SQUID Proxy (Önizleme)** bağlayıcısını seçin ve ardından **bağlayıcı sayfası**' nı açın.

1. **SQUID Proxy** Bağlayıcısı sayfasındaki yönergeleri izleyin:

    1. Linux için aracıyı yükleyip ekleme

        - Azure Linux VM veya Azure olmayan bir Linux makinesi (fiziksel veya sanal) seçin.

    1. Toplanacak günlükleri yapılandırın

        - Çalışma alanı Gelişmiş ayarları ' nda özel bir günlük türü ekleyin, örnek bir dosyayı karşıya yükleyin ve yönlendirilmiş olarak yapılandırın.

## <a name="find-your-data"></a>Verilerinizi bulun

Başarılı bir bağlantı kurulduktan sonra, veriler, tablodaki **özel Günlükler** altında **Günlükler**' de görünür `SquidProxy_CL` .

Bazı yararlı örnek sorgular için bağlayıcı sayfasındaki **sonraki adımlar** sekmesine bakın.

## <a name="validate-connectivity"></a>Bağlantıyı doğrula

Günlüklerinizin Log Analytics görünmeye başlaması 20 dakikaya kadar sürebilir. 

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, SQUID Proxy 'Sini Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .
