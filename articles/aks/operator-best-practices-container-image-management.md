---
title: Operatör en iyi yöntemleri-Azure Kubernetes hizmetlerinde kapsayıcı görüntüsü yönetimi (AKS)
description: Azure Kubernetes Service (AKS) ' de kapsayıcı görüntülerini yönetme ve güvenli hale getirme için küme operatörü en iyi uygulamalarını öğrenin
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 998d8602b6aa0e71a04f75aff1c29551ba09c8a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105128"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içindeki kapsayıcı görüntüsü yönetimi ve güvenlik için en iyi uygulamalar

Azure Kubernetes Service 'te (AKS) uygulama geliştirirken ve çalıştırırken, kapsayıcı ve kapsayıcı görüntüsü güvenliği önemli bir önceliktir. Güncel olmayan temel görüntülerle veya düzeltme eki yüklenmemiş uygulama çalışma zamanlarının bulunduğu kapsayıcılar bir güvenlik riski ve olası saldırı vektörü sağlar. 

Derleme ve çalışma zamanında kapsayıcılarınızdaki tarama ve düzeltme araçlarını tümleştirerek ve çalıştırarak riskleri en aza indirin. Daha önce bu açığı veya eski temel görüntüyü yakalarsanız, kümenizin daha güvenli olmasını sağlayabilirsiniz. 

Bu makalede, *"kapsayıcılar"* her ikisi de anlamına gelir:
* Bir kapsayıcı kayıt defterinde depolanan kapsayıcı görüntüleri.
* Çalışan kapsayıcılar.

Bu makalede, AKS 'de kapsayıcılarınızın güvenliğini sağlama konusu ele alınmaktadır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Görüntü oluşturma güvenlik açıklarını tarayın ve düzeltin.
> * Bir temel görüntü güncelleştirilirken kapsayıcı görüntülerini otomatik olarak tetikler ve yeniden dağıtın.

[Küme güvenliği][best-practices-cluster-security] ve [Pod güvenliği][best-practices-pod-security]için en iyi yöntemleri de okuyabilirsiniz.

Kapsayıcılarınızın güvenlik açıklarına karşı taranmasına yardımcı olması için [Güvenlik Merkezi 'Nde kapsayıcı güvenliği][security-center-containers] de kullanabilirsiniz. Güvenlik Merkezi ile [Azure Container Registry tümleştirme][security-center-acr] , yansımalarınızı ve kayıt defterinizi güvenlik açıklarına karşı korumanıza yardımcı olur.

## <a name="secure-the-images-and-run-time"></a>Görüntülerin ve çalışma zamanının güvenliğini sağlama

> **En iyi yöntemler kılavuzu** 
>
> Güvenlik açıkları için kapsayıcı görüntülerinizi tarayın. Yalnızca doğrulanan görüntüleri dağıtın. Temel görüntüleri ve uygulama çalışma zamanını düzenli olarak güncelleştirin. AKS kümesindeki iş yüklerini yeniden dağıtın.

Kapsayıcı tabanlı iş yüklerini benimseme sırasında, kendi uygulamalarınızı oluşturmak için kullanılan görüntülerin ve çalışma zamanının güvenliğini doğrulamak isteyeceksiniz. Dağıtımlarınıza yönelik güvenlik açıklarına nasıl ulaşmaktan kaçının? 
* [Twistlock][twistlock] veya [deniz mavisi][aqua]gibi araçları kullanarak kapsayıcı görüntülerini taramak için dağıtım iş akışınıza dahil edin.
* Yalnızca doğrulanmış görüntülerin dağıtılmasına izin verin.

![Kapsayıcı görüntülerini tarayın ve düzeltin, doğrulayın ve dağıtın](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Örneğin, sürekli tümleştirme ve sürekli dağıtım (CI/CD) işlem hattını kullanarak görüntü taramalarını, doğrulamayı ve dağıtımlarını otomatik hale getirebilirsiniz. Azure Container Registry, bu güvenlik açıkları tarama özelliklerini içerir.

## <a name="automatically-build-new-images-on-base-image-update"></a>Temel görüntü güncelleştirmesinde otomatik olarak yeni görüntüler oluşturma

> **En iyi yöntemler kılavuzu** 
>
> Uygulama görüntüleri için temel görüntüleri kullanırken, temel görüntü güncelleştirilirken yeni görüntüler oluşturmak için Otomasyon kullanın. Güncelleştirilmiş temel görüntüler genellikle güvenlik düzeltmelerini içerdiğinden, tüm aşağı akış uygulama kapsayıcısı görüntülerini güncelleştirin.

Bir temel görüntü her güncelleştirildiği zaman, herhangi bir aşağı akış kapsayıcı görüntüsünü de güncelleştirmeniz gerekir. Bu derleme işlemini [Azure Pipelines][azure-pipelines] veya Jenkins gibi doğrulama ve dağıtım işlem hatları ile tümleştirin. Bu işlem hatları, uygulamalarınızın güncelleştirilmiş tabanlı görüntülerde çalışmaya devam etmesini sağlar. Uygulama kapsayıcısı görüntüleriniz doğrulandıktan sonra, AKS dağıtımları, en son güvenli görüntüleri çalıştıracak şekilde güncellenebilir.

Azure Container Registry görevler, temel görüntü güncelleştirilirken kapsayıcı görüntülerini da otomatik olarak güncelleştirebilir. Bu özellikle, birkaç temel görüntü oluşturup bunları hata ve güvenlik düzeltmeleriyle güncelleştirmiş olursunuz.

Temel görüntü güncelleştirmeleri hakkında daha fazla bilgi için bkz. [Azure Container Registry görevlerle temel görüntü güncelleştirme üzerinde görüntü derlemelerini otomatikleştirme][acr-base-image-update].

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, kapsayıcılarınızın güvenliğini sağlama konusunda odaklanılmıştır. Bu alanlardan bazılarını uygulamak için aşağıdaki makalelere bakın:

* [Azure Container Registry görevlerle temel görüntü güncelleştirmesinde görüntü derlemelerini otomatikleştirin][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: ../security-center/container-security.md
[security-center-acr]: ../security-center/defender-for-container-registries-introduction.md