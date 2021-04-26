---
title: Kayıt defterleri, depolar, görüntüler ve yapıtlar hakkında
description: Azure Container kayıt defterleri, depolar, kapsayıcı görüntüleri ve diğer yapıtların temel kavramlarına giriş.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 64ab3812b3f23a7b3a480d3530c82bd39f2d29a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784092"
---
# <a name="about-registries-repositories-and-artifacts"></a>Kayıt defterleri, depolar ve yapıtlar hakkında

Bu makalede, kapsayıcı kayıt defterleri, depolar ve kapsayıcı görüntülerinin ve ilgili yapıtların temel kavramları tanıtılmaktadır. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="Kayıt defteri, depolar ve yapıtlar":::

## <a name="registry"></a>Kayıt Defteri

Kapsayıcı *kayıt defteri* , kapsayıcı görüntülerini ve ilgili yapıları depolayan ve dağıtan bir hizmettir. Docker Hub, Docker kapsayıcı görüntülerinin genel kataloğu olarak hizmet veren bir ortak kapsayıcı kayıt defteri örneğidir. Azure Container Registry kullanıcılara, tümleşik kimlik doğrulama, [coğrafi çoğaltma](container-registry-geo-replication.md) , ağ kapatma dağıtımları, [özel bağlantı ile sanal ağ yapılandırması](container-registry-private-link.md), [etiket kilitleme](container-registry-image-lock.md)ve diğer birçok gelişmiş özelliği içeren kapsayıcı içeriklerini doğrudan denetlemeyle sağlar. 

Docker uyumlu kapsayıcı görüntülerine ek olarak, Azure Container Registry HELI grafikleri ve açık kapsayıcı girişimi (OCı) görüntü biçimleri dahil olmak üzere bir dizi [içerik yapıtları](container-registry-image-formats.md) destekler.

## <a name="repository"></a>Depo

*Depo* , aynı ada sahip ancak farklı etiketlere sahip bir kayıt defterindeki kapsayıcı görüntülerinin veya diğer yapıların koleksiyonudur. Örneğin, aşağıdaki üç resim `acr-helloworld` depoda bulunur:

- *ACR-HelloWorld: en son*
- *ACR-HelloWorld: v1*
- *ACR-HelloWorld: v2*

