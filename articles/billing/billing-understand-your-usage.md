---
title: Ayrıntılı kullanım ve ücretlerinizi anlama | Microsoft Docs
description: Ayrıntılı kullanım ve ücretlerinizi okumayı ve anlamayı öğrenin
author: bandersmsft
manager: micflan
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 1842d32a838470d9b2af3a778c44c37464d32294
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "68954335"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Azure kullanımı ve ücretleri dosyasındaki terimleri anlama

Ayrıntılı kullanım ve ücretler dosyası belirtilen dönemin anlaşmalı fiyatlarını, satın almaları (örneğin rezervasyonlar, Market ücretleri) ve para iadelerine bağlı olarak günlük derecelendirilmiş kullanımı içerir.
Bu ücretler kredileri, vergileri veya diğer ücret ve indirimleri içermez.
Her hesap türü için hangi ücretlerin dahil edildiğini aşağıdaki tabloda bulabilirsiniz.

Hesap türü | Azure kullanımı | Market kullanımı | Satın almalar | Para iadeleri
--- | --- | --- | --- | ---
Kurumsal Anlaşma (EA) | Evet | Evet | Evet | Hayır
Microsoft Müşteri Sözleşmesi (MCA) | Evet | Evet | Evet | Evet
Kullandıkça Öde (PAYG) | Evet | Evet | Hayır | Hayır

Market siparişleri (dış hizmetler olarak da bilinir) hakkında daha fazla bilgi için bkz. [Azure dış hizmet ücretlerinizi anlama](billing-understand-your-azure-marketplace-charges.md).

İndirme yönergeleri için bkz. [Azure ödeme faturanızı ve günlük kullanım verilerinizi alma](billing-download-azure-invoice-daily-usage-date.md).
Kullanım ve ücretler CSV dosyanızı Microsoft Excel'de veya başka bir elektronik tablo uygulamasında açabilirsiniz.

## <a name="list-of-terms-and-descriptions"></a>Terim ve açıklamalar listesi

Aşağıdaki tabloda Azure kullanımı ve ücretleri dosyasının en son sürümünde kullanılan önemli terimler açıklanır.
Liste Kullandıkça öde (PAYG), Kurumsal Anlaşma (EA), ve Microsoft Müşteri Sözleşmesi (MCA) hesaplarını içerir.

