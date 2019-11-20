---
title: Öğretici-Azure Olay Hub 'ına Günlükler akışı | Microsoft Docs
description: Azure Active Directory günlüklerini bir olay hub 'ına göndermek için Azure Tanılama ayarlamayı öğrenin
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: eba44252672248b983d7f6e0c843f638e5f73447
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007648"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub"></a>Öğretici: Azure Olay Hub 'ına akış Azure Active Directory günlükleri

Bu öğreticide, Azure Olay Hub 'ına Azure Active Directory (Azure AD) günlüklerini akışa almak için Azure Izleyici tanılama ayarlarını ayarlamayı öğreneceksiniz. Bu mekanizmayı kullanarak günlüklerinizi Splunk ve QRadar gibi üçüncü taraf Güvenlik Bilgileri ve Olay Yönetimi (SIEM) araçlarıyla tümleştirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar 

Bu özelliği kullanmak için şunlara ihtiyacınız vardır:

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz denemeye kaydolabilirsiniz](https://azure.microsoft.com/free/).
* Azure AD kiracısı.
* Azure AD kiracısında *genel yönetici* veya *güvenlik yöneticisi* olan bir kullanıcı.
* Azure aboneliğinizde bir Event Hubs ad alanı ve bir olay hub'ı. [Olay hub'ı oluşturma](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) hakkında bilgi edinin.

## <a name="stream-logs-to-an-event-hub"></a>Günlükleri bir olay hub 'ına akış

1. [Azure portalında](https://portal.azure.com) oturum açın. 

2. **Denetim günlüklerini** > **Azure Active Directory** > **izleme** ' yi seçin. 

3. **Dışarı Aktarma Ayarları**'nı seçin.  
    
4. **Tanılama ayarları** bölmesinde aşağıdakilerden birini yapın:
    * Var olan ayarları değiştirmek için **Ayarı düzenleyin**'i seçin.
    * Yeni ayar eklemek için **Tanılama ayarı ekle**'yi seçin.  
      En fazla üç ayar kullanabilirsiniz.

      ![Dışarı aktarma ayarları](./media/quickstart-azure-monitor-stream-logs-to-event-hub/ExportSettings.png)

5. **Bir olay hub'ına akış yap** onay kutusunu ve ardından **Event Hubs/Yapılandır**'ı seçin.

6. Günlükleri yönlendirmek istediğiniz Azure aboneliğini ve Event Hubs ad alanını seçin.  
    Aboneliğin ve Event Hubs ad alanının günlüklerin akışının yapıldığı Azure AD kiracısı ile ilişkilendirilmiş olması gerekir. Event Hubs alanında günlüklerin gönderilmesini istediğiniz olay hub'ını da belirtebilirsiniz. Olay hub'ı belirtilmezse ad alanında **insights-logs-audit** varsayılan adıyla yeni bir olay hub'ı oluşturulur.

7. Olay hub'ı yapılandırmasını kapatmak için **Tamam**'ı seçin.

8. Aşağıdakilerden birini veya ikisini birden yapın:
    * Denetim günlüklerini depolama hesabına göndermek için **AuditLogs** onay kutusunu işaretleyin. 
    * Oturum açma günlüklerini depolama hesabına göndermek için **SignInLogs** onay kutusunu işaretleyin.

9. Ayarları kaydetmek için **Kaydet**’i seçin.

    ![Tanılama ayarları](./media/quickstart-azure-monitor-stream-logs-to-event-hub/DiagnosticSettings.png)

10. Yaklaşık 15 dakika sonra olayların olay hub'ınızda görüntülenip görüntülenmediğini kontrol edin. Bunu yapmak için portaldan olay hub'ına gidin ve **gelen iletiler** değerinin sıfırdan büyük olduğundan emin olun. 

    ![Denetim günlükleri](./media/quickstart-azure-monitor-stream-logs-to-event-hub/InsightsLogsAudit.png)

## <a name="access-data-from-your-event-hub"></a>Verilere olay hub'ınızdan erişme

Olay hub'ında görüntülenen verilere iki farklı şekilde erişebilir ve onları okuyabilirsiniz:

* **Desteklenen bir SIEM aracını yapılandırın**. Çoğu araç olay hub'ındaki verileri okumak için olay hub'ı bağlantı dizesine ve Azure aboneliğinizde belirli izinlere ihtiyaç duyar. Azure İzleyici tümleştirmesine sahip üçüncü taraf araçlarının bazıları şunlardır:
    
    * **ARCTIS**: Azure AD günlüklerini splunk ile tümleştirme hakkında daha fazla bilgi için bkz. [Azure izleyici kullanarak arctımla Azure Active Directory günlüklerini tümleştirme](howto-integrate-activity-logs-with-arcsight.md).
    
    * **Splunk**: Azure AD günlüklerini Splunk ile tümleştirme hakkında daha fazla bilgi için bkz. [Azure İzleyici'yi kullanarak Azure AD günlüklerini Splunk ile tümleştirme](tutorial-integrate-activity-logs-with-splunk.md).
    
    * **IBM QRadar**: DSM ve Azure Olay Hub'ı Protokolünü [IBM destek](https://www.ibm.com/support) sayfasından indirebilirsiniz. Azure tümleştirmesi hakkında daha fazla bilgi için [IBM QRadar Security Intelligence Platform 7.3.0](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0) sitesini ziyaret edin.
    
    * **Sumo Logic**: Sumo Logic'i bir olay hub'ındaki verileri kullanacak şekilde ayarlamak için bkz. [Azure AD uygulamasını yükleme ve panoları görüntüleme](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards). 

* **Özel araçları ayarlama**. Geçerli SIEM çözümünüz henüz Azure İzleyici tanılamaları için desteklenmiyorsa Event Hubs API'lerini kullanarak özel aracınızı ayarlayabilirsiniz. Daha fazla bilgi için bkz. [Bir olay hub'ındaki verileri almaya başlama](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph).


## <a name="next-steps"></a>Sonraki adımlar

* [Azure Izleyici kullanarak Azure Active Directory günlüklerini Arcizle tümleştirin](howto-integrate-activity-logs-with-arcsight.md)
* [Azure İzleyici kullanarak Azure AD günlüklerini Splunk ile tümleştirme](tutorial-integrate-activity-logs-with-splunk.md)
* [Azure İzleyici kullanarak Azure AD günlüklerini SumoLogic ile tümleştirme](howto-integrate-activity-logs-with-sumologic.md)
* [Azure İzleyici denetim günlükleri şemasını yorumlama](reference-azure-monitor-audit-log-schema.md)
* [Azure İzleyici oturum açma günlükleri şemasını yorumlama](reference-azure-monitor-sign-ins-log-schema.md)
