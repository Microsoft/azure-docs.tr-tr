---
title: Translator Metin Çevirisi API'si Translate yöntemi
titleSuffix: Azure Cognitive Services
description: Azure bilişsel hizmetler Translator Metin Çevirisi API'si çevirme yöntemi için parametreleri, üstbilgileri ve gövde iletilerini, metni çevirecek şekilde anlayın.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 10/16/2019
ms.author: swmachan
ms.openlocfilehash: b4daa04a4dbf87006147fb0d44d7b128a6d8ecf4
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835778"
---
# <a name="translator-text-api-30-translate"></a>Translator Metin Çevirisi API'si 3,0: çevir

Metni çevirir.

## <a name="request-url"></a>İstek URL'si

`POST` isteği gönder:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>İstek parametreleri

Sorgu dizesine geçirilen istek parametreleri şunlardır:

<table width="100%">
  <th width="20%">Sorgu parametresi</th>
  <th>Açıklama</th>
  <tr>
    <td>api sürümü</td>
    <td><em>Gerekli parametre</em>.<br/>İstemci tarafından istenen API 'nin sürümü. Değer <code>3.0</code>olmalıdır.</td>
  </tr>
  <tr>
    <td>Kaynak</td>
    <td><em>Isteğe bağlı parametre</em>.<br/>Giriş metninin dilini belirtir. <code>translation</code> kapsamını kullanarak <a href="./v3-0-languages.md">desteklenen dilleri</a> arayarak hangi dillerin çevrilebileceğini bulun. <code>from</code> parametresi belirtilmemişse, kaynak dili saptamak için otomatik dil algılama uygulanır. <br/><br/><a href="https://docs.microsoft.com/azure/cognitive-services/translator/dynamic-dictionary">Dinamik sözlük</a> özelliğini kullanırken, <code>from</code> parametresini, oto algılaması yerine kullanmanız gerekir.</td>
  </tr>
  <tr>
    <td>-</td>
    <td><em>Gerekli parametre</em>.<br/>Çıkış metninin dilini belirtir. Hedef dil <code>translation</code> kapsamında bulunan <a href="./v3-0-languages.md">desteklenen dillerden</a> biri olmalıdır. Örneğin, Almanca 'ya çevirmek için <code>to=de</code> kullanın.<br/>Sorgu dizesindeki parametresini tekrarlayarak birden fazla dile aynı anda çeviri yapılabilir. Örneğin, Almanca ve Italyanca 'e çevirmek için <code>to=de&to=it</code> kullanın.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td><em>Isteğe bağlı parametre</em>.<br/>Çevrilen metnin düz metin mi yoksa HTML metni mi olduğunu tanımlar. Herhangi bir HTML 'nin iyi biçimlendirilmiş, tam bir öğe olması gerekir. Olası değerler şunlardır: <code>plain</code> (varsayılan) veya <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Isteğe bağlı parametre</em>.<br/>Çeviri kategorisini (etki alanı) belirten bir dize. Bu parametre, <a href="../customization.md">özel çevirmenle</a>oluşturulmuş özelleştirilmiş bir sistemden çevirileri almak için kullanılır. Dağıtılan özelleştirilmiş sisteminizi kullanmak için özel Translator <a href="https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">projem ayrıntılarından</a> bu PARAMETREYE kategori kimliği ekleyin. Varsayılan değer: <code>general</code>.</td>
  </tr>
  <tr>
    <td>ProfanityAction</td>
    <td><em>Isteğe bağlı parametre</em>.<br/>Profanities 'in çevirilerde nasıl ele alınacağını belirtir. Olası değerler şunlardır: <code>NoAction</code> (varsayılan), <code>Marked</code> veya <code>Deleted</code>. Küfür işleme yollarını anlamak için bkz. <a href="#handle-profanity">küfür işleme</a>.</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>Isteğe bağlı parametre</em>.<br/>Profanities 'ın çevirilerde nasıl işaretleneceğini belirtir. Olası değerler şunlardır: <code>Asterisk</code> (varsayılan) veya <code>Tag</code>. Küfür işleme yollarını anlamak için bkz. <a href="#handle-profanity">küfür işleme</a>.</td>
  </tr>
  <tr>
    <td>ıncludehizalaması</td>
    <td><em>Isteğe bağlı parametre</em>.<br/>Kaynak metinden çevrilmiş metne hizalama projeksiyonunun eklenip eklenmeyeceğini belirtir. Olası değerler şunlardır: <code>true</code> veya <code>false</code> (varsayılan). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>Isteğe bağlı parametre</em>.<br/>Giriş metni ve çevrilmiş metin için tümce sınırları eklenip eklenmeyeceğini belirtir. Olası değerler şunlardır: <code>true</code> veya <code>false</code> (varsayılan).</td>
  </tr>
  <tr>
    <td>müported</td>
    <td><em>Isteğe bağlı parametre</em>.<br/>Giriş metninin dili tanımlanamıyorsa, bir geri dönüş dili belirtir. Dil otomatik algılama, <code>from</code> parametresi atlandığında uygulanır. Algılama başarısız olursa, <code>suggestedFrom</code> dil kabul edilir.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Isteğe bağlı parametre</em>.<br/>Giriş metninin betiğini belirtir.</td>
  </tr>
  <tr>
    <td>Toscrıpt</td>
    <td><em>Isteğe bağlı parametre</em>.<br/>Çevrilmiş metnin betiğini belirtir.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>Isteğe bağlı parametre</em>.<br/>Özel bir sistem mevcut olmadığında hizmetin genel sisteme geri yüklenmesine izin verildiğini belirtir. Olası değerler şunlardır: <code>true</code> (varsayılan) veya <code>false</code>.<br/><br/><code>allowFallback=false</code>, çeviri yalnızca istek tarafından belirtilen <code>category</code> için eğitilen sistemleri kullanması gerektiğini belirtir. Dil X-dil Y için bir çeviri, bir pivot dil E-postayla zincirleme gerektiriyorsa, zincirdeki tüm sistemlerin (X-> E ve E-> Y) özel olması ve aynı kategoriye sahip olması gerekir. Belirli bir kategoriye sahip hiçbir sistem bulunamazsa istek bir 400 durum kodu döndürür. <code>allowFallback=true</code>, özel bir sistem mevcut olmadığında hizmetin genel bir sisteme geri dönüşme izin verildiğini belirtir.
