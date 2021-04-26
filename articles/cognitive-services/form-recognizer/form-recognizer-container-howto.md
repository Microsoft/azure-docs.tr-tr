---
title: Form tanıyıcı için kapsayıcıyı yüklemek ve çalıştırmak
titleSuffix: Azure Cognitive Services
description: Bu makalede, form ve tablo verilerini ayrıştırmak için Azure bilişsel hizmetler formu tanıyıcı kapsayıcısının nasıl kullanılacağı açıklanmaktadır.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 1be8afb58b22435f4f43b2d6884332a38b7f1e11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467485"
---
# <a name="install-and-run-form-recognizer-containers-retiring"></a>Form tanıyıcı kapsayıcıları (devre dışı bırakma) yükleyip çalıştırın

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Azure form tanıyıcı, formlardan anahtar-değer çiftlerini ve tabloları tanımlamak ve ayıklamak için makine öğrenimi teknolojisini uygular. Değerleri ve tablo girişlerini anahtar-değer çiftleriyle ilişkilendirir ve ardından özgün dosyadaki ilişkileri içeren yapılandırılmış verileri çıkarır. 

Karmaşıklığı azaltmak ve özel bir form tanıyıcı modelini iş akışı Otomasyonu sürecinizi veya başka bir uygulamayla kolayca bütünleştirmek için, basit bir REST API kullanarak modeli çağırabilirsiniz. Yalnızca beş form belgesi gereklidir, bu sayede sonuçları hızlıca, doğru ve belirli içeriğinize göre kolayca elde edebilirsiniz. Ağır el ile müdahale veya kapsamlı veri bilimi uzmanlığı gerekli değildir. Veri etiketleme veya veri ek açıklaması gerektirmez.

| İşlev | Özellikler |
|----------|----------|
| Form Tanıma | <li>PDF, PNG ve JPG dosyalarını işler<li>Aynı düzene sahip en az beş form ile özel modeller traıns <li>Anahtar-değer çiftlerini ve tablo bilgilerini ayıklar <li>Form içindeki görüntülerden yazdırılmış metni algılamak ve ayıklamak için Azure bilişsel hizmetler Görüntü İşleme API'si Metin Tanıma özelliğini kullanır<li>Ek açıklama veya etiketleme gerektirmez |

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/cognitive-services/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Form tanıyıcı kapsayıcılarını kullanmadan önce, aşağıdaki önkoşulları karşılamanız gerekir:

| Gerekli | Amaç |
|----------|---------|
| Docker altyapısı | Bir [ana bilgisayarda](#the-host-computer)Docker altyapısının yüklü olması gerekir. Docker, [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ve [Linux](https://docs.docker.com/engine/installation/#supported-platforms) üzerinde Docker ortamını yapılandıran paketler sağlar. Docker ve kapsayıcı temel bilgileri ile ilgili giriş yapmak için [Docker’a genel bakış](https://docs.docker.com/engine/docker-overview/) bölümüne bakın.<br><br> Kapsayıcıların Azure 'a bağlanıp faturalandırma verilerini göndermesini sağlamak için Docker yapılandırılmalıdır. <br><br> Windows 'da Docker 'ın de Linux kapsayıcılarını destekleyecek şekilde yapılandırılması gerekir.<br><br> |
| Docker ile benzerlik | Kayıt defterleri, depolar, kapsayıcılar ve kapsayıcı görüntüleri gibi Docker kavramlarından ve temel komutlar hakkında bilgi sahibi olmanız gerekir `docker` . |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) 'yi konağa yükler. |
| Görüntü İşleme API'si kaynağı | Taranmış belge ve görüntüleri işlemek için bir Görüntü İşleme kaynağınız olması gerekir. Metin Tanıma özelliğine bir Azure kaynağı (REST API veya SDK) ya da bilişsel hizmetler-tanı- *metin* [kapsayıcısı](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)olarak erişebilirsiniz. Olağan faturalandırma ücretleri uygulanır. <br><br>Görüntü İşleme kaynağınız için hem API anahtarını hem de uç noktalarını geçirin (Azure bulut veya bilişsel hizmetler kapsayıcısı). Bu API anahtarını ve uç noktayı **{COMPUTER_VISION_API_KEY}** ve **{COMPUTER_VISION_ENDPOINT_URI}** olarak kullanın.<br><br> Bilişsel *Hizmetler-tanı-metin* kapsayıcısını kullanırsanız, şunları yaptığınızdan emin olun:<br><br>Form tanıyıcı kapsayıcısı için Görüntü İşleme anahtarınız, bilişsel `docker run` *Hizmetler-Recognize-metin* kapsayıcısının görüntü işleme komutunda belirtilen anahtardır.<br>Faturanızı, kapsayıcının uç noktasıdır (örneğin, `http://localhost:5000` ). Hem Görüntü İşleme kapsayıcısını hem de form tanıyıcı kapsayıcısını aynı konakta kullanıyorsanız, her ikisi de varsayılan bağlantı noktası olan *5000* ile başlatılamaz. |
| Form tanıyıcı kaynağı | Bu kapsayıcıları kullanmak için, şunları yapmanız gerekir:<br><br>İlişkili API anahtarını ve uç nokta URI 'sini almak için bir Azure **form tanıyıcı** kaynağı. Her iki değer de Azure portal **form tanıyıcıya** genel bakış ve anahtarlar sayfalarında bulunur ve kapsayıcının başlaması için her iki değer de gereklidir.<br><br>**{FORM_RECOGNIZER_API_KEY}**: anahtarlar sayfasında kullanılabilir iki kaynak anahtardan biri<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}**: Genel Bakış sayfasında belirtilen bitiş noktası |

