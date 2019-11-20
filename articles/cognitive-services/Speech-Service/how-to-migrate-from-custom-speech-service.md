---
title: Özel Konuşma Tanıma hizmetinden konuşma hizmetine geçiş
titleSuffix: Azure Cognitive Services
description: Özel konuşma hizmeti artık konuşma hizmeti bir parçasıdır. En son kalite ve özellik güncelleştirmeleri yararlanmak için konuşma hizmeti geçin.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 01b853c59723a8ed79cb32b0ee9c245c9c3ffb3f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562757"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Konuşma hizmeti için özel konuşma hizmeti geçirme

Uygulamalarınızı konuşma hizmeti için özel konuşma tanıma hizmetinden geçirmek için bu makaleyi kullanın.

Özel konuşma hizmeti artık konuşma hizmeti bir parçasıdır. En son kalite ve özellik güncelleştirmelerinden faydalanmak için konuşma hizmetlerine geçin.

## <a name="migration-for-new-customers"></a>Yeni müşteriler için geçiş

Fiyatlandırma modeli konuşma hizmeti için bir saat dayalı bir fiyatlandırma modeli kullanarak basittir.  

1. Uygulamanızın kullanılabilir olduğu her bölgede bir Azure kaynağı oluşturun. Azure kaynak adı **konuşma**. Tek bir Azure kaynağı için ayrı kaynaklar oluşturmak yerine aynı bölgede aşağıdaki hizmetleri kullanabilirsiniz:

    * Konuşmayı Metne Dönüştürme
    * Özel Konuşmayı metne dönüştürme
    * Metin okuma
    * Konuşma çevirisi

2. İndirme [konuşma SDK](speech-sdk.md).

3. Hızlı Başlangıç kılavuzları ve SDK'sı örnekleri doğru API'lerini kullanmayı izleyin. REST API'lerini kullanmanız durumunda Ayrıca kaynak anahtarları ve doğru Uç noktalara kullanmanız gerekir.

4. Konuşma hizmetlerini ve API 'Leri kullanmak için istemci uygulamasını güncelleştirin.

## <a name="migration-for-existing-customers"></a>Mevcut müşteriler için geçiş

Mevcut kaynak anahtarlarınızı konuşma Hizmetleri portalındaki konuşma hizmetlerine geçirin. Aşağıdaki adımları kullanın:

> [!NOTE]
> Kaynak anahtarları yalnızca aynı bölge içinde geçirilebilir.

1. Oturum [cris.ai](https://cris.ai/Home/CustomSpeech) Portalı ' nı seçip sağ üst menüdeki abonelikte.

2. Seçin **seçili abonelik geçişi**.

3. Metin kutusuna abonelik anahtarını girin ve seçin **geçirme**.

## <a name="next-steps"></a>Sonraki adımlar

* [Konuşma hizmetlerini ücretsiz olarak deneyin](get-started.md).
* Bilgi [Konuşmayı metne dönüştürme](./speech-to-text.md) kavramları.

## <a name="see-also"></a>Ayrıca bkz.

* [Konuşma hizmeti nedir](overview.md)
* [Konuşma Hizmetleri ve konuşma SDK belgeleri](speech-sdk.md#get-the-sdk)
