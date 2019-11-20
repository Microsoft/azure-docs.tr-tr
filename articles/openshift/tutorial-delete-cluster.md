---
title: Öğretici - Azure Red Hat OpenShift küme silme | Microsoft Docs
description: Bu öğreticide, Azure CLI kullanarak bir Azure Red Hat OpenShift küme silmeyi öğrenin
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: 0ad70f4c3681705377a350fee8b02a55c526f26c
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669347"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Öğretici: Azure Red Hat OpenShift küme silme

Öğreticinin sonuna geldiniz. Örnek Küme Testi tamamladığınızda, ne kullanmadığınız için ücretlendirilirsiniz olmayan şekilde ve ilişkili kaynakları silme işlemini aşağıda verilmiştir.

Serinin üçüncü bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * Azure Red Hat OpenShift küme silme

Bu öğretici dizisinde şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * [Azure Red Hat OpenShift kümesi oluşturma](tutorial-create-cluster.md)
> * [Azure Red Hat OpenShift kümesini ölçeklendirme](tutorial-scale-cluster.md)
> * Azure Red Hat OpenShift küme silme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* Bir küme oluşturma [Azure Red Hat OpenShift küme oluşturma](tutorial-create-cluster.md) öğretici.

## <a name="step-1-sign-in-to-azure"></a>1\. adım: Azure'da oturum açma

Azure CLI'yi yerel olarak çalıştırıyorsanız, çalıştırma `az login` için Azure'da oturum açın.

```bash
az login
```

Birden çok aboneliğe erişiminiz çalıştırırsanız `az account set -s {subscription ID}` değiştirerek `{subscription ID}` kullanmak istediğiniz aboneliği ile.

## <a name="step-2-delete-the-cluster"></a>2\. adım: Küme silme

Bir Bash Terminali açın ve kümenizin adını değişken küme_adı ayarlayın:

```bash
CLUSTER_NAME=yourclustername
```

Artık kümenize silin:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Kümeyi silmek isteyip istemediğinizi sizden istenir. İle onayladıktan sonra `y`, kümeyi silmek için birkaç dakika sürer. Komut tamamlandığında, kaynak grubun tamamını ve içindeki tüm kaynaklar kümesi de dahil olmak üzere silinir.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Azure portalını kullanarak küme silme

Alternatif olarak, çevrimiçi Azure portalı üzerinden kümenizin ilişkilendirilmiş kaynak grubunu silebilirsiniz. Kaynak grubu adı, küme adı ile aynıdır.

Şu anda `Microsoft.ContainerService/openShiftManagedClusters` kümeyi oluşturduğunuzda, oluşturulan kaynak, Azure portalında gizlenir. İçinde `Resource group` görünümü, onay `Show hidden types` kaynak grubunu görüntülemek için.

![Gizli türü onay kutusunun ekran görüntüsü](./media/aro-portal-hidden-type.png)

Kaynak grubunun silinmesi, tüm Azure Red Hat OpenShift küme oluşturma sırasında oluşturulan ilgili kaynakları siler.

## <a name="next-steps"></a>Sonraki adımlar

Öğreticinin bu bölümünde, şunların nasıl yapıldığını öğrendiniz:
> [!div class="checklist"]
> * Azure Red Hat OpenShift küme silme

OpenShift ile resmi kullanma hakkında daha fazla bilgi edinin [Red Hat OpenShift belgeleri](https://docs.openshift.com/aro/welcome/index.html)