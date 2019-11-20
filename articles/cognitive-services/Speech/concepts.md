---
title: Bing Konuşma kavramlar | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Microsoft konuşma hizmetinde kullanılan temel kavramlar.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: fba1bbdeaf68bdd45524b336011627a27cd024da
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965707"
---
# <a name="basic-concepts"></a>Temel kavramlar

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bu sayfada, Microsoft konuşma tanıma hizmetinde bazı temel kavramlar açıklanmaktadır. Uygulamanızda Microsoft konuşma tanıma API 'sini kullanmadan önce bu sayfayı okumanızı öneririz.

## <a name="understanding-speech-recognition"></a>Konuşma tanımayı anlama

İlk kez konuşma özellikli bir uygulama oluşturuyorsanız veya var olan bir uygulamaya ilk kez konuşma özelliği ekliyorsanız, bu bölüm başlamanıza yardımcı olur. Konuşma özellikli uygulamalarla ilgili bir deneyiminiz zaten varsa, bu bölümü yalnızca geç olarak seçebilirsiniz veya bir konuşma daha eski bir işlem yapıyorsanız ve protokol ayrıntılarına hemen ulaşmak istiyorsanız bu bölümü tamamen atlayabilirsiniz.

### <a name="audio-streams"></a>Ses akışları

Konuşma kavramlarının temel kavramları arasında foremost, *Ses akışıdır*. Tek bir noktada oluşan ve tek bir bilgi içeren bir tuş vuruşundan farklı olarak, konuşulan bir istek yüzlerce milisaniyeye yayılır ve çok fazla kilobayt bilgisi içerir. Konuşmaların süresi, uygulamaları için kolaylaştırılmış ve zarif bir konuşma deneyimi sağlamak isteyen geliştiricilere zorluk gösterir. Günümüzün bilgisayarları ve algoritmaları, söylenişi 'in yaklaşık yarısında konuşma dökümü gerçekleştirir. bu nedenle, 2 saniyelik bir değer kabaca 1 saniye içinde oluşturulabilir, ancak Kullanıcı işlerken 1 saniyelik bir gecikmeyle karşılaşan uygulamalar Kolaylaştırılmış veya şık değil.

Neyse ki, Kullanıcı başka bir bölüme konuştuğken, utnasyon 'nin bir bölümünde döküm gerçekleştirerek, döküm süresini "gizlemenin" bazı yolları vardır. Örneğin, 1 saniyelik bir uttertans 'ı 100 milisaniyelik parçalara ayırarak ve her bir yığında her bir öbek için gereken 450 ' 500 nin üzerinde, kullanıcının farkında olduğunun farkında olması için "gizli" olabilir. konuşurken gerçekleştiriliyor. Bu örneğe göz önünde bulundurulması durumunda, kullanıcının bir sonraki 100 konuşduğu sırada hizmetin önceki 100 milisaniyede devam eden bir işlem gerçekleştirdiğinden emin olun; böylece Kullanıcı konuşmayı kestiğinde, hizmet yalnızca kabaca 100. bir sonuç üretmek için ses milisaniyesi.

Bu kullanıcı deneyimine ulaşmak için, konuşulan ses bilgileri parçalar halinde toplanır ve Kullanıcı kulakça yeniden yapılır. Bu ses, *ses akışından*topluca parçalar ve bu ses öbeklerini hizmete gönderme işlemine *ses akışı denir.* Ses akışı, konuşma özellikli herhangi bir uygulamanın önemli bir parçasıdır; öbek boyutunu ayarlama ve akış uygulamasını iyileştirme, uygulamanızın kullanıcı deneyimini iyileştirmeye yönelik en etkili yollardır.

### <a name="microphones"></a>Gör

İnsanlar, kulamlarını kullanarak seslendirme sürecini işler, ancak donanımla animasyon uygulama mikrofonları kullanır. Herhangi bir uygulamada konuşmayı etkinleştirmek için, uygulamanız için ses akışını sağlayan mikrofonla tümleştirmeniz gerekir.

