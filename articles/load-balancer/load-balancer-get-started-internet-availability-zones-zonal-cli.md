---
title: Bölgesel ön ucu ile yük dengeleyici oluşturma-Azure CLI
titleSuffix: Azure Load Balancer
description: Azure CLI kullanarak bölgesel ön ucu ile standart Load Balancer oluşturma hakkında bilgi edinin
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 620e4456e86a8610819495bd490353a3a5681738
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076080"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-cli"></a>Azure CLI kullanarak bölgesel ön ucu ile standart Load Balancer oluşturma

Bu makalede, bölgesel ön ucu ile genel [Standart Load Balancer](https://aka.ms/azureloadbalancerstandard) oluşturma adımları sağlanır. Bir ZGen ön ucu olması, gelen veya giden akışın bir bölgedeki tek bir bölge tarafından hizmet verdiği anlamına gelir. Ön uç yapılandırmasında bölgesel standart genel IP adresini kullanarak, bölgesel ön ucu ile yük dengeleyici oluşturabilirsiniz. Kullanılabilirlik alanları standart Load Balancer ile nasıl çalıştığını anlamak için bkz: [Standard Load Balancer ve kullanılabilirlik bölgeleri](load-balancer-standard-availability-zones.md). 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz, en son yüklediğinizden emin olun [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ve bir Azure hesabı ile oturum açtığınız [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest).

> [!NOTE]
> Kullanılabilirlik bölgeleri, seçili Azure kaynakları ve bölgeler ve sanal makine boyutu aileleri için kullanılabilir. Kullanmaya başlamak nasıl daha fazla bilgi ve hangi Azure kaynakları, bölgeleri ve kullanılabilirlik alanları ile deneyebilirsiniz sanal makine boyutu aileleri için bkz. [kullanılabilirlik alanlarına genel bakış](https://docs.microsoft.com/azure/availability-zones/az-overview). Destek için [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) üzerinden bize ulaşabilir veya [bir Azure destek bileti açabilirsiniz](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Aşağıdaki komutu kullanarak bir kaynak grubu oluşturun:

```azurecli-interactive
az group create --name myResourceGroupZLB --location westeurope
```

## <a name="create-a-public-standard-ip-address"></a>Genel Standart IP adresi oluşturma

Aşağıdaki komutu kullanarak bir bölgesel standart genel IP adresi oluşturun:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupZLB --name myPublicIPZonal --sku Standard --zone 1
```

## <a name="create-a-load-balancer"></a>Yük dengeleyici oluşturma

Aşağıdaki komutu kullanarak önceki adımda oluşturduğunuz standart genel IP ile genel bir Standart Load Balancer oluşturun:

```azurecli-interactive
az network lb create --resource-group myResourceGroupZLB --name myLoadBalancer --public-ip-address myPublicIPZonal --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Bağlantı noktası 80 üzerinde bir LB araştırma oluşturma

Aşağıdaki komutu kullanarak bir yük dengeleyici durum araştırması oluşturun:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupZLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Bağlantı noktası 80 için bir LB kuralı oluşturma

Aşağıdaki komutu kullanarak bir yük dengeleyici kuralı oluşturun:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroupZLB --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Sonraki adımlar
- Daha fazla bilgi edinin [Standard Load Balancer ve kullanılabilirlik bölgeleri](load-balancer-standard-availability-zones.md).



