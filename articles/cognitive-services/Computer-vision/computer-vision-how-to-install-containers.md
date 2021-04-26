---
title: Görüntü İşleme 'den OCR Docker Kapsayıcıları okuma
titleSuffix: Azure Cognitive Services
description: Şirket içindeki görüntülerden ve belgelerden metin çıkarmak için Görüntü İşleme 'dan OCR Docker kapsayıcılarını oku ' nı kullanın.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: Şirket içi, OCR, Docker, kapsayıcı
ms.openlocfilehash: dead48d7d449d1d403359c518eb842b32a54c634
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779102"
---
# <a name="install-read-ocr-docker-containers"></a>Okuma OCR Docker Kapsayıcıları 'nı yükler

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

Kapsayıcılar, Görüntü İşleme API’lerini kendi ortamınızda çalıştırmanızı sağlar. Kapsayıcılar, belirli güvenlik ve veri idare gereksinimleri için çok kullanışlıdır. Bu makalede Görüntü İşleme kapsayıcıları indirme, yükleme ve çalıştırma hakkında bilgi edineceksiniz.

*Okuma* OCR KAPSAYıCıSı, JPEG, PNG, BMP, PDF ve TIFF dosya biçimleri desteğiyle, görüntülerden ve belgelerden yazdırılmış ve el yazısı metinleri ayıklamanızı sağlar. Daha fazla bilgi için bkz. [API nasıl yapılır Kılavuzu](Vision-API-How-to-Topics/call-read-api.md).

## <a name="read-32-container"></a>3,2 kapsayıcısını oku