> [!NOTE]
> Görüntü İşleme kaynak adı, kısa çizgi `-` veya diğer özel karakterler olmadan tek bir sözcük olmalıdır. Bu kısıtlama, form tanıyıcı ve Metin Tanıma kapsayıcı uyumluluğunu sağlamak için kullanılır.

## <a name="gathering-required-parameters"></a>Gerekli parametreler toplanıyor

Gerekli olan tüm bilişsel hizmetler için üç birincil parametre vardır. Son Kullanıcı Lisans Sözleşmesi 'nin (EULA) değeri ile mevcut olması gerekir `accept` . Ayrıca, hem bir uç nokta URL 'SI hem de API anahtarı gereklidir.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>Uç nokta URI 'SI `{COMPUTER_VISION_ENDPOINT_URI}` ve `{FORM_RECOGNIZER_ENDPOINT_URI}`

**Uç nokta** URI değeri, karşılık gelen bilişsel hizmet kaynağının Azure Portal *genel bakış* sayfasında bulunur. *Genel bakış* sayfasına gidin, uç noktanın üzerine gelin ve bir `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> simge görüntülenir. Gerektiğinde kopyalayın ve kullanın.

![Uç nokta URI 'sini daha sonra kullanılmak üzere toplayın](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Anahtarlar `{COMPUTER_VISION_API_KEY}` ve `{FORM_RECOGNIZER_API_KEY}`

Bu anahtar, kapsayıcıyı başlatmak için kullanılır ve ilgili bilişsel hizmet kaynağının Azure portal tuşları sayfasında kullanılabilir. *Anahtarlar* sayfasına gidin ve `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> simgesine tıklayın.

