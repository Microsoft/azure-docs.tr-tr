---
title: 'Hızlı başlangıç: Sentezleştirme konuşma, Swift-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: Konuşma SDK 'sını kullanarak macOS 'ta bir konuşmayı nasıl sentezleyeceğinizi öğrenin
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 727929efb499f32dcd42a964b6b6c8e762803590
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503468"
---
# <a name="quickstart-synthesize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Hızlı başlangıç: konuşma SDK 'sını kullanarak macOS 'ta Swift 'ta konuşmayı Sentezleştirme

Bu makalede, bilişsel hizmetler konuşma SDK 'sını kullanarak Swift 'ta bir macOS uygulamasının nasıl oluşturulduğunu öğrenirsiniz ve bu da konuşmayı metinden bulup varsayılan ses çıkışıyla oynatabilir.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, önkoşulların bir listesi aşağıda verilmiştir:

* Konuşma hizmeti için bir [abonelik anahtarı](~/articles/cognitive-services/Speech-Service/get-started.md) .
* [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) veya üzeri ve [Cocoapods](https://cocoapods.org/) yüklü bir MacOS makinesi.

## <a name="get-the-speech-sdk-for-macos"></a>MacOS için konuşma SDK 'sını alın

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

MacOS için bilişsel hizmetler konuşma SDK 'Sı bir çerçeve paketi olarak dağıtılır.
Xcode projelerinde bir [CocoaPod](https://cocoapods.org/)olarak veya https://aka.ms/csspeech/macosbinary indirilen ve el ile bağlanmış bir şekilde kullanılabilir. Bu kılavuzda bir CocoaPod kullanılır.

> [!NOTE] 
> Bu öğretici, 1.7.0 ' den önceki SDK sürümleriyle çalışmaz.

## <a name="create-an-xcode-project"></a>Xcode projesi oluşturma

Xcode’u başlatın ve **Dosya** > **Yeni** > **Proje** seçeneklerine tıklayarak yeni bir proje başlatın.
Şablon Seçimi iletişim kutusunda, "Cocoa uygulaması" şablonunu seçin.

Takip eden iletişim kutularında, aşağıdaki seçimleri yapın:

1. Proje Seçenekleri İletişim Kutusu
    1. Hızlı başlangıç uygulaması için bir ad girin, örneğin `helloworld`.
    1. Zaten bir Apple geliştirici hesabınız varsa, uygun bir kuruluş adı ve kuruluş tanımlayıcısı girin. Test amacıyla, `testorg` gibi herhangi bir ad seçebilirsiniz. Uygulamayı imzalamak için uygun bir sağlama profiline ihtiyacınız vardır. Ayrıntılar için [Apple Developer sitesine](https://developer.apple.com/) bakın.
    1. Swift 'un proje için dil olarak seçildiğinden emin olun.
    1. Görsel taslakları kullanmak ve belge tabanlı bir uygulama oluşturmak için onay kutularını devre dışı bırakın. Örnek uygulama için basit kullanıcı arabirimi programlı olarak oluşturulacaktır.
    1. Testler ve temel veriler için tüm onay kutularını devre dışı bırakın.
1. Proje dizini seçin
    1. Projeyi içine koymak için bir dizin seçin. Bu, Xcode projesi için tüm dosyaları içeren seçili dizinde `helloworld` bir dizin oluşturur.
    1. Bu örnek için Git deposu oluşturmayı devre dışı bırakın.
1. Xcode projesini kapatın. Daha sonra CocoaPods ayarladıktan sonra farklı bir örneğini kullanacaksınız.

## <a name="add-the-sample-code"></a>Örnek kod ekleme

1. HelloWorld projesi içindeki `helloworld` dizinine `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` adlı yeni bir üst bilgi dosyası yerleştirin ve içine aşağıdaki kodu yapıştırın:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/text-to-speech/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Hedef *-C köprü üstbilgi* alanı ![üst bilgi özellikleri ' nde bulunan HelloWorld hedefine ait Swift proje ayarlarına göreli yol `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` ekleyin](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-bridging-header.png)
1. Aşağıdaki şekilde otomatik oluşturulan `AppDelegate.swift` dosyasının içeriğini değiştirin:

   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/text-to-speech/helloworld/helloworld/AppDelegate.swift#code)]
1. `AppDelegate.swift`' de, dize `YourSubscriptionKey`, abonelik anahtarınızla değiştirin.
1. `YourServiceRegion` dize, aboneliğinizle ilişkili bölgeyle (örneğin, ücretsiz deneme aboneliği için `westus`) değiştirin.

## <a name="install-the-sdk-as-a-cocoapod"></a>SDK 'Yı bir CocoaPod olarak yükler

1. [Yükleme yönergeleri](https://guides.cocoapods.org/using/getting-started.html)bölümünde açıklandığı gibi CocoaPod Dependency Manager 'ı yükleme.
1. Örnek uygulamanızın dizinine gidin (`helloworld`). `Podfile` adlı bir metin dosyasını ve bu dizine aşağıdaki içeriği yerleştirin:

   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/text-to-speech/helloworld/Podfile)]
1. Terminalde `helloworld` dizinine gidin ve `pod install`komutunu çalıştırın. Bu işlem, bağımlılık olarak hem örnek uygulamayı hem de konuşma SDK 'sını içeren `helloworld.xcworkspace` bir Xcode çalışma alanı oluşturur. Bu çalışma alanı aşağıdaki şekilde kullanılacaktır.

## <a name="build-and-run-the-sample"></a>Örneği derleme ve çalıştırma

1. `helloworld.xcworkspace` çalışma alanını Xcode 'da açın.
1. Hata ayıklama çıkışını görünür hale getirin (**Görünüm** > **Hata Ayıklama Alanı** > **Konsolu Etkinleştir**).
1. Menüden **ürün** > **Çalıştır** ' ı seçerek veya **oynat** düğmesine tıklayarak örnek kodu derleyin ve çalıştırın.
1. Bazı metinleri girdikten ve uygulamadaki düğmeye tıkladıktan sonra, birleştirilmiş sesin çalındığını duymalısınız.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [GitHub 'da örneklerimizi keşfet](https://aka.ms/csspeech/samples)

