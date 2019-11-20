---
title: Kapsayıcılar-yüz tanıma API 'sini yükleyip çalıştırın
titleSuffix: Azure Cognitive Services
description: Bu makalede, bu adım adım öğreticide, yüzleri için kapsayıcıları indirme, yükleme ve çalıştırma işlemlerinin nasıl yapılacağı gösterilir.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: dapine
ms.openlocfilehash: 97b1fd27e91823594d972bdd49184d2b0f5d320b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171707"
---
# <a name="install-and-run-face-containers"></a>Yüz kapsayıcıları yükleyip çalıştırma

Azure bilişsel hizmetler, resimlerde insan yüzlerini algılayan, Docker için standartlaştırılmış bir Linux kapsayıcısı sağlar. Ayrıca, Noi 'ler ve gözler, cinsiyet, yaş ve diğer makine tarafından öngörülen yüz özellikleri gibi yüz başlıkları dahil olmak üzere öznitelikleri tanımlar. Algılamanın yanı sıra, aynı görüntüde veya farklı resimlerde iki yüzün aynı olup olmadığını, Güvenirlik puanı kullanılarak kontrol edebilir. Yüz Ayrıca, benzer bir veya aynı yüzün zaten mevcut olup olmadığını görmek için yüzleri bir veritabanıyla karşılaştırabilir. Ayrıca, paylaşılan görsel nitelikleri kullanarak benzer yüzeyleri gruplar halinde düzenleyebilir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Yüz Tanıma API'si kapsayıcılarını kullanmadan önce aşağıdaki önkoşulları karşılamanız gerekir.