Okuma 3,2 OCR kapsayıcısı şunları sağlar:
* Gelişmiş doğruluk için yeni modeller.
* Aynı belge içinde birden çok dil desteği.
* Toplam 73 dil desteği. [OCR tarafından desteklenen dillerin](./language-support.md#optical-character-recognition-ocr)tam listesine bakın.
* Hem belgeler hem de görüntüler için tek bir işlem.
* Daha büyük belgeler ve görüntüler için destek.
* Güvenilirlik puanları.
* Hem yazdırma hem de el yazısı metin içeren belgeler için destek.
* Belgedeki yalnızca seçili olan sayfadan metin ayıklama özelliği.
* Varsayılan olarak yalnızca Latin dilleri için daha doğal bir okuma düzeninde metin satırı çıkış sırası seçin.
* Yalnızca Latin dilleri için yazı el yazısı stili olarak metin satırı sınıflandırması.

Bugün okuma 2,0 kapsayıcıları kullanıyorsanız, yeni sürümlerdeki değişiklikler hakkında bilgi edinmek için [geçiş kılavuzuna](read-container-migration-guide.md) bakın.

## <a name="prerequisites"></a>Önkoşullar

Kapsayıcıları kullanmadan önce aşağıdaki önkoşulları karşılamanız gerekir:

|Gerekli|Amaç|
|--|--|
|Docker altyapısı| Bir [ana bilgisayarda](#the-host-computer)Docker altyapısının yüklü olması gerekir. Docker, [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ve [Linux](https://docs.docker.com/engine/installation/#supported-platforms) üzerinde Docker ortamını yapılandıran paketler sağlar. Docker ve kapsayıcı temel bilgileri ile ilgili giriş yapmak için [Docker’a genel bakış](https://docs.docker.com/engine/docker-overview/) bölümüne bakın.<br><br> Kapsayıcıların Azure 'a bağlanıp faturalandırma verilerini göndermesini sağlamak için Docker yapılandırılmalıdır. <br><br> **Windows 'da** Docker 'ın de Linux kapsayıcılarını destekleyecek şekilde yapılandırılması gerekir.<br><br>|
|Docker ile benzerlik | Kayıt defterleri, depolar, kapsayıcılar ve kapsayıcı görüntüleri gibi Docker kavramlarının yanı sıra temel komutlar hakkında bilgi sahibi olmanız gerekir `docker` .| 
|Görüntü İşleme kaynağı |Kapsayıcısını kullanabilmeniz için şunları yapmanız gerekir:<br><br>Uç nokta URI 'SI olan bir Azure **görüntü işleme** kaynağı ve ilişkili API anahtarı. Her iki değer de kaynak için genel bakış ve anahtarlar sayfalarında bulunur ve kapsayıcıyı başlatmak için gereklidir.<br><br>**{API_KEY}**: **anahtarlar** sayfasında kullanılabilir iki kaynak anahtardan biri<br><br>**{ENDPOINT_URI}**: **genel bakış** sayfasında belirtilen bitiş noktası|

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/cognitive-services/) oluşturun.

## <a name="request-approval-to-run-the-container"></a>Kapsayıcıyı çalıştırmak için onay isteyin

Kapsayıcıyı çalıştırmak için onay istemek üzere [istek formunu](https://aka.ms/csgate) doldurun ve iletin. 

[!INCLUDE [Request access to run the container](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Ana bilgisayar

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Gelişmiş vektör uzantısı desteği

**Ana** bilgisayar, Docker kapsayıcısını çalıştıran bilgisayardır. Ana bilgisayar  [Gelişmiş vektör uzantılarını](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) desteklemelidir (AVX2). Aşağıdaki komutla Linux konaklarda AVX2 desteğini denetleyebilirsiniz:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> Ana bilgisayar, AVX2 desteklemek için *gereklidir* . Kapsayıcı, AVX2 desteği olmadan *düzgün çalışmaz.*

### <a name="container-requirements-and-recommendations"></a>Kapsayıcı gereksinimleri ve önerileri

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Kapsayıcı görüntüsünü al `docker pull`

Okuma için kapsayıcı görüntüleri kullanılabilir.

| Kapsayıcı | Container Registry/depo/görüntü adı |
|-----------|------------|
| Okuma 2,0-Önizleme | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| 3,2 okuyun | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.2` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)Bir kapsayıcı görüntüsünü indirmek için komutunu kullanın.

### <a name="docker-pull-for-the-read-ocr-container"></a>Okuma OCR kapsayıcısı için Docker çekme

# <a name="version-32"></a>[Sürüm 3,2](#tab/version-3-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
```

# <a name="version-20-preview"></a>[Sürüm 2,0-Önizleme](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Kapsayıcıyı kullanma

Kapsayıcı [ana bilgisayardan](#the-host-computer)olduktan sonra, kapsayıcında çalışmak için aşağıdaki işlemi kullanın.

1. [Kapsayıcıyı](#run-the-container-with-docker-run)gerekli faturalandırma ayarlarıyla çalıştırın. Komuta [](computer-vision-resource-container-config.md) daha fazla örnek `docker run` kullanılabilir. 
1. [Kapsayıcının tahmin uç noktasını sorgulayın](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Kapsayıcıyı ile çalıştırma `docker run`

Kapsayıcıyı çalıştırmak için [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) komutunu kullanın. Ve değerlerini alma hakkında ayrıntılar için [gerekli parametreleri toplama](#gathering-required-parameters) bölümüne bakın `{ENDPOINT_URI}` `{API_KEY}` .

Komut [örnekleri](computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` mevcuttur.

# <a name="version-32"></a>[Sürüm 3,2](#tab/version-3-2)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Şu komut:

* Kapsayıcı görüntüsünden OCR kapsayıcısını oku ' nı çalıştırır.
* 8 CPU çekirdeği ve 18 gigabayt (GB) bellek ayırır.
* TCP bağlantı noktası 5000 ' i gösterir ve kapsayıcı için bir sözde TTY ayırır.
* Kapsayıcıyı çıktıktan sonra otomatik olarak kaldırır. Kapsayıcı görüntüsü hala ana bilgisayarda kullanılabilir.

# <a name="version-20-preview"></a>[Sürüm 2,0-Önizleme](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Şu komut:

* Kapsayıcı görüntüsünden OCR kapsayıcısını oku ' nı çalıştırır.
* 8 CPU çekirdeği ve 16 gigabayt (GB) bellek ayırır.
* TCP bağlantı noktası 5000 ' i gösterir ve kapsayıcı için bir sözde TTY ayırır.
* Kapsayıcıyı çıktıktan sonra otomatik olarak kaldırır. Kapsayıcı görüntüsü hala ana bilgisayarda kullanılabilir.

---


Komuta [](./computer-vision-resource-container-config.md#example-docker-run-commands) daha fazla örnek `docker run` kullanılabilir. 

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Kapsayıcıyı çalıştırmak için, ve seçenekleri belirtilmelidir; Aksi takdirde kapsayıcı başlatılmaz.  Daha fazla bilgi için bkz. [faturalandırma](#billing).

Daha yüksek aktarım hızına ihtiyacınız varsa (örneğin, çok sayfalı dosyaları işlerken), [Azure depolama](../../storage/common/storage-account-create.md) ve [Azure kuyruğu](../../storage/queues/storage-queues-introduction.md)kullanarak [bir Kubernetes kümesinde](deploy-computer-vision-on-premises.md)birden çok kapsayıcı dağıtmaya göz önünde bulundurun.

İşleme için görüntüleri depolamak üzere Azure Storage kullanıyorsanız, kapsayıcıyı çağırırken kullanılacak bir [bağlantı dizesi](../../storage/common/storage-configure-connection-string.md) oluşturabilirsiniz.

Bağlantı dizenizi bulmak için:

1. Azure portal **depolama hesaplarına** gidin ve hesabınızı bulun.
2. Sol gezinti listesindeki **erişim tuşları** ' na tıklayın.
3. Bağlantı dizeniz, **bağlantı dizesinin** altında bulunur

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Kapsayıcının tahmin uç noktasını sorgulama

Kapsayıcı REST tabanlı sorgu tahmin uç noktası API’lerini sağlar. 

# <a name="version-32"></a>[Sürüm 3,2](#tab/version-3-2)

Kapsayıcı API’leri için `http://localhost:5000` konağını kullanın. Swagger yolunu şurada görebilirsiniz: `http://localhost:5000/swagger/vision-v3.2-read/swagger.json` .

# <a name="version-20-preview"></a>[Sürüm 2,0-Önizleme](#tab/version-2)

Kapsayıcı API’leri için `http://localhost:5000` konağını kullanın. Swagger yolunu şurada görebilirsiniz: `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>Zaman uyumsuz okuma


# <a name="version-32"></a>[Sürüm 3,2](#tab/version-3-2)

`POST /vision/v3.2/read/analyze` `GET /vision/v3.2/read/operations/{operationId}` Görüntü işleme hizmetinin ilgili Rest işlemlerini nasıl kullandığına benzer şekilde bir görüntüyü zaman uyumsuz olarak okumak için konser içindeki ve işlemlerini kullanabilirsiniz. Zaman uyumsuz POST yöntemi, `operationId` HTTP GET isteğine tanımlayıcı olarak kullanılan bir döndürür.


Swagger kullanıcı arabiriminden, `Analyze` tarayıcıda genişletmek için öğesini seçin. Ardından **deneyin**  >  **dosyayı** seçin öğesini seçin. Bu örnekte, aşağıdaki görüntüyü kullanacağız:

![sekmeler vs alanları](media/tabs-vs-spaces.png)

Zaman uyumsuz GÖNDERI başarıyla çalıştırıldığında, bir **HTTP 202** durum kodu döndürür. Yanıtın bir parçası olarak, `operation-location` isteğin sonuç uç noktasını tutan bir üst bilgi vardır.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.2/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

, `operation-location` Tam URL 'dir ve bır http get aracılığıyla erişilir. Önceki görüntüden URL 'yi yürütmenin JSON yanıtı aşağıda verilmiştir `operation-location` :

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          },
          {
            "boundingBox": [
              286,
              171,
              415,
              165,
              417,
              197,
              287,
              201
            ],
            "text": "paces",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.746
              }
            },
            "words": [
              {
                "boundingBox": [
                  286,
                  179,
                  404,
                  166,
                  405,
                  198,
                  290,
                  201
                ],
                "text": "paces",
                "confidence": 0.938
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[Sürüm 2,0-Önizleme](#tab/version-2)

`POST /vision/v2.0/read/core/asyncBatchAnalyze` `GET /vision/v2.0/read/operations/{operationId}` Görüntü işleme hizmetinin ilgili Rest işlemlerini nasıl kullandığına benzer şekilde bir görüntüyü zaman uyumsuz olarak okumak için konser içindeki ve işlemlerini kullanabilirsiniz. Zaman uyumsuz POST yöntemi, `operationId` HTTP GET isteğine tanımlayıcı olarak kullanılan bir döndürür.

Swagger kullanıcı arabiriminden, `asyncBatchAnalyze` tarayıcıda genişletmek için öğesini seçin. Ardından **deneyin**  >  **dosyayı** seçin öğesini seçin. Bu örnekte, aşağıdaki görüntüyü kullanacağız:

![sekmeler vs alanları](media/tabs-vs-spaces.png)

Zaman uyumsuz GÖNDERI başarıyla çalıştırıldığında, bir **HTTP 202** durum kodu döndürür. Yanıtın bir parçası olarak, `operation-location` isteğin sonuç uç noktasını tutan bir üst bilgi vardır.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

, `operation-location` Tam URL 'dir ve bır http get aracılığıyla erişilir. Önceki görüntüden URL 'yi yürütmenin JSON yanıtı aşağıda verilmiştir `operation-location` :

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

---

> [!IMPORTANT]
> Bir yük dengeleyicinin arkasında birden çok okuma OCR kapsayıcısı dağıtırsanız (örneğin, Docker Compose veya Kubernetes), bir dış önbelleğiniz olması gerekir. İşlem kapsayıcısı ve GET isteği kapsayıcısı aynı olamaz, çünkü bir dış önbellek sonuçları depolar ve kapsayıcılar arasında paylaşır. Önbellek ayarları hakkında daha fazla bilgi için bkz. [görüntü işleme Docker kapsayıcılarını yapılandırma](./computer-vision-resource-container-config.md).

### <a name="synchronous-read"></a>Zaman uyumlu okuma

Bir görüntüyü eşzamanlı olarak okumak için aşağıdaki işlemi kullanabilirsiniz. 

# <a name="version-32"></a>[Sürüm 3,2](#tab/version-3-2)

`POST /vision/v3.2/read/syncAnalyze` 

# <a name="version-20-preview"></a>[Sürüm 2,0-Önizleme](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

Resim tamamen okunsa ve yalnızca API bir JSON yanıtı döndürüyor. Bunun tek istisnası bir hata meydana gelir. Bir hata oluştuğunda aşağıdaki JSON döndürülür:

```json
{
    "status": "Failed"
}
```

JSON yanıt nesnesi, zaman uyumsuz sürümle aynı nesne grafiğine sahiptir. Bir JavaScript kullanıcısı ve tür güvenliği istiyorsanız, JSON yanıtını dönüştürmek için TypeScript kullanmayı düşünün.

Örnek kullanım örneği için, <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">burada TypeScript korumalı alanı</a> ' na bakın ve kullanım kolaylığını görselleştirmek için **Çalıştır** ' ı seçin.

## <a name="stop-the-container"></a>Kapsayıcıyı durdurma

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Sorun giderme

Kapsayıcıyı bir çıkış [bağlaması](./computer-vision-resource-container-config.md#mount-settings) ve günlüğü etkin olarak çalıştırırsanız kapsayıcı, kapsayıcıyı başlatırken veya çalıştırırken oluşan sorunları gidermek için yararlı olan günlük dosyaları oluşturur.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturalandırma

Bilişsel hizmetler kapsayıcıları, Azure hesabınızdaki ilgili kaynağı kullanarak faturalandırma bilgilerini Azure 'a gönderir.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Bu seçenekler hakkında daha fazla bilgi için bkz. [kapsayıcıları yapılandırma](./computer-vision-resource-container-config.md). 

## <a name="summary"></a>Özet

Bu makalede, Görüntü İşleme kapsayıcıları indirmek, yüklemek ve çalıştırmak için kavramları ve iş akışını öğrendiniz. Özet:

* Görüntü İşleme, Docker için bir Linux kapsayıcısı sağlar ve bu da kapsülleme okur.
* Okuma kapsayıcı görüntüsü bir uygulamanın çalıştırmasını gerektirir. 
* Kapsayıcı görüntüleri Docker 'da çalışır.
* Kapsayıcının ana bilgisayar URI 'sini belirterek, okuma OCR kapsayıcılarındaki işlemleri çağırmak için REST API ya da SDK kullanabilirsiniz.
* Bir kapsayıcıyı örnekledikten sonra faturalandırma bilgilerini belirtmeniz gerekir.

> [!IMPORTANT]
> Bilişsel hizmetler kapsayıcıları, ölçüm için Azure 'a bağlı kalmadan çalıştırılmak üzere lisanslanmaz. Müşterilerin, ödeme bilgilerini her zaman ölçüm hizmetiyle iletişimine olanak tanımak için kapsayıcıların etkinleştirilmesi gerekir. Bilişsel hizmetler kapsayıcıları, müşteri verilerini (örneğin, çözümlenmekte olan resim veya metin) Microsoft 'a göndermez.

## <a name="next-steps"></a>Sonraki adımlar

* Yapılandırma ayarları için [kapsayıcıları](computer-vision-resource-container-config.md) yapılandırmayı gözden geçir
* [OCR genel bakışını](overview-ocr.md) inceleyerek yazdırılmış ve el yazısı metin tanıma hakkında daha fazla bilgi edinin
* Kapsayıcı tarafından desteklenen yöntemler hakkındaki ayrıntılar için [okuma API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) 'sine bakın.
* Görüntü İşleme işlevlerle ilgili sorunları çözmek için [sık sorulan sorular (SSS)](FAQ.md) bölümüne bakın.
* Daha fazla bilişsel [Hizmetler kapsayıcısı](../cognitive-services-container-support.md) kullanın
