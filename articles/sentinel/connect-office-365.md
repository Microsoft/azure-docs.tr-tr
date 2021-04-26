---
title: Office 365 günlüklerini Azure Sentinel 'e bağlama | Microsoft Docs
description: OneDrive dahil olmak üzere Exchange, takımlar ve SharePoint 'te devam eden Kullanıcı ve yönetici etkinlikleri hakkında bilgi almak için Office 365 günlük bağlayıcısını kullanmayı öğrenin.
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
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 05848eb2761284669e659b3875e96acdfa71f90f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632191"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Office 365 günlüklerini Azure Sentinel'e bağlama

[Office 365](/office/) log Connector, **Exchange** ve **SharePoint** 'te ( **OneDrive** dahil) ve artık **ekiplerde** bulunan devam eden Kullanıcı ve yönetici etkinliklerinde Azure Sentinel bilgilerini sunar. Bu bilgiler, dosya indirmeleri, gönderilen erişim istekleri, Grup olayları, posta kutusu işlemleri, takımlar olayları (sohbet, takım, üye ve kanal olayları gibi) gibi eylemlerin ayrıntılarını ve eylemleri gerçekleştiren kullanıcının ayrıntılarını içerir. Office 365 günlüklerini Azure Sentinel 'e bağlamak, bu verileri çalışma kitaplarınızda görüntülemenize ve çözümlemenize, özel uyarılar oluşturmak için sorgulamanıza ve araştırma işleminizi iyileştirebilmenizi sağlayarak Office 365 güvenliğine daha fazla öngörü sağlamanıza olanak sağlar.

## <a name="prerequisites"></a>Önkoşullar

- Azure Sentinel çalışma alanınızda okuma ve yazma izinlerine sahip olmanız gerekir.

- Kiracınızda genel yönetici veya güvenlik yöneticisi olmanız gerekir.

- Office 365 dağıtımınız, Azure Sentinel çalışma alanınız ile aynı kiracı üzerinde olmalıdır.

> [!IMPORTANT]
> - Bağlayıcının Office 365 yönetim etkinliği API 'SI üzerinden verilere erişebilmeleri için Office 365 dağıtımınızda **Birleşik denetim günlüğü** 'nün etkinleştirilmiş olması gerekir. Sahip olduğunuz Office 365/Microsoft 365 lisansının türüne bağlı olarak, varsayılan olarak etkinleştirilmemiş olabilir veya etkinleştirilmemiş olabilir. Lisans türüne göre Birleşik denetim günlüğünün durumunu denetlemek için [Office 365 güvenlik ve Uyumluluk Merkezi](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) 'ne başvurun.
> - Ayrıca, Office 365 Birleşik denetim günlüğü 'nün geçerli durumunu el ile etkinleştirebilir, devre dışı bırakabilir ve denetleyebilirsiniz. Yönergeler için bkz. [Office 365 denetim günlüğü aramasını açma veya kapatma](/office365/securitycompliance/turn-audit-log-search-on-or-off).
> - Daha fazla bilgi için bkz. [Office 365 Yönetim ETKINLIĞI API başvurusu](/office/office-365-management-api/office-365-management-activity-api-reference).


   > [!NOTE]
   > Yukarıda belirtildiği gibi, ve **veri türleri** altındaki bağlayıcı sayfasında gördüğünüz gibi, Azure Sentinel Office 365 Bağlayıcısı Şu anda yalnızca Microsoft Exchange ve SharePoint 'Ten (OneDrive dahil) **ve artık ekiplerden de bulunan** denetim günlüklerinin alımını desteklemektedir. Ancak, [diğer Office verilerini](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) Azure Sentinel 'e getirmek istiyorsanız bazı dış çözümler vardır. 

## <a name="enable-the-office-365-log-connector"></a>Office 365 günlük bağlayıcısını etkinleştirme

### <a name="instructions-tab"></a>Yönergeler sekmesi

1. Azure Sentinel gezinti menüsünde **veri bağlayıcıları**' nı seçin.

1. **Veri bağlayıcıları** galerisinden **Office 365**' i seçin ve ardından Önizleme bölmesinde **bağlayıcı sayfasını aç** ' ı seçin.

1. **Yapılandırma** etiketli bölüm altında, Azure Sentinel 'e bağlanmak istediğiniz Office 365 etkinlik günlüklerinin onay kutularını Işaretleyin ve **Değişiklikleri Uygula**' ya tıklayın. 

   > [!NOTE]
   > Daha önce birden fazla kiracıyı Azure Sentinel 'e bağladıysanız, bunu destekleyen Office 365 bağlayıcısının daha eski bir sürümünü kullanarak her bir kiracıdan topladığınız günlükleri görüntüleyebilir ve değiştirebilirsiniz. Ek kiracılar ekleyemeyeceksiniz, ancak daha önce eklenen kiracılar kaldırabilirsiniz.

### <a name="next-steps-tab"></a>Sonraki adımlar sekmesi

- SharePoint, OneDrive, Exchange ve takımlar günlük verilerinize ilişkin Öngörüler almak için **Office 365** günlük Bağlayıcısı ile birlikte paketlenmiş önerilen çalışma kitaplarına, sorgu örneklerine ve analiz kuralı şablonlarına bakın.

- **Günlüklerde** Office 365 günlük verilerini el ile sorgulamak için, `OfficeActivity` sorgu penceresinin ilk satırına girin.
   - Belirli bir günlük türüne yönelik sorguyu filtrelemek için, `| where OfficeWorkload == "<logtype>"` sorgunun ikinci satırına,, veya şeklinde bir *\<logtype\>* değer girin `SharePoint` `OneDrive` `Exchange` `MicrosoftTeams` .

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede Office 365 ' i Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Yerleşik](tutorial-detect-threats-built-in.md) veya [özel](tutorial-detect-threats-custom.md) kurallar kullanarak Azure Sentinel ile tehditleri algılamaya başlayın.