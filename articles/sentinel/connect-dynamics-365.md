---
title: Dynamics 365 günlüklerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Devam eden yönetici, Kullanıcı ve destek etkinlikleri hakkında bilgi almak için Dynamics 365 Common Data Service (CD) etkinlikleri bağlayıcısını kullanmayı öğrenin.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: 018368b6284cf39edec01f0a9a943b8ea15c85d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98104200"
---
# <a name="connect-dynamics-365-activity-logs-to-azure-sentinel"></a>Dynamics 365 etkinlik günlüklerini Azure Sentinel 'e bağlama

[Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description) Common Data SERVICE (CD) etkinlikleri Bağlayıcısı, yönetici, Kullanıcı ve destek etkinliklerinin yanı sıra Microsoft sosyal katılım günlüğü olayları hakkında öngörüler sağlar. Dynamics 365 CRM günlüklerini Azure Sentinel 'e bağlayarak, bu verileri çalışma kitaplarında görüntüleyebilir, özel uyarılar oluşturmak için kullanabilir ve araştırma işleminizi geliştirmek için kullanabilirsiniz. Bu yeni Azure Sentinel Bağlayıcısı, Office Yönetim API 'sinden Dynamics CD verilerini toplar. Power platform 'da denetlenen Dynamics CD etkinlikleri hakkında daha fazla bilgi edinmek için [etkinleştirme ve etkinlik günlüğünü kullanma](/power-platform/admin/enable-use-comprehensive-auditing)' yı ziyaret edin.

> [!IMPORTANT]
>
> Dynamics 365 Common Data Service (CD) etkinlikleri Bağlayıcısı Şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

## <a name="prerequisites"></a>Önkoşullar

- Azure Sentinel çalışma alanınızda okuma ve yazma izinlerine sahip olmanız gerekir.

- Bir [Microsoft Dynamics 365 üretim lisansına](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description)sahip olmanız gerekir. Bu bağlayıcı, korumalı alan ortamları için kullanılamaz.
    - Etkinlik günlüğü yapmak için bir Microsoft 365 Kurumsal [E3 veya E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) aboneliği gerekir.

- Office Yönetim API 'sinden veri çekmek için:
    - Kiracınızda genel yönetici olmanız gerekir.

    - Office [Denetim günlüğü](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) 'Nün [Office güvenlik ve Uyumluluk Merkezi](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance)'nde etkinleştirilmesi gerekir.

## <a name="enable-the-dynamics-365-activities-data-connector"></a>Dynamics 365 etkinlikleri veri bağlayıcısını etkinleştirme

1. Azure Sentinel gezinti menüsünde **veri bağlayıcıları**' nı seçin.

1. **Veri bağlayıcıları** galerisinden **Dynamics 365 (Önizleme)** öğesini seçin ve ardından Önizleme bölmesinde **bağlayıcı sayfasını aç** ' ı seçin.

1. **Yönergeler** sekmesinde, **yapılandırma** altında, **Bağlan**' a tıklayın. 

    Bağlayıcı etkinleştirildikten sonra, grafiğe ulaşan verileri görebilmeniz için 30 dakika sürer. 

    [Uyumluluk Merkezi 'Ndeki Office denetim günlüğüne](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#requirements-to-search-the-audit-log)göre, sonuçlara karşılık gelen denetim günlüğü kaydı için bir olay oluştuktan sonra 30 dakika veya 24 saate kadar sürebilir.

1. Microsoft Dynamics denetim günlükleri `Dynamics365Activity` tabloda bulunabilir. Tablonun [şema başvurusuna](/azure/azure-monitor/reference/tables/dynamics365activity)bakın.

## <a name="querying-the-data"></a>Veriyi sorgulama

1. Azure Sentinel gezinti menüsünde **Günlükler**' i seçin.

1. Günlüklerin ulaştığını doğrulamak için aşağıdaki sorguyu çalıştırın:

    ```kusto
    Dynamics365Activity
    | take 10
    ```


## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Dynamics 365 etkinlik verilerini Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Yerleşik](tutorial-detect-threats-built-in.md) veya [özel](tutorial-detect-threats-custom.md) kurallar kullanarak Azure Sentinel ile tehditleri algılamaya başlayın.
