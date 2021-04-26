---
title: Bing Konuşma konuşma hizmetine geçirme
titleSuffix: Azure Cognitive Services
description: Mevcut bir Bing Konuşma aboneliğinden Azure bilişsel hizmetler 'den konuşma hizmetine nasıl geçeceğinizi öğrenin.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: nitinme
ms.openlocfilehash: d6b7b298e7e4d18e68985ec3cf536c90ef773608
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644395"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Bing Konuşma konuşma hizmetine geçirme

Uygulamalarınızı Bing Konuşma API'si konuşma hizmetine geçirmek için bu makaleyi kullanın.

Bu makalede, Bing Konuşma API 'Leri ile konuşma hizmeti arasındaki farklar özetlenmektedir ve uygulamalarınızın geçirilmesi için stratejiler önerilir. Bing Konuşma API'si abonelik anahtarınız konuşma hizmetiyle çalışmaz; Yeni bir konuşma hizmeti aboneliğine ihtiyacınız olacaktır.

Tek bir konuşma hizmeti abonelik anahtarı aşağıdaki özelliklere erişim verir. Hepsinin kullanımı bağımsız bir şekilde ölçüldüğünden yalnızca kullandığınız hizmetler için ödeme yapmış olursunuz.

* [Konuşmayı metne dönüştürme](speech-to-text.md)
* [Özel konuşmayı metne dönüştürme](/azure/cognitive-services/speech-service/custom-speech-overview)
* [Metin okuma](text-to-speech.md)
* [Özel metin okuma sesleri](./how-to-custom-voice-create-voice.md)
* [Konuşma çevirisi ](speech-translation.md) ([Metin çevirisi](../translator/translator-info-overview.md) özelliğini kapsamaz)

