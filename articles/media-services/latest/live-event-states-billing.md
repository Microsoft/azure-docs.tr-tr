---
title: Canlı olay durumları ve faturalama Azure Media Services | Microsoft Docs
description: Bu konu, Azure Media Services LiveEvent durumları ve faturalandırma hakkında genel bakış sunar.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 10/24/2019
ms.author: juliako
ms.openlocfilehash: af3d4b51dadfaa99a166ca0ce475c5a110d8f6e8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933675"
---
# <a name="live-event-states-and-billing"></a>Canlı olay durumları ve faturalandırma

Azure Media Services, canlı bir olay, durum geçişleri **çalışmaya**hemen sonra faturalandırmaya başlar. Canlı etkinliği faturalandırmayı durdurmak için canlı olayı durdurmanız gerekir.

[Canlı olayınızdaki](https://docs.microsoft.com/rest/api/media/liveevents) **Liveeventencodingtype** standart veya Premium1080p olarak ayarlandığında Media Services, giriş akışı kaybolduktan sonra **çalışmakta** olan 12 saat içinde devam eden canlı olayları otomatik olarak kapatır ve **canlı çıkış** yok demektir çalışıyor. Ancak, hala canlı olayın **çalışır** durumda olduğu zaman faturalandırılırsınız.

> [!NOTE]
> Doğrudan geçiş canlı olayları otomatik olarak kapatılmaz ve aşırı faturalandırmayı önlemek için API aracılığıyla açıkça durdurulmalıdır. 

## <a name="states"></a>Durumlar

Canlı olay, aşağıdaki durumlardan birinde olabilir.

|Eyalet|Açıklama|
|---|---|
|**Durdurulacağını**| Bu, oluşturulduktan sonra canlı etkinliğin ilk durumudur (autostart, true olarak ayarlanmadığı müddetçe).) Bu durumda faturalandırma yapılmaz. Bu durumda, Canlı Etkinlik özellikleri güncelleştirilebilir ama akışa izin verilmez.|
|**Şunlar**| Canlı etkinlik başlatılıyor ve kaynaklar ayrıldı. Bu durumda faturalandırma yapılmaz. Bu durum sırasında güncelleştirmelere veya akışa izin verilmez. Bir hata oluşursa canlı olay durdurulmuş duruma geri döner.|
|**Çalıştıran**| Canlı olay kaynakları ayrıldı, alma ve önizleme URL 'Leri oluşturulmuştur ve canlı akışlar alabilir. Bu noktada faturalandırma etkin olur. Daha fazla faturalandırmayı durdurmak için canlı olay kaynağında durdurmayı açıkça çağırmanız gerekir.|
|**Durdurulamadı**| Canlı etkinlik durduruluyor ve kaynaklar zaten sağlanıyor. Bu geçici durumda faturalandırma yapılmaz. Bu durum sırasında güncelleştirmelere veya akışa izin verilmez.|
|**Silinmesinden**| Canlı Etkinlik siliniyor. Bu geçici durumda faturalandırma yapılmaz. Bu durum sırasında güncelleştirmelere veya akışa izin verilmez.|

## <a name="next-steps"></a>Sonraki adımlar

- [Canlı akışa genel bakış](live-streaming-overview.md)
- [Canlı akış öğreticisi](stream-live-tutorial-with-api.md)
