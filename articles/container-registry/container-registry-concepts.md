---
title: Azure Container Registry içindeki depolar ve görüntüler hakkında
description: Azure Container kayıt defterleri, depolar ve kapsayıcı görüntülerinin temel kavramlarına giriş.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 09/10/2019
ms.author: danlep
ms.openlocfilehash: 9a3d4a7d9c3fd4a0465d4e780024559a71372d9d
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300199"
---
# <a name="about-registries-repositories-and-images"></a>Kayıt defterleri, depolar ve görüntüler hakkında

Bu makalede, kapsayıcı kayıt defterleri, depolar ve kapsayıcı görüntülerinin ve ilgili yapıtların temel kavramları tanıtılmaktadır. 

## <a name="registry"></a>Kayıt defteri

Kapsayıcı *kayıt defteri* , kapsayıcı görüntülerini depolayan ve dağıtan bir hizmettir. Docker Hub, açık kaynak topluluğu destekleyen ve görüntülerin genel kataloğu olarak hizmet veren ortak bir kapsayıcı kayıt defteridir. Azure Container Registry, kullanıcılara, tümleşik kimlik doğrulama, [coğrafi çoğaltma](container-registry-geo-replication.md) desteği sunan, ağ kapatma dağıtımları, [sanal ağ ve güvenlik duvarı için genel dağıtım ve güvenilirliği destekleyen bir şekilde kendi görüntülerinin doğrudan denetimini sağlar yapılandırma](container-registry-vnet.md), [etiket kilitleme](container-registry-image-lock.md)ve diğer birçok gelişmiş özellik. 

Docker kapsayıcı görüntülerine ek olarak Azure Container Registry, açık kapsayıcı girişimi (OCı) görüntü biçimleri dahil ilgili [içerik yapılarını](container-registry-image-formats.md) destekler.

## <a name="content-addressable-elements-of-an-artifact"></a>Yapıtın içerik adreslenebilir öğeleri

Bir Azure Container Registry 'deki yapıt adresi aşağıdaki öğeleri içerir. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** -kayıt defteri konağının tam adı. Azure Container Registry içindeki kayıt defteri Konağı *myregistry*. azurecr.io biçimindedir (tümü küçük harfle). Bir Azure Container Registry 'ye yapıt çekmek veya göndermek için Docker veya diğer istemci araçlarını kullanırken loginUrl 'sini belirtmeniz gerekir. 
* **ad alanı** eğik çizgili-ilişkili görüntülerin veya yapıtların mantıksal gruplandırması-Örneğin, bir çalışma grubu veya uygulama için
* **yapıt** -belirli bir görüntü veya yapıt için bir deponun adı
* **etiket** -bir depoda depolanan bir görüntünün veya yapıtın belirli bir sürümü


Örneğin, bir Azure Container Registry 'deki bir görüntünün tam adı şöyle görünebilir:

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

Bu öğeler hakkındaki ayrıntılar için aşağıdaki bölümlere bakın.

## <a name="repository-name"></a>Depo adı

