---
title: Azure Izleyici günlüklerinin yapısı | Microsoft Docs
description: Azure Izleyici 'den günlük verilerini almak için bir günlük sorgusu gerekir.  Bu makalede, yeni günlük sorgularının Azure Izleyici 'de nasıl kullanıldığı açıklanır ve bir tane oluşturmadan önce anlamanız gereken kavramlar sağlanır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 091d7f598a9841ae45b4248ad8a07a355203445a
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894242"
---
# <a name="structure-of-azure-monitor-logs"></a>Azure Izleyici günlüklerinin yapısı
[Günlük sorgusu](log-query-overview.md) kullanarak verilerinize hızlı bir şekilde Öngörüler elde etme özelliği, Azure izleyici 'nin güçlü bir özelliğidir. Etkili ve yararlı sorgular oluşturmak için, istediğiniz verilerin bulunduğu yer ve nasıl yapılandırıldığı gibi bazı temel kavramları anlamanız gerekir. Bu makalede, başlamak için ihtiyacınız olan temel kavramlar sağlanmaktadır.

## <a name="overview"></a>Genel Bakış
Azure Izleyici günlüklerindeki veriler Log Analytics çalışma alanında veya Application Insights uygulamasında depolanır. Her ikisi de [Azure Veri Gezgini](/azure/data-explorer/) tarafından desteklenir ve güçlü veri motoru ve sorgu dilinden faydalanır.

Her iki çalışma alanında ve uygulamada bulunan veriler, her biri farklı veri türlerini depolayan ve kendi benzersiz özellik kümesine sahip olan tablolar halinde düzenlenmiştir. Çoğu [veri kaynağı](../platform/data-sources.md) bir Log Analytics çalışma alanında kendi tablolarına yazılır, ancak Application Insights bir Application Insights uygulamasındaki önceden tanımlanmış bir tablo kümesine yazar. Günlük sorguları, birden çok tablodan verileri kolayca birleştirebilmenizi ve hatta birden fazla çalışma alanındaki tablolardan verileri birleştirmek ya da çalışma alanını ve uygulama verilerini birleştiren sorgular yazmak için bir çapraz kaynak sorgusu kullanmak çok esnektir.

Aşağıdaki görüntüde, örnek sorgularda kullanılan farklı tablolara yazan veri kaynağı örnekleri gösterilmektedir.

