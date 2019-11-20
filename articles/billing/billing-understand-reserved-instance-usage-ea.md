---
title: Kurumsal Anlaşmalar için Azure rezervasyonlarının kullanımını anlama
description: Kurumsal kaydınızda Azure rezervasyonunun nasıl uygulandığını anlamak için kullanım bilgilerinizi okumayı öğrenin.
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: f2f5b2ecf096d7dc8babb79a38d00158a2120688
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218079"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Kurumsal Anlaşma rezervasyon maliyetlerini ve kullanımını alma

Kurumsal Anlaşma müşterileri için rezervasyon maliyetleri ve kullanım verileri Azure portalında ve REST API'lerde sağlanır. Bu makale şu konularda size yardımcı olur:

- Rezervasyon satın alma verilerini alma
- Hangi aboneliğin, kaynak grubunun veya kaynağın rezervasyonu kullandığını öğrenme
- Rezervasyon kullanımı için ücreti yansıtma
- Rezervasyon tasarruflarını hesaplama
- Rezervasyonun kapasitesi altındaki kullanım verilerini alma
- Rezervasyon maliyetlerini amorti etme

Market ücretleri kullanım verileriyle birleştirilir. İlk parti kullanım, market kullanımı ve tek veri kaynağından yapılan satın almalar için ücretleri görüntülersiniz.

## <a name="reservation-charges-in-azure-usage-data"></a>Azure kullanım verilerinde rezervasyon ücretleri

Veriler iki ayrı veri kümesine bölünür: _Gerçek Maliyet_ ve _Amorti Edilmiş Maliyet_. Bu iki veri kümesi arasındaki farklar şunlardır:

**Gerçek Maliyet** - Aylık faturanızda mutabık kalmak için gereken verileri sağlar. Bu verilerde rezervasyon satın alma maliyetleri ve rezervasyon uygulama ayrıntıları vardır. Bu verilerle, belirli bir günde rezervasyon indirimini hangi aboneliğin, kaynak grubunun veya kaynağın aldığını bilebilirsiniz. Rezervasyon indirimini alan kullanım için EffectivePrice değeri sıfırdır.

**Amorti Edilmiş Maliyet** - Bu veri kümesi Gerçek Maliyet veri kümesine benzer; tek farkı rezervasyon indirimini alan kullanım için EffectivePrice değerinin (sıfır yerine) rezervasyonun eşit dağıtılmış maliyeti olmasıdır. Bu veri kümesi aboneliğe, kaynak grubuna veya kaynağa göre rezervasyon tüketiminin parasal değerini bilmenize yardımcı olur ve şirket içinde rezervasyon kullanımı ücretini yansıtmanıza katkıda bulunabilir. Veri kümesinde kullanılmayan rezervasyon saatleri de vardır. Veri kümesinde rezervasyon satın alma kayıtları yoktur. 

İki veri kümesinin karşılaştırması:

| Veriler | Gerçek Maliyet veri kümesi | Amorti Edilmiş Maliyet veri kümesi |
| --- | --- | --- |
| Rezervasyon satın almaları | Bu görünümde kullanılabilir.<br><br>  Bu verileri almak için ChargeType = &quot;Purchase&quot; filtresini uygulayın. <br><br> Ücretin hangi rezervasyona yönelik olduğunu öğrenmek için ReservationID veya ReservationName değerine bakın.  | Bu görünüm için geçerli değildir. <br><br> Amorti edilmiş verilerde satın alma maliyetleri sağlanmaz. |
| EffectivePrice | Rezervasyon indirimini alan kullanım için değeri sıfırdır. | Rezervasyon indirimini alan kullanım için değeri rezervasyonun günlük eşit dağıtılmış maliyetidir. |
| Kullanılmayan rezervasyon (Gün içinde rezervasyonun kullanılmadığı saat sayısını ve boşa harcanan parasal değeri sağlar) | Bu görünümde geçerli değildir. | Bu görünümde kullanılabilir.<br><br> Bu verileri almak için ChargeType = &quot;UnusedReservation&quot; filtresini uygulayın.<br><br>  Hangi rezervasyonun kapasitesinin altında kullanıldığını öğrenmek için ReservationID veya ReservationName değerine bakın. Bu, gün içinde rezervasyonun ne kadarının boşa harcandığını gösterir.  |
| UnitPrice (Fiyat listenizden kaynağın fiyatı) | Kullanılabilir | Kullanılabilir |

Azure kullanım verilerinde sağlanan diğer bilgiler değişmiştir:

