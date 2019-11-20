---
title: Office 365 verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Office 365 verilerini Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 481acfdccb54077f18c1f4bf0375c0dc66e66007
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240656"
---
# <a name="connect-data-from-office-365-logs"></a>Office 365 günlüklerinden veri bağlama



Denetim günlüklerini [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) ' den Azure Sentinel 'e tek bir tıklama ile akışla aktarabilirsiniz. Azure Sentinel 'de, birden çok kiracıdan gelen denetim günlüklerini tek bir çalışma alanına akışla aktarabilirsiniz. Office 365 etkinlik günlüğü Bağlayıcısı, devam eden kullanıcı etkinlikleri hakkında öngörüler sağlar. Office 365 ' den çeşitli Kullanıcı, yönetici, sistem ve ilke eylemleri ve olayları hakkında bilgi alacaksınız. Office 365 günlüklerini Azure Sentinel 'e bağlayarak panoları görüntülemek, özel uyarılar oluşturmak ve araştırma işleminizi geliştirmek için bu verileri kullanabilirsiniz.

> [!IMPORTANT]
> E3 lisansınız varsa, Office 365 yönetimi etkinlik API 'SI üzerinden verilere erişebilmek için önce Office 365 kuruluşunuz için Birleşik denetim günlüğünü etkinleştirmeniz gerekir. Bunu, Office 365 denetim günlüğünü açarak yapabilirsiniz. Yönergeler için bkz. [Office 365 denetim günlüğü aramasını açma veya kapatma](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Daha fazla bilgi için bkz. [Office 365 Yönetim ETKINLIĞI API başvurusu](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).

## <a name="prerequisites"></a>Önkoşullar

- Kiracınızda genel yönetici veya güvenlik yöneticisi olmanız gerekir
- Bağlantı oluşturmak için Azure Sentinel 'de oturum açtığınız bilgisayarınızda, bağlantı noktası 4433 ' nin Web trafiğine açık olduğundan emin olun. Bağlantı başarıyla yapıldıktan sonra bu bağlantı noktası yeniden kapatılabilir.
- Kiracınızda Office 365 E3 veya Office 365 E5 lisansı yoksa, bu işlemlerden birini kullanarak kiracınızda Birleşik denetimi etkinleştirmeniz gerekir:
    - [Set-AdminAuditLogConfig cmdlet 'Ini kullanarak](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) "UnifiedAuditLogIngestionEnabled" parametresini etkinleştirin.
    - [Veya güvenlik ve Uyumluluk Merkezi Kullanıcı arabirimini kullanma](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Office 365’e bağlanın

1. Azure Sentinel 'de, **veri bağlayıcıları** ' nı seçin ve ardından **Office 365** kutucuğuna tıklayın.

2. Henüz etkinleştirmediyseniz, **bağlantı** altında Office 365 çözümünü etkinleştirmek için **Etkinleştir** düğmesini kullanın. Zaten etkinse, bağlantı ekranında zaten etkinleştirilmiş olarak belirtilir.
1. Office 365, birden çok kiracıdan Azure Sentinel 'e veri akışı yapmanızı sağlar. Bağlanmak istediğiniz her kiracı için kiracıların **bağlantısını Azure Sentinel 'e**ekleyin. 
1. Active Directory bir ekran açılır. Azure Sentinel 'e bağlanmak istediğiniz her kiracı üzerinde bir genel yönetici kullanıcıyla kimlik doğrulaması yapmanız istenir ve günlüklerini okumak için Azure Sentinel 'e izinler sağlayabilirsiniz. 
5. Stream Office 365 etkinlik günlükleri altında, Azure Sentinel 'e hangi günlük türlerini aktarmak istediğinizi seçmek için **Seç** ' e tıklayın. Şu anda Azure Sentinel, Exchange ve SharePoint 'i destekliyor.

4. **Değişiklikleri Uygula**' ya tıklayın.

3. Office 365 günlükleri için Log Analytics ilgili şemayı kullanmak için **Officeactivity**' yi arayın.


## <a name="next-steps"></a>Sonraki adımlar
Bu belgede Office 365 ' i Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.

