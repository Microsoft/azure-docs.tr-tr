---
title: Azure Container Registry 'de Held depoları kullanma
description: Uygulamalarınız için grafik depolamak üzere Azure Container Registry ile Held deposunu nasıl kullanacağınızı öğrenin
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: iainfou
ms.openlocfilehash: 2135a3a5a8f14cf6c2e7fd2984d9b221e2445c1d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68309503"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>Uygulama grafiklerinizde Helu deposu olarak Azure Container Registry kullanın

Kubernetes uygulamalarını hızlıca yönetmek ve dağıtmak için, [Açık kaynaklı HELI paket yöneticisini][helm]kullanabilirsiniz. Held ile uygulamalar, Held grafik deposunda depolanan *grafikler* olarak tanımlanır. Bu grafikler, yapılandırma ve bağımlılıkları tanımlar ve uygulama yaşam döngüsü boyunca sürümlenebilir. Azure Container Registry, Held grafik depoları için konak olarak kullanılabilir.

Azure Container Registry, derleme işlem hatları veya diğer Azure hizmetleriyle tümleştirilen özel, güvenli bir Held grafik deposudur. Azure Container Registry hele grafik depoları, grafiklerinizi dağıtımlara ve artıklık için kapalı tutmaya yönelik coğrafi çoğaltma özelliklerini içerir. Yalnızca grafikler tarafından kullanılan depolama alanı için ödeme yaparsınız ve tüm Azure Container Registry fiyat katmanlarında kullanılabilir.

Bu makalede, Azure Container Registry depolanan bir Helu grafik deposunun nasıl kullanılacağı gösterilmektedir.

> [!IMPORTANT]
> Bu özellik şu anda önizleme sürümündedir. Önizlemeler, [ek kullanım koşullarını][terms-of-use] kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaledeki adımları tamamlayabilmeniz için aşağıdaki önkoşulların karşılanması gerekir:

- **Azure Container Registry** -Azure aboneliğinizde bir kapsayıcı kayıt defteri oluşturun. Örneğin, [Azure Portal](container-registry-get-started-portal.md) veya [Azure CLI](container-registry-get-started-azure-cli.md)'yi kullanın.
- **Hele istemci sürümü 2.11.0 (RC sürümü değil) veya daha sonra** geçerli sürümünüzü `helm version` bulmak için ' i çalıştırın. Ayrıca bir Kubernetes kümesi içinde başlatılan bir Held sunucusuna (Tiller) ihtiyacınız vardır. Gerekirse, [bir Azure Kubernetes hizmet kümesi oluşturabilirsiniz][aks-quickstart]. Held 'yi yükleme ve yükseltme hakkında daha fazla bilgi için bkz. [Held yükleme][helm-install].
- Sürümü bulmak için **Azure CLI sürüm 2.0.46 veya üzeri** -çalıştırın `az --version` . Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli-install].

## <a name="add-a-repository-to-helm-client"></a>Hele istemcisine bir depo ekleyin

Held deposu, Held grafiklerini depolayabilen bir HTTP sunucusudur. Azure Container Registry, Held grafikleri için bu depolama alanını sağlayabilir ve havuza grafik ekleyip kaldırırken Dizin tanımını yönetebilir.

Azure Container Registry hele grafik deposu olarak eklemek için Azure CLı 'yi kullanırsınız. Bu yaklaşımda, HELI istemciniz Azure Container Registry tarafından desteklenen deponun URI ve kimlik bilgileriyle güncelleştirilir. Bu depo bilgilerini el ile belirtmeniz gerekmez, bu nedenle kimlik bilgileri komut geçmişinde gösterilmez, örneğin.

Gerekirse, Azure CLı 'da oturum açın ve istemleri izleyin:

```azurecli
az login
```

[Az configure][az-configure] komutunu kullanarak Azure CLI varsayılan değerlerini Azure Container Registry adıyla yapılandırın. Aşağıdaki örnekte, öğesini kayıt defterinizin `<acrName>` adıyla değiştirin:

```azurecli
az configure --defaults acr=<acrName>
```

