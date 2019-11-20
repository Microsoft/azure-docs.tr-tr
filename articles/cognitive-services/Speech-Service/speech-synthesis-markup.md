---
title: Konuşma birleştirme biçimlendirme dili (SSML)-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Söyleniş ve metin okuma, prosody denetlemek için konuşma sentezi biçimlendirme dili kullanma.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 3791b2d60b84299fc3b646f7e6585002078b607f
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350171"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Konuşma Sentezi Biçimlendirme Dili (SSML)

Konuşma sentezi biçimlendirme dili (SSML), geliştiricilerin metin okuma hizmeti kullanılarak, giriş metninin birleştirilmiş konuşmaya nasıl dönüştürüldüğünü belirtmesini sağlayan XML tabanlı bir biçimlendirme dilidir. SSML, düz metin ile karşılaştırıldığında, geliştiricilerin sıklık, telaffuz, konuşma oranı, hacim ve metin okuma çıktısından daha fazlasını yapma olanağı sağlar. Bir dönemden sonra duraklatma gibi normal noktalama işaretleri veya bir tümce bir soru işaretiyle sona erdiğinde doğru innnation 'ın kullanılması otomatik olarak işlenir.

SSML 'nin konuşma hizmetleri uygulamasının, World Wide Web Konsorsiyumu [konuşma sen, biçimlendirme dili sürüm 1,0](https://www.w3.org/TR/speech-synthesis)' i temel alır.

