---
title: YAML başvurusu-ACR görevleri
description: Görev özellikleri, adım türleri, adım özellikleri ve yerleşik değişkenler de dahil olmak üzere ACR görevleri için YAML görevlerini tanımlamaya yönelik başvuru.
ms.topic: article
ms.date: 07/08/2020
ms.openlocfilehash: 126fcbce0569b2be6d9302cbbb718fa11e3e8046
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780956"
---
# <a name="acr-tasks-reference-yaml"></a>ACR görevleri başvurusu: YAML

ACR görevlerinde çok adımlı görev tanımı, kapsayıcıları oluşturmaya, teste ve düzeltme eki uygulamaya odaklanan kapsayıcı merkezli bir işlem temel alanı sağlar. Bu makalede, çok adımlı görevlerinizi tanımlayan YAML dosyalarının komutları, parametreleri, özellikleri ve söz dizimi ele alınmaktadır.

Bu makale, ACR görevleri için çok adımlı görev YAML dosyaları oluşturmak için başvuru içerir. ACR görevlerine giriş istiyorsanız [ACR görevlerine genel bakış ' a](container-registry-tasks-overview.md)bakın.

## <a name="acr-taskyaml-file-format"></a>ACR-Task. YAML dosya biçimi

ACR görevleri standart YAML sözdiziminde çok adımlı görev bildirimini destekler. Bir YAML dosyasında bir görevin adımlarını tanımlarsınız. Daha sonra dosyayı [az ACR Run][az-acr-run] komutuna geçirerek görevi el ile çalıştırabilirsiniz. Ya da bir git işlemesinde, bir temel görüntü güncelleştirmesinde veya bir zamanlamaya göre otomatik olarak tetiklenen [az ACR görev oluşturmaya][az-acr-task-create] sahip bir görev oluşturmak için dosyasını kullanın. Bu makale, `acr-task.yaml` adımları içeren dosya olarak ifade edilse de, ACR görevleri [desteklenen bir uzantıya](#supported-task-filename-extensions)sahip tüm geçerli dosya adını destekler.

En üst düzey `acr-task.yaml` temel elemanlar **görev özellikleri**, **adım türleri** ve **adım özelliklerdir**:

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

`acr-task.yaml`Bazı yaygın adım özellikleri de dahil olmak üzere bir dosyanın temel biçimi aşağıdaki gibidir. Tüm kullanılabilir adım özelliklerinin veya adım türü kullanımının ayrıntılı bir gösterimi olmasa da temel dosya biçimine hızlı bir genel bakış sağlar.

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

ACR görevleri, `.yaml` bir görev dosyası olarak işlem yaptığı gibi birkaç dosya adı uzantısını ayırmıştır. Aşağıdaki listede *olmayan* herhangi bir uzantı, ACR görevlerinin bir Dockerfile olarak değerlendirilir:. YAML,. yml,. TOML,. JSON,. sh,. Bash,. ZSH,. ps1,. PS,. cmd,. bat,. TS,. js,. php,.,. RB,. lua

YAML, şu anda ACR görevleri tarafından desteklenen tek dosya biçimidir. Diğer dosya adı uzantıları olası gelecekteki destek için ayrılmıştır.

## <a name="run-the-sample-tasks"></a>Örnek görevleri çalıştırma

Bu makalenin aşağıdaki bölümlerinde başvurulan birkaç örnek görev dosyası vardır. Örnek görevler ortak bir GitHub deposunda, [Azure-Samples/ACR-Tasks][acr-tasks]' dadır. Bunları, [az ACR Run][az-acr-run]Azure CLI komutuyla çalıştırabilirsiniz. Örnek komutlar şuna benzerdir:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Örnek komutların biçimlendirmesi, Azure CLı 'de varsayılan bir kayıt defteri yapılandırdığınız ve bu nedenle parametreyi attıkları varsayılır `--registry` . Varsayılan bir kayıt defteri yapılandırmak için, bir değeri kabul eden parametresiyle [az configure][az-configure] komutunu kullanın `--defaults` `acr=REGISTRY_NAME` .

Örneğin, Azure CLı 'yı "myregistry" adlı varsayılan bir kayıt defteriyle yapılandırmak için:

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Görev özellikleri

Görev özellikleri genellikle bir dosyanın en üstünde görünür `acr-task.yaml` ve görev adımlarının tam yürütülmesi boyunca uygulanan genel özelliklerdir. Bu genel özelliklerden bazıları, tek bir adım içinde geçersiz kılınabilir.

| Özellik | Tür | İsteğe Bağlı | Description | Geçersiz kılma destekleniyor | Varsayılan değer |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | Yes | `acr-task.yaml`ACR görevler hizmeti tarafından ayrıştırılabilen dosyanın sürümü. ACR görevleri geriye dönük uyumluluğu sürdürmeye devam ederken, bu değer ACR görevlerinin tanımlı bir sürüm içinde uyumluluğu korumasına olanak tanır. Belirtilmemişse, varsayılan olarak en son sürümü alır. | No | Yok |
| `stepTimeout` | int (saniye) | Yes | Bir adımın çalıştırılacağı en fazla saniye sayısı. Özellik bir görevde belirtilmişse, `timeout` tüm adımların varsayılan özelliğini ayarlar. `timeout`Özellik bir adımda belirtilmişse, görev tarafından sunulan özelliği geçersiz kılar. | Yes | 600 (10 dakika) |
| `workingDirectory` | string | Yes | Çalışma zamanı sırasında kapsayıcının çalışma dizini. Özellik bir görevde belirtilmişse, `workingDirectory` tüm adımların varsayılan özelliğini ayarlar. Bir adımda belirtilmişse, görev tarafından sunulan özelliği geçersiz kılar. | Yes | `c:\workspace` Windows veya `/workspace` Linux 'ta |
| `env` | [dize, dize,...] | Yes |  `key=value`Görevin ortam değişkenlerini tanımlayan biçimdeki dizeler dizisi. Özellik bir görevde belirtilmişse, `env` tüm adımların varsayılan özelliğini ayarlar. Bir adımda belirtilmişse, görevden devralınan tüm ortam değişkenlerini geçersiz kılar. | Yes | Yok |
| `secrets` | [gizli, gizli,...] | Yes | [Gizli](#secret) nesneler dizisi. | No | Yok |
| `networks` | [Ağ, ağ,...] | Yes | [Ağ](#network) nesneleri dizisi. | No | Yok |
| `volumes` | [Birim, birim,...] | Yes | [Birim](#volume) nesnelerinin dizisi. Bir adıma bağlanacak kaynak içeriğe sahip birimleri belirtir. | No | Yok |

### <a name="secret"></a>gizli dizi

Gizli nesne aşağıdaki özelliklere sahiptir.

| Özellik | Tür | İsteğe Bağlı | Description | Varsayılan değer |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | dize | No | Gizli dizi tanımlayıcısı. | Yok |
| `keyvault` | string | Yes | Azure Key Vault gizli URL 'SI. | Yok |
| `clientID` | string | Yes | Azure kaynakları için [Kullanıcı tarafından atanan yönetilen kimliğin](container-registry-tasks-authentication-managed-identity.md) istemci kimliği. | Yok |

### <a name="network"></a>network

Ağ nesnesi aşağıdaki özelliklere sahiptir.

| Özellik | Tür | İsteğe Bağlı | Description | Varsayılan değer |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | dize | No | Ağın adı. | Yok |
| `driver` | string | Yes | Ağı yönetmek için sürücü. | Yok |
| `ipv6` | bool | Yes | IPv6 ağ oluşturma özelliğinin etkin olup olmadığı. | `false` |
| `skipCreation` | bool | Yes | Ağ oluşturma atlanıp atlanmayacağı. | `false` |
| `isDefault` | bool | Yes | Ağın Azure Container Registry ile belirtilen varsayılan bir ağ olup olmadığı. | `false` |

### <a name="volume"></a>Ses düzeyi

Birim nesnesi aşağıdaki özelliklere sahiptir.

| Özellik | Tür | İsteğe Bağlı | Description | Varsayılan değer |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | dize | No | Takılacak birimin adı. Yalnızca alfasayısal karakterler, '-' ve ' _ ' içerebilir. | Yok |
| `secret` | Map [dize] dizesi | No | Haritanın her anahtarı oluşturulan ve birimde doldurulmuş bir dosyanın adıdır. Her değer, gizli dizinin dize sürümüdür. Gizli değerlerin Base64 kodlamalı olması gerekir. | Yok |

## <a name="task-step-types"></a>Görev adımı türleri

ACR görevleri üç adım türünü destekler. Her adım türü, her adım türü için bölümünde ayrıntılı olarak açıklanan birkaç özelliği destekler.

| Adım türü | Description |
| --------- | ----------- |
| [`build`](#build) | Tanıdık sözdizimi kullanarak bir kapsayıcı görüntüsü oluşturur `docker build` . |
| [`push`](#push) | `docker push`Yeni oluşturulmuş veya retagged görüntülerinin bir kapsayıcı kayıt defterine yürütülür. Azure Container Registry, diğer özel kayıt defterleri ve genel Docker Hub desteklenir. |
| [`cmd`](#cmd) | Kapsayıcıyı kapsayıcının kendisine geçirilmiş parametreler ile bir komut olarak çalıştırır `[ENTRYPOINT]` . `cmd`Adım türü `env` , `detach` ve diğer tanıdık komut seçenekleri gibi parametreleri destekler `docker run` , eşzamanlı kapsayıcı yürütmesi ile birim ve işlevsel testi etkinleştirir. |

## <a name="build"></a>derleme

Kapsayıcı görüntüsü oluşturun. `build`Adım türü, `docker build` bulutta ilk sınıf temel olarak çalıştırmanın çok kiracılı, güvenli bir şekilde temsil eder.

### <a name="syntax-build"></a>Sözdizimi: derleme

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

`build`Adım türü aşağıdaki tablodaki parametreleri destekler. `build`Adım türü Ayrıca, oluşturma zamanı değişkenlerini ayarlamak için gibi [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) komutunun tüm derleme seçeneklerini de destekler `--build-arg` .

| Parametre | Açıklama | İsteğe Bağlı |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Oluşturulan görüntünün tam nitelikli olduğunu tanımlar `image:tag` .<br /><br />Görüntü, işlevsel testler gibi iç görev doğrulamaları için kullanılabilir, ancak tüm görüntülerin `push` bir kayıt defterine ihtiyacı yoktur. Ancak, bir görev yürütme içindeki bir görüntüyü örnek olarak, görüntünün başvuru için bir adı olması gerekir.<br /><br />Farklı olarak `az acr build` , ACR görevlerinin çalıştırılması varsayılan gönderme davranışı sağlamaz. ACR görevleri ile, varsayılan senaryo bir görüntüyü oluşturma, doğrulama ve gönderme özelliğini kabul eder. İsteğe bağlı olarak oluşturulan görüntüleri gönderme hakkında bilgi için bkz. [gönderim](#push) . | Yes |
| `-f` &#124; `--file` | Geçirilen Dockerfile dosyasını belirtir `docker build` . Belirtilmemişse, bağlam kökündeki varsayılan Dockerfile varsayılır. Bir Dockerfile belirtmek için, dosya adını bağlamın köküne göre geçirin. | Yes |
| `context` | Kök dizin geçildi `docker build` . Her görevin kök dizini paylaşılan bir [WorkingDirectory](#task-step-properties)olarak ayarlanır ve ilişkili git klonlanmış dizinin kökünü içerir. | No |

### <a name="properties-build"></a>Özellikler: derleme

`build`Adım türü aşağıdaki özellikleri destekler. Bu makalenin [görev adımı özellikleri](#task-step-properties) bölümünde bu özelliklerin ayrıntılarını bulun.

| Özellikler | Tür | Gerekli |
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
| `volumeMount` | object | İsteğe Bağlı |
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

## <a name="push"></a>push

Bir veya daha fazla oluşturulmuş veya retagged görüntüsünü bir kapsayıcı kayıt defterine gönderin. Azure Container Registry veya genel Docker Hub 'ına gibi özel kayıt defterlerine göndermeyi destekler.

### <a name="syntax-push"></a>Sözdizimi: Push

`push`Adım türü bir görüntü koleksiyonunu destekler. YAML koleksiyon sözdizimi, satır içi ve iç içe biçimleri destekler. Tek bir görüntünün gönderilmesi genellikle satır içi sözdizimi kullanılarak temsil edilir:

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

`push`Adım türü aşağıdaki özellikleri destekler. Bu makalenin [görev adımı özellikleri](#task-step-properties) bölümünde bu özelliklerin ayrıntılarını bulun.

| Özellik | Tür | Gerekli |
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

`cmd`Adım türü bir kapsayıcı çalıştırır.

### <a name="syntax-cmd"></a>Sözdizimi: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Özellikler: cmd

`cmd`Adım türü aşağıdaki özellikleri destekler:

| Özellik | Tür | Gerekli |
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
| `volumeMount` | object | İsteğe Bağlı |
| `when` | [dize, dize,...] | İsteğe Bağlı |
| `workingDirectory` | string | İsteğe Bağlı |

Bu özelliklerin ayrıntılarını bu makalenin [görev adımı özellikleri](#task-step-properties) bölümünde bulabilirsiniz.

### <a name="examples-cmd"></a>Örnekler: cmd

#### <a name="run-hello-world-image"></a>Merhaba-Dünya görüntüsünü Çalıştır

Bu komut `hello-world.yaml` , Docker Hub 'daki [Merhaba-Dünya](https://hub.docker.com/_/hello-world/) görüntüsüne başvuran görev dosyasını yürütür.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Bash görüntüsünü ve "Hello World" yankısını çalıştırın

Bu komut `bash-echo.yaml` , Docker Hub 'da [Bash](https://hub.docker.com/_/bash/) görüntüsüne başvuran görev dosyasını yürütür.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Belirli Bash Image etiketini Çalıştır

Belirli bir görüntü sürümünü çalıştırmak için içinde etiketini belirtin `cmd` .

Bu komut `bash-echo-3.yaml` , Docker Hub 'da [bash: 3.0](https://hub.docker.com/_/bash/) görüntüsüne başvuran görev dosyasını yürütür.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Özel görüntüleri Çalıştır

`cmd`Adım türü, standart biçimi kullanarak görüntülere başvurur `docker run` . Bir kayıt defteriyle önceden kullanıma hazır olmayan görüntülerin docker.io 'tan kaynaklandığından varsayılır. Önceki örnek eşit olarak temsil edilebilir:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Standart `docker run` görüntü başvuru kuralını kullanarak, `cmd` herhangi bir özel kayıt defterinden veya ortak Docker Hub 'ından görüntü çalıştırabilir. ACR görevinin yürütüldüğü aynı kayıt defterindeki görüntülere başvuruyorsanız, kayıt defteri kimlik bilgilerini belirtmeniz gerekmez.

* Azure Container Registry 'den bir görüntü çalıştırın. Aşağıdaki örnek, adlı bir kayıt defterinizin `myregistry` ve özel bir görüntünün olduğunu varsayar `myimage:mytag` .

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Bir çalıştırma değişkeni veya diğer adla kayıt defteri başvurusunu genelleştirin

    Kayıt defteri adınızı bir dosyada sabit kodlamak yerine `acr-task.yaml` bir [çalıştırma değişkeni](#run-variables) veya [diğer ad](#aliases)kullanarak daha taşınabilir hale getirebilirsiniz. `Run.Registry`Değişken veya `$Registry` diğer ad çalışma zamanında, görevin yürütüldüğü kayıt defterinin adına genişletilir.

    Örneğin, önceki görevi herhangi bir Azure Container Registry 'de çalışacak şekilde genelleştirmek için, görüntü adı içindeki $Registry değişkenine başvurun:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

#### <a name="access-secret-volumes"></a>Gizli birimlere erişin

`volumes`Özelliği, için birimlerin ve gizli içeriklerinin belirtilmesini `build` ve `cmd` bir görevdeki adımları sağlar. Her adımın içinde, isteğe bağlı bir `volumeMounts` özellik, bu adımdaki kapsayıcıya bağlamak üzere birimleri ve karşılık gelen kapsayıcı yollarını listeler. Gizli dizileri her bir birimin bağlama yolunda dosya olarak sağlanır.

Bir görevi yürütün ve bir adımla iki gizli dizi bağlayın: bir anahtar kasasında depolanan diğeri komut satırında belirtilen bir:

```azurecli
az acr run -f mounts-secrets.yaml --set-secret mysecret=abcdefg123456 https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/mounts-secrets.yaml -->
[!code-yml[task](~/acr-tasks/mounts-secrets.yaml)]

## <a name="task-step-properties"></a>Görev adımı özellikleri

Her adım türü, türü için uygun olan birkaç özelliği destekler. Aşağıdaki tabloda, tüm kullanılabilir adım özellikleri tanımlanmaktadır. Tüm adım türleri tüm özellikleri desteklemez. Her adım türü için bu özelliklerden hangilerinin kullanılabildiğini görmek için, [cmd](#cmd), [Build](#build)ve [Push](#push) Step Type başvuru bölümlerine bakın.

| Özellik | Tür | İsteğe Bağlı | Description | Varsayılan değer |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Yes | Çalışma sırasında kapsayıcının ayrılmayacağı. | `false` |
| `disableWorkingDirectoryOverride` | bool | Yes | Geçersiz kılma işlevselliğinin devre dışı bırakılıp başlatılmayacağını belirtir `workingDirectory` . `workingDirectory`Kapsayıcının çalışma dizini üzerinde tamamen denetim sağlamak için bunu ile birlikte kullanın. | `false` |
| `entryPoint` | string | Yes | `[ENTRYPOINT]`Bir adımın kapsayıcısını geçersiz kılar. | Yok |
| `env` | [dize, dize,...] | Yes | `key=value`Adımın ortam değişkenlerini tanımlayan biçimdeki dizeler dizisi. | Yok |
| `expose` | [dize, dize,...] | Yes | Kapsayıcıdan sunulan bağlantı noktası dizisi. |  Yok |
| [`id`](#example-id) | string | Yes | Görevi içindeki adımı benzersiz bir şekilde tanımlar. Görevdeki diğer adımlarda `id` , ile bağımlılık denetimi gibi bir adım başvuru yapılabilir `when` .<br /><br />, `id` Aynı zamanda çalışan kapsayıcının adıdır. Görevdeki diğer kapsayıcılar üzerinde çalışan süreçler, `id` DNS ana bilgisayar adı olarak veya Docker logs [ID] ile erişim için öğesine başvurabilir. | `acb_step_%d`, `%d` YAML dosyasında adımın en üst düzey 0 tabanlı dizinidir |
| `ignoreErrors` | bool | Yes | Kapsayıcının yürütülmesi sırasında bir hata oluşup oluşmadığını ne olursa olsun adımın başarıyla işaretleneceğini belirtir. | `false` |
| `isolation` | string | Yes | Kapsayıcının yalıtım düzeyi. | `default` |
| `keep` | bool | Yes | Adım kapsayıcısının yürütmeden sonra tutulup tutulmayacağını belirtir. | `false` |
| `network` | object | Yes | Kapsayıcının çalıştığı bir ağı tanımlar. | Yok |
| `ports` | [dize, dize,...] | Yes | Kapsayıcıdan konağa yayınlanan bağlantı noktaları dizisi. |  Yok |
| `pull` | bool | Yes | Herhangi bir önbelleğe alma davranışına engel olmak için yürütmeden önce kapsayıcının yapılıp yapılmayacağını belirtir. | `false` |
| `privileged` | bool | Yes | Kapsayıcının ayrıcalıklı modda çalıştırılıp çalıştırılmayacağı. | `false` |
| `repeat` | int | Yes | Kapsayıcının yürütülmesini yinelemek için yeniden deneme sayısı. | 0 |
| `retries` | int | Yes | Bir kapsayıcının yürütülmesinin başarısız olması durumunda denenecek yeniden deneme sayısı. Yeniden deneme yalnızca bir kapsayıcının çıkış kodu sıfır değilse denenir. | 0 |
| `retryDelay` | int (saniye) | Yes | Bir kapsayıcının çalışmasının yeniden denemeleri arasındaki gecikme süresi (saniye cinsinden). | 0 |
| `secret` | object | Yes | [Azure kaynakları için](container-registry-tasks-authentication-managed-identity.md)Azure Key Vault gizli dizi veya yönetilen kimlik tanımlar. | Yok |
| `startDelay` | int (saniye) | Yes | Kapsayıcının yürütülmesinin geciktirileceği saniye sayısı. | 0 |
| `timeout` | int (saniye) | Yes | Bir adımın sonlandırılmadan önce yürütebilmesi için gereken en fazla saniye sayısı. | 600 |
| [`when`](#example-when) | [dize, dize,...] | Yes | Görevin içindeki bir veya daha fazla adım için bir adımın bağımlılığını yapılandırır. | Yok |
| `user` | string | Yes | Bir kapsayıcının Kullanıcı adı veya UID 'SI | Yok |
| `workingDirectory` | string | Yes | Bir adım için çalışma dizinini ayarlar. Varsayılan olarak ACR görevleri, çalışma dizini olarak bir kök dizin oluşturur. Ancak, derlemeniz birkaç adım içeriyorsa, önceki adımlar aynı çalışma dizinini belirterek yapıtları sonraki adımlarla paylaşabilir. | `c:\workspace` Windows veya `/workspace` Linux 'ta |

### <a name="volumemount"></a>Birimbağlama

VolumeMount nesnesi aşağıdaki özelliklere sahiptir.

| Özellik | Tür | İsteğe Bağlı | Description | Varsayılan değer |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | dize | No | Takılacak birimin adı. Bir özellikten tam olarak bir adla eşleşmesi gerekir `volumes` . | Yok |
| `mountPath`   | string | hayır | Kapsayıcıya dosya bağlamaya yönelik mutlak yol.  | Yok |

### <a name="examples-task-step-properties"></a>Örnekler: görev adımı özellikleri

#### <a name="example-id"></a>Örnek: kimlik

İki görüntü oluşturun ve işlevsel test görüntüsünü örnekleyebilirsiniz. Her adım, `id` özelliğindeki görev başvurusunda bulunan diğer adımların benzersiz olarak tanımlanır `when` .

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Örnek: ne zaman

`when`Özelliği, bir adımın görev içindeki diğer adımlara bağımlılığı belirtir. İki parametre değerini destekler:

* `when: ["-"]` -Diğer adımlara bağımlılığı olmadığını gösterir. Öğesini belirten bir adım `when: ["-"]` , yürütme işlemini hemen başlatır ve eşzamanlı adım yürütülmesine olanak sağlayacaktır.
* `when: ["id1", "id2"]` -Adımın `id` "ID1" ve `id` "ID2" ile adımlara bağlı olduğunu gösterir. "ID1" ve "ID2" adımları tamamlanana kadar bu adım yürütülmez.

`when`Bir adımda belirtilmemişse, bu adım dosyadaki önceki adımın tamamlanmasına bağlıdır `acr-task.yaml` .

Ardışık adım yürütme `when` :

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

İle sıralı adım yürütme `when` :

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

ACR görevleri, yürütdiklerinde görev adımları için kullanılabilen varsayılan bir değişken kümesi içerir. Bu değişkenlere, `{{.Run.VariableName}}` `VariableName` aşağıdakilerden biri olan biçimi kullanılarak erişilebilir:

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

Değişken adları genellikle kendi kendine açıklayıcıdır. Ayrıntılar, yaygın olarak kullanılan değişkenlerin ayrıntılarını izler. YAML sürümünde `v1.1.0` , çoğu çalıştırma değişkeninin yerine kısaltılmış, önceden tanımlanmış bir [görev diğer adı](#aliases) kullanabilirsiniz. Örneğin, yerine `{{.Run.Registry}}` `$Registry` diğer adı kullanın.

### <a name="runid"></a>Run.ID

Kullanılarak oluşturulan görevlerin her çalıştırma, çalışma `az acr run` veya tetikleme tabanlı yürütmesi `az acr task create` BENZERSIZ bir kimliğe sahiptir. KIMLIK Şu anda yürütülmekte olan çalışmayı temsil eder.

Genellikle bir görüntüyü benzersiz bir şekilde etiketlemek için kullanılır:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runsharedvolume"></a>. SharedVolume Çalıştır

Tüm görev adımları tarafından erişilebilen paylaşılan bir birimin benzersiz tanımlayıcısı. Birim `c:\workspace` Windows veya Linux 'a bağlanır `/workspace` . 

### <a name="runregistry"></a>Çalıştır. kayıt defteri

Kayıt defterinin tam sunucu adı. Genellikle görevin çalıştırıldığı kayıt defterine genel olarak başvurmak için kullanılır.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Çalışma. RegistryName

Kapsayıcı kayıt defterinin adı. Genellikle, bir tam sunucu adı gerektirmeyen görev adımlarında kullanılır, örneğin, `cmd` kayıt defterlerinde Azure CLI komutlarını çalıştıran adımlar.

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

İtibariyle `v1.1.0` , ACR görevleri çalıştırıldığında görev adımları için kullanılabilen diğer adları destekler. Diğer adlar, Bash 'de desteklenen diğer adlar (komut kısayolları) kavramıyla benzerdir. 

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

Görev adımları ' nda, `$` Aşağıdaki örnekte olduğu gibi, yönergeyle bir diğer addan önce gelmeli:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Görüntü diğer adları

Aşağıdaki diğer adların her biri Microsoft Container Registry (MCR) içindeki kararlı bir görüntüye işaret eder. `cmd`Bir yönerge kullanmadan, bir görev dosyasının bölümünde bunların her birine başvurabilirsiniz.

| Diğer ad | Görüntü |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

Aşağıdaki örnek görev, çalışma kayıt defterindeki depoda 7 günden daha eski olan görüntü etiketlerini [temizlemek](container-registry-auto-purge.md) için birkaç diğer ad kullanır `samples/hello-world` :

```yml
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
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-configure]: /cli/azure/reference-index#az_configure
