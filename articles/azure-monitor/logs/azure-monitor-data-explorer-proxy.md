---
title: Azure Izleyici kullanarak Azure Veri Gezgini çapraz kaynak sorgulama
description: Azure izleyici 'de Azure Veri Gezgini, Log Analytics çalışma alanları ve klasik Application Insights uygulamaları arasında çapraz ürün sorguları gerçekleştirmek için Azure Izleyici 'yi kullanın.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: a800f78df26ce76144994bb9da2cac6271323eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419431"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>Azure Izleyici kullanarak Azure Veri Gezgini çapraz kaynak sorgulama
Azure Izleyici, Azure Veri Gezgini, [Application Insights](../app/app-insights-overview.md)ve [Log Analytics](../logs/data-platform-logs.md)arasında çapraz hizmet sorgularını destekler. Daha sonra, Log Analytics/Application Insights araçlarını kullanarak Azure Veri Gezgini kümenizi sorgulayabilir ve bir çapraz hizmet sorgusunda buna başvurabilirsiniz. Makalesinde, bir çapraz hizmet sorgusunun nasıl yapılacağı gösterilir.

Aşağıdaki diyagramda Azure Izleyici çapraz hizmet akışı gösterilmektedir:

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Bir Kullanıcı, Azure Izleyici, proxy ve Azure Veri Gezgini arasındaki sorguların akışını gösteren diyagram.":::

>[!NOTE]
> Azure Izleyici çapraz hizmet sorgusu genel önizlemede. Herhangi bir soru ile [hizmet ekibine](mailto:ADXProxy@microsoft.com) başvurun.

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Log Analytics veya Application Insights kaynaklarınızı ve Azure Veri Gezgini çapraz sorgulama

Kusto sorgularını destekleyen istemci araçlarını kullanarak, çapraz kaynak sorguları çalıştırabilirsiniz. Bu araçlara örnek olarak Log Analytics Web Kullanıcı arabirimi, çalışma kitapları, PowerShell ve REST API verilebilir.

Model içindeki bir sorgudaki bir Azure Veri Gezgini kümesi için tanımlayıcıyı `adx` , ardından veritabanı adını ve tabloyu girin.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Bir çapraz hizmet sorgusuna bir örnek gösteren ekran görüntüsü.":::

> [!NOTE]
>* Veritabanı adları büyük/küçük harfe duyarlıdır.
>* Uyarı olarak çapraz kaynak sorgusu desteklenmez.
>* Kümedeki zaman damgası sütununun tanımlanması desteklenmez; Log Analytics sorgu API 'SI zaman filtresi içinde geçmeyecektir

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>Azure Veri Gezgini küme tablolarını bir Log Analytics çalışma alanıyla birleştirme

`union`Küme tablolarını bir Log Analytics çalışma alanıyla birleştirmek için komutunu kullanın.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Birleşim komutuyla bir çapraz hizmet sorgu örneği gösteren ekran görüntüsü.":::

> [!Tip]
> Kısayol biçimine izin veriliyor: *clustername* / *InitialCatalog*. Örneğin, `adx('help/Samples')` öğesine çevrilir `adx('help.kusto.windows.net/Samples')` .

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Azure Izleyici kaynağı ile bir Kiracıdaki Azure Veri Gezgini kümesinden veri ekleme

Hizmetler arasında çapraz kiracı sorguları desteklenmez. Her iki kaynağa yayılan sorguyu çalıştırmak için tek bir kiracıya oturum açtınız.

Azure Veri Gezgini kaynağı kiracı A 'dedir ve Log Analytics çalışma alanı B kiracısında ise aşağıdaki yöntemlerden birini kullanın:

*  Azure Veri Gezgini, farklı kiracılardaki sorumlular için roller eklemenize olanak tanır. Kullanıcı KIMLIĞINIZI Azure Veri Gezgini kümesinde yetkili bir kullanıcı olarak B kiracısına ekleyin. Azure Veri Gezgini kümesindeki [Trustedexternaltenant](/powershell/module/az.kusto/update-azkustocluster) özelliğinin b kiracısı içerdiğini doğrulayın. çapraz sorguyu b kiracısı içinde tam olarak çalıştırın.
*  Azure Izleyici kaynağını A kiracısına [eklemek için açık bir kullanım kullanın](../../lighthouse/index.yml) .

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Farklı kiracılardan Azure Veri Gezgini kümelerine bağlanma

Kusto Explorer, Kullanıcı hesabının ilk ait olduğu kiracıya otomatik olarak oturum açar. Aynı kullanıcı hesabına sahip diğer kiracılardaki kaynaklara erişmek için, bağlantı dizesinde açıkça belirtmeniz gerekir `TenantId` :

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>Sonraki adımlar
* [Sorgu yazma](/azure/data-explorer/write-queries)
* [Azure Veri Gezgini kullanarak Azure Izleyici 'de verileri sorgulama](/azure/data-explorer/query-monitor-data)
* [Azure Izleyici 'de çapraz kaynak günlük sorguları gerçekleştirme](../logs/cross-workspace-query.md)