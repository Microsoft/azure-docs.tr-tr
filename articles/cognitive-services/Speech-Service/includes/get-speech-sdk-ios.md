---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 31634abe2768ec47ee2aa66051a7a363f83c6009
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043230"
---
:::row:::
    :::column span="3":::
        İOS için geliştirme yaparken, kullanılabilir iki konuşma SDK 'Sı vardır. Amaç-C konuşma SDK 'Sı yerel olarak bir iOS CocoaPod paketi olarak kullanılabilir. Alternatif olarak, 2,0 .NET Standard uyguladığı için .NET konuşma SDK 'Sı Xamarin. iOS ile birlikte kullanılabilir.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Swift ile amaç-C konuşma SDK 'sını kullanan Ayrıntılar için bkz. <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">hedefi Swift 'A alma-c </a>.

### <a name="system-requirements"></a>Sistem Gereksinimleri

- MacOS sürüm 10,3 veya üzeri
- Hedef iOS 9,3 veya üzeri

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        İOS CocoaPod paketi, <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (veya üzeri) </a> tümleşik geliştirme ORTAMı (IDE) ile indirilebilir ve kullanılabilir. İlk olarak, <a href="https://aka.ms/csspeech/iosbinary" target="_blank">CocoaPod ikili dosyasını indirin </a>. Kendisini amaçlanan kullanım için aynı dizinde ayıklayın, *pod dosyası* oluşturun ve `pod` olarak listeleyin `target` .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.iOS, .NET geliştiricileri için eksiksiz iOS SDK’sını sunar. Visual Studio’da C# veya F# kullanarak tamamen yerel iOS uygulamaları oluşturun. Daha fazla bilgi için bkz. <a href="/xamarin/ios/" target="_blank">Xamarin. iOS </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp;❤️ &nbsp;        <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>Ek kaynaklar

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS konuşma SDK 'Sı hızlı başlangıç hedefi-C kaynak kodu </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">iOS konuşma SDK hızlı başlangıç Swift kaynak kodu </a>