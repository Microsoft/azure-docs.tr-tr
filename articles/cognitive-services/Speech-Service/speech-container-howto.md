---
title: Konuşma kapsayıcıları yüklemesi-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma kapsayıcılarını yükleyip çalıştırın. Konuşmadan metne dönüştürme, uygulamalarınızın, araçlarınızın veya cihazlarınızın tüketebileceği veya görüntüleyeceği gerçek zamanlı olarak metin halinde ses akışları. Metin okuma, giriş metnini insan benzeri sentezleştirilmiş konuşmaya dönüştürür.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 4170db596d3d4f4b197120770afa2f6e8b0f8a1c
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132613"
---
# <a name="install-and-run-speech-service-containers"></a>Konuşma hizmeti kapsayıcılarını yükleyip çalıştırma

Kapsayıcılar, konuşma hizmeti API 'Lerinden bazılarını kendi ortamınızda çalıştırmanızı sağlar. Kapsayıcılar, belirli güvenlik ve veri idare gereksinimleri için harika. Bu makalede, bir konuşma kapsayıcısının nasıl indirileceği, yükleneceği ve çalıştırılacağı hakkında bilgi edineceksiniz.

Konuşma kapsayıcıları, müşterilerin hem güçlü bulut özellikleri hem de kenar konumu için iyileştirilmiş bir konuşma uygulaması mimarisi oluşturmasını sağlar. Dört farklı kapsayıcı mevcuttur. İki standart kapsayıcı, **konuşmadan metne** ve **metinden konuşmaya**. İki özel kapsayıcı **özel konuşma tanıma metin** ve **özel metinden konuşmaya**.

> [!IMPORTANT]
> Tüm konuşma kapsayıcıları Şu anda [Genel "geçitli" önizlemenin](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio)bir parçası olarak sunulmaktadır. Konuşma kapsayıcıları genel kullanıma (GA) İlerlemede bir duyuru yapılır.

