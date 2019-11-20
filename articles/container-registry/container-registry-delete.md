---
title: Azure Container Registry resim kaynaklarını silme
description: Azure CLı komutları kullanılarak kapsayıcı görüntüsü verilerini silerek kayıt defteri boyutunu etkin bir şekilde yönetme hakkında ayrıntılar.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/31/2019
ms.author: danlep
ms.openlocfilehash: d415bef80ed8c96ff6e5df81ae9281ae681a4879
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300179"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Azure CLı kullanarak Azure Container Registry kapsayıcı görüntülerini silme

Azure Container Registry 'nizin boyutunu korumak için eski görüntü verilerini düzenli aralıklarla silmelisiniz. Üretime dağıtılan bazı kapsayıcı görüntüleri, daha uzun süreli depolama gerektirebilir, ancak diğerleri genellikle daha hızlı silinebilir. Örneğin, bir otomatik derleme ve test senaryosunda, kayıt defteriniz hiçbir şekilde dağıtılmayan görüntülerle hızla doldurabilir ve derleme ve test geçişini tamamladıktan sonra kısa bir süre sonra temizlenir.

Görüntü verilerini birçok farklı şekilde silebildiğinden, her silme işleminin depolama kullanımını nasıl etkilediğini anlamak önemlidir. Bu makalede, görüntü verilerini silmeye yönelik çeşitli yöntemler ele alınmaktadır:

