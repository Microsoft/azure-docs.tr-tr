---
title: Canlı olay durumları ve faturalama Azure Media Services
description: Bu konu, canlı olay durumları ve faturalandırma Azure Media Services bir genel bakış sunar.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: f17c2fcd5793412716c72f02c7cfbe670360fa33
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123466"
---
# <a name="live-event-states-and-billing"></a>Canlı olay durumları ve faturalandırma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services, canlı bir olay, **çalışma** veya **bekleme** durumuna geçiş yaptığında faturalandırmaya başlar. Hizmetten herhangi bir video akışı olmasa bile faturalandırılırsınız. Canlı etkinliği faturalandırmayı durdurmak için canlı olayı durdurmanız gerekir. Canlı döküm, canlı olayla aynı şekilde faturalandırılır.

[Canlı olayınızdaki](/rest/api/media/liveevents) **Liveeventencodingtype** standart veya Premium1080p olarak ayarlandığında Media Services, giriş akışı kaybolduktan sonra çalışmakta olan 12 saat içinde hala **çalışır** durumda olan canlı olayları otomatik olarak kapatır ve çalışan **canlı bir çıktı** yoktur. Ancak, hala canlı olayın **çalışır** durumda olduğu zaman faturalandırılırsınız.

> [!NOTE]
> Doğrudan geçiş canlı olayları otomatik olarak kapatılmaz ve aşırı faturalandırmayı önlemek için API aracılığıyla açıkça durdurulmalıdır.

## <a name="states"></a>Durumlar

Canlı olay, aşağıdaki durumlardan birinde olabilir.

|Durum|Açıklama|
|---|---|
|**Durduruldu**| Bu, oluşturulduktan sonra canlı etkinliğin ilk durumudur (autostart, true olarak ayarlanmadığı müddetçe).) Bu durumda faturalandırma yapılmaz. Canlı olay tarafından hiçbir giriş alınamaz. |
|**Başlatılıyor**| Canlı olay başlar ve kaynaklar ayrıldıktan sonra. Bu durumda faturalandırma yapılmaz.  Bir hata oluşursa canlı olay durdurulmuş duruma geri döner.|
| **Ayırma** | Ayırma eylemi canlı olayda çağrıldı ve bu canlı etkinlik için kaynaklar sağlanıyor. Bu işlem başarılı bir şekilde tamamlandıktan sonra, canlı olay bekleme durumuna geçer.
|**Modunda**| canlı olay kaynakları sağlandı ve başlamaya hazır. Faturalandırma bu durumda gerçekleşir.  Çoğu özellik yine de güncelleştirilemeyebilir, ancak bu durum sırasında alma veya akışa izin verilmez.
|**Çalışma**| Canlı olay kaynakları ayrıldı, alma ve önizleme URL 'Leri oluşturulmuştur ve canlı akışlar alabilir. Bu noktada faturalandırma etkin olur. Daha fazla faturalandırmayı durdurmak için canlı olay kaynağında durdurmayı açıkça çağırmanız gerekir.|
|**Durduruluyor**| Canlı etkinlik durduruluyor ve kaynaklar zaten sağlanıyor. Bu geçici durumda faturalandırma yapılmaz. |
|**Siliniyor**| Canlı etkinlik siliniyor. Bu geçici durumda faturalandırma yapılmaz. |

Canlı olayları oluştururken canlı onayları etkinleştirmeyi seçebilirsiniz. Bunu yaparsanız, canlı olay **çalışır** durumda olduğunda canlı döküm için faturalandırılırsınız. Canlı olay aracılığıyla bir ses akışı olmasa bile faturalandırılacağını unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

- [Canlı akışa genel bakış](stream-live-streaming-concept.md)
- [Canlı akış öğreticisi](stream-live-tutorial-with-api.md)
