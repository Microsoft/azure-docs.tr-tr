---
title: 'Hızlı başlangıç: Sentezleştirme konuşma, amaç-C-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: Konuşma SDK 'sını kullanarak iOS 'ta bir konuşmayı hedefe nasıl sentezleyeceğinizi öğrenin
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 460d2069bea8809b7d453bbe1cf27284109df9b5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503475"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Hızlı başlangıç: konuşma SDK 'sını kullanarak iOS 'ta hedef için konuşmayı Sentezleştirme

Bu makalede, metinden konuşmayı sentezleştirmek için bilişsel hizmetler konuşma SDK 'sını kullanarak amaç-C ' d e bir iOS uygulaması oluşturmayı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, önkoşulların bir listesi aşağıda verilmiştir:

* Konuşma hizmeti için bir [abonelik anahtarı](~/articles/cognitive-services/Speech-Service/get-started.md)
* [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) veya üzeri bir MacOS makinesi
* Hedef, iOS sürüm 9,3 veya üzeri olarak ayarlandı

## <a name="get-the-speech-sdk-for-ios"></a>iOS için Konuşma SDK’sını alın

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Bu öğreticinin SDK sürümü 1.7.0 ' den önceki sürümüyle çalışmadığını unutmayın.

İOS için bilişsel hizmetler konuşma SDK 'Sı Şu anda bir Cocoa çerçevesi olarak dağıtılır.
Xcode projelerinde bir [CocoaPod](https://cocoapods.org/)olarak veya https://aka.ms/csspeech/iosbinary indirilen ve el ile bağlanmış bir şekilde kullanılabilir. Bu kılavuzda bir CocoaPod kullanılır.

## <a name="create-an-xcode-project"></a>Xcode Projesi oluşturma

Xcode’u başlatın ve **Dosya** > **Yeni** > **Proje** seçeneklerine tıklayarak yeni bir proje başlatın.
Şablon seçimi iletişim kutusunda, “iOS Tek Görünüm Uygulaması” şablonunu seçin.

Takip eden iletişim kutularında, aşağıdaki seçimleri yapın:

1. Proje Seçenekleri İletişim Kutusu
    1. Hızlı başlangıç uygulaması için bir ad girin, örneğin `helloworld`.
    1. Zaten bir Apple geliştirici hesabınız varsa uygun bir kuruluş adı ve kuruluş tanımlayıcısı girin. Test amacıyla, `testorg` gibi herhangi bir ad seçebilirsiniz. Uygulamayı imzalamak için uygun bir sağlama profiline ihtiyacınız vardır. Ayrıntılar için [Apple Developer sitesine](https://developer.apple.com/) bakın.
    1. Proje dili olarak Objective-C seçildiğinden emin olun.
    1. Testler ve temel veriler için tüm onay kutularını devre dışı bırakın.
    ![Proje Ayarları](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)
1. Proje dizini seçin
    1. Projeyi yerleştirmek için giriş dizininizi seçin. Bu, giriş dizininizde Xcode projesi için tüm dosyaları içeren `helloworld` bir dizin oluşturur.
    1. Bu örnek için Git deposu oluşturmayı devre dışı bırakın.

## <a name="install-the-sdk-as-a-cocoapod"></a>SDK 'Yı bir CocoaPod olarak yükler

1. [Yükleme yönergeleri](https://guides.cocoapods.org/using/getting-started.html)bölümünde açıklandığı gibi CocoaPod Dependency Manager 'ı yükleme.
1. Örnek uygulamanızın dizinine gidin (`helloworld`). `Podfile` adlı bir metin dosyasını ve bu dizine aşağıdaki içeriği yerleştirin:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/Podfile)]
1. Terminalde `helloworld` dizinine gidin ve `pod install`komutunu çalıştırın. Bu işlem, bağımlılık olarak hem örnek uygulamayı hem de konuşma SDK 'sını içeren `helloworld.xcworkspace` bir Xcode çalışma alanı oluşturur. Bu çalışma alanı aşağıdaki şekilde kullanılacaktır.

## <a name="add-the-sample-code"></a>Örnek kod ekleme

1. `helloworld.xcworkspace` çalışma alanını Xcode 'da açın.
1. Aşağıdaki şekilde otomatik oluşturulan `AppDelegate.m` dosyasının içeriğini değiştirin:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Aşağıdaki şekilde otomatik oluşturulan `ViewController.m` dosyasının içeriğini değiştirin:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/ViewController.m#code)]
1. `YourSubscriptionKey` dizesini abonelik anahtarınızla değiştirin.
1. `YourServiceRegion` dizesini aboneliğinizle ilişkili [bölge](~/articles/cognitive-services/Speech-Service/regions.md) ile (örneğin ücretsiz deneme aboneliğinde `westus`) değiştirin.

## <a name="build-and-run-the-sample"></a>Örneği derleme ve çalıştırma

1. Hata ayıklama çıkışını görünür hale getirin (**Görünüm** > **Hata Ayıklama Alanı** > **Konsolu Etkinleştir**).
1. **Ürün** > **hedefi** menüsündeki listeden uygulama hedefi olarak, geliştirme makinenize bağlı iOS simülatörü veya iOS cihazını seçin.
1. Menüden **Ürün** > **Çalıştır** seçeneklerini belirleyerek veya **Oynat** düğmesine tıklayarak iOS simülatöründe örnek kodu derleyin ve çalıştırın.

   ![iOS Uygulaması Simülasyonu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app-tts.png)

1. Bazı metinleri girdikten ve uygulamadaki düğmeye tıkladıktan sonra, birleştirilmiş sesin çalındığını duymalısınız.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [GitHub 'da hedef-C örneklerini keşfet](https://aka.ms/csspeech/samples)

