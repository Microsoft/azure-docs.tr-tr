---
title: B2B iletileri için izleme sorguları oluşturma-Azure Logic Apps
description: Azure Logic Apps için Azure Log Analytics 'de AS2, x12 ve EDIOLGU iletilerini izleyen sorgular oluşturun
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 7d7bb53d24a113ea78b5bac3f9682fbb61ce2de9
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680090"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>Azure Logic Apps için Azure Izleyici günlüklerinde B2B iletileri için izleme sorguları oluşturma

[Azure izleyici günlükleri](../log-analytics/log-analytics-overview.md)ile IZLEMEKTE olduğunuz AS2, x12 veya ediolgu iletilerini bulmak için, eylemleri belirli ölçütlere göre filtreleyen sorgular oluşturabilirsiniz. Örneğin, belirli bir değişim denetim numarasına göre iletileri bulabilirsiniz.

> [!NOTE]
> Bu sayfada daha önce bu görevleri Microsoft Operations Management Suite (OMS) ile gerçekleştirme adımları açıklanmakta ve bu adımların [ocak 2019 ' de devre dışı](../azure-monitor/platform/oms-portal-transition.md)bırakılmakta olan adımlar, bunun yerine Azure Log Analytics ile değiştirilir. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Önkoşullar

* Tanılama günlük kaydı ile ayarlanan mantıksal uygulama. [Mantıksal uygulama oluşturmayı](quickstart-create-first-logic-app-workflow.md) ve [Bu mantıksal uygulama için günlüğü ayarlamayı](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)öğrenin.

* İzleme ve günlüğe kaydetme ile ayarlanan bir tümleştirme hesabı. [Tümleştirme hesabı oluşturmayı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) ve [Bu hesap için izleme ve günlüğe kaydetmeyi ayarlama](../logic-apps/logic-apps-monitor-b2b-message.md)hakkında bilgi edinin.

* Henüz yapmadıysanız, [tanılama verilerini Azure izleyici günlüklerine yayımlayın](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) ve [Azure izleyici günlüklerinde ileti izlemeyi ayarlayın](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="create-queries-with-filters"></a>Filtrelerle sorgu oluşturma

Belirli özelliklere veya değerlere göre iletileri bulmak için, filtreleri kullanan sorgular oluşturabilirsiniz. 

1. [Azure portalda](https://portal.azure.com) **Tüm hizmetler**’i seçin. Arama kutusunda, "Log Analytics" i bulun ve **Log Analytics**' yi seçin.

   ![Log Analytics seçin](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. **Log Analytics**altında Log Analytics çalışma alanınızı bulun ve seçin. 

   ![Log Analytics çalışma alanı seçin](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. Çalışma alanı menünüzde **genel**altında **Günlükler (klasik)** veya **Günlükler**' i seçin. 

   Bu örnek, klasik Günlükler görünümünü nasıl kullanacağınızı gösterir. 
   **Log Analytics deneyiminizi en üst düzeye çıkarın** bölümünde **günlükleri görüntüle** ' yi seçerseniz, günlükleri **Ara ve çözümle**bölümünde **Günlükler (Klasik Görünüm)** alırsınız. 

   ![Klasik günlükleri görüntüle](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. Sorgu düzenleme kutusunda, bulmak istediğiniz alan adını yazmaya başlayın. Yazmaya başladığınızda, sorgu Düzenleyicisi kullanabileceğiniz olası eşleşmeleri ve işlemleri gösterir. Sorgunuzu oluşturduktan sonra **Çalıştır** ' ı seçin veya ENTER tuşuna basın.

   Bu örnek, **LogicAppB2B**ile eşleşmeleri arar. 
   [Azure izleyici günlüklerinde veri bulma](../log-analytics/log-analytics-log-searches.md)hakkında daha fazla bilgi edinin.

   ![Sorgu dizesi yazmaya başlayın](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. Görüntülemek istediğiniz zaman dilimini değiştirmek için, sol bölmedeki süre listesinden seçim yapın veya kaydırıcıyı sürükleyin. 

   ![Zaman dilimini değiştir](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. Sorgunuza bir filtre eklemek için **Ekle**' yi seçin. 

   ![Sorguya filtre Ekle](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. **Filtre Ekle**' nin altında, bulmak istediğiniz filtre adını girin. Filtreyi bulursanız filtre ' yi seçin. Sol bölmede yeniden **Ekle** ' yi seçin.

   Örneğin, **Type = = "AzureDiagnostics"** olaylarını arayan ve **event_record_messageProperties_interchangeControlNumber_s** filtresini seçerek değişim denetim numarasını temel alan sonuçları bulan farklı bir sorgu aşağıda verilmiştir.

   ![Filtre değeri seçin](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   **Ekle**' yi seçtikten sonra, sorgunuz Seçili filtre olaylarınız ve değeri ile güncelleştirilir. 
   Önceki sonuçlarınız artık çok filtrelenmiştir. 

   Örneğin, bu sorgu **Type = = "AzureDiagnostics"** öğesini arar ve **event_record_messageProperties_interchangeControlNumber_s** filtresini kullanarak bir değişim denetim numarası temelinde sonuçları bulur.

   ![Filtrelenmiş sonuçlar](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>Sorguyu Kaydet

Sorgunuzu **Günlükler (klasik)** görünümünde kaydetmek için şu adımları izleyin:

1. Sorgular **Günlükler (klasik)** sayfasında **analiz**' i seçin. 

   !["Analytics" i seçin](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. Sorgu araç çubuğunda **Kaydet**' i seçin.

   !["Kaydet"i seçin](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. Sorgunuzun ayrıntılarını sağlayın, örneğin, sorgunuza bir ad verin, **sorgu**seçin ve bir kategori adı sağlayın. İşiniz bittiğinde **Kaydet**’i seçin.

   !["Kaydet"i seçin](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. Kaydedilen sorguları görüntülemek için sorgu sayfasına dönün. Sorgu araç çubuğunda, **kaydedilmiş aramalar**' ı seçin.

   !["Kayıtlı aramalar" ı seçin](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. **Kayıtlı aramalar**' ın altında, sonuçları görüntüleyebilmeniz için sorgunuzu seçin. 

   ![Sorgunuzu seçin](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   Farklı sonuçlar bulabilmeniz için sorguyu güncelleştirmek üzere sorguyu düzenleyin.

## <a name="find-and-run-saved-queries"></a>Kaydedilmiş sorguları bul ve Çalıştır

1. [Azure portalda](https://portal.azure.com) **Tüm hizmetler**’i seçin. Arama kutusunda, "Log Analytics" i bulun ve **Log Analytics**' yi seçin.

   ![Log Analytics seçin](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. **Log Analytics**altında Log Analytics çalışma alanınızı bulun ve seçin. 

   ![Log Analytics çalışma alanı seçin](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. Çalışma alanı menünüzde **genel**altında **Günlükler (klasik)** veya **Günlükler**' i seçin. 

   Bu örnek, klasik Günlükler görünümünü nasıl kullanacağınızı gösterir. 

1. Sorgu sayfası açıldıktan sonra sorgu araç çubuğunda **kaydedilmiş aramalar**' ı seçin.

   !["Kayıtlı aramalar" ı seçin](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. **Kayıtlı aramalar**' ın altında, sonuçları görüntüleyebilmeniz için sorgunuzu seçin. 

   ![Sorgunuzu seçin](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   Sorgu otomatik olarak çalışır, ancak sorgu herhangi bir nedenden dolayı çalışmazsa sorgu Düzenleyicisi 'nde **Çalıştır**' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [AS2 izleme şemaları](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 izleme şemaları](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Özel izleme şemaları](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)