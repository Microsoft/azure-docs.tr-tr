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
ms.openlocfilehash: b40f1833f08074cb0a8d45fe3afc6bac7cbac7f0
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73524054"
---
## <a name="create-an-anomaly-detector-resource"></a>Anomali algılayıcısı kaynağı oluşturma

1. [Azure Portal](https://portal.azure.com) oturum açın
1. [ **Anomali algılayıcı** kaynağı oluştur](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) ' a tıklayın
1. Tüm gerekli ayarları girin:

    |Ayar|Değer|
    |--|--|
    |Ad|İstenen ad (2-64 karakter)|
    |Abonelik|Uygun aboneliği seçin|
    |Konum|Yakındaki ve kullanılabilir herhangi bir konumu seçin|
    |Fiyatlandırma Katmanı|`F0`-minimum Fiyatlandırma Katmanı|
    |Kaynak Grubu|Kullanılabilir bir kaynak grubu seçin|
    |Önizleme onay kutusu (gerekli)|**Önizleme** bildiriminin okunup okunmayacağı|

1. **Oluştur** ' a tıklayın ve kaynağın oluşturulmasını bekleyin. Oluşturulduktan sonra kaynak sayfasına gidin
1. Yapılandırılmış `endpoint` ve bir API anahtarı toplayın:

    |Portalda kaynak sekmesi|Ayar|Değer|
    |--|--|--|
    |**Genel Bakış**|Uç Nokta|Uç noktayı kopyalayın. Şuna benzer `https://westus2.api.cognitive.microsoft.com/`|
    |**Belirlenmesine**|API anahtarı|İki anahtardan oluşan 1 ' i kopyalayın. Boşluk veya kesik çizgi olmayan 32 alfasayısal karakter dizesidir `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|



