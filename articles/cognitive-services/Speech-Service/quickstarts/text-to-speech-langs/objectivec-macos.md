---
title: 'Hızlı başlangıç: Sentezleştirme konuşma, amaç-C-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: MacOS 'ta konuşma SDK 'sını kullanarak konuşmayı nasıl sentezleyeceğinizi öğrenin
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 27e471bb1b155ab245e68712031eef4efb4d5b34
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503461"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Hızlı başlangıç: konuşma SDK 'sını kullanarak macOS 'ta hedef için konuşmayı Sentezleştirme

Bu makalede, bilişsel hizmetler konuşma SDK 'sını kullanarak bir macOS uygulamasının nasıl oluşturulduğunu öğrenirsiniz ve bu da konuşmayı metinden bulup varsayılan ses çıkışıyla oynatabilir.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, önkoşulların bir listesi aşağıda verilmiştir:

* Konuşma hizmeti için bir [abonelik anahtarı](~/articles/cognitive-services/Speech-Service/get-started.md)
* [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) veya üzeri ve macos 10,13 veya üzeri bir MacOS makinesi

## <a name="get-the-speech-sdk-for-macos"></a>MacOS için konuşma SDK 'sını alın

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Bu öğreticinin SDK sürümü 1.7.0 ' den önceki sürümüyle çalışmadığını unutmayın.

Mac için bilişsel hizmetler konuşma SDK 'Sı, çerçeve paketi olarak dağıtılır.
Xcode projelerinde bir [CocoaPod](https://cocoapods.org/)olarak veya https://aka.ms/csspeech/macosbinary indirilen ve el ile bağlanmış bir şekilde kullanılabilir. Bu kılavuzda bir CocoaPod kullanılır.

## <a name="create-an-xcode-project"></a>Xcode projesi oluşturma

Xcode’u başlatın ve **Dosya** > **Yeni** > **Proje** seçeneklerine tıklayarak yeni bir proje başlatın.
Şablon Seçimi iletişim kutusunda, "Cocoa uygulaması" şablonunu seçin.

Takip eden iletişim kutularında, aşağıdaki seçimleri yapın:

1. Proje Seçenekleri İletişim Kutusu
    1. Hızlı başlangıç uygulaması için bir ad girin, örneğin `helloworld`.
    1. Zaten bir Apple geliştirici hesabınız varsa, uygun bir kuruluş adı ve kuruluş tanımlayıcısı girin. Test amacıyla, `testorg` gibi herhangi bir ad seçebilirsiniz. Uygulamayı imzalamak için uygun bir sağlama profiline ihtiyacınız vardır. Ayrıntılar için [Apple Developer sitesine](https://developer.apple.com/) bakın.
    1. Proje dili olarak Objective-C seçildiğinden emin olun.
    1. Görsel taslakları kullanmak ve belge tabanlı bir uygulama oluşturmak için onay kutularını devre dışı bırakın. Örnek uygulama için basit kullanıcı arabirimi programlı olarak oluşturulacaktır.
    1. Testler ve temel veriler için tüm onay kutularını devre dışı bırakın.
    ![Proje Ayarları](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)
1. Proje dizini seçin
    1. Projeyi içine koymak için bir dizin seçin. Bu, giriş dizininizde Xcode projesi için tüm dosyaları içeren `helloworld` bir dizin oluşturur.
    1. Bu örnek için Git deposu oluşturmayı devre dışı bırakın.
1. Ağ erişimi için yetkilendirmeleri ayarlayın. Uygulama yapılandırmasına ulaşmak için sol taraftaki genel bakışın ilk satırındaki uygulama adına tıklayın ve ardından "yetenekler" sekmesini seçin.
    1. Uygulama için "uygulama korumalı alanı" ayarını etkinleştirin.
    1. "Giden bağlantılar" erişimi için onay kutularını etkinleştirin.
    ![korumalı alan ayarları](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox-tts.png)
1. Xcode projesini kapatın. Daha sonra CocoaPods ayarladıktan sonra farklı bir örneğini kullanacaksınız.

## <a name="install-the-sdk-as-a-cocoapod"></a>SDK 'Yı bir CocoaPod olarak yükler

1. [Yükleme yönergeleri](https://guides.cocoapods.org/using/getting-started.html)bölümünde açıklandığı gibi CocoaPod Dependency Manager 'ı yükleme.
1. Örnek uygulamanızın dizinine gidin (`helloworld`). `Podfile` adlı bir metin dosyasını ve bu dizine aşağıdaki içeriği yerleştirin:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/Podfile)]
1. Terminalde `helloworld` dizinine gidin ve `pod install`komutunu çalıştırın. Bu işlem, bağımlılık olarak hem örnek uygulamayı hem de konuşma SDK 'sını içeren `helloworld.xcworkspace` bir Xcode çalışma alanı oluşturur. Bu çalışma alanı aşağıdaki şekilde kullanılacaktır.

## <a name="add-the-sample-code"></a>Örnek kod ekleme

1. `helloworld.xcworkspace` çalışma alanını Xcode 'da açın.
1. Aşağıdaki şekilde otomatik oluşturulan `AppDelegate.m` dosyasının içeriğini değiştirin:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. `YourSubscriptionKey` dizesini abonelik anahtarınızla değiştirin.
1. `YourServiceRegion` dizesini aboneliğinizle ilişkili [bölge](~/articles/cognitive-services/Speech-Service/regions.md) ile (örneğin ücretsiz deneme aboneliğinde `westus`) değiştirin.

## <a name="build-and-run-the-sample"></a>Örneği derleme ve çalıştırma

1. Hata ayıklama çıkışını görünür hale getirin (**Görünüm** > **Hata Ayıklama Alanı** > **Konsolu Etkinleştir**).
1. Menüden **ürün** -> **Çalıştır** ' ı seçerek veya **oynat** düğmesine tıklayarak örnek kodu derleyin ve çalıştırın.
1. Bazı metinleri girdikten ve uygulamadaki düğmeye tıkladıktan sonra, birleştirilmiş sesin çalındığını duymalısınız.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [GitHub 'da hedef-C örneklerini keşfet](https://aka.ms/csspeech/samples)

