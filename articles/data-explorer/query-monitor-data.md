---
title: Azure Veri Gezgini kullanarak Azure Izleyici 'de verileri sorgulama (Önizleme)
description: Bu konu başlığında, Application Insights ve Log Analytics ile çapraz ürün sorguları için bir Azure Veri Gezgini proxy oluşturarak Azure Izleyici 'deki verileri sorgulayın
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 43d91bff6b8b67e79a9549c1524f918166c9adc4
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934004"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Azure Veri Gezgini kullanarak Azure Izleyici 'de verileri sorgulama (Önizleme)

Azure Veri Gezgini proxy kümesi (ADX proxy), Azure [izleyici](/azure/azure-monitor/) hizmetinde Azure Veri Gezgini, [Application Insights (aı)](/azure/azure-monitor/app/app-insights-overview)ve [Log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs) arasında çapraz ürün sorguları gerçekleştirmenizi sağlayan bir varlıktır. Azure Izleyici Log Analytics çalışma alanlarını veya Application Insights uygulamalarını bir proxy kümesi olarak eşleyebilirsiniz. Daha sonra Azure Veri Gezgini araçları 'nı kullanarak proxy kümesini sorgulayabilir ve bir çapraz küme sorgusunda buna başvurabilirsiniz. Makalede bir proxy kümesine bağlanma, Azure Veri Gezgini Web Kullanıcı arabirimine bir proxy kümesi ekleme ve Azure Veri Gezgini ait AI uygulamalarınızda veya LA çalışma alanlarınızda sorgu çalıştırma işlemlerinin nasıl yapılacağı gösterilir.

Azure Veri Gezgini proxy akışı: 

![ADX proxy akışı](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Önkoşullar

> [!NOTE]
> ADX proxy, önizleme modunda. Bu özelliği etkinleştirmek için [Adxproxy](mailto:adxproxy@microsoft.com) ekibine başvurun.

## <a name="connect-to-the-proxy"></a>Ara sunucuya Bağlan

1. Log Analytics veya Application Insights kümenize bağlanmadan önce, Azure Veri Gezgini yerel kümenizin ( *Yardım* kümesi gibi) sol menüde göründüğünü doğrulayın.

    ![ADX yerel kümesi](media/adx-proxy/web-ui-help-cluster.png)

1. Azure Veri Gezgini Kullanıcı arabiriminde (https://dataexplorer.azure.com/clusters) **küme Ekle**' yi seçin.

1. **Küme Ekle** penceresinde:

    * URL 'YI LA veya AI kümesine ekleyin. Örneğin, `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * **Add (Ekle)** seçeneğini belirleyin.

    ![Küme ekleme](media/adx-proxy/add-cluster.png)

    Birden fazla proxy kümesine bağlantı eklerseniz, her birine farklı bir ad verin. Aksi takdirde, sol bölmede hepsi aynı ada sahip olacaktır.

1. Bağlantı kurulduktan sonra, LA veya AI kümeniz yerel ADX kümenizin sol bölmesinde görünür. 

    ![Log Analytics ve Azure Veri Gezgini kümeleri](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Sorgu çalıştırma

Proxy kümelerini sorgulamak için kusto Explorer, ADX web Explorer, Jupyıter Könmagic veya REST API kullanabilirsiniz. 

> [!TIP]
> * Veritabanı adı, proxy kümesinde belirtilen kaynakla aynı ada sahip olmalıdır. Adlar büyük/küçük harfe duyarlıdır.
> * Küme içi sorgularda, Application Insights uygulamalar ve Log Analytics çalışma alanlarının adlandırılmasının doğru olduğundan emin olun.
>     * Adlar özel karakterler içeriyorsa, bunlar proxy kümesi adındaki URL kodlamasıyla değiştirilirler. 
>     * Adlar, [KQL tanımlayıcı adı kurallarını](/azure/kusto/query/schema-entities/entity-names)karşılamayan karakterler içeriyorsa, bunlar Dash **-** karakteriyle değiştirilmiştir.

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Yerel Azure Veri Gezgini kümesinde sorgulama 

Azure Veri Gezgini kümenizdeki sorguları çalıştırın ( *Yardım* kümesinde *stormevents* tablosu gibi). Sorguyu çalıştırırken, sol bölmede yerel Azure Veri Gezgini kümenizin seçildiğini doğrulayın.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![StormEvents tablosunu sorgula](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>LA veya AI kümenize göre sorgulama

Sorguları LA veya AL kümenizde çalıştırdığınızda, sol bölmede LA veya AI kümenizin seçildiğini doğrulayın. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Sorgu LA çalışma alanı](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>ADX proxy 'sinden LA veya AI kümenizi sorgulama  

Proxy 'den LA veya AI kümenizde sorgular çalıştırdığınızda, sol bölmede ADX yerel kümenizin seçildiğini doğrulayın. Aşağıdaki örnek, yerel ADX kümesini kullanarak LA çalışma alanının bir sorgusunu gösterir

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Azure Veri Gezgini proxy 'den sorgulama](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>KAX proxy 'sinden LA veya AI kümesinin çapraz sorgusu ve ADX kümesi 

Proxy 'den çapraz küme sorguları çalıştırdığınızda, sol bölmede ADX yerel kümenizin seçildiğini doğrulayın. Aşağıdaki örneklerde, ADX küme tablolarını (kullanılarak `union`) La çalışma alanıyla birleştirme gösterilmektedir.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Azure Veri Gezgini proxy 'den çapraz sorgu](media/adx-proxy/cross-query-adx-proxy.png)

Birleşim yerine [`hint`](/azure/kusto/query/joinoperator#join-hints) işleci kullanmak için, bir Azure Veri Gezgini yerel kümesinde (proxy üzerinde değil) çalıştırmak için bir de gerekebilir. [ `join` ](/azure/kusto/query/joinoperator) 

## <a name="additional-syntax-examples"></a>Ek sözdizimi örnekleri

Application Insights (AI) veya Log Analytics (LA) kümeleri çağrılırken aşağıdaki sözdizimi seçenekleri mevcuttur:

|Sözdizimi açıklaması  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Bu abonelikte yalnızca tanımlı kaynağı içeren bir küme içindeki veritabanı (**çapraz küme sorguları için önerilir**) |   küme (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | küme (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Bu abonelikteki tüm uygulamaları/çalışma alanlarını içeren küme    |     küme (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    küme (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Abonelikteki tüm uygulamaları/çalışma alanlarını içeren ve bu kaynak grubunun üyesi olan küme    |   küme (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    küme (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Bu abonelikte yalnızca tanımlı kaynağı içeren küme      |    küme (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  küme (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Sonraki adımlar

[Sorgu yazma](write-queries.md)
