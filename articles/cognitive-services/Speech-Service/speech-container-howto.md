---
title: Konuşma hizmeti API 'Leri için Docker Kapsayıcıları yükleyip çalıştırın
titleSuffix: Azure Cognitive Services
description: Konuşma tanıma, döküm, oluşturma ve daha fazlasını gerçekleştirmek için konuşma hizmeti için Docker kapsayıcılarını kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
ms.custom: cog-serv-seo-aug-2020
keywords: Şirket içi, Docker, kapsayıcı
ms.openlocfilehash: cb99dc3c5e16ee117df46d7fda0caab9c57f0853
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388100"
---
# <a name="install-and-run-docker-containers-for-the-speech-service-apis"></a>Konuşma hizmeti API 'Leri için Docker Kapsayıcıları yükleyip çalıştırın 

Kapsayıcılar, Konuşma Hizmeti API’lerinin bazılarını kendi ortamınızda çalıştırmanızı sağlar. Kapsayıcılar, belirli güvenlik ve veri idare gereksinimleri için çok kullanışlıdır. Bu makalede bir Konuşma kapsayıcısını indirme, yükleme ve çalıştırmayı öğreneceksiniz.

Konuşma kapsayıcıları, müşterilerin hem sağlam bulut özellikleri hem de uç yeri için iyileştirilmiş bir konuşma uygulama mimarisi derlemelerini sağlar. Bulut tabanlı Azure konuşma hizmetleriyle aynı [fiyatlandırmayı](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) kullanan birkaç kapsayıcı mevcuttur.


> [!IMPORTANT]
> Aşağıdaki konuşma kapsayıcıları artık genel kullanıma sunulmuştur:
> * Standart konuşmayı metne dönüştürme
> * Özel Konuşma Tanıma metin
> * Standart metin okuma
> * Sinir metin okuma
>
> Aşağıdaki konuşma kapsayıcıları geçitli önizlemededir.
> * Özel metin okuma
> * Konuşma Dil Algılama 
>
> Konuşma kapsayıcılarını kullanmak için bir çevrimiçi istek göndermeniz ve onaylanmış olması gerekir. Daha fazla bilgi için aşağıdaki **kapsayıcıyı çalıştırın** bölümüne bakın.

