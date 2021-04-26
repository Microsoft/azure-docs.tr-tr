---
title: Azure Active Directory izleme nedir? | Microsoft Belgeleri
description: Azure Active Directory izlemeye genel bir bakış sağlar.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 763e628183e5f6ad7b7bdbb8ee7ce6db572f44ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577787"
---
# <a name="what-is-azure-active-directory-monitoring"></a>Azure Active Directory izleme nedir?

Azure Active Directory (Azure AD) izleme özellikleriyle artık Azure AD etkinlik günlüklerinizi farklı uç noktalara yönlendirebilirsiniz. Ardından bu günlükleri uzun vadeli kullanım için saklayabilir veya ortamınızla ilgili içgörülere ulaşmak için üçüncü taraf Güvenlik Bilgileri ve Olay Yönetimi (SIEM) araçlarıyla tümleştirebilirsiniz.

Günlükleri şu an için şu hedeflere yönlendirebilirsiniz:

- Bir Azure depolama hesabı.
- Splunk ve Sumologic örneklerinizle tümleştirmek üzere bir Azure olay hub'ı.
- Verileri analiz edip belirli olaylar için pano ve uyarı oluşturabileceğiniz Azure Log Analytics çalışma alanı

**Önkoşul rolü**: genel yönetici

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Azure AD raporlama ve izleme için lisanslama ve Önkoşullar

Azure AD oturum açma günlüklerine erişmek için bir Azure AD Premium lisansına sahip olmanız gerekir.

[Azure Active Directory fiyatlandırma kılavuzunda](https://azure.microsoft.com/pricing/details/active-directory/)ayrıntılı özellik ve lisanslama bilgileri için.

Azure AD izleme ve raporlama dağıtmak için, Azure AD kiracısı için genel yönetici veya güvenlik yöneticisi olan bir kullanıcıya ihtiyacınız vardır.

Günlük verilerinizin son hedefine bağlı olarak, aşağıdakilerden birine sahip olmanız gerekir:

* ListKeys izinlerine sahip olduğunuz bir Azure depolama hesabı. Blob depolama hesabı değil genel bir depolama hesabı kullanmanızı öneririz. Depolamayla fiyatlandırma bilgileri için bkz. [Azure Depolama fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Üçüncü taraf SıEM çözümleriyle tümleştirilecek Azure Event Hubs ad alanı.

* Azure Izleyici günlüklerine Günlükler göndermek için bir Azure Log Analytics çalışma alanı.

## <a name="diagnostic-settings-configuration"></a>Tanılama ayarlarını yapılandırma

Azure AD etkinlik günlükleri izleme ayarlarını yapılandırmak için [Azure portalda](https://portal.azure.com) oturum açın ve **Azure Active Directory**'yi seçin. Buradan tanılama ayarları yapılandırma sayfasına erişmek için kullanabileceğiniz iki yöntem vardır:

* **İzleme** bölümünden **Tanılama ayarları**'nı seçin.

    ![Tanılama ayarları](./media/overview-monitoring/diagnostic-settings.png)
    
* **Denetim Günlükleri** veya **Oturum açma işlemleri**'ni ve ardından **Dışarı aktarma ayarları**'nı seçin. 

    ![Dışarı aktarma ayarları](./media/overview-monitoring/export-settings.png)


## <a name="route-logs-to-storage-account"></a>Günlükleri depolama hesabına yönlendirme

Günlükleri bir Azure depolama hesabına yönlendirerek [saklama ilkelerimizde](reference-reports-data-retention.md) belirtilen varsayılan saklama süresinden daha uzun bir süre boyunca saklayabilirsiniz. [Verileri depolama hesabınıza yönlendirmeyi](quickstart-azure-monitor-route-logs-to-storage-account.md) öğrenin.

## <a name="stream-logs-to-event-hub"></a>Günlükleri olay hub’ına aktarma

Günlükleri bir Azure olay hub'ına aktarmak, Sumologic ve Splunk gibi üçüncü taraf SIEM araçlarına tümleştirmenizi sağlar. Bu tümleştirme, ortamınızda daha zengin Öngörüler sağlamak için Azure AD etkinlik günlüğü verilerini SıEM 'niz tarafından yönetilen diğer verilerle birleştirmenizi sağlar. [Olay hub'ına günlük akışı yapmayı](tutorial-azure-monitor-stream-logs-to-event-hub.md) öğrenin.

## <a name="send-logs-to-azure-monitor-logs"></a>Günlükleri Azure Izleyici günlüklerine gönder

[Azure izleyici günlükleri](../../azure-monitor/logs/log-query-overview.md) , farklı kaynaklardan izleme verilerini birleştiren ve uygulamalarınızın ve kaynaklarınızın çalışması hakkında Öngörüler sunan bir sorgu dili ve analiz altyapısı sağlayan bir çözümdür. Azure AD etkinlik günlüklerini Azure Izleyici günlüklerine göndererek, toplanan verileri hızlıca alabilir, izleyebilir ve uyarabilir. [Azure izleyici günlüklerine veri gönderme](howto-integrate-activity-logs-with-log-analytics.md)hakkında bilgi edinin.

Oturum açma ve denetim etkinlikleri gibi yaygın senaryoları izlemek için önceden oluşturulmuş Azure AD etkinlik günlüklerini de görüntüleyebilirsiniz. [Azure ad etkinlik günlükleri için Log Analytics görünümlerini yüklemeyi ve kullanmayı](howto-install-use-log-analytics-views.md)öğrenin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure İzleyici'deki etkinlik günlükleri](concept-activity-logs-azure-monitor.md)
* [Günlükleri olay hub’ına aktarma](tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Günlükleri Azure Izleyici günlüklerine gönder](howto-integrate-activity-logs-with-log-analytics.md)
