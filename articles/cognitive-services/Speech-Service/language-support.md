---
title: Dil desteği-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma hizmeti, konuşmadan metne ve metinden konuşmaya dönüştürmeye, konuşma çevirisi ile birlikte çok sayıda dili destekler. Bu makale, hizmet özelliğine göre dil desteğinin kapsamlı bir listesini sağlar.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 52dd642c661aa60157876a89d41c771cabfe2f1d
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256166"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Konuşma hizmeti için dil ve ses desteği

Dil desteği, konuşma hizmeti işlevselliğine göre farklılık gösterir. Aşağıdaki tablolar, [konuşmadan metne](#speech-to-text), [metinden konuşmaya](#text-to-speech)ve [konuşma çevirisi](#speech-translation) hizmet teklifleri için dil desteğini özetler.

## <a name="speech-to-text"></a>Konuşmayı metne dönüştürme

Hem Microsoft konuşma SDK 'Sı hem de REST API aşağıdaki dilleri (yerel ayarlar) destekler. 

Doğruluğu artırmak için, bir dilin bir alt kümesi için, **Ses + insan etiketli yazılı** döküm veya **Ilgili metin: cümleler** karşıya yüklenirken özelleştirme sunulur. **Ses + insan etiketli yazılı betikler** ile akustik modelin özelleştirilmesi için destek, aşağıda listelenen belirli temel modellerle sınırlıdır. Diğer temel modeller ve diller yalnızca Ilgili metinle aynı şekilde özel modeller eğmek için yazılı betiklerin metnini kullanır **: cümleler**. Özelleştirme hakkında daha fazla bilgi için bkz. [özel konuşma tanıma kullanmaya başlama](./custom-speech-overview.md).

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Dil                 | Yerel ayar (BCP-47) | Özelleştirmeler  | [Dil algılama](how-to-automatic-language-detection.md) |
|------------------------------------|--------|---------------------------------------------------|-------------------------------|
| Arapça (Bahreyn), modern standart  | `ar-BH` | Metin                                   |                           | 
| Arapça (Mısır)                     | `ar-EG` | Metin                                   | Yes                          |
| Arapça (Irak)                      | `ar-IQ` | Metin                                   |                           |
| Arapça (Israil)                    | `ar-IL` | Metin                                   |                           |
| Arapça (Ürdün)                    | `ar-JO` | Metin                                   |                           |
| Arapça (Kuveyt)                    | `ar-KW` | Metin                                   |                           |
| Arapça (Lübnan)                   | `ar-LB` | Metin                                   |                           |
| Arapça (Umman)                      | `ar-OM` | Metin                                   |                           |
| Arapça (Qtor)                     | `ar-QA` | Metin                                   |                           |
| Arapça (Suudi Arabistan)              | `ar-SA` | Metin                                   |                           |
| Arapça (Filistin Yönetimi devleti)        | `ar-PS` | Metin                                   |                           |
| Arapça (Suriye)                     | `ar-SY` | Metin                                   |                           |
| Arapça (Birleşik Arap Emirlikleri)      | `ar-AE` | Metin                                   |                           |
| Bulgarca (Bulgaristan)               | `bg-BG` | Metin                                   |                           |
| Katalanca (İspanya)                    | `ca-ES` | Metin                                   | Yes                          |
| Çince (Cantoneo, geleneksel)   | `zh-HK` | Ses (20201015)<br>Metin                 |        Yes                   |
| Çince (Mandarin, Basitleştirilmiş)     | `zh-CN` | Ses (20200910)<br>Metin                 |     Yes                      |
| Çince (Taiwanese Mandarin)       | `zh-TW` | Ses (20190701, 20201015)<br>Metin                 |           Yes                |
| Hırvatça (Hırvatistan)                 | `hr-HR` | Metin                                   |                           |
| Çekçe (Çek Cumhuriyeti)             | `cs-CZ` | Metin                                   |                           |
| Danca (Danimarka)                   | `da-DK` | Metin                                   | Yes                          |
| Felemenkçe (Hollanda)                | `nl-NL` | Ses (20201015)<br>Metin<br>İşi|    Yes                       |
| İngilizce (Avustralya)                | `en-AU` | Ses (20201019)<br>Metin                 | Yes                          |
| İngilizce (Kanada)                   | `en-CA` | Ses (20201019)<br>Metin                 | Yes                          |
| İngilizce (Gana)                    | `en-GH` | Metin                                   |                           |
| İngilizce (Hong Kong)                | `en-HK` | Metin                                   |                           |
| İngilizce (Hindistan)                    | `en-IN` | Ses (20200923)<br>Metin                 |                          |
| İngilizce (İrlanda)                  | `en-IE` | Metin                                   |                           |
| İngilizce (Kenya)                    | `en-KE` | Metin                                   |                           |
| İngilizce (Yeni Zelanda)              | `en-NZ` | Ses (20201019)<br>Metin                 |                          |
| İngilizce (Nijerya)                  | `en-NG` | Metin                                   |                           |
| İngilizce (Filipinler)              | `en-PH` | Metin                                   |                           |
| İngilizce (Singapur)                | `en-SG` | Metin                                   |                           |
| İngilizce (Güney Afrika)             | `en-ZA` | Metin                                   |                           |
| İngilizce (Tanzanya)                 | `en-TZ` | Metin                                   |                           |
| İngilizce (İngiltere)           | `en-GB` | Ses (20201019)<br>Metin<br>İşi| Yes                          |
| İngilizce (ABD)            | `en-US` | Ses (20201019)<br>Metin<br>İşi| Yes                          |
| Estonya dili (Estonya)                  | `et-EE` | Metin                                   |                           |
| Filipin Dili (Filipinler)             | `fil-PH`| Metin                                   |                           |
| Fince (Finlandiya)                  | `fi-FI` | Metin                                   |     Yes                      |
| Fransızca (Kanada)                    | `fr-CA` | Ses (20201015)<br>Metin<br>İşi|     Yes                      |
| Fransızca (Fransa)                    | `fr-FR` | Ses (20201015)<br>Metin<br>İşi|      Yes                     |
| Fransızca (İsviçre)               | `fr-CH` | Metin<br>İşi                  |                           |
| Almanca (Avusturya)                   | `de-AT` | Metin<br>İşi                  |                           |
| Almanca (Almanya)                   | `de-DE` | Ses (20190701, 20200619, 20201127)<br>Metin<br>İşi|  Yes                         |
| Yunanca (Yunanistan)                     | `el-GR` | Metin                                   |  Yes                         |
| Gucerat dili (Hindistan)                  | `gu-IN` | Metin                                   |                           |
| Hintçe (Hindistan)                      | `hi-IN` | Ses (20200701)<br>Metin                 |     Yes                      |
| Macarca (Macaristan)                | `hu-HU` | Metin                                   |                           |
| Endonezce (Endonezya)             | `id-ID` | Metin                                   |                           |
| İrlanda dili (Irlanda)                     | `ga-IE` | Metin                                   |                           |
| İtalyanca (İtalya)                    | `it-IT` | Ses (20201016)<br>Metin<br>İşi|      Yes                     |
| Japonca (Japonya)                   | `ja-JP` | Metin                                   |      Yes                     |
| Korece (Kore)                     | `ko-KR` | Ses (20201015)<br>Metin                 |      Yes                     |
| Letonca (Letonya)                   | `lv-LV` | Metin                                   |                           |
| Litvanca (Litvanya)             | `lt-LT` | Metin                                   |                           |
| Malayca (Malezya)                   | `ms-MY` | Metin                                   |                           |
| Malta dili (Malta)                    | `mt-MT` | Metin                                   |                           |
| Marathi dili (Hindistan)                    | `mr-IN` | Metin                                   |                           |
| Norveççe (Bokmål, Norveç)         | `nb-NO` | Metin                                   |     Yes                      |
| Lehçe (Polonya)                    | `pl-PL` | Metin                                   |       Yes                    |
| Portekizce (Brezilya)                | `pt-BR` | Ses (20190620, 20201015)<br>Metin<br>İşi|          Yes                 |
| Portekizce (Portekiz)              | `pt-PT` | Metin<br>İşi                  |             Yes              |
| Rumence (Romanya)                 | `ro-RO` | Metin                                   |  Yes                         |
| Rusça (Rusya)                   | `ru-RU` | Ses (20200907)<br>Metin                 |                Yes           |
| Slovakça (Slovakya)                  | `sk-SK` | Metin                                   |                           |
| Slovence (Slovenya)               | `sl-SI` | Metin                                   |                           |
| İspanyolca (Arjantin)                | `es-AR` | Metin<br>İşi                  |                           |
| İspanyolca (Bolivya)                  | `es-BO` | Metin<br>İşi                  |                           |
| İspanyolca (Şili)                    | `es-CL` | Metin<br>İşi                  |                           |
| İspanyolca (Kolombiya)                 | `es-CO` | Metin<br>İşi                  |                           |
| İspanyolca (Kosta Rika)               | `es-CR` | Metin<br>İşi                  |                           |
| İspanyolca (Küa)                     | `es-CU` | Metin<br>İşi                  |                           |
| İspanyolca (Dominik Cumhuriyeti)       | `es-DO` | Metin<br>İşi                  |                           |
| İspanyolca (Ekvador)                  | `es-EC` | Metin<br>İşi                  |                           |
| İspanyolca (El Salvador)              | `es-SV` | Metin<br>İşi                  |                           |
| İspanyolca (Ekvator Ginesi)        | `es-GQ` | Metin                                   |                           |
| İspanyolca (Guatemala)                | `es-GT` | Metin<br>İşi                  |                           |
| İspanyolca (Honduras)                 | `es-HN` | Metin<br>İşi                  |                           |
| İspanyolca (Meksika)                   | `es-MX` | Ses (20200907)<br>Metin<br>İşi|    Yes                       |
| İspanyolca (Nikaragua)                | `es-NI` | Metin<br>İşi                  |                           |
| İspanyolca (Panama)                   | `es-PA` | Metin<br>İşi                  |                           |
| İspanyolca (Paraguay)                 | `es-PY` | Metin<br>İşi                  |                           |
| İspanyolca (Peru)                     | `es-PE` | Metin<br>İşi                  |                           |
| İspanyolca (Porto Riko)              | `es-PR` | Metin<br>İşi                  |                           |
| İspanyolca (İspanya)                    | `es-ES` | Ses (20201015)<br>Metin<br>İşi|  Yes                         |
| İspanyolca (Uruguay)                  | `es-UY` | Metin<br>İşi                  |                           |
| İspanyolca (ABD)                      | `es-US` | Metin<br>İşi                  |                           |
| İspanyolca (Venezuela)                | `es-VE` | Metin<br>İşi                  |                           |
| İsveççe (İsviçre)                   | `sv-SE` | Metin                                   |   Yes                        |
| Tamil dili (Hindistan)                      | `ta-IN` | Metin                                   |                           |
| Telugu dili (Hindistan)                     | `te-IN` | Metin                                   |                           |
| Tayca (Tayland)                    | `th-TH` | Metin                                   |      Yes                     |
| Türkçe (Türkiye)                   | `tr-TR` | Metin                                   |                           |
| Vietnamca (Vietnam)               | `vi-VN` | Metin                                   |                           |

## <a name="text-to-speech"></a>Metin okuma

Hem Microsoft konuşma SDK 'Sı hem de REST API 'Leri, her biri yerel ayar ile tanımlanan belirli bir dili ve diyalekt destekleyen bu sesleri destekler. Ayrıca, [sesler/liste API 'si](rest-text-to-speech.md#get-a-list-of-voices)aracılığıyla her belirli bölge/uç nokta için desteklenen dillerin ve seslerin tam listesini de alabilirsiniz. 

> [!IMPORTANT]
> Fiyatlandırma, standart, özel ve sinir sesleri için farklılık gösterir. Daha fazla bilgi için lütfen [fiyatlandırma](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) sayfasını ziyaret edin.

### <a name="neural-voices"></a>Sinir sesleri

Sinir metin okuma, derin sinir ağlarla desteklenen yeni bir konuşma türü türüdür. Bir sinir sesi kullanırken, sentezlenmiş konuşma, insan kayıtlarından neredeyse ayırt edilemez.

Sinir sesleri, chatbots ve ses yardımcılarıyla daha doğal ve etkileyici bir şekilde etkileşim kurmak, e-kitaplar gibi dijital metinleri audiobooks 'a dönüştürmek ve oto içi gezinti sistemlerini geliştirmek için kullanılabilir. İnsan benzeri doğal Prosody ve sözcüklerin bir kısmını temizleyerek, sinir seslerde kullanıcılar AI sistemleriyle etkileşim kurarken büyük ölçüde dinleme performansını önemli ölçüde azaltır.

> [!NOTE]
> Sinir sesleri, 24 kHz örnek bir hız kullanan örneklerden oluşturulur.
> Tüm sesler, birleştirme sırasında diğer örnek hızlara eşit veya daha fazla örnek alabilir.


| Dil | Yerel Ayar | Cinsiyet | Ses adı | Stil desteği |
|---|---|---|---|---|
| Arapça (Mısır) | `ar-EG` | Kadın | `ar-EG-SalmaNeural` | Genel |
| Arapça (Mısır) | `ar-EG` | Erkek | `ar-EG-ShakirNeural` | Genel |
| Arapça (Suudi Arabistan) | `ar-SA` | Kadın | `ar-SA-ZariyahNeural` | Genel |
| Arapça (Suudi Arabistan) | `ar-SA` | Erkek | `ar-SA-HamedNeural` | Genel |
| Bulgarca (Bulgaristan) | `bg-BG` | Kadın | `bg-BG-KalinaNeural` | Genel |
| Bulgarca (Bulgaristan) | `bg-BG` | Erkek | `bg-BG-BorislavNeural` | Genel |
| Katalanca (İspanya) | `ca-ES` | Kadın | `ca-ES-AlbaNeural` | Genel |
| Katalanca (İspanya) | `ca-ES` | Kadın | `ca-ES-JoanaNeural` | Genel |
| Katalanca (İspanya) | `ca-ES` | Erkek | `ca-ES-EnricNeural` | Genel |
| Çince (Cantoneo, geleneksel) | `zh-HK` | Kadın | `zh-HK-HiuGaaiNeural` | Genel |
| Çince (Cantoneo, geleneksel) | `zh-HK` | Kadın | `zh-HK-HiuMaanNeural` | Genel |
| Çince (Cantoneo, geleneksel) | `zh-HK` | Erkek | `zh-HK-WanLungNeural` | Genel |
| Çince (Mandarin, Basitleştirilmiş) | `zh-CN` | Kadın | `zh-CN-XiaoxiaoNeural` | Genel, [SSML kullanarak](speech-synthesis-markup.md#adjust-speaking-styles) birden çok ses stili mevcuttur |
| Çince (Mandarin, Basitleştirilmiş) | `zh-CN` | Kadın | `zh-CN-XiaoyouNeural` | Öykü anlatımı için en iyi duruma getirilmiş alt ses |
| Çince (Mandarin, Basitleştirilmiş) | `zh-CN` | Erkek | `zh-CN-YunyangNeural` | Haberleri okuma için iyileştirilmiş,<br /> [SSML kullanarak](speech-synthesis-markup.md#adjust-speaking-styles) birden çok ses stili kullanılabilir |
| Çince (Mandarin, Basitleştirilmiş) | `zh-CN` | Erkek | `zh-CN-YunyeNeural` | Öykü anlatımı için iyileştirildi |
| Çince (Taiwanese Mandarin) | `zh-TW` | Kadın | `zh-TW-HsiaoChenNeural` | Genel |
| Çince (Taiwanese Mandarin) | `zh-TW` | Kadın | `zh-TW-HsiaoYuNeural` | Genel |
| Çince (Taiwanese Mandarin) | `zh-TW` | Erkek | `zh-TW-YunJheNeural` | Genel |
| Hırvatça (Hırvatistan) | `hr-HR` | Kadın | `hr-HR-GabrijelaNeural` | Genel |
| Hırvatça (Hırvatistan) | `hr-HR` | Erkek | `hr-HR-SreckoNeural` | Genel |
| Çekçe (Çek) | `cs-CZ` | Kadın | `cs-CZ-VlastaNeural` | Genel |
| Çekçe (Çek) | `cs-CZ` | Erkek | `cs-CZ-AntoninNeural` | Genel |
| Danca (Danimarka) | `da-DK` | Kadın | `da-DK-ChristelNeural` | Genel |
| Danca (Danimarka) | `da-DK` | Erkek | `da-DK-JeppeNeural` | Genel |
| Felemenkçe (Belçika) | `nl-BE` | Kadın | `nl-BE-DenaNeural` <sup>Yeni</sup> | Genel | 
| Felemenkçe (Belçika) | `nl-BE` | Erkek | `nl-BE-ArnaudNeural` <sup>Yeni</sup> | Genel | 
| Felemenkçe (Hollanda) | `nl-NL` | Kadın | `nl-NL-ColetteNeural` | Genel |
| Felemenkçe (Hollanda) | `nl-NL` | Kadın | `nl-NL-FennaNeural` | Genel |
| Felemenkçe (Hollanda) | `nl-NL` | Erkek | `nl-NL-MaartenNeural` | Genel |
| İngilizce (Avustralya) | `en-AU` | Kadın | `en-AU-NatashaNeural` | Genel |
| İngilizce (Avustralya) | `en-AU` | Erkek | `en-AU-WilliamNeural` | Genel |
| İngilizce (Kanada) | `en-CA` | Kadın | `en-CA-ClaraNeural` | Genel |
| İngilizce (Kanada) | `en-CA` | Erkek | `en-CA-LiamNeural` | Genel |
| İngilizce (Hindistan) | `en-IN` | Kadın | `en-IN-NeerjaNeural` | Genel |
| İngilizce (Hindistan) | `en-IN` | Erkek | `en-IN-PrabhatNeural` | Genel |
| İngilizce (İrlanda) | `en-IE` | Kadın | `en-IE-EmilyNeural` | Genel |
| İngilizce (İrlanda) | `en-IE` | Erkek | `en-IE-ConnorNeural` | Genel |
| İngilizce (Filipinler) | `en-PH` | Kadın | `en-PH-RosaNeural` <sup>Yeni</sup> | Genel | 
| İngilizce (Filipinler) | `en-PH` | Erkek | `en-PH-JamesNeural` <sup>Yeni</sup> | Genel | 
| İngilizce (İngiltere) | `en-GB` | Kadın | `en-GB-LibbyNeural` | Genel |
| İngilizce (İngiltere) | `en-GB` | Kadın | `en-GB-MiaNeural` | Genel |
| İngilizce (İngiltere) | `en-GB` | Erkek | `en-GB-RyanNeural` | Genel |
| İngilizce (ABD) | `en-US` | Kadın | `en-US-AriaNeural` | Genel, [SSML kullanarak](speech-synthesis-markup.md#adjust-speaking-styles) birden çok ses stili mevcuttur |
| İngilizce (ABD) | `en-US` | Kadın | `en-US-JennyNeural` | Genel |
| İngilizce (ABD) | `en-US` | Erkek | `en-US-GuyNeural` | Genel |
| Estonca (Estonya) | `et-EE` | Kadın | `et-EE-AnuNeural` | Genel |
| Estonca (Estonya) | `et-EE` | Erkek | `et-EE-KertNeural` | Genel |
| Fince (Finlandiya) | `fi-FI` | Kadın | `fi-FI-NooraNeural` | Genel |
| Fince (Finlandiya) | `fi-FI` | Kadın | `fi-FI-SelmaNeural` | Genel |
| Fince (Finlandiya) | `fi-FI` | Erkek | `fi-FI-HarriNeural` | Genel |
| Fransızca (Belçika) | `fr-BE` | Kadın | `fr-BE-CharlineNeural` <sup>Yeni</sup> | Genel | 
| Fransızca (Belçika) | `fr-BE` | Erkek | `fr-BE-GerardNeural` <sup>Yeni</sup> | Genel | 
| Fransızca (Kanada) | `fr-CA` | Kadın | `fr-CA-SylvieNeural` | Genel |
| Fransızca (Kanada) | `fr-CA` | Erkek | `fr-CA-AntoineNeural` | Genel |
| Fransızca (Kanada) | `fr-CA` | Erkek | `fr-CA-JeanNeural` | Genel |
| Fransızca (Fransa) | `fr-FR` | Kadın | `fr-FR-DeniseNeural` | Genel |
| Fransızca (Fransa) | `fr-FR` | Erkek | `fr-FR-HenriNeural` | Genel |
| Fransızca (İsviçre) | `fr-CH` | Kadın | `fr-CH-ArianeNeural` | Genel |
| Fransızca (İsviçre) | `fr-CH` | Erkek | `fr-CH-FabriceNeural` | Genel |
| Almanca (Avusturya) | `de-AT` | Kadın | `de-AT-IngridNeural` | Genel |
| Almanca (Avusturya) | `de-AT` | Erkek | `de-AT-JonasNeural` | Genel |
| Almanca (Almanya) | `de-DE` | Kadın | `de-DE-KatjaNeural` | Genel |
| Almanca (Almanya) | `de-DE` | Erkek | `de-DE-ConradNeural` | Genel |
| Almanca (İsviçre) | `de-CH` | Kadın | `de-CH-LeniNeural` | Genel |
| Almanca (İsviçre) | `de-CH` | Erkek | `de-CH-JanNeural` | Genel |
| Yunanca (Yunanistan) | `el-GR` | Kadın | `el-GR-AthinaNeural` | Genel |
| Yunanca (Yunanistan) | `el-GR` | Erkek | `el-GR-NestorasNeural` | Genel |
| İbranice (Israil) | `he-IL` | Kadın | `he-IL-HilaNeural` | Genel |
| İbranice (Israil) | `he-IL` | Erkek | `he-IL-AvriNeural` | Genel |
| Hintçe (Hindistan) | `hi-IN` | Kadın | `hi-IN-SwaraNeural` | Genel |
| Hintçe (Hindistan) | `hi-IN` | Erkek | `hi-IN-MadhurNeural` | Genel |
| Macarca (Macaristan) | `hu-HU` | Kadın | `hu-HU-NoemiNeural` | Genel |
| Macarca (Macaristan) | `hu-HU` | Erkek | `hu-HU-TamasNeural` | Genel |
| Endonezce (Endonezya) | `id-ID` | Kadın | `id-ID-GadisNeural` | Genel |
| Endonezce (Endonezya) | `id-ID` | Erkek | `id-ID-ArdiNeural` | Genel |
| İrlanda dili (Irlanda) | `ga-IE` | Kadın | `ga-IE-OrlaNeural` | Genel |
| İrlanda dili (Irlanda) | `ga-IE` | Erkek | `ga-IE-ColmNeural` | Genel |
| İtalyanca (İtalya) | `it-IT` | Kadın | `it-IT-ElsaNeural` | Genel |
| İtalyanca (İtalya) | `it-IT` | Kadın | `it-IT-IsabellaNeural` | Genel |
| İtalyanca (İtalya) | `it-IT` | Erkek | `it-IT-DiegoNeural` | Genel |
| Japonca (Japonya) | `ja-JP` | Kadın | `ja-JP-NanamiNeural` | Genel |
| Japonca (Japonya) | `ja-JP` | Erkek | `ja-JP-KeitaNeural` | Genel |
| Korece (Kore) | `ko-KR` | Kadın | `ko-KR-SunHiNeural` | Genel |
| Korece (Kore) | `ko-KR` | Erkek | `ko-KR-InJoonNeural` | Genel |
| Letonca (Letonya) | `lv-LV` | Kadın | `lv-LV-EveritaNeural` | Genel |
| Letonca (Letonya) | `lv-LV` | Erkek | `lv-LV-NilsNeural` | Genel |
| Litvanca (Litvanya) | `lt-LT` | Kadın | `lt-LT-OnaNeural` | Genel |
| Litvanca (Litvanya) | `lt-LT` | Erkek | `lt-LT-LeonasNeural` | Genel |
| Malayca (Malezya) | `ms-MY` | Kadın | `ms-MY-YasminNeural` | Genel |
| Malayca (Malezya) | `ms-MY` | Erkek | `ms-MY-OsmanNeural` | Genel |
| Malta dili (Malta) | `mt-MT` | Kadın | `mt-MT-GraceNeural` | Genel |
| Malta dili (Malta) | `mt-MT` | Erkek | `mt-MT-JosephNeural` | Genel |
| Norveççe (Bokmål, Norveç) | `nb-NO` | Kadın | `nb-NO-IselinNeural` | Genel |
| Norveççe (Bokmål, Norveç) | `nb-NO` | Kadın | `nb-NO-PernilleNeural` | Genel |
| Norveççe (Bokmål, Norveç) | `nb-NO` | Erkek | `nb-NO-FinnNeural` | Genel |
| Lehçe (Polonya) | `pl-PL` | Kadın | `pl-PL-AgnieszkaNeural` | Genel |
| Lehçe (Polonya) | `pl-PL` | Kadın | `pl-PL-ZofiaNeural` | Genel |
| Lehçe (Polonya) | `pl-PL` | Erkek | `pl-PL-MarekNeural` | Genel |
| Portekizce (Brezilya) | `pt-BR` | Kadın | `pt-BR-FranciscaNeural` | Genel, [SSML kullanarak](speech-synthesis-markup.md#adjust-speaking-styles) birden çok ses stili mevcuttur |
| Portekizce (Brezilya) | `pt-BR` | Erkek | `pt-BR-AntonioNeural` | Genel |
| Portekizce (Portekiz) | `pt-PT` | Kadın | `pt-PT-FernandaNeural` | Genel |
| Portekizce (Portekiz) | `pt-PT` | Kadın | `pt-PT-RaquelNeural` | Genel |
| Portekizce (Portekiz) | `pt-PT` | Erkek | `pt-PT-DuarteNeural` | Genel |
| Rumence (Romanya) | `ro-RO` | Kadın | `ro-RO-AlinaNeural` | Genel |
| Rumence (Romanya) | `ro-RO` | Erkek | `ro-RO-EmilNeural` | Genel |
| Rusça (Rusya) | `ru-RU` | Kadın | `ru-RU-DariyaNeural` | Genel |
| Rusça (Rusya) | `ru-RU` | Kadın | `ru-RU-SvetlanaNeural` | Genel |
| Rusça (Rusya) | `ru-RU` | Erkek | `ru-RU-DmitryNeural` | Genel |
| Slovakça (Slovakya) | `sk-SK` | Kadın | `sk-SK-ViktoriaNeural` | Genel |
| Slovakça (Slovakya) | `sk-SK` | Erkek | `sk-SK-LukasNeural` | Genel |
| Slovence (Slovenya) | `sl-SI` | Kadın | `sl-SI-PetraNeural` | Genel |
| Slovence (Slovenya) | `sl-SI` | Erkek | `sl-SI-RokNeural` | Genel |
| İspanyolca (Meksika) | `es-MX` | Kadın | `es-MX-DaliaNeural` | Genel |
| İspanyolca (Meksika) | `es-MX` | Erkek | `es-MX-JorgeNeural` | Genel |
| İspanyolca (İspanya) | `es-ES` | Kadın | `es-ES-ElviraNeural` | Genel |
| İspanyolca (İspanya) | `es-ES` | Erkek | `es-ES-AlvaroNeural` | Genel |
| İsveççe (İsviçre) | `sv-SE` | Kadın | `sv-SE-HilleviNeural` | Genel |
| İsveççe (İsviçre) | `sv-SE` | Kadın | `sv-SE-SofieNeural` | Genel |
| İsveççe (İsviçre) | `sv-SE` | Erkek | `sv-SE-MattiasNeural` | Genel |
| Tamil dili (Hindistan) | `ta-IN` | Kadın | `ta-IN-PallaviNeural` | Genel |
| Tamil dili (Hindistan) | `ta-IN` | Erkek | `ta-IN-ValluvarNeural` | Genel |
| Telugu dili (Hindistan) | `te-IN` | Kadın | `te-IN-ShrutiNeural` | Genel |
| Telugu dili (Hindistan) | `te-IN` | Erkek | `te-IN-MohanNeural` | Genel |
| Tayca (Tayland) | `th-TH` | Kadın | `th-TH-AcharaNeural` | Genel |
| Tayca (Tayland) | `th-TH` | Kadın | `th-TH-PremwadeeNeural` | Genel |
| Tayca (Tayland) | `th-TH` | Erkek | `th-TH-NiwatNeural` | Genel |
| Türkçe (Türkiye) | `tr-TR` | Kadın | `tr-TR-EmelNeural` | Genel |
| Türkçe (Türkiye) | `tr-TR` | Erkek | `tr-TR-AhmetNeural` | Genel |
| Ukraynaca (Ukrayna) | `uk-UA` | Kadın | `uk-UA-PolinaNeural` <sup>Yeni</sup> | Genel | 
| Ukraynaca (Ukrayna) | `uk-UA` | Erkek | `uk-UA-OstapNeural` <sup>Yeni</sup> | Genel | 
| Urduca (Pakistan) | `ur-PK` | Kadın | `ur-PK-UzmaNeural` <sup>Yeni</sup>  | Genel | 
| Urduca (Pakistan) | `ur-PK` | Erkek | `ur-PK-AsadNeural` <sup>Yeni</sup> | Genel | 
| Vietnamca (Vietnam) | `vi-VN` | Kadın | `vi-VN-HoaiMyNeural` | Genel |
| Vietnamca (Vietnam) | `vi-VN` | Erkek | `vi-VN-NamMinhNeural` | Genel |
| Gal Dili (UK) | `cy-GB` | Kadın | `cy-GB-NiaNeural` <sup>Yeni</sup> | Genel | 
| Gal Dili (UK) | `cy-GB` | Erkek | `cy-GB-AledNeural` <sup>Yeni</sup> | Genel | 

#### <a name="neural-voices-in-preview"></a>Önizlemede sinir sesler

Aşağıdaki sinir sesleri genel önizlemede. 

| Dil                         | Yerel Ayar  | Cinsiyet | Ses adı                             | Stil desteği |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| Çince (Mandarin, Basitleştirilmiş) | `zh-CN` | Kadın | `zh-CN-XiaohanNeural` | [SSML kullanarak](speech-synthesis-markup.md#adjust-speaking-styles) genel, birden çok stil kullanılabilir |
| Çince (Mandarin, Basitleştirilmiş) | `zh-CN` | Kadın | `zh-CN-XiaomoNeural` | [SSML kullanarak](speech-synthesis-markup.md#adjust-speaking-styles) genel, birden çok rol yürütme ve stil kullanılabilir |
| Çince (Mandarin, Basitleştirilmiş) | `zh-CN` | Kadın | `zh-CN-XiaoruiNeural` | [SSML kullanarak](speech-synthesis-markup.md#adjust-speaking-styles) üst düzey ses, birden çok stil kullanılabilir |
| Çince (Mandarin, Basitleştirilmiş) | `zh-CN` | Kadın | `zh-CN-XiaoxuanNeural` | [SSML kullanarak](speech-synthesis-markup.md#adjust-speaking-styles) genel, birden çok rol yürütme ve stil kullanılabilir |
| Çince (Mandarin, Basitleştirilmiş) | `zh-CN` | Erkek   | `zh-CN-YunxiNeural` | [SSML kullanarak](speech-synthesis-markup.md#adjust-speaking-styles) genel, birden çok stil kullanılabilir |

> [!IMPORTANT]
> Genel önizlemede bulunan sesler yalnızca 3 hizmet bölgesinde kullanılabilir: Doğu ABD, Batı Avrupa ve Güneydoğu Asya.

Bölgesel kullanılabilirlik hakkında daha fazla bilgi için bkz. [bölgeler](regions.md#neural-and-standard-voices).

Konuşma stilleri gibi sinir seslerini nasıl yapılandıracağınızı ve ayarlayabileceğinizi öğrenmek için bkz. [konuşma SensMe biçimlendirme dili](speech-synthesis-markup.md#adjust-speaking-styles).

> [!IMPORTANT]
> `en-US-JessaNeural`Ses olarak değiştirildi `en-US-AriaNeural` . Daha önce "Jessa" kullandıysanız, "Aria" öğesine dönüştürün.

> [!TIP]
> Konuşma sensimi isteklerinizin "Microsoft Server konuşma Metin Okuma sesi (en-US, AriaNeural)" gibi tam hizmet adı eşlemesini kullanmaya devam edebilirsiniz.

### <a name="standard-voices"></a>Standart sesler

75 ' den fazla standart ses, daha fazla 45 dilde ve yerel ayarlarda bulunabilir ve bu da metni sentezleştirilmiş konuşmaya dönüştürmenize olanak tanır. Bölgesel kullanılabilirlik hakkında daha fazla bilgi için bkz. [bölgeler](regions.md#neural-and-standard-voices).

> [!NOTE]
> İki özel durum ile, 16 kHz örnek oranı kullanan örneklerden standart sesler oluşturulur.
> **En-US-AriaRUS** ve **en-US-Guyrus** sesleriniz, 24 kHz örnek bir hız kullanan örneklerden de oluşturulmuştur.
> Tüm sesler, birleştirme sırasında diğer örnek hızlara eşit veya daha fazla örnek alabilir.

| Dil | Yerel ayar (BCP-47) | Cinsiyet | Ses adı |
|--|--|--|--|
| Arapça (Arapça) | `ar-EG` | Kadın | `ar-EG-Hoda`|
| Arapça (Suudi Arabistan) | `ar-SA` | Erkek | `ar-SA-Naayf`|
| Bulgarca (Bulgaristan) | `bg-BG` | Erkek | `bg-BG-Ivan`|
| Katalanca (İspanya) | `ca-ES` | Kadın | `ca-ES-HerenaRUS`|
| Çince (Cantoneo, geleneksel) | `zh-HK` | Erkek | `zh-HK-Danny`|
| Çince (Cantoneo, geleneksel) | `zh-HK` | Kadın | `zh-HK-TracyRUS`|
| Çince (Mandarin, Basitleştirilmiş) | `zh-CN` | Kadın | `zh-CN-HuihuiRUS`|
| Çince (Mandarin, Basitleştirilmiş) | `zh-CN` | Erkek | `zh-CN-Kangkang`|
| Çince (Mandarin, Basitleştirilmiş) | `zh-CN` | Kadın | `zh-CN-Yaoyao`|
| Çince (Taiwanese Mandarin) |  `zh-TW` | Kadın | `zh-TW-HanHanRUS`|
| Çince (Taiwanese Mandarin) |  `zh-TW` | Kadın | `zh-TW-Yating`|
| Çince (Taiwanese Mandarin) |  `zh-TW` | Erkek | `zh-TW-Zhiwei`|
| Hırvatça (Hırvatistan) | `hr-HR` | Erkek | `hr-HR-Matej`|
| Çekçe (Çek Cumhuriyeti) | `cs-CZ` | Erkek | `cs-CZ-Jakub`|
| Danca (Danimarka) | `da-DK` | Kadın | `da-DK-HelleRUS`|
| Felemenkçe (Hollanda) | `nl-NL` | Kadın | `nl-NL-HannaRUS`|
| İngilizce (Avustralya) | `en-AU` | Kadın | `en-AU-Catherine`|
| İngilizce (Avustralya) | `en-AU` | Kadın | `en-AU-HayleyRUS`|
| İngilizce (Kanada) | `en-CA` | Kadın | `en-CA-HeatherRUS`|
| İngilizce (Kanada) | `en-CA` | Kadın | `en-CA-Linda`|
| İngilizce (Hindistan) | `en-IN` | Kadın | `en-IN-Heera`|
| İngilizce (Hindistan) | `en-IN` | Kadın | `en-IN-PriyaRUS`|
| İngilizce (Hindistan) | `en-IN` | Erkek | `en-IN-Ravi`|
| İngilizce (İrlanda) | `en-IE` | Erkek | `en-IE-Sean`|
| İngilizce (İngiltere) | `en-GB` | Erkek | `en-GB-George`|
| İngilizce (İngiltere) | `en-GB` | Kadın | `en-GB-HazelRUS`|
| İngilizce (İngiltere) | `en-GB` | Kadın | `en-GB-Susan`|
| İngilizce (ABD) | `en-US` | Erkek | `en-US-BenjaminRUS`|
| İngilizce (ABD) | `en-US` | Erkek | `en-US-GuyRUS`|
| İngilizce (ABD) | `en-US` | Kadın | `en-US-AriaRUS`|
| İngilizce (ABD) | `en-US` | Kadın | `en-US-ZiraRUS`|
| Fince (Finlandiya) | `fi-FI` | Kadın | `fi-FI-HeidiRUS`|
| Fransızca (Kanada) | `fr-CA` | Kadın | `fr-CA-Caroline`|
| Fransızca (Kanada) | `fr-CA` | Kadın | `fr-CA-HarmonieRUS`|
| Fransızca (Fransa) | `fr-FR` | Kadın | `fr-FR-HortenseRUS`|
| Fransızca (Fransa) | `fr-FR` | Kadın | `fr-FR-Julie`|
| Fransızca (Fransa) | `fr-FR` | Erkek | `fr-FR-Paul`|
| Fransızca (İsviçre) | `fr-CH` | Erkek | `fr-CH-Guillaume`|
| Almanca (Avusturya) | `de-AT` | Erkek | `de-AT-Michael`|
| Almanca (Almanya) | `de-DE` | Kadın | `de-DE-HeddaRUS`|
| Almanca (Almanya) | `de-DE` | Erkek | `de-DE-Stefan`|
| Almanca (İsviçre) | `de-CH` | Erkek | `de-CH-Karsten`|
| Yunanca (Yunanistan) | `el-GR` | Erkek | `el-GR-Stefanos`|
| İbranice (Israil) | `he-IL` | Erkek | `he-IL-Asaf`|
| Hintçe (Hindistan) | `hi-IN` | Erkek | `hi-IN-Hemant`|
| Hintçe (Hindistan) | `hi-IN` | Kadın | `hi-IN-Kalpana`|
| Macarca (Macaristan) | `hu-HU` | Erkek | `hu-HU-Szabolcs`|
| Endonezce (Endonezya) | `id-ID` | Erkek | `id-ID-Andika`|
| İtalyanca (İtalya) | `it-IT` | Erkek | `it-IT-Cosimo`|
| İtalyanca (İtalya) | `it-IT` | Kadın | `it-IT-LuciaRUS`|
| Japonca (Japonya) | `ja-JP` | Kadın | `ja-JP-Ayumi`|
| Japonca (Japonya) | `ja-JP` | Kadın | `ja-JP-HarukaRUS`|
| Japonca (Japonya) | `ja-JP` | Erkek | `ja-JP-Ichiro`|
| Korece (Kore) | `ko-KR` | Kadın | `ko-KR-HeamiRUS`|
| Malayca (Malezya) | `ms-MY` | Erkek | `ms-MY-Rizwan`|
| Norveççe (Bokmål, Norveç) | `nb-NO` | Kadın | `nb-NO-HuldaRUS`|
| Lehçe (Polonya) | `pl-PL` | Kadın | `pl-PL-PaulinaRUS`|
| Portekizce (Brezilya) | `pt-BR` | Erkek | `pt-BR-Daniel`|
| Portekizce (Brezilya) | `pt-BR` | Kadın | `pt-BR-HeloisaRUS`|
| Portekizce (Portekiz) | `pt-PT` | Kadın | `pt-PT-HeliaRUS`|
| Rumence (Romanya) | `ro-RO` | Erkek | `ro-RO-Andrei`|
| Rusça (Rusya) | `ru-RU` | Kadın | `ru-RU-EkaterinaRUS`|
| Rusça (Rusya) | `ru-RU` | Kadın | `ru-RU-Irina`|
| Rusça (Rusya) | `ru-RU` | Erkek | `ru-RU-Pavel`|
| Slovakça (Slovakya) | `sk-SK` | Erkek | `sk-SK-Filip`|
| Slovence (Slovenya) | `sl-SI` | Erkek | `sl-SI-Lado`|
| İspanyolca (Meksika) | `es-MX` | Kadın | `es-MX-HildaRUS`|
| İspanyolca (Meksika) | `es-MX` | Erkek | `es-MX-Raul`|
| İspanyolca (İspanya) | `es-ES` | Kadın | `es-ES-HelenaRUS`|
| İspanyolca (İspanya) | `es-ES` | Kadın | `es-ES-Laura`|
| İspanyolca (İspanya) | `es-ES` | Erkek | `es-ES-Pablo`|
| İsveççe (İsviçre) | `sv-SE` | Kadın | `sv-SE-HedvigRUS`|
| Tamil dili (Hindistan) | `ta-IN` | Erkek | `ta-IN-Valluvar`|
| Telugu dili (Hindistan) | `te-IN` | Kadın | `te-IN-Chitra`|
| Tayca (Tayland) | `th-TH` | Erkek | `th-TH-Pattara`|
| Türkçe (Türkiye) | `tr-TR` | Kadın | `tr-TR-SedaRUS`|
| Vietnamca (Vietnam) | `vi-VN` | Erkek | `vi-VN-An` |

> [!IMPORTANT]
> `en-US-Jessa`Ses olarak değiştirildi `en-US-Aria` . Daha önce "Jessa" kullandıysanız, "Aria" öğesine dönüştürün.

> [!TIP]
> Konuşma birleştirme isteklerinizin "Microsoft Server konuşma Metin Okuma sesi (en-US, AriaRUS)" gibi tam hizmet adı eşlemesini kullanmaya devam edebilirsiniz.

### <a name="customization"></a>Özelleştirme

Özel ses standart ve sinir katmanında kullanılabilir. Desteklenen diller bu iki katmanda farklıdır. 

| Dil | Yerel Ayar | Standart | Sinir |
|--|--|--|--|
| Çince (Mandarin, Basitleştirilmiş) | `zh-CN` | Yes | Yes |
| Çince (Mandarin, Basitleştirilmiş), Ingilizce iki dilli | `zh-CN` iki dilli | Yes | Yes |
| İngilizce (Avustralya) | `en-AU` | Hayır | Yes |
| İngilizce (Hindistan) | `en-IN` | Yes | Yes |
| İngilizce (İngiltere) | `en-GB` | Yes | Yes |
| İngilizce (ABD) | `en-US` | Yes | Yes |
| Fransızca (Kanada) | `fr-CA` | Hayır | Yes |
| Fransızca (Fransa) | `fr-FR` | Yes | Yes |
| Almanca (Almanya) | `de-DE` | Yes | Yes |
| İtalyanca (İtalya) | `it-IT` | Yes | Yes |
| Japonca (Japonya) | `ja-JP` | Hayır | Yes |
| Korece (Kore) | `ko-KR` | Hayır | Yes |
| Portekizce (Brezilya) | `pt-BR` | Yes | Yes |
| İspanyolca (Meksika) | `es-MX` | Yes | Yes |
| İspanyolca (İspanya) | `es-ES` | Hayır | Yes |

Özel bir ses modeli eğitmeniz gereken eğitim verileriyle eşleşen doğru yerel ayarı seçin. Örneğin, kullandığınız kayıt verileri Ingiliz alfabesindeki bir vurgu ile konuşulursam, seçeneğini belirleyin `en-GB` .

> [!NOTE]
> Chinese-English bı-dilli dışında, özel seste bı-dilli model eğitimini desteklemiyoruz. Ingilizce 'ye de konuşarak bir Çince ses alıştırması yapmak istiyorsanız "Çince-Ingilizce çift dilli" seçeneğini belirleyin. Standart yöntemi kullanan Chinese-English iki dilli model eğitimi yalnızca Kuzey Avrupa ve Orta Kuzey ABD bulunabilir. Özel sinir ses eğitimi UK Güney ve Doğu ABD kullanılabilir.

## <a name="speech-translation"></a>Konuşma çevirisi

**Konuşma çevirisi** API 'si, konuşmaya konuşmaya ve konuşmadan metne dönüştürme için farklı dilleri destekler. Kaynak dilin her zaman konuşma-metin dili tablosundan olması gerekir. Kullanılabilir hedef diller, çeviri hedefinin konuşma veya metin olmasına bağlıdır. Gelen konuşmayı 60 ' den fazla [dile](https://www.microsoft.com/translator/business/languages/)çevirebilirsiniz. [Konuşma birleştirme](language-support.md#text-languages)için bir dil alt kümesi mevcuttur.

### <a name="text-languages"></a>Metin dilleri

| Metin dili           | Dil kodu |
|:------------------------|:-------------:|
| Afrikaner               | `af`          |
| Arapça                  | `ar`          |
| Bangla                  | `bn`          |
| Boşnakça (Latin)         | `bs`          |
| Bulgarca               | `bg`          |
| Cantonetıcı (Geleneksel) | `yue`         |
| Katalanca                 | `ca`          |
| Basitleştirilmiş Çince      | `zh-Hans`     |
| Geleneksel Çince     | `zh-Hant`     |
| Hırvatça                | `hr`          |
| Çekçe                   | `cs`          |
| Danca                  | `da`          |
| Felemenkçe                   | `nl`          |
| İngilizce                 | `en`          |
| Estonya Dili                | `et`          |
| Fiji Adaları dili                  | `fj`          |
| Filipino                | `fil`         |
| Fince                 | `fi`          |
| Fransızca                  | `fr`          |
| Almanca                  | `de`          |
| Yunanca                   | `el`          |
| Gucerat dili                | `gu`          |
| Haian Creole          | `ht`          |
| İbranice                  | `he`          |
| Hintçe                   | `hi`          |
| Hmong DAW               | `mww`         |
| Macarca               | `hu`          |
| Endonezce              | `id`          |
| İrlandaca                   | `ga`          |
| İtalyanca                 | `it`          |
| Japonca                | `ja`          |
| Kannada dili                 | `kn`          |
| Svahili dili               | `sw`          |
| Klingon                 | `tlh-Latn`    |
| Klingon (plqaD)         | `tlh-Piqd`    |
| Korece                  | `ko`          |
| Letonca                 | `lv`          |
| Litvanca              | `lt`          |
| Malgaşça                | `mg`          |
| Malayca                   | `ms`          |
| Malayalam dili               | `ml`          |
| Maltaca                 | `mt`          |
| Maori dili                   | `mi`          |
| Marathi                 | `mr`          |
| Norveççe               | `nb`          |
| Farsça                 | `fa`          |
| Lehçe                  | `pl`          |
| Portekizce (Brezilya)     | `pt-br`       |
| Portekizce (Portekiz)   | `pt-pt`       |
| Pencap dili                 | `pa`          |
| Queretaro Otomi         | `otq`         |
| Rumence                | `ro`          |
| Rusça                 | `ru`          |
| Samoan                  | `sm`          |
| Sırpça (Kiril)      | `sr-Cyrl`     |
| Sırpça (Latin)         | `sr-Latn`     |
| Slovakça                  | `sk`          |
| Slovence               | `sl`          |
| İspanyolca                 | `es`          |
| İsveççe                 | `sv`          |
| Tahiti dili                | `ty`          |
| Tamil dili                   | `ta`          |
| Telugu dili                  | `te`          |
| Tayca                    | `th`          |
| Tonga dili                  | `to`          |
| Türkçe                 | `tr`          |
| Ukraynaca               | `uk`          |
| Urduca                    | `ur`          |
| Vietnamca              | `vi`          |
| Galce                   | `cy`          |
| Yucatec Maya            | `yua`         |

## <a name="speaker-recognition"></a>Konuşmacı Tanıma

Çeşitli konuşmacı tanıma API 'Leri için desteklenen diller için aşağıdaki tabloya bakın. Konuşmacı Tanıma hakkında daha fazla bilgi için bkz. [genel bakış](speaker-recognition-overview.md) .

| Dil | Yerel ayar (BCP-47) | Metne bağımlı doğrulama | Metnin bağımsız doğrulaması | Metnin bağımsız tanımlayıcısı |
|----|----|----|----|----|
|İngilizce (ABD)  |  en-US  |  evet  |  evet  |  evet |
|Çince (Mandarin, Basitleştirilmiş) | zh-CN     |     yok |     evet |     evet|
|İngilizce (Avustralya)     | En-AU     | yok     | evet     | evet|
|İngilizce (Kanada)     | en-CA     | yok |     evet |     evet|
|İngilizce (UK)     | en-GB     | yok     | evet     | evet|
|Fransızca (Kanada)     | fr-CA     | yok     | evet |     evet|
|Fransızca (Fransa)     | fr-FR     | yok     | evet     | evet|
|Almanca (Almanya)     | de-DE     | yok     | evet     | evet|
|İtalyanca | it-IT     |     yok     | evet |     evet|
|Japonca     | ja-JP | yok     | evet     | evet|
|Portekizce (Brezilya) | pt-BR |     yok |     evet |     evet|
|İspanyolca (Meksika)     | es-MX     | yok |     evet |     evet|
|İspanyolca (İspanya)     | es-ES | yok     | evet |     evet|

## <a name="next-steps"></a>Sonraki adımlar

* [Ücretsiz bir Azure hesabı oluşturma](https://azure.microsoft.com/free/cognitive-services/)
* [Bkz. C 'de konuşmayı tanıma #](./get-started-speech-to-text.md?pivots=programming-language-chsarp)
