---
title: Azure Databricks önceden satın alma indiriminin uygulanması
description: Azure Databricks önceden satın alma indiriminin kullanımınıza nasıl uygulandığı hakkında bilgi edinin.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 9651a61cb7b5c995a46571b7628d5416c08f4161
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719659"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Azure Databricks önceden satın alma indiriminin uygulanması

Satın alma dönemi boyunca istediğiniz zaman, önceden satın alınan Azure Databricks işleme birimlerini (DBCU) kullanabilirsiniz. Tüm Azure Databricks kullanımı, önceden satın alınan DBCU’lardan otomatik olarak düşülür.

Sanal makinelerden farklı olarak, önceden satın alınan birimlerin süresi saat bazında dolmaz. Satın alma dönemi boyunca istediğiniz zaman bunları kullanabilirsiniz. Önceden satın alma indirimleri almak için kullanıma ilişkin Azure Databricks çalışma alanlarınıza önceden satın alınan bir planı yeniden dağıtmanız veya atamanız gerekmez.

Önceden satın alma indirimi yalnızca Azure Databricks birimi (DBU) kullanımı için geçerlidir. İşlem, depolama ve ağ iletişimi gibi diğer ücretler ayrı olarak ücretlendirilir.

## <a name="pre-purchase-discount-application"></a>Önceden satın alma indiriminin uygulanması:

Databricks önceden satın alımı, tüm Databricks iş yükleri ve katmanları için geçerlidir. Önceden satın alımı, ön ödemeli Databricks işleme birimleri havuzu gibi düşünebilirsiniz. Kullanım, iş yüküne veya katmana bakılmaksızın havuzdan düşülür. Kullanım şu oranda düşülür:

| **İş yükü** | **DBU uygulama oranı - Standart katman** | **DBU uygulama oranı - Premium katman** |
| --- | --- | --- |
| Veri Analizi | 0,4 | 0,55 |
| Veri Mühendisliği | 0,15 | 0,30 |
| Veri Mühendisliği Hafif | 0,07 | 0,22 |

Örneğin, bir Veri Analizi - Standart katman miktarı kullanıldığında, önceden satın alınan Databricks işleme birimleri, 0,4 birim düşülür. Bir Veri Mühendisliği Hafif - Standart katman miktarı kullanıldığında, önceden satın alınan Databricks işleme birimleri, 0,07 birim düşülür

## <a name="determine-plan-use"></a>Plan kullanımını belirleme

Kullanılacak DBCU planınızı belirlemek için Azure portalı > **Rezervasyonlar**’a gidin ve satın alınan Databricks planına tıklayın. Kullanım bitiş tarihiniz, kalan birimlerle birlikte gösterilir. Rezervasyon kullanımınızı belirleme hakkında daha fazla bilgi için [Rezervasyon kullanımını görüntüleme](billing-reservation-apis.md#see-reservation-usage) makalesine bakın.

## <a name="how-discount-application-shows-in-usage-data"></a>Kullanım verilerinde indirim uygulamasının gösterilmesi

Databricks kullanımınıza önceden satın alma indirimi uygulandığında, iste bağlı ücretler kullanım verilerinde sıfır olarak görüntülenir. Rezervasyon maliyetleri ve kullanımı hakkında daha fazla bilgi için bkz. [Kurumsal Anlaşma rezervasyon maliyetleri ve kullanım bilgilerini alma](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Sonraki adımlar

- Rezervasyonu yönetme hakkında bilgi edinmek için bkz. [Azure Ayrılmış Sanal Makine Örnekleri’ni Yönetme](billing-manage-reserved-vm-instance.md).
- Para tasarrufu sağlamak için Azure Databricks’i önceden satın alma hakkında daha fazla bilgi edinmek için bkz. [Önceden satın alma ile Azure Databricks maliyetlerini iyileştirme](billing-prepay-databricks-reserved-capacity.md).
- Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
  - [Azure Ayrılmış Sanal Makine Örnekleri nedir?](billing-save-compute-costs-reservations.md)
  - [Azure’da Rezervasyonları Yönetme](billing-manage-reserved-vm-instance.md)
  - [Kullandıkça öde fiyatlarına tabi bir abonelik için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage.md)
  - [Kurumsal kaydınız için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage-ea.md)