Depo adlarında [ad alanları](container-registry-best-practices.md#repository-namespaces)da bulunabilir. Ad alanları, eğik çizgi ile ayrılmış adları kullanarak kuruluşunuzda ilgili depoları ve yapıt sahipliğini tanımlamanızı sağlar. Ancak kayıt defteri, tüm depoları bağımsız olarak değil, bağımsız olarak yönetir. Örnek:

- *Pazarlama/campaign10-18/Web: v2*
- *Pazarlama/campaign10-18/API: v3*
- *Pazarlama/campaign10-18/email-gönderici: v2*
- *Ürün-döndürür/Web-gönderim: 20180604*
- *Ürün-döndürür/eski-tümleştirici: 20180715*

Depo adları yalnızca küçük harfli alfasayısal karakterler, nokta, tire, alt çizgi ve eğik çizgi içerebilir. 

Tüm depo adlandırma kuralları için bkz. [kapsayıcı girişim dağıtım belirtimi](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

## <a name="artifact"></a>Yapıt

Bir kayıt defteri içindeki bir kapsayıcı görüntüsü veya diğer yapıtlar, bir veya daha fazla katmana sahiptir ve bir bildirim tarafından tanımlanır. Bu bileşenlerin birbirleriyle ilişkisini anlamak, kayıt defterinizi etkili bir şekilde yönetmenize yardımcı olabilir.

### <a name="tag"></a>Etiket

Bir görüntünün veya diğer yapıtın *etiketi* , sürümünü belirtir. Bir depodaki tek bir yapıya bir veya daha fazla etiket atanabilir ve ayrıca "etiketsiz" olabilir. Diğer bir deyişle, görüntünün verileri (katmanları) kayıt defterinde kalacağından, bir görüntüdeki tüm etiketleri silebilirsiniz.

Depo (veya depo ve ad alanı) ve bir etiket bir görüntünün adını tanımlar. Anında iletme veya çekme işleminde adını belirterek bir görüntüyü gönderebilir ve çekebilirsiniz. Etiket, `latest` Docker komutlarınıza bir tane sağlamazsanız varsayılan olarak kullanılır.

Kapsayıcı görüntülerini etiketleyerek, bunları geliştirmek veya dağıtmak için senaryolarınız tarafından nasıl kılavuzluk edilir. Örneğin, kararlı Etiketler temel görüntülerinizi sürdürmek için, görüntü dağıtmak için de benzersiz Etiketler önerilir. Daha fazla bilgi için bkz. [kapsayıcı görüntülerini etiketleme ve sürüm oluşturma önerileri](container-registry-image-tag-version.md).

Etiket adlandırma kuralları için [Docker belgelerine](https://docs.docker.com/engine/reference/commandline/tag/)bakın.

### <a name="layer"></a>Katman

Kapsayıcı görüntüleri ve yapıtlar bir veya daha fazla *katmandan* oluşur. Farklı yapıt türleri katmanları farklı şekilde tanımlar. Örneğin, bir Docker kapsayıcı görüntüsünde her katman, görüntüyü tanımlayan Dockerfile içindeki bir satıra karşılık gelir:

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="Kapsayıcı resminin katmanları":::

Kayıt defterindeki yapıtlar ortak katmanları paylaşır ve depolama verimliliğini artırır. Örneğin, farklı depolardaki birçok görüntüde ortak bir ASP.NET Core temel katman olabilir, ancak bu katmanın yalnızca bir kopyası kayıt defterinde saklanır. Katman paylaşımı, birden çok yapıtı ortak katmanlara paylaştırmayla, katman dağıtımını düğümlere de iyileştirir. Zaten bir düğümde bulunan bir görüntü temel olarak ASP.NET Core katmanını içeriyorsa, aynı katmana başvuruda bulunan farklı bir görüntünün sonraki çekme katmanı düğüme aktarılmaz. Bunun yerine, düğüm üzerinde zaten var olan katmana başvurur.

Olası katman işlemesini güvenli yalıtım ve koruma sağlamak için Katmanlar kayıt defterleri arasında paylaşılmaz.

### <a name="manifest"></a>Bildirim

Bir kapsayıcı kayıt defterine gönderilen her kapsayıcı görüntüsü veya yapıtı bir *bildirimle* ilişkilendirilir. İçerik itildiğinde kayıt defteri tarafından oluşturulan bildirim, yapıtları benzersiz bir şekilde tanımlar ve katmanları belirler. Azure CLı komutu ile bir deponun bildirimlerini listelemek için [az ACR Repository Show-bildirimleri][az-acr-repository-show-manifests]' ni kullanabilirsiniz. 

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

Azure CLı komutu ile bir depo için bildirimleri listeleyebilir [az ACR Repository Show-bildirimleri][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Örneğin, "ACR-HelloWorld" deposu için bildirimleri listeleyin:

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp&quot;: &quot;2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp&quot;: &quot;2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp&quot;: &quot;2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Bildirim Özeti

Bildirimler benzersiz bir SHA-256 karması veya *bildirim Özeti* tarafından tanımlanır. Her resim veya yapıt--etiketlenebilir veya değil, Özeti tarafından tanımlanır. Yapının katman verileri başka bir yapıtla aynı olsa bile Özet değeri benzersizdir. Bu mekanizma, aynı etiketli görüntüleri bir kayıt defterine sürekli olarak göndermenize olanak tanır. Örneğin, `myimage:latest` her görüntü benzersiz Özet tarafından tanımlandığından, hata vermeden Kayıt defterinize sürekli olarak gönderim yapabilirsiniz.

Çekme işleminde özetini belirterek bir kayıt defterinden yapı çekebilirsiniz. Aynı etiketli bir görüntü daha sonra kayıt defterine gönderilse bile, bazı sistemler Özet tarafından çekilecek şekilde yapılandırılabilir.

> [!IMPORTANT]
> Aynı etiketlere sahip değiştirilmiş yapıtları sürekli olarak dağıtırsanız, etiketlenmemiş olan ancak kayıt defterinizde boş alan olan "artık" yapıları oluşturabilirsiniz. Resimleri etikete göre listelediğinizde veya görüntülediğinizde etiketlenmemiş görüntüler Azure CLı 'de veya Azure portal gösterilmez. Ancak, katmanları hala mevcut ve kayıt defterinizde alan tüketir. Etiketlenmemiş bir görüntünün silinmesi, bildirim tek bir veya sonuncu olduğunda, belirli bir katmana işaret edildiğinde kayıt defteri alanını serbest bırakır. Etiketlenmemiş görüntüler tarafından kullanılan boşluğu boşaltma hakkında daha fazla bilgi için bkz. [Azure Container Registry kapsayıcı görüntülerini silme](container-registry-delete.md).

## <a name="addressing-an-artifact"></a>Yapıtı adresleme

Docker veya diğer istemci araçları ile gönderme ve çekme işlemlerine yönelik bir kayıt defteri yapıtı için, tam nitelikli kayıt defteri adını, depo adını (varsa ad alanı yolu dahil) ve yapıt etiketini veya bildirim özetini birleştirin. Bu koşulların açıklamaları için önceki bölümlere bakın.

  **Etikete göre adres**: `[loginServerUrl]/[repository][:tag]`
    
  **Özet olarak adres**: `[loginServerUrl]/[repository@sha256][:digest]`  

Bir Azure Container Registry 'ye yapıt çekmek veya göndermek için Docker veya diğer istemci araçlarını kullanırken, *oturum açma sunucusu* adı olarak da bilinen kayıt defteri 'nin tam URL 'sini kullanın. Azure bulutunda, bir Azure Container Registry 'nin tam URL 'SI biçimindedir `myregistry.azurecr.io` (tümü küçük harfle).

> [!NOTE]
> * Kayıt defteri oturum açma sunucusu URL 'sinde, gibi bir bağlantı noktası numarası belirtemezsiniz `myregistry.azurecr.io:443` . 
> * `latest`Komutunuz bir etiketi sağlamazsanız varsayılan olarak etiketi kullanılır.  

   
### <a name="push-by-tag"></a>Etikete göre gönder

Örnekler: 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>Etikete göre Çek

Örnek: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>Bildirim özetine göre Çek


Örnek:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>Sonraki adımlar

Azure Container Registry 'de [kayıt defteri depolama](container-registry-storage.md) ve [desteklenen içerik biçimleri](container-registry-image-formats.md) hakkında daha fazla bilgi edinin.

Azure Container Registry [görüntüleri gönderme ve çekme](container-registry-get-started-docker-cli.md) hakkında bilgi edinin.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
