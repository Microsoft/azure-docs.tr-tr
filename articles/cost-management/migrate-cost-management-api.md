---
title: Kurumsal Anlaşma 'den Microsoft müşteri anlaşması API 'Lerine geçiş-Azure | Microsoft Docs
description: Bu makale, Microsoft Kurumsal Anlaşma (EA) ' ın bir Microsoft Müşteri sözleşmesine geçirmesinin sonuçlarını anlamanıza yardımcı olur.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: ee4b2196240ceff1351b7ea310d9660ed613d075
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73152070"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Kurumsal Anlaşma 'den Microsoft Müşteri Sözleşmesi API 'Lerine geçiş

Bu makale, Kurumsal Anlaşma (EA) ve Microsoft Müşteri Sözleşmesi (MCA) hesapları arasındaki veri yapısını, API 'yi ve diğer sistem tümleştirme farklarını anlamanıza yardımcı olur. Azure maliyet yönetimi, her iki hesap türü için API 'Leri destekler. Devam etmeden önce Microsoft müşteri anlaşması [için faturalandırma hesabı](../billing/mca-setup-account.md) ' nı gözden geçirin.

Mevcut bir EA hesabı olan kuruluşlar, bu makaleyi bir MCA hesabı ayarıyla birlikte incelemelidir. Daha önce, bir EA hesabının yenilenmesi, eski bir kayıttan yenisine geçiş yapmak için az sayıda iş gerektirir. Ancak, bir MCA hesabına geçiş için ek çaba gerekir. Daha fazla çaba, temel alınan faturalandırma alt sistemindeki değişikliklerden kaynaklanır ve bu da tüm maliyetlerle ilgili API 'Leri ve hizmet tekliflerini etkiler.

## <a name="mca-apis-and-integration"></a>MCA API 'Leri ve Tümleştirme

MCA API 'Leri ve yeni tümleştirme şunları yapmanıza olanak sağlar:

- Yerel Azure API 'Leri aracılığıyla tüm API kullanılabilirliği vardır.
- Tek bir faturalandırma hesabında birden çok fatura yapılandırın.
- Azure hizmet kullanımı, üçüncü taraf Market kullanımı ve Market satın alımları ile birleştirilmiş bir API 'ye erişin.
- Azure maliyet yönetimi 'ni kullanarak faturalandırma profillerindeki (kayıtlarla aynı) maliyetleri görüntüleyin.
- Maliyetleri göstermek, maliyetler önceden tanımlanmış eşikleri aştığında bildirim almak ve ham verileri otomatik olarak dışarı aktarmak için yeni API 'Lere erişin.

## <a name="migration-checklist"></a>Geçiş denetim listesi

Aşağıdaki öğeler, MCA API 'Lerine geçişinize yardımcı olur.

