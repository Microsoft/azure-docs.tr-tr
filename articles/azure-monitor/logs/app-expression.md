---
title: Azure Izleyici günlük sorgularındaki App () ifadesi | Microsoft Docs
description: Uygulama ifadesi bir Azure Izleyici günlük sorgusunda aynı kaynak grubunda, başka bir kaynak grubunda veya başka bir abonelikte bulunan belirli bir Application Insights uygulamasından verileri almak için kullanılır.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2019
ms.openlocfilehash: 9d60857503402554381068fc36121fa3f7b61622
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107283567"
---
# <a name="app-expression-in-azure-monitor-query"></a>Azure Izleyici sorgusunda App () ifadesi

Bu `app` ifade, bir Azure izleyici sorgusunda aynı kaynak grubunda, başka bir kaynak grubunda veya başka bir abonelikte bulunan belirli bir Application Insights uygulamasından verileri almak için kullanılır. Bu, uygulama verilerini bir Azure Izleyici günlük sorgusuna eklemek ve bir Application Insights sorgusunda birden çok uygulama arasında veri sorgulamak için yararlıdır.

> [!IMPORTANT]
> Günlük verileri bir Log Analytics çalışma alanında depolandığından, bu, bir [çalışma alanı tabanlı Application Insights kaynağı](../app/create-workspace-resource.md) kullanıyorsanız, uygulama () ifadesi kullanılmaz. Birden çok çalışma alanındaki uygulamayı içeren bir sorgu yazmak için Workspace () ifadesini kullanın. Aynı çalışma alanındaki birden çok uygulama için, bir çapraz çalışma alanı sorgusuna gerek yoktur.

## <a name="syntax"></a>Syntax

`app(`*Tanımlayıcısını*`)`


## <a name="arguments"></a>Bağımsız değişkenler

- *Tanımlayıcı*: aşağıdaki tablodaki biçimlerden birini kullanarak uygulamayı tanımlar.

| Tanımlayıcı | Açıklama | Örnek
|:---|:---|:---|
| Kaynak Adı | Uygulamanın okunabilir adı ("bileşen adı" olarak da bilinir) | uygulama ("fabrikamapp") |
| Tam ad | Şu biçimdeki uygulamanın tam adı: "subscriptionName/resourceGroup/componentName" | uygulama (' AI-Prototype/fabrikam/fabrikamapp ') |
| ID | Uygulamanın GUID 'SI | uygulama ("988ba129-363e-4415-8fe7-8cbe5447518") |
| Azure Kaynak Kimliği | Azure kaynağı için tanımlayıcı |uygulama ("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Notlar

* Uygulamaya okuma erişiminizin olması gerekir.
* Bir uygulamayı adına göre tanımlamak, tüm erişilebilir abonelikler genelinde benzersiz olduğunu varsayar. Belirtilen ada sahip birden çok uygulamanız varsa, belirsizlik nedeniyle sorgu başarısız olur. Bu durumda, diğer tanımlayıcılardan birini kullanmanız gerekir.
* Log Analytics çalışma alanlarını sorgulamak için ilgili ifade [çalışma alanını](../logs/workspace-expression.md) kullanın.
* Bir Application Insights uygulama, uyarı kuralı için kaynak olarak kullanılmadığı sürece, bir [özel günlük araması uyarı kuralı](../alerts/alerts-log.md)oluşturmak için Azure Portal kullanılırken uygulama () ifadesi şu anda desteklenmiyor.

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

- Bkz. bir Log Analytics çalışma alanına başvurmak için [çalışma alanı ifadesi](../logs/workspace-expression.md) .
- [Azure izleyici verilerinin](./log-query-overview.md) nasıl depolandığı hakkında bilgi edinin.
- [Kusto sorgu dili](/azure/kusto/query/)için tam belgelere erişin.
