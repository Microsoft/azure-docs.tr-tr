---
title: Azure App Service kaynak olaylarını al
description: Etkinlik günlükleri aracılığıyla kaynak olaylarını nasıl alabileceğinizi ve App Service uygulamanızda Event Grid öğrenin.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: c20028a4f84dae9d292cf855a1e164bd69864909
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574038"
---
# <a name="get-resource-events-in-azure-app-service"></a>Azure App Service kaynak olaylarını al

Azure App Service kaynaklarınızın durumunu ve sistem durumunu izlemek için yerleşik araçlar sağlar. Kaynak olayları, temel Web uygulaması kaynaklarınız üzerinde yapılan tüm değişiklikleri anlamanıza yardımcı olur ve gerekli işlemleri gerçekleştirin. Olay örnekleri şunlardır: örneklerin ölçeklendirilmesi, uygulama ayarlarına yönelik güncelleştirmeler, Web uygulamasının yeniden başlatılması ve çok daha fazlası. Bu makalede, [Azure etkinlik günlüklerini](../azure-monitor/essentials/activity-log.md#view-the-activity-log) görüntülemeyi ve App Service Web uygulamanız ile ilgili kaynak olaylarını izlemek için [Event Grid](../event-grid/index.yml) etkinleştirmeyi öğreneceksiniz.

> [!NOTE]
> Event Grid ile tümleştirme App Service **önizlemededir**. [Daha fazla ayrıntı için duyuruyu görüntüleyin.](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>Azure etkinlik günlüklerini görüntüleme
Azure etkinlik günlükleri, aboneliğinizdeki kaynaklar üzerinde gerçekleştirilen işlemler tarafından yayılan kaynak olaylarını içerir. Azure portal ve Azure Resource Manager şablonlarındaki Kullanıcı eylemleri, etkinlik günlüğü tarafından yakalanan olaylara katkıda bulunur. 

Azure etkinlik günlükleri gibi App Service Ayrıntılar için:
- kaynaklarda gerçekleştirilen işlemler (örn. App Service planlar)
- işlemi kim başlattı
- işlem oluştuğunda
- işlemin durumu
- işlemi araştırmanıza yardımcı olacak özellik değerleri

### <a name="what-can-you-do-with-azure-activity-logs"></a>Azure etkinlik günlükleri ile neler yapabilirsiniz?

Azure etkinlik günlükleri Azure portal, PowerShell, REST API veya CLı kullanılarak sorgulanabilir. Günlükleri bir depolama hesabına, Olay Hub 'ına ve Log Analytics gönderebilirsiniz. Ayrıca, bunları Power BI analiz edebilir veya kaynak olaylarında güncel kalmasını sağlamak için uyarılar oluşturabilirsiniz.

[Azure etkinlik günlüğü olaylarını görüntüleyin ve alın.](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

## <a name="ship-activity-logs-to-event-grid"></a>Etkinlik günlüklerini Event Grid gönder

Etkinlik günlükleri Kullanıcı tabanlı olsa da, hem kullanıcı eylemlerini hem de otomatik olayları günlüğe kaydeden App Service (Önizleme) ile yeni bir [Event Grid](../event-grid/index.yml) tümleştirme vardır. Event Grid, bir işleyiciyi, söylemi olaylara tepki verecek şekilde yapılandırabilirsiniz. Event Grid’i örneğin bir blob depolama kapsayıcısına eklenen her yeni fotoğrafta görüntü analizini çalıştıracak sunucusuz bir işlev tetiklemek için kullanabilirsiniz.

Ayrıca Event Grid’i Logic Apps ile birlikte kullanarak kod yazmadan her yerden veri işleyebilirsiniz. Event Grid, veri kaynaklarını ve olay işleyicilerini bağlar. Event Grid’i örneğin bir blob depolama kapsayıcısına eklenen her yeni fotoğrafta görüntü analizini çalıştıracak sunucusuz bir işlev tetiklemek için kullanabilirsiniz.

[Azure App Service olaylar için özellikleri ve şemayı görüntüleyin.](../event-grid/event-schema-app-service.md)

## <a name="next-steps"></a><a name="nextsteps"></a> Sonraki adımlar
* [Azure Izleyici ile günlük sorgulama](../azure-monitor/logs/log-query-overview.md)
* [Azure App Service Izleme](web-sites-monitor.md)
* [Visual Studio 'da Azure App Service sorunlarını giderme](troubleshoot-dotnet-visual-studio.md)
* [HDInsight 'ta uygulama günlüklerini çözümleme](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)