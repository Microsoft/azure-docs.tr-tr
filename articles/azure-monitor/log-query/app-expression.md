---
title: Azure Izleyici günlük sorgularındaki App () ifadesi | Microsoft Docs
description: Uygulama ifadesi bir Azure Izleyici günlük sorgusunda aynı kaynak grubunda, başka bir kaynak grubunda veya başka bir abonelikte bulunan belirli bir Application Insights uygulamasından verileri almak için kullanılır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/25/2019
ms.openlocfilehash: ffef73f88c8679d0b0be81222b1b61c4eaef5098
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933091"
---
# <a name="app-expression-in-azure-monitor-query"></a>Azure Izleyici sorgusunda App () ifadesi

`app` ifadesi bir Azure Izleyici sorgusunda aynı kaynak grubundaki belirli bir Application Insights uygulamasından, başka bir kaynak grubunda veya başka bir abonelikte veri almak için kullanılır. Bu, uygulama verilerini bir Azure Izleyici günlük sorgusuna eklemek ve bir Application Insights sorgusunda birden çok uygulama arasında veri sorgulamak için yararlıdır.



## <a name="syntax"></a>Sözdizimi

`app(`*tanımlayıcı*`)`


## <a name="arguments"></a>Bağımsız Değişkenler

- *Tanımlayıcı*: aşağıdaki tablodaki biçimlerden birini kullanarak uygulamayı tanımlar.

| Tanımlayıcı | Açıklama | Örnek
|:---|:---|:---|
| Kaynak Adı | Uygulamanın okunabilir adı (DIĞER adıyla "bileşen adı") | uygulama ("fabrikamapp") |
| Tam ad | Şu biçimdeki uygulamanın tam adı: "subscriptionName/resourceGroup/componentName" | uygulama (' AI-Prototype/fabrikam/fabrikamapp ') |
| Kimlik | Uygulamanın GUID 'SI | uygulama ("988ba129-363e-4415-8fe7-8cbe5447518") |
| Azure Kaynak KIMLIĞI | Azure kaynağı için tanımlayıcı |uygulama ("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Notlar

* Uygulamaya okuma erişiminizin olması gerekir.
* Bir uygulamayı adına göre tanımlamak, tüm erişilebilir abonelikler genelinde benzersiz olduğunu varsayar. Belirtilen ada sahip birden çok uygulamanız varsa, belirsizlik nedeniyle sorgu başarısız olur. Bu durumda, diğer tanımlayıcılardan birini kullanmanız gerekir.
* Log Analytics çalışma alanlarını sorgulamak için ilgili ifade [çalışma alanını](workspace-expression.md) kullanın.
* Bir Application Insights uygulama, uyarı kuralı için kaynak olarak kullanılmadığı sürece, bir [özel günlük araması uyarı kuralı](../platform/alerts-log.md)oluşturmak için Azure Portal kullanılırken uygulama () ifadesi şu anda desteklenmiyor.

## <a name="examples"></a>Örnekler

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. bir Log Analytics çalışma alanına başvurmak için [çalışma alanı ifadesi](workspace-expression.md) .
- [Azure izleyici verilerinin](../../azure-monitor/log-query/log-query-overview.md) nasıl depolandığı hakkında bilgi edinin.
- [Kusto sorgu dili](/azure/kusto/query/)için tam belgelere erişin.
