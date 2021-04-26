---
title: Programlı analiz için örnek sorgular
description: Microsoft Commercial Market Analytics verilerine programlı bir şekilde erişmek için bu örnek sorguları kullanın.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7d788448fb3f8a849f79e43fcb0737898f4c9e15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584197"
---
# <a name="sample-queries-for-programmatic-analytics"></a>Programlı analiz için örnek sorgular

Bu makalede, Microsoft ticari Market siparişlerine, kullanımına ve müşteri raporlarına yönelik örnek sorgular sağlanmaktadır. Bu sorguları [rapor sorgusu oluştur](analytics-programmatic-access.md#create-report-query-api) API uç noktasını çağırarak kullanabilirsiniz. Gerekirse, daha fazla sütun eklemek, hesaplama dönemini ayarlamak ve filtre koşulları eklemek için [rapor sorgusu oluştur](analytics-programmatic-access.md#create-report-query-api) API çağrısı değiştirilebilir. Desteklenen süre (6 milyon), 12 ay (12D) ve özel zaman aralığı.

Sütun adları, öznitelikler ve açıklamalar hakkındaki ayrıntılar için aşağıdaki tablolara bakın:

- [Müşteri Ayrıntıları tablosu](customer-dashboard.md#customer-details-table)
- [Sipariş Ayrıntıları tablosu](orders-dashboard.md#orders-details-table)
- [Kullanım Ayrıntıları tablosu](usage-dashboard.md#usage-details-table)

## <a name="customers-report-queries"></a>Müşteriler rapor sorguları

Bu örnek sorgular müşteriler raporuna uygulanır.

| **Sorgu açıklaması** | **Örnek sorgu** |
| --- | --- |
| Seçtiğiniz tarihe kadar iş ortağının etkin müşterileri | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 1` |
| Seçtiğiniz tarihe kadar iş ortağının müşterileri | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 0` |
| Son altı ay içinde belirli bir Coğrafya 'daki yeni müşterilerin listesi | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE DateAcquired <= ‘2020-06-30’ AND CustomerCountryRegion = ‘United States’` |
|||

## <a name="usage-report-queries"></a>Kullanım raporu sorguları

Bu örnek sorgular kullanım raporu için geçerlidir.

| **Sorgu açıklaması** | **Örnek sorgu** |
| --- | --- |
| Son 6M için "Azure üzerinden faturalandırılan" Market lisans türü için sanal makine (VM) normalleştirilmiş kullanımı | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Son 12M için "Azure üzerinden faturalandırılan" Market lisans türü için VM ham kullanımı | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_1_YEAR` |
| Son 6Dk için "kendi lisansını getir" Market lisans türü için VM normalleştirilmiş kullanımı | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Son 6Dk için "kendi lisansını getir" Market lisans türü için VM RAW kullanımı | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Son aya ait ücretli planlar için kullanım tarihini, günlük toplam normalleştirilmiş kullanımı ve "tahmini genişletilmiş ücretler (PC/CC)" temelinde | `SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST_MONTH` |
| Son aya ait ücretli planlar için kullanım tarihini, günlük toplam ham kullanımı ve "tahmini genişletilmiş ücretler (PC/CC)" temelinde | `SELECT UsageDate, RawUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST\_MONTH` |
| Belirli bir teklif adı için, son 6M için "Azure üzerinden faturalandırılan" Market lisans türü için VM normalleştirilmiş kullanımı | `SELECT OfferName, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Belirli bir teklif adı için, son 6Dk için ölçülen kullanım | `SELECT OfferName, MeteredUsage FROM ISVUsage WHERE OfferName = ‘Example Offer Name’ AND OfferType IN (‘SaaS’, ‘Azure Applications’) TIMESPAN LAST_6_MONTHS` |
|||

## <a name="orders-report-queries"></a>Rapor sorgularını siparişler

Bu örnek sorgular, siparişler raporu için geçerlidir.

| **Sorgu açıklaması** | **Örnek sorgu** |
| --- | --- |
| Son 6Dk için Azure lisans türü için "kuruluş" olarak rapor siparişi | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Enterprise’ TIMESPAN LAST\_6\_MONTHS` |
| Son 6Dk için Azure lisans türü için rapor siparişi "Kullandıkça öde" | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Pay as You Go’ TIMESPAN LAST_6_MONTHS` |
| Son 6d için belirli teklif adı için sipariş raporu | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Son 6Dk için etkin siparişler için rapor siparişi | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| Son 6Dk için iptal edilen siparişlerin rapor siparişi | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Cancelled’ TIMESPAN LAST_6_MONTHS` |
|||

## <a name="next-steps"></a>Sonraki adımlar

- [Ticari Market Analytics verilerine erişim için API 'Ler](analytics-available-apis.md)
