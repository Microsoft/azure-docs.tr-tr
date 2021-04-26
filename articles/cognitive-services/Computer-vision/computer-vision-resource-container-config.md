---
title: Okuma OCR kapsayıcıları yapılandırma-Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Bu makalede, Görüntü İşleme okuma OCR kapsayıcıları için hem gerekli hem de isteğe bağlı ayarların nasıl yapılandırılacağı gösterilir.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 3e6c4b73e8aeb26c6ac4025ef3c07fb4f8d48eaf
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308651"
---
# <a name="configure-read-ocr-docker-containers"></a>Okuma OCR Docker kapsayıcılarını yapılandırma

Komut bağımsız değişkenlerini kullanarak, Görüntü İşleme OCR kapsayıcısının çalışma zamanı ortamını oku ' nı yapılandırırsınız `docker run` . Bu kapsayıcıda bazı gerekli ayarlar ve bazı isteğe bağlı ayarlar vardır. Birkaç komuta [örnek](#example-docker-run-commands) vardır. Kapsayıcıya özgü ayarlar faturalandırma ayarlardır. 

## <a name="configuration-settings"></a>Yapılandırma ayarları

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) Ve [`Eula`](#eula-setting) ayarları birlikte kullanılır ve üçü için geçerli değerler sağlamanız gerekir; Aksi takdirde Kapsayıcınız başlatılmaz. Bir kapsayıcı oluşturmak için bu yapılandırma ayarlarını kullanma hakkında daha fazla bilgi için bkz. [faturalandırma](computer-vision-how-to-install-containers.md).

Kapsayıcı Ayrıca, kapsayıcıya özgü aşağıdaki yapılandırma ayarlarına sahiptir:

|Gerekli|Ayar|Amaç|
|--|--|--|
|Hayır|ReadEngineConfig: ResultExpirationPeriod| yalnızca v 2.0 kapsayıcıları. Saat cinsinden sonuç süre sonu dönemi. Varsayılan değer 48 saattir. Ayar sistemin tanınma sonuçlarını ne zaman temizlemeli olduğunu belirtir. Örneğin, sistem, `resultExpirationPeriod=1` işlem sonrasında tanınma sonucunu 1 saat sonra temizler. İse `resultExpirationPeriod=0` , sistem, sonuç alındıktan sonra tanınma sonucunu temizler.|
|Hayır|Önbellek: Redsıs| yalnızca v 2.0 kapsayıcıları. Sonuçları depolamak için Redsıs depolamayı sunar. Bir yük dengeleyicinin arkasına birden çok okuma OCR kapsayıcısı yerleştirilirse önbellek *gerekir* .|
|Hayır|Kuyruk: Kbıbitmq|yalnızca v 2.0 kapsayıcıları. Görevleri dağıtırken Kbıbitmq 'ı sunar. Bu ayar, bir yük dengeleyicinin arkasına birden çok okuma OCR kapsayıcısı yerleştirildiğinde yararlıdır.|
|Hayır|Kuyruk: Azure: Queuevisibilitytimeoutınmilliseconds | yalnızca v3. x kapsayıcıları. Başka bir çalışan tarafından işlendiği zaman bir iletinin görünmez olması. |
|Hayır|Depolama::D Okısaentstore:: MongoDB|yalnızca v 2.0 kapsayıcıları. Kalıcı sonuç depolaması için MongoDB 'yi sunar. |
|Hayır|Depolama: ObjectStore: AzureBlob: ConnectionString| yalnızca v3. x kapsayıcıları. Azure Blob depolama bağlantı dizesi. |
|Hayır|Depolama: Timetoliveındays| yalnızca v3. x kapsayıcıları. Sonuç süre sonu dönemi (gün). Ayar sistemin tanınma sonuçlarını ne zaman temizlemeli olduğunu belirtir. Varsayılan değer 2 gündür (48 saat); Bu, bu dönemden daha uzun süre içinde olan tüm sonuçlar başarıyla alınmayacağından garanti edilmez. |
|Hayır|Görev: Maxrunningtimespanınminutes| yalnızca v3. x kapsayıcıları. Tek bir istek için en fazla çalışma süresi. Varsayılan değer 60 dakikadır. |

## <a name="apikey-configuration-setting"></a>ApiKey yapılandırma ayarı

Bu `ApiKey` ayar, `Cognitive Services` kapsayıcının fatura bilgilerini izlemek Için kullanılan Azure Kaynak anahtarını belirtir. ApiKey için bir değer belirtmeniz gerekir ve değerin yapılandırma ayarı için belirtilen bilişsel _Hizmetler_ kaynağı için geçerli bir anahtar olması gerekir [`Billing`](#billing-configuration-setting) .

Bu ayar aşağıdaki yerde bulunabilir:

* Azure portal: bilişsel **Hizmetler** kaynak yönetimi, **anahtarlar** altında

## <a name="applicationinsights-setting"></a>ApplicationInsights ayarı

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Faturalandırma yapılandırma ayarı

`Billing`Ayar, Azure üzerinde bulunan bilişsel _Hizmetler_ kaynağının, kapsayıcının fatura bilgilerini ölçmek için kullanılan uç nokta URI 'sini belirtir. Bu yapılandırma ayarı için bir değer belirtmeniz gerekir ve Azure 'daki bilişsel _Hizmetler_ kaynağı için değer geçerli bir uç nokta URI 'si olmalıdır. Kapsayıcı her 10 ila 15 dakikada bir kullanım raporu sağlar.

Bu ayar aşağıdaki yerde bulunabilir:

* Azure portal: bilişsel **Hizmetler** genel bakış, etiketli `Endpoint`

`vision/<version>`Aşağıdaki tabloda gösterildiği gibi, yönlendirmeyi, uç nokta URI 'sine eklemeyi unutmayın. 

|Gerekli| Name | Veri türü | Açıklama |
|--|------|-----------|-------------|
|Evet| `Billing` | Dize | Faturalama uç noktası URI 'SI<br><br>Örnek:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v3.2` |

## <a name="eula-setting"></a>EULA ayarı

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Akışkan entd ayarları

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP proxy kimlik bilgileri ayarları

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Oturum açma ayarları
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Bağlama ayarları

Kapsayıcıya ve kapsayıcılardan veri okumak ve buradan veri yazmak için BIND bağlama kullanın. `--mount` [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) komutunda seçeneğini belirterek bir giriş bağlama veya çıkış bağlama belirtebilirsiniz.

Görüntü İşleme kapsayıcıları, eğitim veya hizmet verilerini depolamak için giriş veya çıkış taklarını kullanmaz. 

Konak bağlama konumunun tam sözdizimi, ana bilgisayar işletim sistemine bağlı olarak değişir. Ayrıca, Docker hizmeti hesabı ve konak bağlama konumu izinleri tarafından kullanılan izinler arasındaki bir çakışma nedeniyle [ana bilgisayarın](computer-vision-how-to-install-containers.md#the-host-computer)bağlama konumu erişilebilir olmayabilir. 

|İsteğe Bağlı| Name | Veri türü | Açıklama |
|-------|------|-----------|-------------|
|İzin verilmiyor| `Input` | Dize | Görüntü İşleme kapsayıcılar bunu kullanmaz.|
|İsteğe Bağlı| `Output` | Dize | Çıkış bağlama hedefi. `/output` varsayılan değerdir. Bu, günlüklerin konumudur. Bu, kapsayıcı günlüklerini içerir. <br><br>Örnek:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Örnek Docker Run komutları

Aşağıdaki örneklerde, komutlarının nasıl yazılacağını ve kullanılacağını göstermek için yapılandırma ayarları kullanılır `docker run` .  Çalışan bir kez, kapsayıcıyı [durduruncaya](computer-vision-how-to-install-containers.md#stop-the-container) kadar çalışmaya devam eder.

* **Satır devamlılık karakteri**: aşağıdaki bölümlerdeki Docker komutları, `\` satır devamlılık karakteri olarak ters eğik çizgi kullanır. Bunu, ana bilgisayar işletim sisteminizin gereksinimlerine göre değiştirin veya kaldırın. 
* **Bağımsız değişken sırası**: Docker Kapsayıcıları hakkında bilginiz yoksa bağımsız değişkenlerin sırasını değiştirmeyin.

{_Argument_name_} değerini kendi değerlerinizle değiştirin:

| Yer tutucu | Değer | Biçim veya örnek |
|-------------|-------|---|
| **{API_KEY}** | `Computer Vision`Azure anahtarları sayfasında kaynağın uç nokta anahtarı `Computer Vision` . | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Faturalandırma uç noktası değeri, Azure `Computer Vision` Genel Bakış sayfasında bulunur.| Açık örnekler için [gerekli parametreleri toplama](computer-vision-how-to-install-containers.md#gathering-required-parameters) konusuna bakın. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Kapsayıcıyı çalıştırmak için, ve seçenekleri belirtilmelidir; Aksi takdirde kapsayıcı başlatılmaz.  Daha fazla bilgi için bkz. [faturalandırma](computer-vision-how-to-install-containers.md#billing).
> ApiKey değeri, Azure  `Cognitive Services` kaynak anahtarları sayfasından alınan anahtardır.

## <a name="container-docker-examples"></a>Kapsayıcı Docker örnekleri

Aşağıdaki Docker örnekleri, okuma OCR kapsayıcısına yöneliktir.


# <a name="version-32"></a>[Sürüm 3,2](#tab/version-3-2)

### <a name="basic-example"></a>Temel örnek

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Günlüğe kaydetme örneği 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

# <a name="version-20-preview"></a>[Sürüm 2,0-Önizleme](#tab/version-2)

### <a name="basic-example"></a>Temel örnek

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Günlüğe kaydetme örneği 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Sonraki adımlar

* [Kapsayıcıları yüklemeyi ve çalıştırmayı](computer-vision-how-to-install-containers.md)gözden geçirin.
