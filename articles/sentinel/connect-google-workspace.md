---
title: Google Workspace (G Suite) verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Google çalışma alanı etkinlik olaylarını Azure Sentinel 'e almak için Google Workspace (G Suite) veri bağlayıcısını nasıl kullanacağınızı öğrenin. Çalışma kitaplarında Google çalışma alanı verilerini görüntüleyin, uyarılar oluşturun ve araştırmayı geliştirebilirsiniz.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 4ada570502d913283ba9ee4cc4c65b7bdd853935
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746285"
---
# <a name="connect-your-google-workspace-to-azure-sentinel"></a>Google çalışma alanınızı Azure Sentinel 'e bağlama

> [!IMPORTANT]
> Google çalışma alanı Bağlayıcısı Şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

[Google çalışma alanı (eskiden G Suite)](https://workspace.google.com/) veri Bağlayıcısı, Google çalışma alanı etkinlik olaylarını REST API aracılığıyla Azure Sentinel 'e alma olanağı sağlar. Bağlayıcı, olası güvenlik risklerini incelemenize, takımınızın işbirliği kullanımını çözümlemenize, yapılandırma sorunlarını tanılamanıza, yönetici etkinliğini analiz etmenizi, kullanıcıların içerik oluşturma ve paylaşma şeklini nasıl anlayacağınızı ve kuruluşunuzdaki diğer olayları incelemenizi sağlayan SOC ' de bu [olayların](https://developers.google.com/admin-sdk/reports/reference/rest/v1/activities) görünürlüğünü verir.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="prerequisites"></a>Önkoşullar

Google çalışma alanı verilerini toplamak için aşağıdaki izinlere ve kimlik bilgilerine sahip olmanız gerekir:

- Azure Sentinel çalışma alanınızda okuma ve yazma izinleri.

- Çalışma alanı için paylaşılan anahtarların okuma izinleri. [Çalışma alanı anahtarları hakkında daha fazla bilgi edinin](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- İşlev Uygulaması oluşturmak için Azure Işlevlerine yönelik okuma ve yazma izinleri. [Azure işlevleri hakkında daha fazla bilgi edinin](../azure-functions/index.yml).

- REST API için **GooglePickleString** kimlik bilgileri gereklidir. [Google REST API hakkında daha fazla bilgi edinin](https://developers.google.com/admin-sdk/reports/v1/reference/activities). [Kimlik bilgileri alma hakkında bilgi edinin](https://developers.google.com/admin-sdk/reports/v1/quickstart/python).

## <a name="configure-and-connect-google-workspace"></a>Google çalışma alanını yapılandırma ve bağlama

Google çalışma alanı, Azure İşlev Uygulaması kullanarak günlükleri doğrudan Azure Sentinel 'e tümleştirebilir ve dışarı aktarabilir.

> [!NOTE]
> Bu bağlayıcı, etkinlik olaylarını Azure Sentinel 'e çekmek üzere Google Reports API 'sine bağlanmak için Azure Işlevleri 'ni kullanır. Bu, ek veri alımı maliyetlerine neden olabilir. Ayrıntılar için [Azure işlevleri fiyatlandırma](https://azure.microsoft.com/pricing/details/functions/) sayfasına bakın.

1. Azure Sentinel portalında, **veri bağlayıcıları**' na tıklayın. 

1. Bağlayıcılar galerisinden **Google Workspace (G Suite) (Önizleme)** seçeneğini belirleyin ve **bağlayıcı sayfasını aç**' ı seçin.

1. Bağlayıcı sayfasının **yapılandırma** bölümünde açıklanan adımları izleyin.

## <a name="validate-connectivity-and-find-your-data"></a>Bağlantıyı doğrulayın ve verilerinizi bulun

Azure Sentinel 'de alınan verileri görebilene kadar 20 dakikaya kadar sürebilir.

Başarılı bir bağlantı kurulduktan sonra, veriler **günlüklerde**, **özel Günlükler** bölümünde aşağıdaki tablolarda görünür:
- `GWorkspace_ReportsAPI_admin_CL`
- `GWorkspace_ReportsAPI_calendar_CL`
- `GWorkspace_ReportsAPI_drive_CL`
- `GWorkspace_ReportsAPI_login_CL`
- `GWorkspace_ReportsAPI_mobile_CL`
- `GWorkspace_ReportsAPI_token_CL`
- `GWorkspace_ReportsAPI_user_accounts_CL`

Bu veri Bağlayıcısı, beklenen şekilde çalışması için bir kusto Işlevini temel alan bir ayrıştırıcıya bağımlıdır. **GWorkspace activityreports** kusto Functions diğer adını oluşturmak için [Bu adımları izleyin](https://aka.ms/sentinel-GWorkspaceReportsAPI-parser) .

Bazı yararlı örnek sorgular için bağlayıcı sayfasındaki **sonraki adımlar** sekmesine bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Google çalışma alanını Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .
