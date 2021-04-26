---
title: Canlı akış geçiş kılavuzunu Media Services
description: Bu makale, Azure Media Services V2 'den v3 'e geçiş yaparken size en az yardımcı olacak canlı akış senaryosu tabanlı rehberlik sağlar.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: d021267c38f0043d2361b1a6392fbacd0634a164
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279706"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>Canlı akış senaryosu tabanlı geçiş kılavuzu

![Geçiş Kılavuzu logosu](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![geçiş adımları 2](./media/migration-guide/steps-4.svg)

Azure portal artık canlı olay ayarlama ve yönetimini desteklemektedir.  V2 'yi v3 geçişine sınarken bu uygulamayı denemeniz önerilir.

## <a name="test-the-v3-live-event-workflow"></a>V3 canlı etkinlik iş akışını test etme

> [!NOTE]
> V2 ile oluşturulan kanallar ve programlar (yani, canlı olaylar ve v3 'deki canlı çıktılar ile eşleştirilir) v3 API 'siyle yönetilir. Bu kılavuz, mevcut v2 kanalını durdurduğunuzda, uygun bir zamanda v3 canlı olaylara ve canlı çıkışlara geçiş yapmak. Şu anda sürekli olarak çalışan 7/24 canlı bir kanalı yeni v3 Live olaylarına sorunsuz bir şekilde geçirme desteği yoktur. Bu nedenle, bakım kapalı kalma süresinin hedef kitlesi ve işletmeniz için en iyi kolaylığa göre koordine olması gerekir.

İçeriğinizi v2 'den v3 'ye taşımadan önce Media Services canlı olaylar sunmanın yeni yolunu test edin. İşte çalışmak ve geçiş için göz önünde bulundurmanız gereken v3 özellikleri aşağıda verilmiştir.

- Kodlama için yeni bir v3 [Live olayı](live-event-outputs-concept.md#live-events) oluşturun. [1080p ve 720p kodlama ön ayarlarını](live-event-types-comparison-reference.md#system-presets)etkinleştirebilirsiniz.
- Programlar yerine [canlı çıkış](live-event-outputs-concept.md#live-outputs) varlığını kullanın
- [Akış Konumlandırıcı](stream-streaming-locators-concept.md)oluştur.
- [HLS ve Dash](encode-dynamic-packaging-concept.md) canlı akış ihtiyacını göz önünde bulundurun.
- Canlı olaylar için hızlı başlangıç yapmanız gerekiyorsa, yeni [Bekleme modu](live-event-outputs-concept.md#standby-mode) özelliklerini gözden geçirebilirsiniz.
- Canlı olaylarınızı çalışırken eklemek istiyorsanız, yeni [canlı](live-event-live-transcription-how-to.md) döküm özelliğini bulun.
- Daha uzun bir akış süresine ihtiyacınız varsa, v3 'de 24x7x365 canlı olay oluşturun.
- Canlı olaylarınızı izlemek için [Event Grid](monitoring/monitor-events-portal-how-to.md) kullanın.

Belirli adımlar için bkz. canlı etkinlik kavramları, öğreticiler ve aşağıdaki kılavuzlar.

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>Canlı olaylar kavramları, öğreticiler ve nasıl yapılır kılavuzlarından

### <a name="concepts"></a>Kavramlar

- [Azure Media Services v3 ile canlı akış](stream-live-streaming-concept.md)
- [Canlı etkinlikler ve canlı çıktılar Media Services](live-event-outputs-concept.md)
- [Doğrulanan şirket içi canlı akış kodlayıcıları](encode-recommended-on-premises-live-encoders.md)
- [İsteğe bağlı video oynatma oluşturmak için zaman değiştirme ve canlı çıktıları kullanma](live-event-cloud-dvr-time-how-to.md)
- [Live-event-Live-Transcription-nasıl yapılır (Önizleme)](live-event-live-transcription-how-to.md)
- [Canlı olay türleri karşılaştırması](live-event-types-comparison-reference.md)
- [Canlı olay durumları ve faturalandırma](live-event-states-billing-concept.md)
- [Canlı olay düşük gecikme süresi ayarları](live-event-latency-reference.md)
- [Media Services canlı olay hata kodları](live-event-error-codes-reference.md)

### <a name="tutorials-and-quickstarts"></a>Öğreticiler ve hızlı başlangıç

- [Öğretici: Media Services ile canlı akış](stream-live-tutorial-with-api.md)
- [OBS ile Azure Media Services canlı akış oluşturma](live-event-obs-quickstart.md)
- [Hızlı başlangıç: portala karşıya içerik yükleme, kodlama ve akışla](asset-create-asset-upload-portal-quickstart.md)
- [Hızlı başlangıç: kablolu atama ile Azure Media Services canlı akış oluşturma](live-event-wirecast-quickstart.md)

## <a name="samples"></a>Örnekler

Ayrıca [kod örneklerinde v2 ve v3 kodunu da karşılaştırabilirsiniz](migrate-v-2-v-3-migration-samples.md).
