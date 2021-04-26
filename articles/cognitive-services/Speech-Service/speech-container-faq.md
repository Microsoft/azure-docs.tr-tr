---
title: Konuşma hizmeti kapsayıcıları hakkında sık sorulan sorular (SSS)
titleSuffix: Azure Cognitive Services
description: Konuşma kapsayıcılarını yükleyip çalıştırın. Konuşmadan metne dönüştürme, uygulamalarınızın, araçlarınızın veya cihazlarınızın tüketebileceği veya görüntüleyeceği gerçek zamanlı olarak metin halinde ses akışları. Metin okuma, giriş metnini insan benzeri sentezleştirilmiş konuşmaya dönüştürür.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 28a044f42d0774d940521964b68b38a0f35bcdbb
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387964"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Konuşma hizmeti kapsayıcıları hakkında sık sorulan sorular (SSS)

Konuşma hizmetini kapsayıcılarla birlikte kullanırken, destek 'e ilerleden önce bu sık sorulan sorular koleksiyonuna güvenin. Bu makalede, genel 'ten Technical 'e kadar değişen soruların bir derecesi de yer alır. Bir yanıtı genişletmek için soruya tıklayın.

## <a name="general-questions"></a>Genel sorular

<details>
<summary>
<b>Konuşma kapsayıcıları nasıl çalışır ve bunları nasıl ayarlayabilirim?</b>
</summary>

**Cevap:** Üretim kümesini ayarlarken göz önünde bulundurmanız gereken birkaç nokta vardır. Birincisi, tek dilin kurulması, aynı makinede birden çok kapsayıcı olması, büyük bir sorun olmamalıdır. Sorun yaşıyorsanız, donanımla ilgili bir sorun olabilir. bu nedenle öncelikle kaynağa bakacağız, yani CPU ve bellek belirtimleri.

Bir süre, `ja-JP` kapsayıcı ve en son modeli göz önünde bulundurun. Akustik model en zorlu parça CPU temelinde, dil modeli ise en fazla bellek talep ederken. Kullanımı geliştirdiğimiz zaman, ses gerçek zamanlı olarak (mikrofondan gibi) bir kez akışa eklendiğinde tek bir konuşmaya metin isteği işlemek için 0,6 CPU çekirdeği sürer. Sesi gerçek zamanlı olarak (bir dosyadan) daha hızlı bir şekilde besuyorsanız, bu kullanım çift (1.2 x çekirdek) olabilir. Bu arada, aşağıda listelenen bellek, konuşmayı çözmek için çalışan bellektir. Dosya önbelleğinde yer alacak dil modelinin gerçek tam boyutunu *hesaba almaz.* `ja-JP`Bunun için ek 2 GB; `en-US` daha fazla (6-7 GB) olabilir.

Belleğin scarce olduğu bir makineniz varsa ve bu makinede birden çok dil dağıtmaya çalışıyorsanız, dosya önbelleği dolu olabilir ve işletim sistemi sayfa modellerine giriş ve çıkış için zorlanır. Çalışan bir döküm için felaket olabilir ve yavaşlamalar ve diğer performans etkilerine neden olabilir.

