---
title: Kayıt defterinizde çok mimarili görüntüler
description: Multi-Architecture (çok katmanlı) görüntüler oluşturmak, içeri aktarmak, depolamak ve dağıtmak için Azure Container Registry 'nizi kullanın
ms.topic: article
ms.date: 02/07/2021
ms.custom: ''
ms.openlocfilehash: 520518a275e0a9d191ae770e560a0e6e8278eae2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802463"
---
# <a name="multi-architecture-images-in-your-azure-container-registry"></a>Azure Container kayıt defterinizde çok mimarili görüntüler

Bu makalede, *Çoklu mimari* (*çok* katmanlı) görüntüler ve bunları oluşturma, depolama ve kullanma konusunda yardımcı olmak için Azure Container Registry özellikleri nasıl kullanabileceğiniz açıklanır. 

Çok katmanlı bir görüntü, farklı mimarilere ve bazen farklı işletim sistemlerine yönelik türevlerini birleştirebileceğiniz bir kapsayıcı görüntüsü türüdür. Çoklu mimari desteğiyle bir görüntü çalıştırırken kapsayıcı istemcileri, işletim sistemi ve mimarinizle eşleşen bir görüntü varyantı otomatik olarak seçer.

## <a name="manifests-and-manifest-lists"></a>Bildirimler ve bildirim listeleri

Çoklu mimari görüntüler, görüntü bildirimlerini ve bildirim listelerini temel alır.

### <a name="manifest"></a>Bildirim

