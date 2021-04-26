---
title: Veri denetleyicisi oluşturma
description: Zaten dağıttığınız tipik bir çok düğümlü Kubernetes kümesi üzerinde Azure Arc veri denetleyicisi oluşturun.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 329df78bb5829695b95fcca5b7ed7e1439ced821
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688374"
---
# <a name="create-the-azure-arc-data-controller"></a>Azure Arc veri denetleyicisi oluşturma

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-creating-the-azure-arc-data-controller"></a>Azure Arc veri denetleyicisi oluşturmaya genel bakış

Azure Arc etkin veri Hizmetleri, birden fazla farklı yaklaşım kullanılarak birden çok farklı türde Kubernetes kümesi ve yönetilen Kubernetes Hizmetleri üzerinde oluşturulabilir.

Şu anda, Kubernetes Hizmetleri ve dağıtımların desteklenen listesi şunlardır:

- Azure Kubernetes Service (AKS)
- Azure Stack 'de Azure Kubernetes hizmet altyapısı (AKS motoru)
- Azure Stack CI üzerinde Azure Kubernetes hizmeti
- Azure RedHat OpenShift (ARO)
- OpenShift kapsayıcı platformu (OCP)
- AWS Elastic Kubernetes Service (EKS)
- Google Cloud Kubernetes altyapısı (GKE)
- Genellikle kubeadm kullanarak dağıtılan açık kaynak, yukarı akış Kubernetes

> [!IMPORTANT]
> * Kubernetes 'in desteklenen en düşük sürümü v 1.17. Daha fazla bilgi için [bilinen sorunlara](./release-notes.md#known-issues) bakın. 
> * OCP 'nin desteklenen en düşük sürümü 4,5 ' dir.
> * Ortamınız ve Azure arasında hangi bağlantının gerekli olduğunu anlamak için [bağlantı gereksinimlerine](connectivity.md) bakın.
> * Kalıcı depolama alanınızı yapılandırmaya ilişkin ayrıntıları öğrenmek için [depolama yapılandırma kılavuzu](storage-configuration.md) ' na bakın.
> * Azure Kubernetes hizmetini kullanıyorsanız, kümenizin çalışan düğümü VM 'sinin boyutu en az **Standard_D8s_v3** olmalıdır ve **Premium diskleri kullanmalıdır.** Küme birden çok kullanılabilirlik bölgesini kapsamamalıdır. 
> * Başka bir Kubernetes dağıtımı veya hizmeti kullanıyorsanız, Kubernetes düğümlerinizin tümünde en az 8 GB RAM ve 4 çekirdek ve toplam 32 GB RAM kapasiteye sahip olduğunuzdan emin olmanız gerekir. Örneğin, 32 GB RAM ve 4 çekirdekte 1 düğüme sahip olabilirsiniz veya her biri 16 GB RAM ve 4 çekirdeğe sahip 2 düğüme sahip olabilirsiniz.

> [!NOTE]
> Azure 'da Red Hat OpenShift kapsayıcı platformu kullanıyorsanız, kullanılabilir en son sürümü kullanmanız önerilir.

Seçtiğiniz seçeneğe bağlı olarak, bazı araçlar _gerekli_ olacaktır, ancak Azure Arc veri denetleyicisi 'ni oluşturmaya başlamadan önce [tüm istemci araçlarının yüklenmesi](./install-client-tools.md) önerilir.

Seçtiğiniz seçenek ne olursa olsun, oluşturma işlemi sırasında aşağıdaki bilgileri sağlamanız gerekir:

- **Veri denetleyicisi adı** -veri denetleyiciniz için açıklayıcı bir ad (örneğin, "üretim verileri denetleyicisi", "Seattle veri denetleyicisi").
- **Veri denetleyicisi Kullanıcı adı** -veri denetleyicisi Yönetici kullanıcısı için herhangi bir Kullanıcı adı.
- **Veri denetleyicisi parolası** -veri denetleyicisi Yönetici kullanıcısı için bir parola.
- **Kubernetes ad alanı adı** -içinde veri denetleyicisi oluşturmak Istediğiniz Kubernetes ad alanının adı.
- **Bağlantı modu** -bağlantı modu, Azure Arc etkin veri Hizmetleri ortamınızdan Azure 'a bağlantı derecesini belirler. Önizleme Şu anda yalnızca dolaylı olarak bağlı ve doğrudan bağlı modlarını destekler.  Bilgi için bkz. [bağlantı modu](./connectivity.md). 
- **Azure ABONELIK kimliği** -Azure 'da veri denetleyicisi kaynağının oluşturulmasını istediğiniz Azure abonelik GUID 'si.
- **Azure Kaynak grubu adı** -Azure 'da veri denetleyicisi kaynağının oluşturulmasını istediğiniz kaynak grubunun adı.
- **Azure konumu** -veri denetleyicisi kaynak meta verilerinin Azure 'Da depolanacağı Azure konumu. Kullanılabilir bölgelerin listesi için bkz. [bölgeye göre Azure genel altyapısı/ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

## <a name="next-steps"></a>Sonraki adımlar

Azure Arc veri denetleyicisi oluşturmak için birden çok seçenek vardır:

> **Yalnızca bir şeyi denemek mi istiyorsunuz?**  
> Azure Kubernetes Service (AKS), AWS elastik Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) veya bir Azure VM 'de [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) ile hızlıca çalışmaya başlayın!
> 
- [İle veri denetleyicisi oluşturma [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](create-data-controller-using-azdata.md)
- [Azure Data Studio bir veri denetleyicisi oluşturma](create-data-controller-azure-data-studio.md)
- [Azure Data Studio bir Jupyter Not defteri aracılığıyla Azure portal bir veri denetleyicisi oluşturun](create-data-controller-resource-in-azure-portal.md)
- [Kubectl veya OC gibi Kubernetes araçlarıyla veri denetleyicisi oluşturma](create-data-controller-using-kubernetes-native-tools.md)
- [Bir test dağıtımının hızlandırılmış deneyimi için Azure Arc Jumpstart ile veri denetleyicisi oluşturma](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)
