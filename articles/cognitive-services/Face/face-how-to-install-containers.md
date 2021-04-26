---
title: Yüz Tanıma API'si için Docker Kapsayıcıları yükleyip çalıştırın
titleSuffix: Azure Cognitive Services
description: Görüntülerde insan yüzlerini algılamak ve tanımlamak için Yüz Tanıma API'si için Docker kapsayıcısını kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: aahi
keywords: Şirket içi, Docker, kapsayıcı, tanımla
ms.openlocfilehash: 36cbd7bd24304871593b107f9b8ed9be02ce46de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101706801"
---
# <a name="install-and-run-face-containers-preview"></a>Yüz kapsayıcıları (Önizleme) yükleyip çalıştırma

> [!IMPORTANT]
> Yüz Tanıma v1 kapsayıcısı kullanıcılarının sınırına ulaşıldı. Şu anda Yüz Tanıma kapsayıcısı için yeni uygulama kabul etmiyoruz.

Azure bilişsel Hizmetler Yüz Tanıma API'si, görüntülerdeki insan yüzlerini algılayan ve çözümleyen bir Linux Docker kapsayıcısı sağlar. Ayrıca, Noi 'ler ve gözler, cinsiyet, yaş ve diğer makine tarafından öngörülen yüz özellikleri gibi yüz başlıkları dahil olmak üzere öznitelikleri tanımlar. Algılamanın yanı sıra, aynı görüntüde veya farklı resimlerde iki yüzün aynı olup olmadığını, Güvenirlik puanı kullanılarak kontrol edebilir. Yüz Ayrıca, benzer bir veya aynı yüzün zaten mevcut olup olmadığını görmek için yüzleri bir veritabanıyla karşılaştırabilir. Ayrıca, paylaşılan görsel nitelikleri kullanarak benzer yüzeyleri gruplar halinde düzenleyebilir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/cognitive-services/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Yüz hizmeti kapsayıcılarını kullanmadan önce aşağıdaki önkoşulları karşılamanız gerekir.

