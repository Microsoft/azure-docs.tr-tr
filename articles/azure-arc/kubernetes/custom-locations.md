---
title: Azure Arc etkin Kubernetes 'de özel konumlar
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions
description: Azure Arc etkin Kubernetes kümelerinde Azure PaaS hizmetlerini dağıtmak için özel konumlar kullanma
ms.openlocfilehash: ddda6420acd7126cb46b043f5c1bce67758342bc
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451206"
---
# <a name="custom-locations-on-azure-arc-enabled-kubernetes"></a>Azure Arc etkin Kubernetes 'de özel konumlar

Azure konum uzantısı olarak *özel konumlar* , kiracı yöneticilerinin Azure 'un etkin Kubernetes kümelerini Azure hizmetleri örneklerinin dağıtımına yönelik hedef konumlar olarak kullanması için bir yol sağlar. Azure Kaynak örnekleri, Azure Arc etkin SQL yönetilen örneği ve Azure Arc etkin PostgreSQL hiper ölçeğini içerir.

Azure konumlarına benzer şekilde, kiracının özel konumlarına erişimi olan son kullanıcılar, şirketinin özel işlem kullanımını kullanarak kaynakları dağıtabilir.

Özel konumlarda bu özelliğe kavramsal bir genel bakış sunulmaktadır [-Azure Arc etkin Kubernetes](conceptual-custom-locations.md) makalesi.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Önkoşullar

- Azure CLı 'yı >= 2.16.0 sürümüne [yükler veya yükseltin](https://docs.microsoft.com/cli/azure/install-azure-cli) .

- `connectedk8s` (sürüm >= 1.1.0), `k8s-extension` (sürüm >= 0.2.0) ve `customlocation` (sürüm >= 0.1.0) Azure CLI uzantıları. Aşağıdaki komutları çalıştırarak bu Azure CLı uzantılarını yüklemelisiniz:
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    az extension add --name customlocation
    ```
    
    `connectedk8s`, `k8s-extension` Ve `customlocation` uzantıları zaten yüklüyse, aşağıdaki komutu kullanarak bunları en son sürüme güncelleştirebilirsiniz:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    az extension update --name customlocation
    ```

- İçin sağlayıcı kaydı tamamlanmıştır `Microsoft.ExtendedLocation` .
    1. Aşağıdaki komutları girin:
    
    ```azurecli
    az provider register --namespace Microsoft.ExtendedLocation
    ```

    2. Kayıt işlemini izleyin. Kayıt, en fazla 10 dakika sürebilir.
    
    ```azurecli
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

>[!NOTE]
>**Özel konumlar için desteklenen bölgeler:**
>* Doğu ABD
>* West Europe

## <a name="enable-custom-locations-on-cluster"></a>Kümede özel konumları etkinleştir

Bu özelliği kümenizde etkinleştirmek için aşağıdaki komutu yürütün:

```console
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features cluster-connect custom-locations
```

> [!NOTE]
> 1. Özel konumlar özelliği, küme bağlantısı özelliğine bağımlıdır. Bu nedenle, özel konumların çalışması için her iki özelliğin de etkinleştirilmesi gerekir.
> 2. `az connectedk8s enable-features` , `kubeconfig` dosyanın etkinleştirilecek kümeyi işaret eden bir makinede çalıştırılması gerekir ().

## <a name="create-custom-location"></a>Özel konum oluştur

1. Azure Arc etkin bir Kubernetes kümesi oluşturun.
    - Henüz bir kümeye bağlanmadıysanız [hızlı](quickstart-connect-cluster.md)başlangıçlarımızı kullanın.
    - [Aracılarınızı sürüme yükseltin](agent-upgrade.md#manually-upgrade-agents) >= 1.1.0.

1. Örneğini son olarak özel konumun üzerine istediğiniz Azure hizmetinin küme uzantısını dağıtın:

    ```azurecli
    az k8s-extension create --name <extensionInstanceName> --extension-type microsoft.arcdataservices --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName> --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper
    ```

    > [!NOTE]
    > Temel kimlik doğrulaması ile kimlik doğrulaması ve giden ara sunucu olmayan giden ara sunucu, yay etkin veri Hizmetleri kümesi uzantısı tarafından desteklenir. Güvenilen sertifikaları bekleyen giden ara sunucu şu anda desteklenmiyor.

1. Sonraki adımlarda başvurulan Azure Arc etkinleştirilmiş Kubernetes kümesinin Azure Resource Manager tanımlayıcısını alın `connectedClusterId` :

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Daha sonraki adımlarda başvurulan Azure Arc etkin Kubernetes kümesi üzerinde dağıtılan küme uzantısının Azure Resource Manager tanımlayıcısını alın `extensionId` :

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Azure Arc etkin Kubernetes kümesine ve uzantısına başvurarak özel konum oluşturun:

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace arc --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionId>
    ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Küme bağlantısı](cluster-connect.md) kullanarak kümeye güvenli bir şekilde bağlanma