</td>
  </tr>
</table> 

İstek üstbilgileri şunları içerir:

<table width="100%">
  <th width="20%">Üst bilgiler</th>
  <th>Açıklama</th>
  <tr>
    <td>Kimlik doğrulama üst bilgisi</td>
    <td><em>Gerekli istek üst bilgisi</em>.<br/><a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">Kimlik doğrulaması için kullanılabilen seçeneklere</a>bakın.</td>
  </tr>
  <tr>
    <td>İçerik türü</td>
    <td><em>Gerekli istek üst bilgisi</em>.<br/>Yükün içerik türünü belirtir.<br/> Kabul edilen değer <code>application/json; charset=UTF-8</code>.</td>
  </tr>
  <tr>
    <td>İçerik uzunluğu</td>
    <td><em>Gerekli istek üst bilgisi</em>.<br/>İstek gövdesinin uzunluğu.</td>
  </tr>
  <tr>
    <td>X-Clienttraceıd</td>
    <td><em>İsteğe bağlı</em>.<br/>İsteği benzersiz şekilde tanımlamak için istemci tarafından oluşturulan bir GUID. <code>ClientTraceId</code>adlı bir sorgu parametresi kullanarak, sorgu dizesinde izleme KIMLIĞINI eklerseniz bu üstbilgiyi atlayabilirsiniz.</td>
  </tr>
</table> 

## <a name="request-body"></a>İstek gövdesi

İsteğin gövdesi bir JSON dizisidir. Her dizi öğesi, çevrilecek dizeyi temsil eden `Text`adlı dize özelliğine sahip bir JSON nesnesidir.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Aşağıdaki sınırlamalar geçerlidir:

* Dizi en fazla 100 öğe içerebilir.
* İstekte bulunan metnin tamamı boşluk dahil 5.000 karakteri aşamaz.

## <a name="response-body"></a>Yanıt gövdesi

