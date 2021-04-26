---
title: Media Services V2 'den v3 tanıtımı 'na geçirme
description: Bu makale, Media Services V2 'den v3 'e geçirmeye giriş niteliğindedir.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 32e502ff175a9222faa0eb79fb53f2cc3f76bfb7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559749"
---
# <a name="migrate-from-media-services-v2-to-v3-introduction"></a>Media Services V2 'den v3 tanıtımı 'na geçirme

![Geçiş Kılavuzu logosu](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

Media Services Geçiş Kılavuzu, artık kullanılabilir olan yeni özellik ve işlevlerden faydalanan bir geçişe bağlı olarak Media Services V2 API 'Lerinden v3 API 'Lerine geçiş yapmanıza yardımcı olur. En iyi kararlarınızı kullanmalı ve senaryonuza en uygun olanı belirlemelisiniz.

## <a name="how-to-use-this-guide"></a>Bu kılavuz nasıl kullanılır?

### <a name="navigating"></a>İçinde

Kılavuzun tamamında aşağıdaki grafiği görürsünüz.

![geçiş adımları](./media/migration-guide/steps.svg)<br/>

Kullandığınız adım, adımın sayısında bir renk değişikliği ile belirtilir, örneğin:

![geçiş adımları 2](./media/migration-guide/steps-2.svg)<br/>

Her sayfanın sonunda, **sonraki adımlar** başlığının altında okuyabilmeniz için geçiş belgelerinin geri kalanı bağlantılarını görürsünüz.

### <a name="guidance"></a>Rehber

Burada sunulan kılavuzluk *genel* bir uygulamadır. Bu, artık v3 'de kullanılabilir olan ve Media Services iş akışlarında nelerin değiştiği hakkında daha fazla farkında olacak içerik içerir.

Ekran görüntüleri ve kavramsal grafikler de dahil olmak üzere daha ayrıntılı rehberlik için, her senaryo tabanlı konu başlığında kavramlar, Öğreticiler, hızlı başlangıçlar, örnekler ve API başvuruları bağlantıları vardır. Ayrıca, v2 API 'sini v3 API 'siyle karşılaştırmanıza yardımcı olan örnekler de ekledik.

## <a name="migration-guidance-overview"></a>Geçiş kılavuzlarına genel bakış

Geçişiniz sırasında izlemeniz gereken dört genel adım vardır:

## <a name="step-1-benefits"></a>1. adım avantajları

<hr color="#5ea0ef" size="10">

Media Services API v3 sürümüne geçiş [avantajlarının avantajlarını anlayın](migrate-v-2-v-3-migration-benefits.md) .

## <a name="step-2-differences"></a>2. adım farklar

<hr color="#5ea0ef" size="10">

Media Services V2 API 'SI ile v3 API arasındaki farkları anlayın.

- [API erişimi](migrate-v-2-v-3-differences-api-access.md)
- [Özellik boşlukları](migrate-v-2-v-3-differences-feature-gaps.md)
- [Terminoloji ve varlık değişiklikleri](migrate-v-2-v-3-differences-terminology.md)

## <a name="step-3-sdk-setup"></a>Adım 3 SDK kurulumu

<hr color="#5ea0ef" size="10">

SDK farklılıklarını anlayın ve [V3 REST API veya istemci SDK 'sına geçirilecek şekilde ayarlayın](migrate-v-2-v-3-migration-setup.md).

## <a name="step-4-scenario-based-guidance"></a>4. adım senaryo tabanlı yönergeler

<hr color="#5ea0ef" size="10">

Media Services V2 uygulamanız benzersiz olabilir. Bu nedenle, geçmişte medya Hizmetleri 'ni nasıl kullandığımıza ve hizmetin  her bir özelliğine ilişkin adımlara göre senaryo tabanlı yönergeler sunuyoruz:

- [Kodlama](migrate-v-2-v-3-migration-scenario-based-encoding.md)
- [Canlı akış](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)
- [Paketleme ve teslim](migrate-v-2-v-3-migration-scenario-based-publishing.md)
- [İçerik koruma](migrate-v-2-v-3-migration-scenario-based-content-protection.md)
- [Medya ayrılmış birimleri (MRU)](migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md)
