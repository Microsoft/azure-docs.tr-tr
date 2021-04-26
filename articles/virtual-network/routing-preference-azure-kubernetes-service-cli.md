---
title: Azure CLı kullanarak bir Azure Kubernetes hizmeti için yönlendirme tercihini yapılandırma
titlesuffix: Azure Virtual Network
description: Azure CLı kullanarak bir AKS kümesini yönlendirme tercihiyle yapılandırmayı öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 9eaad12e254150109498be0fac2f285f33a5965c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776618"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Azure CLı kullanarak bir Kubernetes kümesi için yönlendirme tercihini yapılandırma

Bu makalede, Azure CLı kullanarak bir Kubernetes kümesi için ISS ağı (**Internet** seçeneği) aracılığıyla yönlendirme tercihini yapılandırma gösterilmektedir. Yönlendirme tercihi, yönlendirme tercihi türü * * Internet * * * * genel IP adresi oluşturularak ve ardından AKS kümesi oluşturulurken kullanılarak ayarlanır.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.49 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
[az group create](/cli/azure/group#az_group_create) komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek, **Doğu ABD** Azure bölgesinde bir kaynak grubu oluşturur:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma

[Az Network public-IP Create](/cli/azure/network/public-ip#az_network_public_ip_create)komutunu kullanarak **Internet** türü yönlendirme tercıhı Ile genel bir IP adresi oluşturun.

Aşağıdaki komut, **Doğu ABD** Azure bölgesinde **Internet** yönlendirme tercihi Ile yeni bir genel IP oluşturur.

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```
> [!NOTE]
>  Şu anda yönlendirme tercihi yalnızca ıPV4 genel IP adreslerini destekler.

## <a name="get-the-id-of-public-ip-address"></a>Genel IP adresi KIMLIĞINI al

Aşağıdaki komut, önceki bölümde oluşturulan genel IP adresi KIMLIĞINI döndürür:
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>Ortak IP ile Kubernetes kümesi oluşturma

Aşağıdaki komut, önceki bölümde oluşturulan genel IP ile AKS kümesini oluşturur:

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>AKS kümesinin dağıtılması birkaç dakika sürer.

Doğrulamak için Azure portal önceki adımda oluşturulan genel IP 'yi arayın, IP 'nin aşağıda gösterildiği gibi Kubernetes kümesiyle ilişkili yük dengeleyiciyle ilişkilendirildiğini görürsünüz:

 ![Kubernetes için yönlendirme tercihi genel IP 'si](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>Sonraki adımlar

- [Genel IP adreslerinde yönlendirme tercihi](routing-preference-overview.md)hakkında daha fazla bilgi edinin. 
- [Azure CLI kullanarak BIR VM için yönlendirme tercihini yapılandırın](configure-routing-preference-virtual-machine-cli.md).
