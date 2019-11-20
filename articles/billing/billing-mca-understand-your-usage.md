---
title: Microsoft Müşteri Sözleşmesi için Azure kullanımı ve ücretleri dosyasındaki terimler
description: Faturalama profiliniz için Azure kullanım bilgilerini ve ücretlerini içeren CSV dosyasının bölümlerinin nasıl okunup anlaşılacağını öğrenin.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c0c43c779affb4edca78def95906f5adfcc6fac4
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709458"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Microsoft Müşteri Sözleşmesi için Azure kullanımı ve ücretleri dosyasındaki terimler

Bu makale, Microsoft Müşteri Sözleşmesi’ne ilişkin ödeme hesabı için geçerlidir. [Microsoft Müşteri Sözleşmesi’ne erişiminiz olup olmadığını denetleyin](#check-access-to-a-microsoft-customer-agreement).

Azure kullanım bilgilerini ve ücretlerini içeren CSV dosyası, geçerli faturalama dönemi için günlük ve ölçüm düzeyinde kullanım ücretlerini sunar.

Azure kullanım bilgilerinizi ve ücretlerinizi içeren dosyayı almak için bkz. [Microsoft Müşteri Sözleşmeniz için Azure kullanım bilgilerinizi ve ücretlerinizi görüntüleme ve indirme](billing-download-azure-daily-usage.md). Bu, bir elektronik tablo uygulamasında açabileceğiniz virgülle ayrılmış değerler (.csv) dosya biçiminde mevcuttur.

Kullanım ücretleri bir abonelikteki toplam **aylık** ücretlerdir. Kullanım ücretleri, kredilerin veya indirimlerin hiçbirini dikkate almaz.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Azure Kurumsal Anlaşma kullanımı ve ücretlerindeki değişiklikler

Kurumsal Anlaşma müşterisiyseniz, Azure faturalama profili kullanım bilgilerini içeren CSV dosyasındaki terimlerin, Azure Kurumsal Anlaşma kullanım bilgilerini içeren CSV dosyasındaki terimlerden farklılık gösterdiğini göreceksiniz. Kurumsal Anlaşma kullanımı terimlerinin faturalama profili kullanımı terimleri ile eşlemesi aşağıda verilmiştir:

| Azure Kurumsal Anlaşma kullanım bilgilerini içeren CSV dosyası | Microsoft Müşteri Sözleşmesi Azure kullanım bilgilerini ve ücretlerini içeren CSV dosyası |
| --- | --- |
| Tarih | date |
| Ay| date |
| Gün | date |
| Yıl | date |
| Ürün | ürün |
| MeterId | meterID |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | miktar |
| ResourceRate | effectivePrice |
| ExtendedCost | maliyet |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| HizmetBilgisi2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| Etiketler | etiketler |
| StoreServiceIdentifier | Yok |
| DepartmentName | invoiceSection |
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| adlı yönetilen örnek, | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>Ayrıntılı terimler ve açıklamaları

Azure kullanım bilgilerini ve ücretlerini içeren dosyada aşağıdaki terimler gösterilmektedir.

Sözleşme Dönemi | Açıklama
--- | ---
invoiceId | Fatura PDF dosyasında listelenen benzersiz belge kimliği
previousInvoiceId | Bu kalemin para iadesi olması durumunda asıl faturanın başvurusu
billingAccountName | Ödeme hesabının adı
billingAccountId | Kök ödeme hesabının benzersiz tanımlayıcısı
billingProfileId | Faturalanan ücretleri tahakkuk eden faturalama profilinin adı
billingProfileName | Faturalanan ücretleri tahakkuk eden Faturalama Profilinin benzersiz tanımlayıcısı
invoiceSectionId | Fatura bölümünün benzersiz tanımlayıcısı
invoiceSectionName | Fatura bölümünün adı
costCenter | Maliyetleri izlemek için abonelikte tanımlanan maliyet merkezi (yalnızca açık faturalama döneminde kullanılabilir)
billingPeriodStartDate | Faturanın oluşturulduğu faturalama döneminin başlangıç tarihi
billingPeriodEndDate | Faturanın oluşturulduğu faturalama döneminin bitiş tarihi
servicePeriodStartDate | Kullanılan veya satın alınan hizmet için fiyatlandırmanın tanımlandığı ve kilitlendiği değerlendirme döneminin başlangıç tarihi
servicePeriodEndDate | Kullanılan veya satın alınan hizmet için fiyatlandırmanın tanımlandığı ve kilitlendiği değerlendirme döneminin bitiş tarihi
date | Azure ve Market kullanım tabanlı ücretleri için bu, değerlendirme tarihidir. Tek seferlik satın alımlar (Rezervasyonlar, Market) veya sabit yinelenen ücretler (destek teklifleri) için bu, satın alma tarihidir.
serviceFamily | Hizmetin ait olduğu hizmet ailesi
productOrderId | Ürün siparişinin benzersiz tanımlayıcısı
productOrderName | Ürün siparişinin benzersiz adı
consumedService | Kullanılan hizmetin adı
meterId | Ölçümün benzersiz tanımlayıcısı
meterName | Ölçümün adı
meterCategory | Ölçüm için sınıflandırma kategorisinin adı. Örneğin, *Bulut hizmetleri*, *Ağ iletişimi* vb.
meterSubCategory | Ölçüm alt sınıflandırma kategorisinin adı
meterRegion | Hizmet ölçümünün kullanılabilir olduğu bölgenin adı. Veri merkezi konumuna göre fiyatlandırılan belirli hizmetler için veri merkezinin konumunu belirtir.
teklif | Satın alınan teklifin adı
productId | Ücretleri tahakkuk eden ürünün benzersiz tanımlayıcısı
ürün | Ücretleri tahakkuk eden ürünün adı
abonelik kimliği | Ücretleri tahakkuk eden aboneliğin benzersiz tanımlayıcısı
subscriptionName | Ücretleri tahakkuk eden aboneliğin adı
reservationId | Satın alınan rezervasyon örneğinin benzersiz tanımlayıcısı
reservationName | Satın alınan rezervasyon örneğinin adı
publisherType | Yayımcı türü (Değerler: firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | Market hizmetlerinin yayımcısı
resourceGroupId | Kaynakla ilişkili kaynak grubunun benzersiz tanımlayıcısı
resourceGroupName | Kaynakla ilişkili kaynak grubunun adı
resourceId | Kaynak örneğinin benzersiz tanımlayıcısı
resourceType | Kaynak örneğinin türü
resourceLocation | Kaynağın çalıştığı veri merkezinin konumunu belirtir.
location | Aynı bölgeler için farklı kaynak konumları yapılandırılmışsa kaynağın normalleştirilmiş konumu
miktar | Satın alınan veya kullanılan birim sayısı
unitOfMeasure | Hizmet faturalamasının ölçü birimi. Örneğin, işlem hizmetleri saat bazında faturalandırılır.
chargeType | Ücret türü. Değerler: <ul><li>AsCharged-Usage: Bir Azure hizmetinin kullanımına göre tahakkuk eden ücretler. Ayrılmış örnekler nedeniyle ücretlendirilmemiş olan sanal makinelere karşı kullanım buna dahildir.</li><li>AsCharged-PurchaseMarketplace: Market satın alımlarındaki bir kerelik veya sabit yinelenen ücretler</li><li>AsCharged-UsageMarketplace: Tüketim birimlerine göre ücretlendirilen Market hizmetleri için ücretler</li></ul>
isAzureCreditEligible | Hizmet ücretinin, Azure kredileri kullanılarak ödenmeye uygun olup olmadığını belirten işaret (Değerler: True, False)
serviceInfo1 | Hizmete özgü meta veriler
serviceInfo2 | Hizmete özgü isteğe bağlı meta verileri yakalayan eski alan
additionalInfo | Hizmete özgü ek meta veriler.
etiketler | Kaynağa atadığınız etiketler

### <a name="make-sure-that-charges-are-correct"></a>Ücretlerin doğru olduğundan emin olun

Ayrıntılı kullanım dosyanızdaki ücretlerin doğru olduğundan emin olmak istiyorsanız bunları doğrulayabilirsiniz. Bkz. [Faturalama profilinizin faturasındaki ücretleri anlama](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft Müşteri Sözleşmesi’ne erişimi denetleme
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

- [Microsoft Azure faturanızı görüntüleme ve indirme](billing-download-azure-invoice.md)
- [Microsoft Azure kullanımınızı ve ücretlerinizi görüntüleme ve indirme](billing-download-azure-daily-usage.md)
