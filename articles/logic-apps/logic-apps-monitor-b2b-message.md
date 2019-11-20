---
title: Azure Izleyici ile B2B iletilerini izleme-Azure Logic Apps
description: Azure Izleyici 'yi kullanarak Azure Logic Apps AS2, x12 ve EDIOLGU iletileri için tanılama günlüğü ayarlama
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: e5c8e5f3d42f4e85406fcc7dd5a2f6602045c8ed
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680197"
---
# <a name="set-up-diagnostics-logging-for-b2b-messages-in-azure-logic-apps-by-using-azure-monitor"></a>Azure Izleyici 'yi kullanarak Azure Logic Apps B2B iletileri için tanılama günlüğü ayarlama

Tümleştirme hesabınızdaki ticari iş ortakları arasında B2B iletişimini ayarladıktan sonra, bu iş ortakları birbirleriyle ileti değiş tokuş edebilir. Bu iletişimin istediğiniz şekilde çalışıp çalışmadığını denetlemek için, AS2, x12 ve EDIOLGU iletilerini izleyebilir ve [Azure izleyici günlükleri](../log-analytics/log-analytics-overview.md)ile tümleştirme hesabınız için tanılama günlüğü ayarlayabilirsiniz. Bu hizmet bulutunuzu ve şirket içi ortamlarınızı izler, kullanılabilirlik ve performanslarını korumanıza yardımcı olur ve daha zengin hata ayıklama için çalışma zamanı ayrıntılarını ve olayları toplar. Bu verileri Azure depolama ve Azure Event Hubs gibi diğer hizmetlerle de kullanabilirsiniz.

