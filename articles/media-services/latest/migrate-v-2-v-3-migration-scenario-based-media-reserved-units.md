---
title: Medya ayrılmış birimleri (MRUs) geçiş kılavuzu
description: Bu makalede, Azure Media Services V2 'den v3 'e geçiş yaparken size yardımcı olacak MRU senaryo tabanlı rehberlik sunulmaktadır.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 00e1fa623fc625a568f6580998443758c3916be8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563081"
---
# <a name="media-reserved-units-mrus-scenario-based-migration-guidance"></a>Medya ayrılmış birimleri (MRUs) senaryo tabanlı geçiş kılavuzu

![Geçiş Kılavuzu logosu](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![geçiş adımları 2](./media/migration-guide/steps-4.svg)

Bu makalede, Azure Media Services V2 'den v3 'e geçiş yaparken size yardımcı olacak MRU senaryo tabanlı rehberlik sunulmaktadır.

- Azure portal veya v3 API 'sinin 2020-05-01 sürümüyle oluşturulan yeni v3 hesapları için artık medya ayrılmış birimleri 'ni (MRU) ayarlamanız gerekmez. Sistem artık yüklemeye göre otomatik olarak yukarı ve aşağı ölçeklenecektir.
- API 'nin 2020-05-01 sürümünden önce oluşturulmuş bir v3 veya v2 hesabınız varsa, medya ayrılmış birimleri 'ni kullanarak işlerinizin eşzamanlılık ve performansını denetlemeye devam edebilirsiniz. Daha fazla bilgi için bkz. Medya İşlemeyi Ölçeklendirme. MRUs 'yi Media Services v3 için CLı 2,0 kullanarak veya Azure portal kullanarak yönetebilirsiniz.  
- Azure portal MRU 'yi yönetme seçeneğini görmüyorsanız, 2020-05-01 API 'SI veya sonraki bir sürümü ile oluşturulmuş bir hesabı çalıştırıyor olursunuz.
- MRU türünü S3 olarak ayarlamayı biliyorsanız, performansınız aynı şekilde geliştirecek veya aynı kalır.
- Mevcut bir v2 müşterisiyseniz, geçiş tamamlanmadan önce mevcut uygulamanızı desteklemek için yeni bir v2 hesabı oluşturmanız gerekir. 
- Henüz kullanım dışı bırakılmayan Dizin Oluşturucu v1 veya diğer medya işlemcilerin yeniden etkinleştirilmesi gerekebilir. 

MRUs hakkında daha fazla bilgi için bkz. [medya ayrılmış birimleri](concept-media-reserved-units.md) ve [medya ayrılmış birimlerinin ölçeklendirilmesi](media-reserved-units-cli-how-to.md).

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>MRU kavramları, öğreticiler ve nasıl yapılır kılavuzlarından

### <a name="concepts"></a>Kavramlar

[Medya ayrılmış birimleri](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>Nasıl yapılır kılavuzları

[Medya ayrılmış birimlerini ölçeklendirme](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>Örnekler

Ayrıca [kod örneklerinde v2 ve v3 kodunu da karşılaştırabilirsiniz](migrate-v-2-v-3-migration-samples.md).
