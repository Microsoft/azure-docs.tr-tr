---
title: Docker kapsayıcıları için Tarifler
titleSuffix: Azure Cognitive Services
description: Dağıtım ve yeniden kullanım için yapılandırma ayarlarınızın bazılarını veya tümünü kullanarak kapsayıcıları nasıl oluşturacağınızı, test ve depolayacağınızı öğrenin.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: dbe2e288309b6682041bf3db9fe3d39455359806
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647275"
---
# <a name="create-containers-for-reuse"></a>Yeniden kullanım için kapsayıcı oluşturma

Yeniden kullanılabilen bilişsel hizmetler kapsayıcıları oluşturmak için bu kapsayıcı tariflerini kullanın. Kapsayıcılar, kapsayıcı başlatıldığında gerekli _olmaması_ için bazı veya tüm yapılandırma ayarları ile oluşturulabilir.

Bu yeni kapsayıcı katmanı (ayarlarla) varsa ve yerel olarak test ederseniz kapsayıcıyı bir kapsayıcı kayıt defterine kaydedebilirsiniz. Kapsayıcı başladığında, bu ayarlara yalnızca kapsayıcıda depolanmayan ayarlar gerekecektir. Özel kayıt kapsayıcısı, içindeki bu ayarları geçirmeniz için yapılandırma alanı sağlar.

## <a name="docker-run-syntax"></a>Docker Run sözdizimi

Bu belgedeki tüm `docker run` örnekleri, bir Windows konsolunun `^` satırı devamlılık karakteriyle varsayılmıştır. Kendi kullanımı için aşağıdakileri göz önünde bulundurun:

* Docker Kapsayıcıları hakkında bilginiz yoksa bağımsız değişkenlerin sırasını değiştirmeyin.
* Windows dışında bir işletim sistemi veya Windows konsolu dışında bir konsol kullanıyorsanız, doğru konsol/Terminal, bağlar için klasör söz dizimini ve konsolunuz ve sisteminiz için satır devamlılık karakteri kullanın.  Bilişsel hizmetler kapsayıcısı bir Linux işletim sistemi olduğundan, hedef bağlama bir Linux stili klasör söz dizimini kullanır.
* `docker run` örnekler, Windows 'ta herhangi bir izin çakışmasını önlemek için `c:` sürücüdeki dizini kullanır. Giriş dizini olarak belirli bir dizin kullanmanız gerekiyorsa, Docker hizmeti iznini vermeniz gerekebilir.

## <a name="store-no-configuration-settings-in-image"></a>Görüntüde yapılandırma ayarları depolama

Her hizmet için örnek `docker run` komutları kapsayıcıda hiçbir yapılandırma ayarını depolamaz. Kapsayıcıyı bir konsoldan veya kayıt defteri hizmetinden başlattığınızda, bu yapılandırma ayarlarının geçmesi gerekir. Özel kayıt kapsayıcısı, içindeki bu ayarları geçirmeniz için yapılandırma alanı sağlar.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Tarifi yeniden kullan: tüm yapılandırma ayarlarını kapsayıcı ile depola

Tüm yapılandırma ayarlarını depolamak için bu ayarlarla bir `Dockerfile` oluşturun.

Bu yaklaşımla ilgili sorunlar:

* Yeni kapsayıcının özgün kapsayıcısından ayrı bir adı ve etiketi vardır.
* Bu ayarları değiştirmek için Dockerfile değerlerini değiştirmeniz, görüntüyü yeniden oluşturmanız ve Kayıt defterinize yeniden yayımlamanız gerekir.
* Birisi kapsayıcı Kayıt defterinize veya yerel ana bilgisayarınıza erişim alırsa, kapsayıcıyı çalıştırabilir ve bilişsel hizmetler uç noktalarını kullanabilir.
* Bilişsel hizmetiniz giriş takmaları gerektirmiyorsa `COPY` satırları Dockerfile 'a eklemeyin.

Kullanmak istediğiniz mevcut bilişsel hizmetler kapsayıcısından çekerek Dockerfile oluşturun, sonra kapsayıcının ihtiyaç duyacağı bilgileri ayarlamak veya çekmek için Dockerfile içindeki Docker komutlarını kullanın.

Bu örnek:

* `ENV`kullanarak ana bilgisayarın ortam anahtarından `{BILLING_ENDPOINT}` faturalandırma uç noktasını ayarlar.
* ' ENV ' kullanarak konağın ortam anahtarından faturalandırma API anahtarı, `{ENDPOINT_KEY}` ayarlar.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Tarifi yeniden kullan: kapsayıcı ile faturalandırma ayarlarını depolayın