Mikrofonunuzun API 'Leri, mikrofondan ses baytları almayı ve durdurmayı sağlamalıdır. Konuşmayı dinlemeye başlamak için mikrofonu hangi Kullanıcı eylemlerinin tetikleyeceğini belirlemeniz gerekir. Dinleme başlangıcını tetikleyerek bir düğme tuşuna *basın veya bir* *anahtar sözcüğü* ya da mikrofona sürekli olarak dinleme yapmak ve Microsoft Speech 'e ses göndermeyi tetiklemek için bu modülün çıkışını kullanmak isteyebilirsiniz Hizmetle.

### <a name="end-of-speech"></a>Konuşma sonu

Konuşmacının konuşmayı *ne zaman* *durdurduğunu* algılama, insanlar için yeterince basit görünüyor, ancak laboratuvar koşulları dışında zor bir sorundur. Her şeyi karmaşıklaşmak için genellikle çok sayıda çevresel gürültü olduğundan, utterance sonrasında saf sessizlik aramak yeterli değildir. Microsoft konuşma hizmeti, bir kullanıcının konuşmayı ne zaman durdurduğunu hızlı bir şekilde algılayabilmenizi sağlar ve hizmet bu olguyu size bildirebilir, ancak bu düzenleme, uygulamanızın kullanıcı konuşmayı durduğunda en son öğrenme anlamına gelir. Bu, uygulamanızın, kullanıcının girişi başladığında *ve* sona erdiğinde *ilk* bildiğiniz diğer giriş biçimleri gibi değildir.

### <a name="asynchronous-service-responses"></a>Zaman uyumsuz hizmet yanıtları

Kullanıcı girişi tamamlandığında uygulamanız, uygulamanız üzerinde herhangi bir performans cezaları veya programlama sorunları uygulamaz, ancak konuşma isteklerini giriş isteğinden farklı şekilde düşünmenize gerek duyuyor. Bildiğiniz yanıt desenleri. Uygulamanız kullanıcı konuşmayı ne zaman durdurduğunu bilmeyeceği için, uygulamanız aynı anda hizmete bir yanıt beklerken, bu hizmete ses akışı yapmaya devam etmelidir. Bu model, HTTP gibi diğer istek/yanıt Web protokollerinden farklı olarak yapılır. Bu protokollerde herhangi bir yanıt almadan önce bir isteği tamamlamalısınız; Microsoft konuşma hizmeti protokolünde, *istek için hala ses akışı*yaparken yanıt alırsınız.

> [!NOTE]
> Konuşma HTTP REST API kullanılırken bu özellik desteklenmez.

### <a name="turns"></a>Açar

Konuşma, bilgi taşıyıcısı. Konuşurken, bu bilgileri dinleyen bir kişiye sahip olduğunuz bilgileri iletmeye çalışıyorsunuz. Bilgileri gönderdiğinizde, genellikle konuşma ve dinleme yapmanız gerekir. Benzer şekilde, konuşma özellikli uygulamanız, Kullanıcı tarafından dinlemeden ve yanıt vererek kullanıcılara etkileşime girer, ancak uygulamanız genellikle dinlemeyi büyük olasılıkla yapar. Kullanıcının konuşulan girişi ve bu girişe hizmet yanıtı, *sırasıyla*bir olarak adlandırılır. Bir *açma* işlemi, uygulamanız konuşma hizmeti yanıtını işlemeyi tamamladığında Kullanıcı konuşuyor ve sona erdiğinde başlar.

### <a name="telemetry"></a>Telemetri

Konuşma özellikli bir cihaz veya uygulama oluşturmak, deneyimli geliştiriciler için bile zor olabilir. Akış tabanlı protokoller genellikle ilk bakışta zaman içinde görünür ve sessizlik algılama gibi önemli ayrıntılar tamamen yeni olabilir. Çok sayıda iletinin tek bir istek/yanıt çiftini tamamlamaya yönelik olarak başarıyla gönderilmesi ve alınması gerekir, bu iletilerle ilgili tam ve doğru verilerin toplanması *çok* önemlidir. Microsoft konuşma hizmeti protokolü bu verilerin toplanması için sağlar. Gerekli verileri mümkün olduğunca doğru şekilde sağlamak için her çabayı yapmanız gerekir; eksiksiz ve doğru veriler sunarak kendinize yardımcı olursunuz. Microsoft konuşma hizmeti ekibinin istemci uygulamanızda sorun giderme bölümünde yardıma ihtiyacınız olması gerekir, topladığınız Telemetri verilerinin kalitesi sorun için kritik öneme sahip olacaktır çözümlemeleri.

