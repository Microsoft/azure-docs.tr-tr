---
title: Azure Percept ses düğmesi ve LED davranışı
description: Azure Percept Audio düğme ve LED durumları hakkında daha fazla bilgi edinin
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: fa919acb527084d19ab88b2e7895d4e6ab0b72d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609080"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Azure Percept ses düğmesi ve LED davranışı

Azure Percept ses cihazının düğme ve LED durumları hakkında bilgi edinmek için aşağıdaki kılavuza bakın.

## <a name="button-behavior"></a>Düğme davranışı

Aygıtın davranışını denetlemek için düğmeleri kullanın.

|Düğme durumu|Davranış|
|------------|----------|
|Mikrofon|MIC dizisinin sesini kapatmak/açmak için tuşuna basın. Düğme olayı, basıldığında tetiklenir.|
|PTT/PTS|Anahtar sözcük biriktirme durumunu atlayıp komut dinleme durumunu etkinleştirmek için PTT tuşuna basın. Aracının etkin iletişim kutusunu durdurmak için bir kez daha basın ve anahtar sözcük biriktirme durumuna dönün. Düğme olayı, basıldığında tetiklenir. PTS, aracı dinleme yaparken veya düşünirken değil, yalnızca aracı konuşurken düğme basıldığında işe yarar.|

## <a name="led-behavior"></a>LED davranışı

Cihazın hangi durumda olduğunu anlamak için, LED göstergelerini kullanın.

|GELIŞTIRMESINE|LED durumu|Ear SoM durumu|
|---|------------|----------------|
|L02|1x beyaz, statik açık|Açma |
|L02|1x beyaz, 0,5 Hz yanıp sönen|Kimlik doğrulama devam ediyor |
|L01 & L02 & L03|3x mavisi, statik|Anahtar sözcük bekleniyor|
|L01 & L02 & L03|LED dizisi yanıp sönme, 20 fps |Dinleme veya konuşuyor|
|L01 & L02 & L03|LED dizisi yarış, 20 fps|Düğün|
|L01 & L02 & L03|3x kırmızı, statik |Mikrofon|

## <a name="next-steps"></a>Sonraki adımlar

Azure Percept ses cihazınız için sorun giderme ipuçları için bu [kılavuza](./troubleshoot-audio-accessory-speech-module.md)bakın.