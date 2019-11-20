---
title: Azure Izleyici ile mantıksal uygulamaları izleme-Azure Logic Apps
description: Azure Izleyici günlükleri ile ilgili sorun gidermek ve mantıksal uygulama çalıştırmalarını tanılamak için öngörüleri ve hata ayıklama verilerini alın
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 08/29/2019
ms.openlocfilehash: a038a05f03ce7a209ae82203441750749bc6c4c4
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70138770"
---
# <a name="get-insights-and-debugging-data-for-logic-apps-by-using-azure-monitor-logs"></a>Azure Izleyici günlüklerini kullanarak Logic Apps için Öngörüler ve hata ayıklama verileri alın

Mantıksal uygulamalarınız hakkında daha zengin hata ayıklama ayrıntılarını izlemek ve almak için, mantıksal uygulamanızı oluştururken [Azure izleyici günlüklerini](../log-analytics/log-analytics-overview.md) açın. Azure Izleyici günlükleri, Azure portal Logic Apps yönetim çözümünü yüklediğinizde mantıksal uygulamalarınız için tanılama günlüğü ve izleme sağlar. Bu çözüm Ayrıca durum, yürütme süresi, yeniden gönderme görevi durumu ve bağıntı kimlikleri gibi belirli ayrıntılarla mantıksal uygulama çalıştırmaları için toplu bilgiler sağlar. Bu makalede, mantıksal uygulama çalışmalarınız için çalışma zamanı olaylarını ve verilerini görüntüleyebilmeniz için Azure Izleyici günlüklerinin nasıl kullanılacağı gösterilmektedir.

Bu konu başlığı altında, mantıksal uygulamanızı oluştururken Azure Izleyici günlüklerinin nasıl ayarlanacağı gösterilmektedir. Mevcut Logic Apps için Azure Izleyici günlüklerini açmak üzere [Tanılama günlüğünü açmak ve Azure izleyici günlüklerine mantıksal uygulama çalışma zamanı verilerini göndermek](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)için aşağıdaki adımları izleyin.

