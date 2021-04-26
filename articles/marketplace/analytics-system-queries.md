---
title: Sistem sorgularının listesi
description: Microsoft ticari Market 'teki tekliflerinizi kullanarak analiz verilerini program aracılığıyla almak için kullanabileceğiniz sistem sorguları hakkında bilgi edinin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f2b5f7eb559e349947f88067a3d2ea53d99b7cbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584196"
---
# <a name="list-of-system-queries"></a>Sistem sorgularının listesi

Aşağıdaki sistem sorguları, [rapor oluşturma API](analytics-programmatic-access.md#create-report-api) 'sinde doğrudan ile kullanılabilir `QueryId` . Sistem sorguları, altı aylık (6M) hesaplama dönemi için Iş Ortağı Merkezi 'nde dışarı aktarma raporlarına benzer.

Sütun adları, öznitelikler ve açıklama hakkında daha fazla bilgi için şu makalelere bakın:

- [Ticari Market Analytics 'te müşteriler panosu](customer-dashboard.md#customer-details-table)
- [Ticari market analizinde Siparişler panosu](orders-dashboard.md#orders-details-table)
- [Ticari Market Analytics 'teki kullanım panosu](usage-dashboard.md#usage-details-table)
- [Ticari market analizinde Market İçgörüleri](insights-dashboard.md#marketplace-insights-details-table)

Aşağıdaki bölümlerde çeşitli raporlar için rapor sorguları sağlanmaktadır.

## <a name="customers-report-query"></a>Müşteriler rapor sorgusu

**Rapor açıklaması**: son 6DK için müşteriler raporu

**Sorgu kimliği**:  `b9df4929-073f-4795-b0cb-a2c81b11e28d`

**Rapor sorgusu**:

`SELECT MarketplaceSubscriptionId,DateAcquired,DateLost,ProviderName,ProviderEmail,FirstName,LastName,Email,CustomerCompanyName,CustomerCity,CustomerPostalCode,CustomerCommunicationCulture,CustomerCountryRegion,AzureLicenseType,PromotionalCustomers,CustomerState,CommerceRootCustomer,CustomerId,BillingAccountId,ID  FROM ISVCustomer TIMESPAN LAST_6_MONTHS`

## <a name="orders-report-query"></a>Rapor sorgusu siparişi

**Rapor açıklaması**: son 6DK için sipariş raporu

**Sorgu kimliği**:  `fd0f299c-5a1c-4929-9f48-bfc6cc44355d`

**Rapor sorgusu**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,Sku,CustomerCountry,IsPreviewSKU,OrderId,OrderQuantity,CloudInstanceName,IsNewCustomer,OrderStatus,OrderCancelDate,CustomerCompanyName,CustomerName,OrderPurchaseDate,OfferName,TrialEndDate,CustomerId,BillingAccountId FROM ISVOrder TIMESPAN LAST_6_MONTHS`

## <a name="usage-report-queries"></a>Kullanım raporu sorguları

**Rapor açıklaması**: son 6d Için VM normalleştirilmiş kullanım raporu

**Sorgu kimliği**:  `2c6f384b-ad52-4aed-965f-32bfa09b3778`

**Rapor sorgusu**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,NormalizedUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Rapor açıklaması**: son 6d Için VM ham kullanım raporu

**Sorgu kimliği**:  `3f19fb95-5bc4-4ee0-872e-cedd22578512`

**Rapor sorgusu**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,RawUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Rapor açıklaması**: son 6DK için ölçülen kullanım raporu

**Sorgu kimliği**:  `f0c4927f-1f23-4c99-be4a-1371a5a9a086`

**Rapor sorgusu**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,MeteredUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('SaaS', 'Azure Applications') TIMESPAN LAST_6_MONTHS`

## <a name="marketplace-insights-report-query"></a>Market öngörüleri rapor sorgusu

**Rapor açıklaması**: son 6DK Için Market öngörüleri raporu

**Sorgu kimliği**:  `6fd7624b-aa9f-42df-a61d-67d42fd00e92`

**Rapor sorgusu**:

`Date,OfferName,ReferralDomain,CountryName,PageVisits,GetItNow,ContactMe,TestDrive,FreeTrial FROM ISVMarketplaceInsights TIMESPAN LAST_6_MONTHS`

## <a name="next-steps"></a>Sonraki adımlar

- [Ticari Market Analytics verilerine erişim için API 'Ler](analytics-available-apis.md)
- [Ticari Market Analytics verilerine erişmek için örnek uygulama](analytics-sample-application.md)
