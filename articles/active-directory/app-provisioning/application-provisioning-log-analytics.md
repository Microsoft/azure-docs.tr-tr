---
title: Sağlamanın Azure Active Directory Azure Izleyici günlükleri ile nasıl tümleştirildiğini anlayın.
description: Sağlamanın Azure Active Directory Azure Izleyici günlükleri ile nasıl tümleştirildiğini anlayın.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: f656f55b0c74103053f110b8c66645d353db9fbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561577"
---
# <a name="understand-how-provisioning-integrates-with-azure-monitor-logs"></a>Sağlamanın Azure Izleyici günlükleriyle nasıl tümleştirildiğini anlama

Sağlama, Azure Izleyici günlükleri ve Log Analytics ile tümleşir. Azure izleme ile, pano olarak da bilinen çalışma kitaplarını oluşturma, 30 ila gün için sağlama günlüklerini depolama ve özel sorgular ve uyarılar oluşturma gibi işlemleri yapabilirsiniz. Bu makalede, sağlama günlüklerinin Azure Izleyici günlükleriyle nasıl tümleştirileceği açıklanmaktadır. Sağlama günlüklerinin genel olarak nasıl çalıştığı hakkında daha fazla bilgi edinmek için bkz. [sağlama günlükleri](../reports-monitoring/concept-provisioning-logs.md).

## <a name="enabling-provisioning-logs"></a>Sağlama günlüklerinin etkinleştirilmesi

Azure izleme ve Log Analytics hakkında zaten bilgi sahibi olmanız gerekir. Aksi takdirde, hakkında bilgi edinmek için üzerine atlayın ve ardından uygulama sağlama günlükleri hakkında bilgi edinmek için geri dönün. Azure izleme hakkında daha fazla bilgi edinmek için bkz. [Azure izleyiciye genel bakış](../../azure-monitor/overview.md). Azure Izleyici günlükleri ve Log Analytics hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici 'de günlük sorgularına genel bakış](../../azure-monitor/logs/log-query-overview.md).

Azure izlemeyi yapılandırdıktan sonra, uygulama sağlama için günlükleri etkinleştirebilirsiniz. Seçeneği **Tanılama ayarları** sayfasında bulunur.

:::image type="content" source="media/application-provisioning-log-analytics/diagnostic-settings.png" alt-text="Erişim Tanılama ayarları" lightbox="media/application-provisioning-log-analytics/diagnostic-settings.png":::

:::image type="content" source="media/application-provisioning-log-analytics/enable-log-analytics.png" alt-text="Uygulama sağlama günlüklerini etkinleştir" lightbox="media/application-provisioning-log-analytics/enable-log-analytics.png":::

> [!NOTE]
> Yeni bir çalışma alanı sağladıysanız, günlüğe Günlükler gönderebilmeniz için biraz zaman alabilir. Aboneliğin *Microsoft. Insights* 'ı kullanmak için kayıtlı olmadığını belirten bir hata alırsanız, birkaç dakika sonra tekrar kontrol edin.
 
## <a name="understanding-the-data"></a>Verileri anlama
Kaynak görüntüleyicilerinin gönderdiği temel alınan veri akışı neredeyse aynıdır. Azure Izleyici günlükleri Azure portal UI ve Azure API 'SI ile neredeyse aynı akışı alır. Aşağıdaki tabloda özetlenen günlük alanlarında yalnızca birkaç **fark** vardır. Bu alanlar hakkında daha fazla bilgi edinmek için bkz. [provisioningObjectSummary listeleme](/graph/api/provisioningobjectsummary-list?preserve-view=true&tabs=http&view=graph-rest-beta).

|Azure İzleyici günlükleri   |Azure portal Kullanıcı arabirimi   |Azure API 'SI |
|----------|-----------|------------|
|errorDescription |reason |resultDescription |
|durum |resultType |resultType |
|activityDateTime |TimeGenerated |TimeGenerated |


## <a name="azure-monitor-workbooks"></a>Azure Izleyici çalışma kitapları

Azure Izleyici çalışma kitapları, veri analizi için esnek bir tuval sağlar. Ayrıca, Azure portal içinde zengin görsel raporların oluşturulmasını sağlar. Daha fazla bilgi için bkz. [Azure Izleyici çalışma kitaplarına genel bakış](../../azure-monitor/visualize/workbooks-overview.md).

Uygulama sağlama, önceden oluşturulmuş bir çalışma kitapları kümesiyle gelir. Bunları çalışma kitapları sayfasında bulabilirsiniz. Verileri görüntülemek için tüm filtrelerin (timeRange, JobId, appName) doldurulduğundan emin olmanız gerekir. Ayrıca, bir uygulamayı sağladığınızdan emin olmanız gerekir, aksi takdirde günlüklerde hiç veri olmayacaktır.

:::image type="content" source="media/application-provisioning-log-analytics/workbooks.png" alt-text="Uygulama sağlama çalışma kitapları" lightbox="media/application-provisioning-log-analytics/workbooks.png":::

