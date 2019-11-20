---
title: V3-Translator Metin Çevirisi API'si 'e geçiş
titleSuffix: Azure Cognitive Services
description: Bu makalede, v2 'den Azure bilişsel hizmetler Translator Metin Çevirisi API'si 'dan v3 'e geçiş yapmanıza yardımcı olacak adımlar sağlanmaktadır.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: eb43d549d3e0cd449c865d533fc8701c4c3912fd
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837321"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Translator Metin Çevirisi API'si v2 'yi v3 geçişine

> [!NOTE]
> V2, 30 Nisan 2018 tarihinde kullanımdan kaldırılmıştır. Yalnızca v3 'de bulunan yeni işlevsellikten yararlanmak için uygulamalarınızı v3 'e geçirin.
> 
> Microsoft Translator hub 'ı 17 Mayıs 2019 tarihinde kullanımdan kaldırılacaktır. [Önemli geçiş bilgilerini ve tarihlerini görüntüleyin](https://www.microsoft.com/translator/business/hub/).  

Microsoft Translator ekibi, Translator Metin Çevirisi API'si sürüm 3 ' ü (v3) yayımladı. Bu sürüm, yeni özellikler, kullanım dışı Yöntemler ve Microsoft Translator hizmetinden veri almaya yönelik yeni bir biçim içerir. Bu belge, uygulamaları v3 kullanacak şekilde değiştirmeye yönelik bilgiler sağlar. 

Bu belgenin sonu, daha fazla bilgi edinmek için yararlı bağlantılar içerir.

## <a name="summary-of-features"></a>Özelliklerin özeti

* Hiçbir Trace-of v3 No-Trace, Azure portal tüm fiyatlandırma katmanları için geçerlidir. Bu özellik, v3 API 'sine gönderilen metinlerin Microsoft tarafından kaydedileceği anlamına gelir.
* JSON-XML dosyası JSON ile değiştirilmiştir. Hizmete gönderilen ve hizmetten alınan tüm veriler JSON biçimindedir.
* Tek bir istekte birden çok hedef dil-çevirme yöntemi, tek bir istekteki çeviri için birden fazla ' to ' dillerini kabul eder. Örneğin, tek bir istek ' Ingilizce ve '-' Almanca, Ispanyolca ve Japonca ya da başka bir dil grubu olabilir.
* İki dilli sözlük-API 'ye iki dilli sözlük yöntemi eklenmiştir. Bu yöntem ' Lookup ' ve ' örnekler ' içerir.
* Alfabe içinde, API 'ye bir alfabede bir yöntem eklenmiştir. Bu yöntem, sözcükleri ve tümceleri tek bir betikte dönüştürür (ör. Arapça) başka bir betiğe (ör. Latin).
* Diller-yeni bir ' Languages ' yöntemi, ' Translate ', ' Dictionary ' ve ' alfabede ' yöntemleriyle kullanılmak üzere dil bilgilerini JSON biçiminde sunar.
* Çeviri için yeni-' Çevir ' yöntemine yeni özellikler eklenmiştir. v2 API 'sinde ayrı yöntemler olarak bulunan özelliklerden bazıları desteklenir. TranslateArray örneği bir örnektir.
* Konuş yöntemi-metin okuma işlevselliği artık Microsoft Translator API 'sinde desteklenmez. Metin okuma işlevselliği, [Microsoft konuşma hizmeti](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)'nde bulunabilir.

Aşağıdaki v2 ve v3 yöntemleri listesi, v2 ile gelen işlevleri sağlayacak olan v3 yöntemlerini ve API 'Leri tanımlar.

