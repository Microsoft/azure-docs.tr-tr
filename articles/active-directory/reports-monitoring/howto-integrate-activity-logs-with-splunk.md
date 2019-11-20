---
title: Azure Izleyici 'yi kullanarak splunk 'ı tümleştirme | Microsoft Docs
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
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7da3a545847382f8fed192a6ec4fe2ac75bb8b35
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014402"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Nasıl yapılır: Azure Izleyici kullanarak splunk ile Azure Active Directory günlüklerini tümleştirme

Bu makalede, Azure Izleyici 'yi kullanarak Azure Active Directory (Azure AD) günlüklerini splunk ile tümleştirmeyi öğreneceksiniz. Önce günlükleri bir Azure Olay Hub 'ına yönlendirdiğinizde Olay Hub 'ını splunk ile tümleştirin.

## <a name="prerequisites"></a>Önkoşullar

Bu özelliği kullanmak için şunlara ihtiyacınız vardır:
* Azure AD etkinlik günlüklerini içeren bir Azure Olay Hub 'ı. [Etkinlik günlüklerinizi bir olay hub 'ına akışa](quickstart-azure-monitor-stream-logs-to-event-hub.md)alma hakkında bilgi edinin. 
* Splunk için Azure Izleyici eklentisi. [Splunk örneğinizi indirin ve yapılandırın](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="integrate-azure-active-directory-logs"></a>Azure Active Directory günlüklerini tümleştirme 

1. Splunk örneğinizi açın ve **veri özeti**' ni seçin.

    !["Veri özeti" düğmesi](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. **Sourcetypes** sekmesini seçin ve ardından **Amal: aadal: Audit** ' i seçin

    ![Veri özeti Sourcetypes sekmesi](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Azure AD etkinlik günlükleri aşağıdaki şekilde gösterilmiştir:

    ![Etkinlik günlükleri](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Splunk örneğiniz için bir eklenti yükleyemezseniz (örneğin, bir ara sunucu kullanıyorsanız veya splunk bulutu üzerinde çalıştırıyorsanız), bu olayları splunk HTTP olay toplayıcısına iletebilirsiniz. Bunu yapmak için, Olay Hub 'ında yeni iletiler tarafından tetiklenen bu [Azure işlevini](https://github.com/Microsoft/AzureFunctionforSplunkVS)kullanın. 
>

## <a name="next-steps"></a>Sonraki adımlar

* [Azure İzleyici denetim günlükleri şemasını yorumlama](reference-azure-monitor-audit-log-schema.md)
* [Azure İzleyici oturum açma günlükleri şemasını yorumlama](reference-azure-monitor-sign-ins-log-schema.md)
* [Sık sorulan sorular ve bilinen sorunlar](concept-activity-logs-azure-monitor.md#frequently-asked-questions)