- Yeni [Microsoft Müşteri Sözleşmesi faturalandırma hesabı](../billing/billing-mca-overview.md)hakkında bilgi edinin.
- Hangi API 'Leri kullanacağınızı öğrenin ve aşağıdaki bölümde hangi hangilerinin değiştirildiğini görüntüleyin.
- [Azure Resource Manager REST API 'leri](/rest/api/azure)hakkında bilgi edinin.
- Zaten Azure Resource Manager API 'Leri kullanmıyorsanız, [istemci uygulamanızı Azure AD 'ye kaydedin](/rest/api/azure/#register-your-client-application-with-azure-ad).
- [Azure AD kimlik doğrulamasını kullanmak](/rest/api/azure/#create-the-request)için herhangi bir programlama kodunu güncelleştirin.
- EA API çağrılarını, MCA API çağrıları ile değiştirmek için herhangi bir programlama kodunu güncelleştirin.
- Yeni hata kodlarını kullanmak için hata işlemeyi güncelleştirin.
- Cloudyn ve Power BI gibi diğer tümleştirme tekliflerini gözden geçirin ve diğer gerekli işlemleri yapın.

## <a name="ea-apis-replaced-with-mca-apis"></a>EA API 'Leri, MCA API 'Leriyle değiştirilmiştir

EA API 'Leri, kimlik doğrulama ve yetkilendirme için bir API anahtarı kullanır. MCA API 'Leri Azure AD kimlik doğrulamasını kullanır.

| Amaç | EA APı 'SI | MCA APı 'SI |
| --- | --- | --- |
| Bakiye ve krediler | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft. faturalandırma/billingAccounts/billingProfiles/availableBalanceussae |
| Kullanım (JSON) | [/UsageDetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usageayrýntýbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft. tüketim/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Kullanım (CSV) | [/UsageDetails/Download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/UsageDetails/gönder](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft. tüketim/usageDetails/Download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Market kullanımı (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft. tüketim/usageDetails/Download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Fatura dönemleri | [/billingdönemler](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft. faturalandırma/billingAccounts/billingProfiles/faturalar |
| Fiyat listesi | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft. faturalandırma/billingAccounts/billingProfiles/fiyat listesi/varsayılan/indirme biçimi = JSON|CSV Microsoft. faturalandırma/billingAccounts/.../billingProfiles/.../faturalar/... /pricesheet/default/Download Format = JSON|CSV Microsoft. faturalandırma/billingAccounts/.. /Billingprofiles/.exe. /providers/Microsoft.Consumption/pricesheets/download  |
| Rezervasyon satın almaları | [/rezervationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft. faturalandırma/billingAccounts/billingProfiles/işlemler |
| Ayırma önerileri | [/Sharedrezervationönerilere](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft. tüketim/Rezervationönerilere](/rest/api/consumption/reservationrecommendations/list) |
| Ayırma kullanımı | [/rezervationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/rezervationözetler](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft. tüketim/Rezervationdetails](/rest/api/consumption/reservationsdetails)[Microsoft. tüketim/rezervationözetler](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> Azure hizmeti ve üçüncü taraf Market kullanımı, [kullanım ayrıntıları API 'si](/rest/api/consumption/usagedetails)ile kullanılabilir.

Aşağıdaki API 'Ler, MCA faturalandırma hesapları için kullanılabilir:

| Amaç | Microsoft Müşteri Sözleşmesi (MCA) API 'SI |
| --- | --- |
| Faturalandırma hesapları<sup>2</sup> | Microsoft. faturalandırma/billingAccounts |
| Faturalandırma profilleri<sup>2</sup> | Microsoft. faturalandırma/billingAccounts/billingProfiles |
| Fatura bölümleri<sup>2</sup> | Microsoft. faturalandırma/billingAccounts/ınvoicesections |
| Faturalar | Microsoft. faturalandırma/billingAccounts/billingProfiles/faturalar |
| Faturalama abonelikleri | {Scope}/Billingabonelikleri |

<sup>2</sup> API 'ler, Azure Portal ve API 'Lerde maliyet yönetimi deneyimlerinin çalıştığı kapsam olan nesnelerin listesini döndürür. Maliyet yönetimi kapsamları hakkında daha fazla bilgi için bkz. [kapsamları anlama ve bunlarla çalışma](understand-work-scopes.md).

Mevcut EA API 'Lerini kullanıyorsanız, bunları MCA faturalandırma hesaplarını destekleyecek şekilde güncelleştirmeniz gerekir. Aşağıdaki tabloda diğer tümleştirme değişiklikleri gösterilmektedir:

| Amaç | Eski teklif | Yeni teklif |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Maliyet Yönetimi](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft tüketim öngörüleri](/power-bi/desktop-connect-azure-consumption-insights) içerik paketi ve Bağlayıcısı | [Microsoft Azure Consumption Insights Power BI uygulama](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) ve [Azure consumption Insights Bağlayıcısı](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>Bakiye ve kredilerin alınacağı API 'Ler

[Get bakiye Özeti](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) API 'si size aylık bir özet verir:

- Bakiyeler
- Yeni satın almalar
- Azure Market hizmet ücretleri
- Düzeltmeler
- Hizmet fazla kullanım ücretleri

Tüm tüketim API 'Leri, kimlik doğrulama ve yetkilendirme için Azure AD kullanan yerel Azure API 'Leri tarafından değiştirilmiştir. Azure REST API 'Leri çağırma hakkında daha fazla bilgi için bkz. [rest ile çalışmaya](/rest/api/azure/#create-the-request)başlama.

Get bakiye Özeti API 'SI Microsoft. faturalandırma/billingAccounts/billingProfiles/Availablebakiye API 'siyle değiştirilmiştir.

Kullanılabilir bakiye API 'SI ile kullanılabilir bakiyeleri almak için:

| Yöntem | İstek URI 'SI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>Maliyet ve kullanım almak için API 'Ler

Azure hizmet kullanımı, üçüncü taraf Market kullanımı ve aşağıdaki API 'lerle diğer Market satın alımlarından oluşan maliyetlerin günlük dökümünü alın. Azure hizmetleri ve üçüncü taraf Market kullanımı için aşağıdaki ayrı API 'Ler birleştirildi. Eski API 'Ler, [Microsoft. tüketim/usageDetails](/rest/api/consumption/usagedetails) API 'siyle değiştirilmiştir. Daha önce yalnızca Bakiye özetinde tarih olarak gösterilen Market satın alımları ekler.

- [Kullanım ayrıntısı al/indir](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Kullanım ayrıntısı al/gönder](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Kullanım ayrıntısı/UsageDetails al](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Kullanım ayrıntısı/usageayrıntılar bycustomdate al](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Market mağaza ücretini/marketplaceücretlerini alın](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Market mağaza ücreti/marketplacechargesbycustomdate al](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Tüm tüketim API 'Leri, kimlik doğrulama ve yetkilendirme için Azure AD kullanan yerel Azure API 'Leri tarafından değiştirilmiştir. Azure REST API 'Leri çağırma hakkında daha fazla bilgi için bkz. [rest ile çalışmaya](/rest/api/azure/#create-the-request)başlama.

Önceki tüm API 'Ler, tüketim/kullanım ayrıntıları API 'siyle değiştirilmiştir.

Kullanım ayrıntıları API 'SI ile kullanım ayrıntılarını almak için:

| Yöntem | İstek URI 'SI |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

Kullanım ayrıntıları API 'SI, tüm maliyet yönetimi API 'Lerinde olduğu gibi birden fazla kapsam ile kullanılabilir. Faturalanan maliyetler için, genellikle bir kayıt düzeyinde alacağınız gibi, Faturalandırma profili kapsamını kullanın.  Maliyet yönetimi kapsamları hakkında daha fazla bilgi için bkz. [kapsamları anlama ve bunlarla çalışma](understand-work-scopes.md).

| Tür | KIMLIK biçimi |
| --- | --- |
| Fatura hesabı | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Faturalama profili | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Abonelik | `/subscriptions/{subscriptionId}` |
| Kaynak grubu | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Herhangi bir programlama kodunu güncelleştirmek için aşağıdaki QueryString parametrelerini kullanın.

| Eski parametreler | Yeni parametreler |
| --- | --- |
| `billingPeriod={billingPeriod}` | Desteklenmiyor |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Yanıtın gövdesi de değişmiştir.

Eski yanıt gövdesi:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Yeni yanıt gövdesi:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

Kullanım kayıtları dizisinin bulunduğu özellik adı, verilerden _değerlere_değişir. Her kayıt, ayrıntılı özelliklerin düz bir listesini almak için kullanılır. Ancak, her bir kayıt artık tüm ayrıntılar, Etiketler hariç _Özellikler_adlı iç içe bir özellikte bulunur. Yeni yapı diğer Azure API 'Leriyle tutarlıdır. Bazı özellik adları değiştirildi. Aşağıdaki tabloda ilgili özellikler gösterilmektedir.

| Eski Özellik | Yeni Özellik | Notlar |
| --- | --- | --- |
| Accoun | Yok | Abonelik Oluşturucu izlenmiyor. Invoicesectionıd (departmentId ile aynı) kullanın. |
| Accountnameaccountownerıd ve AccountOwnerEmail | Yok | Abonelik Oluşturucu izlenmiyor. Invoicesectionname (departmentName ile aynı) kullanın. |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Bu özelliklerin Opposites olduğunu unutmayın. İsAzureCreditEnabled true ise, ChargesBilledSeparately false olur. |
| ConsumedQuantity | miktar | &nbsp; |
| ConsumedService | consumedService | Tam dize değerleri farklı şekilde değişebilir. |
| Tüketimedserviceıd | Hiçbiri | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Date ve usageStartDate | date | &nbsp;  |
| Gün | Hiçbiri | Günü tarih olarak ayrıştırır. |
| DepartmentId | invoiceSectionId | Tam değerler farklı. |
| DepartmentName | invoiceSectionName | Tam dize değerleri farklı şekilde değişebilir. Gerekirse, fatura bölümlerini departmanlara uyacak şekilde yapılandırın. |
| ExtendedCost ve maliyet | Maliyetsiz para birimi | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| Yinelenen ücret | Hiçbiri | &nbsp;  |
| Konum | location | &nbsp;  |
| MeterCategory | meterCategory | Tam dize değerleri farklı şekilde değişebilir. |
| MeterId | meterId | Tam dize değerleri farklı. |
| MeterName | meterName | Tam dize değerleri farklı şekilde değişebilir. |
| MeterRegion | meterRegion | Tam dize değerleri farklı şekilde değişebilir. |
| MeterSubCategory | meterSubCategory | Tam dize değerleri farklı şekilde değişebilir. |
| Ay | Hiçbiri | Ayı tarih olarak ayrıştırır. |
| Teklif Adı | Hiçbiri | PublisherName ve productOrderName kullanın. |
| OfferId | Hiçbiri | &nbsp;  |
| Sipariş Numarası | Hiçbiri | &nbsp;  |
| PartNumber | Hiçbiri | Fiyatları benzersiz şekilde tanımlamak için ölçüm kimliği ve productOrderName kullanın. |
| Plan Adı | productOrderName | &nbsp;  |
| Ürün | Ürün |   |
| ProductID | productId | Tam dize değerleri farklı. |
| Yayımcı Adı | publisherName | &nbsp;  |
| adlı yönetilen örnek, | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | Tam dize değerleri farklı. |
| ResourceLocation | resourceLocation | &nbsp;  |
| Resourcelocationıd | Hiçbiri | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| HizmetYöneticisiKimliği | Yok | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| HizmetAdı | meterCategory | Tam dize değerleri farklı şekilde değişebilir. |
| ServiceTier | meterSubCategory | Tam dize değerleri farklı şekilde değişebilir. |
| StoreServiceIdentifier | Yok | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| kaynak grubundaki | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Etiketler | etiketler | Etiketler özelliği, iç içe geçmiş Özellikler özelliğine değil kök nesne için geçerlidir. |
| UnitOfMeasure | unitOfMeasure | Tam dize değerleri farklı. |
| usageEndDate | date | &nbsp;  |
| Yıl | Hiçbiri | Yılın tarihini ayrıştırır. |
| Yeni | billingCurrency | Ücret için kullanılan para birimi. |
| Yeni | billingProfileId | Faturalandırma profili için benzersiz KIMLIK (kayıt ile aynı). |
| Yeni | billingProfileName | Faturalandırma profilinin adı (kayıt ile aynı). |
| Yeni | chargeType | Azure hizmeti kullanımını, Market kullanımını ve satın alımlarını ayırt etmek için kullanın. |
| Yeni | invoiceId | Faturanın benzersiz KIMLIĞI. Geçerli, açık ay için boştur. |
| Yeni | publisherType | Satın alımlara Yayımcı türü. Kullanım için boş. |
| Yeni | serviceFamily | Satın alma türü. Kullanım için boş. |
| Yeni | servicePeriodEndDate | Satın alınan hizmetin bitiş tarihi. |
| Yeni | servicePeriodStartDate | Satın alınan hizmetin başlangıç tarihi. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>Fatura dönemleri API 'si, faturalar API 'siyle değiştirilmiştir

MCA faturalandırma hesapları faturalandırma dönemlerini kullanmaz. Bunun yerine, maliyetleri belirli faturalandırma dönemleriyle sınırlamak için faturalar kullanır. [Faturalandırma dönemleri API 'si](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) , faturalar API 'siyle değiştirilmiştir. Tüm tüketim API 'Leri, kimlik doğrulama ve yetkilendirme için Azure AD kullanan yerel Azure API 'Leri tarafından değiştirilmiştir. Azure REST API 'Leri çağırma hakkında daha fazla bilgi için bkz. [rest ile çalışmaya](/rest/api/azure/#create-the-request)başlama.

Faturalar API 'SI ile faturaları almak için:

| Yöntem | İstek URI 'SI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>Fiyat listesi API 'Leri

Bu bölümde, mevcut fiyat listesi API 'Leri ele alınmaktadır ve Microsoft Müşteri anlaşmaları için fiyat listesi API 'sine geçiş önerileri sağlanır. Ayrıca, Microsoft Müşteri anlaşmaları için fiyat listesi API 'sini açıklar ve fiyat sayfalarındaki alanları açıklar. [Kurumsal Get fiyat listesi](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) ve [Kurumsal Sözleşme ödeme dönemleri](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) API 'Leri, Microsoft Müşteri anlaşmaları (Microsoft. faturalandırma/Billingaccounts/billingprofiles/fiyat listesi) IÇIN fiyat listesi API 'siyle değiştirilmiştir. Yeni API, zaman uyumsuz REST biçimlerinde hem JSON hem de CSV biçimlerini destekler. Tüm tüketim API 'Leri, kimlik doğrulama ve yetkilendirme için Azure AD kullanan yerel Azure API 'Leri tarafından değiştirilmiştir. Azure REST API 'Leri çağırma hakkında daha fazla bilgi için bkz. [rest ile çalışmaya](/rest/api/azure/#create-the-request)başlama.

### <a name="billing-enterprise-apis"></a>Faturalandırma kurumsal API 'Leri

Fiyat ve fatura dönemi bilgilerini almak için Enterprise kayıtları ile faturalandırma kurumsal API 'Leri kullandınız. Web belirteçleri Azure Active Directory kullanılan kimlik doğrulaması ve yetkilendirme.

Fiyat listesi ve faturalandırma dönemi API 'Leri ile belirtilen kurumsal kayıt için geçerli fiyatları almak için:

| Yöntem | İstek URI 'SI |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Microsoft Müşteri anlaşmaları için fiyat listesi API 'SI

Tüm Azure tüketimi ve Market tüketim hizmetlerinin fiyatlarını görüntülemek için Microsoft Müşteri anlaşmaları için fiyat listesi API 'sini kullanın. Faturalandırma profili için gösterilen fiyatlar, faturalandırma profiline ait olan tüm abonelikler için geçerlidir.

Tüm Azure tüketim Hizmetleri fiyat listesi verilerini CSV biçiminde görüntülemek için fiyat listesi API 'sini kullanın:

| Yöntem | İstek URI 'SI |
| --- | --- |
| Yayınla | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Tüm Azure tüketim Hizmetleri fiyat listesi verilerini JSON biçiminde görüntülemek için fiyat listesi API 'sini kullanın:

| Yöntem | İstek URI 'SI |
| --- | --- |
| Yayınla | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

API 'nin kullanılması, tüm hesabın fiyat listesini döndürür. Ancak, Ayrıca, PDF biçiminde fiyat listesi 'nin sıkıştırılmış bir sürümünü de alabilirsiniz. Özet, belirli bir fatura için faturalandırılan Azure tüketimini ve Market tüketim hizmetlerini içerir. Fatura, Fatura Özeti PDF dosyalarında gösterilen **fatura numarasıyla** aynı olan {InvoiceId} tarafından tanımlanır. İşte bir örnek.

![InvoiceId 'ye karşılık gelen fatura numarasını gösteren örnek resim](./media/migrate-cost-management-api/invoicesummary.png)

Fiyat listesi API 'SI ile fatura bilgilerini CSV biçiminde görüntülemek için:

| Yöntem | İstek URI 'SI |
| --- | --- |
| Yayınla | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Fiyat listesi API 'SI ile fatura bilgilerini JSON biçiminde görüntülemek için:

| Yöntem | İstek URI 'SI |
| --- | --- |
| Yayınla | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Geçerli açık fatura döngüsündeki veya hizmet döneminde herhangi bir Azure tüketimi veya Market tüketim hizmeti için tahmini fiyatları da görebilirsiniz.

Fiyat listesi API 'SI ile birlikte, tüketim Hizmetleri için tahmini fiyatları CSV biçiminde görüntülemek için:

| Yöntem | İstek URI 'SI |
| --- | --- |
| Yayınla | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Fiyat listesi API 'SI ile birlikte, tüketim Hizmetleri için tahmini fiyatları JSON biçiminde görüntülemek için:

| Yöntem | İstek URI 'SI |
| --- | --- |
| Yayınla | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Microsoft müşteri anlaşması fiyat listesi API 'Leri, *zaman uyumsuz REST API*'lerdir. API 'Lerin yanıtları eski zaman uyumlu API 'lerden değiştirilmiştir. API yanıtının gövdesi de değişmiştir.

#### <a name="old-response-body"></a>Eski yanıt gövdesi

Zaman uyumlu REST API yanıtına bir örnek aşağıda verilmiştir:

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>Yeni yanıt gövdesi

API 'Ler [Azure Rest zaman uyumsuz](../azure-resource-manager/resource-manager-async-operations.md) biçimini destekler. GET kullanarak API 'YI çağırın ve aşağıdaki yanıtı alırsınız:

```
No Response Body

HTTP Status 202 Accepted
```

Aşağıdaki üstbilgiler çıktının konumuyla birlikte gönderilir:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Konuma başka bir GET çağrısı yapın. GET çağrısına olan yanıt, işlem bir tamamlama veya hata durumuna ulaşıncaya kadar aynıdır. Tamamlandığında, GET çağrısı konumuna olan yanıt indirme URL 'sini döndürür. Aynı anda işlemin yürütüldüğünden olduğu gibi. Bir örneği aşağıda verilmiştir:

```
HTTP Status 200

{
  "id": "providers/Microsoft.Consumption/operationresults/{operationId}",
  "name": {operationId},
  "type": “Microsoft.Consumption/operationResults",
  "properties" : {
    "downloadUrl": {urltoblob},
    "validTill": "Date"
  }
}
```

İstemci Ayrıca `Azure-AsyncOperation` için bir GET çağrısı yapabilir. Uç nokta işlemin durumunu döndürür.

Aşağıdaki tabloda, eski Kurumsal Get fiyat listesi API 'sindeki alanlar gösterilmektedir. Microsoft Müşteri anlaşmaları için yeni fiyat listesi ' nde karşılık gelen alanları içerir:

| Eski Özellik | Yeni Özellik | Notlar |
| --- | --- | --- |
| billingPeriodId  | _Uygulanamaz_ | Uygulanamaz. Microsoft Müşteri anlaşmaları için, fatura ve ilişkili fiyat listesi billingPeriodId kavramını değiştirdi. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Tam dize değerleri farklı şekilde değişebilir. |
| includedQuantity  | includedQuantity | Microsoft Müşteri anlaşmalarındaki hizmetler için geçerli değildir. |
| PartNumber  | _Uygulanamaz_ | Bunun yerine, productOrderName (OfferId ile aynı) ve ölçüm kimliği birleşimini kullanın. |
| unitPrice  | unitPrice | Birim fiyatı, Microsoft Müşteri sözleşmelerinden tüketilen hizmetler için geçerlidir. |
| currencyCode  | pricingCurrency | Microsoft Müşteri anlaşmaları fiyatlandırma para birimi ve faturalandırma para birimi cinsinden fiyat temsillerine sahiptir. CurrencyCode, Microsoft Müşteri sözleşmeleri içindeki pricingCurrency öğesine karşılık gelir. |
| OfferId | productOrderName | OfferId yerine productOrderName kullanabilir, ancak OfferId ile aynı olamaz. Ancak, productOrderName ve Meter, eski kayıt işlemlerinde ölçüm kimliği ve OfferId ile ilgili Microsoft Müşteri anlaşmalarında fiyatlandırmayı belirleme. |

## <a name="consumption-price-sheet-api-operations"></a>Tüketim fiyat listesi API işlemleri

Kurumsal sözleşmeler için, SubscriptionID veya faturalandırma dönemi kapsamında bir kapsam için [faturalandırma dönemi aracılığıyla](/rest/api/consumption/pricesheet/getbybillingperiod) API [Al](/rest/api/consumption/pricesheet/get) ve Al işlemleri için tüketim fiyatı listesini kullandınız. API, Azure Kaynak yönetimi kimlik doğrulamasını kullanır.

Fiyat listesi API 'SI ile bir kapsamın fiyat listesi bilgilerini almak için:

| Yöntem | İstek URI 'SI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Fiyat listesi API 'SI ile faturalandırma dönemine göre fiyat listesi bilgilerini almak için:

| Yöntem | İstek URI 'SI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Yukarıdaki API uç noktaları yerine, Microsoft Müşteri anlaşmaları için aşağıdaki olanları kullanın:

**Microsoft Müşteri anlaşmaları için fiyat listesi API 'SI (zaman uyumsuz REST API)**

Bu API, Microsoft Müşteri anlaşmaları içindir ve ek öznitelikler sağlar.

**Faturalandırma hesabındaki bir faturalandırma profili kapsamının fiyat listesi**

Bu API, var olan API 'dir. Faturalandırma hesabındaki bir faturalandırma profili için fiyat listesi sağlamak üzere güncelleştirildi.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Bir kapsamın faturalandırma hesabına göre fiyat listesi

Azure Resource Manager kimlik doğrulaması, bir faturalandırma hesabındaki kayıt kapsamında fiyat listesi aldığınızda kullanılır.

Bir faturalandırma hesabındaki kayıt hesabındaki fiyat listesini almak için:

| Yöntem | İstek URI 'SI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Bir Microsoft Müşteri Sözleşmesi için aşağıdaki bölümdeki bilgileri kullanın. Microsoft Müşteri anlaşmaları için kullanılan alan özelliklerini sağlar.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Faturalandırma hesabındaki bir faturalandırma profili kapsamının fiyat listesi

Faturalandırma hesabı API 'sine göre güncelleştirilmiş fiyat listesi, Fiyat listesini CSV biçiminde alır. Bir MCA için Faturalandırma profili kapsamında fiyat listesi almak için:

| Yöntem | İstek URI 'SI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

EA 'nın kayıt kapsamında, API yanıtı ve özellikleri aynıdır. Özellikler aynı MCA özelliklerine karşılık gelir.

[Azure Resource Manager fiyat listesi API 'leri](/rest/api/consumption/pricesheet) için eski Özellikler ve aynı yeni özellikler aşağıdaki tabloda verilmiştir.

| Eski Azure Resource Manager fiyat listesi API özelliği  | Yeni Microsoft müşteri anlaşması fiyat listesi API 'SI özelliği   | Açıklama |
| --- | --- | --- |
| Ölçüm Kimliği | _Ölçüm kimliği_ | Ölçümün benzersiz tanımlayıcısı. Ölçüm kimliği ile aynı. |
| Ölçüm adı | meterName | Ölçümün adı. Ölçüm, Azure hizmeti dağıtılabilir kaynağını temsil eder. |
| Ölçüm kategorisi  | hizmet | Ölçüm için sınıflandırma kategorisinin adı. Microsoft müşteri anlaşması fiyat listesi ' nde hizmetle aynı. Tam dize değerleri farklı. |
| Ölçüm alt kategorisi | meterSubCategory | Ölçüm alt sınıflandırma kategorisinin adı. Hizmette bulunan üst düzey özellik kümesi sınıflandırmasına dayalı olarak. Örneğin, temel SQL DB ile standart SQL VERITABANı. |
| Ölçüm bölgesi | meterRegion | &nbsp;  |
| Birim | _Uygulanamaz_ | UnitOfMeasure öğesinden ayrıştırılabilir. |
| Ölçü birimi | unitOfMeasure | &nbsp;  |
| Parça numarası | _Uygulanamaz_ | PartNumber yerine, bir faturalandırma profilinin fiyatını benzersiz bir şekilde tanımlamak için productOrderName ve ölçüm kimliği kullanın. Alanlar, MCA faturalarında partNumber yerine MCA faturasında listelenir. |
| Birim fiyat | unitPrice | Microsoft Müşteri Sözleşmesi birim fiyatı. |
| Para birimi kodu | pricingCurrency | Microsoft Müşteri anlaşmaları fiyatlandırma para birimi ve faturalandırma para birimi cinsinden fiyatları temsil eder. Para birimi kodu, Microsoft Müşteri sözleşmeleri içindeki pricingCurrency ile aynıdır. |
| Dahil edilen miktar | includedQuantity | Microsoft Müşteri anlaşmalarındaki hizmetlere uygulanmaz. Sıfır değerleriyle göster. |
|  Teklif kimliği  | productOrderName | OfferId yerine productOrderName kullanın. OfferId ile aynı değildir, ancak productOrderName ve Meter, Microsoft Müşteri anlaşmalarında fiyatlandırmaya göre belirlenir. Eski kayıtlar 'daki ölçüm kimliği ve OfferId ile ilgilidir. |

Microsoft Müşteri anlaşmalarının fiyatı, kurumsal anlaşmalardan farklı şekilde tanımlanır. Kurumsal kayıt içindeki hizmetlerin fiyatı ürün, PartNumber, ölçüm ve teklif için benzersizdir. PartNumber, Microsoft Müşteri sözleşmelerde kullanılmaz.

Microsoft Müşteri sözleşmesinin bir parçası olan Azure tüketim hizmeti fiyatı productOrderName ve ölçüm kimliği için benzersizdir. Hizmet ölçümünü ve ürün planını temsil eder.

Kullanım ayrıntıları API 'sindeki fiyat listesi ile kullanım arasında mutabakat sağlamak için productOrderName ve ölçüm kimliği kullanabilirsiniz.

Faturalama profili sahibi, katkıda bulunan, okuyucu ve fatura yöneticisi haklarına sahip kullanıcılar fiyat listesini indirebilir.

Fiyat listesi, fiyatı kullanımı temel alan hizmetlerin fiyatlarını içerir. Hizmetler Azure tüketim ve Market tüketimini içerir. Her hizmet döneminin sonundaki en son fiyat kilitlidir ve tek bir hizmet döneminde kullanıma uygulanır. Azure tüketim Hizmetleri için hizmet dönemi genellikle bir takvim aydır.

### <a name="retired-price-sheet-api-fields"></a>Kullanımdan kaldırılan fiyat listesi API alanları

Aşağıdaki alanlar Microsoft Müşteri Sözleşmesi fiyat listesi API 'Lerinde kullanılamaz veya aynı alanlara sahip değildir.

|Kullanımdan kaldırılan alan| Açıklama|
|---|---|
| billingPeriodId | Uygulanabilir değil. MCA için InvoiceId öğesine karşılık gelir. |
| OfferId | Uygulanamaz. MCA productOrderName öğesine karşılık gelir. |
| meterCategory  | Uygulanamaz. MCA içindeki hizmete karşılık gelir. |
| birim | Uygulanamaz. UnitOfMeasure öğesinden ayrıştırılabilir. |
| currencyCode | MCA içindeki pricingCurrency ile aynı. |
| meterLocation | MCA ile meterRegion aynı. |
| partNumber partNumber | Bölüm numarası MCA faturalarında listelenmediğinden uygulanamaz. PartNumber yerine, fiyatları benzersiz şekilde tanımlamak için ölçüm kimliği ve productOrderName birleşimini kullanın. |
| Totalıncludedquantity | Uygulanamaz. |
| pretaxStandardRate  | Uygulanamaz. |

## <a name="reservation-instance-charge-api-replaced"></a>Ayırma örneği ücreti API 'SI değişti

[Ayrılmış örnek ücreti API 'si](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)ile rezervasyon satın alımları için faturalandırma işlemleri alabilirsiniz. Yeni API, üçüncü taraf Market teklifleri dahil olmak üzere tüm satınalmaları içerir. Tüm tüketim API 'Leri, kimlik doğrulama ve yetkilendirme için Azure AD kullanan yerel Azure API 'Leri tarafından değiştirilmiştir. Azure REST API 'Leri çağırma hakkında daha fazla bilgi için bkz. [rest ile çalışmaya](/rest/api/azure/#create-the-request)başlama. Ayrılmış örnek ücreti API 'SI, Işlemler API 'siyle değiştirilmiştir.

Işlem API 'SI ile rezervasyon satın alma işlemlerini almak için:

| Yöntem | İstek URI 'SI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Öneriler API 'Leri değişti

Ayrılmış örnek satın alma önerileri API 'Leri, son 7, 30 veya 60 gün içinde sanal makine kullanımını sağlar. API 'Ler Ayrıca, rezervasyon satın alma önerilerini de sağlar. Şunları içerir:

- [Paylaşılan ayrılmış örnek önerisi API 'SI](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [Tek ayrılmış örnek önerileri API 'SI](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Tüm tüketim API 'Leri, kimlik doğrulama ve yetkilendirme için Azure AD kullanan yerel Azure API 'Leri tarafından değiştirilmiştir. Azure REST API 'Leri çağırma hakkında daha fazla bilgi için bkz. [rest ile çalışmaya](/rest/api/azure/#create-the-request)başlama. Daha önce listelenen ayırma önerileri API 'Leri, [Microsoft. tüketim/Rezervationöneriler](/rest/api/consumption/reservationrecommendations/list) API 'siyle değiştirilmiştir.

Ayırma önerileri API 'SI ile rezervasyon önerileri almak için:

| Yöntem | İstek URI 'SI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Ayırma kullanım API 'Leri değişti

Ayrılmış Örnek Kullanımı API 'SI ile bir kayıtta rezervasyon kullanımı edinebilirsiniz. Bir kayıtta birden fazla ayrılmış örnek varsa, bu API 'YI kullanarak tüm ayrılmış örnek satın almalarınızın kullanımını da edinebilirsiniz.

Şunları içerir:

- [Ayrılmış Örnek Kullanımı ayrıntıları](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Ayrılmış Örnek Kullanımı Özeti](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Tüm tüketim API 'Leri, kimlik doğrulama ve yetkilendirme için Azure AD kullanan yerel Azure API 'Leri tarafından değiştirilmiştir. Azure REST API 'Leri çağırma hakkında daha fazla bilgi için bkz. [rest ile çalışmaya](/rest/api/azure/#create-the-request)başlama. Daha önce listelenen ayırma önerileri API 'Leri [Microsoft. tüketim/Rezervationdetails](/rest/api/consumption/reservationsdetails) ve [Microsoft. tüketim/rezervationözetler](/rest/api/consumption/reservationssummaries) API 'leri tarafından değiştirilmiştir.

Ayırma ayrıntıları API 'SI ile rezervasyon ayrıntılarını almak için:

| Yöntem | İstek URI 'SI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

RESERVATION özetler API 'SI ile rezervasyon özetlerini almak için:

| Yöntem | İstek URI 'SI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Cloudyn 'ten maliyet yönetimine taşıma

[Cloudyn](https://cloudyn.com) kullanan kuruluşlar, herhangi bir maliyet yönetimi Ihtiyacı Için [Azure maliyet yönetimi](https://azure.microsoft.com/services/cost-management/) 'ni kullanmaya başlamalıdır. Maliyet yönetimi, hiçbir ekleme ve sekiz saatlik gecikme süresi olmadan Azure portal kullanılabilir. Daha fazla bilgi için bkz. [maliyet yönetimi belgeleri](index.yml).

Azure maliyet yönetimi ile şunları yapabilirsiniz:

- Önceden tanımlanmış bir bütçeye göre zaman içinde maliyetleri görüntüleyin. Harcama bozuklularını belirlemek ve durdurmak için günlük maliyet desenlerini çözümleyin. Maliyetleri etiketlere, kaynak grubuna, hizmete ve konuma göre bölün.
- Kullanım ve maliyetler için sınırlamalar ayarlamak ve önemli eşikler approached olduğunda bildirim almak için bütçeler oluşturun. Özel olayları tetiklemek ve koşullarınızda sabit limitleri zorlamak için eylem gruplarıyla Otomasyon ayarlayın.
- Azure Advisor önerileriyle maliyeti ve kullanımı iyileştirin. Rezervasyonlar ile satın alma iyileştirmelerini bulun, daha az kullanılan sanal makineleri yeniden boyutlandırın ve kullanılmayan kaynakları silerek bütçeleri dahilinde kalır.
- Günlük olarak bir CSV dosyası yayımlamak için bir maliyet ve kullanım verilerini dışa aktarma zamanlayın. Faturalama verilerini eşitlenmiş ve güncel tutmak için dış sistemlerle tümleştirmeyi otomatikleştirin.

## <a name="power-bi-integration"></a>Power BI tümleştirmesi

Maliyet raporlama için Power BI kullanıyorsanız, aşağıdakilere geçiş yapmanız gerekir:

- Microsoft Azure Consumption Insights Power BI uygulaması
- Azure Consumption Insights Desktop Bağlayıcısı


Bağlayıcı, en çok esnekliği isteyen kuruluşlar için önerilir. Ancak Power BI uygulama hızlı kurulum için de kullanılabilir.

- [Microsoft Azure Consumption Insights Power BI uygulamasını](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) yükler
- [Azure Consumption Insights Bağlayıcısı ile bağlanma](/power-bi/desktop-connect-azure-consumption-insights)

Eski tüketim öngörüleri içerik paketi ve bağlayıcı bir kayıt düzeyinde çalıştı. En azından okuma erişimi gerekiyor. Yeni tüketim öngörüleri Power BI uygulaması ve yeni Azure Consumption Insights Bağlayıcısı Faturalandırma profili kullanıcıları tarafından kullanılabilir. Maliyetleri gözden geçirmek veya maliyetleri izlemek için ek seçeneklere ihtiyaç duyulan ekipler Azure portal [Maliyet analizinde](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/costanalysis) kullanılmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

- Azure harcamalarınızı izleme ve denetleme hakkında bilgi edinmek için [maliyet yönetimi belgelerini](index.yml) okuyun. Veya maliyet yönetimiyle kaynak kullanımını iyileştirmek istiyorsanız.