Şimdi [az ACR Helm depo Add][az-acr-helm-repo-add] komutunu kullanarak Azure Container Registry Helm grafik deponuzu Helm istemcimize ekleyin. Bu komut, Azure Container kayıt defteriniz için Helm istemcisi tarafından kullanılan bir kimlik doğrulama belirteci alır. Kimlik doğrulama belirteci 1 saat için geçerlidir. Benzer şekilde `docker login`, Azure Container Registry Held grafik deponuzu ile helmistemcinizin kimliğini doğrulamak için bu komutu gelecekteki CLI oturumlarında çalıştırabilirsiniz:

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>Depoya bir grafik ekleyin

Bu makalede, genel Helm *kararlı* deposundan mevcut bir Helm grafiği edinebilirsiniz. *Kararlı* depo, ortak uygulama grafiklerini içeren, seçkin bir ortak depodır. Paket bakım makineleri, Docker Hub 'ının ortak kapsayıcı görüntüleri için ortak bir kayıt defteri sağladığı şekilde grafiklerini *kararlı* depoya gönderebilirler. Ortak *kararlı* depodan indirilen grafik daha sonra özel Azure Container Registry deponuza gönderilir. Çoğu senaryoda, geliştirdiğiniz uygulamalar için kendi grafiklerinizi derleyip karşıya yüklersiniz. Kendi hele grafiklerini oluşturma hakkında daha fazla bilgi için bkz. [HELI grafikleri geliştirme][develop-helm-charts].

İlk olarak, *~/ACR-Held*konumunda bir dizin oluşturun, ardından mevcut *kararlı/WordPress* grafiğini indirin:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

İndirilen grafiği listeleyin ve dosya adına dahil olan WordPress sürümünü aklınızda yapın. Komut belirli bir sürüm belirtmedi, bu nedenle *en son* sürüm getirildi. `helm fetch stable/wordpress` Tüm Heli grafikleri, [Semver 2][semver2] standardını izleyen dosya adında bir sürüm numarası içerir. Aşağıdaki örnek çıktıda, WordPress grafiğinin sürümü *2.1.10*olur:

```
$ ls

wordpress-2.1.10.tgz
```

Şimdi Azure CLı [az ACR Helm Push][az-acr-helm-push] komutunu kullanarak Azure Container Registry ' deki Helm grafik deponuza grafik gönderin. Önceki adımda indirilen HELI grafiğinin adını belirtin, örneğin *WordPress-2.1.10. tgz*:

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

Birkaç dakika sonra Azure CLı, aşağıdaki örnek çıktıda gösterildiği gibi, grafiğinizin kaydedildiğini bildirir:

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>Depodaki grafikleri listeleme

Helmclient, uzak depoların içeriklerinin yerel olarak önbelleğe alınmış bir kopyasını tutar. Uzak bir depodaki değişiklikler, hele istemcisi tarafından yerel olarak bilinen kullanılabilir grafiklerin listesini otomatik olarak güncelleştirmez. Depolarda grafik ararken, HELI yerel önbelleğe alınmış dizini kullanır. Önceki adımda karşıya yüklenen grafiği kullanmak için yerel Held depo dizininin güncellenmesi gerekir. Hele istemcisinde depoları yeniden düzenleyebilir veya depo dizinini güncelleştirmek için Azure CLı kullanabilirsiniz. Deponuza bir grafik eklediğiniz her sefer, bu adımın tamamlanması gerekir:

```azurecli
az acr helm repo add
```

Deponuzda depolanan ve güncelleştirilmiş Dizin yerel olarak bulunan bir grafik ile, aramak veya yüklemek için normal hele istemci komutlarını kullanabilirsiniz. Deponuzdaki tüm grafikleri görmek için kullanın `helm search <acrName>`. Kendi Azure Container Registry adınızı sağlayın:

```console
helm search <acrName>
```

Önceki adımda gönderilen WordPress grafiği, aşağıdaki örnek çıktıda gösterildiği gibi listelenir:

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

Grafikleri, [az ACR Helm List][az-acr-helm-list]kullanarak Azure CLI ile de listeleyebilirsiniz:

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Hela grafiğinin bilgilerini göster