- Ürün ve Ölçüm bilgileri - Azure daha önce yaptığı gibi başlangıçta tüketilen ölçümü ReservationId ve ReservationName ile değiştirmez.
- ReservationId ve ReservationName - Bunlar verilerde kendi alanlarında bulunur. Daha önce yalnızca AdditionalInfo altında sağlanıyordu.
- ProductOrderId - Kendi alanına eklenen rezervasyon sipariş kimliği.
- ProductOrderName - Satın alınan rezervasyonun ürün adı.
- Term - 12 ay veya 36 ay.
- RINormalizationRatio - AdditionalInfo altında sağlanır. Rezervasyonun kullanım kaydına uygulanma oranıdır. Rezervasyonunuzda örnek boyutu esnekliği etkinleştirildiyse diğer boyutlara da uygulanabilir. Değer, kullanım kaydı için rezervasyonun uygulanma oranını gösterir.

[Bkz. alan tanımı](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#definitions)

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>API'yi kullanarak Azure tüketim ve rezervasyon kullanımı verilerini alma

Verileri API'yi kullanarak alabilir veya Azure portalından indirebilirsiniz.

Yeni verileri almak için [Kullanım Ayrıntıları API’sini](/rest/api/consumption/usagedetails/list) çağırırsınız. Terminolojiyle ilgili ayrıntılar için bkz. [Kullanım terimleri](billing-understand-your-usage.md). Çağrıyı yapan kurumsal anlaşmanın Kuruluş Yöneticisi olmalı ve [EA portalını](https://ea.azure.com) kullanmalıdır. Salt okuma erişimli Kuruluş Yöneticileri de verileri alabilir.

Veriler, [Enterprise müşterileri için Raporlama API'leri - Kullanım Ayrıntıları](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)'nda sağlanmaz.

Aşağıda örnek bir API çağrısı verilmiştir:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

{enrollmentId} ve {billingPeriodId} hakkında daha fazla bilgi için bkz. [Kullanım Ayrıntıları – Liste](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) API makalesine bakın.

Aşağıdaki tabloda verilen ölçüm ve filtre bilgileri yaygın rezervasyon sorunlarını çözmeye yardımcı olabilir.

| **API verilerinin türü** | API çağrısı eylemi |
| --- | --- |
| **Tüm Ücretler (kullanım ve satın almalar)** | {metric} öğesini ActualCost ile değiştirin |
| **Rezervasyon indirimi alan kullanım** | {metric} öğesini ActualCost ile değiştirin<br><br>{filter} öğesini şununla değiştirin: properties/reservationId%20ne%20 |
| **Rezervasyon indirimi almayan kullanım** | {metric} öğesini ActualCost ile değiştirin<br><br>{filter} öğesini şununla değiştirin: properties/reservationId%20eq%20 |
| **Amorti edilmiş ücretler (kullanım ve satın almalar)** | {metric} öğesini AmortizedCost ile değiştirin |
| **Kullanılmayan rezervasyon raporu** | {metric} öğesini AmortizedCost ile değiştirin<br><br>{filter} öğesini şununla değiştirin: properties/ChargeType%20eq%20'UnusedReservation' |
| **Rezervasyon satın almaları** | {metric} öğesini ActualCost ile değiştirin<br><br>{filter} öğesini şununla değiştirin: properties/ChargeType%20eq%20'Purchase'  |
| **Para iadeleri** | {metric} öğesini ActualCost ile değiştirin<br><br>{filter} öğesini şununla değiştirin: properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Yeni verileri içeren kullanım CSV dosyasını indirme

EA yöneticisiyseniz yeni kullanım verilerini içeren CSV dosyasını Azure portalından indirebilirsiniz. Bu veriler [EA portalından](https://ea.azure.com) sağlanmaz.

Azure portalında [Maliyet yönetimi + faturalama](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts)'ya gidin.

1. Ödeme hesabını seçin.
2. **Kullanım + ücretler**'e tıklayın.
3. **İndir**’e tıklayın.  
![Azure portalında CSV kullanım verileri dosyasının nereden indirileceğini gösteren örnek](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. **Kullanımı + Ücretleri İndir**'deki **Kullanım Ayrıntıları Sürüm 2**'nin altında **Tüm Ücretler (kullanım ve satın almalar)** öğesini seçin ve indir düğmesine tıklayın. **Amorti edilmiş ücretler (kullanım ve satın almalar)** için işlemi yineleyin.

İndirdiğiniz CSV dosyaları gerçek maliyetleri ve amorti edilmiş maliyetleri içerir.

## <a name="common-cost-and-usage-tasks"></a>Ortak maliyet ve kullanım görevleri

Aşağıdaki bölümler çoğu kişinin rezervasyon maliyeti ve kullanımı verilerini görüntülemek için kullandığı ortak görevleri içerir.

### <a name="get-reservation-purchase-costs"></a>Rezervasyon satın alma maliyetlerini alma

Rezervasyon satın alma maliyetleri Gerçek Maliyet verilerinde sağlanır. _ChargeType = Purchase_ filtresini uygulayın. Satın almanın hangi rezervasyon siparişi için olduğunu belirlemek için ProductOrderID öğesine bakın.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Kapasitesinin altında kullanılan rezervasyon miktarını ve maliyetlerini alma

Amorti Edilmiş Maliyet verilerini alın ve _ChargeType_ _= UnusedReservation_ filtresini uygulayın. Günlük kullanılmayan rezervasyon miktarını ve maliyeti alırsınız. Rezervasyon ve rezervasyon siparişi için verileri sırasıyla _ReservationId_ ve _ProductOrderId_ alanlarını kullanarak filtreleyebilirsiniz. Rezervasyon %100 kullanıldıysa kaydın miktarı 0 olur.

### <a name="amortize-reservation-costs"></a>Rezervasyon maliyetlerini amorti etme

Rezervasyonun günlük amorti edilmiş maliyetlerini elde etmek için Amorti Edilmiş Maliyeti alın ve _ProductOrderID_ alanını kullanarak bir rezervasyon siparişi için filtreleyin.

### <a name="chargeback-for-a-reservation"></a>Rezervasyon için ücreti yansıtma

Rezervasyon kullanımının ücretini aboneliğe, kaynak gruplarına veya etiketlere göre başka organizasyonlara yansıtabilirsiniz. Amorti edilmiş maliyet verileri aşağıdaki veri türlerinde rezervasyon kullanımının parasal değerini sağlar:

- Kaynaklar (VM gibi)
- Kaynak grubu
- Etiketler
- Abonelik

### <a name="get-the-blended-rate-for-chargeback"></a>Ücret yansıtma için karışık fiyatı alma

Karışık fiyatı belirlemek için amorti edilmiş maliyet verilerini alın ve toplam maliyeti toplayın. VM'ler için AdditionalInfo JSON verilerinden MeterName veya ServiceType bilgilerini kullanabilirsiniz. Karışık fiyatı elde etmek için toplam maliyeti kullanılan miktara bölün.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Örnek boyutu esnekliği için en uygun rezervasyon kullanımını denetleme

Miktarı AdditionalInfo verilerinden _RINormalizationRatio_ ile çarpın. Sonuçlar kullanım kaydına kaç saatlik rezervasyon kullanımının uygulandığını gösterir.

### <a name="determine-reservation-savings"></a>Rezervasyon tasarruflarını belirleme

Amorti edilmiş maliyet verilerini alın ve verileri ayrılmış örnek için filtreleyin. Ardından:

1. Tahmini kullandıkça öde maliyetlerini elde edin. Kullanıma rezervasyon indirimi uygulanmadıysa, tahmini kullandıkça öde maliyetlerini elde etmek için _UnitPrice_ değerini _Quantity_ değerleriyle çarpın.
2. Rezervasyon maliyetlerini elde edin. Ayrılmış örneğe yaptığınız ödemenin parasal değerini elde etmek için _Cost_ değerlerini toplayın. Bu değer rezervasyonun kullanılan ve kullanılmayan maliyetlerini içerir.
3. Tahmini tasarrufları elde etmek için rezervasyon maliyetlerini tahmini kullandıkça öde maliyetlerinden çıkarın.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>Maliyet analizinde rezervasyon satın almaları ve amortisman

Rezervasyon maliyetleri, [maliyet analizinde](https://aka.ms/costanalysis) kullanılabilir. Maliyet analizi varsayılan olarak **Gerçek maliyeti** gösterir; faturanızda maliyetler böyle gösterilecektir. Dökümü alınmış ve avantajı kullanan kaynaklarla ilişkilendirilmiş rezervasyon satın almalarını görüntülemek için **Amorti edilmiş maliyete** geçin:

![Maliyet analizinde amorti edilmiş maliyetin nerede seçileceğini gösteren örnek](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Kullanımın, satın almaların ve para iadelerinin dökümünü görmek için ücret türüne göre gruplandırın; rezervasyon ve isteğe bağlı maliyetlerin dökümü için rezervasyona göre gruplandırın. Gerçek maliyete baktığınızda göreceğiniz tek rezervasyon maliyetinin satın almalar olduğunu unutmayın. Öte yandan amorti edilmiş maliyete baktığınızda maliyetler avantajı kullanan tek tek kaynaklara ayrılmış olacaktır. Amorti edilmiş maliyete baktığınızda bir de yeni **UnusedReservation** ücret türünü görürsünüz.

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure Ayrılmış Sanal Makine Örnekleri nedir?](billing-save-compute-costs-reservations.md)
- [Azure Ayrılmış VM Örnekleri ile Sanal Makinelere ön ödeme yapma](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Veritabanı ayrılmış kapasitesi ile SQL Veritabanı işlem kaynakları için ön ödeme yapma](../sql-database/sql-database-reserved-capacity.md)
- [Azure Ayırmalarını yönetme](billing-manage-reserved-vm-instance.md)
- [Rezervasyon indiriminin nasıl uygulandığını anlama](billing-understand-vm-reservation-charges.md)
- [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage.md)
- [Rezervasyonlara dahil olmayan Windows yazılımı maliyetleri](billing-reserved-instance-windows-software-costs.md)