Her kapsayıcı görüntüsü bir [bildirimle](container-registry-concepts.md#manifest)temsil edilir. Bildirim, görüntüsünü ve bunlara karşılık gelen boyutlarına başvurarak görüntüyü benzersiz bir şekilde tanımlayan bir JSON dosyasıdır. 

Linux görüntüsü için temel bir bildirim aşağıdakine `hello-world` benzer şekilde görünür:

  ```json
  {
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "config": {
        "mediaType": "application/vnd.docker.container.image.v1+json",
        "size": 1510,
        "digest": "sha256:fbf289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e"
      },
    "layers": [
        {
          "mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
          "size": 977,
          "digest": "sha256:2c930d010525941c1d56ec53b97bd057a67ae1865eebf042686d2a2d18271ced"
        }
      ]
  }
  ```
    
Azure CLı 'de [az ACR Repository Show-bildirimleri](/cli/azure/acr/repository#az_acr_repository_show_manifests) komutu gibi Azure Portal veya araçları kullanarak Azure Container Registry bildirimi görüntüleyebilirsiniz.

### <a name="manifest-list"></a>Bildirim listesi

Çok katmanlı bir görüntü için *bildirim listesi* (OCI görüntüleri için [görüntü dizini](https://github.com/opencontainers/image-spec/blob/master/image-index.md) olarak daha yaygın olarak bilinir), görüntülerin bir koleksiyonu (dizini) ve bir veya daha fazla görüntü adı belirterek bir tane oluşturabilirsiniz. Desteklenen işletim sistemi ve mimari, boyut ve bildirim Özeti gibi her bir görüntü hakkındaki ayrıntıları içerir. Bildirim listesi, ve komutlarında bir görüntü adı ile aynı şekilde kullanılabilir `docker pull` `docker run` . 

`docker`CLI, [Docker manifest](https://docs.docker.com/engine/reference/commandline/manifest/) komutunu kullanarak bildirimleri ve bildirim listelerini yönetir.

> [!NOTE]
> Şu anda, `docker manifest` komutu ve alt komutları deneysel. Deneysel komutları kullanma hakkında ayrıntılı bilgi için bkz. Docker belgeleri.

Komutunu kullanarak bir bildirim listesini görüntüleyebilirsiniz `docker manifest inspect` . Aşağıda, üç bildirimi olan çok katmanlı görüntünün çıktısı verilmiştir `mcr.microsoft.com/mcr/hello-world:latest` : Linux işletim sistemi mimarileri için iki, diğeri de Windows mimarisi içindir.
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.docker.distribution.manifest.list.v2+json",
  "manifests": [
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 524,
      "digest": "sha256:83c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 525,
      "digest": "sha256:873612c5503f3f1674f315c67089dee577d8cc6afc18565e0b4183ae355fb343",
      "platform": {
        "architecture": "arm64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 1124,
      "digest": "sha256:b791ad98d505abb8c9618868fc43c74aa94d08f1d7afe37d19647c0030905cae",
      "platform": {
        "architecture": "amd64",
        "os": "windows",
        "os.version": "10.0.17763.1697"
      }
    }
  ]
}
```

Azure Container Registry ' de bir çok katmanlı bildirim listesi depolanıyorsa, Azure portal veya [az ACR Repository Show-bildirimleri](/cli/azure/acr/repository#az_acr_repository_how_manifests) komutu gibi araçlarla bildirim listesini de görüntüleyebilirsiniz.

## <a name="import-a-multi-arch-image"></a>Çoklu mimari görüntüyü içeri aktarma 

Mevcut bir çok katmanlı görüntü, [az ACR Import](/cli/azure/acr#az_acr_import) komutunu kullanarak bir Azure Container Registry 'ye aktarılabilir. Görüntü içeri aktarma söz dizimi, tek mimarili bir görüntüyle aynıdır. Tek mimarili bir görüntünün içeri aktarılması gibi, çok katmanlı bir görüntünün içe aktarılması Docker komutlarını kullanmaz. 

Ayrıntılar için bkz. kapsayıcı [görüntülerini kapsayıcı kayıt defterine aktarma](container-registry-import-images.md).

## <a name="push-a-multi-arch-image"></a>Çok katmanlı bir görüntü gönderin

Farklı mimarilere yönelik kapsayıcı görüntüleri oluşturmak için derleme iş akışları varsa, Azure Container Registry 'nize çok katmanlı bir görüntü göndermek için aşağıdaki adımları izleyin.

1. Her mimariye özgü görüntüyü kapsayıcı Kayıt defterinize etiketleyin ve gönderin. Aşağıdaki örnekte iki Linux mimarisi varsayılır: arm64 ve AMD64. 

   ```console
   docker tag myimage:arm64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:arm64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:arm64
 
   docker tag myimage:amd64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:amd64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ``` 

1. `docker manifest create`Önceki görüntüleri çoklu mimari görüntüde birleştirmek için bir bildirim listesi oluşturmak üzere öğesini çalıştırın.

   ```console
   docker manifest create myregistry.azurecr.io/multi-arch-samples/myimage:multi \
    myregistry.azurecr.io/multi-arch-samples/myimage:arm64 \
    myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ```

1. Aşağıdakileri kullanarak bildirimi kapsayıcı Kayıt defterinize gönderin `docker manifest push` :

   ```console
   docker manifest push myregistry.azurecr.io/multi-arch-samples/myimage:multi
   ```

1. `docker manifest inspect`Bildirim listesini görüntülemek için komutunu kullanın. Bir komut çıktısı örneği, önceki bölümde gösterilmiştir.

Multi-Arch bildirimini Kayıt defterinize gönderdikten sonra, çok katmanlı görüntüyle, tek mimari görüntüyle yaptığınız şekilde çalışın. Örneğin, görüntüsünü kullanarak çekin `docker pull` , etiketleri, bildirimleri ve görüntünün diğer özelliklerini görüntülemek için [az ACR Repository](/cli/azure/acr/repository#az_acr_repository) komutları kullanın.

## <a name="build-and-push-a-multi-arch-image"></a>Çoklu mimari görüntü oluşturma ve gönderme

[ACR görevlerinin](container-registry-tasks-overview.md)özelliklerini kullanarak, Azure Container Registry 'nize çok katmanlı bir görüntü oluşturup gönderebilirsiniz. Örneğin, [çok adımlı bir görevi](container-registry-tasks-multi-step.md) , bir Linux çoklu mimari görüntüsü oluşturan bir YAML dosyasında tanımlayın.

Aşağıdaki örnek, arm64 ve AMD64 olmak üzere iki mimaride ayrı Dockerfiles kullandığınızı varsayar. Mimariye özgü görüntüleri oluşturup iter, ardından etiketine sahip bir çok katmanlı bildirim oluşturur ve gönderir `latest` :

```yml
version: v1.1.0

steps:
- build: -t {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64 -f dockerfile.arm64 . 
- build: -t {{.Run.Registry}}/multi-arch-samples/myyimage:{{.Run.ID}}-arm64 -f dockerfile.amd64 . 
- push: 
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: >
    docker manifest create
    {{.Run.Registry}}/multi-arch-samples/myimage:latest
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: docker manifest push --purge {{.Run.Registry}}/multi-arch-samples/myimage:latest
- cmd: docker manifest inspect {{.Run.Registry}}/multi-arch-samples/myimage:latest
```

## <a name="next-steps"></a>Sonraki adımlar

* Farklı mimarilerin kapsayıcı görüntülerini derlemek için [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) kullanın.
* Deneysel Docker [buildx](https://docs.docker.com/buildx/working-with-buildx/) eklentisini kullanarak çok platformlu görüntüler oluşturma hakkında bilgi edinin.

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
