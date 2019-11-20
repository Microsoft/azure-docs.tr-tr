---
title: Azure Container Registry görevleri başvurusu-YAML
description: Görev özellikleri, adım türleri, adım özellikleri ve yerleşik değişkenler de dahil olmak üzere ACR görevleri için YAML görevlerini tanımlamaya yönelik başvuru.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 10/23/2019
ms.author: danlep
ms.openlocfilehash: 6e55b65d58fe6545d8212b4233f2f45261d18ee5
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043876"
---
# <a name="acr-tasks-reference-yaml"></a>ACR görevleri başvurusu: YAML

ACR görevlerinde çok adımlı görev tanımı, kapsayıcıları oluşturmaya, teste ve düzeltme eki uygulamaya odaklanan kapsayıcı merkezli bir işlem temel alanı sağlar. Bu makalede, çok adımlı görevlerinizi tanımlayan YAML dosyalarının komutları, parametreleri, özellikleri ve söz dizimi ele alınmaktadır.

Bu makale, ACR görevleri için çok adımlı görev YAML dosyaları oluşturmak için başvuru içerir. ACR görevlerine giriş istiyorsanız [ACR görevlerine genel bakış ' a](container-registry-tasks-overview.md)bakın.

## <a name="acr-taskyaml-file-format"></a>ACR-Task. YAML dosya biçimi

ACR görevleri standart YAML sözdiziminde çok adımlı görev bildirimini destekler. Bir YAML dosyasında bir görevin adımlarını tanımlarsınız. Daha sonra dosyayı [az ACR Run][az-acr-run] komutuna geçirerek görevi el ile çalıştırabilirsiniz. Ya da bir git işlemesinde veya temel görüntü güncelleştirmesinde otomatik olarak tetiklenen [az ACR görev oluşturma][az-acr-task-create] ile bir görev oluşturmak için dosyasını kullanın. Bu makale, adımları içeren dosya olarak `acr-task.yaml` anlamına gelse de, ACR görevleri [desteklenen bir uzantıya](#supported-task-filename-extensions)sahip tüm geçerli dosya adını destekler.

En üst düzey `acr-task.yaml` temel elemanlar **görev özellikleri**, **adım türleri**ve **adım özelliklerdir**:

* Görev [özellikleri](#task-properties) , görev yürütme boyunca tüm adımlara uygulanır. Aşağıdakiler dahil olmak üzere birkaç genel görev özelliği vardır:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Görev adımı türleri](#task-step-types) , bir görevde gerçekleştirilebilecek eylem türlerini temsil eder. Üç adım türü vardır:
  * `build`
  * `push`
  * `cmd`
* [Görev adımı özellikleri](#task-step-properties) , tek bir adıma uygulanan parametrelerdir. Aşağıdakiler dahil olmak üzere birkaç adım özelliği vardır:
  * `startDelay`
  * `timeout`
  * `when`
  * ... ve çok daha fazlası.

Bazı yaygın adım özellikleri de dahil olmak üzere bir `acr-task.yaml` dosyasının temel biçimi aşağıdaki gibidir. Tüm kullanılabilir adım özelliklerinin veya adım türü kullanımının ayrıntılı bir gösterimi olmasa da temel dosya biçimine hızlı bir genel bakış sağlar.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Desteklenen görev dosya adı uzantıları

ACR görevleri, `.yaml`dahil olmak üzere birkaç dosya adı uzantısını ayırmıştır ve bu işlem bir görev dosyası olarak işlem görür. Aşağıdaki listede *olmayan* herhangi bir uzantı, ACR görevlerinin bir Dockerfile olarak değerlendirilir:. YAML,. yml,. TOML,. JSON,. sh,. Bash,. ZSH,. ps1,. PS,. cmd,. bat,. TS,. js,. php,.,. RB,. lua

YAML, şu anda ACR görevleri tarafından desteklenen tek dosya biçimidir. Diğer dosya adı uzantıları olası gelecekteki destek için ayrılmıştır.

## <a name="run-the-sample-tasks"></a>Örnek görevleri çalıştırma

Bu makalenin aşağıdaki bölümlerinde başvurulan birkaç örnek görev dosyası vardır. Örnek görevler ortak bir GitHub deposunda, [Azure-Samples/ACR-Tasks][acr-tasks]' dadır. Bunları, [az ACR Run][az-acr-run]Azure CLI komutuyla çalıştırabilirsiniz. Örnek komutlar şuna benzerdir:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Örnek komutların biçimlendirmesi, Azure CLı 'de varsayılan bir kayıt defteri yapılandırdığınız varsayılmaktadır, bu nedenle `--registry` parametresini atlarlar. Varsayılan bir kayıt defteri yapılandırmak için, [az configure][az-configure] komutunu bir `acr=REGISTRY_NAME` değeri kabul eden `--defaults` parametresiyle kullanın.

Örneğin, Azure CLı 'yı "myregistry" adlı varsayılan bir kayıt defteriyle yapılandırmak için:

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Görev özellikleri

Görev özellikleri genellikle `acr-task.yaml` bir dosyanın en üstünde görünür ve görev adımlarının tam yürütülmesi boyunca uygulanan genel özelliklerdir. Bu genel özelliklerden bazıları, tek bir adım içinde geçersiz kılınabilir.

| Özellik | Tür | İsteğe Bağlı | Açıklama | Geçersiz kılma destekleniyor | Varsayılan değer |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | Yes | ACR görevler hizmeti tarafından ayrıştırılabilen `acr-task.yaml` dosyanın sürümü. ACR görevleri geriye dönük uyumluluğu sürdürmeye devam ederken, bu değer ACR görevlerinin tanımlı bir sürüm içinde uyumluluğu korumasına olanak tanır. Belirtilmemişse, varsayılan olarak en son sürümü alır. | Hayır | Hiçbiri |
| `stepTimeout` | int (saniye) | Yes | Bir adımın çalıştırılacağı en fazla saniye sayısı. Özellik bir görevde belirtilmişse, tüm adımların varsayılan `timeout` özelliğini ayarlar. `timeout` özelliği bir adımda belirtilmişse, görev tarafından sunulan özelliği geçersiz kılar. | Yes | 600 (10 dakika) |
| `workingDirectory` | string | Yes | Çalışma zamanı sırasında kapsayıcının çalışma dizini. Özellik bir görevde belirtilmişse, tüm adımların varsayılan `workingDirectory` özelliğini ayarlar. Bir adımda belirtilmişse, görev tarafından sunulan özelliği geçersiz kılar. | Yes | `$HOME` |
| `env` | [dize, dize,...] | Yes |  Görevin ortam değişkenlerini tanımlayan `key=value` biçimdeki dizeler dizisi. Özellik bir görevde belirtilmişse, tüm adımların varsayılan `env` özelliğini ayarlar. Bir adımda belirtilmişse, görevden devralınan tüm ortam değişkenlerini geçersiz kılar. | Hiçbiri |
| `secrets` | [gizli, gizli,...] | Yes | [Gizli](#secret) nesneler dizisi. | Hiçbiri |
| `networks` | [Ağ, ağ,...] | Yes | [Ağ](#network) nesneleri dizisi. | Hiçbiri |

### <a name="secret"></a>gizli dizi

Gizli nesne aşağıdaki özelliklere sahiptir.

| Özellik | Tür | İsteğe Bağlı | Açıklama | Varsayılan değer |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | string | Hayır | Gizli dizi tanımlayıcısı. | Hiçbiri |
| `keyvault` | string | Yes | Azure Key Vault gizli URL 'SI. | Hiçbiri |
| `clientID` | string | Yes | Azure kaynakları için [Kullanıcı tarafından atanan yönetilen kimliğin](container-registry-tasks-authentication-managed-identity.md) istemci kimliği. | Hiçbiri |

### <a name="network"></a>Network

Ağ nesnesi aşağıdaki özelliklere sahiptir.

| Özellik | Tür | İsteğe Bağlı | Açıklama | Varsayılan değer |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | Hayır | Ağın adı. | Hiçbiri |
| `driver` | string | Yes | Ağı yönetmek için sürücü. | Hiçbiri |
| `ipv6` | bool | Yes | IPv6 ağ oluşturma özelliğinin etkin olup olmadığı. | `false` |
| `skipCreation` | bool | Yes | Ağ oluşturma atlanıp atlanmayacağı. | `false` |
| `isDefault` | bool | Yes | Ağın Azure Container Registry ile belirtilen bir varsayılan ağ olup olmadığı | `false` |

## <a name="task-step-types"></a>Görev adımı türleri

ACR görevleri üç adım türünü destekler. Her adım türü, her adım türü için bölümünde ayrıntılı olarak açıklanan birkaç özelliği destekler.

| Adım türü | Açıklama |
| --------- | ----------- |
| [`build`](#build) | Tanıdık `docker build` sözdizimini kullanarak bir kapsayıcı görüntüsü oluşturur. |
| [`push`](#push) | Yeni oluşturulan veya retagged görüntülerinin bir kapsayıcı kayıt defterine `docker push` yürütür. Azure Container Registry, diğer özel kayıt defterleri ve genel Docker Hub desteklenir. |
| [`cmd`](#cmd) | Kapsayıcının `[ENTRYPOINT]`geçirilen parametreleri içeren bir kapsayıcıyı komut olarak çalıştırır. `cmd` adım türü `env`, `detach`ve diğer tanıdık `docker run` komut seçenekleri gibi parametreleri destekler, eşzamanlı kapsayıcı yürütmesi ile birim ve işlevsel testi etkinleştirir. |

## <a name="build"></a>Derlemeyi

Kapsayıcı görüntüsü oluşturun. `build` adım türü, bulutta ilk sınıf temel olarak `docker build` çalıştırmanın çok kiracılı ve güvenli bir şekilde temsil eder.

### <a name="syntax-build"></a>Sözdizimi: derleme

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

`build` adımı türü aşağıdaki tablodaki parametreleri destekler. `build` Step Type, derleme zamanı değişkenlerini ayarlamak için `--build-arg` gibi [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) komutunun tüm derleme seçeneklerini de destekler.

| Parametre | Açıklama | İsteğe Bağlı |
| --------- | ----------- | :-------: |
| `-t` &#124;`--image` | Oluşturulan görüntünün tam nitelikli `image:tag` tanımlar.<br /><br />Görüntü, işlevsel testler gibi iç görev doğrulamaları için kullanılabilir, tüm görüntülerin bir kayıt defterine `push` gerektirmez. Ancak, bir görev yürütme içindeki bir görüntüyü örnek olarak, görüntünün başvuru için bir adı olması gerekir.<br /><br />`az acr build`farklı olarak, ACR görevlerinin çalıştırılması varsayılan gönderme davranışını sağlamaz. ACR görevleri ile, varsayılan senaryo bir görüntüyü oluşturma, doğrulama ve gönderme özelliğini kabul eder. İsteğe bağlı olarak oluşturulan görüntüleri gönderme hakkında bilgi için bkz. [gönderim](#push) . | Yes |
| `-f` &#124;`--file` | `docker build`geçirilen Dockerfile dosyasını belirtir. Belirtilmemişse, bağlam kökündeki varsayılan Dockerfile varsayılır. Bir Dockerfile belirtmek için, dosya adını bağlamın köküne göre geçirin. | Yes |
| `context` | `docker build`öğesine geçirilen kök dizin. Her görevin kök dizini paylaşılan bir [WorkingDirectory](#task-step-properties)olarak ayarlanır ve ilişkili git klonlanmış dizinin kökünü içerir. | Hayır |

### <a name="properties-build"></a>Özellikler: derleme

`build` adımı türü aşağıdaki özellikleri destekler. Bu makalenin [görev adımı özellikleri](#task-step-properties) bölümünde bu özelliklerin ayrıntılarını bulun.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | İsteğe Bağlı |
| `disableWorkingDirectoryOverride` | bool | İsteğe Bağlı |
| `entryPoint` | string | İsteğe Bağlı |
| `env` | [dize, dize,...] | İsteğe Bağlı |
| `expose` | [dize, dize,...] | İsteğe Bağlı |
| `id` | string | İsteğe Bağlı |
| `ignoreErrors` | bool | İsteğe Bağlı |
| `isolation` | string | İsteğe Bağlı |
| `keep` | bool | İsteğe Bağlı |
| `network` | object | İsteğe Bağlı |
| `ports` | [dize, dize,...] | İsteğe Bağlı |
| `pull` | bool | İsteğe Bağlı |
| `repeat` | int | İsteğe Bağlı |
| `retries` | int | İsteğe Bağlı |
| `retryDelay` | int (saniye) | İsteğe Bağlı |
| `secret` | object | İsteğe Bağlı |
| `startDelay` | int (saniye) | İsteğe Bağlı |
| `timeout` | int (saniye) | İsteğe Bağlı |
| `when` | [dize, dize,...] | İsteğe Bağlı |
| `workingDirectory` | string | İsteğe Bağlı |

### <a name="examples-build"></a>Örnekler: derleme

#### <a name="build-image---context-in-root"></a>Derleme görüntüsü-kökte bağlam

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Alt dizinde derleme görüntüsü-bağlam

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>hareketle

Bir veya daha fazla oluşturulmuş veya retagged görüntüsünü bir kapsayıcı kayıt defterine gönderin. Azure Container Registry veya genel Docker Hub 'ına gibi özel kayıt defterlerine göndermeyi destekler.

### <a name="syntax-push"></a>Sözdizimi: Push

`push` adım türü bir görüntü koleksiyonunu destekler. YAML koleksiyon sözdizimi, satır içi ve iç içe biçimleri destekler. Tek bir görüntünün gönderilmesi genellikle satır içi sözdizimi kullanılarak temsil edilir:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Daha fazla okunabilirlik için, birden çok görüntü gönderirken iç içe geçmiş sözdizimini kullanın:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Özellikler: gönderim

`push` adımı türü aşağıdaki özellikleri destekler. Bu makalenin [görev adımı özellikleri](#task-step-properties) bölümünde bu özelliklerin ayrıntılarını bulun.

| | | |
| -------- | ---- | -------- |
| `env` | [dize, dize,...] | İsteğe Bağlı |
| `id` | string | İsteğe Bağlı |
| `ignoreErrors` | bool | İsteğe Bağlı |
| `startDelay` | int (saniye) | İsteğe Bağlı |
| `timeout` | int (saniye) | İsteğe Bağlı |
| `when` | [dize, dize,...] | İsteğe Bağlı |

### <a name="examples-push"></a>Örnekler: Push

#### <a name="push-multiple-images"></a>Birden çok görüntü gönderme

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Oluşturma, gönderme ve çalıştırma

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

`cmd` adımı türü bir kapsayıcı çalıştırır.

### <a name="syntax-cmd"></a>Sözdizimi: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Özellikler: cmd

`cmd` adımı türü aşağıdaki özellikleri destekler:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | İsteğe Bağlı |
| `disableWorkingDirectoryOverride` | bool | İsteğe Bağlı |
| `entryPoint` | string | İsteğe Bağlı |
| `env` | [dize, dize,...] | İsteğe Bağlı |
| `expose` | [dize, dize,...] | İsteğe Bağlı |
| `id` | string | İsteğe Bağlı |
| `ignoreErrors` | bool | İsteğe Bağlı |
| `isolation` | string | İsteğe Bağlı |
| `keep` | bool | İsteğe Bağlı |
| `network` | object | İsteğe Bağlı |
| `ports` | [dize, dize,...] | İsteğe Bağlı |
| `pull` | bool | İsteğe Bağlı |
| `repeat` | int | İsteğe Bağlı |
| `retries` | int | İsteğe Bağlı |
| `retryDelay` | int (saniye) | İsteğe Bağlı |
| `secret` | object | İsteğe Bağlı |
| `startDelay` | int (saniye) | İsteğe Bağlı |
| `timeout` | int (saniye) | İsteğe Bağlı |
| `when` | [dize, dize,...] | İsteğe Bağlı |
| `workingDirectory` | string | İsteğe Bağlı |

Bu özelliklerin ayrıntılarını bu makalenin [görev adımı özellikleri](#task-step-properties) bölümünde bulabilirsiniz.

### <a name="examples-cmd"></a>Örnekler: cmd

#### <a name="run-hello-world-image"></a>Merhaba-Dünya görüntüsünü Çalıştır

Bu komut, Docker Hub 'daki [Merhaba-Dünya](https://hub.docker.com/_/hello-world/) görüntüsüne başvuran `hello-world.yaml` görev dosyasını yürütür.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Bash görüntüsünü ve "Hello World" yankısını çalıştırın

Bu komut, Docker Hub 'da [Bash](https://hub.docker.com/_/bash/) görüntüsüne başvuran `bash-echo.yaml` görev dosyasını yürütür.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Belirli Bash Image etiketini Çalıştır

Belirli bir görüntü sürümünü çalıştırmak için `cmd`etiketi belirtin.

Bu komut, Docker Hub 'da [bash: 3.0](https://hub.docker.com/_/bash/) görüntüsüne başvuran `bash-echo-3.yaml` görev dosyasını yürütür.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Özel görüntüleri Çalıştır

`cmd` adım türü, standart `docker run` biçimini kullanarak görüntülere başvurur. Bir kayıt defteriyle önceden kullanıma hazır olmayan görüntülerin docker.io 'tan kaynaklandığından varsayılır. Önceki örnek eşit olarak temsil edilebilir:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

`cmd`, standart `docker run` resim başvuru kuralını kullanarak, herhangi bir özel kayıt defterinden veya genel Docker Hub 'ından görüntü çalıştırabilir. ACR görevinin yürütüldüğü aynı kayıt defterindeki görüntülere başvuruyorsanız, kayıt defteri kimlik bilgilerini belirtmeniz gerekmez.

* Azure Container Registry 'den bir görüntü çalıştırın. Aşağıdaki örnek, `myregistry`adlı bir kayıt defteriniz olduğunu ve `myimage:mytag`özel bir görüntü olduğunu varsayar.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Bir çalıştırma değişkeni veya diğer adla kayıt defteri başvurusunu genelleştirin

    Kayıt defteri adınızı bir `acr-task.yaml` dosyasında sabit kodlamak yerine, bir [çalıştırma değişkeni](#run-variables) veya [diğer ad](#aliases)kullanarak daha taşınabilir hale getirebilirsiniz. `Run.Registry` değişkeni veya `$Registry` diğer adı, çalışma zamanında görevin yürütüldüğü kayıt defterinin adına genişletilir.

    Örneğin, önceki görevi herhangi bir Azure Container Registry 'de çalışacak şekilde genelleştirmek için, görüntü adı içindeki $Registry değişkenine başvurun:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>Görev adımı özellikleri

Her adım türü, türü için uygun olan birkaç özelliği destekler. Aşağıdaki tabloda, tüm kullanılabilir adım özellikleri tanımlanmaktadır. Tüm adım türleri tüm özellikleri desteklemez. Her adım türü için bu özelliklerden hangilerinin kullanılabildiğini görmek için, [cmd](#cmd), [Build](#build)ve [Push](#push) Step Type başvuru bölümlerine bakın.

| Özellik | Tür | İsteğe Bağlı | Açıklama | Varsayılan değer |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Yes | Çalışma sırasında kapsayıcının ayrılmayacağı. | `false` |
| `disableWorkingDirectoryOverride` | bool | Yes | `workingDirectory` geçersiz kılma işlevinin devre dışı bırakılıp başlatılmayacağını belirtir. Kapsayıcının çalışma dizini üzerinde tamamen denetim sağlamak için bunu `workingDirectory` birlikte kullanın. | `false` |
| `entryPoint` | string | Yes | Bir adımın kapsayıcısının `[ENTRYPOINT]` geçersiz kılar. | Hiçbiri |
| `env` | [dize, dize,...] | Yes | Adım için ortam değişkenlerini tanımlayan `key=value` biçimdeki dizeler dizisi. | Hiçbiri |
| `expose` | [dize, dize,...] | Yes | Kapsayıcıdan sunulan bağlantı noktası dizisi. |  Hiçbiri |
| [`id`](#example-id) | string | Yes | Görevi içindeki adımı benzersiz bir şekilde tanımlar. Görevdeki diğer adımlar, `when`ile bağımlılık denetimi gibi bir adım `id`başvuruda bulunabilir.<br /><br />`id` aynı zamanda çalışan kapsayıcının adıdır. Görevdeki diğer kapsayıcılar üzerinde çalışan süreçler, DNS ana bilgisayar adı olarak `id` veya Docker günlükleri [ID] ile erişim için, örneğin. | `acb_step_%d`, `%d` YAML dosyasında adımın 0 tabanlı dizinidir |
| `ignoreErrors` | bool | Yes | Kapsayıcının yürütülmesi sırasında bir hata oluşup oluşmadığını ne olursa olsun adımın başarıyla işaretleneceğini belirtir. | `false` |
| `isolation` | string | Yes | Kapsayıcının yalıtım düzeyi. | `default` |
| `keep` | bool | Yes | Adım kapsayıcısının yürütmeden sonra tutulup tutulmayacağını belirtir. | `false` |
| `network` | object | Yes | Kapsayıcının çalıştığı bir ağı tanımlar. | Hiçbiri |
| `ports` | [dize, dize,...] | Yes | Kapsayıcıdan konağa yayınlanan bağlantı noktaları dizisi. |  Hiçbiri |
| `pull` | bool | Yes | Herhangi bir önbelleğe alma davranışına engel olmak için yürütmeden önce kapsayıcının yapılıp yapılmayacağını belirtir. | `false` |
| `privileged` | bool | Yes | Kapsayıcının ayrıcalıklı modda çalıştırılıp çalıştırılmayacağı. | `false` |
| `repeat` | int | Yes | Kapsayıcının yürütülmesini yinelemek için yeniden deneme sayısı. | 0 |
| `retries` | int | Yes | Bir kapsayıcının yürütülmesinin başarısız olması durumunda denenecek yeniden deneme sayısı. Yeniden deneme yalnızca bir kapsayıcının çıkış kodu sıfır değilse denenir. | 0 |
| `retryDelay` | int (saniye) | Yes | Bir kapsayıcının çalışmasının yeniden denemeleri arasındaki gecikme süresi (saniye cinsinden). | 0 |
| `secret` | object | Yes | [Azure kaynakları için](container-registry-tasks-authentication-managed-identity.md)Azure Key Vault gizli dizi veya yönetilen kimlik tanımlar. | Hiçbiri |
| `startDelay` | int (saniye) | Yes | Kapsayıcının yürütülmesinin geciktirileceği saniye sayısı. | 0 |
| `timeout` | int (saniye) | Yes | Bir adımın sonlandırılmadan önce yürütebilmesi için gereken en fazla saniye sayısı. | 600 |
| [`when`](#example-when) | [dize, dize,...] | Yes | Görevin içindeki bir veya daha fazla adım için bir adımın bağımlılığını yapılandırır. | Hiçbiri |
| `user` | string | Yes | Bir kapsayıcının Kullanıcı adı veya UID 'SI | Hiçbiri |
| `workingDirectory` | string | Yes | Bir adım için çalışma dizinini ayarlar. Varsayılan olarak ACR görevleri, çalışma dizini olarak bir kök dizin oluşturur. Ancak, derlemeniz birkaç adım içeriyorsa, önceki adımlar aynı çalışma dizinini belirterek yapıtları sonraki adımlarla paylaşabilir. | `$HOME` |

### <a name="examples-task-step-properties"></a>Örnekler: görev adımı özellikleri

#### <a name="example-id"></a>Örnek: kimlik

İki görüntü oluşturun ve işlevsel test görüntüsünü örnekleyebilirsiniz. Her adım, `when` özelliğinde görev başvurusunda bulunan diğer adımların `id` benzersiz bir şekilde tanımlanır.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Örnek: ne zaman

`when` özelliği, görevin içindeki diğer adımlara bir adımın bağımlılığını belirtir. İki parametre değerini destekler:

* `when: ["-"]`-diğer adımlara bağımlılığı olmadığını gösterir. `when: ["-"]` belirten bir adım yürütme hemen başlayacaktır ve eşzamanlı adım yürütülmesine olanak sağlayacaktır.
* `when: ["id1", "id2"]`-adımın "ID1" `id` ve "ID2" `id` olan adımlara bağlı olduğunu gösterir. "ID1" ve "ID2" adımları tamamlanana kadar bu adım yürütülmez.

Bir adımda `when` belirtilmemişse, bu adım `acr-task.yaml` dosyasındaki önceki adımın tamamlanmasına bağlıdır.

`when`olmadan sıralı adım yürütme:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

`when`ile sıralı adım yürütme:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Paralel görüntü oluşturma:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Paralel görüntü oluşturma ve bağımlı test:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Değişkenleri Çalıştır

ACR görevleri, yürütdiklerinde görev adımları için kullanılabilen varsayılan bir değişken kümesi içerir. Bu değişkenlere `{{.Run.VariableName}}`biçim kullanılarak erişilebilir; burada `VariableName` aşağıdakilerden biridir:

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

Değişken adları genellikle kendi kendine açıklayıcıdır. Sık kullanılan değişkenler için Ayrıntılar aşağıda verilmiştir. YAML sürüm `v1.1.0`itibariyle, çoğu çalıştırma değişkeninin yerine kısaltılmış, önceden tanımlanmış bir [görev diğer adı](#aliases) kullanabilirsiniz. Örneğin, `{{.Run.Registry}}`yerine `$Registry` diğer adı kullanın.

### <a name="runid"></a>Run.ID

Her çalıştırma, `az acr run`veya `az acr task create`kullanılarak oluşturulan görevlerin tetiklenmesi, benzersiz bir KIMLIĞE sahiptir. KIMLIK Şu anda yürütülmekte olan çalışmayı temsil eder.

Genellikle bir görüntüyü benzersiz bir şekilde etiketlemek için kullanılır:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Çalıştır. kayıt defteri

Kayıt defterinin tam sunucu adı. Genellikle görevin çalıştırıldığı kayıt defterine genel olarak başvurmak için kullanılır.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Çalışma. RegistryName

Kapsayıcı kayıt defterinin adı. Genellikle, bir tam sunucu adı gerektirmeyen görev adımlarında kullanılır, örneğin, kayıt defterlerinde Azure CLı komutlarını çalıştıran adımlar `cmd`.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run. Date

Çalıştırmanın başladığı geçerli UTC saati.

### <a name="runcommit"></a>Run. COMMIT

GitHub deposuna yapılan bir kayıt tarafından tetiklenen bir görev için, COMMIT tanımlayıcısı.

### <a name="runbranch"></a>Run. Branch

GitHub deposuna yapılan bir kayıt tarafından tetiklenen bir görev için, dal adı.

## <a name="aliases"></a>Diğer adlar

`v1.1.0`itibariyle ACR görevleri, yürütme sırasında görev adımları için kullanılabilen diğer adları destekler. Diğer adlar, Bash 'de desteklenen diğer adlar (komut kısayolları) kavramıyla benzerdir. 

Bir diğer ad ile, tek bir kelime girerek herhangi bir komutu veya komut grubunu (Seçenekler ve dosya adları dahil) başlatabilirsiniz.

ACR görevleri, önceden tanımlanmış birkaç diğer adı ve ayrıca oluşturduğunuz özel diğer adları destekler.

### <a name="predefined-aliases"></a>Önceden tanımlanmış diğer adlar

Aşağıdaki görev diğer adları, [çalıştırma değişkenlerinin](#run-variables)yerine kullanılabilir:

| Diğer ad | Çalıştırma değişkeni |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

Görev adımları ' nda, bu örnekte olduğu gibi `$` yönergesi ile bir diğer addan önce gelmeli:

```yaml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Görüntü diğer adları

Aşağıdaki diğer adların her biri Microsoft Container Registry (MCR) içindeki kararlı bir görüntüye işaret eder. Bir yönerge kullanmadan bir görev dosyasının `cmd` bölümünde bunların her birine başvurabilirsiniz.

| Diğer ad | Görüntü |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:d0725bc` |
| `bash` | `mcr.microsoft.com/acr/bash:d0725bc` |
| `curl` | `mcr.microsoft.com/acr/curl:d0725bc` |

Aşağıdaki örnek görev, çalışma kayıt defterindeki `samples/hello-world`, çalışma kayıt defterindeki 7 günden daha eski olan görüntü etiketlerini [temizlemek](container-registry-auto-purge.md) için birkaç diğer ad kullanır:

```yaml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Özel diğer ad

YAML dosyanızda özel bir diğer ad tanımlayın ve aşağıdaki örnekte gösterildiği gibi kullanın. Diğer ad yalnızca alfasayısal karakterler içerebilir. Diğer adı genişletmek için varsayılan yönerge `$` karakterdir.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

Özel diğer ad tanımları için uzak veya yerel bir YAML dosyasına bağlantı oluşturabilirsiniz. Aşağıdaki örnek, Azure Blob depolama alanındaki bir YAML dosyasına bağlantı sağlar:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Sonraki adımlar

Çok adımlı görevlere genel bakış için [ACR görevlerinde multi-step Build, test ve Patch görevlerini çalıştırma](container-registry-tasks-multi-step.md)sayfasına bakın.

Tek adımlı derlemeler için bkz. [ACR görevlerine genel bakış](container-registry-tasks-overview.md).



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