|Gerekli|Amaç|
|--|--|
|Docker altyapısı| Docker altyapısının bir [ana bilgisayara](#the-host-computer)yüklenmesi gerekir. Docker, [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ve [Linux](https://docs.docker.com/engine/installation/#supported-platforms) üzerinde Docker ortamını yapılandıran paketler sağlar. Docker ve kapsayıcı temel bilgileri ile ilgili giriş yapmak için [Docker’a genel bakış](https://docs.docker.com/engine/docker-overview/) bölümüne bakın.<br><br> Kapsayıcıların Azure 'a bağlanıp faturalandırma verilerini göndermesini sağlamak için Docker yapılandırılmalıdır. <br><br> Windows 'da Docker 'ın de Linux kapsayıcılarını destekleyecek şekilde yapılandırılması gerekir.<br><br>|
|Docker ile benzerlik | Kayıt defterleri, depolar, kapsayıcılar ve kapsayıcı görüntüleri gibi Docker kavramlarının temel olarak anlaşılmasına ihtiyacınız vardır. Ayrıca temel komutlar hakkında bilgi sahibi olmanız gerekir `docker` .| 
|Yüz kaynağı |Kapsayıcıyı kullanmak için şunları yapmanız gerekir:<br><br>Bir Azure **yüz** kaynağı ve ilgili API anahtarı ve uç nokta URI 'si. Her iki değer de kaynak için **genel bakış** ve **anahtarlar** sayfalarında kullanılabilir. Kapsayıcının başlatılması gerekir.<br><br>**{API_KEY}**: **anahtarlar** sayfasında kullanılabilir iki kaynak anahtardan biri<br><br>**{ENDPOINT_URI}**: **genel bakış** sayfasında belirtilen bitiş noktası

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Ana bilgisayar

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Kapsayıcı gereksinimleri ve önerileri

Aşağıdaki tabloda, her yüz hizmeti kapsayıcısı için ayrılacak minimum ve önerilen CPU çekirdekleri ve bellek açıklanmaktadır.

| Kapsayıcı | Minimum | Önerilen | Saniye başına işlem<br>(En düşük, en yüksek)|
|-----------|---------|-------------|--|
|Yüz Tanıma | 1 çekirdek, 2 GB bellek | 1 çekirdek, 4 GB bellek |10, 20|

* Her çekirdek en az 2,6 GHz veya daha hızlı olmalıdır.
* Saniyedeki işlem sayısı (TPS).

Çekirdek ve bellek, `--cpus` `--memory` komutunun bir parçası olarak kullanılan ve ayarlarına karşılık gelir `docker run` .

## <a name="get-the-container-image-with-docker-pull"></a>docker pull kapsayıcı görüntüsünü al

Yüz hizmeti için kapsayıcı görüntüleri kullanılabilir. 

| Kapsayıcı | Depo |
|-----------|------------|
| Yüz Tanıma | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Yüz kapsayıcısı için Docker çekme

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Kapsayıcıyı kullanma

Kapsayıcı [ana bilgisayar](#the-host-computer)üzerinde olduktan sonra, kapsayıcında çalışmak için aşağıdaki işlemi kullanın.

1. [Kapsayıcıyı](#run-the-container-with-docker-run) gerekli faturalandırma ayarlarıyla çalıştırın. Komuta [](./face-resource-container-config.md#example-docker-run-commands) daha fazla örnek `docker run` kullanılabilir. 
1. [Kapsayıcının tahmin uç noktasını sorgulayın](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Kapsayıcıyı Docker Run ile çalıştırma

Kapsayıcıyı çalıştırmak için [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) komutunu kullanın. Ve değerlerini alma hakkında ayrıntılar için [gerekli parametreleri toplama](#gathering-required-parameters) bölümüne bakın `{ENDPOINT_URI}` `{API_KEY}` .

Komut [örnekleri](face-resource-container-config.md#example-docker-run-commands) `docker run` mevcuttur.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Şu komut:

* Kapsayıcı görüntüsünden bir yüz kapsayıcısı çalıştırır.
* Bir CPU çekirdeği ve 4 GB bellek ayırır.
* TCP bağlantı noktası 5000 ' i gösterir ve kapsayıcı için bir sahte TTY ayırır.
* Kapsayıcıyı çıktıktan sonra otomatik olarak kaldırır. Kapsayıcı görüntüsü hala ana bilgisayarda kullanılabilir. 

Komuta [](./face-resource-container-config.md#example-docker-run-commands) daha fazla örnek `docker run` kullanılabilir. 

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Kapsayıcıyı çalıştırmak için,, ve seçenekleri belirtilmelidir, aksi olarak kapsayıcı başlamamalıdır. Daha fazla bilgi için bkz. [faturalandırma](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Kapsayıcının tahmin uç noktasını sorgulama

Kapsayıcı REST tabanlı sorgu tahmin uç noktası API’lerini sağlar. 

Kapsayıcı API’leri için `http://localhost:5000` konağını kullanın.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Kapsayıcıyı durdurma

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Sorun giderme

Kapsayıcıyı bir çıkış [bağlaması](./face-resource-container-config.md#mount-settings) ile çalıştırırsanız ve günlüğe kaydetme etkinse kapsayıcı, kapsayıcıyı başlatırken veya çalıştırırken oluşan sorunları gidermek için faydalı olan günlük dosyaları oluşturur.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturalandırma

Yüz hizmet kapsayıcıları, Azure hesabınızdaki bir yüz kaynağı kullanarak faturalama bilgilerini Azure 'a gönderir. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Bu seçenekler hakkında daha fazla bilgi için bkz. [kapsayıcıları yapılandırma](./face-resource-container-config.md).

## <a name="summary"></a>Özet

Bu makalede, yüz hizmet kapsayıcılarını indirme, yükleme ve çalıştırma ile ilgili kavramları ve iş akışını öğrendiniz. Özet:

* Kapsayıcı görüntüleri Azure Container Registry indirilir.
* Kapsayıcı görüntüleri Docker 'da çalışır.
* Kapsayıcının ana bilgisayar URI 'sini belirterek yüz hizmeti kapsayıcılarındaki işlemleri çağırmak için REST API veya SDK kullanabilirsiniz.
* Bir kapsayıcıyı örneklediğinizde faturalandırma bilgilerini belirtmeniz gerekir.

> [!IMPORTANT]
> Bilişsel hizmetler kapsayıcıları, ölçüm için Azure 'a bağlı kalmadan çalıştırılmak üzere lisanslanmaz. Müşterilerin, ödeme bilgilerini her zaman ölçüm hizmeti ile iletmelerini sağlamak için kapsayıcıların etkinleştirilmesi gerekir. Bilişsel hizmetler kapsayıcıları, müşteri verilerini (örneğin, çözümlenen resim veya metin gibi) Microsoft 'a göndermez.

## <a name="next-steps"></a>Sonraki adımlar

* Yapılandırma ayarları için bkz. [kapsayıcıları yapılandırma](face-resource-container-config.md).
* Yüzleri algılama ve belirleme hakkında daha fazla bilgi edinmek için bkz. [yüze genel bakış](Overview.md).
* Kapsayıcı tarafından desteklenen yöntemler hakkında daha fazla bilgi için [Yüz Tanıma API'si](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)bakın.
* Daha fazla bilişsel hizmetler kapsayıcısı kullanmak için bkz. bilişsel [Hizmetler kapsayıcıları](../cognitive-services-container-support.md).