> [!NOTE]
> Bu sayfada, [2019 Ocak 'ta devre dışı](../azure-monitor/platform/oms-portal-transition.md)bırakılmakta olan MICROSOFT OPERATIONS Management SUITE (OMS) başvuruları olabilir, ancak bu adımlar mümkün olduğunca Azure Log Analytics ile değiştirilir. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Önkoşullar

* Tanılama günlük kaydı ile ayarlanan mantıksal uygulama. [Mantıksal uygulama oluşturmayı](quickstart-create-first-logic-app-workflow.md) ve [Bu mantıksal uygulama için günlüğü ayarlamayı](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)öğrenin.

* Önceki gereksinimleri karşıladıktan sonra, Azure Izleyici günlükleri aracılığıyla B2B iletişimini izlemek ve izlemek için kullandığınız bir Log Analytics çalışma alanına da ihtiyacınız vardır. Log Analytics çalışma alanınız yoksa, [Log Analytics çalışma alanı oluşturmayı](../azure-monitor/learn/quick-create-workspace.md)öğrenin.

* Mantıksal uygulamanıza bağlı bir tümleştirme hesabı. [Mantıksal uygulamanıza yönelik bir bağlantıyla tümleştirme hesabı oluşturmayı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)öğrenin.

## <a name="turn-on-diagnostics-logging"></a>Tanılama günlüğünü aç

Doğrudan tümleştirme hesabınızdan veya [Azure İzleyici hizmeti aracılığıyla](#azure-monitor-service)günlüğe kaydetmeyi açabilirsiniz. Azure Izleyici, altyapı düzeyindeki verilerle temel izleme sağlar. [Azure izleyici](../azure-monitor/overview.md)hakkında daha fazla bilgi edinin.

### <a name="turn-on-logging-from-integration-account"></a>Tümleştirme hesabından günlüğe kaydetmeyi aç

1. [Azure Portal](https://portal.azure.com)tümleştirme hesabınızı bulun ve seçin. **İzleme**altında **Tanılama ayarları**' nı seçin.

   ![Tümleştirme hesabınızı bulun ve seçin, "Tanılama ayarları" nı seçin.](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Şimdi tümleştirme hesabınızı bulun ve seçin. Filtre listelerinde, tümleştirme hesabınıza uygulanan değerleri seçin.
İşiniz bittiğinde, **Tanılama ayarı Ekle**' yi seçin.

   | Özellik | Değer | Açıklama | 
   |----------|-------|-------------|
   | **Abonelik** | <*Azure-subscription-name*> | Tümleştirme hesabınızla ilişkili Azure aboneliği | 
   | **Kaynak grubu** | <*Azure-Resource-Group-name*> | Tümleştirme hesabınız için Azure Kaynak grubu | 
   | **Kaynak türü** | **Tümleştirme hesaplarına genel bakış** | Günlük kaydını açmak istediğiniz Azure kaynağının türü | 
   | **Kaynak** | <*tümleştirme-hesap-adı* > | Günlük kaydını açmak istediğiniz Azure kaynağınızın adı | 
   ||||  

   Örnek:

   ![Tümleştirme hesabınız için tanılamayı ayarlama](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Yeni tanılama ayarınız için bir ad girin ve Log Analytics çalışma alanınızı ve günlüğe kaydetmek istediğiniz verileri seçin.

   1. **Log Analytics gönder**' i seçin. 

   1. **Log Analytics**altında **Yapılandır**' ı seçin. 

   1. **OMS çalışma alanları**altında, günlük kaydı için kullanmak istediğiniz Log Analytics çalışma alanını seçin. 

      > [!NOTE]
      > OMS çalışma alanları Log Analytics çalışma alanlarıyla değiştiriliyor. 

   1. **Günlük**altında, **ıntegrationaccounttrackingevents** kategorisini seçin ve **Kaydet**' i seçin.

   Örnek: 

   ![Azure Izleyici günlüklerini bir günlüğe Tanılama verileri gönderebilmeniz için ayarlama](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. [Azure izleyici GÜNLÜKLERINDE B2B iletilerinize yönelik izlemeyi şimdi ayarlayın](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Azure Izleyici aracılığıyla günlüğü açma

1. [Azure Portal](https://portal.azure.com)ana Azure menüsünde **izleyici**' yi seçin. **Ayarlar**altında **Tanılama ayarları**' nı seçin. 

   ![Tümleştirme hesabınız > "Izleyici" > "Tanılama ayarları" öğesini seçin](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Şimdi tümleştirme hesabınızı bulun ve seçin. Filtre listelerinde, tümleştirme hesabınıza uygulanan değerleri seçin.
İşiniz bittiğinde, **Tanılama ayarı Ekle**' yi seçin.

   | Özellik | Değer | Açıklama | 
   |----------|-------|-------------|
   | **Abonelik** | <*Azure-subscription-name*> | Tümleştirme hesabınızla ilişkili Azure aboneliği | 
   | **Kaynak grubu** | <*Azure-Resource-Group-name*> | Tümleştirme hesabınız için Azure Kaynak grubu | 
   | **Kaynak türü** | **Tümleştirme hesaplarına genel bakış** | Günlük kaydını açmak istediğiniz Azure kaynağının türü | 
   | **Kaynak** | <*tümleştirme-hesap-adı* > | Günlük kaydını açmak istediğiniz Azure kaynağınızın adı | 
   ||||  

   Örnek:

   ![Tümleştirme hesabınız için tanılamayı ayarlama](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Yeni tanılama ayarınız için bir ad girin ve Log Analytics çalışma alanınızı ve günlüğe kaydetmek istediğiniz verileri seçin.

   1. **Log Analytics gönder**' i seçin. 

   1. **Log Analytics**altında **Yapılandır**' ı seçin. 

   1. **OMS çalışma alanları**altında, günlük kaydı için kullanmak istediğiniz Log Analytics çalışma alanını seçin. 

      > [!NOTE]
      > OMS çalışma alanları Log Analytics çalışma alanlarıyla değiştiriliyor. 

   1. **Günlük**altında, **ıntegrationaccounttrackingevents** kategorisini seçin ve **Kaydet**' i seçin.

   Örnek: 

   ![Azure Izleyici günlüklerini bir günlüğe Tanılama verileri gönderebilmeniz için ayarlama](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. [Azure izleyici GÜNLÜKLERINDE B2B iletilerinize yönelik izlemeyi şimdi ayarlayın](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="use-diagnostic-data-with-other-services"></a>Tanılama verilerini diğer hizmetlerle kullanma

Azure Izleyici günlükleriyle birlikte, mantıksal uygulamanızın tanılama verilerini diğer Azure hizmetleriyle nasıl kullanacağınızı genişletebilirsiniz, örneğin: 

* [Azure depolama 'da Azure Tanılama günlüklerini arşivleme](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Azure Event Hubs akış Azure Tanılama günlükleri](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 

Daha sonra [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) ve [Power BI](../azure-monitor/platform/powerbi.md)gibi diğer hizmetlerden telemetri ve analiz kullanarak gerçek zamanlı izleme sağlayabilirsiniz. Örnek:

* [Event Hubs veri akışı Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Stream Analytics ile akış verilerini çözümleme ve Power BI bir gerçek zamanlı analiz panosu oluşturma](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Ayarlamak istediğiniz seçeneklere bağlı olarak, önce [bir Azure depolama hesabı oluşturun](../storage/common/storage-create-storage-account.md) veya [bir Azure Olay Hub 'ı oluşturun](../event-hubs/event-hubs-create.md). Daha sonra, tanılama verilerini göndermek istediğiniz hedefleri seçebilirsiniz.
Saklama süreleri yalnızca bir depolama hesabı kullanmayı seçtiğinizde geçerlidir.

![Verileri Azure depolama hesabına veya Olay Hub 'ına gönder](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Desteklenen izleme şemaları

Azure, bu izleme şeması türlerini destekler, bu, hepsi özel tür dışında sabit şemaları vardır.

* [AS2 izleme şeması](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 izleme şeması](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Özel izleme şeması](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Izleyici günlüklerinde B2B iletilerini izleme](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Azure Izleyici günlüklerinde B2B iletilerini izleme")
* [Enterprise Integration Pack hakkında daha fazla bilgi edinin](../logic-apps/logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack hakkında bilgi edinin")

