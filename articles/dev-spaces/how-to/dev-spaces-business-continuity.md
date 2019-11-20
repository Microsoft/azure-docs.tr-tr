---
title: Azure geliştirme alanları'nda iş sürekliliği ve olağanüstü durum kurtarma
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: Azure’da kapsayıcılar ve mikro hizmetlerle hızlı Kubernetes geliştirme
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kapsayıcılar, Helm, hizmet kafes, ağ hizmeti Yönlendirme, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 2da92b4fcd98024ada8d852d65e08fe8c70e3884
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704049"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Azure geliştirme alanları'nda iş sürekliliği ve olağanüstü durum kurtarma

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Olağanüstü Durum Kurtarma Kılavuzu Azure Kubernetes Service (AKS) için gözden geçirin

Azure geliştirme alanları Azure Kubernetes Service (AKS) bir özelliktir. Aks'deki olağanüstü durum kurtarma için yönergeler farkında olmanız ve geliştirme alanları için kullandığınız AKS kümeleri için geçerli olup olmadığını göz önünde bulundurun gerekir. Daha fazla bilgi için lütfen başvuru [iş sürekliliği ve olağanüstü durum kurtarma Azure Kubernetes Service (AKS) için en iyi yöntemler](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Geliştirme alanları farklı bölgelerde AKS kümelerinde etkinleştirme

Geliştirme alanları farklı bölgelerde AKS kümelerinde etkinleştirilmesi geliştirme alanları kullanarak bir Azure bölgesine hatadan hemen sonra devam etmek sağlar.

AKS, çok bölgeli dağıtımlar hakkında genel bilgi için bkz: [planlamak için çok bölgeli dağıtım](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment)

Azure geliştirme alanları ile uyumlu bir AKS kümesi dağıtma hakkında daha fazla bilgi için bkz: [Azure Cloud Shell kullanarak bir Kubernetes kümesi oluşturma](https://docs.microsoft.com/azure/dev-spaces/how-to/create-cluster-cloud-shell)

### <a name="enable-dev-spaces-via-the-azure-portal"></a>Azure portalı üzerinden geliştirme alanları etkinleştirme

Tıklayın **geliştirme alanları** Gezinti öğesi özelliklerini Azure portalında her kümesinin altında. Geliştirme alanları etkinleştirme seçeneği seçin.

![Azure portal aracılığıyla etkinleştirme geliştirme alanları](../media/common/enable-dev-spaces.jpg)

Her küme için bu işlemi yineleyin.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Azure CLI aracılığıyla geliştirme alanları etkinleştirme

Komut satırında geliştirme alanları da etkinleştirebilirsiniz:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Her küme için takımınızın temeli dağıtma

Geliştirme alanları ile çalışırken, Kubernetes kümenizde genellikle üst geliştirme alanı için tüm uygulama dağıtın. Varsayılan olarak, `default` alanı kullanılır. İlk dağıtım, bu hizmetleri, veritabanları veya kuyruklar gibi bağımlı dış kaynaklara yanı sıra tüm hizmetleri içerir. Bu olarak bilinir *temel*. Bir temel üst geliştirme alanında ayarladıktan sonra üzerinde yineleme gerçekleştirin ve alt geliştirme alanları içinde tek tek hizmetlerinde hata ayıklama.

Birden çok bölgede kümelerine hizmetlerinin taban çizgisi kümenizi en son sürümlerini dağıtmanız gerekir. Güncelleştirme taban çizgisi hizmetlerinizi bu şekilde, bir Azure bölgesine hatası olursa geliştirme alanları kullanmaya devam edebilirsiniz sağlar. Örneğin, temel bir CI/CD işlem hattı aracılığıyla dağıtırsanız, böylece farklı bölgelerdeki birden fazla küme dağıtır işlem hattı değiştirin.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Geliştirme alanları için kullanılacak doğru AKS kümesi seçin

Takımınızın temel çalışan bir yedekleme kümesi düzgün şekilde yapılandırdıktan sonra hızlı bir şekilde yedekleme kümeye dilediğiniz zaman geçebilirsiniz. Geliştirme alanlarında üzerinde çalıştığınız tek tek hizmetleri yeniden çalıştırabilirsiniz.

Aşağıdaki CLI komutunu farklı bir kümeyle seçin:

```cmd
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Aşağıdaki komutla yeni kümede kullanılabilir geliştirme alanları listeleyebilirsiniz:

```cmd
azds space list
```

İş için yeni bir geliştirme alanı oluşturun veya aşağıdaki komutla var olan bir geliştirme alanı seçin:

```cmd
azds space select -n <space name>
```

Bu komutları çalıştırdıktan sonra seçili küme ve geliştirme alanı sonraki CLI işlemleri ve Azure Dev alanları için Visual Studio Code uzantısı kullanma projelerinde hata ayıklama için kullanılacak.

Visual Studio kullanıyorsanız, aşağıdaki adımları var olan bir proje tarafından kullanılan Küme geçiş yapabilirsiniz:

1. Projenizi Visual Studio'da açın.
1. Çözüm Gezgini'nde proje adına sağ tıklayın ve tıklayın **özellikleri**
1. Sol bölmede **hata ayıklama**
1. Hata ayıklama özellikleri sayfasında tıklayın **profili** açılan listesindeki **Azure geliştirme alanları**.
1. Tıklayın **değişiklik** düğmesi.
1. Görüntülenen iletişim kutusunda, kullanmak istediğiniz bir AKS kümesi seçin. İsterseniz, iş için farklı geliştirme alanı seçin veya uygun seçeneği seçerek yeni bir geliştirme alan oluşturabilirsiniz **alanı** aşağı açılan listesi.

Alan ve doğru küme seçtikten sonra hizmeti geliştirme alanlarında çalıştırmak için F5 tuşuna basabilirsiniz.

Özgün kümeyi kullanacak şekilde yapılandırılmış diğer projeler için bu adımları yineleyin.

## <a name="access-a-service-on-a-backup-cluster"></a>Bir yedekleme kümesi üzerinde bir hizmete erişme

Ardından hizmetinizin genel bir DNS adı kullanmak için yapılandırdıysanız, bir yedekleme kümesinde çalıştırırsanız hizmetin farklı bir URL gerekir. Genel DNS adları her zaman biçiminde `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io`. Farklı bir kümeye geçiş, küme GUID ve büyük olasılıkla bölge değişecektir.

Geliştirme alanları çalıştırırken hizmetinin doğru URL her zaman gösterir `azds up`, ya da altında Visual Studio çıktı penceresinde **Azure geliştirme alanları**.

Çalıştırarak URL bulabilirsiniz `azds list-uris` komutu:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Hizmet erişim sırasında bu URL'yi kullanın.
