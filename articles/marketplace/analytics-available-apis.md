---
title: Ticari Market Analytics verilerine erişim için API 'Ler
description: Iş Ortağı Merkezi 'nde analiz verilerine programlı bir şekilde erişmek için bu API 'Leri kullanın.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: ed6d658155267ab21fd4cdd28dd50fcbb258ee90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584253"
---
# <a name="apis-for-accessing-commercial-marketplace-analytics-data"></a>Ticari Market Analytics verilerine erişim için API 'Ler

Ticari Market Analytics verilerine ve bunlarla ilişkili işlevlere erişmek için API 'lerin listesi aşağıda verilmiştir.

- [Veri kümesi çekme API 'Leri](#dataset-pull-apis)
- [Sorgu yönetimi API 'Leri](#query-management-apis)
- [Rapor yönetimi API 'Leri](#report-management-apis)
- [Rapor yürütme çekme API 'Leri](#report-execution-pull-apis)

## <a name="dataset-pull-apis"></a>Veri kümesi çekme API 'Leri

***Tablo 1: veri kümesi çekme API 'Leri***

| **API** | **İşlev** |
| --- | --- |
| [Tüm veri kümelerini al](analytics-api-get-all-datasets.md) | Tüm kullanılabilir veri kümelerini alır. Veri kümeleri tabloları, sütunları, ölçümleri ve zaman aralıklarını listeler. |
|||

## <a name="query-management-apis"></a>Sorgu yönetimi API 'Leri

***Tablo 2: sorgu yönetimi API 'Leri***

| **API** | **İşlev** |
| --- | --- |
| [Rapor sorgusu oluştur](analytics-programmatic-access.md#create-report-query-api) | Sütunların ve ölçümlerin verilmesi gereken veri kümesini tanımlayan özel sorgular oluşturur. |
| [Rapor sorgularını al](analytics-api-get-report-queries.md) | Raporlarda kullanıma sunulan tüm sorguları alır. Tüm sistem ve Kullanıcı tanımlı sorguları varsayılan olarak alır. |
| [Rapor sorgularını SIL](analytics-api-delete-report-queries.md) | Kullanıcı tanımlı sorguları siler. |
|||

## <a name="report-management-apis"></a>Rapor yönetimi API 'Leri

***Tablo 3: rapor yönetim API 'Leri***

| **API** | **İşlev** |
| --- | --- |
| [Rapor Oluştur](analytics-programmatic-access.md#create-report-api) | Düzenli aralıklarla yürütülecek bir sorgu zamanlar. |
| [Rapor sorgularını deneyın](analytics-api-try-report-queries.md) | Bir rapor sorgu ekstresi yürütür. Yalnızca bir ortağın, verilerin beklendiğini doğrulamak için kullanabileceği 10 kayıt döndürür. |
| [Rapor Al](analytics-api-get-report.md) | Zamanlanan tüm raporları alın. |
| [Raporu Güncelleştir](analytics-api-update-report.md) | Rapor parametresini değiştirme. |
| [Raporu Sil](analytics-api-delete-report.md) | Tüm rapor ve rapor yürütme kayıtlarını siler. |
| [Rapor yürütmelerini Duraklat](analytics-api-pause-report-executions.md) | Raporların zamanlanan yürütmesini duraklatır. |
| [Rapor yürütmelerini sürdürür](analytics-api-resume-report-executions.md) | Duraklatılmış bir raporun zamanlanan yürütmesini sürdürür. |
|||

## <a name="report-execution-pull-apis"></a>Rapor yürütme çekme API 'Leri

***Tablo 4: rapor yürütme çekme API 'Leri***

| **API** | **İşlev** |
| --- | --- |
| [Rapor yürütmelerini al](analytics-programmatic-access.md#get-report-executions-api) | Belirli bir rapor için gerçekleşen tüm yürütmeleri alın. |
|||

## <a name="next-steps"></a>Sonraki adımlar

- [Swagger API URL 'si](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html)aracılığıyla API 'leri deneyebilirsiniz.