> [!NOTE]
> Bu sayfa, bu görevleri daha önce [2019 Ocak 'ta kullanımdan kaldırılan](../azure-monitor/platform/oms-portal-transition.md)MICROSOFT OPERATIONS Management SUITE (OMS) ile gerçekleştirmeye yönelik adımları ve Log Analytics teriminin yerini alan [Azure izleyici günlükleri](../azure-monitor/platform/data-platform-logs.md)ile değiştirir. Günlük verileri hala bir Log Analytics çalışma alanında depolanır ve yine de aynı Log Analytics hizmeti tarafından toplanıp çözümlenmektedir. Daha fazla bilgi için bkz. [Azure izleyici terminolojisi değişiklikleri](../azure-monitor/terminology.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce bir Log Analytics çalışma alanına ihtiyacınız vardır. [Log Analytics çalışma alanı oluşturmayı](../azure-monitor/learn/quick-create-workspace.md)öğrenin.

## <a name="turn-on-logging-for-new-logic-apps"></a>Yeni mantıksal uygulamalar için günlüğü aç

1. [Azure Portal](https://portal.azure.com), mantıksal uygulamanızı oluşturun. Ana Azure menüsünden **kaynak** > **tümleştirme** > **mantıksal uygulaması**oluştur ' u seçin.

   ![Yeni mantıksal uygulama oluştur](media/logic-apps-monitor-your-logic-apps-oms/create-new-logic-app.png)

1. **Mantıksal uygulama**altında şu adımları izleyin:

   1. Mantıksal uygulamanız için bir ad girin ve Azure aboneliğinizi seçin.

   1. Bir Azure Kaynak grubu oluşturun veya seçin. Mantıksal uygulamanızın konumunu seçin.

   1. **Log Analytics**altında **Açık**' ı seçin.

   1. **Log Analytics çalışma alanı** listesinden, mantıksal uygulama çalıştırmalarından verileri göndermek istediğiniz çalışma alanını seçin.

      ![Mantıksal uygulama bilgilerini sağlama](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app-details.png)

      Bu adımı tamamladıktan sonra Azure, artık Log Analytics çalışma alanınız ile ilişkilendirilen mantıksal uygulamanızı oluşturur. Ayrıca, bu adım Logic Apps yönetim çözümünü çalışma alanınıza otomatik olarak yüklenir.

   1. İşiniz bittiğinde **Oluştur**’u seçin.

1. Mantıksal uygulama çalıştırmalarını görüntülemek için [Bu adımlarla devam edin](#view-logic-app-runs-oms).

## <a name="install-logic-apps-management-solution"></a>Logic Apps yönetimi çözümünü yükler

Mantıksal uygulamanızı oluştururken Azure Izleyici günlüklerini zaten ayarladıysanız, bu adımı atlayın. Logic Apps Management Solution zaten yüklüdür.

1. [Azure portalda](https://portal.azure.com) **Tüm hizmetler**’i seçin. Arama kutusunda, "Log Analytics çalışma alanları" nı bulun ve **Log Analytics çalışma alanları**' nı seçin.

   !["Log Analytics çalışma alanları" i seçin](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. **Log Analytics çalışma alanları**altında, çalışma alanınızı seçin.

   ![Log Analytics çalışma alanınızı seçin](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. Genel Bakış bölmesinde,**izleme çözümlerini yapılandırma** >  **Log Analytics kullanmaya başlama**altında, **çözümleri görüntüle**' yi seçin.

   !["Çözümleri görüntüle" seçeneğini belirleyin](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. **Genel bakış**' ın altında **Ekle**' yi seçin.

   !["Ekle" yi seçin](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

1. **Market** açıldıktan sonra arama kutusuna "Logic Apps Management" yazın ve **Logic Apps yönetim**' i seçin.

   !["Logic Apps yönetimi" ni seçin](./media/logic-apps-monitor-your-logic-apps-oms/select-logic-apps-management.png)

1. Çözüm açıklaması bölmesinde **Oluştur**' u seçin.

   !["Logic Apps yönetimi" için "Oluştur" u seçin](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

1. Çözümü yüklemek istediğiniz Log Analytics çalışma alanını gözden geçirip onaylayın ve yeniden **Oluştur** ' u seçin.

   !["Logic Apps yönetimi" için "Oluştur" u seçin](./media/logic-apps-monitor-your-logic-apps-oms/confirm-log-analytics-workspace.png)

   Azure, çözümü Log Analytics çalışma alanınızı içeren Azure Kaynak grubuna dağıtduktan sonra, çözüm çalışma alanınızın Özet bölmesinde görünür.

   ![Çalışma alanı Özet bölmesi](./media/logic-apps-monitor-your-logic-apps-oms/workspace-summary-pane-logic-apps-management.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>Mantıksal uygulama çalıştırma bilgilerini görüntüle

Mantıksal uygulamanız çalıştıktan sonra, **Logic Apps yönetim** kutucuğunda Bu çalıştırmaların durumunu ve sayısını görüntüleyebilirsiniz.

1. Log Analytics çalışma alanınıza gidin ve **çalışma alanı Özeti** > **Logic Apps yönetim**' i seçin.

   ![Mantıksal uygulama çalıştırma durumu ve sayısı](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary.png)

   Burada, mantıksal uygulamanız adına veya yürütme durumuna göre gruplandırılır. Bu sayfada Ayrıca, mantıksal uygulama çalıştırmaları için eylemlerdeki veya tetikleyicilere yönelik hataların ayrıntıları gösterilir.

   ![Mantıksal uygulama çalışmalarınız için durum özeti](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary-details.png)

1. Belirli bir mantıksal uygulama veya durum için tüm çalıştırmaları görüntülemek için, bir mantıksal uygulamanın satırını veya durumunu seçin.

   Belirli bir mantıksal uygulama için tüm çalıştırmaları gösteren bir örnek aşağıda verilmiştir:

   ![Mantıksal uygulama çalıştırmalarını ve durumunu görüntüleme](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Bu sayfa gelişmiş seçeneklere sahiptir: 

   * **İzlenen Özellikler** sütunu: Eylemlerle gruplanmış olan izlenen özellikleri ayarladığınız bir mantıksal uygulama için bu özellikleri bu sütundan görüntüleyebilirsiniz. Bu izlenen özellikleri görüntülemek için **görüntüle**' yi seçin. İzlenen özelliklerde arama yapmak için sütun filtresini kullanın.

      ![Mantıksal uygulama için izlenen özellikleri görüntüleme](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

      Yeni eklenen tüm izlenen özellikler ilk kez görüntülenmeden önce 10-15 dakika sürebilir. [Mantıksal uygulamanıza izlenen özellikler eklemeyi](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details)öğrenin.

   * Yeniden **Gönder**: Başarısız, başarılı veya çalışır durumda olan bir veya daha fazla Logic Apps çalıştırmasını yeniden gönderebilirsiniz. Yeniden göndermek istediğiniz çalıştırmaların onay kutularını seçin ve ardından yeniden **Gönder**' i seçin.

     ![Mantıksal uygulama çalıştırmalarını yeniden gönder](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Sonuçlarınızı filtrelemek için hem istemci tarafı hem de sunucu tarafı filtrelemeyi gerçekleştirebilirsiniz.

   * **İstemci tarafı filtresi**: Her sütun için istediğiniz filtreleri seçin, örneğin:

     ![Örnek sütun filtreleri](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Sunucu tarafı filtresi**: Belirli bir zaman penceresi seçmek veya görüntülenen çalıştırmaların sayısını sınırlandırmak için sayfanın üst kısmındaki kapsam denetimini kullanın. Varsayılan olarak, tek seferde yalnızca 1.000 kayıt görünür.

     ![Zaman penceresini değiştirme](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)

1. Belirli bir çalıştırmaya ilişkin tüm eylemleri ve bunların ayrıntılarını görüntülemek için, mantıksal uygulama çalıştırmasının satırını seçin.

   Aşağıda, belirli bir mantıksal uygulama çalıştırması için tüm eylemleri ve Tetikleyicileri gösteren bir örnek verilmiştir:

   ![Mantıksal uygulama çalıştırması için eylemleri görüntüleme](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)

1. Herhangi bir sonuç sayfasında, sonuçların arkasındaki sorguyu görüntülemek veya tüm sonuçları görmek için, **Günlükler** sayfasını açan **Tümünü**göster ' i seçin.

   ![Tüm sonuçları görüntüle](media/logic-apps-monitor-your-logic-apps-oms/logic-app-see-all.png)

   **Günlükler** sayfasında, şu seçenekleri belirleyebilirsiniz:

   * Sorgu sonuçlarını bir tabloda görüntülemek için **tablo**' yı seçin.

   * Sorgular, farklı sonuçları görüntülemek istiyorsanız düzenleyebileceğiniz [kusto sorgu dilini](https://aka.ms/LogAnalyticsLanguageReference)kullanır. Sorguyu değiştirmek için sorgu dizesini güncelleştirin ve sonuçları tabloda görüntülemek için **Çalıştır** ' ı seçin. 

     ![Log Analytics-sorgu görünümü](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Sonraki adımlar

* [B2B iletilerini izleme](../logic-apps/logic-apps-monitor-b2b-message.md)