| İşlev | Özellikler | Sürümü |
|--|--|--|
| Konuşmayı metne dönüştürme | Sürekli gerçek zamanlı konuşmayı veya toplu ses kayıtlarını, ara sonuçlarla metin içine ekleyin. | 2.0.0 |
| Özel Konuşma Tanıma metin | [Özel konuşma tanıma portalından](https://speech.microsoft.com/customspeech)özel bir model kullanarak, sürekli gerçek zamanlı konuşmayı veya toplu ses kayıtlarını, ara sonuçlarla birlikte metne ekleyin. | 2.0.0 |
| Metin okuma | Düz metin girişi veya konuşma birleştirme biçimlendirme dili (SSML) ile metni doğal-sounkonuşmaya dönüştürür. | 1.3.0 |
| Özel metin okuma | [Özel ses portalından](https://aka.ms/custom-voice-portal)özel bir model kullanarak, düz metin girişi veya konuşma birleştirme biçimlendirme DILI (SSML) ile metni doğal-sounkonuşmaya dönüştürür. | 1.3.0 |

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Konuşma kapsayıcılarını kullanmadan önce aşağıdaki Önkoşullar:

| Gerekli | Amaç |
|--|--|
| Docker altyapısı | Bir [ana bilgisayarda](#the-host-computer)Docker altyapısının yüklü olması gerekir. Docker, [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)ve [Linux](https://docs.docker.com/engine/installation/#supported-platforms)'ta Docker ortamını yapılandıran paketler sağlar. Docker ve kapsayıcı temelleri hakkında bilgi için bkz: [Docker'a genel bakış](https://docs.docker.com/engine/docker-overview/).<br><br> Docker, kapsayıcılar ile bağlanma ve faturalama verileri Azure'a göndermek izin verecek şekilde yapılandırılmalıdır. <br><br> **Windows 'da**Docker 'ın de Linux kapsayıcılarını destekleyecek şekilde yapılandırılması gerekir.<br><br> |
| Docker ile benzerlik | Kayıt defterleri, depolar, kapsayıcılar ve kapsayıcı görüntüleri gibi Docker kavramlarından ve temel `docker` komutlarının bilgisine sahip olmanız gerekir. |
| Konuşma kaynağı | Bu kapsayıcıları kullanabilmeniz için, şunları yapmanız gerekir:<br><br>İlişkili API anahtarını ve uç nokta URI 'sini almak için bir Azure _konuşma_ kaynağı. Her iki değer de Azure portal **konuşmaya** genel bakış ve anahtarlar sayfalarında kullanılabilir. Kapsayıcının başlatılması için her ikisi de gereklidir.<br><br>**{API_KEY}** : **anahtarlar** sayfasında kullanılabilir iki kaynak anahtardan biri<br><br>**{ENDPOINT_URI}** : **genel bakış** sayfasında belirtilen bitiş noktası |

## <a name="request-access-to-the-container-registry"></a>Kapsayıcı kayıt defterine erişim isteme

Kapsayıcıya erişim istemek için bilişsel [Hizmetler konuşma kapsayıcıları istek formunu](https://aka.ms/speechcontainerspreview/) doldurun ve iletin. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Ana bilgisayar

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Gelişmiş vektör uzantısı desteği

**Konak** , Docker kapsayıcısını çalıştıran bilgisayardır. Ana bilgisayar [Gelişmiş vektör uzantılarını](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) desteklemelidir (AVX2). Aşağıdaki komutla Linux konaklarda AVX2 desteğini denetleyebilirsiniz:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Ana bilgisayar, AVX2 desteklemek için *gereklidir* . Kapsayıcı, AVX2 desteği olmadan *düzgün çalışmaz.*

### <a name="container-requirements-and-recommendations"></a>Kapsayıcı gereksinimleri ve önerileri

Aşağıdaki tabloda, her bir konuşma kapsayıcısı için kaynakların en düşük ve önerilen ayırması açıklanmaktadır.

# <a name="speech-to-texttabstt"></a>[Konuşmayı metne dönüştürme](#tab/stt)

| Kapsayıcı | Minimum | Önerilen |
|-----------|---------|-------------|
| Konuşmayı metne dönüştürme | 2 çekirdek, 2 GB bellek | 4 çekirdek, 4 GB bellek |

# <a name="custom-speech-to-texttabcstt"></a>[Özel Konuşma Tanıma metin](#tab/cstt)

| Kapsayıcı | Minimum | Önerilen |
|-----------|---------|-------------|
| Özel Konuşma Tanıma metin | 2 çekirdek, 2 GB bellek | 4 çekirdek, 4 GB bellek |

# <a name="text-to-speechtabtts"></a>[Metin okuma](#tab/tts)

| Kapsayıcı | Minimum | Önerilen |
|-----------|---------|-------------|
| Metin okuma | 1 çekirdek, 2 GB bellek | 2 çekirdek, 3 GB bellek |

# <a name="custom-text-to-speechtabctts"></a>[Özel metin okuma](#tab/ctts)

| Kapsayıcı | Minimum | Önerilen |
|-----------|---------|-------------|
| Özel metin okuma | 1 çekirdek, 2 GB bellek | 2 çekirdek, 3 GB bellek |

***

* Her çekirdek en az 2,6 gigahertz (GHz) veya daha hızlı olmalıdır.

Çekirdek ve bellek, `docker run` komutunun bir parçası olarak kullanılan `--cpus` ve `--memory` ayarlarına karşılık gelir.

> [!NOTE]
> En düşük ve önerilen, ana makine kaynaklarından *değil* , Docker sınırlarına dayanır. Örneğin, konuşmadan metne kapsayıcı bellek eşleme, büyük bir dil modelinin bölümlerine eşlenir ve tüm dosyanın belleğe sığması *önerilir* , bu da ek 4-6 GB 'tır. Ayrıca, modellerin bellekte sayfalandığından kapsayıcının ilk çalışması daha uzun sürebilir.

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` kapsayıcı görüntüsünü al

Konuşma için kapsayıcı görüntüleri aşağıdaki Container Registry kullanılabilir.

# <a name="speech-to-texttabstt"></a>[Konuşmayı metne dönüştürme](#tab/stt)

| Kapsayıcı | Havuz |
|-----------|------------|
| Konuşmayı metne dönüştürme | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-texttabcstt"></a>[Özel Konuşma Tanıma metin](#tab/cstt)

| Kapsayıcı | Havuz |
|-----------|------------|
| Özel Konuşma Tanıma metin | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speechtabtts"></a>[Metin okuma](#tab/tts)

| Kapsayıcı | Havuz |
|-----------|------------|
| Metin okuma | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speechtabctts"></a>[Özel metin okuma](#tab/ctts)

| Kapsayıcı | Havuz |
|-----------|------------|
| Özel metin okuma | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Konuşma kapsayıcıları için Docker çekme

# <a name="speech-to-texttabstt"></a>[Konuşmayı metne dönüştürme](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Konuşmadan metne kapsayıcı için Docker çekme

Kapsayıcı önizlemesi kayıt defterinden bir kapsayıcı görüntüsünü indirmek için [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) komutunu kullanın.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> `latest` etiketi `en-US` yerel ayarı çeker. Daha fazla yerel ayar için bkz. [konuşmayı metne göre yerel ayarlar](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Konuşmayı metne göre yerel ayarlar

`latest` dışındaki tüm Etiketler aşağıdaki biçimdedir, burada `<culture>` yerel ayar kapsayıcısını gösterir:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

Aşağıdaki etiket, biçiminin bir örneğidir:

```
2.0.0-amd64-en-us-preview
```

**Konuşmadan metne** kapsayıcının desteklenen tüm yerel ayarları Için lütfen [konuşmadan metne görüntü etiketleri](../containers/container-image-tags.md#speech-to-text)bölümüne bakın.

# <a name="custom-speech-to-texttabcstt"></a>[Özel Konuşma Tanıma metin](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Özel Konuşma Tanıma metin kapsayıcısı için Docker Pull

Kapsayıcı önizlemesi kayıt defterinden bir kapsayıcı görüntüsünü indirmek için [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) komutunu kullanın.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> Özel konuşma kapsayıcıları için `locale` ve `voice` kapsayıcı tarafından alınan özel model tarafından belirlenir.

# <a name="text-to-speechtabtts"></a>[Metin okuma](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Metin okuma kapsayıcısı için Docker çekme

Kapsayıcı önizlemesi kayıt defterinden bir kapsayıcı görüntüsünü indirmek için [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) komutunu kullanın.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> `latest` etiketi `en-US` yerel ayarı ve `jessarus` sesi çeker. Daha fazla yerel ayar için bkz. [metin okuma yerel ayarları](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Metinden konuşmaya yerel ayarlar

`latest` dışındaki tüm Etiketler aşağıdaki biçimdedir; burada `<culture>` yerel ayarı gösterir ve `<voice>` kapsayıcının sesini gösterir:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

Aşağıdaki etiket, biçiminin bir örneğidir:

```
1.3.0-amd64-en-us-jessarus-preview
```

Desteklenen tüm yerel ayarlar ve **metin okuma** kapsayıcısının karşılık gelen sesleri için bkz. [metin okuma görüntü etiketleri](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> *Standart bir metinden konuşmaya* http gönderisi oluştururken, [konuşma birleştirme biçimlendirme dili (SSML)](speech-synthesis-markup.md) iletisi, `name` özniteliği olan bir `voice` öğesi gerektirir. Değer, ["kısa ad"](language-support.md#standard-voices)olarak da bilinen karşılık gelen kapsayıcı yerel ayarı ve sestir. Örneğin, `latest` etiketinde `en-US-JessaRUS`bir ses adı olacaktır.

# <a name="custom-text-to-speechtabctts"></a>[Özel metin okuma](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Özel metin okuma kapsayıcısı için Docker Pull

Kapsayıcı önizlemesi kayıt defterinden bir kapsayıcı görüntüsünü indirmek için [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) komutunu kullanın.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> Özel konuşma kapsayıcıları için `locale` ve `voice` kapsayıcı tarafından alınan özel model tarafından belirlenir.

***

## <a name="how-to-use-the-container"></a>Kapsayıcıyı kullanma

Kapsayıcı [ana bilgisayardan](#the-host-computer)olduktan sonra, kapsayıcında çalışmak için aşağıdaki işlemi kullanın.

1. [Kapsayıcıyı](#run-the-container-with-docker-run)gerekli faturalandırma ayarlarıyla çalıştırın. `docker run` komutuna daha fazla [örnek](speech-container-configuration.md#example-docker-run-commands) kullanılabilir.
1. [Kapsayıcının tahmin uç noktasını sorgulayın](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Kapsayıcıyı `docker run` ile çalıştırma

Kapsayıcıyı çalıştırmak için [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) komutunu kullanın. [ Ve](#gathering-required-parameters) değerlerini alma hakkında ayrıntılar için gerekli `{Endpoint_URI}`parametreleri`{API_Key}` toplama bölümüne bakın. `docker run` komutuna ek [örnekler](speech-container-configuration.md#example-docker-run-commands) de mevcuttur.

# <a name="speech-to-texttabstt"></a>[Konuşmayı metne dönüştürme](#tab/stt)

*Konuşmaya metin* kapsayıcısını çalıştırmak için aşağıdaki `docker run` komutunu yürütün.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Bu komut:

* Kapsayıcı görüntüsünden *konuşmaya metin* kapsayıcısı çalıştırır.
* 4 CPU çekirdeği ve 4 gigabayt (GB) bellek ayırır.
* TCP bağlantı noktası 5000 ' i gösterir ve kapsayıcı için bir sözde TTY ayırır.
* Kapsayıcıyı çıktıktan sonra otomatik olarak kaldırır. Kapsayıcı görüntüsü hala ana bilgisayarda kullanılabilir.

# <a name="custom-speech-to-texttabcstt"></a>[Özel Konuşma Tanıma metin](#tab/cstt)

*Özel konuşma tanıma metin* kapsayıcısı özel bir konuşma modeline bağlıdır. Özel bir model [özel konuşma portalı](https://speech.microsoft.com/customspeech)kullanılarak [eğitilmiş](how-to-custom-speech-train-model.md) olmalıdır. Kapsayıcıyı çalıştırmak için özel konuşma **MODELI kimliği** gereklidir. Özel konuşma portalının **eğitim** sayfasında bulunabilir. Özel konuşma portalından **eğitim** sayfasına gidin ve modeli seçin.
<br>

![Özel konuşma eğitimi sayfası](media/custom-speech/custom-speech-model-training.png)

`docker run` komutunun `ModelId` parametresine bağımsız değişken olarak kullanılacak **model kimliğini** edinin.
<br>

![Özel konuşma modeli ayrıntıları](media/custom-speech/custom-speech-model-details.png)

Aşağıdaki tablo çeşitli `docker run` parametrelerini ve bunlara karşılık gelen açıklamalarını temsil eder:

| Parametre | Açıklama |
|---------|---------|
| `{VOLUME_MOUNT}` | Docker 'ın özel modeli kalıcı hale getirmek için kullandığı Konak bilgisayar [birimi bağlama](https://docs.docker.com/storage/volumes/). Örneğin, *c sürücüsünün* konak makinede bulunduğu *c:\customspeech* . |
| `{MODEL_ID}` | Özel konuşma portalının **eğitim** sayfasından özel konuşma tanıma **model kimliği** . |
| `{ENDPOINT_URI}` | Uç nokta, ölçüm ve faturalandırma için gereklidir. Daha fazla bilgi için bkz. [gerekli parametreleri toplama](#gathering-required-parameters). |
| `{API_KEY}` | API anahtarı gereklidir. Daha fazla bilgi için bkz. [gerekli parametreleri toplama](#gathering-required-parameters). |

*Özel konuşma tanıma metin* kapsayıcısını çalıştırmak için aşağıdaki `docker run` komutunu yürütün:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Bu komut:

* Kapsayıcı görüntüsünden *özel konuşma tanıma metin* kapsayıcısı çalıştırır.
* 4 CPU çekirdeği ve 4 gigabayt (GB) bellek ayırır.
* *Özel konuşma tanıma metin* modelini birim girişi bağlamalarından yükler, örneğin *c:\customspeech*.
* TCP bağlantı noktası 5000 ' i gösterir ve kapsayıcı için bir sözde TTY ayırır.
* `ModelId` verilen modeli indirir (birim bağlama üzerinde bulunamazsa).
* Özel model daha önce indirildiyse, `ModelId` yok sayılır.
* Kapsayıcıyı çıktıktan sonra otomatik olarak kaldırır. Kapsayıcı görüntüsü hala ana bilgisayarda kullanılabilir.

# <a name="text-to-speechtabtts"></a>[Metin okuma](#tab/tts)

*Metin okuma* kapsayıcısını çalıştırmak için aşağıdaki `docker run` komutunu yürütün.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Bu komut:

* Kapsayıcı görüntüsünden *metinden konuşmaya* kapsayıcısı çalıştırır.
* 2 CPU çekirdeği ve bir gigabayt (GB) bellek ayırır.
* TCP bağlantı noktası 5000 ' i gösterir ve kapsayıcı için bir sözde TTY ayırır.
* Kapsayıcıyı çıktıktan sonra otomatik olarak kaldırır. Kapsayıcı görüntüsü hala ana bilgisayarda kullanılabilir.

# <a name="custom-text-to-speechtabctts"></a>[Özel metin okuma](#tab/ctts)

*Özel metin okuma* kapsayıcısı özel bir ses modeline dayanır. Özel bir modelin [özel ses portalı](https://aka.ms/custom-voice-portal)kullanılarak [eğitilli](how-to-custom-voice-create-voice.md) olması gerekir. Kapsayıcıyı çalıştırmak için özel ses **MODELI kimliği** gereklidir. Özel ses portalının **eğitim** sayfasında bulunabilir. Özel ses portalından **eğitim** sayfasına gidin ve modeli seçin.
<br>

![Özel ses eğitimi sayfası](media/custom-voice/custom-voice-model-training.png)

Docker Run komutunun `ModelId` parametresine bağımsız değişken olarak kullanılacak **model kimliğini** edinin.
<br>

![Özel ses modeli ayrıntıları](media/custom-voice/custom-voice-model-details.png)

Aşağıdaki tablo çeşitli `docker run` parametrelerini ve bunlara karşılık gelen açıklamalarını temsil eder:

| Parametre | Açıklama |
|---------|---------|
| `{VOLUME_MOUNT}` | Docker 'ın özel modeli kalıcı hale getirmek için kullandığı Konak bilgisayar [birimi bağlama](https://docs.docker.com/storage/volumes/). Örneğin, *c sürücüsünün* konak makinede bulunduğu *c:\customspeech* . |
| `{MODEL_ID}` | Özel ses portalının **eğitim** sayfasından özel konuşma tanıma **model kimliği** . |
| `{ENDPOINT_URI}` | Uç nokta, ölçüm ve faturalandırma için gereklidir. Daha fazla bilgi için bkz. [gerekli parametreleri toplama](#gathering-required-parameters). |
| `{API_KEY}` | API anahtarı gereklidir. Daha fazla bilgi için bkz. [gerekli parametreleri toplama](#gathering-required-parameters). |

*Özel metin okuma* kapsayıcısını çalıştırmak için aşağıdaki `docker run` komutunu yürütün:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Bu komut:

* Kapsayıcı görüntüsünden *özel bir metin okuma* kapsayıcısı çalıştırır.
* 2 CPU çekirdeği ve bir gigabayt (GB) bellek ayırır.
* Toplu giriş bağlamalarından *özel metin okuma* modelini yükler, örneğin *c:\customvoice*.
* TCP bağlantı noktası 5000 ' i gösterir ve kapsayıcı için bir sözde TTY ayırır.
* `ModelId` verilen modeli indirir (birim bağlama üzerinde bulunamazsa).
* Özel model daha önce indirildiyse, `ModelId` yok sayılır.
* Kapsayıcıyı çıktıktan sonra otomatik olarak kaldırır. Kapsayıcı görüntüsü hala ana bilgisayarda kullanılabilir.

***

> [!IMPORTANT]
> `Eula`, `Billing`, Ve `ApiKey` kapsayıcıyı çalıştırmak için seçenekler belirtilmelidir; Aksi takdirde, kapsayıcı başlatılamıyor.  Daha fazla bilgi için [faturalama](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Kapsayıcının tahmin uç noktasını sorgulama

| Kapsayıcı | Uç Nokta | Protokol |
|--|--|--|
| Konuşmayı metne dönüştürme | `ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1` | RW |
| Özel Konuşma Tanıma metin | `ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1` | RW |
| Metin okuma | `http://localhost:5000/speech/synthesize/cognitiveservices/v1` | HTTP |
| Özel metin okuma | `http://localhost:5000/speech/synthesize/cognitiveservices/v1` | HTTP |

WSS ve HTTPS protokollerini kullanma hakkında daha fazla bilgi için bkz. [kapsayıcı güvenliği](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Metin okuma veya özel metin okuma

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Birden çok kapsayıcıyı aynı konakta Çalıştır

Açığa çıkarılan bağlantı noktalarıyla birden çok kapsayıcı çalıştırmak istiyorsanız, her kapsayıcıyı farklı bir açığa çıkarılan bağlantı noktasıyla çalıştırdığınızdan emin olun. Örneğin, bağlantı noktası 5000 ' deki ilk kapsayıcıyı ve bağlantı noktası 5001 üzerindeki ikinci kapsayıcıyı çalıştırın.

Bu kapsayıcınızı ve KONAKTA çalışan farklı bir Azure bilişsel Hizmetler kapsayıcısını birlikte kullanabilirsiniz. Aynı bilişsel Hizmetler kapsayıcısının çalışan birden fazla kapsayıcınızı da kullanabilirsiniz.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Kapsayıcıyı durdur

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Sorun giderme

Kapsayıcıyı başlatırken veya çalıştırırken sorunlarla karşılaşabilirsiniz. Çıkış [bağlaması](speech-container-configuration.md#mount-settings) kullanın ve günlüğe kaydetmeyi etkinleştirin. Bunun yapılması, kapsayıcının sorunları giderirken faydalı olan günlük dosyaları oluşturmasına izin verir.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturalandırma

Konuşma kapsayıcıları, Azure hesabınızdaki bir *konuşma* kaynağını kullanarak faturalama bilgilerini Azure 'a gönderir.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Bu seçenekler hakkında daha fazla bilgi için bkz. [kapsayıcıları yapılandırma](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Özet

Bu makalede, konuşma kapsayıcılarını indirmek, yüklemek ve çalıştırmak için kavramları ve iş akışını öğrendiniz. Özet:

* Konuşma, çeşitli özellikleri kapsülleyerek Docker için dört Linux kapsayıcısı sağlar:
  * *Konuşmayı metne dönüştürme*
  * *Özel Konuşma Tanıma metin*
  * *Metin okuma*
  * *Özel metin okuma*
* Kapsayıcı görüntüleri Azure 'daki kapsayıcı kayıt defterinden indirilir.
* Docker kapsayıcı görüntüleri çalıştırın.
* Kapsayıcının ana bilgisayar URI 'sini belirterek, konuşma kapsayıcılarındaki işlemleri çağırmak için REST API veya SDK kullanabilirsiniz.
* Bir kapsayıcıyı örnekledikten sonra faturalandırma bilgilerini sağlamanız gerekir.

> [!IMPORTANT]
>  Bilişsel hizmetler kapsayıcıları, kullanım ölçümü için Azure'a bağlanmadan çalıştırmak için lisanslanmaz. Müşteriler, her zaman faturalandırma bilgileri ölçüm hizmeti ile iletişim kurmak kapsayıcıları etkinleştirmeniz gerekiyor. Bilişsel hizmetler kapsayıcılar, Microsoft müşteri verilerini (örneğin, görüntü veya metin analiz edilen) göndermeyin.

## <a name="next-steps"></a>Sonraki adımlar

* Yapılandırma ayarları için [kapsayıcıları](speech-container-configuration.md) yapılandırmayı gözden geçir
* [Kubernetes ve Held Ile konuşma hizmeti kapsayıcılarını nasıl kullanacağınızı](speech-container-howto-on-premises.md) öğrenin
* Daha fazla bilişsel [Hizmetler kapsayıcısı](../cognitive-services-container-support.md) kullanın
