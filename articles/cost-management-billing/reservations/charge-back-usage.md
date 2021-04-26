---
title: Azure Rezervasyon maliyetlerini geri ödeme
description: Geri ödeme için Azure Rezervasyon maliyetlerini görüntülemeyi öğrenin.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: 4059318e6b8052f3b0221c87e8a357cfc8679e44
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532757"
---
# <a name="charge-back-azure-reservation-costs"></a>Azure Rezervasyon maliyetlerini geri ödeme

Kurumsal Anlaşma ve Microsoft Müşteri Sözleşmesi okuyucuları, rezervasyonlar için itfa edilen maliyet verilerini görüntüleyebilir. Bu kişiler maliyet verilerini bir abonelik, kaynak grubu, kaynak veya iş ortaklarına yönelik bir etiketin parasal değerini geri ödemek üzere kullanabilirler. İtfa edilmiş verilerde geçerli fiyat, eşit olarak bölünmüş saatlik rezervasyon maliyetidir. Maliyet, ilgili günde kaynağa göre rezervasyon kullanımının toplam maliyetidir.

Tek aboneliğe sahip kullanıcılar, itfa edilmiş maliyet verilerini kullanım dosyalarından alabilir. Bir kaynağa rezervasyon indirimi yapıldığında, kullanım dosyasındaki *AdditionalInfo* bölümünde rezervasyon ayrıntıları yer alır. Daha fazla bilgi için bkz. [Azure portalından kullanımı indirme](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv).

## <a name="see-reservation-usage-data-for-show-back-and-charge-back"></a>Geri göstermek ve geri ücret için ayırma kullanım verilerine bakın

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Maliyet Yönetimi + Faturalama**’ya gidin 
3. Sol gezinmede **maliyet analizini** seçin 
4. **Gerçek Maliyet** bölümünde **İtfa Edilen Maliyet** ölçümünü seçin.
5. Rezervasyon tarafından kullanılan kaynakları görmek için **Rezervasyon**’a bir filtre uygulayın ve sonra rezervasyonları seçin.
6. **Ayrıntı düzeyi**'ni **Aylık** veya **Günlük** olarak ayarlayın.
7. Grafik türünü **Tablo** olarak ayarlayın.
8. **Gruplandırma ölçütü** seçeneğini **Kaynak** olarak ayarlayın.

[![Geri ödeme için kullanabileceğiniz rezervasyon kaynağı maliyetlerini gösteren örnek](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

İşte, Azure portal abonelik, kaynak grubu ve kaynak düzeyinde rezervasyon kullanım maliyetlerinin nasıl görüntüleneceğini gösteren bir video.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="get-the-data-for-show-back-and-charge-back"></a>Geri göstermek ve geri ücret almak için verileri alın
1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Maliyet Yönetimi + Faturalama**’ya gidin 
3. Sol gezinmede **dışarı aktar** 'ı seçin 
4. **Ekle** düğmesine tıklayın
5. Ölçüm düğmesi olarak Itfası maliyeti ' ni seçin ve dışa aktarma dosyanızı ayarlayın

rezervasyon iskontosunu alan kullanım için uygulanan bir ücret, rezervasyonun eşit olarak dağıtılmış maliyetidir (sıfır yerine). Bu veri kümesi aboneliğe, kaynak grubuna veya kaynağa göre rezervasyon tüketiminin parasal değerini bilmenize yardımcı olur ve şirket içinde rezervasyon kullanımı ücretini yansıtmanıza katkıda bulunabilir. Veri kümesinde kullanılmayan rezervasyon saatleri de vardır. 

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>API'yi kullanarak Azure tüketim ve rezervasyon kullanımı verilerini alma

Verileri API'yi kullanarak alabilir veya Azure portalından indirebilirsiniz.

Yeni verileri almak için [Kullanım Ayrıntıları API’sini](/rest/api/consumption/usagedetails/list) çağırırsınız. Terminolojiyle ilgili ayrıntılar için bkz. [Kullanım terimleri](../understand/understand-usage.md).

Aşağıda örnek bir Kullanım Ayrıntıları API’si çağrısı verilmiştir:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

{enrollmentId} ve {billingPeriodId} hakkında daha fazla bilgi için bkz. [Kullanım Ayrıntıları – Liste](/rest/api/consumption/usagedetails/list) API makalesine bakın.

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

EA yöneticisiyseniz, Azure portal yeni kullanım verilerini içeren CSV dosyasını indirebilirsiniz. Bu verilere EA portalından erişilemez (ea.azure.com). Yeni verileri görmek için Azure portalından (portal.azure.com) kullanım dosyasını indirmeniz gerekir.

Azure portalında [Maliyet yönetimi + faturalama](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts)'ya gidin.

1. Ödeme hesabını seçin.
2. **Kullanım + ücretler**'e tıklayın.
3. **İndir**’e tıklayın.  
![Azure portalında CSV kullanım verileri dosyasının nereden indirileceğini gösteren örnek](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. **Kullanım ayrıntıları**' nda, **itfası kullanımı verileri**' ni seçin.

İndirdiğiniz CSV dosyaları gerçek maliyetleri ve amorti edilmiş maliyetleri içerir.

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Sonraki adımlar
- Azure ayırmaları kullanım verileri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
  - [Kurumsal Anlaşma ve Microsoft müşteri anlaşması rezervasyon maliyeti ve kullanımı](understand-reserved-instance-usage-ea.md)
 