* [Depoyu](#delete-repository)silme: Tüm görüntüleri ve depodaki tüm benzersiz katmanları siler.
* [Etikete](#delete-by-tag)göre Sil: Görüntüyü, etiketi, görüntünün başvurduğu tüm benzersiz katmanları ve görüntüyle ilişkili diğer tüm etiketleri siler.
* [Bildirim özetine](#delete-by-manifest-digest)göre Sil: Görüntü tarafından başvurulan tüm benzersiz katmanları ve görüntüyle ilişkili tüm etiketleri siler.

Örnek betikler, silme işlemlerinin otomatikleştirilmesine yardımcı olmak için sağlanır.

Bu kavramlara giriş için bkz. [kayıt defterleri, depolar ve görüntüler hakkında](container-registry-concepts.md).

## <a name="delete-repository"></a>Depoyu Sil

Bir deponun silinmesi, tüm Etiketler, benzersiz katmanlar ve bildirimler dahil olmak üzere depodaki tüm görüntüleri siler. Bir depoyu sildiğinizde, bu depodaki benzersiz katmanlara başvuran görüntüler tarafından kullanılan depolama alanını kurtarmanız gerekir.

Aşağıdaki Azure CLı komutu, "ACR-HelloWorld" deposunu ve depo içindeki tüm etiketleri ve bildirimleri siler. Silinen bildirimler tarafından başvurulan katmanlara kayıt defterindeki diğer görüntülerde başvurulmuyorsa, depolama alanını kurtararak bunların katman verileri de silinir.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Etikete göre Sil

Silme işleminde depo adını ve etiketini belirterek, bir depodan tek tek görüntüleri silebilirsiniz. Etiketine göre sildiğinizde, görüntüdeki tüm benzersiz katmanlar tarafından kullanılan depolama alanını kurtarmanız gerekir (Bu, kayıt defterindeki diğer görüntüler tarafından paylaşılmayan Katmanlar).

Etikete göre silmek için [az ACR Repository Delete][az-acr-repository-delete] kullanın ve `--image` parametre içinde görüntü adını belirtin. Görüntüye özgü tüm katmanlar ve görüntüyle ilişkili diğer Etiketler silinir.

Örneğin, "ACR-HelloWorld: latest" görüntüsünü "myregistry" kayıt defterinden silme:

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> Etikete *göre* silme bir etiketi (etiketlemesini kaldırma) ile karıştırılmamalıdır. Azure CLı komutu ile bir etiketi silmek için [az ACR Repository untag][az-acr-repository-untag]. Bir görüntünün etiketini kaldırdığınızda hiçbir boşluk serbest bırakılmaz çünkü [bildirim](container-registry-concepts.md#manifest) ve katman verileri kayıt defterinde kalır. Yalnızca etiket başvurusunun kendisi silinir.

## <a name="delete-by-manifest-digest"></a>Bildirim özetine göre Sil

Bir [bildirim Özeti](container-registry-concepts.md#manifest-digest) bir, None veya birden çok etiketle ilişkilendirilebilir. Özet ile sildiğinizde, bildirim tarafından başvurulan tüm Etiketler, görüntüye özgü tüm katmanlarda katman verileri olduğu gibi silinir. Paylaşılan katman verileri silinmedi.

Özet olarak silmek için, önce silmek istediğiniz görüntüleri içeren deponun bildirim bildirimlerini listeleyin. Örneğin:

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  }
]
```

Ardından, [az ACR Repository Delete][az-acr-repository-delete] komutunda silmek istediğiniz Özeti belirtin. Komut biçimi şu şekildedir:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Örneğin, önceki çıktıda listelenen son bildirimi silmek için ("v2" etiketiyle birlikte):

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

`acr-helloworld:v2` Görüntü, söz konusu görüntüye özgü herhangi bir katman verisi olduğundan, kayıt defterinden silinir. Bir bildirim birden çok etiketle ilişkiliyse, ilişkili tüm Etiketler de silinir.

## <a name="delete-digests-by-timestamp"></a>Zaman damgasına göre özetleri 'yi Sil

Bir deponun veya kayıt defterinin boyutunu korumak için belirli bir tarihten daha eski bildirim türlerini düzenli olarak silmeniz gerekebilir.

Aşağıdaki Azure CLı komutu, belirli bir zaman damgasından daha eski bir depodaki tüm bildirim özetini artan düzende listeler. `<acrName>` Ve`<repositoryName>` değerlerini ortamınız için uygun değerlerle değiştirin. Bu örnekte olduğu gibi, zaman damgası tam bir tarih-saat ifadesi veya tarih olabilir.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Eski bildirim dallarını tanımladıktan sonra, belirtilen zaman damgasından daha eski bildirim bildirimlerini silmek için aşağıdaki Bash betiğini çalıştırabilirsiniz. Azure CLı ve **xargs**gerektirir. Varsayılan olarak, komut dosyası silme işlemini gerçekleştirir. Görüntüyü silme işlemini etkinleştirmek `true` için değeriniolarakdeğiştirin.`ENABLE_DELETE`

> [!WARNING]
> Aşağıdaki örnek betiği dikkatle kullanın--silinen görüntü verileri KURTARıLAMAZ. Bildirim özetine (görüntü adından farklı olarak) görüntüleri çeken sistemleriniz varsa, bu betikleri çalıştırmamalıdır. Bildirim özetleri silindiğinde, bu sistemlerin Kayıt defterinizden görüntüleri çekmesini engeller. Bildirime göre çekmek yerine, [Önerilen en iyi yöntem](container-registry-image-tag-version.md)olan *benzersiz etiketleme* düzenini benimsede düşünün. 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>Etiketlenmemiş görüntüleri Sil

[Bildirim Özeti](container-registry-concepts.md#manifest-digest) bölümünde belirtildiği gibi, var olan bir etiketi kullanarak değiştirilmiş bir görüntüyü, daha önce gönderilen görüntünün etiketlerini, bir tek kalan (veya "Dangling") görüntüsüne neden olacak şekilde **çıkarır** . Daha önce gönderilen görüntünün bildirimi ve katman verileri--kayıt defterinde kalır. Aşağıdaki olay sırasını göz önünde bulundurun:

1. İlet resmi *ACR-HelloWorld* WITH Tag **latest**:`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Depo *ACR-HelloWorld*için bildirimleri denetle:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. *ACR-HelloWorld* dockerfile 'ı değiştirme
1. İlet resmi *ACR-HelloWorld* WITH Tag **latest**:`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Depo *ACR-HelloWorld*için bildirimleri denetle:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

Sıradaki son adımın çıktısında görebileceğiniz gibi, artık `"tags"` özelliği boş bir liste olan bir yalnız bırakılmış bildirim vardır. Bu bildirim, başvurduğu benzersiz katman verileriyle birlikte kayıt defteri içinde de bulunur. **Bu tür yalnız bırakılmış görüntüleri ve bunların katman verilerini silmek için bildirim özetine göre silmeniz gerekir**.

## <a name="delete-all-untagged-images"></a>Etiketlenmemiş tüm görüntüleri Sil

Aşağıdaki Azure CLı komutunu kullanarak, Deponuzdaki tüm etiketlenmemiş görüntüleri listeleyebilirsiniz. `<acrName>` Ve`<repositoryName>` değerlerini ortamınız için uygun değerlerle değiştirin.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Bu komutu bir betikte kullanarak, bir depodaki tüm etiketlenmemiş görüntüleri silebilirsiniz.

> [!WARNING]
> Aşağıdaki örnek komut dosyalarını dikkatli bir şekilde kullanın--silinen görüntü verileri KURTARıLAMAZ. Bildirim özetine (görüntü adından farklı olarak) görüntüleri çeken sistemleriniz varsa, bu betikleri çalıştırmamalıdır. Etiketlenmemiş görüntüleri silmek, bu sistemlerin Kayıt defterinizden görüntüleri çekmesini engeller. Bildirime göre çekmek yerine, [Önerilen en iyi yöntem](container-registry-image-tag-version.md)olan *benzersiz etiketleme* düzenini benimsede düşünün.

**Bash 'de Azure CLı**

Aşağıdaki Bash betiği, bir depodaki tüm etiketlenmemiş görüntüleri siler. Azure CLı ve **xargs**gerektirir. Varsayılan olarak, komut dosyası silme işlemini gerçekleştirir. Görüntüyü silme işlemini etkinleştirmek `true` için değeriniolarakdeğiştirin.`ENABLE_DELETE`

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**PowerShell 'de Azure CLı**

Aşağıdaki PowerShell betiği, bir depodaki tüm etiketlenmemiş görüntüleri siler. PowerShell ve Azure CLı gerektirir. Varsayılan olarak, komut dosyası silme işlemini gerçekleştirir. Görüntüyü silme işlemini etkinleştirmek `$TRUE` için değeriniolarakdeğiştirin.`$enableDelete`

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```


## <a name="automatically-purge-tags-and-manifests-preview"></a>Etiketleri ve bildirimleri otomatik olarak temizleme (önizleme)

Azure CLı komutlarını komut dosyası oluşturmaya alternatif olarak, belirli bir süreden daha eski olan veya belirtilen bir ad filtresiyle eşleşen tüm etiketleri silmek için isteğe bağlı veya zamanlanmış bir ACR görevi çalıştırın. Daha fazla bilgi için bkz. [Azure Container Registry 'den görüntüleri otomatik olarak temizleme](container-registry-auto-purge.md).

Etiketlenmemiş bildirimleri yönetmek için isteğe bağlı olarak her kayıt defteri için bir [bekletme ilkesi](container-registry-retention-policy.md) ayarlayın. Bir bekletme ilkesini etkinleştirdiğinizde, ilişkili etiketi olmayan kayıt defterindeki görüntü bildirimleri ve temel alınan katman verileri bir ayarlanan dönemden sonra otomatik olarak silinir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Container Registry görüntü depolama hakkında daha fazla bilgi için bkz. [Azure Container Registry Içindeki kapsayıcı görüntü depolaması](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
