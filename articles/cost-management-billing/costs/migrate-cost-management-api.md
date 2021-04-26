---
title: EA'yı Microsoft Müşteri Sözleşmesi API'lerine geçirme - Azure
description: Bu makale, Microsoft Kurumsal Anlaşma'dan (EA) Microsoft Müşteri Sözleşmesi'ne geçiş yapmanın sonuçlarını anlamanıza yardımcı olacaktır.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.openlocfilehash: 46ad81f6723d160bf1d675b68a8459dd8df32c80
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078358"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Kurumsal Anlaşma'dan Microsoft Müşteri Sözleşmesi API'lerine geçiş

Bu makale, Kurumsal Anlaşma (EA) ile Microsoft Müşteri Sözleşmesi (MCA) hesapları arasındaki veri yapısı, API ve diğer sistem tümleştirmesi farklarını anlamanıza yardımcı olacaktır. Azure Maliyet Yönetimi, iki hesap türünün API'lerini de destekler. Devam etmeden önce Microsoft Müşteri Sözleşmesi için [Ödeme hesabını ayarlama](../manage/mca-setup-account.md) makalesini inceleyin.

EA hesabı olan kuruluşlar, MCA hesabı oluşturmanın yanı sıra bu makaleyi de incelemelidir. Eskiden EA hesabının yenilenmesi için eski kaydı yeni bir kayda taşıma amacıyla çok az işlem gerekiyordu. Ancak MCA hesabına geçiş süreci bundan daha ayrıntılıdır. Bunun nedeni, maliyetlerle ilgili API'leri ve sunulan hizmetleri etkileyen faturalandırma alt sisteminde yapılan değişikliklerdir.

## <a name="mca-apis-and-integration"></a>MCA API'leri ve tümleştirme

MCA API'leri ve yeni tümleştirme sayesinde şunları gerçekleştirebilirsiniz:

- Yerel Azure API'leri aracılığıyla tam API kullanılabilirliği sağlama.
- Tek bir ödeme hesabında birden fazla fatura yapılandırma.
- Azure hizmeti kullanımını, üçüncü taraf Market kullanımını ve Market satın alma işlemlerini içeren birleştirilmiş bir API'ye erişme.
- Azure Maliyet Yönetimi'ni kullanarak farklı faturalama profillerindeki (kayıtlarda olduğu gibi) maliyetleri görüntüleme.
- Maliyetleri göstermek, maliyetler önceden tanımlanmış eşikleri aştığında bildirim almak ve ham verileri otomatik olarak dışarı aktarmak için yeni API'lere erişme.

## <a name="migration-checklist"></a>Geçiş denetim listesi

Aşağıdaki maddeler, MCA API'lerine geçiş yapmanıza yardımcı olacaktır.

