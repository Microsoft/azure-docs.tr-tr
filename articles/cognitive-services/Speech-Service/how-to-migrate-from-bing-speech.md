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
ms.date: 10/01/2018
ms.author: nitinme
ms.openlocfilehash: 00a20ac2d7f05c42be9e955eeb8f47c302db7885
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464578"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Bing Konuşma konuşma hizmetine geçirme

Uygulamalarınızı Bing Konuşma API'si konuşma hizmetine geçirmek için bu makaleyi kullanın.

Bu makalede, Bing Konuşma API 'Leri ile konuşma Hizmetleri arasındaki farklılıklar özetlenmektedir ve uygulamalarınızın geçirilmesi için stratejiler önerilmektedir. Bing Konuşma API'si abonelik anahtarınız konuşma hizmetiyle çalışmaz; Yeni bir konuşma Hizmetleri aboneliğine ihtiyacınız olacaktır.

Tek bir konuşma Hizmetleri abonelik anahtarı aşağıdaki özelliklere erişim verir. Hepsinin kullanımı bağımsız bir şekilde ölçüldüğünden yalnızca kullandığınız hizmetler için ödeme yapmış olursunuz.

* [Konuşmayı metne dönüştürme](speech-to-text.md)
* [Özel konuşmayı metne dönüştürme](https://cris.ai)
* [Metin okuma](text-to-speech.md)
* [Özel metin okuma sesleri](how-to-customize-voice-font.md)
* [Konuşma çevirisi ](speech-translation.md) ([Metin çevirisi](../translator/translator-info-overview.md) özelliğini kapsamaz)

[Konuşma SDK 'sı](speech-sdk.md) , Bing Konuşma istemci kitaplıklarının işlevsel bir yerini alır, ancak farklı bir API kullanır.

## <a name="comparison-of-features"></a>Özelliklerin karşılaştırması

Konuşma Hizmetleri, aşağıdaki farklılıklarla büyük ölçüde Bing Konuşma benzerdir.

Özellik | Bing Konuşma | Konuşma Hizmetleri | Ayrıntılar
-|-|-|-
C++'SıNıN | :heavy_minus_sign: | :heavy_check_mark: | Konuşma Hizmetleri Windows ve Linux 'u destekler.
Java SDK | :heavy_check_mark: | :heavy_check_mark: | Konuşma Hizmetleri, Android ve konuşma cihazlarını destekler.
C# SDK’sı | :heavy_check_mark: | :heavy_check_mark: | Konuşma Hizmetleri, Windows 10, Evrensel Windows Platformu (UWP) ve .NET Standard 2,0 'yi destekler.
Sürekli konuşma tanıma | 10 dakika | Sınırsız (SDK ile) | Hem Bing Konuşma hem de konuşma Hizmetleri WebSockets protokolleri çağrı başına en fazla 10 dakika destekler. Ancak, konuşma SDK 'Sı zaman aşımına göre otomatik olarak yeniden bağlanır veya bağlantıyı keser.
Kısmi veya geçici sonuçlar | :heavy_check_mark: | :heavy_check_mark: | WebSockets Protocol veya SDK ile.
Özel konuşma modelleri | :heavy_check_mark: | :heavy_check_mark: | Bing Konuşma, ayrı bir Özel Konuşma Tanıma aboneliği gerektirir.
Özel ses yazı tipleri | :heavy_check_mark: | :heavy_check_mark: | Bing Konuşma ayrı bir özel ses aboneliği gerektirir.
24-KHz sesler | :heavy_minus_sign: | :heavy_check_mark:
Konuşma niyeti tanıma | Ayrı bir LUSıS API çağrısı gerektirir | Tümleşik (SDK ile) |  Konuşma hizmetiyle bir LUO anahtarı kullanabilirsiniz.
Basit amaç tanıma | :heavy_minus_sign: | :heavy_check_mark:
Uzun ses dosyalarının toplu olarak dökümünü alma | :heavy_minus_sign: | :heavy_check_mark:
Tanıma modu | Uç nokta URI 'SI aracılığıyla el ile | Automatic | Tanıma modu konuşma hizmetinde kullanılamıyor.
Uç nokta konumu | Genel | Sel | Bölgesel uç noktalar gecikme süresini geliştirir.
REST API'leri | :heavy_check_mark: | :heavy_check_mark: | Konuşma Hizmetleri REST API 'Leri Bing Konuşma (farklı uç nokta) ile uyumludur. REST API 'Leri, metinden konuşmaya ve sınırlı konuşmadan metne işlevselliği destekler.
WebSockets protokolleri | :heavy_check_mark: | :heavy_check_mark: | Konuşma Hizmetleri WebSockets API 'SI Bing Konuşma (farklı uç nokta) ile uyumludur. Mümkünse, kodunuzu basitleştirmek için konuşma SDK 'sına geçiş yapın.
Hizmetten hizmete API çağrıları | :heavy_check_mark: | :heavy_minus_sign: | C# Hizmet kitaplığı aracılığıyla Bing Konuşma olarak sunulmaktadır.
Açık kaynak SDK 'Sı | :heavy_check_mark: | :heavy_minus_sign: |

Konuşma Hizmetleri, zaman tabanlı bir fiyatlandırma modeli kullanır (işlem tabanlı model yerine). Ayrıntılar için bkz. [konuşma Hizmetleri Fiyatlandırması](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

## <a name="migration-strategies"></a>Geçiş stratejileri

Siz veya kuruluşunuzun bir Bing Konuşma API'si kullanan geliştirme veya üretimde uygulamalar varsa, bunları konuşma hizmetlerini mümkün olan en kısa sürede kullanacak şekilde güncelleştirmelisiniz. Kullanılabilir SDK 'lar, kod örnekleri ve öğreticiler için [konuşma Hizmetleri belgelerine](index.md) bakın.

Konuşma Hizmetleri [REST API 'leri](rest-apis.md) Bing Konuşma API 'leriyle uyumludur. Şu anda Bing Konuşma REST API 'Lerini kullanıyorsanız, yalnızca REST uç noktasını değiştirmeniz ve bir konuşma Hizmetleri abonelik anahtarına geçmeniz gerekir.

Konuşma Hizmetleri WebSockets protokolleri, Bing Konuşma tarafından kullanılanlarla de uyumludur. Yeni geliştirme için WebSockets yerine konuşma SDK 'sını kullanmanızı öneririz. Mevcut kodu SDK 'ya de geçirmek iyi bir fikirdir. Ancak REST API 'Lerinde olduğu gibi, WebSockets aracılığıyla Bing Konuşma kullanan mevcut kod, yalnızca Endpoint ve güncelleştirilmiş bir anahtarda değişiklik gerektirir.

Belirli bir programlama dili için Bing Konuşma istemci kitaplığı kullanıyorsanız, [konuşma SDK 'sına](speech-sdk.md) GEÇIŞ, API farklı olduğundan uygulamanızda değişiklik yapılmasını gerektirir. Konuşma SDK 'Sı, kodunuzu daha basit hale getirir ve ayrıca yeni özelliklere erişmenizi sağlar.

Şu anda konuşma SDK 'sı ( C# [ayrıntıları burada](https://aka.ms/csspeech)), Java (Android ve özel cihazlar), amaç C (iOS), C++ (Windows ve Linux) ve JavaScript 'i desteklemektedir. Tüm platformlardaki API 'Ler benzer, çok platformlu geliştirme için de benzerdir.

Konuşma Hizmetleri küresel bir uç nokta sunmaz. Uygulamanızın tüm trafiği için tek bir bölgesel uç nokta kullandığında uygulamanızın verimli bir şekilde işlev görür olup olmadığını belirleme. Aksi takdirde, en etkili uç noktayı öğrenmek için coğrafi konum kullanın. Kullandığınız her bölgede ayrı bir konuşma Hizmetleri aboneliğine sahip olmanız gerekir.

Uygulamanız uzun süreli bağlantılar kullanıyorsa ve kullanılabilir bir SDK kullanamıyoruz, bir WebSockets bağlantısı kullanabilirsiniz. 10 dakikalık zaman aşımı sınırını uygun saatlere yeniden bağlanarak yönetin.

Konuşma SDK 'sını kullanmaya başlamak için:

1. [Konuşma SDK 'sını](speech-sdk.md)indirin.
1. Konuşma Hizmetleri [hızlı başlangıç kılavuzlarından](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) ve [öğreticilerle](how-to-recognize-intents-from-speech-csharp.md)çalışın. Ayrıca, yeni API 'lerle ilgili deneyim almak için [kod örneklerine](samples.md) bakın.
1. Konuşma hizmetlerini kullanmak için uygulamanızı güncelleştirin.

## <a name="support"></a>Destek

Bing Konuşma müşterilerin bir [destek bileti](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)açarak müşteri desteğiyle iletişim kurabilmesi gerekir. Destek gereksinimlerinizin [Teknik destek planı](https://azure.microsoft.com/support/plans/)gerektirmesi durumunda bize de başvurabilirsiniz.

Konuşma hizmeti, SDK ve API desteği için konuşma Hizmetleri [desteği sayfasını](support.md)ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* [Konuşma hizmetlerini ücretsiz deneyin](get-started.md)
* [Hızlı başlangıç: konuşma SDK 'sını kullanarak UWP uygulamasındaki konuşmayı tanıma](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Ayrıca bkz.
* [Konuşma Hizmetleri sürüm notları](releasenotes.md)
* [Konuşma hizmeti nedir?](overview.md)
* [Konuşma Hizmetleri ve konuşma SDK belgeleri](speech-sdk.md#get-the-sdk)