![Tablolar](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics çalışma alanı
Azure Izleyici günlükleri tarafından toplanan tüm veriler, Application Insights dışında bir [Log Analytics çalışma alanında](../platform/manage-access.md)depolanır. Gereksinimlerinize bağlı olarak bir veya daha fazla çalışma alanı oluşturabilirsiniz. Azure kaynaklarından gelen etkinlik günlükleri ve tanılama günlükleri, sanal makinelerdeki aracılar ve Öngörüler ve izleme çözümlerinin verileri gibi [veri kaynakları](../platform/data-sources.md) , kendi ekleme kapsamında yapılandırdığınız bir veya daha fazla çalışma alanına veri yazar. [Azure Güvenlik Merkezi](/azure/security-center/) ve [Azure Sentinel](/azure/sentinel/) gibi diğer hizmetler aynı zamanda verilerini depolamak için bir Log Analytics çalışma alanı kullanır. böylece, diğer kaynaklardaki izleme verileriyle birlikte günlük sorguları kullanılarak analiz edilebilir.

Farklı veri türleri çalışma alanındaki farklı tablolarda depolanır ve her tablo benzersiz bir özellik kümesine sahiptir. Standart bir tablo kümesi oluşturulduğunda çalışma alanına eklenir ve farklı veri kaynakları, çözümler ve hizmetler için eklendi oldukları gibi yeni tablolar eklenir. Ayrıca, [veri TOPLAYıCı API](../platform/data-collector-api.md)'sini kullanarak özel tablolar oluşturabilirsiniz.

Çalışma alanı için Log Analytics **şema** sekmesinde bir çalışma alanındaki tablolara ve bunların şemasına gözatabilmeniz gerekir.

![Çalışma alanı şeması](media/scope/workspace-schema.png)

Çalışma alanındaki tabloları ve bir önceki gün içinde her birine toplanan kayıt sayısını listelemek için aşağıdaki sorguyu kullanın. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Oluşturdukları tabloların ayrıntıları için her bir veri kaynağı için belgelere bakın. Örnek olarak, [aracı veri kaynakları](../platform/agent-data-sources.md), [tanılama günlükleri](../platform/diagnostic-logs-schema.md)ve [izleme çözümleri](../insights/solutions-inventory.md)için makaleler bulunur.

### <a name="workspace-permissions"></a>Çalışma alanı izinleri
Çalışma alanındaki verilere erişim sağlama önerilerini ve erişim denetimi stratejisini anlamak için bkz. [Azure Izleyici günlükleri dağıtımı tasarlama](../platform/design-logs-deployment.md) . Çalışma alanına erişim vermeye ek olarak, [tablo DÜZEYI RBAC](../platform/manage-access.md#table-level-rbac)kullanarak tek tek tablolarla erişimi sınırlayabilirsiniz.

## <a name="application-insights-application"></a>Application Insights uygulaması
Application Insights bir uygulama oluşturduğunuzda, Azure Izleyici günlüklerinde ilgili bir uygulama otomatik olarak oluşturulur. Veri toplamak için yapılandırma gerekmez ve uygulama sayfa görünümleri, istekler ve özel durumlar gibi izleme verilerini otomatik olarak yazar.

Log Analytics çalışma alanının aksine, bir Application Insights uygulamasının sabit bir tablo kümesi vardır. Başka veri kaynaklarını uygulamaya yazacak şekilde yapılandıramazsınız, böylece başka bir tablo oluşturulamaz. 

| Tablo | Açıklama | 
|:---|:---|
| availabilityResults | Kullanılabilirlik testlerinden özet veriler. |
| Browserzamanlamalar      | Gelen verileri işlemek için geçen süre gibi istemci performansı hakkındaki veriler. |
| customEvents        | Uygulamanız tarafından oluşturulan özel olaylar. |
| customMetrics       | Uygulamanız tarafından oluşturulan özel ölçümler. |
| Bağlantılıdır        | Uygulamadan dış bileşenlere çağrılar. |
| Larý          | Uygulama çalışma zamanı tarafından oluşturulan özel durumlar. |
| PageViews           | Tarayıcı bilgileriyle her bir Web sitesi görünümüyle ilgili veriler. |
| performanceCounters | Uygulamayı destekleyen işlem kaynaklarından performans ölçümleri. |
| istekleri            | Her uygulama isteğinin ayrıntıları.  |
| Lerin              | Dağıtılmış izlemenin sonuçları. |

Her tablo için şemayı, uygulama için Log Analytics **şema** sekmesinde görüntüleyebilirsiniz.

![Uygulama şeması](media/scope/application-schema.png)

## <a name="standard-properties"></a>Standart özellikler
Azure Izleyici günlüklerindeki her tablo kendi şemasına sahip olsa da, tüm tablolar tarafından paylaşılan standart özellikler vardır. Her birinin ayrıntıları için bkz. [Azure Izleyici günlüklerinde standart özellikler](../platform/log-standard-properties.md) .

| Log Analytics çalışma alanı | Application Insights uygulaması | Açıklama |
|:---|:---|:---|
| TimeGenerated | timestamp  | Kaydın oluşturulduğu tarih ve saat. |
| Tür          | ItemType   | Kaydın alındığı tablonun adı. |
| _Resourceıd   |            | Kaydın ilişkilendirildiği kaynak için benzersiz tanımlayıcı. |
| _Faturalandırılabilir   |            | Alınan verilerin faturalandırılabilir olup olmadığını belirtir. |
| _BilledSize   |            | Faturalanacak verilerin bayt cinsinden boyutunu belirtir. |

## <a name="next-steps"></a>Sonraki adımlar
- [Günlük aramaları oluşturmak ve düzenlemek için Log Analytics](../log-query/portals.md)kullanma hakkında bilgi edinin.
- Yeni sorgu dilini kullanarak [sorgu yazma hakkında öğreticiye](../log-query/get-started-queries.md) göz atın.