> [!IMPORTANT]
> Çince, Japonca ve Korece karakterler, faturalandırma için iki karakter olarak sayılır. Daha fazla bilgi için bkz. [fiyatlandırma](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Standart, sinir ve özel sesler

Standart ve sinir sesler arasından seçim yapın ya da ürün veya marka için özel sesinizi benzersiz bir şekilde oluşturun. 75 + standart sesler 45 ' den fazla dilde ve yerel ayarlarda kullanılabilir ve 4 dilde ve yerel ayarlarda 5 sinir seste erişilebilir. Desteklenen dillerin, yerel ayarların ve seslerin (sinir ve standart) tüm listesi için bkz. [dil desteği](language-support.md).

Standart, sinir ve özel sesler hakkında daha fazla bilgi edinmek için bkz. [metinden konuşmaya genel bakış](text-to-speech.md).

## <a name="special-characters"></a>Özel karakterler

Metin sentezleştirilmiş konuşmayı dönüştürmek için SSML kullanırken, XML gibi özel karakterlerin, tırnak işaretleri, kesme işareti ve köşeli ayraçlar gibi özel karakterler kaçışına dikkat edilmelidir. Daha fazla bilgi için bkz. [Genişletilebilir biçimlendirme dili (XML) 1,0: Ek D @ no__t-0.

## <a name="supported-ssml-elements"></a>Desteklenen SSML öğeleri

Her SSML belgesi SSML öğeleri (veya etiketleri) ile oluşturulur. Bu öğeler, sıklık, Prosody, hacim ve daha fazlasını ayarlamak için kullanılır. Aşağıdaki bölümler her bir öğenin nasıl kullanıldığını ve bir öğe gerekli veya isteğe bağlı olduğunu ayrıntılandırır.  

> [!IMPORTANT]
> Öznitelik değerleri etrafında çift tırnak kullanmayı unutmayın. Doğru biçimlendirilmiş, geçerli XML standartları, öznitelik değerlerinin çift tırnak işareti içine alınması gerekir. Örneğin, `<prosody volume="90">` iyi biçimlendirilmiş, geçerli bir öğedir, ancak `<prosody volume=90>` değildir. SSML tırnak içinde olmayan öznitelik değerlerini tanıyamayabilir.

## <a name="create-an-ssml-document"></a>SSML belgesi oluşturma

`speak`, kök öğesidir ve tüm SSML belgeleri için **gereklidir** . `speak` Öğesi sürüm, dil ve biçimlendirme sözlük tanımı gibi önemli bilgiler içerir.

**Söz dizimi**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Öznitelikler**

| Öznitelik | Açıklama | Gerekli / isteğe bağlı |
|-----------|-------------|---------------------|
| version | Belge işaretlemesini yorumlamak için kullanılan SSML belirtiminin sürümünü gösterir. Geçerli sürüm 1,0 ' dir. | Gerekli |
| XML: lang | Kök belgenin dilini belirtir. Değer küçük harf, iki harfli dil kodu (örneğin, **en**) veya dil kodu ve büyük harfli ülke/bölge (örneğin, **en-US**) içerebilir. | Gerekli |
| özniteliði | SSML belgesinin biçimlendirme sözlüğünü (öğe türleri ve öznitelik adları) tanımlayan belgenin URI 'sini belirtir. Geçerli URI https://www.w3.org/2001/10/synthesis. | Gerekli |

## <a name="choose-a-voice-for-text-to-speech"></a>Metinden konuşmaya için bir ses seçin

`voice` Öğe gereklidir. Metin okuma için kullanılan sesi belirtmek için kullanılır.

**Söz dizimi**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Öznitelikler**

| Öznitelik | Açıklama | Gerekli / isteğe bağlı |
|-----------|-------------|---------------------|
| name | Metinden konuşmaya çıkış için kullanılan sesi tanımlar. Desteklenen seslerin tüm listesi için bkz. [dil desteği](language-support.md#text-to-speech). | Gerekli |

**Örnek**

> [!NOTE]
> Bu örnek, `en-US-Jessa24kRUS` sesini kullanır. Desteklenen seslerin tüm listesi için bkz. [dil desteği](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Birden çok kişilerden daha fazlasını kullanın

`speak` Öğesi içinde, metinden konuşmaya çıkış için birden çok ses belirtebilirsiniz. Bu sesler farklı dillerde olabilir. Her ses için, metnin bir `voice` öğe içinde sarmalanması gerekir.

**Öznitelikler**

| Öznitelik | Açıklama | Gerekli / isteğe bağlı |
|-----------|-------------|---------------------|
| name | Metinden konuşmaya çıkış için kullanılan sesi tanımlar. Desteklenen seslerin tüm listesi için bkz. [dil desteği](language-support.md#text-to-speech). | Gerekli |

**Örnek**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice  name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Konuşma stillerini ayarla

> [!IMPORTANT]
> Bu özellik yalnızca sinir seslerle çalışır.

Varsayılan olarak, metinden konuşmaya hizmeti, standart ve sinir sesler için bağımsız bir konuşma stili kullanarak metni birleştirir. Sinir seslerle, konuşma stilini, `<mstts:express-as>` öğesiyle birlikte hızlı bir şekilde, empabillik veya yaklaşım için ayarlayabilirsiniz. Bu, Azure konuşma Hizmetleri için benzersiz olan isteğe bağlı bir öğedir.

Şu anda, bu sinir sesleri için konuşma stil ayarlamaları desteklenir:
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

Değişiklikler tümce düzeyinde uygulanır ve stil sese göre farklılık gösterir. Bir stil desteklenmiyorsa, hizmet konuşmayı varsayılan nötr konuşma stilinde döndürür.

**Söz dizimi**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**Öznitelikler**

| Öznitelik | Açıklama | Gerekli / isteğe bağlı |
|-----------|-------------|---------------------|
| type | Konuşma stilini belirtir. Şu anda, konuşma stilleri sese özeldir. | Sinir Voice için konuşma stilini ayarlarken gereklidir. Kullanıyorsanız `mstts:express-as`, Type belirtilmelidir. Geçersiz bir değer sağlanmışsa, bu öğe yok sayılır. |

Her sinir sesi için hangi konuşma stillerinin desteklendiğini öğrenmek için bu tabloyu kullanın.

| Ses | Type | Açıklama |
|-------|------|-------------|
| `en-US-JessaNeural` | tür =`cheerful` | Olumlu ve mutlu bir ehareketini ifade eder |
| | tür =`empathy` | Bir fikir ve anlalama hakkında ifade eder |
| | tür =`chat` | Rastgele, gevşek bir tonda konuşabilirsiniz |
| `zh-CN-XiaoxiaoNeural` | tür =`newscast` | Haber yayınlarına benzer şekilde resmi bir tonu ifade eder |
| | tür =`sentiment` | Dokunmadan bir ileti veya hikaye ileten |

**Örnek**

Bu SSML kod parçacığı, `<mstts:express-as>` konuşma stilini olarak `cheerful`değiştirmek için öğesinin nasıl kullanıldığını gösterir.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Kesme/duraklatma ekleme veya kaldırma

Sözcükler arasına duraklamalar (veya kesintiler) eklemek için öğesinikullanınveyaotomatikolarakmetinokumahizmetitarafındanduraklamalarıönleyin.`break`

> [!NOTE]
> Bu sözcük veya tümcecik için birleştirilmiş konuşma doğal olmayan bir sözcük veya tümcecik için metin okuma (TTS) varsayılan davranışını geçersiz kılmak için bu öğeyi kullanın. Metin `strength` okuma `none` hizmeti tarafından otomatik olarak yerleştirilen bir bürünsel kesmesini engellemek için olarak ayarlayın.

**Söz dizimi**

```xml
<break strength="string" />
<break time="string" />
```

**Öznitelikler**

| Öznitelik | Açıklama | Gerekli / isteğe bağlı |
|-----------|-------------|---------------------|
| gücüyle | Aşağıdaki değerlerden birini kullanarak duraklamanın göreli süresini belirtir:<ul><li>yok</li><li>x-zayıf</li><li>zayıf</li><li>Orta (varsayılan)</li><li>güçlü</li><li>x-Strong</li></ul> | İsteğe Bağlı |
| time | Saniye veya milisaniye cinsinden bir duraklama 'nin mutlak süresini belirtir. Geçerli değer örnekleri 2s ve 500 ' dir | İsteğe Bağlı |

| gücüyle | Açıklama |
|----------|-------------|
| Hiçbiri veya hiçbir değer sağlanmazsa | 0 MS |
| x-zayıf | 250 MS |
| zayıf | 500 ms |
| orta | 750 ms |
| güçlü | 1000 ms |
| x-Strong | 1250 MS |


**Örnek**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Paragrafları ve tümceleri belirtme

`p`ve `s` öğeleri, sırasıyla paragrafları ve tümceleri göstermek için kullanılır. Bu öğelerin yokluğunda, metinden konuşmaya hizmeti, SSML belgesinin yapısını otomatik olarak belirler.

`phoneme` `prosody` Öğesimetin`say-as`ve şu öğeleri içerebilir: `audio`, `break`,,,, `sub`, ,`mstts:express-as`ve .`s` `p`

`phoneme` `break` `audio` `prosody` `sub`Öğesi metin ve şu öğeleri içerebilir:,,,, `say-as`, `mstts:express-as`, ve. `s`

**Söz dizimi**

```XML
<p></p>
<s></s>
```

**Örnek**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Söyleniş 'yi geliştirmek için alfabesine kullanın

`ph` Öğesi SSML belgelerindeki fonetik telaffuz için kullanılır. `ph` Öğesi yalnızca metin içerebilir, başka öğe içeremez. Her zaman bir geri dönüş olarak insan tarafından okunabilen konuşmayı sağlayın.

Fonetik alfabeller, bazen birlikte harflerin, sayıların veya karakterlerin üzerinde oluşan telefonlardan oluşur. Her telefonda benzersiz bir konuşma sesi açıklanır. Bu Latin alfabesinden farklıdır, burada herhangi bir harf birden çok konuşulan sesi temsil edebilir. "Candy" ve "işten" kelimelerdeki "c" harfinin farklı söylenmelerini veya "şeyler" ve "Bunlar" kelimelerinde "TH" harf birleşiminin farklı söylenmelerini göz önünde bulundurun.

**Söz dizimi**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Öznitelikler**

| Öznitelik | Açıklama | Gerekli / isteğe bağlı |
|-----------|-------------|---------------------|
| yi | `ph` Öznitelikteki dizenin okunuşunu birleştirirken kullanılacak fonetik alfabesini belirtir. Alfabeyi belirten dize küçük harfle belirtilmelidir. Aşağıdakiler, belirtebileceğiniz olası harfler sonuçlardır.<ul><li>ipa &ndash; uluslararası fonetik alfabesi</li><li>sapı &ndash; konuşma API telefon kümesi</li><li>UPS &ndash; Universal telefon kümesi</li></ul>Alfabe yalnızca öğedeki Fonem için geçerlidir. Daha fazla bilgi için bkz. [Fonetik alfabe başvurusu](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx). | İsteğe Bağlı |
| f | `phoneme` Öğe içinde sözcüğün söylenişini belirten telefonları içeren bir dize. Belirtilen dize tanınmayan telefonlar içeriyorsa, metinden konuşmaya (TTS) hizmeti SSML belgesinin tamamını reddeder ve belgede belirtilen konuşma çıktısından hiçbiri üretir. | Phonemes kullanılıyorsa gereklidir. |

**Örnekler**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="adjust-prosody"></a>Prosody 'ı ayarla

`prosody` Öğesi, metinden konuşmaya çıkışı için sıklık, countur, Aralık, oran, süre ve birim değişikliklerini belirtmek için kullanılır. `p` `phoneme` Öğesimetin`prosody`ve şu öğeleri içerebilir: `audio`, `break`,,,, `say-as`, ,`sub`ve .`s` `prosody`

Bürünsel öznitelik değerleri geniş bir aralığa göre değişebildiğinden, konuşma tanıyıcı atanan değerleri, seçili sesin gerçek bürünsel değerlerinin ne olduğuna ilişkin bir öneri olarak yorumlar. Metinden konuşmaya hizmeti, desteklenmeyen değerleri sınırlandırır veya yerini alır. Desteklenmeyen değerlere örnek olarak 1 MHz veya 120 birimi gösterilebilir.

**Söz dizimi**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Öznitelikler**

| Öznitelik | Açıklama | Gerekli / isteğe bağlı |
|-----------|-------------|---------------------|
| perde | Metnin taban çizgisi aralığını gösterir. Bu aralığı şöyle ifade edebilirsiniz:<ul><li>Sayı olarak ifade edilen ve ardından "Hz" (Hertz) gelen mutlak bir değer. Örneğin, 600Hz.</li><li>"+" Veya "-" işaretinden sonra gelen ve ardından "Hz" veya "St" gelen bir sayı olarak ifade edilen, sıklığı değiştirecek bir miktar belirten göreli bir değer. Örneğin: + 80Hz veya-2ST. "St", değişim biriminin standart Diatonic ölçeğinde bir ton (yarım bir adım) yarısı olan semitone olduğunu gösterir.</li><li>Sabit değer:<ul><li>x-düşük</li><li>düşük</li><li>orta</li><li>Geniş</li><li>x-yüksek</li><li>default</li></ul></li></ul>. | İsteğe Bağlı |
| kapalı | Sinir sesleriniz için kontur desteklenmez. Dağılım, konuşma çıkışında belirtilen zaman konumlarında bir hedef dizisi olarak konuşma içeriği için değişen değişiklik değişikliklerini temsil eder. Her hedef, parametre çiftleri kümesi tarafından tanımlanır. Örneğin: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Her bir parametre kümesindeki ilk değer, metnin süresinin yüzdesi olarak sıklık değişikliğinin konumunu belirtir. İkinci değer, bir göreli değer veya sıklık için bir numaralandırma değeri kullanarak, sıklığı yükseltmek veya azaltmak için miktarı belirtir (bkz `pitch`.). | İsteğe Bağlı |
| aralığı  | Metin için sıklık aralığını temsil eden bir değer. ' I betimleyen `range` `pitch`mutlak değerleri, göreli değerleri veya numaralandırma değerlerini kullanarak ifade edebilirsiniz. | İsteğe Bağlı |
| derecelendir  | Metnin konuşma oranını gösterir. Şöyle ifade `rate` edebilirsiniz:<ul><li>Varsayılan değer çarpanı olarak davranan sayı olarak ifade edilen göreli bir değer. Örneğin, *1* değeri, fiyata hiçbir değişikliğe neden olmaz. *5* değeri, oranın bir haline neden olur. *3* değeri, ücretle sonuçlanmasına neden olur.</li><li>Sabit değer:<ul><li>x-yavaş</li><li>dığını</li><li>orta</li><li>hızlı</li><li>x-Fast</li><li>default</li></ul></li></ul> | İsteğe Bağlı |
| duration  | Konuşma birleştirme (TTS) hizmeti, metni saniye veya milisaniye olarak okurken geçmesi gereken süre. Örneğin, *2s* veya *1800ms*. | İsteğe Bağlı |
| birim  | Konuşma sesinizin birim düzeyini gösterir. Birimi şu şekilde ifade edebilirsiniz:<ul><li>*Deetest* 'den *loudest*'e kadar 0,0 ile 100,0 arasında bir sayı olarak ifade edilen mutlak bir değer. Örneğin, 75. Varsayılan değer 100,0 ' dir.</li><li>Birimin değiştirileceği miktarı belirten, "+" veya "-" işaretinden önce gelen sayı olarak ifade edilen göreli bir değer. Örneğin + 10 veya-5,5.</li><li>Sabit değer:<ul><li>katılımı</li><li>x-Soft</li><li>yumuşatılmış</li><li>orta</li><li>DIN</li><li>x-yüksek</li><li>default</li></ul></li></ul> | İsteğe Bağlı |

### <a name="change-speaking-rate"></a>Konuşma hızını değiştirme

Konuşma ücreti, standart seslere veya tümce düzeyinde uygulanabilir. Konuşma oranı yalnızca tümce düzeyinde sinir seslere uygulanabilir.

**Örnek**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Birimi Değiştir

Birim değişiklikleri, standart seslere veya tümce düzeyinde uygulanabilir. Birim değişiklikleri, tümce düzeyinde yalnızca sinir seslere uygulanabilir.

**Örnek**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Aralık değiştirme

Sıklık değişiklikleri, standart seslere veya tümce düzeyinde uygulanabilir. Sıklık değişiklikleri yalnızca tümce düzeyinde sinir seslere uygulanabilir.

**Örnek**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Değişiklik aralık dağılımı

> [!IMPORTANT]
> Aralık dağılımı değişiklikleri sinir seslerle desteklenmez.

**Örnek**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>as öğesi  

`say-as`, öğe metninin (sayı veya tarih gibi) içerik türünü belirten isteğe bağlı bir öğedir. Bu, metnin nasıl pronounce hakkında konuşma birleştirme altyapısına kılavuzluk sağlar. 

**Söz dizimi**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Öznitelikler**

| Öznitelik | Açıklama | Gerekli / isteğe bağlı |
|-----------|-------------|---------------------|
| farklı yorumlama | Öğe metninin içerik türünü gösterir. Türlerin listesi için aşağıdaki tabloya bakın. | Gerekli |
| format | Belirsiz biçimleri olabilecek içerik türleri için öğenin metninin kesin biçimlendirmesi hakkında ek bilgiler sağlar. SSML bunları kullanan içerik türleri için biçimleri tanımlar (aşağıdaki tabloya bakın). | İsteğe Bağlı |
| Ayrıntılarını | Söylenen ayrıntı düzeyini gösterir. Örneğin, bu öznitelik konuşma senkiyle motor pronounce noktalama işaretlerinin olmasını isteyebilir. @No__t-0 için tanımlanmış standart değer yok. | İsteğe Bağlı |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

@No__t-0 ve `format` öznitelikleri için desteklenen içerik türleri aşağıda verilmiştir. Yalnızca `interpret-as` Tarih ve saat olarak ayarlandıysa `format` özniteliğini ekleyin.

| farklı yorumlama | format | Korunur |
|--------------|--------|----------------|
| address | | Metin bir adres olarak konuşulur. Konuşma birleştirme motoru pronounces:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Yani "150th mahkeme, Kuzey Doğu Redmond Washington." |
| Kardinal, sayı | | Metin bir Kardinal sayı olarak konuşulur. Konuşma birleştirme motoru pronounces:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />"Üç alternatif vardır." |
| karakterler, yazım denetimi | | Metin, tek tek harfler (yazılmış) olarak konuşulur. Konuşma birleştirme motoru pronounces:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />"T E S T" olarak. |
| date  | DMY, mdy, yımd, yıdm, YM, My, MD, DM, d, m, y | Metin bir tarih olarak konuşulur. @No__t-0 özniteliği, tarihin biçimini belirtir (*d = Day, m = month ve y = Year*). Konuşma birleştirme motoru pronounces:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />"Bugün Ekim, 2016. |
| rakamlar, number_digit | | Metin, tek basamaklı bir dizi olarak konuşulur. Konuşma birleştirme motoru pronounces:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />"1 2 3 4 5 6 7 8 9" olarak |
| dört | | Metin kesirli bir sayı olarak konuşulur. Konuşma birleştirme motoru pronounces:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />"Bir inç üç sekizde biri." |
| ordinal  | | Metin bir sıra numarası olarak konuşulur. Konuşma birleştirme motoru pronounces:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />As "üçüncü seçeneği belirleyin". |
| unuzdaki  | | Metin telefon numarası olarak konuşulur. @No__t-0 özniteliği bir ülke kodunu temsil eden rakamlar içerebilir. Örneğin, Birleşik Devletler için "1" veya Italya için "39". Konuşma birleştirme altyapısı, bu bilgileri bir telefon numarası söylenişini yönlendirecek şekilde kullanabilir. Telefon numarası da ülke kodunu içerebilir ve bu durumda `format` ' daki ülke kodundan önceliklidir. Konuşma birleştirme motoru pronounces:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />As "My No Area Code 8 8 8 5 5 5 1 2 1 2." |
| time | hms12, hms24 | Metin bir zaman olarak konuşulur. @No__t-0 özniteliği, saatin 12 saatlik bir saat (hms12) veya 24 saatlik saat (hms24) kullanılarak mı belirtilmediğini belirtir. Saatleri, dakikaları ve saniyeleri temsil eden sayıları ayırmak için iki nokta üst üste kullanın. Geçerli zaman örnekleri aşağıda verilmiştir: 12:35, 1:14:32, 08:15 ve 02:50:45. Konuşma birleştirme motoru pronounces:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />"," Parçaları dört bir A 'da eğitme. " |

**Kullanım**

@No__t-0 öğesi yalnızca metin içerebilir.

**Örnek**

Konuşma sennet12 35 Me motoru, "ilk isteğiniz," Ekim 20 10 ' de erken gelişle birlikte en fazla bir yere kadar bir odada yer aldığı için aşağıdaki örneği konuşuyor. "
 
```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
    <p>
    Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
    on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
    </p>
</speak>
```


## <a name="add-recorded-audio"></a>Kayıtlı ses ekleme

`audio`, bir SSML belgesine MP3 sesi eklemenize olanak sağlayan isteğe bağlı bir öğedir. Ses öğesinin gövdesi, ses dosyasının kullanılamadığı veya yürütülemeyebilir olmadığı durumlarda konuşulan düz metin veya SSML biçimlendirmesi içerebilir. Ayrıca, `audio` öğesi metin ve şu öğeleri içerebilir: `audio`, `break`, `p`, `s`, `phoneme`, `prosody`, `say-as` ve `sub`.

SSML belgesine dahil edilen tüm seslerin bu gereksinimleri karşılaması gerekir:

* MP3, Internet erişimli bir HTTPS uç noktasında barındırılmalıdır. HTTPS gereklidir ve MP3 dosyasını barındıran etki alanı geçerli ve güvenilen bir SSL sertifikası sunmalıdır.
* MP3 geçerli bir MP3 dosyası (MPEG v2) olmalıdır.
* Bit hızının 48 kbps olması gerekir.
* Örnek hız 16000 Hz olmalıdır.
* Tek bir yanıttaki tüm metin ve ses dosyaları için Birleşik Toplam süre 90 (90) saniyeyi aşamaz.
* MP3, müşteriye özgü veya diğer gizli bilgileri içermemelidir.

**Söz dizimi**

```xml
<audio src="string"/></audio>
```

**Öznitelikler**

| Öznitelik | Açıklama | Gerekli / isteğe bağlı |
|-----------|-------------|---------------------|
| YN | Ses dosyasının konumunu/URL 'sini belirtir. | SSML belgenizde ses öğesi kullanılıyorsa gereklidir. |

**Örnek**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <p>
        <audio src="https://contoso.com/opinionprompt.wav"/>
        Thanks for offering your opinion. Please begin speaking after the beep.
        <audio src="https://contoso.com/beep.wav">
        Could not play the beep, please voice your opinion now. </audio>
    </p>
</speak>
```

## <a name="add-background-audio"></a>Arka plan sesi Ekle

@No__t-0 öğesi SSML belgelerinize arka plan sesi eklemenize (veya metinden konuşmaya sahip bir ses dosyası karıştırabilmeniz) olanak tanır. @No__t-0 ile bir ses dosyasını arka planda döngüye alabilir, metin okuma başlangıcında soluklaşır ve metin okuma sonunda soluklaştırın.

Belirtilen arka plan sesi metinden konuşmaya veya soluklaştırmadan kısaysa, döngü gerçekleştirilir. Metinden konuşmaya daha uzunsa, bu, silinme bittiğinde durur.

SSML belgesi başına yalnızca bir arka plan ses dosyasına izin verilir. Ancak, SSML belgenize Ek ses eklemek için `voice` öğesi içinde `audio` etiketlerini birbirine bağlayabilirsiniz.

**Söz dizimi**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Öznitelikler**

| Öznitelik | Açıklama | Gerekli / isteğe bağlı |
|-----------|-------------|---------------------|
| YN | Arka plan ses dosyasının konumunu/URL 'sini belirtir. | SSML belgenizde arka plan sesi kullanılıyorsa gereklidir. |
| birim | Arka plan ses dosyasının hacmini belirtir. **Kabul edilen değerler**: `0` ile `100` arasında. Varsayılan değer `1` şeklindedir. | İsteğe Bağlı |
| FADEIN | Arka plan sesinin saniye cinsinden silinme süresini belirtir. Varsayılan değer `0` ' dır ve bu değer soldurmak için eşittir. **Kabul edilen değerler**: `0` ile `10000` arasında.  | İsteğe Bağlı |
| fadeout | Arka plan sesinin milisaniye cinsinden silinme süresini belirtir. Varsayılan değer `0` ' dır ve bu, soluklaştırma eşittir. **Kabul edilen değerler**: `0` ile `10000` arasında.  | İsteğe Bağlı |

**Örnek**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Sonraki adımlar

* [Dil desteği: sesler, yerel ayarlar, diller](language-support.md)