| Kapsayıcı | Özellikler | En son |
|--|--|--|
| Konuşmayı metne dönüştürme | , Yaklaşım ve sürekli gerçek zamanlı konuşma veya toplu ses kayıtlarını ara sonuçlarla analiz eder.  | 2.11.0 |
| Özel Konuşma Tanıma metin | [Özel konuşma tanıma portalından](https://speech.microsoft.com/customspeech)özel bir model kullanarak, sürekli gerçek zamanlı konuşmayı veya toplu ses kayıtlarını, ara sonuçlarla birlikte metne ekleyin. | 2.11.0 |
| Metin okuma | Düz metin girişi veya konuşma birleştirme biçimlendirme dili (SSML) ile metni doğal-sounkonuşmaya dönüştürür. | 1.13.0 |
| Özel metin okuma | [Özel ses portalından](https://aka.ms/custom-voice-portal)özel bir model kullanarak, düz metin girişi veya konuşma birleştirme biçimlendirme DILI (SSML) ile metni doğal-sounkonuşmaya dönüştürür. | 1.13.0 |
| Konuşma Dil Algılama | Ses dosyalarında konuşulan dili algılayın. | 1.0 |
| Sinir metin okuma | Derin sinir ağ teknolojisini kullanarak metni doğal-sounding konuşmaya dönüştürür. Bu, doğal olarak birleştirilmiş konuşmaya olanak sağlar. | 1.5.0 |

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/cognitive-services/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Konuşma kapsayıcılarını kullanmadan önce aşağıdaki Önkoşullar:

| Gerekli | Amaç |
|--|--|
| Docker altyapısı | Bir [ana bilgisayarda](#the-host-computer)Docker altyapısının yüklü olması gerekir. Docker, [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ve [Linux](https://docs.docker.com/engine/installation/#supported-platforms) üzerinde Docker ortamını yapılandıran paketler sağlar. Docker ve kapsayıcı temel bilgileri ile ilgili giriş yapmak için [Docker’a genel bakış](https://docs.docker.com/engine/docker-overview/) bölümüne bakın.<br><br> Kapsayıcıların Azure 'a bağlanıp faturalandırma verilerini göndermesini sağlamak için Docker yapılandırılmalıdır. <br><br> **Windows 'da** Docker 'ın de Linux kapsayıcılarını destekleyecek şekilde yapılandırılması gerekir.<br><br> |
| Docker ile benzerlik | Kayıt defterleri, depolar, kapsayıcılar ve kapsayıcı görüntüleri gibi Docker kavramlarının yanı sıra temel komutlar hakkında bilgi sahibi olmanız gerekir `docker` . |
| Konuşma kaynağı | Bu kapsayıcıları kullanabilmeniz için, şunları yapmanız gerekir:<br><br>İlişkili API anahtarını ve uç nokta URI 'sini almak için bir Azure _konuşma_ kaynağı. Her iki değer de Azure portal **konuşmaya** genel bakış ve anahtarlar sayfalarında kullanılabilir. Kapsayıcının başlatılması için her ikisi de gereklidir.<br><br>**{API_KEY}**: **anahtarlar** sayfasında kullanılabilir iki kaynak anahtardan biri<br><br>**{ENDPOINT_URI}**: **genel bakış** sayfasında belirtilen bitiş noktası |

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Ana bilgisayar

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Gelişmiş vektör uzantısı desteği

**Konak** , Docker kapsayıcısını çalıştıran bilgisayardır. Ana bilgisayar  [Gelişmiş vektör uzantılarını](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) desteklemelidir (AVX2). Aşağıdaki komutla Linux konaklarda AVX2 desteğini denetleyebilirsiniz:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Ana bilgisayar, AVX2 desteklemek için *gereklidir* . Kapsayıcı, AVX2 desteği olmadan *düzgün çalışmaz.*

### <a name="container-requirements-and-recommendations"></a>Kapsayıcı gereksinimleri ve önerileri

Aşağıdaki tabloda, her bir konuşma kapsayıcısı için kaynakların en düşük ve önerilen ayırması açıklanmaktadır.

| Kapsayıcı | Minimum | Önerilen |
|-----------|---------|-------------|
| Konuşmayı metne dönüştürme | 2 çekirdek, 2 GB bellek | 4 çekirdek, 4 GB bellek |
| Özel Konuşma Tanıma metin | 2 çekirdek, 2 GB bellek | 4 çekirdek, 4 GB bellek |
| Metin okuma | 1 çekirdek, 2 GB bellek | 2 çekirdek, 3 GB bellek |
| Özel metin okuma | 1 çekirdek, 2 GB bellek | 2 çekirdek, 3 GB bellek |
| Konuşma Dil Algılama | 1 çekirdek, 1 GB bellek | 1 çekirdek, 1 GB bellek |
| Sinir metin okuma | 6 çekirdek, 12 GB bellek | 8 çekirdek, 16 GB bellek |

* Her çekirdek en az 2,6 gigahertz (GHz) veya daha hızlı olmalıdır.

Çekirdek ve bellek, `--cpus` `--memory` komutunun bir parçası olarak kullanılan ve ayarlarına karşılık gelir `docker run` .

> [!NOTE]
> En düşük ve önerilen, ana makine kaynaklarından *değil* , Docker sınırlarına dayanır. Örneğin, konuşmadan metne kapsayıcı bellek eşleme, büyük bir dil modelinin bölümlerine eşlenir ve tüm dosyanın belleğe sığması *önerilir* , bu da ek 4-6 GB 'tır. Ayrıca, modellerin bellekte sayfalandığından kapsayıcının ilk çalışması daha uzun sürebilir.

## <a name="request-approval-to-the-run-the-container"></a>Kapsayıcıyı çalıştırmak için onay isteyin

Kapsayıcıya erişim istemek için [istek formunu](https://aka.ms/csgate) doldurun ve iletin. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]


## <a name="get-the-container-image-with-docker-pull"></a>Kapsayıcı görüntüsünü al `docker pull`

Konuşma için kapsayıcı görüntüleri aşağıdaki Container Registry kullanılabilir.

# <a name="speech-to-text"></a>[Konuşmayı metne dönüştürme](#tab/stt)

| Kapsayıcı | Depo |
|-----------|------------|
| Konuşmayı metne dönüştürme | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Özel Konuşma Tanıma metin](#tab/cstt)

| Kapsayıcı | Depo |
|-----------|------------|
| Özel Konuşma Tanıma metin | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Metin okuma](#tab/tts)

| Kapsayıcı | Depo |
|-----------|------------|
| Metin okuma | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest` |

# <a name="neural-text-to-speech"></a>[Sinir metin okuma](#tab/ntts)

| Kapsayıcı | Depo |
|-----------|------------|
| Sinir metin okuma | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Özel metin okuma](#tab/ctts)

| Kapsayıcı | Depo |
|-----------|------------|
| Özel metin okuma | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest` |

# <a name="speech-language-detection"></a>[Konuşma Dil Algılama](#tab/lid)

> [!TIP]
> En faydalı sonuçları almak için konuşma dili algılama kapsayıcısını konuşmaya metin veya özel konuşmaya metin kapsayıcılarıyla kullanmanızı öneririz. 

| Kapsayıcı | Depo |
|-----------|------------|
| Konuşma Dil Algılama | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Konuşma kapsayıcıları için Docker çekme

# <a name="speech-to-text"></a>[Konuşmayı metne dönüştürme](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Konuşmadan metne kapsayıcı için Docker çekme

Microsoft Container Registry 'den bir kapsayıcı görüntüsünü indirmek için [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) komutunu kullanın.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest
```

> [!IMPORTANT]
> `latest`Etiket, `en-US` yerel ayarı çeker. Daha fazla yerel ayar için bkz. [konuşmayı metne göre yerel ayarlar](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Konuşmayı metne göre yerel ayarlar

Dışındaki tüm Etiketler `latest` aşağıdaki biçimdedir ve büyük/küçük harfe duyarlıdır:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

Aşağıdaki etiket, biçiminin bir örneğidir:

```
2.6.0-amd64-en-us
```

**Konuşmadan metne** kapsayıcının desteklenen tüm yerel ayarları Için lütfen [konuşmadan metne görüntü etiketleri](../containers/container-image-tags.md#speech-to-text)bölümüne bakın.

# <a name="custom-speech-to-text"></a>[Özel Konuşma Tanıma metin](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Özel Konuşma Tanıma metin kapsayıcısı için Docker Pull

Microsoft Container Registry 'den bir kapsayıcı görüntüsünü indirmek için [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) komutunu kullanın.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest
```

> [!NOTE]
> `locale` `voice` Özel konuşma kapsayıcıları için ve, kapsayıcı tarafından alınan özel model tarafından belirlenir.

# <a name="text-to-speech"></a>[Metin okuma](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Metin okuma kapsayıcısı için Docker çekme

Microsoft Container Registry 'den bir kapsayıcı görüntüsünü indirmek için [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) komutunu kullanın.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest
```

> [!IMPORTANT]
> `latest`Etiket, `en-US` yerel ayarı ve `ariarus` sesi çeker. Daha fazla yerel ayar için bkz. [metin okuma yerel ayarları](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Metinden konuşmaya yerel ayarlar

Dışındaki tüm Etiketler `latest` aşağıdaki biçimdedir ve büyük/küçük harfe duyarlıdır:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Aşağıdaki etiket, biçiminin bir örneğidir:

```
1.8.0-amd64-en-us-ariarus
```

Desteklenen tüm yerel ayarlar ve **metin okuma** kapsayıcısının karşılık gelen sesleri için bkz. [metin okuma görüntü etiketleri](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> Bir *metinden konuşmaya* http gönderisi oluştururken, [konuşma sen, biçimlendirme dili (SSML)](speech-synthesis-markup.md) iletisi için bir `voice` özniteliği olan bir öğesi gerekir `name` . Değer, ["kısa ad"](language-support.md#standard-voices)olarak da bilinen karşılık gelen kapsayıcı yerel ayarı ve sestir. Örneğin, `latest` etiketinin bir ses adı olacaktır `en-US-AriaRUS` .

# <a name="neural-text-to-speech"></a>[Sinir metin okuma](#tab/ntts)

#### <a name="docker-pull-for-the-neural-text-to-speech-container"></a>Sinir metin okuma kapsayıcısı için Docker Pull

Microsoft Container Registry 'den bir kapsayıcı görüntüsünü indirmek için [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) komutunu kullanın.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest
```

> [!IMPORTANT]
> `latest`Etiket, `en-US` yerel ayarı ve `arianeural` sesi çeker. Daha fazla yerel ayar için bkz. [sinir Text-to-konuşmaya yerel ayarları](#neural-text-to-speech-locales).

#### <a name="neural-text-to-speech-locales"></a>Sinir metin okuma yerel ayarları

Dışındaki tüm Etiketler `latest` aşağıdaki biçimdedir ve büyük/küçük harfe duyarlıdır:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>
```

Aşağıdaki etiket, biçiminin bir örneğidir:

```
1.3.0-amd64-en-us-arianeural
```

Desteklenen tüm yerel ayarlar ve **sinir metin okuma** kapsayıcısının bunlara karşılık gelen sesler için bkz. [sinir Text-to-Speech Image Tags](../containers/container-image-tags.md#neural-text-to-speech).

> [!IMPORTANT]
> *Sinir metin okuma* http gönderisi oluştururken, [konuşma sen, biçimlendirme dili (SSML)](speech-synthesis-markup.md) iletisi için bir `voice` özniteliği olan bir öğesi gerekir `name` . Değer, ["kısa ad"](language-support.md#neural-voices)olarak da bilinen karşılık gelen kapsayıcı yerel ayarı ve sestir. Örneğin, `latest` etiketinin bir ses adı olacaktır `en-US-AriaNeural` .

# <a name="custom-text-to-speech"></a>[Özel metin okuma](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Özel metin okuma kapsayıcısı için Docker Pull

Microsoft Container Registry 'den bir kapsayıcı görüntüsünü indirmek için [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) komutunu kullanın.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest
```

> [!NOTE]
> `locale` `voice` Özel konuşma kapsayıcıları için ve, kapsayıcı tarafından alınan özel model tarafından belirlenir.

# <a name="speech-language-detection"></a>[Konuşma Dil Algılama](#tab/lid)

#### <a name="docker-pull-for-the-speech-language-detection-container"></a>Konuşma Dil Algılama kapsayıcısı için Docker Pull

Microsoft Container Registry 'den bir kapsayıcı görüntüsünü indirmek için [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) komutunu kullanın.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest
```

***

## <a name="how-to-use-the-container"></a>Kapsayıcıyı kullanma

Kapsayıcı [ana bilgisayardan](#the-host-computer)olduktan sonra, kapsayıcında çalışmak için aşağıdaki işlemi kullanın.

1. [Kapsayıcıyı](#run-the-container-with-docker-run)gerekli faturalandırma ayarlarıyla çalıştırın. Komuta [](speech-container-configuration.md#example-docker-run-commands) daha fazla örnek `docker run` kullanılabilir.
1. [Kapsayıcının tahmin uç noktasını sorgulayın](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Kapsayıcıyı ile çalıştırma `docker run`

Kapsayıcıyı çalıştırmak için [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) komutunu kullanın. Ve değerlerini alma hakkında ayrıntılar için [gerekli parametreleri toplama](#gathering-required-parameters) bölümüne bakın `{Endpoint_URI}` `{API_Key}` . Komuta ek [örnekler](speech-container-configuration.md#example-docker-run-commands) `docker run` de mevcuttur.

# <a name="speech-to-text"></a>[Konuşmayı metne dönüştürme](#tab/stt)

Standart *konuşma-metin* kapsayıcısını çalıştırmak için aşağıdaki `docker run` komutu yürütün.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Şu komut:

* Kapsayıcı görüntüsünden *konuşmaya metin* kapsayıcısı çalıştırır.
* 4 CPU çekirdeği ve 4 gigabayt (GB) bellek ayırır.
* TCP bağlantı noktası 5000 ' i gösterir ve kapsayıcı için bir sözde TTY ayırır.
* Kapsayıcıyı çıktıktan sonra otomatik olarak kaldırır. Kapsayıcı görüntüsü hala ana bilgisayarda kullanılabilir.

> [!NOTE]
> Kapsayıcılar, GStreamer kullanarak konuşma SDK 'sına sıkıştırılmış ses girişini destekler.
> Bir kapsayıcıya GStreamer yüklemek için, GStreamer için Linux yönergelerini izleyin [konuşma SDK 'sı ile codec sıkıştırılmış ses girişini kullanın](how-to-use-codec-compressed-audio-input-streams.md).

#### <a name="diarization-on-the-speech-to-text-output"></a>Konuşmadan metne çıkışı
Seçme varsayılan olarak etkindir. Yanıtınıza uygun hale getirmek için kullanın `diarize_speech_config.set_service_property` .

1. Tümcecik çıkış biçimini olarak ayarlayın `Detailed` .
2. Diarleştirme modunu ayarlayın. Desteklenen modlar `Identity` ve ' dir `Anonymous` .
```python
diarize_speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Format',
    value='Detailed',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)

diarize_speech_config.set_service_property(
    name='speechcontext-phraseDetection.speakerDiarization.mode',
    value='Identity',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```
> [!NOTE]
> "Kimlik" modu, `"SpeakerId": "Customer"` veya döndürür `"SpeakerId": "Agent"` .
> "Anonim" mod döndürür `"SpeakerId": "Speaker 1"` veya `"SpeakerId": "Speaker 2"`


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>Konuşmayı metne dönüştürme sırasında yaklaşımı çözümleme 
Konuşmadan metne kapsayıcının v 2.6.0 'den başlayarak, önizleme yerine TextAnalytics 3,0 API uç noktasını kullanmanız gerekir. Örneğin:
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0/sentiment`
* `https://localhost:5000/text/analytics/v3.0/sentiment`

> [!NOTE]
> Metin Analizi `v3.0` API 'si metin analizi ile geriye dönük olarak uyumlu değildir `v3.0-preview.1` . En son yaklaşım özelliği desteğini almak için, `v2.6.0` konuşmadan metne kapsayıcı görüntüsünü ve metin analizi kullanın `v3.0` .

Konuşmayı metin kapsayıcısının 2.2.0 ' den başlayarak, çıkışta yaklaşım [Analizi v3 API](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) 'sini çağırabilirsiniz. Yaklaşım analizini çağırmak için Metin Analizi API'si kaynak uç noktasına ihtiyacınız olacaktır. Örnek: 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

Buluttaki bir metin analizi uç noktasına erişiyorsanız, bir anahtara ihtiyacınız olacaktır. Metin Analizi yerel olarak çalıştırıyorsanız, bunu sağlamanız gerekebilir.

Anahtar ve uç nokta, aşağıdaki örnekte olduğu gibi, konuşma kapsayıcısına bağımsız değişkenler olarak geçirilir.

```bash
docker run -it --rm -p 5000:5000 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

Şu komut:

* Yukarıdaki komutla aynı adımları gerçekleştirir.
* , Yaklaşım Analizi isteklerini göndermek için bir Metin Analizi API'si uç noktası ve anahtarı depolar. 

#### <a name="phraselist-v2-on-the-speech-to-text-output"></a>Konuşmayı metne dönüştürme üzerinde PhraseList v2 

Konuşma-metin kapsayıcısının 2.6.0 ' den başlayarak, kendi tümceciklerinizi veya ortadaki tümceciklerinizi kullanarak çıktıyı alabilir. Örneğin, aşağıdaki tümcedeki *uzun adam* :

* "Bu bir cümle, **Bu,** başka bir tümcedir."

Bir tümcecik listesi yapılandırmak için, çağrıyı yaparken kendi tümceciklerinizi eklemeniz gerekir. Örnek:

```python
    phrase="the tall man"
    recognizer = speechsdk.SpeechRecognizer(
        speech_config=dict_speech_config,
        audio_config=audio_config)
    phrase_list_grammer = speechsdk.PhraseListGrammar.from_recognizer(recognizer)
    phrase_list_grammer.addPhrase(phrase)
    
    dict_speech_config.set_service_property(
        name='setflight',
        value='xonlineinterp',
        channel=speechsdk.ServicePropertyChannel.UriQueryParameter
    )
```

Eklenecek birden çok tümceciği varsa, `.addPhrase()` tümcecik listesine eklemek için her bir tümceciği çağırın. 


# <a name="custom-speech-to-text"></a>[Özel Konuşma Tanıma metin](#tab/cstt)

*Özel konuşma tanıma metin* kapsayıcısı özel bir konuşma modeline bağlıdır. Özel bir model [özel konuşma portalı](https://speech.microsoft.com/customspeech)kullanılarak [eğitilmiş](how-to-custom-speech-train-model.md) olmalıdır.

Kapsayıcıyı çalıştırmak için özel konuşma **MODELI kimliği** gereklidir. Özel konuşma portalının **eğitim** sayfasında bulunabilir. Özel konuşma portalından **eğitim** sayfasına gidin ve modeli seçin.
<br>

![Özel konuşma eğitimi sayfası](media/custom-speech/custom-speech-model-training.png)

Komutun parametresine bağımsız değişken olarak kullanılacak **model kimliğini** edinin `ModelId` `docker run` .
<br>

![Özel konuşma modeli ayrıntıları](media/custom-speech/custom-speech-model-details.png)

Aşağıdaki tablo çeşitli `docker run` parametreleri ve bunlara karşılık gelen açıklamalarını temsil eder:

| Parametre | Açıklama |
|---------|---------|
| `{VOLUME_MOUNT}` | Docker 'ın özel modeli kalıcı hale getirmek için kullandığı Konak bilgisayar [birimi bağlama](https://docs.docker.com/storage/volumes/). Örneğin, *c sürücüsünün* konak makinede bulunduğu *c:\customspeech* . |
| `{MODEL_ID}` | Özel konuşma portalının **eğitim** sayfasından özel konuşma tanıma **model kimliği** . |
| `{ENDPOINT_URI}` | Uç nokta, ölçüm ve faturalandırma için gereklidir. Daha fazla bilgi için bkz. [gerekli parametreleri toplama](#gathering-required-parameters). |
| `{API_KEY}` | API anahtarı gereklidir. Daha fazla bilgi için bkz. [gerekli parametreleri toplama](#gathering-required-parameters). |

*Özel konuşma tanıma metin* kapsayıcısını çalıştırmak için aşağıdaki `docker run` komutu yürütün:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Şu komut:

* Kapsayıcı görüntüsünden *özel konuşma tanıma metin* kapsayıcısı çalıştırır.
* 4 CPU çekirdeği ve 4 gigabayt (GB) bellek ayırır.
* *Özel konuşma tanıma metin* modelini birim girişi bağlamalarından yükler, örneğin *c:\customspeech*.
* TCP bağlantı noktası 5000 ' i gösterir ve kapsayıcı için bir sözde TTY ayırır.
* Verilen modeli indirir `ModelId` (birim bağlaması üzerinde bulunmazsa).
* Özel model daha önce indirildiyse, yok `ModelId` sayılır.
* Kapsayıcıyı çıktıktan sonra otomatik olarak kaldırır. Kapsayıcı görüntüsü hala ana bilgisayarda kullanılabilir.


#### <a name="base-model-download-on-the-custom-speech-to-text-container"></a>Özel konuşmaya metin kapsayıcısına temel model indirme  
Özel konuşma-metin kapsayıcısının sanal 2.6.0 ' den başlayarak, kullanılabilir temel model bilgilerini seçeneğini kullanarak edinebilirsiniz `BaseModelLocale=<locale>` . Bu seçenek, size faturalandırma hesabınız kapsamındaki bu yerel ayar üzerinde bulunan temel modellerin bir listesini sağlar. Örnek:

```bash
docker run --rm -it \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
BaseModelLocale={LOCALE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Şu komut:

* Kapsayıcı görüntüsünden *özel konuşma tanıma metin* kapsayıcısı çalıştırır.
* Hedef yerel ayarın kullanılabilir temel modellerini denetleyin ve geri döndürün.

Çıktı, bilgi yerel ayarları, model KIMLIĞI ve oluşturulma tarihi saati ile birlikte temel modellerin bir listesini sağlar. Tercih ettiğiniz belirli temel modeli indirmek ve kullanmak için model KIMLIĞINI kullanabilirsiniz. Örnek:
```
Checking available base model for en-us
2020/10/30 21:54:20 [Info] Searching available base models for en-us
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T08:23:42Z, Id: a3d8aab9-6f36-44cd-9904-b37389ce2bfa
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T12:01:02Z, Id: cc7826ac-5355-471d-9bc6-a54673d06e45
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2017-08-17T12:00:00Z, Id: a1f8db59-40ff-4f0e-b011-37629c3a1a53
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-04-16T11:55:00Z, Id: c7a69da3-27de-4a4b-ab75-b6716f6321e5
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-09-21T15:18:43Z, Id: da494a53-0dad-4158-b15f-8f9daca7a412
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-10-19T11:28:54Z, Id: 84ec130b-d047-44bf-a46d-58c1ac292ca7
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T07:59:09Z, Id: ee5c100f-152f-4ae5-9e9d-014af3c01c56
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T09:21:55Z, Id: d04959a6-71da-4913-9997-836793e3c115
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-01-11T10:04:19Z, Id: 488e5f23-8bc5-46f8-9ad8-ea9a49a8efda
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-02-18T14:37:57Z, Id: 0207b3e6-92a8-4363-8c0e-361114cdd719
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-03-03T17:34:10Z, Id: 198d9b79-2950-4609-b6ec-f52254074a05
2020/10/30 21:54:21 [Fatal] Please run this tool again and assign --modelId '<one above base model id>'. If no model id listed above, it means currently there is no available base model for en-us
```

#### <a name="custom-pronunciation-on-the-custom-speech-to-text-container"></a>Özel konuşmayı metin kapsayıcısında özel telaffuz 
Özel bir konuşma-metin kapsayıcısının v 2.5.0 'dan başlayarak çıktıda özel telaffuz elde edebilirsiniz. Tek yapmanız gereken, özel modelinizde kendi özel telaffuz kurallarınızın ayarlanmış olması ve modeli özel-konuşmaya metin kapsayıcısına bağlamasıdır.


# <a name="text-to-speech"></a>[Metin okuma](#tab/tts)

Standart *metin okuma* kapsayıcısını çalıştırmak için aşağıdaki `docker run` komutu yürütün.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Şu komut:

* Kapsayıcı görüntüsünden standart bir *metinden konuşmaya* kapsayıcısı çalıştırır.
* 1 CPU çekirdeği ve 2 gigabayt (GB) bellek ayırır.
* TCP bağlantı noktası 5000 ' i gösterir ve kapsayıcı için bir sözde TTY ayırır.
* Kapsayıcıyı çıktıktan sonra otomatik olarak kaldırır. Kapsayıcı görüntüsü hala ana bilgisayarda kullanılabilir.

# <a name="neural-text-to-speech"></a>[Sinir metin okuma](#tab/ntts)

*Sinir metin okuma* kapsayıcısını çalıştırmak için aşağıdaki `docker run` komutu yürütün.

```bash
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Şu komut:

* Kapsayıcı görüntüsünden *sinir metin okuma* kapsayıcısını çalıştırır.
* 6 CPU çekirdeği ve 12 gigabayt (GB) bellek ayırır.
* TCP bağlantı noktası 5000 ' i gösterir ve kapsayıcı için bir sözde TTY ayırır.
* Kapsayıcıyı çıktıktan sonra otomatik olarak kaldırır. Kapsayıcı görüntüsü hala ana bilgisayarda kullanılabilir.

# <a name="custom-text-to-speech"></a>[Özel metin okuma](#tab/ctts)

*Özel metin okuma* kapsayıcısı özel bir ses modeline dayanır. Özel bir modelin [özel ses portalı](https://aka.ms/custom-voice-portal)kullanılarak [eğitilli](how-to-custom-voice-create-voice.md) olması gerekir. Kapsayıcıyı çalıştırmak için özel ses **MODELI kimliği** gereklidir. Özel ses portalının **eğitim** sayfasında bulunabilir. Özel ses portalından **eğitim** sayfasına gidin ve modeli seçin.
<br>

![Özel ses eğitimi sayfası](media/custom-voice/custom-voice-model-training.png)

Docker Run komutunun parametresine bağımsız değişken olarak kullanılacak **model kimliğini** edinin `ModelId` .
<br>

![Özel ses modeli ayrıntıları](media/custom-voice/custom-voice-model-details.png)

Aşağıdaki tablo çeşitli `docker run` parametreleri ve bunlara karşılık gelen açıklamalarını temsil eder:

| Parametre | Açıklama |
|---------|---------|
| `{VOLUME_MOUNT}` | Docker 'ın özel modeli kalıcı hale getirmek için kullandığı Konak bilgisayar [birimi bağlama](https://docs.docker.com/storage/volumes/). Örneğin, *c sürücüsünün* konak makinede bulunduğu *c:\customspeech* . |
| `{MODEL_ID}` | Özel ses portalının **eğitim** sayfasından özel konuşma tanıma **model kimliği** . |
| `{ENDPOINT_URI}` | Uç nokta, ölçüm ve faturalandırma için gereklidir. Daha fazla bilgi için bkz. [gerekli parametreleri toplama](#gathering-required-parameters). |
| `{API_KEY}` | API anahtarı gereklidir. Daha fazla bilgi için bkz. [gerekli parametreleri toplama](#gathering-required-parameters). |

*Özel metin okuma* kapsayıcısını çalıştırmak için aşağıdaki `docker run` komutu yürütün:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Şu komut:

* Kapsayıcı görüntüsünden *özel bir metin okuma* kapsayıcısı çalıştırır.
* 1 CPU çekirdeği ve 2 gigabayt (GB) bellek ayırır.
* Toplu giriş bağlamalarından *özel metin okuma* modelini yükler, örneğin *c:\customvoice*.
* TCP bağlantı noktası 5000 ' i gösterir ve kapsayıcı için bir sözde TTY ayırır.
* Verilen modeli indirir `ModelId` (birim bağlaması üzerinde bulunmazsa).
* Özel model daha önce indirildiyse, yok `ModelId` sayılır.
* Kapsayıcıyı çıktıktan sonra otomatik olarak kaldırır. Kapsayıcı görüntüsü hala ana bilgisayarda kullanılabilir.

# <a name="speech-language-detection"></a>[Konuşma Dil Algılama](#tab/lid)

*Konuşma dil algılama* kapsayıcısını çalıştırmak için aşağıdaki `docker run` komutu yürütün.

```bash
docker run --rm -it -p 5003:5003 --memory 1g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Şu komut: 

* Kapsayıcı görüntüsünden bir konuşma dili algılaması kapsayıcısı çalıştırır. Şu anda bu görüntüyü çalıştırmak için ücretlendirilmeyecektir.
* 1 CPU çekirdeği ve 1 gigabayt (GB) bellek ayırır.
* TCP bağlantı noktası 5003 ' i gösterir ve kapsayıcı için bir sözde TTY ayırır.
* Kapsayıcıyı çıktıktan sonra otomatik olarak kaldırır. Kapsayıcı görüntüsü hala ana bilgisayarda kullanılabilir.

Yalnızca konuşma Dil Algılama isteklerini gönderiyorsanız, konuşma istemcisinin `phraseDetection` değerini olarak ayarlamanız gerekecektir `None` .  

```python
speech_config.set_service_property(
      name='speechcontext-phraseDetection.Mode',
      value='None',
      channel=speechsdk.ServicePropertyChannel.UriQueryParameter
   )
```

Bu kapsayıcıyı konuşmaya metin kapsayıcında çalıştırmak istiyorsanız, bu [Docker görüntüsünü](https://hub.docker.com/r/antsu/on-prem-client)kullanabilirsiniz. Her iki kapsayıcı de başlatıldıktan sonra, çalıştırmak için bu Docker Run komutunu kullanın `speech-to-text-with-languagedetection-client` .

```Docker
docker run --rm -v ${HOME}:/root -ti antsu/on-prem-client:latest ./speech-to-text-with-languagedetection-client ./audio/LanguageDetection_en-us.wav --host localhost --lport 5003 --sport 5000
```

> [!NOTE]
> Eşzamanlı çağrıların sayısının artırılması güvenilirliği ve gecikme süresini etkileyebilir. Dil algılama için 1 CPU ve 1 GB bellek kullanan en fazla 4 eşzamanlı çağrı önerilir. 2 CPU ve 2 GB belleğe sahip konaklarda en fazla 6 eşzamanlı çağrı yapmanızı öneririz.

***

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Kapsayıcıyı çalıştırmak için, ve seçenekleri belirtilmelidir; Aksi takdirde kapsayıcı başlatılmaz.  Daha fazla bilgi için bkz. [faturalandırma](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Kapsayıcının tahmin uç noktasını sorgulama

> [!NOTE]
> Birden çok kapsayıcı çalıştırıyorsanız benzersiz bir bağlantı noktası numarası kullanın.

| Kapsayıcılar | SDK ana bilgisayar URL 'SI | Protokol |
|--|--|--|
| Standart konuşmadan metne ve Özel Konuşma Tanıma metne dönüştürme | `ws://localhost:5000` | WS |
| Metinden konuşmaya (Standart, özel ve sinir dahil), konuşma dili algılama | `http://localhost:5000` | HTTP |

WSS ve HTTPS protokollerini kullanma hakkında daha fazla bilgi için bkz. [kapsayıcı güvenliği](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

### <a name="speech-to-text-standard-and-custom"></a>Konuşmayı metne dönüştürme (Standart ve özel)

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>Yaklaşımı analiz etme

Metin Analizi API'si kimlik bilgilerinizi [kapsayıcıda](#analyze-sentiment-on-the-speech-to-text-output)sağladıysanız konuşma tanıma isteklerini yaklaşım analiziyle göndermek için konuşma SDK 'sını kullanabilirsiniz. API yanıtlarını *basit* veya *ayrıntılı* bir biçim kullanacak şekilde yapılandırabilirsiniz.
> [!NOTE]
> Konuşma hizmeti Python SDK 'sının v 1.13, yaklaşım analizi ile ilgili bir sorun saptadı. Konuşma hizmeti Python SDK 'sında yaklaşım analizini kullanıyorsanız lütfen v 1.12. x veya daha önceki bir sürümünü kullanın.

# <a name="simple-format"></a>[Basit biçim](#tab/simple-format)

Konuşma istemcisini basit bir biçim kullanacak şekilde yapılandırmak için, `"Sentiment"` için bir değer olarak ekleyin `Simple.Extensions` . Belirli bir Metin Analizi modeli sürümü seçmek istiyorsanız, `'latest'` `speechcontext-phraseDetection.sentimentAnalysis.modelversion` özellik yapılandırmasında değiştirin.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions` yanıtın kök katmanında yaklaşım sonucunu döndürür.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[Ayrıntılı biçim](#tab/detailed-format)

Konuşma istemcisini, ayrıntılı bir biçim kullanacak şekilde yapılandırmak için, `"Sentiment"` `Detailed.Extensions` veya her ikisi için bir değer olarak ekleyin `Detailed.Options` . Belirli bir Metin Analizi modeli sürümü seçmek istiyorsanız, `'latest'` `speechcontext-phraseDetection.sentimentAnalysis.modelversion` özellik yapılandırmasında değiştirin.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions` yanıtın kök katmanında yaklaşım sonucunu sağlar. `Detailed.Options` yanıtın katmanında sonucu sağlar `NBest` . Bunlar ayrı olarak veya birlikte kullanılabilir.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

Yaklaşım analizini tamamen devre dışı bırakmak istiyorsanız, `false` öğesine bir değer ekleyin `sentimentanalysis.enabled` .

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-standard-neural-and-custom"></a>Metin okuma (Standart, sinir ve özel)

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Birden çok kapsayıcıyı aynı konakta Çalıştır

Açığa çıkarılan bağlantı noktalarıyla birden çok kapsayıcı çalıştırmak istiyorsanız, her kapsayıcıyı farklı bir açığa çıkarılan bağlantı noktasıyla çalıştırdığınızdan emin olun. Örneğin, bağlantı noktası 5000 ' deki ilk kapsayıcıyı ve bağlantı noktası 5001 üzerindeki ikinci kapsayıcıyı çalıştırın.

Bu kapsayıcınızı ve KONAKTA çalışan farklı bir Azure bilişsel Hizmetler kapsayıcısını birlikte kullanabilirsiniz. Aynı bilişsel Hizmetler kapsayıcısının çalışan birden fazla kapsayıcınızı da kullanabilirsiniz.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Kapsayıcıyı durdurma

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Sorun giderme

Kapsayıcıyı başlatırken veya çalıştırırken sorunlarla karşılaşabilirsiniz. Çıkış [bağlaması](speech-container-configuration.md#mount-settings) kullanın ve günlüğe kaydetmeyi etkinleştirin. Bunun yapılması, kapsayıcının sorunları giderirken faydalı olan günlük dosyaları oluşturmasına izin verir.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturalandırma

Konuşma kapsayıcıları, Azure hesabınızdaki bir *konuşma* kaynağını kullanarak faturalama bilgilerini Azure 'a gönderir.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Bu seçenekler hakkında daha fazla bilgi için bkz. [kapsayıcıları yapılandırma](speech-container-configuration.md).

## <a name="summary"></a>Özet

Bu makalede, konuşma kapsayıcılarını indirmek, yüklemek ve çalıştırmak için kavramları ve iş akışını öğrendiniz. Özet:

* Konuşma, çeşitli özellikleri kapsülleyerek Docker için dört Linux kapsayıcısı sağlar:
  * *Konuşmayı metne dönüştürme*
  * *Özel Konuşma Tanıma metin*
  * *Metin okuma*
  * *Özel metin okuma*
  * *Sinir metin okuma*
  * *Konuşma Dil Algılama*
* Kapsayıcı görüntüleri Azure 'daki kapsayıcı kayıt defterinden indirilir.
* Kapsayıcı görüntüleri Docker 'da çalışır.
* REST API (yalnızca metinden konuşmaya) veya SDK (konuşmadan metne veya metinden konuşmaya) kullanarak kapsayıcının ana bilgisayar URI 'sini belirtirsiniz. 
* Bir kapsayıcıyı örnekledikten sonra faturalandırma bilgilerini sağlamanız gerekir.

> [!IMPORTANT]
>  Bilişsel hizmetler kapsayıcıları, ölçüm için Azure 'a bağlı kalmadan çalıştırılmak üzere lisanslanmaz. Müşterilerin, ödeme bilgilerini her zaman ölçüm hizmetiyle iletişimine olanak tanımak için kapsayıcıların etkinleştirilmesi gerekir. Bilişsel hizmetler kapsayıcıları, müşteri verilerini (ör., çözümlenmekte olan resim veya metin) Microsoft 'a göndermez.

## <a name="next-steps"></a>Sonraki adımlar

* Yapılandırma ayarları için [kapsayıcıları](speech-container-configuration.md) yapılandırmayı gözden geçir
* [Kubernetes ve Held Ile konuşma hizmeti kapsayıcılarını nasıl kullanacağınızı](speech-container-howto-on-premises.md) öğrenin
* Daha fazla bilişsel [Hizmetler kapsayıcısı](../cognitive-services-container-support.md) kullanın
