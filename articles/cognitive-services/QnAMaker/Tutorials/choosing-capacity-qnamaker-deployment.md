---
title: Dağıtım için kaynak kapasitesi-Soru-Cevap Oluşturma
titleSuffix: Azure Cognitive Services
description: Soru-Cevap Oluşturma hizmetinizi oluşturmadan önce, Yukarıdaki hizmetlerin hangi katmanının sizin için uygun olduğuna karar vermeniz gerekir.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 5cbdb6fcf9fcdf12b54ff1db4b577bb975517131
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73793955"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Soru-Cevap Oluşturma dağıtımınız için kapasite seçme

Soru-Cevap Oluşturma Hizmeti, üç Azure kaynağına bir bağımlılık alır:
1.  App Service (çalışma zamanı için)
2.  Azure Bilişsel Arama (QnAs depolamak ve aramak için)
3.  Uygulama öngörüleri (sohbet günlüklerini ve telemetrisini depolamak için isteğe bağlı)

Soru-Cevap Oluşturma hizmetinizi oluşturmadan önce, Yukarıdaki hizmetlerin hangi katmanının sizin için uygun olduğuna karar vermeniz gerekir. 

Genellikle dikkate almanız gereken üç parametresi vardır:

1. **Hizmetten ihtiyacınız olan aktarım hızı**: gereksinimlerinize göre App Service Için uygun [uygulama planını](https://azure.microsoft.com/pricing/details/app-service/plans/) seçin. Uygulamanın [ölçeğini](https://docs.microsoft.com/azure/app-service/manage-scale-up) değiştirebilir veya azaltabilirsiniz. Bunun yanı sıra Azure Bilişsel Arama SKU seçiminizi de etkilemelidir, daha fazla ayrıntı için [buradaki](https://docs.microsoft.com/azure/search/search-sku-tier)ayrıntıları inceleyin.

1. **Bilgi bankalarının boyutu ve sayısı**: senaryonuz Için uygun [Azure Search SKU](https://azure.microsoft.com/pricing/details/search/) 'sunu seçin. N-1 bilgi bankasını belirli bir katmanda yayımlayabilirsiniz; burada N, katmanda izin verilen en fazla dizin olabilir. Ayrıca, katman başına izin verilen maksimum boyut ve belge sayısını kontrol edin.

    Örneğin, katmanınızda 15 ' in izin verilen dizini varsa, 14 bilgi tabanı (yayımlanan Bilgi Bankası başına 1 Dizin) yayımlayabilirsiniz. On beşinci Dizin, yazma ve test için tüm bilgi tabanları için kullanılır. 

1. **Kaynak olarak belge sayısı**: soru-cevap oluşturma yönetim hizmetinin ücretsiz SKU 'su, Portal ve API 'ler aracılığıyla (her bırı 1 MB boyutunda) yönetebileceğiniz belge sayısını sınırlar. Standart SKU, yönetebileceğiniz belge sayısıyla sınırlı değildir. Daha fazla ayrıntı için [buraya](https://aka.ms/qnamaker-pricing)bakın.

Aşağıdaki tabloda bazı üst düzey yönergeler sunulmaktadır.

|                        | Soru-Cevap Oluşturma yönetimi | App Service | Azure Bilişsel Arama | Sınırlamalar                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Deneme        | Ücretsiz SKU             | Ücretsiz Katmanı   | Ücretsiz Katmanı    | 2 KBs 'e kadar yayımlayın, 50 MB boyutunda  |
| Geliştirme ve test ortamı   | Standart SKU         | Paylaşımlı      | Temel        | En fazla 14 KBs, 2 GB boyut yayımlayın    |
| Üretim ortamı | Standart SKU         | Temel       | Standart     | 49 KBs 'e kadar yayımlayın, 25 GB boyut |

Soru-Cevap Oluşturma yığınınızı yükseltmek için bkz. [soru-cevap oluşturma hizmetinizi yükseltme](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Soru-Cevap Oluşturma hizmetinizi yükseltin](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)
