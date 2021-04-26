---
title: Azure Cosmos DB sağlanan aktarım hızı ve sunucusuz arasında seçim yapma
description: İş yükünüz için sağlanan aktarım hızı ve sunucusuz arasından seçim yapma hakkında bilgi edinin.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/08/2021
ms.openlocfilehash: 3f5c3400f319a3f9d5f1544457b009f90d479634
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98049839"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>Sağlanan aktarım hızı ve sunucusuz arasından seçim yapma
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB iki farklı kapasite modunda kullanılabilir: [sağlanan aktarım hızı](set-throughput.md) ve [sunucusuz](serverless.md). Her iki modda da tam olarak aynı veritabanı işlemlerini gerçekleştirebilirsiniz, ancak bu işlemler için faturalandırıldığınız şekilde tamamen farklıdır. Aşağıdaki videoda, bu modlar arasındaki temel farklılıklar ve farklı iş yükleri türlerine nasıl uydukları açıklanmaktadır:

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

## <a name="detailed-comparison"></a>Ayrıntılı karşılaştırma

| Ölçütler | Sağlanan aktarım hızı | Sunucusuz |
| --- | --- | --- |
| Durum | Genel kullanıma sunuldu | Önizlemede |
| En uygun | Öngörülebilir performans gerektiren sürekli trafiği olan iş yükleri | Aralıklı veya öngörülemeyen trafik ve düşük ortalama-yoğun trafik oranına sahip iş yükleri |
| Nasıl çalışır? | Kapsayıcılarınızın her biri için, saniye başına [Istek birimi](request-units.md) cinsinden ifade edilen bazı üretilen iş miktarı sağlayabilirsiniz. Her saniye, veritabanı işlemleriniz için bu Istek birimi miktarı kullanılabilir. Sağlanan üretilen iş, el ile veya otomatik [ölçeklendirme](provision-throughput-autoscale.md)ile ayarlanabilir. | Veritabanı işlemlerinizi herhangi bir kapasite sağlamak zorunda kalmadan kapsayıcılarınıza karşı çalıştırırsınız. |
| Coğrafi dağıtım | Kullanılabilir (sınırsız sayıda Azure bölgesi) | Kullanılamıyor (sunucusuz hesaplar yalnızca 1 Azure bölgesinde çalışabilir) |
| Kapsayıcı başına en fazla depolama alanı | Sınırsız | 50 GB |
| Performans | SLA tarafından kapsanan nokta okuma ve yazma işlemleri için 10 ms gecikme < | <, SLO tarafından kapsanan yazma işlemleri için nokta okuma ve < 30 MS için 10 ms gecikme süresi |
| Faturalama modeli | Faturalandırma, ne kadar RUs tüketildiğine bakılmaksızın, sağlanan RU/s için saat başına yapılır. | Faturalama, veritabanı işlemlerinizin kullandığı RUs miktarı için saatlik olarak gerçekleştirilir. |

> [!IMPORTANT]
> Sunucusuz kısıtlamaların bazıları sunucusuz genel kullanıma sunulduğunda kaldırılabilir veya kaldırılabilir.  Bize ulaşın ve sunucusuz deneyiminiz hakkında daha fazla bilgi verin: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

## <a name="estimating-your-expected-consumption"></a>Beklenen tüketiminizi tahmin etme

Bazı durumlarda, belirli bir iş yükü için sağlanan aktarım hızı veya sunucusuz seçilmelidir. Bu kararya yardımcı olmak için, genel olarak beklenen, bir ayda kullanabileceğiniz Toplam ru sayısı olan (Bu, [burada](plan-manage-costs.md#estimating-serverless-costs)gösterilen tablonun yardımıyla tahmin edebileceğiniz) Toplam tahmini **tüketiminizi** tahmin edebilirsiniz.

**Örnek 1**: bir iş yükünün en fazla 500 ru/sn 'ye patlaması ve bir ayda toplam 20.000.000 ru kullanması beklenir.

- Sağlanan aktarım hızı modunda, aylık 500 RU/sn ile bir kapsayıcı sağlayacaksınız: $0,008 * 5 * 730 = **$29,20**
- Sunucusuz modda tüketilen RUs için ödeme yaparsınız: $0,25 * 20 = **$5,00**

**Örnek 2**: bir iş yükünün en fazla 500 ru/sn 'ye patlaması ve bir ayda toplam 250.000.000 ru kullanması beklenir.

- Sağlanan aktarım hızı modunda, aylık 500 RU/sn ile bir kapsayıcı sağlayacaksınız: $0,008 * 5 * 730 = **$29,20**
- Sunucusuz modda tüketilen ru: $0,25 * 250 = **$62,50** için ödeme yaparsınız.

(Bu örnekler, iki mod ile aynı olan depolama maliyeti için hesap değildir)

> [!NOTE]
> Önceki örnekte gösterilen maliyetler yalnızca tanıtım amaçlıdır. En son fiyatlandırma bilgileri için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cosmos-db/) bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB üretilen iş sağlama](set-throughput.md) hakkında daha fazla bilgi edinin
- [Azure Cosmos DB sunucusuz](serverless.md) hakkında daha fazla bilgi edinin
- [Istek birimi](request-units.md) kavramıyla ilgili bilgi edinin
