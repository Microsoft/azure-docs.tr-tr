---
title: Anomali algılayıcı API 'SI için kapsayıcı yapılandırma
titleSuffix: Azure Cognitive Services
description: Anomali algılayıcı API kapsayıcısı çalışma zamanı ortamı, `docker run` komut bağımsız değişkenleri kullanılarak yapılandırılır. Bu kapsayıcıda bazı gerekli ayarlar ve bazı isteğe bağlı ayarlar vardır.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mbullwin
ms.openlocfilehash: c175a52259e9cfe5b4d03ce0279bbe24d16a48ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94363723"
---
# <a name="configure-anomaly-detector-containers"></a>Anomali Algılayıcısı kapsayıcılarını yapılandırma

**Anomali algılayıcı** kapsayıcı çalışma zamanı ortamı, `docker run` komut bağımsız değişkenleri kullanılarak yapılandırılır. Bu kapsayıcıda bazı gerekli ayarlar ve bazı isteğe bağlı ayarlar vardır. Birkaç komuta [örnek](#example-docker-run-commands) vardır. Kapsayıcıya özgü ayarlar faturalandırma ayarlardır. 

## <a name="configuration-settings"></a>Yapılandırma ayarları

Bu kapsayıcı aşağıdaki yapılandırma ayarlarına sahiptir:

|Gerekli|Ayar|Amaç|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|Faturalandırma bilgilerini izlemek için kullanılır.|
|No|[ApplicationInsights](#applicationinsights-setting)|, Kapsayıcınıza [Azure Application Insights](/azure/application-insights) telemetri desteği eklemenize olanak tanır.|
|Yes|[Faturalandırma](#billing-configuration-setting)|Azure üzerindeki hizmet kaynağının uç nokta URI 'sini belirtir.|
|Yes|[Sözleşmesi](#eula-setting)| Kapsayıcının lisansını kabul ettiğinizi gösterir.|
|No|[Fluentd](#fluentd-settings)|Günlük ve isteğe bağlı olarak ölçüm verilerini Floentd sunucusuna yazın.|
|No|[Http proxy 'Si](#http-proxy-credentials-settings)|Giden istekler oluşturmak için bir HTTP proxy 'si yapılandırın.|
|No|[Günlüğe kaydetme](#logging-settings)|, Kapsayıcınız için ASP.NET Core günlük desteği sağlar. |
|No|[Kullan](#mount-settings)|Ana bilgisayardan kapsayıcıya ve kapsayıcıdan, ana bilgisayara geri veri okuma ve yazma.|

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) Ve [`Eula`](#eula-setting) ayarları birlikte kullanılır ve üçü için geçerli değerler sağlamanız gerekir; Aksi takdirde Kapsayıcınız başlatılmaz. Bir kapsayıcı oluşturmak için bu yapılandırma ayarlarını kullanma hakkında daha fazla bilgi için bkz. [faturalandırma](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey yapılandırma ayarı

Bu `ApiKey` ayar, kapsayıcının fatura bilgilerini izlemek için kullanılan Azure Kaynak anahtarını belirtir. ApiKey için bir değer belirtmeniz gerekir ve değerin yapılandırma ayarı için belirtilen _anomali algılayıcısı_ kaynağı için geçerli bir anahtar olması gerekir [`Billing`](#billing-configuration-setting) .

Bu ayar aşağıdaki yerde bulunabilir:

* Azure portal: **anomali algılayıcısının** kaynak yönetimi, **anahtarlar** altında

## <a name="applicationinsights-setting"></a>ApplicationInsights ayarı

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Faturalandırma yapılandırma ayarı

Bu `Billing` ayar, kapsayıcının fatura bilgilerini ölçmek için kullanılan Azure 'Daki _anomali algılayıcısı_ KAYNAĞıNıN uç nokta URI 'sini belirtir. Bu yapılandırma ayarı için bir değer belirtmeniz gerekir ve Azure 'da bir _anomali algılayıcısı_ kaynağı için değer geçerli bir uç nokta URI 'si olmalıdır.

Bu ayar aşağıdaki yerde bulunabilir:

* Azure portal: **anomali algılayıcısının** genel bakış, etiketli `Endpoint`

|Gerekli| Name | Veri türü | Açıklama |
|--|------|-----------|-------------|
|Evet| `Billing` | Dize | Faturalama uç noktası URI 'SI. Faturalandırma URI 'sini alma hakkında daha fazla bilgi için bkz. [gerekli parametreleri toplama](anomaly-detector-container-howto.md#gathering-required-parameters). Daha fazla bilgi ve bölgesel uç noktaların tamamen listesi için bkz. bilişsel [Hizmetler Için özel alt etki alanı adları](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>EULA ayarı

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Akışkan entd ayarları

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http proxy kimlik bilgileri ayarları

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Oturum açma ayarları
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Bağlama ayarları

Kapsayıcıya ve kapsayıcılardan veri okumak ve buradan veri yazmak için BIND bağlama kullanın. `--mount` [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) komutunda seçeneğini belirterek bir giriş bağlama veya çıkış bağlama belirtebilirsiniz.

Anomali algılayıcı kapsayıcıları, eğitim veya hizmet verilerini depolamak için giriş veya çıkış taklarını kullanmaz. 

Konak bağlama konumunun tam sözdizimi, ana bilgisayar işletim sistemine bağlı olarak değişir. Ayrıca, Docker hizmeti hesabı ve konak bağlama konumu izinleri tarafından kullanılan izinler arasındaki bir çakışma nedeniyle [ana bilgisayarın](anomaly-detector-container-howto.md#the-host-computer)bağlama konumu erişilebilir olmayabilir. 

|İsteğe Bağlı| Name | Veri türü | Açıklama |
|-------|------|-----------|-------------|
|İzin verilmiyor| `Input` | Dize | Anomali algılayıcı kapsayıcıları bunu kullanmaz.|
|İsteğe Bağlı| `Output` | Dize | Çıkış bağlama hedefi. `/output` varsayılan değerdir. Bu, günlüklerin konumudur. Bu, kapsayıcı günlüklerini içerir. <br><br>Örnek:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Örnek Docker Run komutları 

Aşağıdaki örneklerde, komutlarının nasıl yazılacağını ve kullanılacağını göstermek için yapılandırma ayarları kullanılır `docker run` .  Çalışan bir kez, kapsayıcıyı [durduruncaya](anomaly-detector-container-howto.md#stop-the-container) kadar çalışmaya devam eder.

* **Satır devamlılık karakteri**: aşağıdaki bölümlerdeki Docker komutları, `\` bir bash kabuğu için satır devamlılık karakteri olarak ters eğik çizgi kullanır. Bunu, ana bilgisayar işletim sisteminizin gereksinimlerine göre değiştirin veya kaldırın. Örneğin, Windows için satır devamlılık karakteri bir şapka karakteridir `^` . Ters eğik çizgiyi şapka işareti ile değiştirin. 
* **Bağımsız değişken sırası**: Docker Kapsayıcıları hakkında bilginiz yoksa bağımsız değişkenlerin sırasını değiştirmeyin.

Parantez içindeki değeri, `{}` kendi değerlerinizle değiştirin:

| Yer tutucu | Değer | Biçim veya örnek |
|-------------|-------|---|
| **{API_KEY}** | `Anomaly Detector`Azure anahtarları sayfasında kaynağın uç nokta anahtarı `Anomaly Detector` . | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Faturalandırma uç noktası değeri, Azure `Anomaly Detector` Genel Bakış sayfasında bulunur.| Açık örnekler için [gerekli parametreleri toplama](anomaly-detector-container-howto.md#gathering-required-parameters) konusuna bakın. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Kapsayıcıyı çalıştırmak için, ve seçenekleri belirtilmelidir; Aksi takdirde kapsayıcı başlatılmaz.  Daha fazla bilgi için bkz. [faturalandırma](anomaly-detector-container-howto.md#billing).
> ApiKey değeri, Azure anomali algılayıcısı kaynak anahtarları sayfasından alınan **anahtardır** . 

## <a name="anomaly-detector-container-docker-examples"></a>Anomali algılayıcı kapsayıcısı Docker örnekleri

Aşağıdaki Docker örnekleri anomali algılayıcı kapsayıcısı içindir. 

### <a name="basic-example"></a>Temel örnek 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Komut satırı bağımsız değişkenleriyle günlüğe kaydetme örneği

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Container Instances için bir anomali algılayıcı kapsayıcısı dağıtın](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Anomali algılayıcı API hizmeti hakkında daha fazla bilgi edinin](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)