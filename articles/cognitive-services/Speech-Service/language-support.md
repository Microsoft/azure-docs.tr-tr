---
title: Dil desteği-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma hizmeti, konuşmadan metne ve metinden konuşmaya dönüştürmeye, konuşma çevirisi ile birlikte çok sayıda dili destekler. Bu makale, hizmet özelliğine göre dil desteğinin kapsamlı bir listesini sağlar.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 26cab7ba3ed864382ae5511755fee09c3826702c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580175"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Konuşma Hizmetleri için dil ve bölge desteği

Farklı diller farklı konuşma Hizmetleri işlevleri için desteklenir. Aşağıdaki tablolar dil desteğini özetler.

## <a name="speech-to-text"></a>Konuşmayı metne dönüştürme

Hem Microsoft konuşma SDK 'Sı hem de REST API aşağıdaki dilleri (yerel ayarlar) destekler. Doğruluğu artırmak için, bir dilin bir alt kümesi için, ses + ınsan etiketli yazılı döküm veya Ilgili metin: cümleler karşıya yüklenirken özelleştirme sunulur.  Telaffuz özelleştirmesi Şu anda yalnızca en-US ve de de vardır. [Özelleştirme hakkında](how-to-custom-speech.md)daha fazla bilgi edinin.

  Yerel Ayar | Dil | Destekleniyor | Özelleştirilebilir | Kapsayıcı Desteği
 ------|------------|-----------|--------------|--------------
 ar-EG | Arapça (Mısır), modern standart | ✔️ | ✔️ | ✔️
 ar-SA | Arapça (Suudi Arabistan) | ✔️ | ✔️ | ❌
 AR-AE | Arapça (UAE) | ✔️ | ✔️ | ❌
 AR-KW | Arapça (Kuveyt) | ✔️ | ✔️ | ❌
 AR-QA | Arapça (Qtor) | ✔️ | ✔️ | ❌
 CA-ES | Katalanca | ✔️ | ❌ | ✔️
 da-DK | Danca (Danimarka) | ✔️ | ❌ | ✔️
 de-DE | Almanca (Almanya) | ✔️ | ✔️ | ✔️
 En-AU | İngilizce (Avustralya) | ✔️ | ✔️ | ✔️
 en-CA | İngilizce (Kanada) | ✔️ | ✔️ | ✔️
 en-GB | İngilizce (Birleşik Krallık) | ✔️ | ✔️ | ✔️
 En-ın | English (India) | ✔️ | ✔️ | ✔️
 En-NZ | İngilizce (Yeni Zelanda) | ✔️ | ✔️ | ✔️
 en-US | İngilizce (Birleşik Devletler) | ✔️ | ✔️ | ✔️
 ES-ES | İspanyolca (Ispanya) | ✔️ | ✔️ | ✔️
 es-MX | İspanyolca (Meksika) | ✔️ | ✔️ | ✔️
 Fi-FI | Fince (Finlandiya) | ✔️ | ❌ | ✔️
 fr-CA | Fransızca (Kanada) | ✔️ | ✔️ | ✔️
 fr-FR | Fransızca (Fransa) | ✔️ | ✔️ | ✔️
 Gu-ın | Gucerat dili (Hindistan) | ✔️ | ✔️ | ❌
 Merhaba | Hintçe (Hindistan) | ✔️ | ✔️ | ✔️
 BT BT | İtalyanca (Italya) | ✔️ | ✔️ | ✔️
 ja-JP | Japonca (Japonya) | ✔️ | ✔️ | ✔️
 ko-KR | Kore dili (Kore) | ✔️ | ✔️ | ✔️
 Mr-ın | Marathi dili (Hindistan) | ✔️ | ✔️ | ❌
 NB-hayır | Norveççe (Bokmål) (Norveç) | ✔️ | ❌ | ✔️
 NL-NL | Hollanda dili (Hollanda) | ✔️ | ✔️ | ✔️
 PL-PL | Lehçe (Polonya) | ✔️ | ❌ | ✔️
 PT-BR | Portekizce (Brezilya) | ✔️ | ✔️ | ✔️
 PT NK | Portekizce (Portekiz) | ✔️ | ✔️ | ✔️
 ru-RU | Rusça (Rusya) | ✔️ | ✔️ | ✔️
 ZF-s | İsveççe (Isveç) | ✔️ | ❌ | ✔️
 ta-ın | Tamil dili (Hindistan) | ✔️ | ✔️ | ❌
 te | Telugu dili (Hindistan) | ✔️ | ✔️ | ❌
 zh-CN | Çince (Mandarin, Basitleştirilmiş) | ✔️ | ✔️ | ✔️
 zh-HK | Çince (Cantoneo, geleneksel) | ✔️ | ✔️ | ✔️
 zh-TW | Çince (Taiwanese Mandarin) | ✔️ | ✔️ | ✔️
 TH-TH | Tay dili (Tayland) | ✔️ | ❌ | ✔️
 tr-TR | Türkiye | ✔️ | ✔️ | ❌


