---
title: Birden çok kapsayıcıyı dağıtmak için Docker Compose kullanma
titleSuffix: Azure Cognitive Services
description: Birden çok bilişsel hizmet kapsayıcılarını dağıtmayı öğrenin. Bu makalede, Docker Compose kullanarak birden çok Docker kapsayıcı görüntüsünü nasıl düzenleyeceğinizi gösterilmektedir.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 95ec80af88e0b89f61bebed08f4b96a09947f401
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311545"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Birden çok kapsayıcıyı dağıtmak için Docker Compose kullanma

Bu makalede, birden çok Azure bilişsel hizmet kapsayıcısının nasıl dağıtılacağı gösterilir. Özellikle birden çok Docker kapsayıcı görüntüsünü düzenlemek için Docker Compose kullanmayı öğreneceksiniz.

> [Docker Compose](https://docs.docker.com/compose/) , çok Kapsayıcılı Docker uygulamalarını tanımlamaya ve çalıştırmaya yönelik bir araçtır. Oluşturma bölümünde, uygulamanızın hizmetlerini yapılandırmak için bir YAML dosyası kullanırsınız. Ardından, tek bir komut çalıştırarak, yapılandırmadan tüm hizmetleri oluşturup başlatabilirsiniz.

Tek bir ana bilgisayarda birden çok kapsayıcı görüntüsünü düzenlemek yararlı olabilir. Bu makalede, Metin Tanıma ve form tanıyıcı kapsayıcılarını birlikte ekleyeceğiz.

## <a name="prerequisites"></a>Önkoşullar

Bu yordam, yüklenmesi ve yerel olarak çalıştırılması gereken çeşitli araçlar gerektirir:

* Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* [Docker altyapısı](https://www.docker.com/products/docker-engine). Docker CLı 'nın konsol penceresinde çalıştığından emin olun.
* Doğru fiyatlandırma katmanına sahip bir Azure kaynağı. Yalnızca şu fiyatlandırma katmanları bu kapsayıcmayla çalışır:
  * Yalnızca F0 veya standart fiyatlandırma katmanıyla **görüntü işleme** kaynak.
  * Yalnızca F0 veya standart fiyatlandırma katmanı olan bir **tür tanıyıcı** kaynağı.
  * S0 fiyatlandırma katmanı ile bilişsel **Hizmetler** kaynağı.

## <a name="request-access-to-the-container-registry"></a>Kapsayıcı kayıt defterine erişim isteme

Bilişsel [Hizmetler konuşma kapsayıcıları istek formunu](https://aka.ms/speechcontainerspreview/)doldurun ve iletin. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Docker Compose dosyası

YAML dosyası, dağıtılacak tüm hizmetleri tanımlar. Bu hizmetler, `DockerFile` ya da mevcut bir kapsayıcı görüntüsünü kullanır. Bu durumda, iki önizleme görüntüsü kullanacağız. Aşağıdaki YAML dosyasını kopyalayıp yapıştırın ve *Docker-Compose. YAML*olarak kaydedin. Dosyada uygun **apikey**, **faturalandırma**ve **dosyasında değiştirilecek endpointUri** değerlerini sağlayın.

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: # < Your recognize text API key >
      billing: # < Your recognize text billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Konak makinede **birimler** düğümü altında belirtilen dizinleri oluşturun. Bu yaklaşım, birim bağlamaları kullanarak bir görüntüyü bağlamayı denemeden önce dizinlerin mevcut olması gerektiği için gereklidir.

## <a name="start-the-configured-docker-compose-services"></a>Yapılandırılmış Docker Compose hizmetlerini başlatın

Bir Docker Compose dosyası, tanımlı hizmetin yaşam döngüsünün tüm aşamaları yönetimine izin verebilir: Hizmetleri başlatma, durdurma ve yeniden oluşturma. hizmet durumunu görüntüleme; ve günlük akışını günlüğe kaydedin. Proje dizininden (Docker-Compose. YAML dosyasının bulunduğu) bir komut satırı arabirimi açın.

> [!NOTE]
> Hataları önlemek için konak makinenin, diskleri Docker altyapısına doğru şekilde paylaştığından emin olun. Örneğin, E:\publicpreview Docker-Compose. YAML dosyasında bir dizin olarak kullanılıyorsa, E sürücüsünü Docker ile paylaşabilirsiniz.

Komut satırı arabiriminden, Docker-Compose. YAML dosyasında tanımlanan tüm hizmetleri başlatmak (veya yeniden başlatmak) için aşağıdaki komutu yürütün:

```console
docker-compose up
```

Docker bu yapılandırmayı kullanarak **Docker-Compose up** komutunu ilk kez yürüttüğünde, **Hizmetler** düğümü altında yapılandırılan görüntüleri çeker ve sonra bunları indirir ve bağlar:

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:)...
latest: Pulling from microsoft/cognitive-services-recognize-text
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

Görüntüler indirildikten sonra görüntü hizmetleri başlatılır:

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>Hizmet kullanılabilirliğini doğrulama

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

İşte örnek bir çıktı:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text    latest
```

### <a name="test-the-recognize-text-container"></a>Metin Tanıma kapsayıcısını test etme

Ana makinede bir tarayıcı açın ve Docker-Compose  . YAML dosyasından http://localhost:5021/swagger/index.html belirtilen bağlantı noktasını kullanarak localhost 'a gidin. Metin Tanıma uç noktasını test etmek için API 'de "dene" özelliğini kullanabilirsiniz.

![Metin Tanıma kapsayıcı](media/recognize-text-swagger-page.png)

### <a name="test-the-form-recognizer-container"></a>Form tanıyıcı kapsayıcısını test etme

Ana makinede bir tarayıcı açın ve Docker-Compose  . YAML dosyasından http://localhost:5010/swagger/index.html belirtilen bağlantı noktasını kullanarak localhost 'a gidin. Form tanıyıcı uç noktasını test etmek için API 'de "dene" özelliğini kullanabilirsiniz.

![Form tanıyıcı kapsayıcısı](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bilişsel hizmetler kapsayıcıları](../cognitive-services-container-support.md)
