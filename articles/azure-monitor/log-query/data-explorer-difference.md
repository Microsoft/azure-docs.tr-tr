---
title: Azure Izleyici günlük sorgusu dil farklılıkları | Microsoft Docs
description: Azure Izleyici tarafından kullanılan kusto sorgu dili için başvuru bilgileri. Azure izleyici günlük sorgularında desteklenmeyen Azure Izleyici ve öğelerine özgü ek öğeler içerir.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 16a221c0530d65e11589ac272a8e032de0cd3d2b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933093"
---
# <a name="azure-monitor-log-query-language-differences"></a>Azure Izleyici günlük sorgusu dil farklılıkları

[Azure izleyici 'deki Günlükler](log-query-overview.md) [Azure Veri Gezgini](/azure/data-explorer) kurulmuştur ve aynı [kusto sorgu dilini](/azure/kusto/query)kullandığından, dilin sürümünde bazı farklılıklar olur. Bu makalede, Veri Gezgini için kullanılan dilin sürümü ve Azure Izleyici günlük sorguları için kullanılan sürüm arasında farklı olan öğeler tanımlanmaktadır.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>KQL öğeleri Azure Izleyici 'de desteklenmiyor
Aşağıdaki bölümlerde, Azure Izleyici tarafından desteklenmeyen kusto sorgu dilinin öğeleri açıklanır.

### <a name="statements-not-supported-in-azure-monitor"></a>Azure Izleyici 'de desteklenmeyen deyimler

* [Ek](/azure/kusto/query/aliasstatement)
* [Sorgu parametreleri](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Azure Izleyici 'de desteklenmeyen işlevler

* [küme ()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [veritabanı ()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Azure Izleyici 'de desteklenmeyen işleçler

* [Çapraz küme birleşimi](/azure/kusto/query/joincrosscluster)
* [externaldata işleci](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-azure-monitor"></a>Eklentiler Azure Izleyici 'de desteklenmiyor

* [Python eklentisi](/azure/kusto/query/pythonplugin)
* [sql_request eklentisi](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Azure Izleyici 'de ek işleçler
Aşağıdaki operatörler belirli Azure Izleyici özelliklerini destekler ve Azure Izleyici dışında kullanılamaz.

* [uygulama ()](app-expression.md)
* [çalışma alanı ()](workspace-expression.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure izleyici günlük sorgularını yazmak için farklı kaynaklara](query-language.md)başvurular alın.
- [Kusto sorgu dili için tüm başvuru belgelerine](/azure/kusto/query/)erişin.
