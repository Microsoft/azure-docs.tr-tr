---
title: İşleç en iyi uygulamalar - kapsayıcı görüntüsü Yönetimi Azure Kubernetes Hizmetleri (AKS)
description: Yönetme ve güvenli kapsayıcı görüntülerini Azure Kubernetes Service (AKS) için küme işleci en iyi uygulamaları öğrenin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mlearned
ms.openlocfilehash: 3feadaca361950df2a09f8da33fe380fc3763763
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "67614823"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Kapsayıcı görüntü yönetimi ve güvenliği Azure Kubernetes Service (AKS) için en iyi uygulamalar

Kapsayıcılar ve kapsayıcı görüntüleri güvenliğini geliştirmek ve Azure Kubernetes Service (AKS) uygulamaları çalıştırma gibi anahtar bir noktadır. Eski içeren kapsayıcı görüntülerini temel veya yüklenmemiş bir uygulama çalışma zamanını bir güvenlik riski ve olası bir saldırı vektörü tanıtır. Bu riskleri azaltmak için tarayın ve çalışma zamanı yanı sıra derleme zamanını kapsayıcılarınızı sorunları düzeltmek araçları tümleşmelidir. Güvenlik Açığı veya güncel temel görüntü olarak yakalanır işlemde daha önce küme daha güvenli hale getirin. Bu makalede, *kapsayıcıları* kapsayıcı kayıt defteri ve çalışan kapsayıcıları depolanan her iki kapsayıcı görüntülerini anlamına gelir.

Bu makalede aks'deki kapsayıcılarınızı güvenliğini nasıl ele alınmaktadır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Tarama ve resim güvenlik açıklarını düzeltin
> * Otomatik olarak tetikleyin ve temel görüntüsü güncelleştirildiğinde kapsayıcı görüntülerini yeniden dağıtma

[Küme güvenliği][best-practices-cluster-security] ve [Pod güvenliği][best-practices-pod-security]için en iyi yöntemleri de okuyabilirsiniz.

## <a name="secure-the-images-and-run-time"></a>Görüntüleri güvenli ve çalışma zamanı

**En iyi uygulama kılavuzunu** - kapsayıcı görüntülerinizi güvenlik açıkları için tarayın ve yalnızca doğrulama başarılı görüntüleri dağıtma. Düzenli olarak uygulama çalışma zamanı ve temel görüntüleri güncelleştirin, sonra AKS kümesinde iş yükleri yeniden dağıtın.

Bir kapsayıcı tabanlı iş yüklerini benimsenmesini içeriğiyle görüntüleri ve kendi uygulamalarınızı oluşturmak için kullanılan çalışma zamanı güvenliğini doğruluyor. Nasıl güvenlik açıklarını dağıtımlarınızı açmadığınızdan emin olabilirim? Dağıtım iş akışınız, [twistlock][twistlock] veya [deniz mavisi][aqua]gibi araçları kullanarak kapsayıcı görüntülerini taramak için bir işlem içermelidir ve sonra yalnızca doğrulanmış görüntülerin dağıtılmasına izin verir.

![Tarama ve düzeltme kapsayıcı görüntüleri, doğrulamak ve dağıtma](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Gerçek hayatta kullanılan örnekte, görüntü tarama, doğrulama ve dağıtımı otomatik hale getirmek için bir sürekli tümleştirme ve sürekli dağıtım (CI/CD) işlem hattı kullanabilirsiniz. Azure Container Registry açıklarından tarama özellikleri içerir.

## <a name="automatically-build-new-images-on-base-image-update"></a>Yeni görüntü temel görüntü güncelleştirme otomatik olarak oluşturma

**En iyi uygulama kılavuzunu** - temel görüntü güncelleştirilirken yeni görüntüleri oluşturmak için kullanımı Otomasyon uygulama görüntüleri için temel görüntüleri olarak. Bu temel görüntüleri genellikle güvenlik düzeltmelerini içeren herhangi bir aşağı akış uygulaması kapsayıcı görüntülerini güncelleştirin.

Temel görüntü her güncelleştirildiğinde tüm aşağı akış kapsayıcı görüntülerini de güncelleştirilmesi gerekir. Bu derleme işlemi, [Azure Pipelines][azure-pipelines] veya Jenkins gibi doğrulama ve dağıtım işlem hatları ile tümleştirilebilmelidir. Bu işlem hatları uygulamalarınızı güncelleştirilmiş temel görüntüleri üzerinde çalışmaya devam ettiğinden emin yapar. Uygulama kapsayıcı görüntülerinizi doğrulandıktan sonra AKS dağıtımları daha sonra en son, güvenli görüntülerini çalıştırmak için güncelleştirilebilir.

Temel görüntü güncelleştirildiğinde azure kapsayıcı kayıt defteri görevleri kapsayıcı görüntülerini otomatik olarak güncelleştirebilirsiniz. Bu özellik, az sayıda temel görüntüleri oluşturmak ve düzenli olarak hata ve güvenlik düzeltmeleri ile güncel kalmasını sağlar.

Temel görüntü güncelleştirmeleri hakkında daha fazla bilgi için bkz. [Azure Container Registry görevlerle temel görüntü güncelleştirme üzerinde görüntü derlemelerini otomatikleştirme][acr-base-image-update].

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kapsayıcılarınızı güvenli nasıl odaklanır. Bu alanlardan bazıları uygulamak için aşağıdaki makalelere bakın:

* [Azure Container Registry görevlerle temel görüntü güncelleştirmesinde görüntü derlemelerini otomatikleştirin][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