[Konuşma SDK 'sı](speech-sdk.md) , Bing Konuşma istemci kitaplıklarının işlevsel bir yerini alır, ancak farklı bir API kullanır.

## <a name="comparison-of-features"></a>Özelliklerin karşılaştırması

Konuşma hizmeti, aşağıdaki farklılıklarla büyük ölçüde Bing Konuşma benzerdir.

| Özellik | Bing Konuşma | Konuşma hizmeti | Ayrıntılar |
|--|--|--|--|
| C# SDK’sı | :heavy_check_mark: | :heavy_check_mark: | Konuşma hizmeti, Windows 10, Evrensel Windows Platformu (UWP) ve .NET Standard 2,0 'yi destekler. |
| C++ SDK | : heavy_minus_sign: | :heavy_check_mark: | Konuşma hizmeti Windows ve Linux 'u destekler. |
| Java SDK | :heavy_check_mark: | :heavy_check_mark: | Konuşma hizmeti, Android ve konuşma cihazlarını destekler. |
| Sürekli konuşma tanıma | 10 dakika | Sınırsız | Konuşma SDK 'Sı sınırsız sürekli tanımayı destekler ve zaman aşımından veya bağlantıyı kestikten sonra otomatik olarak yeniden bağlanır. |
| Kısmi veya geçici sonuçlar | :heavy_check_mark: | :heavy_check_mark: | Konuşma SDK 'Sı ile desteklenir. |
| Özel konuşma modelleri | :heavy_check_mark: | :heavy_check_mark: | Bing Konuşma, ayrı bir Özel Konuşma Tanıma aboneliği gerektirir. |
| Özel ses yazı tipleri | :heavy_check_mark: | :heavy_check_mark: | Bing Konuşma ayrı bir özel ses aboneliği gerektirir. |
| 24-kHz sesler | : heavy_minus_sign: | :heavy_check_mark: |
| Konuşma amacı tanıma | Ayrı bir LUSıS API çağrısı gerektirir | Tümleşik (SDK ile) | Konuşma hizmetiyle bir LUO anahtarı kullanabilirsiniz. |
| Basit amaç tanıma | : heavy_minus_sign: | :heavy_check_mark: |
| Uzun ses dosyalarının toplu olarak dökümünü alma | : heavy_minus_sign: | :heavy_check_mark: |
| Tanıma modu | Uç nokta URI 'SI aracılığıyla el ile | Automatic | Tanıma modu konuşma hizmetinde kullanılamıyor. |
| Uç nokta konumu | Genel | Bölgesel | Bölgesel uç noktalar gecikme süresini geliştirir. |
| REST API'leri | :heavy_check_mark: | :heavy_check_mark: | Konuşma hizmeti REST API 'Leri Bing Konuşma (farklı uç nokta) ile uyumludur. REST API 'Leri, metinden konuşmaya ve sınırlı konuşmadan metne işlevselliği destekler. |
| WebSockets protokolleri | :heavy_check_mark: | : heavy_minus_sign: | Konuşma SDK 'Sı, hizmete sabit bir bağlantı gerektiren işlevsellik için Web soketi bağlantılarını soyutlar, bu nedenle bunlara el ile abone olmayı desteklemez. |
| Hizmetten hizmete API çağrıları | :heavy_check_mark: | : heavy_minus_sign: | C# hizmet kitaplığı aracılığıyla Bing Konuşma olarak sunulmaktadır. |
| Açık kaynaklı SDK | :heavy_check_mark: | : heavy_minus_sign: |

Konuşma hizmeti, zaman tabanlı bir fiyatlandırma modeli (işlem tabanlı model yerine) kullanır. Ayrıntılar için bkz. [konuşma hizmeti fiyatlandırması](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

## <a name="migration-strategies"></a>Geçiş stratejileri

Siz veya kuruluşunuzun bir Bing Konuşma API'si kullanan geliştirme veya üretimde uygulamalar varsa, bunları konuşma hizmetini mümkün olan en kısa sürede kullanacak şekilde güncelleştirmelisiniz. Kullanılabilir SDK 'lar, kod örnekleri ve öğreticiler için [konuşma hizmeti belgelerine](index.yml) bakın.

Konuşma hizmeti [REST API 'leri](./overview.md#reference-docs) Bing Konuşma API 'leriyle uyumludur. Şu anda Bing Konuşma REST API 'Lerini kullanıyorsanız, yalnızca REST uç noktasını değiştirmeniz ve bir konuşma hizmeti abonelik anahtarına geçmeniz gerekir.

Belirli bir programlama dili için Bing Konuşma istemci kitaplığı kullanıyorsanız, [konuşma SDK 'sına](speech-sdk.md) GEÇIŞ, API farklı olduğundan uygulamanızda değişiklik yapılmasını gerektirir. Konuşma SDK 'Sı, kodunuzu daha basit hale getirir ve ayrıca yeni özelliklere erişmenizi sağlar. Konuşma SDK 'Sı çok çeşitli programlama dillerinde kullanılabilir. Tüm platformlardaki API 'Ler benzer, çok platformlu geliştirme için de benzerdir.

Konuşma hizmeti küresel bir uç nokta sunmaz. Uygulamanızın tüm trafiği için tek bir bölgesel uç nokta kullandığında uygulamanızın verimli bir şekilde işlev görür olup olmadığını belirleme. Aksi takdirde, en etkili uç noktayı öğrenmek için coğrafi konum kullanın. Kullandığınız her bölgede ayrı bir konuşma hizmeti aboneliğine sahip olmanız gerekir.

Konuşma SDK 'sını kullanmaya başlamak için:

1. [Konuşma SDK 'sını](speech-sdk.md)indirin.
1. Konuşma hizmeti [hızlı başlangıç kılavuzlarından](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) ve [öğreticilerle](how-to-recognize-intents-from-speech-csharp.md)çalışın. Ayrıca, yeni API 'lerle ilgili deneyim almak için [kod örneklerine](./speech-sdk.md#sample-source-code) bakın.
1. Konuşma hizmetini kullanmak için uygulamanızı güncelleştirin.

## <a name="support"></a>Destek

Bing Konuşma müşterilerin bir [destek bileti](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)açarak müşteri desteğiyle iletişim kurabilmesi gerekir. Destek gereksinimlerinizin [Teknik destek planı](https://azure.microsoft.com/support/plans/)gerektirmesi durumunda bize de başvurabilirsiniz.

Konuşma hizmeti, SDK ve API desteği için konuşma hizmeti [desteği sayfasını](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* [Konuşma hizmetini ücretsiz deneyin](overview.md#try-the-speech-service-for-free)
* [Konuşmayı metne dönüştürme ile çalışmaya başlama](get-started-speech-to-text.md)
* [Metin okumayı kullanmaya başlama](get-started-text-to-speech.md)

## <a name="see-also"></a>Ayrıca bkz.

* [Konuşma hizmeti sürüm notları](releasenotes.md)
* [Konuşma hizmeti nedir?](overview.md)
* [Konuşma hizmeti ve konuşma SDK belgeleri](speech-sdk.md#get-the-speech-sdk)
