---
title: Conversation Learner modeliyle varlık algılama geri çağırma kullanma-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner modeliyle varlık algılama geri aramasını nasıl kullanacağınızı öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 3f6094da7b103f1c99e9bb3fa9313354fcb2a8bc
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703981"
---
# <a name="how-to-use-entity-detection-callback"></a>Varlık algılama geri çağırma kullanma

Bu öğretici varlık algılama geri aramasını gösterir ve varlıkları çözümlemek için ortak bir model gösterir.

## <a name="video"></a>Video

[![Varlık algılama geri çağırma öğreticinin önizlemesi](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>Gereksinimler
Bu öğreticide, `tutorialEntityDetectionCallback` bot 'ın çalışıyor olması gerekir.

    npm run tutorial-entity-detection

## <a name="details"></a>Ayrıntılar
Varlık algılama geri çağırmaları, kod aracılığıyla varlık tanıma davranışının ve varlık değiştirmenin değiştirilmesini sağlar. Bu işlevselliği, tipik bir varlık algılama geri çağırma tasarım düzeniyle yürüyerek göstereceğiz. Örneğin, "büyük elma" i "New York" olarak çözümleniyor.

## <a name="steps"></a>Adımlar

### <a name="create-the-model"></a>Model oluşturma

1. Web Kullanıcı arabiriminde "yeni model" e tıklayın.
2. "Ad" alanına "EntityDetectionCallback" yazın ve ENTER tuşuna basın.
3. "Oluştur" düğmesine tıklayın.

Bu örnekte üç varlık gereklidir, bu yüzden üç varlık oluşturalım.

### <a name="create-the-custom-trained-entity"></a>Özel eğitilen varlığı oluşturma

1. Sol bölmede, "varlıklar" ve ardından "yeni varlık" düğmesine tıklayın.
2. "Varlık türü" için "özel eğitimli" seçeneğini belirleyin.
3. "Varlık adı" için "şehir" yazın.
4. "Oluştur" düğmesine tıklayın.

### <a name="create-the-first-programmatic-entity"></a>İlk programlı varlığı oluşturma

1. "Yeni varlık" düğmesine tıklayın.
2. "Varlık türü" için "programsal" seçeneğini belirleyin.
3. "Varlık adı" için "CityUnknown" yazın.
4. "Oluştur" düğmesine tıklayın.

### <a name="create-the-first-programmatic-entity"></a>İlk programlı varlığı oluşturma

1. "Yeni varlık" düğmesine tıklayın.
2. "Varlık türü" için "programsal" seçeneğini belirleyin.
3. "Varlık adı" için "CityResolved" yazın.
4. "Oluştur" düğmesine tıklayın.

Şimdi üç eylem oluşturun.

### <a name="action-creation"></a>Eylem oluşturma

1. Sol bölmede, "eylemler" e ve ardından "yeni eylem" düğmesine tıklayın.
2. "Bot 'un yanıtı..." alanına "hangi şehir istiyorsunuz?" yazın.
3. "Kullanıcı yanıtında beklenen varlık..." alanına "şehir" yazın.
4. "Niteleyen varlıklar" alanına "şehir" yazın.
5. "Oluştur" düğmesine tıklayın.
6. "Yeni eylem" düğmesine tıklayın.
7. "Bot 'un yanıtı..." alanına "hangi şehir istiyorsunuz?" yazın.
8. "Kullanıcı yanıtında beklenen varlık..." alanına "Bu şehri bilmiyorum" yazın.
9. "Oluştur" düğmesine tıklayın.
10. "Yeni eylem" düğmesine tıklayın.
11. "Bot 'un yanıtı..." alanı "$CityResolved çok güzel" yazın.
12. "Oluştur" düğmesine tıklayın.

Geri çağırma kodu aşağıda verilmiştir:

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>Modeli eğitme

1. Sol bölmede "Iletişim kutularını eğitme" düğmesine ve ardından "yeni eğitme Iletişim kutusu" düğmesine tıklayın.
2. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Merhaba" yazın
3. "Eylemleri puan" düğmesine tıklayın.
4. "Hangi şehri istiyorsunuz?" yanıtını seçin
5. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "büyük Apple" yazın
6. "Eylemleri puan" düğmesine tıklayın.
    - Düğmeye tıkladığınızda varlık algılama geri araması tetiklenir
    - Geri çağırma kodu, CityResolved varlık değerini "New York" olarak ayarlar
7. "New York çok güzel" yanıtını seçin.

Bu model birçok bot senaryosunun tipik bir noktadır. Kullanıcı ve ayıklanan varlıklar iş mantığınızı sağlar ve bu mantık, iletişim kutusunun sonraki dönüşi için, daha sonra programlı varlıklara kaydedilir.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Oturum geri çağırmaları](./13-session-callbacks.md)