Başarılı bir yanıt, Giriş dizisindeki her bir dize için bir sonuç içeren bir JSON dizisidir. Bir sonuç nesnesi aşağıdaki özellikleri içerir:

  * `detectedLanguage`: aşağıdaki özellikler aracılığıyla algılanan dili açıklayan bir nesne:

      * `language`: algılanan dilin kodunu temsil eden bir dize.

      * `score`: sonucun güvenilirliği belirten bir float değeri. Puan sıfır ile bir ve düşük puan arasında düşük bir güvenilirlik olduğunu gösterir.

    `detectedLanguage` özelliği yalnızca dilin otomatik algılanması istendiğinde sonuç nesnesinde bulunur.

  * `translations`: bir çeviri sonuçları dizisi. Dizinin boyutu, `to` sorgu parametresi ile belirtilen hedef dillerin sayısıyla eşleşir. Dizideki her öğe şunları içerir:

    * `to`: hedef dilin dil kodunu temsil eden bir dize.

    * `text`: çevrilmiş metni sağlayan bir dize.

    * `transliteration`: `toScript` parametresi tarafından belirtilen betikte çevrilmiş metni sağlayan bir nesne.

      * `script`: hedef betiği belirten bir dize.   

      * `text`: hedef betikte çevrilmiş metni sağlayan bir dize.

    `transliteration` nesnesi, bir alfabeye çevirme gerçekleşmezse dahil edilmez.

    * `alignment`: giriş metnini çevrilmiş metne eşleyen `proj`adlı tek bir dize özelliğine sahip bir nesne. Hizalama bilgileri yalnızca istek parametresi `includeAlignment` `true`olduğunda sağlanır. Hizalama, Şu biçimdeki bir dize değeri olarak döndürülür: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  İki nokta üst üste başlangıç ve bitiş dizinini ayırır, tire dilleri ayırır ve boşluklar sözcükleri ayırır. Bir sözcük, diğer dilde sıfır, bir veya birden çok sözcükten hizalanabilir ve hizalanmış sözcükler bitişik olmayabilir. Hiçbir hizalama bilgisi yoksa, hizalama öğesi boş olur. Bkz. bir örnek ve kısıtlamalar için [hizalama bilgileri alma](#obtain-alignment-information) .

    * `sentLen`: giriş ve çıkış metinlerinde tümce sınırları döndüren bir nesne.

      * `srcSentLen`: giriş metnindeki Tümcelerin uzunluklarını temsil eden bir tamsayı dizisi. Dizinin uzunluğu, Tümcelerin sayısıdır ve değerler her tümcenin uzunluktadır.

      * `transSentLen`: çevrilmiş metindeki Tümcelerin uzunluklarını temsil eden bir tamsayı dizisi. Dizinin uzunluğu, Tümcelerin sayısıdır ve değerler her tümcenin uzunluktadır.

    Tümce sınırları yalnızca istek parametresi `includeSentenceLength` `true`olduğunda dahil edilir.

  * `sourceText`: `text`adlı tek dizeli bir nesne, giriş metnini kaynak dilin varsayılan betiğine verir. `sourceText` özellik, yalnızca giriş, dilin normal betiği olmayan bir betikte ifade edildiğinde mevcuttur. Örneğin, giriş Latin dilinde yazılmış olarak yazılmışsa, `sourceText.text` aynı Arapça metnin Arap betiğine dönüştürülebilmesine neden olur.

[Örnek bölümünde JSON](#examples) yanıtlarının örneği verilmiştir.

## <a name="response-headers"></a>Yanıt üst bilgileri

<table width="100%">
  <th width="20%">Üst bilgiler</th>
  <th>Açıklama</th>
    <tr>
    <td>X-RequestId</td>
    <td>İsteği tanımlamak için hizmet tarafından oluşturulan değer. Sorun giderme amacıyla kullanılır.</td>
  </tr>
  <tr>
    <td>X-MT-sistem</td>
    <td>Çeviri için istenen her ' to ' dili için çeviri için kullanılan sistem türünü belirtir. Değer, dizelerin virgülle ayrılmış bir listesidir. Her dize bir türü gösterir:<br/><ul><li>Özel Istek özel bir sistem içerir ve çeviri sırasında en az bir özel sistem kullanılmıştır.</li><li>Takım-diğer tüm istekler</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Yanıt durum kodları

Bir isteğin döndürdüğü olası HTTP durum kodları aşağıda verilmiştir. 

<table width="100%">
  <th width="20%">Durum kodu</th>
  <th>Açıklama</th>
  <tr>
    <td>200</td>
    <td>Başarılı.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Sorgu parametrelerinden biri eksik veya geçersiz. Yeniden denemeden önce istek parametrelerini düzeltin.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>İsteğin kimliği doğrulanamadı. Kimlik bilgilerinin belirtildiğinden ve geçerli olduğundan emin olun.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>İstek yetkili değil. Ayrıntılar hata iletisini denetleyin. Bu genellikle bir deneme aboneliği ile birlikte sunulan tüm ücretsiz çevirilerin kullanıldığını gösterir.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>Bir kaynak eksik olduğu için istek yerine getirilemedi. Ayrıntılar hata iletisini denetleyin. Özel bir <code>category</code>kullanıldığında, bu genellikle özel çeviri sisteminin istekleri sağlamak için kullanılabilir olmadığını gösterir. İstek bir bekleme süresinden (ör. 1 dakika) sonra yeniden denenmelidir.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>İstemci istek sınırlarını aştığından, sunucu isteği reddetti.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Beklenmeyen bir hata oluştu. Hata devam ederse, bununla raporla: hatanın tarih ve saati, yanıt üst bilgisi <code>X-RequestId</code>istek tanımlayıcısı ve istek üst bilgisi <code>X-ClientTraceId</code>istemci tanımlayıcısı.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Sunucu geçici olarak kullanılamıyor. İsteği yeniden deneyin. Hata devam ederse, bununla raporla: hatanın tarih ve saati, yanıt üst bilgisi <code>X-RequestId</code>istek tanımlayıcısı ve istek üst bilgisi <code>X-ClientTraceId</code>istemci tanımlayıcısı.</td>
  </tr>
</table> 

Bir hata oluşursa, istek bir JSON hata yanıtı da döndürür. Hata kodu 3 basamaklı HTTP durum kodunu birleştiren 6 basamaklı bir sayıdır ve ardından hatayı daha fazla kategorilere ayırarak 3 basamaklı bir sayıdır. Ortak hata kodları, [v3 Translator metin çevirisi API'si başvuru sayfasında](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)bulunabilir. 

## <a name="examples"></a>Örnekler

### <a name="translate-a-single-input"></a>Tek bir girişi çevir

Bu örnek, Ingilizce 'den Basitleştirilmiş Çince 'e tek bir tümcenin nasıl çevrileceğini gösterir.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Yanıt gövdesi:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

`translations` dizi, girişte tek metin parçasının çevirisini sağlayan bir öğesi içerir.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Tek bir girişi dil otomatik algılama ile çevir

Bu örnek, Ingilizce 'den Basitleştirilmiş Çince 'e tek bir tümcenin nasıl çevrileceğini gösterir. İstek, giriş dilini belirtmiyor. Bunun yerine kaynak dilin otomatik algılanması kullanılır.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Yanıt gövdesi:

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
Yanıt, önceki örnekteki yanıta benzerdir. Dil otomatik algılama işlemi istendiğinden yanıt, giriş metni için algılanan dille ilgili bilgiler de içerir. 

### <a name="translate-with-transliteration"></a>Alfabe ile çevir

Daha sonra, bir önceki örneği, alfabeye ekleyerek genişletelim. Aşağıdaki istek Latin komut dosyasında yazılmış bir Çince çevirisi ister.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Yanıt gövdesi:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

Çeviri sonucu artık, çevrilmiş metni Latin karakterlerini kullanarak veren bir `transliteration` özelliği içerir.

### <a name="translate-multiple-pieces-of-text"></a>Birden çok metin parçasını çevirin

Birden çok dizeyi aynı anda çevirmek, istek gövdesinde dize dizisi belirtmenin bir önemi olur.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

Yanıt gövdesi:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Birden çok dile çevir

Bu örnek, aynı girişin bir istekte birkaç dilde nasıl çevrilebileceğini gösterir.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

Yanıt gövdesi:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>Küfür işle

Normalde çevirmen hizmeti, çevirinin kaynağında bulunan küfür 'ı korur. Küfür derecesi ve sözcüklerin küfürlü, kültürler arasında farklı hale getiren bağlam ve sonuç olarak hedef dildeki küfür derecesi indirgenmiş veya azalmış olabilir.

Kaynak metinde küfür varlığından bağımsız olarak çeviride küfür almayı önlemek istiyorsanız, küfür filtreleme seçeneğini kullanabilirsiniz. Bu seçenek, profanities uygun etiketlerle işaretlemek isteyip istemediğinizi seçmenizi sağlar (kendi işlem sonrası kendi işlemlerini ekleme seçeneğini sağlar) veya hiçbir işlem yapılıp yapılmayacağını seçebilirsiniz. Kabul edilen `ProfanityAction` değerleri `Deleted`, `Marked` ve `NoAction` (varsayılan).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Eylem</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Bu, varsayılan davranıştır. Küfür kaynaktan hedefe geçirilecek.<br/><br/>
    <strong>Örnek kaynak (Japonca)</strong>: 彼はジャッカスです.<br/>
    <strong>Örnek çeviri (İngilizce)</strong>: bir Jackass.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Küfürlü sözcükler, değişiklik yapılmadan çıkışta kaldırılacak.<br/><br/>
    <strong>Örnek kaynak (Japonca)</strong>: 彼はジャッカスです.<br/>
    <strong>Örnek çeviri (İngilizce)</strong>: bir.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Küfürlü sözcükleri çıktıda bir işaret ile değiştirilmiştir. İşaretleyici <code>ProfanityMarker</code> parametresine bağlıdır.<br/><br/>
<code>ProfanityMarker=Asterisk</code>için, küfürlü kelimeleri <code>***</code>ile değiştirilmiştir:<br/>
    <strong>Örnek kaynak (Japonca)</strong>: 彼はジャッカスです.<br/>
    <strong>Örnek çeviri (İngilizce)</strong>: \*bir \*\*.<br/><br/>
<code>ProfanityMarker=Tag</code>için, küfürlü kelimeleri &lt;küfür&gt; ve &lt;/küfür&gt;XML etiketleri ile çevrelenmiş:<br/>
    <strong>Örnek kaynak (Japonca)</strong>: 彼はジャッカスです.<br/>
    <strong>Örnek çeviri (İngilizce)</strong>: &lt;Jackass&lt;/küfür&gt;&gt;.
  </tr>
</table> 

Örneğin:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```
Bu değer şunu döndürür:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Karşılaştırma:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```

Bu son istek şunu döndürür:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Biçimlendirme ile içerik çevirin ve ne çevrileceğine karar verin

Bir HTML sayfası veya bir XML belgesinden içerik gibi biçimlendirme içeren içeriği çevirmek yaygındır. Etiketler ile içerik çevrilirken `textType=html` sorgu parametresini ekleyin. Ayrıca, bazı durumlarda belirli içeriği çeviri dışında tutmak yararlı olur. Özgün dilinde kalması gereken içeriği belirtmek için `class=notranslate` özniteliğini kullanabilirsiniz. Aşağıdaki örnekte, ilk `div` öğesi içindeki içerik çevrilmeyecektir, ikinci `div` öğesindeki İçerik çevrilmeyecektir.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Aşağıda, örnek bir istek gösterilmektedir.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

Yanıt:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Hizalama bilgilerini al

Hizalama bilgilerini almak için sorgu dizesinde `includeAlignment=true` belirtin.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation.'}]"
```

Yanıt:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

Hizalama bilgileri `0:2-0:1`başlar, bu, kaynak metindeki ilk üç karakterin (`The`), çevrilmiş metindeki ilk iki karakterle (`La`) eşlendiğini anlamına gelir.

#### <a name="limitations"></a>Sınırlamalar
Aşağıdaki kısıtlamalara göz önünde edin:

* HTML biçimindeki metin için hizalama kullanılamaz, örneğin textType = HTML
* Hizalama yalnızca dil çiftlerinin bir alt kümesi için döndürülür:
  - Ingilizce 'den başka bir dilde;
  - Basitleştirilmiş Çince, geleneksel Çince ve Letonca Ingilizce dışındaki diğer dillerden Ingilizce;
  - Japonca 'dan Korece 'e veya Korece 'e kadar Japonca.
* Tümce bir çeviri ise hizalama almazsınız. "Bu bir sınamadır", "Seni seviyorum" ve diğer yüksek frekanslı cümleler örneği.

### <a name="obtain-sentence-boundaries"></a>Tümce sınırlarını al

Kaynak metninde ve çevrilmiş metinde cümle uzunluğu hakkında bilgi almak için sorgu dizesinde `includeSentenceLength=true` belirtin.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

Yanıt:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Dinamik sözlükle çevir

Bir sözcüğe veya ifadeye uygulamak istediğiniz çeviriyi zaten biliyorsanız istek içinde biçimlendirme olarak sağlayabilirsiniz. Dinamik sözlük, yalnızca uygun adlar ve ürün adları gibi bileşik adlarla güvende olur.

Sağlayacak biçimlendirme aşağıdaki sözdizimini kullanır.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Örneğin, "sözcük çalışma sözcüğü bir sözlük girişi" Ingilizce cümlesini göz önünde bulundurun. Çeviride bir sözcük _dünyayı_ korumak için isteği gönderin:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Sonuç:

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Bu özellik `textType=text` veya `textType=html`ile aynı şekilde çalışmaktadır. Özelliğin gelişigüzel kullanılması gerekir. Çeviriyi özelleştirmenin uygun ve en iyi yolu özel çeviriciyi kullanmaktır. Özel çevirici, bağlam ve istatistiksel olasılıkların tam kullanımını sağlar. Çalışma veya tümceciğinin bağlam içinde gösterildiği eğitim verileri oluşturmak için veya uygun hale getirebiliyorsanız, daha iyi sonuçlar elde edersiniz. [Özel çevirici hakkında daha fazla bilgi edinin](../customization.md).