:::image type="content" source="media/application-provisioning-log-analytics/report.png" alt-text="Uygulama sağlama panosu" lightbox="media/application-provisioning-log-analytics/report.png":::

## <a name="custom-queries"></a>Özel sorgular

Özel sorgular oluşturabilir ve verileri Azure panolarında gösterebilirsiniz. Hakkında bilgi edinmek için bkz. [Log Analytics veri panoları oluşturma ve paylaşma](../../azure-monitor/logs/get-started-queries.md). Ayrıca, [Azure izleyici 'de günlük sorgularına genel bakış](../../azure-monitor/logs/log-query-overview.md)' ı kullanıma aldığınızdan emin olun.

Uygulama sağlamayı kullanmaya başlamak için bazı örnekler aşağıda verilmiştir.

Kaynak sistemdeki KIMLIĞINI temel alarak bir kullanıcıya ait günlükleri sorgulayın:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| where tostring(SourceIdentity.Id) == "49a4974bb-5011-415d-b9b8-78caa7024f9a"
```

Hata kodu başına özetleme sayısı:
```kusto
AADProvisioningLogs
| summarize count() by ErrorCode = ResultSignature
```

Her gün olay sayısını eyleme göre özetle:
```kusto
AADProvisioningLogs
| where TimeGenerated > ago(7d)
| summarize count() by Action, bin(TimeGenerated, 1d)
```

100 olay ve proje anahtarı özellikleri al:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| extend TargetIdentity = parse_json(TargetIdentity)
| extend ServicePrincipal = parse_json(ServicePrincipal)
| where tostring(SourceIdentity.identityType) == "Group"
| project tostring(ServicePrincipal.Id), tostring(ServicePrincipal.Name), ModifiedProperties, JobId, Id, CycleId, ChangeId, Action, SourceIdentity.identityType, SourceIdentity.details, TargetIdentity.identityType, TargetIdentity.details, ProvisioningSteps
|take 100
```

## <a name="custom-alerts"></a>Özel uyarılar

Azure Izleyici, sağlama ile ilgili önemli olaylar hakkında bildirim alabilmeniz için özel uyarılar yapılandırmanıza olanak tanır. Örneğin, hatalarda ani artışlar hakkında bir uyarı almak isteyebilirsiniz. Veya belki de devre dışı bırakır veya siler. Uyarı almak isteyebileceğiniz diğer bir örnek, bir şeyin yanlış olduğunu belirten herhangi bir sağlama olmamasıdır.

Uyarılar hakkında daha fazla bilgi edinmek için bkz. [Azure Izleyici uyarıları ile olaylara yanıt verme](../../azure-monitor/alerts/tutorial-response.md).

Hatalarda ani bir artış olduğunda uyarır. JobId öğesini uygulamanızın iş kimliği ile değiştirin.

:::image type="content" source="media/application-provisioning-log-analytics/alert1.png" alt-text="Hatalarda ani bir artış olduğunda uyarır." lightbox="media/application-provisioning-log-analytics/alert1.png":::

Sağlama hizmetinin çalışmayı durdurmasına neden olan bir sorun olabilir. Belirli bir zaman aralığı boyunca hiçbir sağlama olayı olmadığını algılamak için aşağıdaki uyarıyı kullanın.

:::image type="content" source="media/application-provisioning-log-analytics/alert2.png" alt-text="Sağlama hizmetinin çalışmayı durdurmasına neden olan bir sorun olabilir." lightbox="media/application-provisioning-log-analytics/alert2.png":::

Devre dışı bırakıldığında veya silindiğinde uyarır.

:::image type="content" source="media/application-provisioning-log-analytics/alert3.png" alt-text="Devre dışı bırakıldığında veya silindiğinde uyarır." lightbox="media/application-provisioning-log-analytics/alert3.png":::


## <a name="community-contributions"></a>Topluluk katkıları

Uygulama sağlama sorgularına ve panolarına yönelik açık kaynaklı ve topluluk tabanlı bir yaklaşım sunuyoruz. Başkalarının yararlı bulabileceğini düşündüğünüz bir sorgu, uyarı veya çalışma kitabı oluşturduysanız, [AzureMonitorCommunity GitHub](https://github.com/microsoft/AzureMonitorCommunity)deposunda yayımlamayı unutmayın. Ardından bağlantı içeren bir e-posta gönderin. Başkalarının da avantajlarından faydalanabilmesi için gözden geçireceğiz ve bu hizmette yayımlayacağız. Bizimle iletişime geçin provisioningfeedback@microsoft.com .

## <a name="next-steps"></a>Sonraki adımlar

- [Log Analytics](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)
- [Azure Izleyici günlüklerinde sorguları kullanmaya başlama](../../azure-monitor/logs/get-started-queries.md)
- [Azure portal uyarı grupları oluşturma ve yönetme](../../azure-monitor/alerts/action-groups.md)
- [Azure Active Directory için Log Analytics görünümlerini yükleyip kullanın](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [Sağlama günlükleri API 'SI](/graph/api/resources/provisioningobjectsummary?preserve-view=true&view=graph-rest-beta)