Ayrıca, [Gelişmiş vektör uzantısı (AVX2)](speech-container-howto.md#advanced-vector-extension-support) yönerge kümesi olan makineler için yürütülebilir dosyaları önceden paketliyoruz. AVX512 yönerge kümesine sahip bir makine, bu hedef için kod oluşturmaya gerek duyar ve 10 dil için 10 kapsayıcı başlatıldığında CPU geçici olarak tüketebilir. Docker günlüklerinde şöyle bir ileti görünür:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Değişken kullanarak *tek* bir kapsayıcı içinde istediğiniz kod çözücülerinin sayısını ayarlayabilirsiniz `DECODER MAX_COUNT` . Bu nedenle, temel olarak SKU 'nızda (CPU/bellek) başlamamız gerekir ve bundan en iyi şekilde nasıl yararlanabilmeniz tavsiye ederiz. Harika bir başlangıç noktası, önerilen konak makinesi kaynak belirtimlerine işaret eder.

<br>
</details>

<details>
<summary>
<b>Şirket içi konuşmadan metin kapsayıcılarının kapasite planlaması ve maliyet tahmini konusunda yardımcı olabilir mi?</b>
</summary>

**Cevap:** Toplu işleme modundaki kapsayıcı kapasitesi için, her kod çözücü 2-3x ' i tek bir tanıma için iki CPU çekirdeği ile gerçek zamanlı olarak işleyebilir. Kapsayıcı örneği başına ikiden fazla eşzamanlı tanıma tutulması önerilmez, ancak bir yük dengeleyicinin arkasında güvenilirlik/kullanılabilirlik nedenleriyle daha fazla kapsayıcı örneği çalıştırmayı öneririz.

Ancak, her kapsayıcı örneği daha fazla kod çözücüde çalışıyor olabilir. Örneğin, sekiz çekirdekli bir makinede (milyardan fazla) her kapsayıcı örneği için 7 kod çözücü ayarlayabiliriz. Bilmeniz için bir parametre vardır `DECODER_MAX_COUNT` . Olağanüstü durum için güvenilirlik ve gecikme süresi sorunları ortaya çıkar ve verimlilik önemli ölçüde artar. Bir mikrofon için, bu, 1x gerçek zamanlı olarak olacaktır. Genel kullanım, tek bir tanıma yönelik bir çekirdek hakkında olmalıdır.

Toplu işleme modunda 1 K saat/gün işleme senaryosu için, çok büyük bir durumda 3 VM bu süreyi 24 saat içinde işleyebilir ancak garanti edilmez. Depo, yük devretme, güncelleştirme ve en düşük yedekleme/BCP sağlamak için, küme başına 3 ve 2 + küme yerine 4-5 makine önerilir.

Donanım için standart Azure VM 'yi `DS13_v2` bir başvuru olarak kullanıyoruz (AVX2 yönerge kümesi etkinleştirilmiş olarak her çekirdek 2,6 GHz veya daha iyi olmalıdır).

| Örnek  | vCPU 'lar | RAM    | Geçici depolama | AHB ile Kullandıkça öde | 1 yıllık ayrılmış AHB (% tasarruf) | 3 yıllık, AHB ile ayrılmış (% tasarruf) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | $0.598/saat            | $0.3528/saat (~ %41)                 | $0.2333/saat (~ %61)                  |

Tasarım başvurusuna göre (1 K saat/gün ses toplu işlemini işleyecek iki adet 5 VM kümesi), 1 yıllık donanım maliyeti şu şekilde olacaktır:

> 2 (kümeler) * 5 (küme başına VM) * $0.3528/saat * 365 (gün) * 24 (saat) = $31K/yıl

Fiziksel makineye eşlerken, genel bir tahmin 1 vCPU = 1 fiziksel CPU Çekirdeğdir. Gerçekte, 1vCPU tek bir çekirdekden daha güçlüdür.

Şirket içi için bu ek faktörlerin hepsi yürütmeye gelir:

- Fiziksel CPU ne tür ve üzerinde çekirdek sayısı
- Aynı kutu/makinede birlikte çalışan kaç CPU
- VM 'Ler nasıl ayarlanır
- Hiper iş parçacığı oluşturma/çoklu iş parçacığı kullanma
- Bellek nasıl paylaşılır
- İşletim sistemi, vb.

Normal olarak, ortam Azure olarak da ayarlanmıştır. Diğer ek yükü göz önünde bulundurarak, güvenli bir tahmin için 10 fiziksel CPU çekirdeği = 8 Azure vCPU olduğunu varsayalım. Popüler CPU 'Larda yalnızca sekiz çekirdek olabilir. Şirket içi dağıtım ile maliyet, Azure VM 'Leri kullanmaktan daha yüksek olacaktır. Ayrıca, amortisman oranını göz önünde bulundurun.

Hizmet maliyeti, çevrimiçi hizmet ile aynıdır: konuşma-metin için saat 2. Konuşma hizmeti maliyeti:

> $1 * 1000 * 365 = $365K

Microsoft 'a ödenen bakım maliyeti hizmet düzeyine ve hizmetin içeriğine bağlıdır. Temel düzey için $29.99/ay ile ilgili yerinde hizmet varsa yüzlerce binlerce yüz için farklı. Kaba bir sayı, hizmet/bakım için $300/saat ' tir. Kişi maliyeti dahil değildir. Diğer altyapı maliyetleri (depolama, ağlar ve yük dengeleyiciler gibi) dahil değildir.

<br>
</details>

<details>
<summary>
<b>Neden eksik noktalama işaretleri eksik?</b>
</summary>

**Cevap:** `speech_recognition_language=<YOUR_LANGUAGE>` Karbon istemci kullanılıyorsa istekte açıkça yapılandırılması gerekir.

Örnek:

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
Çıktı şöyledir:

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>Konuşma kapsayıcısı ile özel bir akustik model ve dil modeli kullanabilir miyim?</b>
</summary>

Şu anda yalnızca bir model KIMLIĞI (özel dil modeli veya özel akustik model) geçirebiliyoruz.

**Cevap:** Aynı anda hem akustik hem de dil *modellerini desteklememe* kararı yapılmıştır. Bu işlem, API sonlarını azaltmak için birleştirilmiş bir tanımlayıcı oluşturuluncaya kadar etkin kalır. Bu nedenle, ne yazık ki şu anda desteklenmez.

<br>
</details>

<details>
<summary>
<b>Bu hataları özel konuşmaya metin kapsayıcısından anlatabileceğiniz misiniz?</b>
</summary>

**Hata 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Yanıt 1:** En son özel model ile eğitiyorsanız, şu anda bunu desteklemiyoruz. Daha eski bir sürümle eğitemeniz durumunda kullanılması mümkün olmalıdır. En son sürümleri desteklemeye devam ediyoruz.

Temelde, özel kapsayıcılar halıde veya ONNX tabanlı akustik modelleri (özel eğitim portalında varsayılandır) desteklemez. Bunun nedeni özel modellerin şifrelenmemesinin yanı sıra ONNX modellerini kullanıma sunmak istemiyoruz; ancak dil modelleri iyidir. Müşterinin özel eğitim için eski bir ONNX olmayan modeli açık bir şekilde seçmesini gerekecektir. Doğruluk etkilenmeyecektir. Model boyutu daha büyük (100 MB 'a kadar) olabilir.

> Destek modeli > 20190220 (v 4.5 Birleşik)

**Hata 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Yanıt 2:** İstek için büyük/küçük harfe duyarlı doğru bir ses adı sağlamanız gerekir. Tam hizmet adı eşleme bölümüne bakın.

**Hata 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Yanıt 3:** Bilişsel hizmetler kaynağı değil, bir konuşma kaynağı oluşturun.


<br>
</details>

<details>
<summary>
<b>Hangi API protokolleri desteklenir, REST veya WS?</b>
</summary>

**Cevap:** Konuşmadan metne ve özel konuşmadan metne kapsayıcı için şu anda yalnızca WebSocket tabanlı Protokolü destekliyoruz. SDK yalnızca WS 'de çağrıyı destekler ancak REST değil. REST desteği eklemek için bir plan var, ancak bu süre için ETA değil. Her zaman resmi belgelere başvurun, bkz. [sorgu tahmini uç noktaları](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>CentOS konuşma kapsayıcıları için destekleniyor mu?</b>
</summary>

**Cevap:** CentOS 7, Python SDK tarafından desteklenmiyor, ayrıca Ubuntu 19,04 desteklenmez.

Python konuşma SDK 'Sı paketi, bu işletim sistemleri için kullanılabilir:
- **Windows** -x64 ve x86
- **Mac** -MacOS X sürüm 10,12 veya üzeri
- **Linux** -ubuntu 16,04, ubuntu 18,04, x64 üzerinde detem 9

Ortam kurulumu hakkında daha fazla bilgi için bkz. [Python Platform Kurulumu](quickstarts/setup-platform.md?pivots=programming-language-python). Şimdilik Ubuntu 18,04 önerilen sürümdür.

<br>
</details>

<details>
<summary>
<b>Lug tahmin uç noktalarını çağırmaya çalışırken neden hata alıyorum?</b>
</summary>

IoT Edge dağıtımda LUSıS kapsayıcısını kullanıyorum ve başka bir kapsayıcıdan LUSıS tahmin uç noktasını çağırmaya çalıştım. LUSıS kapsayıcısı 5001 numaralı bağlantı noktasını dinliyor ve kullandığım URL bu.

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

Alırken hata:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

HALSıS kapsayıcı günlüklerinde isteği görüyorum ve ileti şöyle diyor:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Bu ne anlama geliyor? Neleri kaçırdım? Konuşma SDK 'sının örneğini [buradan](https://github.com/Azure-Samples/cognitive-services-speech-sdk)takip ediyorum. Bu senaryo, doğrudan PC mikrofonunuzdan sesi algılıyoruz ve eğitidiğimiz LUO uygulamasına bağlı olarak amacı belirlemeye çalışıyoruz. Bağlandığım örnek tam olarak bunu yapar. Ayrıca, LUSıS bulut tabanlı hizmet ile iyi bir şekilde çalışmaktadır. Konuşma SDK 'sını kullanarak, konuşmayı metne dönüştürme API 'sine ayrı bir açık çağrı yapma zorunlulukisini ve sonra da LUSıS 'e ikinci bir çağrı yapmak zorunda kalmaktan tasarruf edin.

Bu nedenle, BASıS kapsayıcısını kullanmak için bulutta LUIN kullanan senaryodan geçiş yapmaya çalışıyorum. Konuşma SDK 'sının bir için çalışıp çalışmadığını ımaemiyorum, diğer için çalışmaz.

**Cevap:** Konuşma SDK 'Sı bir LUSıS kapsayıcısına karşı kullanılmamalıdır. LUSıS kapsayıcısını kullanmak için, LUYA SDK veya LUSıS REST API kullanılmalıdır. Konuşma SDK 'Sı bir konuşma kapsayıcısına karşı kullanılmalıdır.

Bulut, kapsayıcıdan farklıdır. Bulut, birden fazla toplanmış kapsayıcıyla (bazen mikro hizmetler olarak adlandırılır) oluşabilir. Bu nedenle bir LUSıS kapsayıcısı vardır ve sonra Iki ayrı kapsayıcı olmak üzere bir konuşma kapsayıcısı vardır. Konuşma kapsayıcısı yalnızca konuşma yapar. LUSıS kapsayıcısı yalnızca LUDIR. Bulutta, her iki kapsayıcının de dağıtılması bilindiği ve bir uzak istemcinin buluta gitmesi, konuşma yapması, geri dönüp buluta yeniden gitmesi ve LUSıS 'i yapması için, istemcinin konuşmaya gitmesini sağlayan bir özellik sağlıyoruz, halden sonra da istemciye geri dönebiliriz. Bu senaryoda bile, konuşma SDK 'Sı ses ile konuşma bulutu kapsayıcısına gider ve konuşma bulutu kapsayıcısı, metin ile HALSıS bulut kapsayıcısıyla konuşuyor. LUıN kapsayıcısının ses kabul etme kavramı yok (LUSıS kapsayıcısının akış sesini kabul etmesi için anlamlı hale getirme, metin tabanlı bir hizmettir). On-Pred ile müşterimizin her iki kapsayıcıyı da dağıttığımız hiçbir belirsizlik yoktur, müşterilerimizin Şirket içindeki kapsayıcılar arasında düzenleme yapmamız mümkün değildir ve her iki kapsayıcı de şirket içinde dağıtılırsa, öncelikle SR 'ye gidip istemciye geri dönerek, sonra da bu metni alıp, daha sonra bu metni alıp LUSıS 'ye gitmenize olanak tanıyan bir yük yoktur.

<br>
</details>

<details>
<summary>
<b>MacOS, konuşma kapsayıcısı ve Python SDK ile neden hata alıyorum?</b>
</summary>

Yeniden oluşturulacak bir *. wav* dosyası gönderdiğimiz zaman, sonuç şu şekilde geri gelir:

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

WebSocket 'in doğru şekilde ayarlandığını biliyoruz.

**Cevap:** Bu durumda, [Bu GitHub sorununa](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310)bakın. [Burada önerilen](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)bir çalışmamız var.

Bilgi, 1,8 sürümünde düzeltildi.


<br>
</details>

<details>
<summary>
<b>Konuşma kapsayıcısı uç noktalarında farklılıklar nelerdir?</b>
</summary>

Hangi işlevlerin test edilmesi ve SDK ve REST API 'Lerinin nasıl test edilmesi de dahil olmak üzere, şu test ölçümlerini doldurmaya yardımcı olabilirsiniz misiniz? Özellikle, var olan belge/örnekten görmeyen "Etkileşimli" ve "konuşma" farklılıkları.

| Uç Nokta                                                | İşlevsel test                                                   | SDK | REST API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Metni sentezleştirme (metinden konuşmaya)                                  |     | Yes      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Bilişsel Hizmetler-Şirket içi dikte v1 WebSocket uç noktası        | Yes | Hayır       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | Bilişsel Hizmetler-Şirket içi etkileşimli v1 WebSocket uç noktası  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Bilişsel Hizmetler-Şirket içi konuşma v1 WebSocket uç noktası |     |          |

**Cevap:** Bu bir Fusion:
- Kapsayıcılar için dikte uç noktasını deneyen kişiler, (Bu URL 'nin bu URL 'nin nasıl bulunduğundan emin değilim)
- 1<sup>St</sup> taraf uç noktası, kapsayıcıda bir kapsayıcıdır.
- 1<sup>St</sup> taraf uç noktası, `speech.hypothesis` dikte uç noktası için 3<sup>RD</sup> bölüm uç noktasının döndürdüğü iletiler yerine konuşma. Fragment iletileri döndürüyor.
- Karbon hızlı başlangıç `RecognizeOnce` (etkileşimli mod)
- Bunun için, `speech.fragment` bunları gerektiren iletiler için etkileşimli modda döndürülmeyen bir onaylama.
- Sürüm yapılarında (işlem sonlandırılıyor) Onaylamalar ateşte.

Geçici çözüm kodunuzda sürekli tanıma özelliğini kullanmaya geçiş yapar veya (daha hızlı) kapsayıcıdaki etkileşimli veya sürekli uç noktalara bağlanır.
Kodunuz için, uç noktasını/Speech/Recognition/interactive/cognitiveservices/v1 olarak ayarlayın. `host:port`

Çeşitli modlar için bkz. konuşma modları-aşağıya bakın:

## <a name="speech-modes---interactive-conversation-dictation"></a>Konuşma modları-etkileşimli, konuşma, dikte

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Uygun düzeltmeler, şirket içi desteği olan SDK 1,8 ile gelir (doğru uç noktayı seçer, bu nedenle çevrimiçi hizmetten daha kötü olmayacaktır). Bu sırada, sürekli tanıma için bir örnek var, neden bunu işaret etmedik?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Çeşitli ses dosyaları için hangi modu kullanmalıyım?</b>
</summary>

**Cevap:** [Python kullanan bir hızlı başlangıç](./get-started-speech-to-text.md?pivots=programming-language-python). Docs sitesinde bağlantılı diğer dilleri bulabilirsiniz.

Etkileşimli, konuşmaya ve dikte etmeyi açıklığa kavuşturun; Bu, hizmetimizin konuşma isteğini işleyeceği belirli bir yöntemi belirtmenin gelişmiş bir yoludur. Ne yazık ki, şirket içi kapsayıcılar için tam URI 'yi belirtmemiz gerekir (yerel makine içerdiğinden), bu nedenle bu bilgiler soyutlamadan sızdırıldık. Bu konuda daha fazla kullanılabilir hale getirmek için SDK ekibi ile çalışıyoruz.

<br>
</details>

<details>
<summary>
<b>İşlemin/ikinci/çekirdeğin kaba bir ölçüsünü nasıl kıyaslarız?</b>
</summary>

**Cevap:** Mevcut modelden beklenen kabaca sayıların bazıları aşağıda verilmiştir. (GA 'de getireceğiz bir daha iyi olacaktır):

- Dosyalar için, kısıtlama, 2x ' de konuşma SDK 'sında olacaktır. İlk beş saniyelik ses daraltıldı. Kod çözücü, 3x gerçek zamanlı bir süre alabilir. Bunun için genel CPU kullanımı, tek bir tanıma yönelik 2 çekirdeğe yakın olacaktır.
- MIC için, bu, 1x gerçek zamanlı olarak olacaktır. Tek bir tanıma için genel kullanım yaklaşık 1 çekirdekle olmalıdır.

Bu, Docker günlüklerinden tüm bunların doğrulanması olabilir. Aslında, oturum ve tümcecik/utterance istatistikleriyle satırın dökümünü aldık ve bu, RTF numaralarını içerir.

<br>
</details>

<details>
<summary>
<b>Nasıl yaparım? birden çok kapsayıcı aynı konakta çalışıyor mu?</b>
</summary>

Belge, farklı bir bağlantı noktasını kullanıma sundum, ancak BASıS kapsayıcısı hala 5000 numaralı bağlantı noktasında dinleme yapıyor mu?

**Cevap:** Deneyin `-p <outside_unique_port>:5000` . Örneğin, `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Teknik sorular

<details>
<summary>
<b>Toplu işlem dışı API 'Leri, 15 saniye boyunca sesi işleyecek şekilde nasıl alabilirim &lt; ?</b>
</summary>

**Cevap:** `RecognizeOnce()` modu, en fazla 15 saniyelik sesi işler ve bu mod, söyleyelerin kısa olması beklenen bir konuşma verme amacına yöneliktir. `StartContinuousRecognition()`Dikte veya konuşma için kullanıyorsanız, 15 saniyelik bir sınır yoktur.


<br>
</details>

<details>
<summary>
<b>Önerilen Kaynaklar, CPU ve RAM nedir; 50 eşzamanlı istek için?</b>
</summary>

Kaç tane eş zamanlı istek 4 çekirdek, 4 GB RAM işleme olacaktır? Örneğin, 50 eşzamanlı istek, kaç çekirdek ve RAM önerildiğini sunuyoruz?

**Cevap:** Gerçek zamanlı 8 ' de en son `en-US` olarak, 6 eşzamanlı istek ötesinde daha fazla Docker kapsayıcısı kullanmanızı öneririz. 16 çekirdekten daha büyük bir hal alır ve Tekdüzen olmayan bellek erişimi (NUMA) düğümüne duyarlı hale gelir. Aşağıdaki tabloda, her bir konuşma kapsayıcısı için kaynakların en düşük ve önerilen ayırması açıklanmaktadır.

# <a name="speech-to-text"></a>[Konuşmayı metne dönüştürme](#tab/stt)

| Kapsayıcı      | Minimum             | Önerilen         |
|----------------|---------------------|---------------------|
| Konuşmayı metne dönüştürme | 2 çekirdek, 2 GB bellek | 4 çekirdek, 4 GB bellek |

# <a name="custom-speech-to-text"></a>[Özel Konuşma Tanıma metin](#tab/cstt)

| Kapsayıcı             | Minimum             | Önerilen         |
|-----------------------|---------------------|---------------------|
| Özel Konuşma Tanıma metin | 2 çekirdek, 2 GB bellek | 4 çekirdek, 4 GB bellek |

# <a name="text-to-speech"></a>[Metin okuma](#tab/tts)

| Kapsayıcı      | Minimum             | Önerilen         |
|----------------|---------------------|---------------------|
| Metin okuma | 1 çekirdek, 2 GB bellek | 2 çekirdek, 3 GB bellek |

# <a name="custom-text-to-speech"></a>[Özel metin okuma](#tab/ctts)

| Kapsayıcı             | Minimum             | Önerilen         |
|-----------------------|---------------------|---------------------|
| Özel metin okuma | 1 çekirdek, 2 GB bellek | 2 çekirdek, 3 GB bellek |

***

- Her çekirdek en az 2,6 GHz veya daha hızlı olmalıdır.
- Dosyalar için, daraltma konuşma SDK 'sında, 2x (ilk 5 saniyelik ses azaltmaz) olacaktır.
- Kod çözücü, yaklaşık 2-3x gerçek zamanlı zaman alabilir. Bunun için genel CPU kullanımı, tek bir tanıma için iki çekirdeğe yakın olacaktır. Bu nedenle, kapsayıcı örneği başına ikiden fazla etkin bağlantının tutulması önerilmez. Çok büyük taraf, yaklaşık sekiz gerçek zamanlı 10 kod çözücüsü ile benzer bir şekilde sekiz çekirdekli bir makineye yerleştirilmelidir `DS13_V2` . Kapsayıcı sürümü 1,3 ve üzeri için, ayarlamayı deneyebilmeniz için bir parametre vardır `DECODER_MAX_COUNT=20` .
- Mikrofon için, 1x gerçek zamanlı olarak olacaktır. Genel kullanım, tek bir tanıma yönelik bir çekirdek hakkında olmalıdır.

Sahip olduğunuz toplam ses saati sayısını göz önünde bulundurun. Sayı büyükse, güvenilirliği/kullanılabilirliği artırmak için, bir yük dengeleyicinin arkasında tek bir kutu ya da birden çok kutu üzerinde birden fazla kapsayıcı örneği çalıştırmayı öneririz. Orchestration, Kubernetes (K8S) ve Held kullanılarak veya Docker Compose ile yapılabilir.

Örneğin, 1000 saat/24 saat işlemek için, VM başına 10 örnek/kod çözücü ile 3-4 VM 'Leri ayarlamayı denedik.

<br>
</details>

<details>
<summary>
<b>Konuşma kapsayıcısı noktalama işaretlerini destekliyor mu?</b>
</summary>

**Cevap:** Şirket içi kapsayıcıda büyük küçük harfe (ıG) sahipsiniz. Noktalama, dile bağımlıdır ve Çince ve Japonca dahil bazı dillerde desteklenmez.

Mevcut kapsayıcılar için örtülü ve temel noktalama desteğiniz *var,* ancak `off` Varsayılan olarak. Bunun anlamı, `.` örneğinizdeki karakteri alabileceğiniz, ancak karakteri belirleyebileceğinize ilişkin bir şeydir `。` . Bu örtük mantığı etkinleştirmek için, konuşma SDK 'sını kullanarak Python 'da nasıl yapılacağını gösteren bir örnek aşağıda verilmiştir (diğer dillerde de benzerdir):

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>Verileri konuşmaya metin kapsayıcısına NAKLETMEYE çalışırken neden 404 hata alıyorum?</b>
</summary>

Örnek bir HTTP POST örneği aşağıda verilmiştir:

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**Cevap:** Konuşma-metin kapsayıcısında REST API desteklemiyoruz, yalnızca konuşma SDK 'Sı aracılığıyla WebSockets destekliyoruz. Her zaman resmi belgelere başvurun, bkz. [sorgu tahmini uç noktaları](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>


<details>
<summary>
<b> Kapsayıcı kök olmayan kullanıcı olarak neden çalışıyor? Bu nedenle hangi sorunlar meydana gelebilir?</b>
</summary>

**Cevap:** Kapsayıcı içindeki varsayılan kullanıcının kök olmayan bir kullanıcı olduğunu unutmayın. Bu işlem, kapsayıcıyı kaçış ve ana bilgisayar düğümüne ilerletilen izinleri alma işlemlerine karşı koruma sağlar. Varsayılan olarak, OpenShift kapsayıcı platformu gibi bazı platformlar, rastgele atanan bir kullanıcı KIMLIĞI kullanarak kapsayıcıları çalıştırarak bunu zaten yapamıştı. Bu platformlar için, kök olmayan kullanıcının yazma gerektiren herhangi bir harici eşlenmiş birime yazma izinleri olması gerekir. Örneğin, bir günlük klasörü veya özel bir model indirme klasörü.
<br>
</details>

<details>
<summary>
<b>Konuşmayı metne dönüştürme hizmeti kullanılırken bu hatayı neden alıyorum?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Cevap:** Bu, genellikle ses akışını konuşma tanıma kapsayıcısından daha hızlı bir şekilde katabileceğiniz zaman gerçekleşir. İstemci arabellekleri doldurulur ve iptal tetiklenir. Ses gönderen eşzamanlılık ve RTF 'yi kontrol etmeniz gerekir.

<br>
</details>

<details>
<summary>
<b>C++ örneklerinden bu metinden konuşmaya kapsayıcı hatalarını açıklayabilir misiniz?</b>
</summary>

**Cevap:** Kapsayıcı sürümü 1,3 ' den eskiyse, bu kodun kullanılması gerekir:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Daha eski kapsayıcılar, API ile çalışması için gerekli uç noktaya sahip değildir `FromHost` . 1,3 sürümü için kullanılan kapsayıcılar, bu kodun kullanılması gerekir:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

API kullanımı örneği aşağıda verilmiştir `FromEndpoint` :

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 `SetSpeechSynthesisVoiceName`İşlev, güncelleştirilmiş bir metin okuma altyapısına sahip kapsayıcılar ses adı gerektirdiğinden çağrılır.

<br>
</details>

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bilişsel hizmetler kapsayıcıları](speech-container-howto.md)