## <a name="text-to-speech"></a>Metin okuma

Hem Microsoft konuşma SDK 'Sı hem de REST API, her biri yerel ayar ile tanımlanan belirli bir dili ve diyalekt destekleyen bu sesleri destekler.

> [!IMPORTANT]
> Fiyatlandırma, standart, özel ve sinir sesleri için farklılık gösterir. Daha fazla bilgi için lütfen [fiyatlandırma](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) sayfasını ziyaret edin.

### <a name="neural-voices"></a>Sinir sesleri

Sinir metin okuma, derin sinir ağlarla desteklenen yeni bir konuşma türü türüdür. Bir sinir sesi kullanırken, sentezlenmiş konuşma, insan kayıtlarından neredeyse ayırt edilemez.

Sinir sesleri, chatbots ve ses yardımcılarıyla daha doğal ve etkileyici bir şekilde etkileşim kurmak, e-kitaplar gibi dijital metinleri audiobooks 'a dönüştürmek ve oto içi gezinti sistemlerini geliştirmek için kullanılabilir. İnsan benzeri doğal Prosody ve sözcüklerin bir kısmını temizleyerek, sinir seslerde kullanıcılar AI sistemleriyle etkileşim kurarken büyük ölçüde dinleme performansını önemli ölçüde azaltır.