Depodaki belirli bir grafiğin bilgilerini görüntülemek için, normal hele istemcisini yeniden kullanabilirsiniz. *WordPress*adlı grafiğin bilgilerini görmek için kullanın `helm inspect`.

```console
helm inspect <acrName>/wordpress
```

Sürüm numarası sağlanmamışsa, *en son* sürüm kullanılır. HELI, aşağıdaki sıkıştırılmış örnek çıktıda gösterildiği gibi, grafiğiniz hakkında ayrıntılı bilgiler döndürür:

```
$ helm inspect myacrhelm/wordpress

appVersion: 4.9.8
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: https://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: bitnami-bot
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 2.1.10
[...]
```

Ayrıca, Azure CLı [az ACR Helm Show][az-acr-helm-show] komutuyla bir grafik için bilgileri gösterebilirsiniz. Yine, bir grafiğin *en son* sürümü varsayılan olarak döndürülür. Grafiğin belirli bir `--version` sürümünü listeye ekleyebilirsiniz, örneğin, *2.1.10*:

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Depodan bir Held grafiği yükler

Deponuzdaki hele grafiği, Depo adı ve sonra grafik adı belirtilerek yüklenir. WordPress grafiğini yüklemek için hele istemcisini kullanın:

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Azure Container Registry hele grafik deponuza gönderim yaparsanız ve daha sonra yeni bir CLı oturumunda geri dönerseniz, yerel hele istemciniz güncelleştirilmiş bir kimlik doğrulama belirtecine ihtiyaç duyuyor. Yeni bir kimlik doğrulama belirteci almak için [az ACR Helm depo Add][az-acr-helm-repo-add] komutunu kullanın.

Aşağıdaki adımlar, yüklemesi işlemi sırasında tamamlanır:

- Hela istemcisi yerel depo dizinini arar.
- Karşılık gelen grafik Azure Container Registry deposundan indirilir.
- Grafik, Kubernetes kümenizdeki Tiller kullanılarak dağıtılır.

Aşağıdaki sıkıştırılmış örnek çıktısı, Helu grafiğinden dağıtılan Kubernetes kaynaklarını göstermektedir:

```
$ helm install myacrhelm/wordpress

NAME:   irreverent-jaguar
LAST DEPLOYED: Thu Sep 13 21:44:20 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                          READY  STATUS   RESTARTS  AGE
irreverent-jaguar-wordpress-7ff46d9b8c-b7v6m  0/1    Pending  0         1s
irreverent-jaguar-mariadb-0                   0/1    Pending  0         1s
[...]
```

## <a name="delete-a-helm-chart-from-the-repository"></a>Depodaki bir hela grafiğini silme

Bir grafiği depodan silmek için [az ACR Helm Delete][az-acr-helm-delete] komutunu kullanın. *WordPress*gibi grafiğin adını ve silinecek sürümü (örneğin, *2.1.10*) belirtin.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

Adlandırılmış grafiğin tüm sürümlerini silmek istiyorsanız, `--version` parametresini bırakın.

Grafik ' de `helm search <acrName>`döndürülmeye devam eder. Yine, HELI istemcisi bir depodaki kullanılabilir grafiklerin listesini otomatik olarak güncelleştirmez. Helm istemci deposu dizinini güncelleştirmek için [az ACR Helm depo Add][az-acr-helm-repo-add] komutunu tekrar kullanın:

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, genel *kararlı* depodan mevcut bir hela grafiği kullanıldı. HELI grafikleri oluşturma ve dağıtma hakkında daha fazla bilgi için bkz. [HELI grafikleri geliştirme][develop-helm-charts].

Held grafikleri kapsayıcı oluşturma sürecinin bir parçası olarak kullanılabilir. Daha fazla bilgi için bkz. [Azure Container Registry görevleri kullanma][acr-tasks].

Azure Container Registry kullanımı ve yönetimi hakkında daha fazla bilgi için [en iyi uygulamalar][acr-bestpractices]bölümüne bakın.

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://docs.helm.sh/developing_charts/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
