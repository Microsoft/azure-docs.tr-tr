---
title: 'Hızlı başlangıç: konuşmayı, amaçları ve varlıkları tanıma, Python-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 4efa535118d075addf78b2e9be6a645c458d6bb4
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125634"
---
## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları yaptığınızdan emin olun:

> [!div class="checklist"]
>
> * [Azure konuşma kaynağı oluşturma](../../../../get-started.md)
> * [Bir LUSıS uygulaması oluşturma ve bir uç nokta anahtarı edinme](../../../../quickstarts/create-luis.md)
> * [Geliştirme ortamınızı kurma](../../../../quickstarts/setup-platform.md)
> * [Boş bir örnek proje oluşturun](../../../../quickstarts/create-project.md)

## <a name="open-your-project"></a>Projenizi açın

Python Düzenleyicinizde Quickstart.py öğesini açın.

## <a name="start-with-some-boilerplate-code"></a>Bazı demirbaş kodla başlayın

Projemiz için bir çatı olarak çalışacak bir kod ekleyelim.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Konuşma yapılandırması oluşturma

Bir `IntentRecognizer` nesnesi başlatabilmeniz için, LUSıS uç noktası anahtarınızı ve bölgenizi kullanan bir yapılandırma oluşturmanız gerekir. Daha sonra bu kodu ekleyin.

Bu örnek, LUSıS anahtar ve bölgesini kullanarak `SpeechConfig` nesnesini oluşturur. Kullanılabilir yöntemlerin tam listesi için bkz. [SpeechConfig Class](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

> [!NOTE]
> Yalnızca uç nokta anahtarı konuşma amacı tanıma için geçerli olduğundan, HALME bitiş noktası anahtarını değil, başlangıç veya yazma anahtarlarını kullanmak önemlidir. Doğru anahtar alma hakkında yönergeler için bkz. [lusıs uygulaması oluşturma ve bir uç nokta anahtarı alma](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) .

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

## <a name="initialize-an-intentrecognizer"></a>Bir Yoğunlumtanıyıcıyı başlatma

Şimdi bir `IntentRecognizer`oluşturalım. Konuşma yapılandırmanızın hemen altına bu kodu ekleyin.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel ve amaçlar ekleyin

Artık bir `LanguageUnderstandingModel` amaç tanıyıcı ile ilişkilendirmeniz ve tanınan hedefleri eklemeniz gerekir.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Amacı tanıma

`IntentRecognizer` nesnesinden `recognize_once()` yöntemini çağıracağız. Bu yöntem, konuşma hizmetinin tanıma için tek bir tümcecik gönderdiğini ve bu ifadenin konuşmayı tanımayı durdur olarak belirlenmesinin ardından olduğunu bilmesini sağlar.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Tanıma sonuçlarını (veya hatalarını) görüntüleme

Tanınma sonucu konuşma hizmeti tarafından döndürüldüğünde, onunla ilgili bir şey yapmak isteyeceksiniz. Bu uygulamayı basit tutmaya ve sonucu konsola yazdıracağız.

Using ifadesinin içinde, `recognize_once()`çağrın altında, şu kodu ekleyin: [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Kodunuzu denetleyin

Bu noktada, kodunuzun şöyle görünmesi gerekir:  
(Bu sürüme bazı açıklamalar ekledik) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Uygulamanızı derleyin ve çalıştırın

Örneği konsolundan veya IDE 'ınızdan çalıştırın:

    ````
    python quickstart.py
    ````

Mikrofonunuzdan yapılan sonraki 15 saniyelik konuşma girişi tanınır ve konsol penceresinde günlüğe kaydedilir.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [footer](./footer.md)]
