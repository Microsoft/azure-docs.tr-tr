---
title: Docker kapsayıcı ayarları-LUSıS
titleSuffix: Azure Cognitive Services
description: LUSıS kapsayıcı çalışma zamanı ortamı, `docker run` komut bağımsız değişkenleri kullanılarak yapılandırılır. LUO 'NUN birkaç isteğe bağlı ayarı ve birkaç isteğe bağlı ayarı vardır.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a4f2b07edc6c290fa030621a4dc400ab50890bba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96001213"
---
# <a name="configure-language-understanding-docker-containers"></a>Language Understanding Docker kapsayıcılarını yapılandırma 

**Language Understanding** (lusıs) kapsayıcı çalışma zamanı ortamı, `docker run` komut bağımsız değişkenleri kullanılarak yapılandırılır. LUO 'NUN birkaç isteğe bağlı ayarı ve birkaç isteğe bağlı ayarı vardır. Birkaç komuta [örnek](#example-docker-run-commands) vardır. Kapsayıcıya özgü ayarlar giriş [bağlama ayarları](#mount-settings) ve faturalandırma ayarlardır. 

## <a name="configuration-settings"></a>Yapılandırma ayarları

Bu kapsayıcı aşağıdaki yapılandırma ayarlarına sahiptir:

|Gerekli|Ayar|Amaç|
|--|--|--|
|Yes|[ApiKey](#apikey-setting)|Faturalandırma bilgilerini izlemek için kullanılır.|
|No|[ApplicationInsights](#applicationinsights-setting)|, Kapsayıcınıza [Azure Application Insights](/azure/application-insights) telemetri desteği eklemenize olanak tanır.|
|Yes|[Faturalandırma](#billing-setting)|Azure üzerindeki hizmet kaynağının uç nokta URI 'sini belirtir.|
|Yes|[Sözleşmesi](#eula-setting)| Kapsayıcının lisansını kabul ettiğinizi gösterir.|
|No|[Fluentd](#fluentd-settings)|Günlük ve isteğe bağlı olarak ölçüm verilerini Floentd sunucusuna yazın.|
|No|[Http proxy 'Si](#http-proxy-credentials-settings)|Giden istekler oluşturmak için bir HTTP proxy 'si yapılandırın.|
|No|[Günlüğe kaydetme](#logging-settings)|, Kapsayıcınız için ASP.NET Core günlük desteği sağlar. |
|Yes|[Kullan](#mount-settings)|Ana bilgisayardan kapsayıcıya ve kapsayıcıdan, ana bilgisayara geri veri okuma ve yazma.|

> [!IMPORTANT]
> [`ApiKey`](#apikey-setting), [`Billing`](#billing-setting) Ve [`Eula`](#eula-setting) ayarları birlikte kullanılır ve üçü için geçerli değerler sağlamanız gerekir; Aksi takdirde Kapsayıcınız başlatılmaz. Bir kapsayıcı oluşturmak için bu yapılandırma ayarlarını kullanma hakkında daha fazla bilgi için bkz. [faturalandırma](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>ApiKey ayarı

Bu `ApiKey` ayar, kapsayıcının fatura bilgilerini izlemek için kullanılan Azure Kaynak anahtarını belirtir. ApiKey için bir değer belirtmeniz gerekir ve değerin yapılandırma ayarı için belirtilen bilişsel _Hizmetler_ kaynağı için geçerli bir anahtar olması gerekir [`Billing`](#billing-setting) .

Bu ayar aşağıdaki konumlarda bulunabilir:

* Azure portal: bilişsel **Hizmetler** kaynak yönetimi, **anahtarlar** altında
* LUO Portalı: **anahtarlar ve uç nokta ayarları** sayfası. 

Başlangıç anahtarını veya yazma anahtarını kullanmayın. 

## <a name="applicationinsights-setting"></a>ApplicationInsights ayarı

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Faturalandırma ayarı

`Billing`Ayar, Azure üzerinde bulunan bilişsel _Hizmetler_ kaynağının, kapsayıcının fatura bilgilerini ölçmek için kullanılan uç nokta URI 'sini belirtir. Bu yapılandırma ayarı için bir değer belirtmeniz gerekir ve Azure 'daki bilişsel _Hizmetler_ kaynağı için değer geçerli bir uç nokta URI 'si olmalıdır. Kapsayıcı her 10 ila 15 dakikada bir kullanım raporu sağlar.

Bu ayar aşağıdaki konumlarda bulunabilir:

* Azure portal: bilişsel **Hizmetler** genel bakış, etiketli `Endpoint`
* LUO Portalı: uç nokta URI 'sinin bir parçası olarak **anahtarlar ve uç nokta ayarları** sayfası.

| Gerekli | Name | Veri türü | Açıklama |
|----------|------|-----------|-------------|
| Evet      | `Billing` | string | Faturalama uç noktası URI 'SI. Faturalandırma URI 'sini alma hakkında daha fazla bilgi için bkz. [gerekli parametreleri toplama](luis-container-howto.md#gathering-required-parameters). Daha fazla bilgi ve bölgesel uç noktaların tamamen listesi için bkz. bilişsel [Hizmetler Için özel alt etki alanı adları](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>EULA ayarı

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Akışkan entd ayarları

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP proxy kimlik bilgileri ayarları

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Oturum açma ayarları
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Bağlama ayarları

Kapsayıcıya ve kapsayıcılardan veri okumak ve buradan veri yazmak için BIND bağlama kullanın. `--mount` [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) komutunda seçeneğini belirterek bir giriş bağlama veya çıkış bağlama belirtebilirsiniz. 

LUSıS kapsayıcısı, eğitim veya hizmet verilerini depolamak için giriş veya çıkış takar. 

Konak bağlama konumunun tam sözdizimi, ana bilgisayar işletim sistemine bağlı olarak değişir. Ayrıca, Docker hizmeti hesabı ve konak bağlama konumu izinleri tarafından kullanılan izinler arasındaki bir çakışma nedeniyle [ana bilgisayarın](luis-container-howto.md#the-host-computer)bağlama konumu erişilebilir olmayabilir. 

Aşağıdaki tabloda desteklenen ayarlar açıklanmaktadır.

|Gerekli| Name | Veri türü | Açıklama |
|-------|------|-----------|-------------|
|Evet| `Input` | Dize | Giriş bağlama hedefi. `/input` varsayılan değerdir. Bu, LUSıS paket dosyalarının konumudur. <br><br>Örnek:<br>`--mount type=bind,src=c:\input,target=/input`|
|Hayır| `Output` | Dize | Çıkış bağlama hedefi. `/output` varsayılan değerdir. Bu, günlüklerin konumudur. Bu, LUSıS sorgu günlüklerini ve kapsayıcı günlüklerini içerir. <br><br>Örnek:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Örnek Docker Run komutları

Aşağıdaki örneklerde, komutlarının nasıl yazılacağını ve kullanılacağını göstermek için yapılandırma ayarları kullanılır `docker run` .  Çalışan bir kez, kapsayıcıyı [durduruncaya](luis-container-howto.md#stop-the-container) kadar çalışmaya devam eder.

* Bu örnekler, `C:` Windows 'ta herhangi bir izin çakışmasını önlemek için dizini sürücüden kullanır. Giriş dizini olarak belirli bir dizin kullanmanız gerekiyorsa, Docker hizmeti iznini vermeniz gerekebilir. 
* Docker Kapsayıcıları hakkında bilginiz yoksa bağımsız değişkenlerin sırasını değiştirmeyin.
* Farklı bir işletim sistemi kullanıyorsanız, sistem için doğru konsol/Terminal, bağlama için klasör söz dizimi ve satır devamlılık karakteri kullanın. Bu örneklerde bir Windows konsolunun satır devamlılık karakteriyle birlikte varsayılmaktadır `^` . Kapsayıcı bir Linux işletim sistemi olduğundan, hedef bağlama bir Linux stili klasör söz dizimini kullanır.

{_Argument_name_} değerini kendi değerlerinizle değiştirin:

| Yer tutucu | Değer | Biçim veya örnek |
|-------------|-------|---|
| **{API_KEY}** | `LUIS`Azure anahtarları sayfasında kaynağın uç nokta anahtarı `LUIS` . | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Faturalandırma uç noktası değeri, Azure `LUIS` Genel Bakış sayfasında bulunur.| Açık örnekler için [gerekli parametreleri toplama](luis-container-howto.md#gathering-required-parameters) konusuna bakın. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Kapsayıcıyı çalıştırmak için, ve seçenekleri belirtilmelidir; Aksi takdirde kapsayıcı başlatılmaz. Daha fazla bilgi için bkz. [faturalandırma](luis-container-howto.md#billing).
> ApiKey **değeri,** Lua portalındaki anahtarlar ve uç noktalar sayfasından ve Azure `Cognitive Services` kaynak anahtarları sayfasında da kullanılabilir. 

### <a name="basic-example"></a>Temel örnek

Aşağıdaki örnek, kapsayıcıyı çalıştırmak için mümkün olan en az bağımsız değişkenlere sahiptir:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>ApplicationInsights örneği

Aşağıdaki örnek, kapsayıcı çalışırken Application Insights telemetri göndermek için ApplicationInsights bağımsız değişkenini ayarlar:

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>Günlüğe kaydetme örneği 

Aşağıdaki komut, `Logging:Console:LogLevel` günlük düzeyini olarak yapılandırmak için günlüğe kaydetme düzeyini ayarlar [`Information`](https://msdn.microsoft.com) . 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Sonraki adımlar

* [Kapsayıcıları yüklemeyi ve çalıştırmayı](luis-container-howto.md) inceleyin
* LUSıS işlevleriyle ilgili sorunları çözmek için [sorun giderme](troubleshooting.md) bölümüne bakın.
* Daha fazla bilişsel [Hizmetler kapsayıcısı](../cognitive-services-container-support.md) kullanın