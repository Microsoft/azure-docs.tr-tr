---
title: Azure Izleyici kullanarak günlükleri SumoLogic 'e akış | Microsoft Docs
description: Azure Izleyici kullanarak Azure Active Directory günlüklerini SumoLogic ile tümleştirmeyi öğrenin
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdfc4e393ca7bf4bcbd523b4fad72690d5f2744
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014380"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Azure Izleyici kullanarak Azure Active Directory günlüklerini SumoLogic ile tümleştirme

Bu makalede, Azure Izleyici kullanarak Azure Active Directory (Azure AD) günlüklerini SumoLogic ile tümleştirmeyi öğreneceksiniz. Önce günlükleri bir Azure Olay Hub 'ına yönlendirdiğinizde Olay Hub 'ını SumoLogic ile tümleştirin.

## <a name="prerequisites"></a>Önkoşullar

Bu özelliği kullanmak için şunlara ihtiyacınız vardır:
* Azure AD etkinlik günlüklerini içeren bir Azure Olay Hub 'ı. [Etkinlik günlüklerinizi bir olay hub 'ına akışa](quickstart-azure-monitor-stream-logs-to-event-hub.md)alma hakkında bilgi edinin. 
* SumoLogic çoklu oturum açma etkin aboneliği.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Azure AD günlüklerini SumoLogic ile tümleştirme adımları 

1. İlk olarak [Azure AD günlüklerini bir Azure Olay Hub 'ına akışı](quickstart-azure-monitor-stream-logs-to-event-hub.md)yapın.
2. [Azure Active Directory günlüklerini toplamak](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory)için SumoLogic örneğinizi yapılandırın.
3. Ortamınızın gerçek zamanlı analizini sağlayan önceden yapılandırılmış panoları kullanmak için [Azure AD SumoLogic uygulamasını yükler](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) .

   ![Pano](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure İzleyici denetim günlükleri şemasını yorumlama](reference-azure-monitor-audit-log-schema.md)
* [Azure İzleyici oturum açma günlükleri şemasını yorumlama](reference-azure-monitor-sign-ins-log-schema.md)
* [Sık sorulan sorular ve bilinen sorunlar](concept-activity-logs-azure-monitor.md#frequently-asked-questions)