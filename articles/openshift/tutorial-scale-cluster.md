---
title: Öğretici - Azure Red Hat OpenShift kümesini ölçeklendirme | Microsoft Docs
description: Azure CLI kullanarak bir Microsoft Azure Red Hat OpenShift kümesini ölçeklendirme hakkında bilgi edinin
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: deb136a70c24cb7bd1b6c60505cc6ab0376a7b02
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672431"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Öğretici: Azure Red Hat OpenShift kümesini ölçeklendirme

Bu öğretici, bir dizinin ikinci bölümüdür. Azure CLI kullanarak bir Microsoft Azure Red Hat OpenShift kümesi oluşturma, ölçeklemek ve kaynakları temizlemek için silin öğreneceksiniz.

Serinin ikinci bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * Red Hat OpenShift kümesini ölçeklendirme

Bu öğretici dizisinde şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * [Azure Red Hat OpenShift kümesi oluşturma](tutorial-create-cluster.md)
> * Azure Red Hat OpenShift kümesini ölçeklendirme
> * [Azure Red Hat OpenShift kümesini silme](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* Bir küme oluşturma [Azure Red Hat OpenShift küme oluşturma](tutorial-create-cluster.md) öğretici.

## <a name="step-1-sign-in-to-azure"></a>1\. adım: Azure'da oturum açma

Azure CLI'yi yerel olarak çalıştırıyorsanız, çalıştırma `az login` için Azure'da oturum açın.

```bash
az login
```

Birden çok aboneliğe erişiminiz çalıştırırsanız `az account set -s {subscription ID}` değiştirerek `{subscription ID}` kullanmak istediğiniz aboneliği ile.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>2\. adım: Ek düğümler ile küme ölçeklendirme

Terminal bir Bash değişken küme_adı kümenizin adını ayarlayın:

```bash
CLUSTER_NAME=yourclustername
```

Şimdi şimdi Azure CLI kullanarak beş düğüm Küme ölçeklendirme:

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Birkaç dakika sonra `az openshift scale` başarıyla tamamlanabilmesi ve ölçeklendirilmiş kümeniz ayrıntılarını içeren bir JSON belgesini döndürür.

## <a name="next-steps"></a>Sonraki adımlar

Öğreticinin bu bölümünde, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure Red Hat OpenShift kümesini ölçeklendirme

Sonraki öğreticiye ilerleyin:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift kümesini silme](tutorial-delete-cluster.md)