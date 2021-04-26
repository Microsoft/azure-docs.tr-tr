---
title: Azure Dev Spaces bir özel Held deposu kullanma
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Azure dev alanında özel bir Held deposu kullanın.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kapsayıcılar, Held
manager: gwallace
ms.openlocfilehash: 7c5f28595df2e552fd48033b44e4e1f0ea4ec306
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960346"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Azure Dev Spaces bir özel Held deposu kullanın

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

[Held][helm] , Kubernetes için bir paket yöneticisidir. Held bağımlılıkları paketlemek için bir [grafik][helm-chart] biçimi kullanır. HELI grafikleri, genel veya özel olabilen bir depoda depolanır. Azure Dev Spaces, uygulamanızı çalıştırırken yalnızca ortak depolardan hele grafikleri alır. HELI deposunun özel veya Azure Dev Spaces erişemez durumda, bu depodan doğrudan uygulamanıza bir grafik ekleyebilirsiniz. Grafiği doğrudan eklemek Azure Dev Spaces özel Helu deposuna erişmek zorunda kalmadan uygulamanızı çalıştırmaya izin verir.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Özel Held deposunu yerel makinenize ekleyin

Yerel makinenizden özel Helu deposuna erişmek için [helmrepo Add][helm-repo-add] ve [hele depo Update][helm-repo-update] kullanın.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Grafiği uygulamanıza ekleyin

Projenizin dizinine gidin ve çalıştırın `azds prep` .

```cmd
azds prep --enable-ingress
```

> [!TIP]
> `prep`Komut, projeniz için [bir Dockerfile ve hela grafiği](../how-dev-spaces-works-prep.md#prepare-your-code) oluşturmaya çalışır. Azure Dev Spaces, kodunuzu derlemek ve çalıştırmak için bu dosyaları kullanır, ancak projenin oluşturulup çalıştırıldığını değiştirmek istiyorsanız bu dosyaları değiştirebilirsiniz.

Uygulamanızın grafik dizininde grafiğinizdeki bir [requirements. YAML][helm-requirements] dosyası oluşturun. Örneğin, uygulamanız *APP1* olarak adlandırılmışsa, *grafikler/APP1/requirements. YAML* oluşturursunuz.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Uygulamanıza ait grafik bağımlılıklarını güncelleştirmek ve grafiği özel depodan indirmek için uygulamanızın grafik dizinine gidin ve [Held bağımlılık güncelleştirme][helm-dependency-update] ' yi kullanın.

```cmd
helm dependency update
```

Uygulamanızın grafik dizinine bir *TGT z* dosyası içeren bir *grafik* alt dizininin eklendiğinden emin olun. Örneğin, *grafikler/APP1/grafikler/MyChart-0.1.0. tgz*.

Özel Held deponuzdaki grafik indirildi ve projenize eklendi. *Requirements. YAML* dosyasını, dev alanlarının bu bağımlılığı güncelleştirmeye deneyebilmesi için kaldırın.

## <a name="run-your-application"></a>Uygulamanızı çalıştırma

Projenizin kök dizinine gidin ve `azds up` Uygulamanızın geliştirme alanınızda başarılı bir şekilde çalıştığını doğrulamak için çalıştırın.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>Sonraki adımlar

[Held ve nasıl çalıştığı][helm]hakkında daha fazla bilgi edinin.

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
