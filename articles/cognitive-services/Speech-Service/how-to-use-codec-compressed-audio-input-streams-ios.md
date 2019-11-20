---
title: İOS 'ta konuşma SDK 'Sı ile codec ile sıkıştırılmış ses akışı
titleSuffix: Azure Cognitive Services
description: İOS 'ta konuşma SDK 'Sı ile Azure konuşma Hizmetleri 'ne sıkıştırılmış ses akışını nasıl sağlayacağınızı öğrenin.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: chlandsi
ms.openlocfilehash: 1d78c690fd07eb974418f0ea17d71d1f394d863d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109572"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-ios"></a>Nasıl yapılır: iOS 'ta konuşma SDK 'Sı ile codec ile sıkıştırılmış ses girişi kullanma

Konuşma SDK 'sının **sıkıştırılmış ses giriş akışı** API 'si, bir çekme veya gönderme akışı kullanarak konuşma hizmetine sıkıştırılmış ses akışını sağlamanın bir yolunu sağlar.

> [!IMPORTANT]
> İOS üzerinde sıkıştırılmış ses akışı için konuşma SDK 'Sı sürüm 1.7.0 veya üzeri gereklidir. [ C++Linux (Ubuntu 16,04 C#, Ubuntu 18,04, debir 9)](how-to-use-codec-compressed-audio-input-streams.md) ve [Android 'de](how-to-use-codec-compressed-audio-input-streams-android.md) Java için de desteklenir.

WAV/PCM için ana hat konuşma belgelerine bakın. WAV/PCM dışında, aşağıdaki codec sıkıştırılmış giriş biçimleri desteklenir:

- MP3
- OPUS/OGG
- FLAC
- WAV w kapsayıcısında
- WAV kapsayıcısında MULAW

## <a name="prerequisites"></a>Önkoşullar

Sıkıştırılmış ses işleme, [GStreamer](https://gstreamer.freedesktop.org)kullanılarak uygulanır. Lisanslama nedenleriyle, bu işlevler SDK ile birlikte sunulmayabilir, ancak bu işlevleri içeren bir sarmalayıcı kitaplığın uygulama geliştiricileri tarafından derlenmesi ve SDK kullanılarak uygulamalarla birlikte oluşturulması gerekir.

Bu sarmalayıcı kitaplığı oluşturmak için önce [GStreamer SDK 'sını](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)indirip yükleyin. Ardından, [sarmalayıcı kitaplığı](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)Için Xcode projesini indirin.

Projeyi Xcode 'da açın ve **genel IOS cihaz** hedefi için oluşturun; Bu, belirli bir hedef için oluşturmak için çalışmaz.

Yapı adımı, `GStreamerWrapper.framework`adı ile tüm gerekli mimarilere yönelik dinamik bir kitaplık içeren dinamik bir çerçeve paketi oluşturur.

Bu çerçevenin konuşma Hizmetleri SDK 'Sı ile sıkıştırılmış ses akışları kullanan tüm uygulamalara dahil olması gerekir.

Bunu gerçekleştirmek için aşağıdaki ayarları Xcode projenize uygulayın:

1. Yeni oluşturduğunuz `GStreamerWrapper.framework` ve bilişsel hizmetler konuşma SDK 'sının çerçevesini, [buradan](https://aka.ms/csspeech/iosbinary)indirebilirsiniz, örnek projenizi içeren dizine kopyalayın.
1. _Proje ayarlarındaki_çerçeveler için yolları ayarlayın.
   1. **Katıştırılmış Ikili dosyalar** başlığı altındaki **genel** sekmesinde, bir Framework olarak SDK Kitaplığı ekleyin: **katıştırılmış ikililer Ekle** > **diğer...** > seçtiğiniz dizine gidin ve her iki çerçeveyi seçin.
   1. **Derleme Ayarları** sekmesine gidin ve **Tümü** ayarını etkinleştirin.
1. `$(SRCROOT)/..` dizinini _Arama Yolları_ başlığı altında **Çerçeve Arama Yolları**’na ekleyin.

## <a name="example-code-using-codec-compressed-audio-input"></a>Codec ile sıkıştırılmış ses girişini kullanan örnek kod

Konuşma hizmetlerine sıkıştırılmış bir ses biçiminde akış yapmak için bir `SPXPullAudioInputStream` veya `SPXPushAudioInputStream`oluşturun.

Aşağıdaki kod parçacığında, akışın sıkıştırma biçimi olarak MP3 belirterek bir `SPXPushAudioInputStream`örneğinden bir `SPXAudioConfiguration` oluşturma gösterilmektedir.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=66-77&highlight=2-11)]

Sonraki kod parçacığında sıkıştırılmış ses verilerinin bir dosyadan okunmasının ve `SPXPushAudioInputStream`nasıl balde gösterildiği gösterilmektedir.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=105-151&highlight=19-44)]

## <a name="next-steps"></a>Sonraki adımlar

- [Konuşma deneme aboneliğinizi alın](https://azure.microsoft.com/try/cognitive-services/)
- [Java 'da konuşmayı tanımayı öğrenin](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
