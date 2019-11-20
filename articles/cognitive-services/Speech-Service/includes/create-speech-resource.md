---
title: Kapsayıcı desteği
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3c42bf2b2acc2472741bd603ea9d653a314ecc40
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73524096"
---
## <a name="create-a-speech-resource"></a>Konuşma kaynağı oluşturma

1. [Azure Portal](https://portal.azure.com) oturum açın
1. [ **Konuşma** kaynağı oluştur](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) ' a tıklayın
1. Tüm gerekli ayarları girin:

    |Ayar|Değer|
    |--|--|
    |Ad|İstenen ad (2-64 karakter)|
    |Abonelik|Uygun aboneliği seçin|
    |Konum|Yakındaki ve kullanılabilir herhangi bir konumu seçin|
    |Fiyatlandırma Katmanı|`F0`-minimum Fiyatlandırma Katmanı|
    |Kaynak Grubu|Kullanılabilir bir kaynak grubu seçin|

1. **Oluştur** ' a tıklayın ve kaynağın oluşturulmasını bekleyin. Oluşturulduktan sonra kaynak sayfasına gidin
1. Yapılandırılmış `endpoint` ve bir API anahtarı toplayın:

    |Portalda kaynak sekmesi|Ayar|Değer|
    |--|--|--|
    |**Genel Bakış**|Uç Nokta|Uç noktayı kopyalayın. Şuna benzer `https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**Belirlenmesine**|API anahtarı|İki anahtardan oluşan 1 ' i kopyalayın. Boşluk veya kesik çizgi olmayan 32 alfasayısal karakter dizesidir `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
