---
title: Uygulama eğitimi-LUSıS
titleSuffix: Azure Cognitive Services
description: Eğitim, doğal dil anlama geliştirmek için Language Understanding (LUIS) uygulama sürümü eğitiminde işlemidir. Ekleme, düzenleme, etiketleme veya varlıkları, amacı veya konuşma silme gibi bir Modeli'ne güncelleştirmelerinden sonra LUIS uygulamanızı eğitin.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1da8ab3015730c6b3e1962301a34b1ad43b1aad6
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143685"
---
# <a name="train-your-active-version-of-the-luis-app"></a>LUSıS uygulamasının etkin sürümünü eğitme 

Eğitim, doğal dil anlama geliştirmek için Language Understanding (LUIS) uygulamanızı eğitiminde işlemidir. Ekleme, düzenleme, etiketleme veya varlıkları, amacı veya konuşma silme gibi bir Modeli'ne güncelleştirmelerinden sonra LUIS uygulamanızı eğitin. 

Eğitim ve [test](luis-concept-test.md) uygulama yinelemeli bir işlemdir. LUIS uygulamanızı eğitme sonra varlıkları ve hedefleri doğru olarak tanınır olmadığını görmek için örnek Konuşma ile test edin. Değilseniz, güncelleştirmeleri LUIS uygulaması, eğitin ve test için yeniden yapın. 

Eğitim LUIS Portalı'nda etkin sürüme uygulanır. 

## <a name="how-to-train-interactively"></a>Etkileşimli olarak eğitme

İçinde bir süreçtir başlatmak için [LUIS portalı](https://www.luis.ai), ilk LUIS uygulamanızı en az bir kez eğitmek gerekir. Eğitim önce en az bir utterance her hedefi olduğundan emin olun.

1. Adını seçerek uygulamanıza erişmek **uygulamalarım** sayfası. 

1. Uygulamanızda seçin **eğitme** üst panelinde. 

1. Eğitim tamamlandığında tarayıcının en üstünde bir bildirim görüntülenir.

## <a name="training-date-and-time"></a>Eğitim tarihi ve saati

Eğitim tarihi ve saati GMT + 2 ' dir. 

## <a name="train-with-all-data"></a>Tüm verilerle eğitim

Eğitim negatif örnekleme küçük bir yüzdesine kullanır. Küçük negatif örnekleme yerine tüm verileri kullanmak istiyorsanız, [API](#version-settings-api-use-of-usealltrainingdata)'yi kullanın.

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Sürüm Ayarları API 'SI Usealltraıningdata kullanımı

Bu özelliği kapatmak için `UseAllTrainingData` true olarak ayarlanan [Sürüm Ayarları API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) 'sini kullanın. 

## <a name="unnecessary-training"></a>Gereksiz eğitim

Tek her değişiklikten sonra eğitme gerekmez. Eğitim, sonra bir grup değişiklikleri modele uygulanır ve test etmek veya yayımlamak için yapmanız gereken sonraki adım olan yapılmalıdır. Eğitim, test veya yayımlamak ihtiyacınız yoksa, gerekli değildir. 

## <a name="training-with-the-rest-apis"></a>REST API'leri ile eğitim

LUIS portalında eğitim tuşlarına basarak, tek bir adım olduğunu **eğitme** düğmesi. REST API'leri ile eğitim iki adımlı bir işlemdir. Birincisi [istek eğitim](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) HTTP POST ile. Daha sonra istek [eğitim durumu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) HTTP Get ile. 

Eğitim tamamlandığında öğrenmek için tüm modelleri başarıyla eğitilir kadar durum yoklaması gerekir. 

## <a name="next-steps"></a>Sonraki adımlar

* [Etkileşimli test](luis-interactive-test.md)
* [Toplu işlem testi](luis-how-to-batch-test.md)