> [!NOTE]
> Konuşma tanıma REST API kullanılırken bu özellik desteklenmez.

### <a name="speech-application-states"></a>Konuşma uygulaması durumları

Uygulamanızda konuşma girişini etkinleştirmek için kullandığınız adımlar, fare tıklamaları veya parmakla dokunuş gibi diğer giriş formlarına yönelik adımlardan biraz farklıdır. Uygulamanızın mikrofona ne zaman dinlediğini ve konuşma hizmetine veri gönderdiğini, hizmetten bir yanıt beklediği zaman ve boşta durumunda olduğunu takip etmeniz gerekir. Bu durumlar arasındaki ilişki aşağıdaki diyagramda gösterilmiştir.

![Konuşma uygulaması durum diyagramı](Images/speech-application-state-diagram.png)

Microsoft konuşma hizmeti bazı durumlara katıldığından, hizmet protokolü, uygulamanızın durumlar arasında geçişine yardımcı olan iletileri tanımlar. Uygulamanızın konuşma uygulaması durumlarını izlemek ve yönetmek için bu protokol iletilerini yorumlaması ve üzerinde işlem yapması gerekir.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>Uygulamalarınızdan konuşma tanıma hizmetini kullanma

Microsoft konuşma tanıma hizmeti, geliştiricilerin uygulamalarına konuşma eklemesi için iki yol sunar.

