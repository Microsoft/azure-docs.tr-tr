---
title: Bilişsel hizmetler Metin Analizi kaynak oluşturma
titleSuffix: Azure Cognitive Services
description: Bilişsel hizmetler Metin Analizi kaynağı oluşturmayı öğrenin.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 6a9363472e1d5770232ca9a0b151fb309d9c4c75
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377438"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Bilişsel hizmetler Metin Analizi kaynak oluşturma

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. **Kaynak oluştur**' u seçin ve ardından **AI + Machine Learning** > **metin analizi**' a gidin.
   Ya da [metin analizi oluştur](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)' a gidin.
1. Gerekli tüm ayarları girin:

    |Ayar|Value|
    |--|--|
    |Ad|Bir ad girin (2-64 karakter).|
    |Subscription|Uygun aboneliği seçin.|
    |Location|Yakın bir konum seçin.|
    |Fiyatlandırma katmanı| Standart fiyatlandırma katmanı olan **S**'yi girin.|
    |Resource group|Kullanılabilir bir kaynak grubu seçin.|

1. **Oluştur**' u seçin ve kaynağın oluşturulmasını bekleyin. Tarayıcınız yeni oluşturulan kaynak sayfasına otomatik olarak yeniden yönlendirir.
1. Yapılandırılmış `endpoint` ve bir API anahtarını toplayın:

    |Portalda kaynak sekmesi|Ayar|Value|
    |--|--|--|
    |**Genel bakış**|Uç Nokta|Uç noktayı kopyalayın. Şuna benzer şekilde `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`görünür.|
    |**Belirlenmesine**|API Anahtarı|İki anahtardan birini kopyalayın. Boşluk veya tire içermeyen 32 karakterlik alfasayısal bir dizedir: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