Kapsayıcı kayıt defterleri, *depoları*, kapsayıcı görüntülerinin koleksiyonlarını veya aynı ada sahip diğer yapıtları, ancak farklı etiketleri yönetir. Örneğin, aşağıdaki üç görüntü "ACR-HelloWorld" deposunda bulunur:

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Depo adlarında [ad alanları](container-registry-best-practices.md#repository-namespaces)da bulunabilir. Ad alanları, eğik çizgi ile ayrılmış depo adlarını kullanarak resimleri gruplandırmalarınıza izin verir, örneğin:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>Image

Bir kayıt defteri içindeki bir kapsayıcı görüntüsü veya diğer yapıtlar, bir veya daha fazla katmana sahiptir ve bir bildirim tarafından tanımlanır. Bu bileşenlerin birbirleriyle ilişkisini anlamak, kayıt defterinizi etkili bir şekilde yönetmenize yardımcı olabilir.

### <a name="tag"></a>Etiket

Bir görüntünün veya diğer yapıtın *etiketi* , sürümünü belirtir. Bir depodaki tek bir yapıya bir veya daha fazla etiket atanabilir ve ayrıca "etiketsiz" olabilir. Diğer bir deyişle, görüntünün verileri (katmanları) kayıt defterinde kalacağından, bir görüntüdeki tüm etiketleri silebilirsiniz.

Depo (veya depo ve ad alanı) ve bir etiket bir görüntünün adını tanımlar. Anında iletme veya çekme işleminde adını belirterek bir görüntüyü gönderebilir ve çekebilirsiniz.

Kapsayıcı görüntülerini etiketleyerek, bunları geliştirmek veya dağıtmak için senaryolarınız tarafından nasıl kılavuzluk edilir. Örneğin, kararlı Etiketler temel görüntülerinizi sürdürmek için, görüntü dağıtmak için de benzersiz Etiketler önerilir. Daha fazla bilgi için bkz. [kapsayıcı görüntülerini etiketleme ve sürüm oluşturma önerileri](container-registry-image-tag-version.md).

### <a name="layer"></a>Katman

Kapsayıcı görüntüleri, her biri görüntüyü tanımlayan Dockerfile dosyasında bir satıra karşılık gelen bir veya daha fazla *katmandan*oluşur. Kayıt defterindeki görüntüler ortak katmanları paylaşır ve depolama verimliliğini artırır. Örneğin, farklı depolardaki birçok görüntü aynı alp Linux temel katmanını paylaşabilir, ancak bu katmanın yalnızca bir kopyası kayıt defterinde saklanır.

Katman paylaşımı, katman dağıtımını aynı zamanda ortak katmanları paylaşan birden çok görüntü içeren düğümlere iyileştirir. Örneğin, zaten bir düğümde bulunan bir görüntü temel olarak alp Linux katmanını içeriyorsa, aynı katmana başvuran farklı bir görüntünün sonraki çekme katmanı düğüme aktarılmaz. Bunun yerine, düğüm üzerinde zaten var olan katmana başvurur.

Olası katman işlemesini güvenli yalıtım ve koruma sağlamak için Katmanlar kayıt defterleri arasında paylaşılmaz.

### <a name="manifest"></a>Bildirimi

Bir kapsayıcı kayıt defterine gönderilen her kapsayıcı görüntüsü veya yapıtı bir *bildirimle*ilişkilendirilir. Görüntü gönderildiğinde kayıt defteri tarafından oluşturulan bildirim, görüntüyü benzersiz bir şekilde tanımlar ve katmanlarını belirtir. Azure CLı komutu ile bir depo için bildirimleri listeleyebilir [az ACR Repository Show-bildirimleri][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Örneğin, "ACR-HelloWorld" deposu için bildirimleri listeleyin:

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
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Bildirim Özeti

Bildirimler benzersiz bir SHA-256 karması veya *bildirim Özeti*tarafından tanımlanır. Her resim veya yapıt--etiketlenebilir veya değil, Özeti tarafından tanımlanır. Görüntünün katman verileri başka bir görüntüyle aynı olsa bile Özet değeri benzersizdir. Bu mekanizma, aynı etiketli görüntüleri bir kayıt defterine sürekli olarak göndermenize olanak tanır. Örneğin, her görüntü benzersiz Özet tarafından `myimage:latest` tanımlandığından, hata vermeden Kayıt defterinize sürekli olarak gönderim yapabilirsiniz.

Çekme işleminde özetini belirterek bir kayıt defterinden görüntü çekebilirsiniz. Bazı sistemler, aynı etiketli bir görüntü daha sonra kayıt defterine gönderilse bile, çekilmekte olan görüntü sürümünü garanti ettiğinden Özet tarafından çekilecek şekilde yapılandırılabilir.

Örneğin, bildirim özetine göre "ACR-HelloWorld" deposundan bir görüntü çekin:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Aynı etiketlere sahip değiştirilmiş görüntüleri sürekli olarak gönderseniz, yalnız bırakılmış görüntüler oluşturabilirsiniz--etiketsiz, ancak hala kayıt defterinizde alan tüketiyor olabilirsiniz. Resimleri etikete göre listelediğinizde veya görüntülediğinizde etiketlenmemiş görüntüler Azure CLı 'de veya Azure portal gösterilmez. Ancak, katmanları hala mevcut ve kayıt defterinizde alan tüketir. Etiketlenmemiş bir görüntünün silinmesi, bildirim tek bir veya sonuncu olduğunda, belirli bir katmana işaret edildiğinde kayıt defteri alanını serbest bırakır. Etiketlenmemiş görüntüler tarafından kullanılan boşluğu boşaltma hakkında daha fazla bilgi için bkz. [Azure Container Registry kapsayıcı görüntülerini silme](container-registry-delete.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure Container Registry ' de [görüntü depolama](container-registry-storage.md) ve [desteklenen içerik biçimleri](container-registry-image-formats.md) hakkında daha fazla bilgi edinin.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