![Daha sonra kullanmak üzere iki anahtardan birini al](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> Bu abonelik anahtarları bilişsel hizmet API 'nize erişmek için kullanılır. Anahtarlarınızı paylaşmayın. Azure Key Vault kullanarak güvenli bir şekilde depolayın. Ayrıca, bu anahtarların düzenli olarak yeniden oluşturulması önerilir. API çağrısı yapmak için yalnızca bir anahtar gereklidir. İlk anahtarı yeniden oluştururken, hizmete devam eden erişim için ikinci anahtarı kullanabilirsiniz.

## <a name="the-host-computer"></a>Ana bilgisayar

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Kapsayıcı gereksinimleri ve önerileri

Her form tanıyıcı kapsayıcısı için ayrılacak en düşük ve önerilen CPU çekirdekleri ve bellek aşağıdaki tabloda açıklanmıştır:

| Kapsayıcı | Minimum | Önerilen |
|-----------|---------|-------------|
| Form Tanıma | 2 çekirdek, 4 GB bellek | 4 çekirdek, 8 GB bellek |
| Metin Tanıma | 1 çekirdek, 8 GB bellek | 2 çekirdek, 8 GB bellek |

* Her çekirdek en az 2,6 gigahertz (GHz) veya daha hızlı olmalıdır.
* Çekirdek ve bellek, `--cpus` `--memory` komutunun bir parçası olarak kullanılan ve ayarlarına karşılık gelir `docker run` .

> [!Note]
> En düşük ve önerilen değerler, konak makine kaynakları *değil* , Docker sınırlarına dayanır.

Form tanıyıcı ve Metin Tanıma kapsayıcılarının her ikisi de gerekir, lütfen **metin tanıma** kapsayıcısının [Bu makalenin dışında ayrıntılı](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull) olduğunu unutmayın.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Kapsayıcıyı kullanma

Kapsayıcı [ana bilgisayar](#the-host-computer)üzerinde olduktan sonra, kapsayıcında çalışmak için aşağıdaki işlemi kullanın.

1. [Kapsayıcıyı](#run-the-container-by-using-the-docker-run-command)gerekli faturalandırma ayarlarıyla çalıştırın. Komuta [](form-recognizer-container-configuration.md#example-docker-run-commands) daha fazla örnek `docker run` kullanılabilir.
1. [Kapsayıcının tahmin uç noktasını sorgulayın](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Docker Run komutunu kullanarak kapsayıcıyı çalıştırma

Kapsayıcıyı çalıştırmak için [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) komutunu kullanın. , Ve değerlerini alma hakkında ayrıntılar için [gerekli parametreleri toplama](#gathering-required-parameters) bölümüne bakın `{COMPUTER_VISION_ENDPOINT_URI}` `{COMPUTER_VISION_API_KEY}` `{FORM_RECOGNIZER_ENDPOINT_URI}` `{FORM_RECOGNIZER_API_KEY}` .

Komut [örnekleri](form-recognizer-container-configuration.md#example-docker-run-commands) `docker run` mevcuttur.

### <a name="form-recognizer"></a>Form Tanıma

> [!NOTE]
> Bu örneklerde için kullanılan dizinler `--mount` Windows Dizin yollarıdır. Linux veya macOS kullanıyorsanız, ortamınız için parametreyi değiştirin. 

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Şu komut:

* Kapsayıcı görüntüsünden bir form tanıyıcı kapsayıcısı çalıştırır.
* 2 CPU çekirdeği ve 8 gigabayt (GB) bellek ayırır.
* TCP bağlantı noktası 5000 ' i gösterir ve kapsayıcı için bir sözde TTY ayırır.
* Kapsayıcıyı çıktıktan sonra otomatik olarak kaldırır. Kapsayıcı görüntüsü hala ana bilgisayarda kullanılabilir.
* Kapsayıcıya bir/input ve/Output birimi takar.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Ayrı kapsayıcıları ayrı Docker Run komutları olarak çalıştır

Yerel olarak aynı konakta barındırılan form tanıyıcı ve metin tanıyıcı birleşimi için aşağıdaki iki örnek Docker CLı komutunu kullanın:

İlk kapsayıcıyı 5000 numaralı bağlantı noktasında çalıştırın. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

İkinci kapsayıcıyı 5001 numaralı bağlantı noktasında çalıştırın.

```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Sonraki her kapsayıcı farklı bir bağlantı noktasında olmalıdır. 

### <a name="run-separate-containers-with-docker-compose"></a>Docker Compose ile ayrı kapsayıcılar çalıştırma

Yerel olarak aynı konakta barındırılan form tanıyıcı ve metin tanıyıcı birleşimi için aşağıdaki örneğe bakın Docker Compose YAML dosyası. Metin tanıyıcı, `{COMPUTER_VISION_API_KEY}` ve kapsayıcıları için aynı olmalıdır `formrecognizer` `ocr` . , `{COMPUTER_VISION_ENDPOINT_URI}` `ocr` `formrecognizer` Kapsayıcı `ocr` adı ve bağlantı noktasını kullandığından yalnızca kapsayıcıda kullanılır. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: "{COMPUTER_VISION_API_KEY}"

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"
```

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Kapsayıcı çalıştırmak için, ve ve seçeneklerinin yanı sıra `FormRecognizer:ComputerVisionApiKey` ve `FormRecognizer:ComputerVisionEndpointUri` seçenekleri belirtilmelidir; Aksi takdirde kapsayıcı başlatılmaz. Daha fazla bilgi için bkz. [faturalandırma](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Kapsayıcının tahmin uç noktasını sorgulama

|Kapsayıcı|Uç Nokta|
|--|--|
|Form-tanıyıcı|http://localhost:5000

### <a name="form-recognizer"></a>Form Tanıma

Kapsayıcı, [form tanıyıcı HIZMETLERI SDK belgeleri](./index.yml)aracılığıyla erişebileceğiniz WebSocket tabanlı sorgu uç noktası API 'leri sağlar.

Varsayılan olarak, tanıyıcı SDK 'Sı çevrimiçi hizmetler kullanır. Kapsayıcıyı kullanmak için başlatma yöntemini değiştirmeniz gerekir. Aşağıdaki örneklere bakın.

#### <a name="for-c"></a>C için #

Bu Azure-Cloud başlatma çağrısını kullanarak değiştirin:

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
kapsayıcı uç noktasını kullanan bu çağrıya:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>Python için

Bu Azure-Cloud başlatma çağrısını kullanarak değiştirin:

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

kapsayıcı uç noktasını kullanan bu çağrıya:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Form Tanıma

Kapsayıcı, [form tanıyıcı API] başvuru sayfasında bulabileceğiniz REST uç nokta API 'Leri sağlar https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) .


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Kapsayıcıyı durdurma

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Sorun giderme

Kapsayıcıyı bir çıkış [bağlaması](form-recognizer-container-configuration.md#mount-settings) ve günlüğü etkin olarak çalıştırırsanız kapsayıcı, kapsayıcıyı başlatırken veya çalıştırırken oluşan sorunları gidermek için yararlı olan günlük dosyaları oluşturur.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturalandırma

Form tanıyıcı kapsayıcıları, Azure hesabınızda bir _form tanıyıcı_ kaynağı kullanarak faturalandırma bilgilerini Azure 'a gönderir.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Bu seçenekler hakkında daha fazla bilgi için bkz. [kapsayıcıları yapılandırma](form-recognizer-container-configuration.md).

## <a name="summary"></a>Özet

Bu makalede, form tanıyıcı kapsayıcıları indirmek, yüklemek ve çalıştırmak için kavramlar ve iş akışı öğrendiniz. Özet:

* Form tanıyıcı, Docker için bir Linux kapsayıcısı sağlar.
* Kapsayıcı görüntüleri Azure 'daki özel kapsayıcı kayıt defterinden indirilir.
* Kapsayıcı görüntüleri Docker 'da çalışır.
* Kapsayıcının ana bilgisayar URI 'sini belirterek, form tanıyıcı kapsayıcısında işlemleri çağırmak için REST API ya da REST SDK kullanabilirsiniz.
* Bir kapsayıcıyı örneklediğinizde faturalandırma bilgilerini belirtmeniz gerekir.

> [!IMPORTANT]
>  Bilişsel hizmetler kapsayıcıları, ölçüm için Azure 'a bağlı kalmadan çalıştırılmak üzere lisanslanmaz. Müşterilerin, ödeme bilgilerini her zaman ölçüm hizmetiyle iletişimine olanak tanımak için kapsayıcıların etkinleştirilmesi gerekir. Bilişsel hizmetler kapsayıcıları, müşteri verilerini (örneğin, çözümlenmekte olan resim veya metin) Microsoft 'a göndermez.

## <a name="next-steps"></a>Sonraki adımlar

* Yapılandırma ayarları için [kapsayıcıları yapılandırma](form-recognizer-container-configuration.md) konusunu gözden geçirin.
* Daha fazla bilişsel [Hizmetler kapsayıcısı](../cognitive-services-container-support.md)kullanın.