Sözleşme Dönemi | Hesap türü | Açıklama
--- | --- | ---
AccountName | EA, PAYG | EA kayıt hesabı veya PAYG ödeme hesabının görünen adı.
AccountOwnerId<sup>1</sup> | EA, PAYG | EA kayıt hesabı veya PAYG ödeme hesabının benzersiz tanımlayıcısı.
AdditionalInfo | Tümü | Hizmete özgü meta veriler. Örneğin, sanal makinenin görüntü türü.
BillingAccountId<sup>1</sup> | Tümü | Kök ödeme hesabının benzersiz tanımlayıcısı.
BillingAccountName | Tümü | Ödeme hesabının adı.
BillingCurrency | Tümü | Ödeme hesabıyla ilişkilendirilmiş para birimi.
BillingPeriod | EA, PAYG | Ücretin faturalama dönemi.
BillingPeriodEndDate | Tümü | Faturalama döneminin bitiş tarihi.
BillingPeriodStartDate | Tümü | Faturalama döneminin başlangıç tarihi.
BillingProfileId<sup>1</sup> | Tümü | EA kaydının, PAYG aboneliğinin, MCA faturalama profilinin veya AWS birleştirilmiş hesabının benzersiz tanımlayıcısı.
BillingProfileName | Tümü | EA kaydının, PAYG aboneliğinin, MCA faturalama profilinin veya AWS birleştirilmiş hesabının adı.
ChargeType | Tümü | Ücretin kullanımı mı (**Kullanım**), satın alımı mı (**Satın Alma**) yoksa para iadesini (**Para İadesi**) mi temsil ettiğini gösterir.
ConsumedService | Tümü | Ücretin ilişkilendirildiği hizmetin adı.
CostCenter<sup>1</sup> | EA, MCA | Maliyetleri izleme amacıyla abonelik için tanımlanan maliyet merkezi (yalnızca MCA hesapları için açık faturalama dönemlerinde mevcuttur).
Maliyet | EA, PAYG | Bkz. CostInBillingCurrency.
CostInBillingCurrency | MCA | Krediler veya vergilerden önce ücretin faturalama para birimi cinsinden maliyeti.
CostInPricingCurrency | MCA | Krediler veya vergilerden önce ücretin fiyatlandırma para birimi cinsinden maliyeti.
Para birimi | EA, PAYG | Bkz. BillingCurrency.
Date<sup>1</sup> | Tümü | Ücretin kullanım veya satın alma tarihi.
EffectivePrice | Tümü | Dönem için karıştırılan birim fiyatı. Karıştırılan fiyatlarda, kademeli katmanlama gibi birim fiyatındaki tüm dalgalanmaların ortalaması alınır; dolayısıyla zamanla miktar arttıkça fiyat düşer.
ExchangeRateDate | MCA | Döviz kurunun belirlendiği tarih.
ExchangeRatePricingToBilling | MCA | Fiyatlandırma para birimindeki maliyeti faturalama para birimine dönüştürürken kullanılan döviz kuru.
Frequency | Tümü | Bir ücretin tekrarlanmasının beklenip beklenmediğini gösterir. Ücretler bir kez gerçekleşebilir (**OneTime**), aylık veya yıllık temelde tekrarlanabilir (**Recurring**) ya da kullanıma dayalı olabilir (**UsageBased**).
InvoiceId | PAYG, MCA | Fatura PDF dosyasında listelenen benzersiz belge kimliği.
InvoiceSection | MCA | Bkz. InvoiceSectionName.
InvoiceSectionId<sup>1</sup> | EA, MCA | EA bölümünün veya MCA fatura bölümünün benzersiz tanımlayıcısı.
InvoiceSectionName | EA, MCA | EA bölümünün veya MCA fatura bölümünün adı.
IsAzureCreditEligible | Tümü | Ücretin Azure kredileri kullanılarak ödenmeye uygun olup olmadığını belirtir(Değerler: True, False).
Konum | MCA | Kaynağın çalıştırıldığı veri merkezi konumu.
MeterCategory | Tümü | Ölçüm için sınıflandırma kategorisinin adı. Örneğin *Bulut hizmetleri* ve *Ağ iletişimi*.
MeterId<sup>1</sup> | Tümü | Ölçümün benzersiz tanımlayıcısı.
MeterName | Tümü | Ölçümün adı.
MeterRegion | Tümü | Konuma dayalı olarak fiyatlandırılan hizmetler için veri merkezi konumunun adı. Bkz. Location.
MeterSubCategory | Tümü | Ölçüm alt sınıflandırma kategorisinin adı.
OfferId<sup>1</sup> | Tümü | Satın alınan teklifin adı.
PartNumber<sup>1</sup> | EA, PAYG | Belirli bir ölçüm fiyatlandırmasını almak için kullanılan tanımlayıcı.
PlanName | EA, PAYG | Market planı adı.
PreviousInvoiceId | MCA | Bu satır öğesinde para iadesi yapılıyorsa özgün faturanın başvurusu.
PricingCurrency | MCA | Anlaşmalı fiyatlar temelinde derecelendirirken kullanılan para birimi.
Ürün | Tümü | Ürünün adı.
ProductId<sup>1</sup> | MCA | Ürünün benzersiz tanımlayıcısı.
ProductOrderId | Tümü | Ürün siparişinin benzersiz tanımlayıcısı.
ProductOrderName | Tümü | Ürün siparişinin benzersiz adı.
PublisherName | Tümü | Market hizmetlerinin yayımcısı.
PublisherType | Tümü | Yayımcı türü (Değerler: **Azure**, **AWS**, **Market**).
Miktar | Tümü | Satın alınan veya tüketilen birim sayısı.
ReservationId | EA, MCA | Satın alınan rezervasyon örneğinin benzersiz tanımlayıcısı.
ReservationName | EA, MCA | Satın alınan rezervasyon örneğinin adı.
adlı yönetilen örnek, | Tümü | Kaynağın içinde bulunduğu [kaynak grubunun](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) adı.
ResourceId<sup>1</sup> | Tümü | [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) kaynağının benzersiz tanımlayıcısı.
ResourceLocation | Tümü | Kaynağın çalıştırıldığı veri merkezi konumu. Bkz. Location.
ResourceName | EA, PAYG | Kaynağın adı.
ResourceType | MCA | Kaynak örneğinin türü.
ServiceFamily | MCA | Hizmetin ait olduğu hizmet ailesi.
ServiceInfo1 | Tümü | Hizmete özgü meta veriler.
ServiceInfo2 | Tümü | Hizmete özgü isteğe bağlı meta verilerin bulunduğu eski alan.
ServicePeriodEndDate | MCA | Kullanılan veya satın alınan hizmet için fiyatlandırmanın tanımlandığı ve kilitlendiği değerlendirme döneminin bitiş tarihi.
ServicePeriodStartDate | MCA | Kullanılan veya satın alınan hizmet için fiyatlandırmanın tanımlandığı ve kilitlendiği değerlendirme döneminin başlangıç tarihi.
SubscriptionId<sup>1</sup> | Tümü | Azure aboneliğinin benzersiz tanımlayıcısı.
SubscriptionName | Tümü | Azure aboneliğinin adı.
Tags<sup>1</sup> | Tümü | Kaynağa atanan etiketler. Kaynak grubu etiketlerini içermez. Kuruluş içinde geri ödeme için maliyetleri gruplandırırken veya dağıtırken kullanılabilir. Daha fazla bilgi için bkz. [Azure kaynaklarınızı etiketlerle düzenleme](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Sözleşme Dönemi | Tümü | Teklifin geçerlilik süresini görüntüler. Örnek: Ayrılmış örnekler söz konusu olduğunda Dönem olarak 12 ay görüntüler. Tek seferlik satın almalar veya yinelenen satın almalar için Dönem 1 aydır (SaaS, Market Desteği). Bu Azure tüketimi için geçerli değildir.
UnitOfMeasure | Tümü | Hizmet faturalamasının ölçü birimi. Örneğin, işlem hizmetleri saat bazında faturalandırılır.
UnitPrice | EA, PAYG | Ücret için birim başına fiyat.

_<sup>**1**</sup> Tek maliyet kaydında benzersiz kimliği oluşturmak için kullanılan alanlar._

Hesap türleri arasında bazı alanların büyük/küçük harf ve boşluk kullanımında farklılıklar olduğuna dikkat edin.
Kullandıkça öde kullanım dosyalarının eski sürümlerinde hesap özeti ile günlük kullanım için ayrı bölümler vardır.

### <a name="list-of-terms-from-older-apis"></a>Eski API'lerde kullanılan terimlerin listesi
Aşağıdaki tabloda eski API'lerde kullanılan terimlerle yeni terimler eşlenir. Açıklamaları için yukarıdaki tabloya bakın.

Eski terim | Yeni terim
--- | ---
ConsumedQuantity | Miktar
IncludedQuantity | Yok
InstanceId | ResourceId
Fiyat | EffectivePrice
Birim | UnitOfMeasure
UsageDate | Tarih
UsageEnd | Tarih
UsageStart | Tarih


## <a name="ensure-charges-are-correct"></a>Ücretlerin doğru olduğundan emin olun

Ayrıntılı kullanım ve ücretler hakkında daha fazla bilgi edinmek için [kullandıkça öde](./billing-understand-your-bill.md) veya [Microsoft Müşteri Sözleşmesi](billing-mca-understand-your-bill.md) faturanızı anlamayla ilgili makaleleri okuyun.

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

- [Microsoft Azure faturanızı görüntüleme ve indirme](billing-download-azure-invoice.md)
- [Microsoft Azure kullanımınızı ve ücretlerinizi görüntüleme ve indirme](billing-download-azure-daily-usage.md)