- Yeni [Microsoft Müşteri Sözleşmesi ödeme hesabı](../understand/mca-overview.md) hakkında bilgi edinin.
- Kullanacağınız API'leri belirleyin ve aşağıdaki bölümde değiştirilenleri inceleyin.
- [Azure Resource Manager REST API'leri](/rest/api/azure) hakkında bilgi edinin.
- Daha önce Azure Resource Manager API'lerini kullanmadıysanız [istemci uygulamanızı Azure AD'ye kaydedin](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Programlarınızın kodunu [Azure AD kimlik doğrulamasını](/rest/api/azure/#create-the-request) kullanacak şekilde güncelleştirin.
- Programlarınızın kodunu EA API çağrılarını MCA API çağrılarıyla değiştirecek şekilde güncelleştirin.
- Hata işleme süreçlerini yeni hata kodlarını kullanacak şekilde güncelleştirin.
- İhtiyaç duyulan ek eylemler için Cloudyn ve Power BI gibi ek tümleştirme tekliflerini inceleyin.

## <a name="ea-apis-replaced-with-mca-apis"></a>EA API'ler, MCA API'leriyle değiştirildi

EA API'leri, kimlik doğrulaması ve yetkilendirme için API anahtarı kullanır. MCA API'leri ise Azure AD kimlik doğrulamasını kullanır.

| Amaç | EA API | MCA API |
| --- | --- | --- |
| Bakiye ve krediler | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Kullanım (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Kullanım (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Market Kullanımı (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Faturalama dönemleri | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Fiyat listesi | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft. faturalandırma/billingAccounts/billingProfiles/fiyat listesi/varsayılan/indirme biçimi = JSON \| CSV Microsoft. faturalandırma/billingAccounts/.../billingProfiles/.../faturalar/... /pricesheet/default/Download Format = JSON \| CSV Microsoft. faturalandırma/billingAccounts/.. /Billingprofiles/.exe. /providers/Microsoft.Consumption/pricesheets/download  |
| Rezervasyon satın almaları | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Rezervasyon önerileri | [/Sharedrezervationönerilere](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations) [/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) [SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Rezervasyon kullanımı | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> Azure hizmeti ve üçüncü taraf Market kullanımı, [Kullanım Ayrıntıları API'si](/rest/api/consumption/usagedetails) ile sunulmaktadır.

Aşağıdaki API'ler MCA ödeme hesapları için kullanılabilir:

| Amaç | Microsoft Müşteri Sözleşmesi (MCA) API'si |
| --- | --- |
| Ödeme hesapları<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Faturalama profilleri<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Fatura bölümleri<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Faturalar | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Faturalama abonelikleri | {scope}/billingSubscriptions |

<sup>2</sup> API'ler, Azure portalındaki ve API'lerdeki Maliyet Yönetimi deneyimlerinin çalıştığı kapsamlar olan nesne listelerini döndürür. Maliyet Yönetimi kapsamları hakkında daha fazla bilgi için bkz. [Kapsamları anlama ve birlikte çalışma](understand-work-scopes.md).

Mevcut EA API'lerini kullanıyorsanız bunları MCA ödeme hesaplarını destekleyecek şekilde güncelleştirmeniz gerekir. Aşağıdaki tabloda tümleştirme konusundaki diğer değişiklikler gösterilmektedir:

| Amaç | Eski teklif | Yeni teklif |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Maliyet Yönetimi](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) içerik paketi ve bağlayıcısı |  [Azure Consumption Insights bağlayıcısı](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>Bakiyeyi ve kredileri almak için API'ler

[Bakiye Özetini Al](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) API'si şu bilgilerin aylık özetini verir:

- Bakiyeler
- Yeni satın almalar
- Azure Market hizmet ücretleri
- Düzeltmeler
- Hizmet fazla kullanım ücretleri

Tüm Tüketim API'leri, kimlik doğrulaması ve yetkilendirme için Azure AD'yi kullanan yerel Azure API'leriyle değiştirilmiştir. Azure REST API'lerini çağırma hakkında daha fazla bilgi için bkz. [REST'i kullanmaya başlama](/rest/api/azure/#create-the-request).

Bakiye Özetini Al API'si, Microsoft.Billing/billingAccounts/billingProfiles/availableBalance API'si ile değiştirilmiştir.

Kullanılabilir Bakiye API'si ile kullanılabilir bakiyeleri almak için:

| Yöntem | İstek URI'si |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>Maliyeti ve kullanımı almak için API'ler

Aşağıdaki API'leri kullanarak Azure hizmeti kullanımı, üçüncü taraf Market kullanımı ve diğer Market satın alma işlemlerine ait maliyetlerin günlük dökümünü alabilirsiniz. Aşağıdaki ayrı API'ler, Azure hizmetleri ve üçüncü taraf Market kullanımı için birleştirilmiştir. Eski API'ler [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) API'si ile değiştirilmiştir. Daha önce yalnızca bakiye özetinde o tarihe kadar gösterilen Market satın alma işlemleri eklenmiştir.

- [Kullanım ayrıntılarını al/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Kullanım ayrıntılarını al/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Kullanım ayrıntılarını al/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Kullanım ayrıntılarını al/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Market mağazası ücretini al/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Market mağazası ücretini al/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Tüm Tüketim API'leri, kimlik doğrulaması ve yetkilendirme için Azure AD'yi kullanan yerel Azure API'leriyle değiştirilmiştir. Azure REST API'lerini çağırma hakkında daha fazla bilgi için bkz. [REST'i kullanmaya başlama](/rest/api/azure/#create-the-request).

Yukarıdaki API'lerin tümü, Tüketim/Kullanım Ayrıntıları API'si ile değiştirilmiştir.

Kullanım Ayrıntıları API'si ile kullanım ayrıntılarını almak için:

| Yöntem | İstek URI'si |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

Kullanım Ayrıntıları API'si, diğer tüm Maliyet Yönetimi API'leri gibi birden fazla kapsamda kullanılabilir. Normalde kayıt düzeyinde aldığınız faturalandırılan maliyetler için faturalama profili kapsamını kullanın.  Maliyet Yönetimi kapsamları hakkında daha fazla bilgi için bkz. [Kapsamları anlama ve birlikte çalışma](understand-work-scopes.md).

| Tür | Kimlik biçimi |
| --- | --- |
| Fatura hesabı | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Faturalama profili | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Abonelik | `/subscriptions/{subscriptionId}` |
| Kaynak grubu | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Programlama kodunda güncelleştirme yapmak için aşağıdaki querystring parametrelerini kullanın.

| Eski parametreler | Yeni parametreler |
| --- | --- |
| `billingPeriod={billingPeriod}` | Desteklenmez |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Yanıtın gövdesi de değiştirilmiştir.

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

Kullanım kayıtları dizisini içeren özelliğin adı "data" yerine _values_ olarak değiştirilmiştir. Her kayıt, ayrıntılı özellikleri içeren düz bir listeye sahipti. Ancak her kayıt artık etiketler hariç olmak üzere tüm ayrıntıları _properties_ adlı iç içe yerleştirilmiş bir özellikte tutuyor. Bu yeni yapı, diğer Azure API'leriyle de tutarlı. Bazı özelliklerin adları değiştirildi. Aşağıdaki tabloda karşılık gelen özellikler gösterilmektedir.

| Eski özellik | Yeni özellik | Notlar |
| --- | --- | --- |
| AccountId | Yok | Abonelik oluşturucu izlenmiyor. invoiceSectionId (departmentId ile aynı) kullanın. |
| AccountNameAccountOwnerId ve AccountOwnerEmail | Yok | Abonelik oluşturucu izlenmiyor. invoiceSectionName (departmentName ile aynı) kullanın. |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Bu özelliklerin karşıt olduğunu unutmayın. isAzureCreditEnabled true olduğunda ChargesBilledSeparately false olur. |
| ConsumedQuantity | miktar | &nbsp; |
| ConsumedService | consumedService | Tam dize değerleri farklı olabilir. |
| ConsumedServiceId | Yok | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Date ve usageStartDate | date | &nbsp;  |
| Gün | Yok | Tarihteki günü ayrıştırır. |
| DepartmentId | invoiceSectionId | Tam değerler farklıdır. |
| DepartmentName | invoiceSectionName | Tam dize değerleri farklı olabilir. Gerekirse fatura bölümlerini departmanlarla eşleşecek şekilde yapılandırın. |
| ExtendedCost ve Cost | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| Is Recurring Charge | Yok | &nbsp;  |
| Konum | location | &nbsp;  |
| MeterCategory | meterCategory | Tam dize değerleri farklı olabilir. |
| MeterId | meterId | Tam dize değerleri farklıdır. |
| MeterName | meterName | Tam dize değerleri farklı olabilir. |
| MeterRegion | meterRegion | Tam dize değerleri farklı olabilir. |
| MeterSubCategory | meterSubCategory | Tam dize değerleri farklı olabilir. |
| Ay | Yok | Tarihteki ayı ayrıştırır. |
| Teklif Adı | Yok | publisherName ve productOrderName kullanın. |
| OfferID | Yok | &nbsp;  |
| Sipariş Numarası | Yok | &nbsp;  |
| PartNumber | Yok | Fiyatları benzersiz şekilde tanımlamak için meterId ve productOrderName kullanın. |
| Plan Adı | productOrderName | &nbsp;  |
| Ürün | Ürün |   |
| ProductId | productId | Tam dize değerleri farklıdır. |
| Yayımcı Adı | publisherName | &nbsp;  |
| adlı yönetilen örnek, | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | Tam dize değerleri farklıdır. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | Yok | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | Yok | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Tam dize değerleri farklı olabilir. |
| ServiceTier | meterSubCategory | Tam dize değerleri farklı olabilir. |
| StoreServiceIdentifier | Yok | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Etiketler | tags | Etiketler özelliği, iç içe yerleştirilmiş özellikler özelliğine değil kök nesneye uygulanır. |
| UnitOfMeasure | unitOfMeasure | Tam dize değerleri farklıdır. |
| usageEndDate | date | &nbsp;  |
| Yıl | Yok | Tarihteki yılı ayrıştırır. |
| (yeni) | billingCurrency | Ücret için kullanılan para birimi. |
| (yeni) | billingProfileId | Faturalama profili için benzersiz kimlik (kayıt ile aynı). |
| (yeni) | billingProfileName | Faturalama profilinin adı (kayıt ile aynı). |
| (yeni) | chargeType | Azure hizmeti kullanımı, Market kullanımı ve satın alma işlemleri arasında ayrım yapmak için. |
| (yeni) | invoiceId | Faturanın benzersiz kimliği. Geçerli, mevcut ay için boş bırakın. |
| (yeni) | publisherType | Satın alma işlemleri için yayımcı türü. Kullanım için boş. |
| (yeni) | serviceFamily | Satın alma türü. Kullanım için boş. |
| (yeni) | servicePeriodEndDate | Satın alınan hizmetin bitiş tarihi. |
| (yeni) | servicePeriodStartDate | Satın alınan hizmetin başlangıç tarihi. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>Fatura Dönemleri API'si, Faturalar API'si ile değiştirilmiştir

MCA ödeme hesapları, faturalandırma dönemlerini kullanmaz. Bunun yerine maliyetler için belirli faturalama dönemlerini kapsam olarak belirleme amacıyla faturaları kullanır. [Fatura Dönemleri API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)'si, Faturalar API'si ile değiştirilmiştir. Tüm Tüketim API'leri, kimlik doğrulaması ve yetkilendirme için Azure AD'yi kullanan yerel Azure API'leriyle değiştirilmiştir. Azure REST API'lerini çağırma hakkında daha fazla bilgi için bkz. [REST'i kullanmaya başlama](/rest/api/azure/#create-the-request).

Faturalar API'si ile faturaları almak için:

| Yöntem | İstek URI'si |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>Fiyat Listesi API'leri

Bu bölümde mevcut Fiyat Listesi API'leri anlatılmakta ve Microsoft Müşteri Sözleşmesi için Fiyat Listesi API'sine geçiş yapma önerileri sunulmaktadır. Ayrıca Microsoft Müşteri Sözleşmesi için Fiyat Listesi API'si anlatılmakta ve fiyat listelerindeki alanlar açıklanmaktadır. [Kurumsal Fiyat listesini al](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) ve [Kurumsal Faturalama dönemlerini al](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) API'leri, Microsoft Müşteri Sözleşmesi için Fiyat Listesi API'si (Microsoft.Billing/billingAccounts/billingProfiles/pricesheet) ile değiştirilmiştir. Yeni API, zaman uyumsuz REST biçimlerinde hem JSON hem de CSV biçimlerini desteklemektedir. Tüm Tüketim API'leri, kimlik doğrulaması ve yetkilendirme için Azure AD'yi kullanan yerel Azure API'leriyle değiştirilmiştir. Azure REST API'lerini çağırma hakkında daha fazla bilgi için bkz. [REST'i kullanmaya başlama](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>Faturalama Kurumsal API'leri

Kurumsal kayıtlarda fiyat ve faturalama dönemi bilgilerini almak için Faturalama Kurumsal API'lerini kullanıyordunuz. Kimlik doğrulama ve yetkilendirme için Azure Active Directory web belirteçleri kullanılıyordu.

Fiyat Listesi ve Faturalama Dönemi API'leri ile belirtilen Kurumsal Kayıt için geçerli olan fiyatları almak için:

| Yöntem | İstek URI'si |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Microsoft Müşteri Sözleşmeleri için Fiyat Listesi API'si

Tüm Azure Tüketim ve Market tüketim hizmetlerine ait fiyatları görüntülemek isterseniz Microsoft Müşteri Sözleşmeleri için Fiyat Listesi API'sini kullanın. Faturalama profili için gösterilen fiyatlar, faturalama profiline ait olan tüm abonelikler için geçerlidir.

Tüm Azure Tüketim hizmetlerinin Fiyat Listesi verilerini CSV biçiminde görüntülemek için Fiyat Listesi API'sini kullanın:

| Yöntem | İstek URI'si |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Tüm Azure Tüketim hizmetlerinin Fiyat Listesi verilerini JSON biçiminde görüntülemek için Fiyat Listesi API'sini kullanın:

| Yöntem | İstek URI'si |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

API kullanıldığında hesabın tamamı için geçerli olan fiyat listesi döndürülür. Ancak isterseniz fiyat listesinin sıkıştırılmış sürümünü PDF biçiminde alabilirsiniz. Özette belirli bir faturaya dahil olan Azure Tüketim ve Market tüketim hizmetleri bulunur. Fatura, Fatura Özeti PDF dosyalarında gösterilen **Fatura Numarası** ile aynı olan {invoiceId} ile tanımlanır. Aşağıda bir örneği yer alır.

![InvoiceId değerine karşılık gelen Fatura Numarasını gösteren görüntü](./media/migrate-cost-management-api/invoicesummary.png)

Fiyat Listesi API'si ile fatura bilgilerini CSV biçiminde görüntülemek için:

| Yöntem | İstek URI'si |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Fiyat Listesi API'si ile fatura bilgilerini JSON biçiminde görüntülemek için:

| Yöntem | İstek URI'si |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

İsterseniz geçerli açık faturalama dönemi veya hizmet dönemi içindeki Azure Tüketimi veya Market tüketimi hizmetine ait tahmini fiyatları da görüntüleyebilirsiniz.

Fiyat Listesi API'si ile tüketim hizmetlerine ait tahmini fiyatları CSV biçiminde görüntülemek için:

| Yöntem | İstek URI'si |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Fiyat Listesi API'si ile tüketim hizmetlerine ait tahmini fiyatları JSON biçiminde görüntülemek için:

| Yöntem | İstek URI'si |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Microsoft Müşteri Sözleşmesi Fiyat Listesi API'leri, *zaman uyumsuz REST API'leridir*. API yanıtları, eski zaman uyumlu API'lere göre değiştirilmiştir. API yanıtının gövdesi de değiştirilmiştir.

#### <a name="old-response-body"></a>Eski yanıt gövdesi

Zaman uyumlu REST API yanıtı örneği aşağıda verilmiştir:

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

API'ler, [Azure REST zaman uyumsuz](../../azure-resource-manager/management/async-operations.md) biçimini destekler. API'yi GET kullanarak çağırdığınızda şu yanıtı alırsınız:

```
No Response Body

HTTP Status 202 Accepted
```

Aşağıdaki üst bilgiler, çıkışın konumuyla birlikte gönderilir:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Konuma başka bir GET çağrısı yapın. GET çağrısına verilen yanıt, işlem tamamlanana veya hata verene kadar aynı olur. İşlem tamamlandığında GET çağrısı yanıtı, indirme URL'sini döndürür. İşlem aynı anda yürütülmüş gibi olur. Aşağıda bir örnek verilmiştir:

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

İstemci ayrıca `Azure-AsyncOperation` için bir GET çağrısı yapabilir. Uç nokta, işlemin durumunu döndürür.

Aşağıdaki tabloda eski Enterprise GET fiyat listesi API'sindeki alanlar gösterilmiştir. Microsoft Müşteri Sözleşmeleri için yeni fiyat listesinde bulunan şu alanları içermektedir:

| Eski özellik | Yeni özellik | Notlar |
| --- | --- | --- |
| billingPeriodId  | _Geçerli değil_ | Geçerli değildir. Microsoft Müşteri Sözleşmeleri için billingPeriodId kavramının yerini fatura ve ilgili fiyat listesi almıştır. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Tam dize değerleri farklı olabilir. |
| includedQuantity  | includedQuantity | Microsoft Müşteri Sözleşmeleri kapsamındaki hizmetler için geçerli değildir. |
| partNumber  | _Geçerli değil_ | Bunun yerine productOrderName (offerID ile aynı) ve meterID kullanın. |
| unitPrice  | unitPrice | Birim fiyatı, Microsoft Müşteri Sözleşmeleri kapsamındaki hizmetler için geçerli değildir. |
| currencyCode  | PricingCurrency | Microsoft Müşteri Sözleşmeleri, fiyatlandırma para birimi ve faturalandırma para birimi için fiyat gösterimlerine sahiptir. currencyCode, Microsoft Müşteri Sözleşmelerindeki pricingCurrency özelliğine karşılık gelir. |
| offerID | productOrderName | OfferID yerine productOrderName kullanabilirsiniz ancak bu özellik OfferID ile aynı değildir. Ancak productOrderName ve ölçüm, Microsoft Müşteri Sözleşmelerinde eski kayıtlarda meterId ve OfferID ile belirtilen fiyatlandırmayı belirler. |

## <a name="consumption-price-sheet-api-operations"></a>Tüketim Fiyat Listesi API'si işlemleri

Kurumsal Anlaşmalarda subscriptionId veya faturalama dönemi ile kapsam belirleyerek işlemler için Tüketim Fiyat Listesi API'si [Al](/rest/api/consumption/pricesheet/get) ve [Faturalama Dönemine Göre Al](/rest/api/consumption/pricesheet/getbybillingperiod) işlemlerini kullandınız. API, Azure Resource Management kimlik doğrulamasını kullanır.

Fiyat Listesi API'si ile belirli bir kapsama ait Fiyat Listesi bilgilerini almak için:

| Yöntem | İstek URI'si |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Fiyat Listesi API'si ile faturalama dönemine göre Fiyat Listesi bilgilerini almak için:

| Yöntem | İstek URI'si |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Microsoft Müşteri Sözleşmeleri için yukarıdaki API uç noktalarının yerine aşağıdakileri kullanın:

**Microsoft Müşteri Sözleşmeleri için Fiyat Listesi API'si (zaman uyumsuz REST API'si)**

Bu API, Microsoft Müşteri Sözleşmelerine yöneliktir ve ek öznitelikler sunar.

**Faturalandırma hesabındaki bir faturalandırma profili kapsamının fiyat listesi**

Bu API, mevcut API ile aynıdır. Bir ödeme hesabındaki faturalama profili için fiyat listesini sağlayacak şekilde güncelleştirilmiştir.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Kapsam için ödeme hesabına göre Fiyat Listesi

Kayıt kapsamındaki bir ödeme hesabında Fiyat Listesini aldığınızda Azure Resource Manager kimlik doğrulaması kullanılır.

Bir ödeme hesabındaki kayıt hesabında Fiyat Listesini almak için:

| Yöntem | İstek URI'si |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Microsoft Müşteri Sözleşmesi için aşağıdaki bölümde yer alan bilgileri kullanın. Microsoft Müşteri Sözleşmeleri için kullanılan alan özellikleri sunulmuştur.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Ödeme hesabındaki faturalama profili kapsamı için Fiyat Listesi

Güncelleştirilen ödeme hesabına göre Fiyat Listesi API'si, Fiyat Listesini CSV biçiminde alır. Bir MCA için faturalama profili kapsamındaki Fiyat Listesini almak için:

| Yöntem | İstek URI'si |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

EA'nın kayıt kapsamında API yanıtı ve özellikleri aynıdır. Özellikler aynı MCA özelliklerine karşılık gelir.

[Azure Resource Manager Fiyat Listesi API'leri](/rest/api/consumption/pricesheet) için kullanılan eski özellikler ve karşılık gelen yeni özellikler aşağıdaki tabloda belirtilmiştir.

| Eski Azure Resource Manager Fiyat Listesi API'si Özelliği  | Yeni Microsoft Müşteri Sözleşmesi Fiyat Listesi API'si Özelliği   | Description |
| --- | --- | --- |
| Ölçüm kimliği | _Ölçüm kimliği_ | Ölçümün benzersiz tanımlayıcısı. meterID ile aynıdır. |
| Ölçüm adı | meterName | Ölçümün adı. Ölçüm, dağıtılabilen Azure hizmeti kaynağını temsil eder. |
| Ölçüm kategorisi  | hizmet | Ölçüm için sınıflandırma kategorisinin adı. Microsoft Müşteri Sözleşmesi Fiyat Listesindeki hizmet ile aynıdır. Tam dize değerleri farklıdır. |
| Ölçüm alt kategorisi | meterSubCategory | Ölçüm alt sınıflandırma kategorisinin adı. Hizmetteki üst düzey özellik kümesi farkı sınıflandırmasını temel alır. Örneğin Temel SQL Veritabanı ve Standart SQL Veritabanı. |
| Ölçüm bölgesi | meterRegion | &nbsp;  |
| Birim | _Geçerli değil_ | unitOfMeasure değerinden ayrıştırılabilir. |
| Ölçü birimi | unitOfMeasure | &nbsp;  |
| Parça numarası | _Geçerli değil_ | Parça numarası yerine productOrderName ve MeterID kullanarak faturalama profiline ait fiyatı benzersiz bir şekilde tanımlayabilirsiniz. MCA faturasında parça numarası yerine alanlar listelenir. |
| Birim fiyat | unitPrice | Microsoft Müşteri Sözleşmesi birim fiyatı. |
| Para birimi kodu | PricingCurrency | Microsoft Müşteri Sözleşmeleri, fiyatları fiyatlandırma para birimi ve faturalandırma para birimi ile gösterir. Para birimi kodu, Microsoft Müşteri Sözleşmelerindeki pricingCurrency ile aynıdır. |
| Dahil edilen miktar | includedQuantity | Microsoft Müşteri Sözleşmeleri kapsamındaki hizmetler için geçerli değildir. Sıfır değerleriyle gösterilir. |
|  Teklif Kimliği  | productOrderName | OfferID yerine productOrderName kullanın. OfferID ile aynı değildir ancak productOrderName ve ölçüm, Microsoft Müşteri Sözleşmelerindeki fiyatlandırmayı belirler. Eski kayıtlardaki meterId ve OfferID ile ilgilidir. |

Microsoft Müşteri Sözleşmelerinin fiyatı, Kurumsal Anlaşmalardan farklı bir şekilde tanımlanır. Kurumsal kayıttaki hizmetlerin fiyatı ürün, parça numarası, ölçüm ve teklif için benzersizdir. Parça numarası, Microsoft Müşteri Sözleşmelerinde kullanılmaz.

Microsoft Müşteri Sözleşmesinin bir parçası olan Azure Tüketim hizmeti fiyatı, productOrderName ve meterID için benzersizdir. Bunlar hizmet ölçümünü ve ürün planını temsil eder.

Fiyat listesi ile Kullanım Ayrıntıları API'sindeki kullanımı mutabık kılmak için productOrderName ve meterID kullanabilirsiniz.

Faturalama profili için sahip, katkıda bulunan, okuyucu ve fatura yöneticisi haklarına sahip olan kullanıcılar fiyat listesini indirebilir.

Fiyat listesi, kullanıma göre fiyatlandırılan hizmetlerin listesini içerir. Hizmetler Azure tüketimini ve Market tüketimini kapsar. Her hizmet döneminin sonundaki en son fiyat kilitlenir ve tek bir hizmet dönemindeki kullanıma uygulanır. Azure tüketim hizmetlerinde hizmet süresi genellikle bir takvim ayıdır.

### <a name="retired-price-sheet-api-fields"></a>Kullanımdan Kaldırılan Fiyat Listesi API'si alanları

Aşağıdaki alanlar, Microsoft Müşteri Sözleşmesi Fiyat Listesi API'lerinde mevcut değildir veya değiştirilmemiştir.

|Kullanımdan kaldırılan alan| Description|
|---|---|
| billingPeriodId | Geçerli değil. MCA için InvoiceId alanına karşılık gelir. |
| offerID | Geçerli değildir. MCA için productOrderName alanına karşılık gelir. |
| meterCategory  | Geçerli değildir. MCA için Service alanına karşılık gelir. |
| unit | Geçerli değildir. unitOfMeasure değerinden ayrıştırılabilir. |
| currencyCode | MCA içindeki pricingCurrency ile aynıdır. |
| meterLocation | MCA içindeki meterRegion ile aynıdır. |
| partNumber partnumber | MCA faturalarında parça numarası listelenmediğinden geçerli değildir. Fiyatları benzersiz bir şekilde tanımlamak için bölüm numarası yerine meterId ve productOrderName birleşimini kullanın. |
| totalIncludedQuantity | Geçerli değildir. |
| pretaxStandardRate  | Geçerli değildir. |

## <a name="reservation-instance-charge-api-replaced"></a>Ayrılmış Örnek Ücretlendirme API'si değiştirildi

Satın alınan rezervasyonlara ait faturalandırma işlemlerini [Ayrılmış Örnek Ücretlendirme API'si](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) ile alabilirsiniz. Yeni API, üçüncü taraf Market teklifleri dahil olmak üzere tüm satın alma işlemlerini kapsar. Tüm Tüketim API'leri, kimlik doğrulaması ve yetkilendirme için Azure AD'yi kullanan yerel Azure API'leriyle değiştirilmiştir. Azure REST API'lerini çağırma hakkında daha fazla bilgi için bkz. [REST'i kullanmaya başlama](/rest/api/azure/#create-the-request). Ayrılmış Örnek Ücretlendirme API'si, İşlemler API'si ile değiştirilmiştir.

İşlemler API'si ile rezervasyon satın alma işlemlerini almak için:

| Yöntem | İstek URI'si |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Öneriler API'si değiştirildi

Ayrılmış Örnek Satın Alma Önerileri API'leri son 7, 30 veya 60 gün içindeki sanal makine kullanımını sunar. API'ler ayrıca rezervasyon satın alma önerileri de sunar. Bunlara aşağıdakiler dahildir:

- [Paylaşılan Ayrılmış Örnek Öneri API'si](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [Tek Ayrılmış Örnek Öneri API'si](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Tüm Tüketim API'leri, kimlik doğrulaması ve yetkilendirme için Azure AD'yi kullanan yerel Azure API'leriyle değiştirilmiştir. Azure REST API'lerini çağırma hakkında daha fazla bilgi için bkz. [REST'i kullanmaya başlama](/rest/api/azure/#create-the-request). Önceden listelenen rezervasyon öneri API'leri [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API'si ile değiştirilmiştir.

Rezervasyon Önerileri API'si ile rezervasyon önerileri almak için:

| Yöntem | İstek URI'si |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Rezervasyon Kullanımı API'leri değiştirildi

Ayrılmış Örnek Kullanımı API'si ile bir kayıt içindeki rezervasyon kullanımını alabilirsiniz. Kayıtta birden fazla rezervasyon örneği varsa bu API'yi kullanarak tüm rezervasyon örneği satın alma işlemlerinin kullanımını da alabilirsiniz.

Bunlara aşağıdakiler dahildir:

- [Ayrılmış Örnek Kullanımı ayrıntıları](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Ayrılmış Örnek Kullanımı Özeti](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Tüm Tüketim API'leri, kimlik doğrulaması ve yetkilendirme için Azure AD'yi kullanan yerel Azure API'leriyle değiştirilmiştir. Azure REST API'lerini çağırma hakkında daha fazla bilgi için bkz. [REST'i kullanmaya başlama](/rest/api/azure/#create-the-request). Önceden listelenen rezervasyon öneri API'leri [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) ve [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) API'leri ile değiştirilmiştir.

Rezervasyon Ayrıntıları API'si ile rezervasyon ayrıntılarını almak için:

| Yöntem | İstek URI'si |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Rezervasyon Özetleri API'si ile rezervasyon özetlerini almak için:

| Yöntem | İstek URI'si |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Cloudyn'den Maliyet Yönetimi'ne geçiş yapma

[Cloudyn](https://cloudyn.com) kullanan kuruluşların, maliyet yönetimi ihtiyaçları için [Azure Maliyet Yönetimi](https://azure.microsoft.com/services/cost-management/)'ni kullanmaya başlamaları önerilir. Maliyet Yönetimini herhangi bir kayıt işlemi olmaksızın ve sekiz saatlik gecikmeyle Azure portalından kullanabilirsiniz. Daha fazla bilgi için bkz. [Maliyet Yönetimi belgeleri](../index.yml).

Azure Maliyet Yönetimi ile şunları yapabilirsiniz:

- Önceden tanımlanmış bütçeye göre zaman içindeki maliyetleri görüntüleme. Harcama anomalilerini tespit edip durdurmak için günlük maliyet düzenlerini analiz etme. Maliyetleri etiketlere, kaynak grubuna, hizmete ve konuma göre ayırma.
- Kullanım ve maliyetler için limit belirleme amacıyla bütçeler oluşturma ve önemli eşiklere yaklaşıldığında bildirim alma. Özel olayları tetiklemek ve kalıcı limitler belirlemek için eylem grupları ile otomasyon gerçekleştirme.
- Azure Danışmanı önerileriyle maliyetleri ve kullanımı iyileştirme. Rezervasyonlar ile satın alma iyileştirme fırsatlarını keşfetme, az kullanılan sanal makinelerin boyutunu küçültme ve bütçe içinde kalmak için kullanılmayan kaynakları silme.
- Depolama hesabınızda günlük olarak yayımlanacak CSV biçiminde maliyet ve kullanım verileri raporu zamanlama. Faturalama verilerini eşitlemek ve güncel tutmak için dış sistemlerle tümleştirmeyi otomatikleştirme.

## <a name="power-bi-integration"></a>Power BI tümleştirme

Maliyet raporlama için Power BI'ı da kullanabilirsiniz. Power BI Desktop için [Azure Maliyet Yönetimi bağlayıcısı](/power-bi/desktop-connect-azure-cost-management) ile Azure harcamalarınızı daha iyi anlamanıza yardımcı olacak gelişmiş ve özelleştirilmiş raporlar oluşturabilirsiniz. Azure Maliyet Yönetimi bağlayıcısı şu anda Microsoft Müşteri Sözleşmesi veya Kurumsal Anlaşma (EA) sahibi olan müşterileri desteklemektedir.

## <a name="next-steps"></a>Sonraki adımlar

- Azure harcamalarınızı izleme ve denetleme hakkında bilgi edinmek için [Maliyet Yönetimi belgelerini](../index.yml) okuyun. Bu belgelerden Maliyet Yönetimi ile kaynak kullanımını iyileştirme yöntemlerini de öğrenebilirsiniz.