Bu örnek, bir Dockerfile 'dan Metin Analizi ' yaklaşım kapsayıcısının nasıl oluşturulacağını gösterir.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Kapsayıcıyı [yerel olarak](#how-to-use-container-on-your-local-host) veya [özel kayıt defteri kapsayıcısından](#how-to-add-container-to-private-registry) gerektiği şekilde derleyin ve çalıştırın.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Tarifi yeniden kullanma: depolama ve bağlama ayarlarını kapsayıcı ile depolayın

Bu örnek, Dockerfile 'dan Language Understanding, faturalandırma ve modellerin nasıl kullanılacağını gösterir.

* `COPY`kullanarak Language Understanding (LUSıS) modeli dosyasını konağın dosya sisteminden kopyalar.
* LUSıS kapsayıcısı birden fazla modeli destekler. Tüm modeller aynı klasörde depolanıyorsa, hepsi bir `COPY` bildirimine gerek duyarsınız.
* Model giriş dizininin göreli üst öğesinden Docker dosyasını çalıştırın. Aşağıdaki örnekte, `/input`göreli üst öğesinden `docker build` ve `docker run` komutlarını çalıştırın. `COPY` komutundaki ilk `/input`, ana bilgisayarın dizinidir. İkinci `/input` kapsayıcının dizinidir.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Kapsayıcıyı [yerel olarak](#how-to-use-container-on-your-local-host) veya [özel kayıt defteri kapsayıcısından](#how-to-add-container-to-private-registry) gerektiği şekilde derleyin ve çalıştırın.

## <a name="how-to-use-container-on-your-local-host"></a>Yerel ana bilgisayarınızda kapsayıcı kullanma

Docker dosyasını oluşturmak için `<your-image-name>` görüntünün yeni adıyla değiştirin, ardından şunu kullanın:

```console
docker build -t <your-image-name> .
```

Görüntüyü çalıştırmak ve kapsayıcı durdurulduğunda kaldırmak için (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Özel kayıt defterine kapsayıcı ekleme

Dockerfile dosyasını kullanmak ve yeni görüntüyü özel kapsayıcı Kayıt defterinize yerleştirmek için bu adımları izleyin.  

1. Yeniden kullanım tarifi ' nden metin ile bir `Dockerfile` oluşturun. `Dockerfile` uzantısı yok.

1. Açılı ayraçlar içindeki tüm değerleri kendi değerlerinizle değiştirin.

1. Aşağıdaki komutu kullanarak dosyayı komut satırında veya terminalde bir görüntüde oluşturun. `<>`açılı ayraçlar içindeki değerleri, kendi kapsayıcı adınız ve etiketlerinizle değiştirin.  

    `-t`Tag seçeneği, kapsayıcı için ne değiştirdikleriniz hakkında bilgi eklemenin bir yoludur. Örneğin, `modified-LUIS` kapsayıcı adı özgün kapsayıcının katmanlı olduğunu gösterir. `with-billing-and-model` etiket adı Language Understanding (LUA) kapsayıcısının nasıl değiştirildiğini gösterir.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Bir konsoldan Azure CLı 'da oturum açın. Bu komut bir tarayıcı açar ve kimlik doğrulaması gerektirir. Kimliği doğrulandıktan sonra, tarayıcıyı kapatabilir ve konsolunda çalışmaya devam edebilirsiniz.

    ```azurecli
    az login
    ```

1. Bir konsoldan Azure CLı ile özel kayıt defterinizde oturum açın.

    `<my-registry>`, açılı ayraçlar içindeki değerleri kendi kayıt defteri adınızla değiştirin.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    Hizmet sorumlusu atadıysanız de Docker oturum açma ile oturum açabilirsiniz.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Kapsayıcıyı özel kayıt defteri konumuyla etiketleyin. `<my-registry>`, açılı ayraçlar içindeki değerleri kendi kayıt defteri adınızla değiştirin. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Etiket adı kullanmıyorsanız `latest` kapsanır.

1. Yeni görüntüyü özel kapsayıcı Kayıt defterinize gönderin. Özel kapsayıcı kayıt defterinizi görüntülediğinizde, aşağıdaki CLı komutunda kullanılan kapsayıcı adı deponun adı olacaktır.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Container Instance oluşturma ve kullanma](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->