- [REST API 'leri](GetStarted/GetStartedREST.md): Geliştiriciler, konuşma tanıma için uygulamalarından gelen HTTP çağrılarını kullanabilirler.
- [İstemci kitaplıkları](GetStarted/GetStartedClientLibraries.md): Geliştiriciler, gelişmiş özellikler için Microsoft konuşma istemci kitaplıklarını indirebilir ve uygulamalarına bağlanabilir.  İstemci kitaplıkları, farklı diller (C#, Java, JavaScript, ObjectiveC) kullanan çeşitli platformlarda (Windows, Android, iOS) kullanılabilir.

| Uygulama alanları | [REST API'ler](GetStarted/GetStartedREST.md) | [İstemci kitaplıkları](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Kısa bir konuşulan sesi (örneğin, ses uzunluğu < 15 s) ara sonuçlar olmadan Dönüştür | Evet | Evet |
| Uzun bir sesi dönüştürme (> 15 s) | Hayır | Evet |
| Ara sonuçlarla istenen ses akışını | Hayır | Evet |
| LUSıS kullanarak sesten dönüştürülen metni anlayın | Hayır | Evet |

 Diliniz veya platformunuz henüz bir SDK 'ya sahip değilse, [protokol belgelerine](API-Reference-REST/websocketprotocol.md)göre kendi uygulamanızı oluşturabilirsiniz.

## <a name="recognition-modes"></a>Tanıma modları

Üç tanıma modu vardır: `interactive`, `conversation`, ve `dictation`. Tanıma modu, konuşma tanımayı kullanıcıların konuşma olasılıklı bir şekilde ayarlar. Uygulamanız için uygun tanıma modunu seçin.

> [!NOTE]
> Tanınma modları, REST protokolünde WebSocket protokolünde olduklarından farklı davranışlara sahip olabilir. Örneğin, REST API konuşma veya dikte modunda bile sürekli tanımayı desteklemez.
> [!NOTE]
> Bu modlar, REST veya WebSocket protokolünü doğrudan kullandığınızda geçerlidir. [İstemci kitaplıkları](GetStarted/GetStartedClientLibraries.md) tanıma modunu belirtmek için farklı parametreler kullanır. Daha fazla bilgi için, seçtiğiniz istemci kitaplığına bakın.

Microsoft konuşma hizmeti, tüm tanıma modları için yalnızca bir tanıma ifadesi sonucu döndürür. Tek bir söylük için 15 saniyelik bir sınır vardır.

### <a name="interactive-mode"></a>Etkileşimli mod

`interactive` Modunda, bir Kullanıcı kısa istekler yapar ve uygulamanın yanıt olarak bir eylem gerçekleştirmesini bekler.

Aşağıdaki özellikler etkileşimli mod uygulamalarının tipik bir larıdır:

- Kullanıcılar bir makineye konuşuyor olduğunu ve başka bir insan için değil.
- Uygulama kullanıcıları, uygulamanın ne kadar söylediklerini temel alarak bu sürenin önünde olduğunu bilir.
- Utterlik, genellikle yaklaşık 2-3 saniye.

### <a name="conversation-mode"></a>Konuşma modu

`conversation` Modunda, kullanıcılar bir insan-insan konuşmasıyla meşgul.

Aşağıdaki özellikler, konuşma modu uygulamalarının tipik bir larıdır:

- Kullanıcılar, başka bir kişiyle konuştuğu olduğunu bilir.
- Konuşma tanıma, bir veya her iki katılımcının de konuşulan metni görmesini sağlayarak insan konuşmalarını geliştirir.
- Kullanıcılar her zaman ne söylemek istediklerini planlarlar.
- Kullanıcılar sıklıkla argo ve diğer resmi olmayan konuşmayı kullanır.

### <a name="dictation-mode"></a>Dikte etme modu

`dictation` Modunda, kullanıcılar daha fazla işleme için uygulamaya daha fazla işlem sağlar.

Aşağıdaki özellikler, dikte modu uygulamalarının tipik bir larıdır:

- Kullanıcılar bir makineyle konuştuğu olduğunu bilir.
- Kullanıcılara konuşma tanıma metin sonuçları gösterilir.
- Kullanıcılar genellikle ne söylemek istediğini planlarlar ve daha resmi bir dil kullanır.
- Kullanıcılar son 5-8 saniye içinde tam tümceler ister.

> [!NOTE]
> Dikte ve konuşma modlarında Microsoft konuşma hizmeti kısmi sonuçlar döndürmez. Bunun yerine, hizmet ses akışındaki sessizlik sınırlarından sonra kararlı ifade sonuçları döndürür. Microsoft, bu sürekli tanıma modundaki Kullanıcı deneyimini iyileştirmek için konuşma protokolünü geliştirebilir.

## <a name="recognition-languages"></a>Tanıma dilleri

*Tanıma dili* , uygulamanızın kullanıcı tarafından konuştukça dili belirtir. Bağlantıda *dil* URL 'si sorgu parametresiyle birlikte *tanıma dilini* belirtin. *Language* Query parametresinin değeri [bcp 47](https://en.wikipedia.org/wiki/IETF_language_tag)IETF dil etiketini kullanır ve konuşma tanıma API 'si tarafından desteklenen dillerden biri **olmalıdır** . Konuşma hizmeti tarafından desteklenen dillerin tüm listesi, sayfa [desteklenen dillerde](API-Reference-REST/supportedlanguages.md)bulunabilir.

Microsoft konuşma hizmeti bir `HTTP 400 Bad Request` yanıt görüntüleyerek geçersiz bağlantı isteklerini reddeder. Geçersiz bir istek aşağıdakilerden biridir:

- Bir *dil* sorgusu parametre değeri içermez.
- Yanlış biçimlendirilmiş bir *dil* sorgu parametresi içerir.
- Destek dillerinden biri olmayan bir *dil* sorgu parametresi içerir.

Hizmet tarafından desteklenen dillerin birini veya tümünü destekleyen bir uygulama oluşturmayı tercih edebilirsiniz.

### <a name="example"></a>Örnek

Aşağıdaki örnekte bir uygulama, ABD Ingilizcesi hoparlörü için *konuşma* konuşma tanıma modunu kullanır.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Döküm yanıtları

Geri alma yanıtları, dönüştürülen metni sesten istemcilere döndürür. Bir döküm yanıtı aşağıdaki alanları içerir:

- `RecognitionStatus`tanımanın durumunu belirtir. Olası değerler aşağıdaki tabloda verilmiştir.

| Durum | Açıklama |
| ------------- | ---------------- |
| Başarılı | Tanıma başarılı oldu ve Görüntümetni alanı var |
| NoMatch | Konuşma Tanıma Ses akışında algılandı, ancak hiçbir hedef dil sözcükleri eşleştirilmiş olan. Daha fazla ayrıntı için bkz. [NoMatch tanıma durumu (#nomatch-tanınma-Status)  |
| InitialSilenceTimeout | Ses akışının başlangıcı yalnızca sessizlik içeriyordu ve hizmet konuşmayı beklerken zaman aşımına uğradı |
| Babbtatimeout | Ses akışının başlangıcı yalnızca gürültü içeriyordu ve hizmet konuşmayı beklerken zaman aşımına uğradı |
| Hata | Tanıma hizmeti bir iç hatayla karşılaştı ve devam edemedi |

- `DisplayText`büyük harfler, noktalama işaretleri ve ters metin normalleştirme uygulandıktan sonra tanınan tümceciği temsil eder ve küfür yıldız işaretiyle maskelenir. Görüntümetni alanı *yalnızca* `RecognitionStatus` alanın değeri `Success`varsa vardır.

- `Offset`ifadenin, ses akışının başlangıcına göre tanınmasında kullanılan sapmayı (100-nanosaniyelik birimi) belirtir.

- `Duration`Bu konuşma tümceciğinin süresini (100-nanosaniyelik birimi) belirtir.

Bir döküm yanıtı isteniyorsa daha fazla bilgi döndürür. Daha ayrıntılı çıktılar döndürme için bkz. [çıkış biçimi](#output-format) .

Microsoft konuşma hizmeti, büyük/küçük harf ve noktalama, bir küfür ekleme ve metni ortak formlara normalleştirime içeren ek döküm sürecini destekler. Örneğin, bir Kullanıcı "altı IPhone satın almayı hatırlat" kelimeyle temsil eden bir tümceciği konuşur, Microsoft 'un konuşma Hizmetleri, "6 IPhone satın almayı hatırlat" metnini döndürür. "Altı" sözcüğünü "6" numaralı metne dönüştüren işlem *ters metin normalleştirme* (Short için*ıshn* ) olarak adlandırılır.

### <a name="nomatch-recognition-status"></a>NoMatch tanıma durumu

Döküm yanıtı `NoMatch` `RecognitionStatus` , Microsoft konuşma hizmeti ses akışındaki konuşmayı algıladığında, ancak bu konuşmayı istek için kullanılan dil dilbilgisinde eşleşemez. Örneğin, tanıyıcı dil olarak Ingilizce 'yi beklediği zaman bir kullanıcı Almanca 'ya görünüyorsa bir *NoMatch* koşulu meydana gelebilir. Söylenişi 'in dalga stili, insan konuşmanızı olduğunu gösterir, ancak söylenen sözcüklerin hiçbiri tanıyıcı tarafından kullanılan ABD İngilizcesi sözlüğü ile eşleşmez.

Diğer bir *NoMatch* koşulu, tanınma algoritması ses akışındaki seslerle ilgili doğru bir eşleşme bulamadığında oluşur. Bu durum gerçekleştiğinde, Microsoft konuşma hizmeti *varsayılan metin* içeren, *konuşma. varsayımlardan* oluşan bir Ileti üretebilir ancak *Recognıtionstatus* 'in NoMatch olduğu bir *konuşma. tümcecik* iletisi üretir. Bu durum normaldir; *konuşma. varsayım* iletisindeki metnin doğruluğu veya uygunluğu hakkında herhangi bir varsayımınızın olmaması gerekir. Buna ek olarak, Microsoft konuşma hizmeti, hizmetin, *Recognıtionstatus* *başarısı*ile bir *konuşma. tümcecik* iletisi *ürettiğini belirten* bir konuşma üreten bir ileti oluşturduğundan bu olduğunu varsaymamalıdır.

## <a name="output-format"></a>Çıkış biçimi

Microsoft konuşma hizmeti, geri alma yanıtlarına çeşitli yük biçimleri döndürebilir. Tüm yükleri JSON yapılarıdır.

`format` URL sorgu parametresini belirterek tümcecik sonuç biçimini kontrol edebilirsiniz. Varsayılan olarak, hizmet sonuçları döndürür `simple` .

| Biçimi | Açıklama |
|-----|-----|
| `simple` | Görüntü formundaki tanıma durumunu ve tanınan metni içeren basitleştirilmiş bir ifade sonucu. |
| `detailed` | Her bir ifadenin her dört tanıma formunu ve güvenirlik Puanını içerdiği bir tanıma durumu ve en iyi sözcük sonuçları listesi. |

`duration` `RecognitionStatus` `Offset` [](#n-best-values)Biçim,, ve ' nin yanı sıra,, ve ' a ek olarak N-en iyi değerleri içerir. `detailed`

### <a name="n-best-values"></a>N-en iyi değerler

İnsanlar, insan veya makine tarafından *tam olarak* söylenen bir şeyi duyduklarında hiçbir şekilde emin olamaz. Bir dinleyici, yalnızca belirli bir söylenme yorumu için bir *olasılık* atayabilir.

Normal koşullarda, sık sık etkileşime geçen kişilere konuşurken insanlar, konuşulan kelimeleri çok büyük bir şekilde tanıyor. Makine tabanlı konuşma dinleyicileri benzer doğruluk düzeylerine sahip olmaya çalışır ve doğru koşullarda, [insanlarla eşlik elde](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v)ederler.

Konuşma tanıma 'da kullanılan algoritmalar, normal işlemenin bir parçası olarak bir uttasyonın alternatif yorumlarınları keşfedebilir. Genellikle, tek bir yorumlamayı tercih eden kanıt çok fazla olduğunda bu alternatifler atılır. Ancak, en iyi koşullarda konuşma tanıyıcı, alternatif olası yorumların bir listesiyle biter. Bu listedeki ilk *n* alternatiflere *n en iyi liste*adı verilir. Her alternatif bir [güven puanı](#confidence)atanır. Güvenirlik puanları 0 ile 1 arasındadır. 1 Puanı, en yüksek düzeyde güvenilirliği temsil eder. 0 puanı, en düşük güvenilirlik düzeyini temsil eder.

> [!NOTE]
> N en iyi listesindeki giriş sayısı, birden çok söylenme arasında farklılık gösterir. Giriş sayısı, *aynı* utin birden fazla tanıma boyunca farklılık gösterebilir. Bu çeşitleme, konuşma tanıma algoritmasının dayalı doğası için doğal ve beklenen bir sonucudur.

N en iyi listesinde döndürülen her giriş şunları içerir

- `Confidence`Bu, bu girişin [güven puanlarını](#confidence) temsil eder.
- `Lexical`, tanınan metnin [sözlü biçimidir](#lexical-form) .
- `ITN`Bu, tanınan metnin [Idin biçimidir](#itn-form) .
- `MaskedITN`, tanınan metnin [MASKELENMIŞ Idin biçimidir](#masked-itn-form) .
- `Display`, tanınan metnin [görüntüleme biçimidir](#display-form) .

### Güven puanları<a id="confidence"></a>

Güvenilirlik puanları, konuşma tanıma sistemlerine yönelik olarak tasarlanmıştır. Microsoft konuşma hizmeti güvenirlik *sınıflandırmadan*güven puanlarını edinir. Microsoft, doğru ve yanlış tanıma arasında en yüksek düzeyde ayırt etmek için tasarlanan bir özellikler kümesi üzerinde güvenirlik sınıflandırmasına karşı bir işlem yapın. Güven puanları tek tek sözcükler ve tüm söylerin tamamına göre değerlendirilir.

Hizmet tarafından döndürülen güven puanlarını kullanmayı seçerseniz, aşağıdaki davranışa dikkat edin:

- Güven puanları yalnızca aynı tanıma modu ve dil dahilinde karşılaştırılabilir. Farklı diller veya farklı tanıma modları arasındaki puanları karşılaştırmayın. Örneğin, etkileşimli tanıma modundaki bir güvenilirlik puanı, dikte modundaki bir güven puanı ile bağıntı *içermez* .
- Güvenilirlik puanları, kısıtlı bir dizi noktasında en iyi şekilde kullanılır. Büyük bir dizi çeşitliliğine yönelik puanlara doğal derecede çok sayıda değişkenlik vardır.

Uygulamanızın hareket eden bir *eşik* olarak bir güvenirlik puanı değeri kullanmayı seçerseniz, eşik değerlerini oluşturmak için konuşma tanımayı kullanın.

- Uygulamanız için temsili bir örnek için konuşma tanımayı yürütün.
- Örnek kümesindeki her bir tanıma ait güven puanlarını toplayın.
- Eşik değerini bu örnek için bazı güven yüzdelerini temel alır.

Tüm uygulamalar için tek bir eşik değeri uygun değildir. Bir uygulama için kabul edilebilir bir güven puanı, başka bir uygulama için kabul edilemez olabilir.

### <a name="lexical-form"></a>sözcük temelli form

Sözlü form, tanınan metindir ve noktalama veya büyük harfler olmadan, tam olarak nasıl meydana gelir. Örneğin, "1020 kurumsal yol" adresinin sözlü biçimi, bu şekilde konuşulan varsayılarak, *10 20 kurumsal bir yöntem*olacaktır. "5 satın almayı anımsat" cümlesini ait sözlü form, *beş Pencils satın almayı hatırlatır*.

Sözcük temelli form, en çok standart olmayan metin normalleştirmesi gerçekleştirmesi gereken uygulamalar için uygundur. Sözlü form, işlenmemiş tanıma sözcükleri gerektiren uygulamalar için de uygundur.

Küfür hiçbir şekilde sözcük olmayan biçimde maskelenmez.

### <a name="itn-form"></a>IDIN formu

Metin normalleştirme, metni bir formdan başka bir "kurallı" forma dönüştürme işlemidir. Örneğin, "555-1212" telefon numarası kurallı form *5 5 5 1 2 1 2*' e dönüştürülebilir. *Ters* metin normalleştirme (itn) bu işlemi tersine çevirir ve "5 5 5 1 2 1 2" sözcüklerini ters çevrilmiş kurallı form *555-1212*' e dönüştürüyor. Bir tanıma sonucunun ın formu, büyük/küçük harf veya noktalama işareti içermez.

ISıS formu, tanınan metin üzerinde işlem gören uygulamalar için uygundur. Örneğin, bir kullanıcının arama terimlerini konuşmasına ve daha sonra bu koşulları bir Web sorgusunda kullanmasına izin veren bir uygulama, ıTYPEFORM kullanır. Küfür hiçbir şekilde ıDIN formunda maskelenmez. Küfür 'yi maskelemek için, *maskelenmiş Idin formunu*kullanın.

### <a name="masked-itn-form"></a>Maskelenmiş ıDIN formu

Küfür doğal olarak konuşulan dilin bir parçası olduğundan, Microsoft konuşma hizmeti bu tür sözcükleri ve tümceleri söylerse algılar. Ancak, özellikle yasak, Yetişkin olmayan kullanıcı kitlesi olan uygulamalar tüm uygulamalar için uygun olmayabilir.

Maskelenmiş ıDIN formu, ters metin normalleştirme formuna küfür maskeleme uygular. Küfür 'yi maskelemek için, küfür parametre değerinin değerini olarak `masked`ayarlayın. Küfür maskelenayarlandığında, dilin küfür sözlüğü 'nin bir parçası olarak tanınan sözcükler yıldız işaretiyle değiştirilmiştir. Örneğin: *5 * * * * Pencils satın almayı hatırlatın*. Bir tanıma sonucunun maskelenen ın formu, büyük/küçük harf veya noktalama işareti içermez.

> [!NOTE]
> Küfür sorgu parametresi değeri olarak `raw`ayarlandıysa, maskelenmiş Idin formu, ıtypeform ile aynıdır. Küfür *maskelenmedi* .

### <a name="display-form"></a>Görüntüleme formu

Vurgulamanın, nerede duraklatılacağı ve bu durumda metnin anlaşılması daha kolay anlaşılır hale getiren noktalama ve büyük harfler sinyali. Görüntüleme formu, tanınma sonuçlarına noktalama ve büyük harfleri ekler ve bu, konuşulan metni görüntüleyen uygulamalar için en uygun formu yapar.

Görüntüleme formu maskelenmiş Idin formunu genişlettiğinden, küfür parametre değerini veya `masked` `raw`olarak ayarlayabilirsiniz. Değer olarak `raw`ayarlanırsa, tanınma sonuçları Kullanıcı tarafından konuşulan herhangi bir küfür içerir. Değer olarak ayarlanırsa `masked`, dilin küfür sözlüğü 'nin bir parçası olarak tanınan sözcükler yıldız işaretiyle değiştirilmiştir.

### <a name="sample-responses"></a>Örnek yanıt

Tüm yükleri JSON yapılarıdır.

`simple` Tümcecik sonucunun yük biçimi:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

`detailed` Tümcecik sonucunun yük biçimi:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="profanity-handling-in-speech-recognition"></a>Konuşma tanımada küfür işleme

Microsoft konuşma hizmeti, birçok kişinin "küfür" olarak sınıflandırılacağı sözcükler ve tümcecikler dahil tüm insan konuşmanızı biçimlerini tanır. *Uygunsuz* bir sorgu parametresini kullanarak hizmetin küfür işlemesini denetleyebilirsiniz. Varsayılan olarak, hizmet maskeleri *konuşma. tümcecik* sonuçlarında bir küfür ve küfür içeren *Speech. varsayım* iletileri döndürmez.

| *Küfür* değeri | Açıklama |
| - | - |
| `masked` | Maskeler, yıldız işaretleri ile birlikte küfür. Bu davranış varsayılandır. |
| `removed` | Tüm sonuçlardan küfür kaldırır. |
| `raw` | Tüm sonuçlarda küfür tanır ve döndürür. |

### <a name="profanity-value-masked"></a>Küfür değeri`Masked`

Küfür 'yi maskelemek için, *küfür* sorgu parametresini *maskelenmiş*değere ayarlayın. *Küfür* sorgu parametresi bu değere sahip veya bir istek için belirtilmediğinde, hizmet *maskeleri* küfür. Hizmet, tanıma sonuçlarında küfür ile yıldız işareti koyarak maskeleme gerçekleştirir. Uygunsuz-maske işlemeyi belirttiğinizde hizmet, küfür içeren *Speech. varsayım* iletileri döndürmez.

### <a name="profanity-value-removed"></a>Küfür değeri`Removed`

*Küfür* sorgu parametresi değeri *kaldırıldığında*, hizmet hem *konuşma. tümcecik* hem de *konuşma. varsayım* iletilerinden bir küfür 'yi kaldırır. Sonuçlar, *küfür kelimelerinizle*aynı olur.

#### <a name="profanity-only-utterances"></a>Yalnızca küfür

Bir Kullanıcı, bir uygulama hizmeti küfür kaldırmak üzere yapılandırdıysa *yalnızca* küfür konuşmasını sağlayabilir. Bu senaryo için, tanınma modu *dikte* veya *konuşma*ise, hizmet bir *konuşma döndürmez. sonuç*. Tanıma modu *etkileşimli*ise, hizmet bir *konuşma döndürür.* durum kodu *NoMatch*olan sonuç.

### <a name="profanity-value-raw"></a>Küfür değeri`Raw`

*Küfür* sorgu parametresinin *Ham*değeri varsa, hizmet, *konuşmayı. tümcecik* veya *konuşma. varsayım* iletilerinde bir küfür kaldırmaz veya maskelemez.