|Gerekli|Amaç|
|--|--|
|Docker altyapısı| Docker altyapısının bir [ana bilgisayara](#the-host-computer)yüklenmesi gerekir. Docker, [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)ve [Linux](https://docs.docker.com/engine/installation/#supported-platforms)'ta Docker ortamını yapılandıran paketler sağlar. Docker ve kapsayıcı temelleri hakkında bilgi için bkz: [Docker'a genel bakış](https://docs.docker.com/engine/docker-overview/).<br><br> Docker, kapsayıcılar ile bağlanma ve faturalama verileri Azure'a göndermek izin verecek şekilde yapılandırılmalıdır. <br><br> Windows 'da Docker 'ın de Linux kapsayıcılarını destekleyecek şekilde yapılandırılması gerekir.<br><br>|
|Docker ile benzerlik | Kayıt defterleri, depolar, kapsayıcılar ve kapsayıcı görüntüleri gibi Docker kavramlarının temel olarak anlaşılmasına ihtiyacınız vardır. Ayrıca temel `docker` komutları bilgisine de ihtiyacınız vardır.| 
|Yüz kaynağı |Kapsayıcıyı kullanmak için şunları yapmanız gerekir:<br><br>Bir Azure **yüz** kaynağı ve ilgili API anahtarı ve uç nokta URI 'si. Her iki değer de kaynak için **genel bakış** ve **anahtarlar** sayfalarında kullanılabilir. Kapsayıcının başlatılması gerekir.<br><br>**{API_KEY}** : **anahtarlar** sayfasında kullanılabilir iki kaynak anahtardan biri<br><br>**{ENDPOINT_URI}** : **genel bakış** sayfasında belirtilen bitiş noktası

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>Özel kapsayıcı kayıt defterine erişim isteği

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Ana bilgisayar

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Kapsayıcı gereksinimleri ve önerileri

Aşağıdaki tabloda, her bir Yüz Tanıma API'si kapsayıcısı için ayrılacak minimum ve önerilen CPU çekirdekleri ve bellek açıklanmaktadır.

| Kapsayıcı | Minimum | Önerilen | Saniye başına işlem<br>(En düşük, en yüksek)|
|-----------|---------|-------------|--|
|Yüz | 1 çekirdek, 2 GB bellek | 1 çekirdek, 4 GB bellek |10, 20|

* Her çekirdek en az 2,6 GHz veya daha hızlı olmalıdır.
* Saniyedeki işlem sayısı (TPS).

Çekirdek ve bellek, `docker run` komutunun bir parçası olarak kullanılan `--cpus` ve `--memory` ayarlarına karşılık gelir.

## <a name="get-the-container-image-with-docker-pull"></a>docker pull kapsayıcı görüntüsünü al

Yüz Tanıma API'si için kapsayıcı görüntüleri kullanılabilir. 

| Kapsayıcı | Havuz |
|-----------|------------|
| Yüz | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Yüz kapsayıcısı için Docker çekme

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Kapsayıcıyı kullanma

Kapsayıcı [ana bilgisayar](#the-host-computer)üzerinde olduktan sonra, kapsayıcında çalışmak için aşağıdaki işlemi kullanın.

1. [Kapsayıcıyı](#run-the-container-with-docker-run) gerekli faturalandırma ayarlarıyla çalıştırın. `docker run` komutuna daha fazla [örnek](./face-resource-container-config.md#example-docker-run-commands) kullanılabilir. 
1. [Kapsayıcının tahmin uç noktasını sorgulayın](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Kapsayıcıyı Docker Run ile çalıştırma

Kapsayıcıyı çalıştırmak için [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) komutunu kullanın. [ Ve](#gathering-required-parameters) değerlerini alma hakkında ayrıntılar için gerekli `{ENDPOINT_URI}`parametreleri`{API_KEY}` toplama bölümüne bakın.

Komut [örnekleri](face-resource-container-config.md#example-docker-run-commands) mevcuttur. `docker run`

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Bu komut:

* Kapsayıcı görüntüsünden bir yüz kapsayıcısı çalıştırır.
* Bir CPU çekirdeği ve 4 GB bellek ayırır.
* TCP bağlantı noktası 5000 ' i gösterir ve kapsayıcı için bir sahte TTY ayırır.
* Kapsayıcıyı çıktıktan sonra otomatik olarak kaldırır. Kapsayıcı görüntüsü hala ana bilgisayarda kullanılabilir. 

`docker run` komutuna daha fazla [örnek](./face-resource-container-config.md#example-docker-run-commands) kullanılabilir. 

> [!IMPORTANT]
> Kapsayıcıyı çalıştırmak için `Eula`, `Billing`ve `ApiKey` seçenekleri belirtilmelidir ya da kapsayıcı başlamamalıdır. Daha fazla bilgi için [faturalama](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Kapsayıcının tahmin uç noktasını sorgulama

Kapsayıcı, REST tabanlı sorgu tahmin uç noktası API 'Leri sağlar. 

Kapsayıcı API 'Leri için `http://localhost:5000`konak kullanın.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Kapsayıcıyı durdur

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Sorun giderme

Kapsayıcıyı bir çıkış [bağlaması](./face-resource-container-config.md#mount-settings) ile çalıştırırsanız ve günlüğe kaydetme etkinse kapsayıcı, kapsayıcıyı başlatırken veya çalıştırırken oluşan sorunları gidermek için faydalı olan günlük dosyaları oluşturur.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturalandırma

Yüz Tanıma API'si kapsayıcıları Azure hesabınızdaki bir Yüz Tanıma API'si kaynağını kullanarak faturalandırma bilgilerini Azure 'a gönderir. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Bu seçenekler hakkında daha fazla bilgi için bkz. [kapsayıcıları yapılandırma](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Özet

Bu makalede, Yüz Tanıma API'si kapsayıcıları indirme, yükleme ve çalıştırma ile ilgili kavramları ve iş akışını öğrendiniz. Özet:

* Kapsayıcı görüntüleri Azure Container Registry indirilir.
* Docker kapsayıcı görüntüleri çalıştırın.
* Kapsayıcının ana bilgisayar URI 'sini belirterek Yüz Tanıma API'si kapsayıcılarındaki işlemleri çağırmak için REST API ya da SDK kullanabilirsiniz.
* Bir kapsayıcıyı örneklediğinizde faturalandırma bilgilerini belirtmeniz gerekir.

> [!IMPORTANT]
> Bilişsel hizmetler kapsayıcıları, ölçüm için Azure 'a bağlı kalmadan çalıştırılmak üzere lisanslanmaz. Müşterilerin, ödeme bilgilerini her zaman ölçüm hizmeti ile iletmelerini sağlamak için kapsayıcıların etkinleştirilmesi gerekir. Bilişsel hizmetler kapsayıcıları, müşteri verilerini (örneğin, çözümlenen resim veya metin gibi) Microsoft 'a göndermez.

## <a name="next-steps"></a>Sonraki adımlar

* Yapılandırma ayarları için bkz. [kapsayıcıları yapılandırma](face-resource-container-config.md).
* Yüzleri algılama ve belirleme hakkında daha fazla bilgi edinmek için bkz. [yüze genel bakış](Overview.md).
* Kapsayıcı tarafından desteklenen yöntemler hakkında daha fazla bilgi için [Yüz Tanıma API'si](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)bakın.
* Daha fazla bilişsel hizmetler kapsayıcısı kullanmak için bkz. bilişsel [Hizmetler kapsayıcıları](../cognitive-services-container-support.md).