Sinir seslerine ve bölgesel kullanılabilirliğe ait tam bir liste için bkz. [bölgeler](regions.md#standard-and-neural-voices).

Yerel Ayar | Dil | Cinsiyet | Tam hizmet adı eşleme | Kısa ses adı
--------|----------|--------|---------|------------
de-DE | Almanca (Almanya) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (de-DE, KatjaNeural)" | "de-KatjaNeural"
en-US | English (US) | Erkek | "Microsoft Server konuşma Metin Okuma sesi (en-US, GuyNeural)" | "en-US-GuyNeural"
en-US | English (US) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (en-US, JessaNeural)" | "en-US-JessaNeural"
BT BT | İtalyanca (Italya) | Kadın |"Microsoft Server konuşma Metin Okuma sesi (It-IT, ElsaNeural)" | "It-IT-ElsaNeural"
zh-CN | Çince (Mainland) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> Konuşma senssıs isteklerinizin tam hizmet adı eşlemesini veya kısa ses adını kullanabilirsiniz.

### <a name="standard-voices"></a>Standart sesler

75 ' den fazla standart ses, daha fazla 45 dilde ve yerel ayarlarda bulunabilir ve bu da metni sentezleştirilmiş konuşmaya dönüştürmenize olanak tanır. Bölgesel kullanılabilirlik hakkında daha fazla bilgi için bkz. [bölgeler](regions.md#standard-and-neural-voices).

Yerel Ayar | Dil | Cinsiyet | Tam hizmet adı eşleme | Kısa ad | Kapsayıcı desteği
-------|----------|---------|----------|----------|------
AR-örn.\* | Arapça (Mısır) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (ar-EG, Hoda)" | "ar-EG-Hoda" | ✔️
ar-SA | Arapça (Suudi Arabistan) | Erkek | "Microsoft Server konuşma Metin Okuma sesi (ar-SA, Naayf)" | "ar-SA-Naayf" | ✔️
BG-BG | Bulgarca | Erkek | "Microsoft Server konuşma Metin Okuma sesi (BG-BG, Ivan)" | "BG-BG-Ivan" | ✔️
CA-ES | Katalanca (Ispanya) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (CA-ES, HerenaRUS)" | "CA-ES-HerenaRUS" | ✔️
CS-CZ | Çekçe | Erkek | "Microsoft Server konuşma Metin Okuma sesi (CS-CZ, Jakub)" | "CS-CZ-Jakub" | ✔️
da-DK | Danca | Kadın | "Microsoft Server konuşma Metin Okuma sesi (da-DK, HelleRUS)" | "da-DK-HelleRUS" | ✔️
de | Almanca (Avusturya) | Erkek | "Microsoft Server konuşma Metin Okuma sesi (de-AT, Michael)" | "on-Michael" | ✔️
devre dışı | Almanca (Isviçre) | Erkek | "Microsoft Server konuşma Metin Okuma sesi (de CH, Karsten)" | "de CH-Karsten" | ✔️
de-DE | Almanca (Almanya) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (de-DE, Hedda)" | "de-DE-Hedda" | ❌
| | | Kadın | "Microsoft Server konuşma Metin Okuma sesi (de-DE, HeddaRUS)" | "de, HeddaRUS" | ✔️
| | | Erkek | "Microsoft Server konuşma Metin Okuma sesi (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo" | ✔️
El-GR | Yunanca | Erkek | "Microsoft Server konuşma Metin Okuma sesi (el-GR, Stefanos)" | "el-GR-Stefanos" | ✔️
En-AU | İngilizce (Avustralya) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (en-AU, Catherine)" | "en-AU-Catherine" | ✔️
| | | Kadın | "Microsoft Server konuşma Metin Okuma sesi (en-AU, HayleyRUS)" | "en-AU-HayleyRUS" | ✔️
en-CA | İngilizce (Kanada) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (en-CA, Linda)" | "en-CA-Linda" | ✔️
| | | Kadın | "Microsoft Server konuşma Metin Okuma sesi (en-CA, Centherru)" | "en-CA-Centherrus" | ✔️
en-GB | English (UK) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (en-GB, Çiğdem, Apollo)" | "en-GB-çiğdem-Apollo" | ✔️
| | | Kadın | "Microsoft Server konuşma Metin Okuma sesi (en-GB, tehlikeli Elrus)" | "en-GB-HazelRUS" | ✔️
| | | Erkek | "Microsoft Server konuşma Metin Okuma sesi (en-GB, George, Apollo)" | "en-GB-George-Apollo" | ✔️
en-IE | İngilizce (Irlanda) | Erkek | "Microsoft Server konuşma Metin Okuma sesi (en-IE, kemal)" | "en-IE-kemal" | ✔️
En-ın | English (India) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (en-ın, Heera, Apollo)" | "en-ın-heçi-Apollo" | ✔️
| | | Kadın | "Microsoft Server konuşma Metin Okuma sesi (en-ın, PriyaRUS)" | "en-basit Yarus" | ✔️
| | | Erkek | "Microsoft Server konuşma Metin Okuma sesi (en-ın, Ravi, Apollo)" | "en-ın-Ravi-Apollo" | ✔️
en-US | English (US) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (en-US, ZiraRUS)" | "en-US-ZiraRUS" | ✔️
| | | Kadın | "Microsoft Server konuşma Metin Okuma sesi (en-US, JessaRUS)" | "en-US-JessaRUS" | ✔️
| | | Erkek | "Microsoft Server konuşma Metin Okuma sesi (en-US, BenjaminRUS)" | "en-US-BenjaminRUS" | ✔️
| | | Kadın | "Microsoft Server konuşma Metin Okuma sesi (en-US, Jessa24kRUS)" | "en-US-Jessa24kRUS" | ✔️
| | | Erkek | "Microsoft Server konuşma Metin Okuma sesi (en-US, Guy24kRUS)" | "en-US-Guy24kRUS" | ✔️
ES-ES | İspanyolca (Ispanya) |Kadın | "Microsoft Server konuşma Metin Okuma sesi (ES-ES, Gamze, Apollo)" | "ES-ES-gamze-Apollo" | ✔️
| | | Kadın | "Microsoft Server konuşma Metin Okuma sesi (ES-ES, HelenaRUS)" | "ES-ES-HelenaRUS" | ✔️
| | | Erkek | "Microsoft Server konuşma Metin Okuma sesi (ES-ES, Pablo, Apollo)" | "ES-ES-Pablo-Apollo" | ✔️
es-MX | İspanyolca (Meksika) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (es-MX, Tepdarus)" | "es-MX-Tepdarus" | ✔️
| | | Erkek | "Microsoft Server konuşma Metin Okuma sesi (es-MX, Rayul, Apollo)" | "es-MX-OYUL-Apollo" | ✔️
Fi-FI | Fince | Kadın | "Microsoft Server konuşma Metin Okuma sesi (FI-FI, Heidrus)" | "fi-FI-Heidrus" | ✔️
fr-CA | Fransızca (Kanada) |Kadın | "Microsoft Server konuşma Metin Okuma sesi (fr-CA, Caroline)" | "fr-CA-Caroline" | ✔️
| | | Kadın | "Microsoft Server konuşma Metin Okuma sesi (fr-CA, Harmonitorerus)" | "fr-CA-Harmonitorerus" | ✔️
fr-CH | Fransızca (Isviçre)| Erkek | "Microsoft Server konuşma Metin Okuma sesi (fr-CH, Guldefme)" | "fr-CH-Guildefme" | ✔️
fr-FR | Fransızca (Fransa)| Kadın | "Microsoft Server konuşma Metin Okuma sesi (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo" | ✔️
| | | Kadın | "Microsoft Server konuşma Metin Okuma sesi (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS" | ✔️
| | | Erkek | "Microsoft Server konuşma Metin Okuma sesi (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo" | ✔️
BT-Il| İbranice (Israil) | Erkek| "Microsoft Server konuşma Metin Okuma sesi (he-Il, Asaf)" | "he-Il-asaf" | ✔️
Merhaba | Hintçe (Hindistan) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (Hi-ın, Kalpana, Apollo)" | "HI-IN-Kalpana-Apollo" | ✔️
| | |Kadın | "Microsoft Server konuşma Metin Okuma sesi (Hi-ın, Kalpana)" | "Hi Pana" | ✔️
| | | Erkek | "Microsoft Server konuşma Metin Okuma sesi (Hi-IN, Hemant)" | "Hi-IN-Hemant" | ✔️
HR-SA | Hırvatça | Erkek | "Microsoft Server konuşma Metin Okuma sesi (HR-HR, Matej)" | "HR-HR-Matej" | ✔️
HU-HU | Macarca | Erkek | "Microsoft Server konuşma Metin Okuma sesi (HU-HU, Szacıvacs)" | "HU-HU-Szacıvacs" | ✔️
kimlik KIMLIĞI | Endonezya dili| Erkek | "Microsoft Server konuşma Metin Okuma sesi (kimlik KIMLIĞI, Andika)" | "kimlik-KIMLIĞI-Andika" | ✔️
BT BT | İtalyanca | Erkek | "Microsoft Server konuşma Metin Okuma sesi (It-IT, Cosımo, Apollo)" | "It-IT-Cosımo-Apollo" | ✔️
| | | Kadın | "Microsoft Server konuşma Metin Okuma sesi (It-IT, LuciaRUS)" | "It-IT-LuciaRUS" | ✔️
ja-JP | Japonca | Kadın | "Microsoft Server konuşma Metin Okuma sesi (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo" | ✔️
| | | Erkek | "Microsoft Server konuşma Metin Okuma sesi (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo" | ✔️
| | | Kadın | "Microsoft Server konuşma Metin Okuma sesi (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS" | ✔️
ko-KR | Korece | Kadın | "Microsoft Server konuşma Metin Okuma sesi (ko-KR, Heamırus)" | "ko-KR-Heamırus" | ✔️
MS-MY | Malay dili | Erkek | "Microsoft Server konuşma Metin Okuma sesi (MS-MY, Rizwan)" | "MS-MY-Rizwan" | ✔️
NB-hayır | Norveççe | Kadın | "Microsoft Server konuşma Metin Okuma sesi (NB-NO, HuldaRUS)" | "NB-hayır-HuldaRUS" | ✔️
NL-NL | Hollanda dili | Kadın | "Microsoft Server konuşma Metin Okuma sesi (nl-NL, HannaRUS)" | "nl-NL-HannaRUS" | ✔️
PL-PL | Lehçe | Kadın | "Microsoft Server konuşma Metin Okuma sesi (PL-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS" | ✔️
PT-BR | Portekizce (Brezilya) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (PT-BR, HeloisaRUS)" | "PT-BR-HeloisaRUS" | ✔️
| | | Erkek |"Microsoft Server konuşma Metin Okuma sesi (PT-BR, Daniel, Apollo)" | "PT-BR-Daniel-Apollo" | ✔️
PT NK | Portekizce (Portekiz) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (pt-PT, HeliaRUS)" | "PT-PT Helinarus" | ✔️
RO-RO | Rumence | Erkek | "Microsoft Server konuşma Metin Okuma sesi (RO-RO, ANDREI)" | "RO-RO-Andrei" | ✔️
ru-RU |Rusça| Kadın | "Microsoft Server konuşma Metin Okuma sesi (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo" | ✔️
| | | Erkek | "Microsoft Server konuşma Metin Okuma sesi (ru-RU, Pabol, Apollo)" | "ru-RU-Palevel-Apollo" | ✔️
| | | Kadın | "Microsoft Server konuşma Metin Okuma sesi (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS | ✔️
SK-SK | Slovakça | Erkek | "Microsoft Server konuşma Metin Okuma sesi (SK-SK, Filıp)" | "SK-SK-Filıp" | ✔️
SL-SI | Slovence | Erkek | "Microsoft Server konuşma Metin Okuma sesi (SL-sı, Lado)" | "SL-SI-Lado" | ✔️
ZF-s | İsveççe | Kadın | "Microsoft Server konuşma Metin Okuma sesi (ZF-o, HedvigRUS)" | "ZF-i-HedvigRUS" | ✔️
ta-ın | Tamil dili (Hindistan) | Erkek | "Microsoft Server konuşma Metin Okuma sesi (ta-ın, Valluvar)" | "ta-Valluvar" | ✔️
te | Telugu dili (Hindistan) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (te-ın, Chitra)" | "te-Chitra" | ✔️
TH-TH | Tay dili | Erkek | "Microsoft Server konuşma Metin Okuma sesi (TH, Pattara)" | "TH-Pattara" | ✔️
tr-TR | Türkçe | Kadın | "Microsoft Server konuşma Metin Okuma sesi (tr-TR, SedaRUS)" | "tr-TR-SedaRUS" | ✔️
vi-VN | Vietnam dili | Erkek | "Microsoft Server konuşma Metin Okuma sesi (VN, a)" | "VI-VN-a" | ✔️
zh-CN | Çince (Mainland) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (zh-CN, Huihuırus)" | "zh-CN-Huihuırus" | ✔️
| | | Kadın | "Microsoft Server konuşma Metin Okuma sesi (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo" | ✔️
| | | Erkek | "Microsoft Server konuşma Metin Okuma sesi (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo" | ✔️
zh-HK | Çince (Hong Kong) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo" | ✔️
| | | Kadın | "Microsoft Server konuşma Metin Okuma sesi (zh-HK, Tracyıru)" | "zh-HK-Tracyırus" | ✔️
| | | Erkek | "Microsoft Server konuşma Metin Okuma sesi (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo" | ✔️
zh-TW | Çince (Tayvan) | Kadın | "Microsoft Server konuşma Metin Okuma sesi (zh-TW, Yadırıcı, Apollo)" | "zh-TW-Yating-Apollo" | ✔️
| | | Kadın | "Microsoft Server konuşma Metin Okuma sesi (zh-TW, Hanhanru)" | "zh-TW-HanHanRUS" | ✔️
| | | Erkek | "Microsoft Server konuşma Metin Okuma sesi (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo" | ✔️

\* *ar-örneğin modern Standart Arapça (MSA) desteği.*

> [!NOTE]
> Konuşma senssıs isteklerinizin tam hizmet adı eşlemesini veya kısa ses adını kullanabilirsiniz.

### <a name="customization"></a>Özelleştirme

Ses özelleştirmesi, de-de, en-GB, en-ın, en-US, es-MX, fr-FR, It-IT, PT-BR ve zh-CN için kullanılabilir. Özel bir ses modeli eğitmeniz gereken eğitim verileriyle eşleşen doğru yerel ayarı seçin. Örneğin, kullandığınız kayıt verileri Ingiliz alfabesindeki bir vurgu ile konuşululursam, en-GB ' y i seçin.  

> [!NOTE]
> Çince-Ingilizce bı-dil dışında, özel seste bı-dilli model eğitimini desteklemiyoruz. Ingilizce 'yi de okuyabileceği bir Çince ses alıştırması yapmak istiyorsanız ' Çince-Ingilizce çift dilli ' seçeneğini belirleyin. Tüm yerel ayarlarda bulunan sesli eğitim, her türlü eğitim verisi ile başlayabileceğiniz en-US ve zh-CN dışında bir 2000 ' in veri kümesiyle başlar.

## <a name="speech-translation"></a>Konuşma çevirisi

**Konuşma çevirisi** API 'si, konuşmaya konuşmaya ve konuşmadan metne dönüştürme için farklı dilleri destekler. Kaynak dilin her zaman konuşma-metin dili tablosundan olması gerekir. Kullanılabilir hedef diller, çeviri hedefinin konuşma veya metin olmasına bağlıdır. Gelen konuşmayı 60 ' den fazla [dile](https://www.microsoft.com/translator/business/languages/)çevirebilirsiniz. [Konuşma birleştirme](language-support.md#text-languages)için bu dillerin bir alt kümesi mevcuttur.

### <a name="text-languages"></a>Metin dilleri

| Metin dili    | Dil kodu |
|:----------- |:-------------:|
| Afrikaner      | `af`          |
| Arapça       | `ar`          |
| Bangla      | `bn`          |
| Boşnakça (Latin)      | `bs`          |
| Bulgarca      | `bg`          |
| Cantonetıcı (Geleneksel)      | `yue`          |
| Katalanca      | `ca`          |
| Basitleştirilmiş Çince      | `zh-Hans`          |
| Geleneksel Çince      | `zh-Hant`          |
| Hırvatça      | `hr`          |
| Çekçe      | `cs`          |
| Danca      | `da`          |
| Hollanda dili      | `nl`          |
| Türkçe      | `en`          |
| Estonca      | `et`          |
| Fiji Adaları dili      | `fj`          |
| Filipin dili      | `fil`          |
| Fince      | `fi`          |
| Fransızca      | `fr`          |
| Almanca      | `de`          |
| Yunanca      | `el`          |
| Haian Creole      | `ht`          |
| İbranice      | `he`          |
| Hintçe      | `hi`          |
| Hmong DAW      | `mww`          |
| Macarca      | `hu`          |
| Endonezya dili      | `id`          |
| İtalyanca      | `it`          |
| Japonca      | `ja`          |
| Svahili dili      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Korece      | `ko`          |
| Letonca      | `lv`          |
| Litvanca      | `lt`          |
| Malgaşça      | `mg`          |
| Malay dili      | `ms`          |
| Malta dili      | `mt`          |
| Norveççe      | `nb`          |
| Farsça      | `fa`          |
| Lehçe      | `pl`          |
| Portekizce      | `pt`          |
| Queretaro Otomi      | `otq`          |
| Rumence      | `ro`          |
| Rusça      | `ru`          |
| Samoan      | `sm`          |
| Sırpça (Kiril)      | `sr-Cyrl`          |
| Sırpça (Latin)      | `sr-Latn`          |
| Slovakça     | `sk`          |
| Slovence      | `sl`          |
| İspanyolca      | `es`          |
| İsveççe      | `sv`          |
| Tahiti dili      | `ty`          |
| Tamil dili      | `ta`          |
| Tay dili      | `th`          |
| Tonga dili      | `to`          |
| Türkçe      | `tr`          |
| Ukrayna dili      | `uk`          |
| Urduca      | `ur`          |
| Vietnam dili      | `vi`          |
| Galce      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Sonraki adımlar

* [Konuşma Tanıma Hizmetleri deneme aboneliğinizi alın](https://azure.microsoft.com/try/cognitive-services/)
* [Bkz. c 'de konuşmayı tanıma #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