| V2 API yöntemi   | V3 API uyumluluğu |
|:----------- |:-------------|
| `Translate`     | [Çevir](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Çevir](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Diller](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Diller](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Microsoft konuşma hizmeti](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| `Speak`     | [Microsoft konuşma hizmeti](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| `Detect`     | [Acağı](reference/v3-0-detect.md)         |
| `DetectArray`     | [Acağı](reference/v3-0-detect.md)         |
| `AddTranslation`     | Özellik artık desteklenmiyor       |
| `AddTranslationArray`    | Özellik artık desteklenmiyor          |
| `BreakSentences`      | [Breakcümlesi](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | Özellik artık desteklenmiyor         |
| `GetTranslationsArray`      | Özellik artık desteklenmiyor         |

## <a name="move-to-json-format"></a>JSON biçimine taşı

Microsoft Translator Metin Çevirisi çeviri v2, XML biçimindeki verileri kabul etti ve döndürdü. V3 'de, API kullanılarak gönderilen ve alınan tüm veriler JSON biçimindedir. XML artık v3 'de kabul edilmez veya döndürülmeyecektir.

Bu değişiklik, v2 metin çevirisi API 'SI için yazılmış bir uygulamanın çeşitli yönlerini etkileyecektir. Örnek olarak: diller API 'SI metin çevirisi, alfabede ve iki sözlük yöntemi için dil bilgilerini döndürür. Tüm yöntemler için tüm dil bilgilerini tek bir çağrıda isteyebilir veya tek tek talep edebilirsiniz.

Diller yöntemi kimlik doğrulaması gerektirmez; Aşağıdaki bağlantıya tıklayarak, JSON 'da v3 için tüm dil bilgilerini görebilirsiniz:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, sözlük, alfabede](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Kimlik doğrulama anahtarı

V2 için kullandığınız kimlik doğrulama anahtarı v3 için kabul edilecektir. Yeni bir abonelik almanız gerekmez. Yıluzun geçiş süresi boyunca uygulamalarınızda v2 ve v3 'i karıştıracaksınız, ancak hala v2-XML ' den v3-JSON ' a geçiş yaparken yeni sürümler yayınlanmasını kolaylaştırabilirsiniz.

## <a name="pricing-model"></a>Fiyatlandırma Modeli

Microsoft Translator v3, v2 fiyatlandırılmıştı. boşluk da dahil olmak üzere karakter başına. V3 sürümündeki yeni özellikler, faturalandırma için hangi karakterlerin sayıldığını bazı değişiklikler yapar.

| V3 yöntemi   | Faturalandırma için sayılan karakterler |
|:----------- |:-------------|
| `Languages`     | Hiçbir karakter gönderilmedi, atanmadı, hiçbir ücret alınmaz.          |
| `Translate`     | Sayı, çeviri için kaç karakter gönderildiğini ve karakterlerin kaç dilde çevrileceğini temel alır. 50 karakter gönderildi ve 5 dil istendi.           |
| `Transliterate`     | Alfabede gönderilen karakterlerin sayısı sayılır.         |
| `Dictionary lookup & example`     | Sözlük araması ve örnekleri için gönderilen karakter sayısı sayılır.         |
| `BreakSentence`     | Ücret alınmaz.       |
| `Detect`     | Ücret alınmaz.      |

## <a name="v3-end-points"></a>V3 uç noktaları

Genel

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>V3 API metin çevirisi yöntemleri

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Uyumluluk ve özelleştirme

> [!NOTE]
> 
> Microsoft Translator hub 'ı 17 Mayıs 2019 tarihinde kullanımdan kaldırılacaktır. [Önemli geçiş bilgilerini ve tarihlerini görüntüleyin](https://www.microsoft.com/translator/business/hub/).   

Microsoft Translator v3 varsayılan olarak sinir makine çevirisini kullanır. Bu nedenle, Microsoft Translator hub 'ı ile kullanılamaz. Translator hub 'ı yalnızca eski istatistiksel makine çevirisini destekler. Sinir çevirisi özelleştirmesi artık özel çevirici kullanılarak kullanılabilir. [Sinir makine çevirisini özelleştirme hakkında daha fazla bilgi edinin](custom-translator/overview.md)

V3 metin API 'SI ile sinir çevirisi, standart kategorilerin (SMT, konuşma, teknik, generalnn) kullanımını desteklemez.

| |Uç Nokta|    GDPR Işlemci uyumluluğu|  Çevirmen hub 'ı kullan| Özel çevirici kullan (Önizleme)|
|:-----|:-----|:-----|:-----|:-----|
|Translator Metin Çevirisi API'si sürüm 2| api.microsofttranslator.com|    Hayır  |Evet    |Hayır|
|Translator Metin Çevirisi API'si sürüm 3| api.cognitive.microsofttranslator.com|  Evet|    Hayır| Evet|

**Translator Metin Çevirisi API'si sürüm 3**
* Genel olarak kullanılabilir ve tamamen desteklenir.
* , GDPR uyumlu bir işlemci olarak uyumludur ve tüm ISO 20001 ve 20018 yanı sıra SOC 3 sertifika gereksinimlerini karşılar. 
* Yeni Translator NMT özelleştirme özelliği olan özel çevirmenle (Önizleme) özelleştirdiğiniz sinir ağ çevirisi sistemlerini çağırabilmeniz için izin verir. 
* , Microsoft Translator hub 'ı kullanılarak oluşturulan özel çeviri sistemlerine erişim sağlamaz.

Api.cognitive.microsofttranslator.com uç noktasını kullanıyorsanız, Translator Metin Çevirisi API'si sürüm 3 ' ü kullanıyorsunuz.

**Translator Metin Çevirisi API'si sürüm 2**
* Tüm ISO 20.001, 20018 ve SOC 3 sertifika gereksinimlerini karşılamaz. 
* , Çevirmen özelleştirme özelliğiyle özelleştirdiğiniz sinir ağ çevirisi sistemlerini çağırmada izin vermez.
* Microsoft Translator hub 'ı kullanılarak oluşturulan özel çeviri sistemlerine erişim sağlar.
* Api.microsofttranslator.com uç noktasını kullanıyorsanız, Translator Metin Çevirisi API'si sürüm 2 ' i kullanıyorsunuz.

Çevirmen API 'sinin hiçbir sürümü, çevirilerinizi bir kayıt oluşturur. Çevirlarınız hiçbir şekilde hiç kimseyle paylaşılmaz. [Çevirmen No-Trace](https://www.aka.ms/NoTrace) Web sayfası hakkında daha fazla bilgi.

## <a name="links"></a>Bağlantılar

* [Microsoft Gizlilik Ilkesi](https://privacy.microsoft.com/privacystatement)
* [Yasal bilgileri Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Çevrimiçi hizmet koşulları](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [V 3.0 belgelerini görüntüle](reference/v3-0-reference.md)
