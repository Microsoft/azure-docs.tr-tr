---
title: Bing Konuşma tanıma modunu seçme | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Bing Konuşma ' de en iyi tanıma modunu seçme.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1860acb571c837a5eb4c75be69a96d1b22682118
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965686"
---
# <a name="bing-speech-recognition-modes"></a>Bing Konuşma Tanıma modları

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing Konuşmayı Metne Dönüştürme API 'Leri birçok konuşma tanıma modunu destekler. Uygulamanız için en iyi tanıma sonuçlarını üreten modu seçin.

| Mod | Açıklama |
|---|---|
| *etkileşimli* | Etkileşimli kullanıcı uygulaması senaryoları için "komut ve denetim" tanıma. Kullanıcılar bir uygulamaya komut olarak tasarlanan kısa tümceleri konuştur. |
| *etme* | Dikte senaryoları için sürekli tanıma. Kullanıcılar metin olarak görüntülenen daha uzun cümleler konuşabilir. Kullanıcılar daha resmi bir konuşma stili benimseyin. |
| *görül* | İnsanların arasındaki konuşma konuşmaları için sürekli tanıma. Kullanıcılar daha az resmi bir konuşma stili benimseir ve daha uzun cümleler ve daha kısa ifadeler arasında değişebilir.

> [!NOTE]
> [REST API 'lerini](../GetStarted/GetStartedREST.md)kullandığınızda bu modlar geçerlidir. [İstemci kitaplıkları](../GetStarted/GetStartedClientLibraries.md) tanıma modunu belirtmek için farklı parametreler kullanır. Daha fazla bilgi için, seçtiğiniz istemci kitaplığına bakın.

Daha fazla bilgi için bkz. [Tanıma modları](../concepts.md#recognition-modes) sayfası.
