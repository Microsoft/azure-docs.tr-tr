---
title: Form tanıyıcı için kapsayıcı yapılandırma
titleSuffix: Azure Cognitive Services
description: Form tanıyıcı kapsayıcısının form ve tablo verilerini ayrıştırmak için nasıl yapılandırılacağını öğrenin.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: 324b70fc810acc4faba4f488f821049f7eb0875e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "86538012"
---
# <a name="configure-form-recognizer-containers"></a>Form tanıyıcı kapsayıcılarını yapılandırma

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Azure form tanıyıcı kapsayıcıları kullanarak, hem güçlü bulut özellikleri hem de kenar yerinin avantajlarından yararlanmak için en iyi duruma getirilmiş bir uygulama mimarisi oluşturabilirsiniz.

Form tanıyıcı kapsayıcısı çalışma zamanı ortamını `docker run` komut bağımsız değişkenlerini kullanarak yapılandırırsınız. Bu kapsayıcıda birkaç gerekli ayar ve isteğe bağlı birkaç ayar vardır. Birkaç örnek için, ["örnek Docker Run komutları"](#example-docker-run-commands) bölümüne bakın. Kapsayıcıya özgü ayarlar faturalandırma ayarlardır.

## <a name="configuration-settings"></a>Yapılandırma ayarları

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) Ve [`Eula`](#eula-setting) ayarları birlikte kullanılır. Üç ayar için geçerli değerler sağlamalısınız; Aksi takdirde, Kapsayıcınız başlatılmaz. Bir kapsayıcı oluşturmak için bu yapılandırma ayarlarını kullanma hakkında daha fazla bilgi için bkz. [faturalandırma](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey yapılandırma ayarı

`ApiKey`Ayar, kapsayıcının fatura bilgilerini izlemek için kullanılan Azure Kaynak anahtarını belirtir. ApiKey değeri, "Faturalandırma yapılandırma ayarı" bölümünde için belirtilen _form tanıyıcı_ kaynağı için geçerli bir anahtar olmalıdır `Billing` .

Bu ayarı, Azure portal, **anahtarlar** altında, **form tanıyıcı kaynak yönetimi**' nde bulabilirsiniz.

## <a name="applicationinsights-setting"></a>ApplicationInsights ayarı

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Faturalandırma yapılandırma ayarı

`Billing`Ayar, Azure 'da kapsayıcı için fatura bilgilerini ölçmek için kullanılan _form tanıyıcı_ KAYNAĞıNıN uç nokta URI 'sini belirtir. Bu yapılandırma ayarının değeri, Azure 'daki bir _form tanıyıcı_ kaynağı için geçerli bir uç nokta URI 'si olmalıdır. Kapsayıcı her 10 ila 15 dakikada bir kullanım raporu sağlar.

Bu ayarı, Azure portal, **uç nokta** altında **form tanıyıcıya genel bakış**' da bulabilirsiniz.

|Gerekli| Name | Veri türü | Açıklama |
|--|------|-----------|-------------|
|Evet| `Billing` | Dize | Faturalama uç noktası URI 'SI. Faturalandırma URI 'sini alma hakkında daha fazla bilgi için bkz. [gerekli parametreleri toplama](form-recognizer-container-howto.md#gathering-required-parameters). Daha fazla bilgi ve bölgesel uç noktaların tamamen listesi için bkz. bilişsel [Hizmetler Için özel alt etki alanı adları](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>EULA ayarı

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Akışkan entd ayarları

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP proxy kimlik bilgileri ayarları

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Oturum açma ayarları

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Bağlama ayarları

Kapsayıcıya ve kapsayıcılardan veri okumak ve buradan veri yazmak için BIND bağlama kullanın. Komutta seçeneğini belirleyerek bir giriş bağlama veya çıkış bağlama belirtebilirsiniz `--mount` . [ `docker run` ](https://docs.docker.com/engine/reference/commandline/run/)

Form tanıyıcı kapsayıcısı, bir giriş bağlama ve çıkış bağlama gerektirir. Giriş bağlama salt okunurdur ve eğitim ve Puanlama için kullanılan verilere erişim için gereklidir. Çıkış bağlaması yazılabilir olmalıdır ve modelleri ve geçici verileri depolamak için kullanılır.

Konak bağlama konumunun tam sözdizimi, ana bilgisayar işletim sistemine bağlı olarak değişir. Ayrıca, Docker hizmet hesabı izinleri ile konak bağlama konumu izinleri arasındaki bir çakışma nedeniyle [ana bilgisayarın](form-recognizer-container-howto.md#the-host-computer) bağlama konumuna erişilemiyor olabilir.

|İsteğe Bağlı| Name | Veri türü | Açıklama |
|-------|------|-----------|-------------|
|Gerekli| `Input` | Dize | Giriş bağlama hedefi. `/input` varsayılan değerdir.    <br><br>Örnek:<br>`--mount type=bind,src=c:\input,target=/input`|
|Gerekli| `Output` | Dize | Çıkış bağlama hedefi. `/output` varsayılan değerdir.  <br><br>Örnek:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Örnek Docker Run komutları

Aşağıdaki örneklerde, komutlarının nasıl yazılacağını ve kullanılacağını göstermek için yapılandırma ayarları kullanılır `docker run` . Çalıştığında, kapsayıcı siz [durduruncaya](form-recognizer-container-howto.md#stop-the-container)kadar çalışmaya devam eder.

* **Satır devamlılık karakteri**: aşağıdaki bölümlerdeki Docker komutları, \\ satır devamlılık karakteri olarak bir ters eğik çizgi () kullanır. Ana bilgisayar işletim sisteminizin gereksinimlerine bağlı olarak bu karakteri değiştirin veya kaldırın.
* **Bağımsız değişken sırası**: Docker Kapsayıcıları hakkında bilgi sahibi olmadığınız müddetçe bağımsız değişkenlerin sırasını değiştirmeyin.

Aşağıdaki tablodaki {_argument_name_} değerlerini kendi değerlerinizle değiştirin:

| Yer tutucu | Değer |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | Kapsayıcıyı başlatmak için kullanılan anahtar. Azure portal form tanıyıcı anahtarları sayfasında kullanılabilir. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | Faturalandırma uç noktası URI değeri Azure portal form tanıyıcıya Genel Bakış sayfasında bulunur.|
| **{COMPUTER_VISION_API_KEY}** | Anahtar, Azure portal Görüntü İşleme API'si anahtarlar sayfasında kullanılabilir.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | Faturalama uç noktası. Bulut tabanlı bir Görüntü İşleme kaynağı kullanıyorsanız URI değeri, Azure portal Görüntü İşleme API'si Genel Bakış sayfasında kullanılabilir. Bilişsel Hizmetler- *tanı-metin* kapsayıcısı kullanıyorsanız, komutta kapsayıcıya geçirilen faturalandırma uç nokta URL 'sini kullanın `docker run` . |

Bu değerleri alma hakkında ayrıntılar için bkz. [gerekli parametreleri toplama](form-recognizer-container-howto.md#gathering-required-parameters) .

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Kapsayıcıyı çalıştırmak için,, `Eula` `Billing` ve `ApiKey` seçeneklerini belirtin; Aksi takdirde kapsayıcı başlatılmaz. Daha fazla bilgi için bkz. [faturalandırma](#billing-configuration-setting).

## <a name="form-recognizer-container-docker-examples"></a>Form tanıyıcı kapsayıcısı Docker örnekleri

Aşağıdaki Docker örnekleri form tanıyıcı kapsayıcısı içindir.

### <a name="basic-example-for-form-recognizer"></a>Form tanıyıcı için temel örnek

```Docker
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

### <a name="logging-example-for-form-recognizer"></a>Form tanıyıcı için günlüğe kaydetme örneği

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Sonraki adımlar

* [Kapsayıcıları yüklemeyi ve çalıştırmayı](form-recognizer-container-howto.md)gözden geçirin.
