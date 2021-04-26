---
title: VNet TAP oluşturma, değiştirme veya silme-Azure CLı
description: Azure CLı kullanarak bir sanal ağ oluşturma, değiştirme veya silme hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kaanan
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e3a56e4a6eb1fb6eb633021178ef78f8ac7287d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014797"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Azure CLı kullanarak bir sanal ağ ile çalışma

Azure sanal ağ TAP (Terminal erişim noktası), sanal makine ağ trafiğinizi bir ağ paketi toplayıcısına veya analiz aracına sürekli olarak akışla kullanmanıza olanak sağlar. Toplayıcı veya Analiz Aracı bir [ağ sanal gereç](https://azure.microsoft.com/solutions/network-appliances/) ortağı tarafından sağlanır. Sanal ağ dokunarak çalışmak üzere doğrulanan iş ortağı çözümlerinin listesi için bkz. [iş ortağı çözümleri](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions). 

## <a name="create-a-virtual-network-tap-resource"></a>Sanal ağ dokunma kaynağı oluşturma

Sanal ağ TAP kaynağı oluşturmadan önce [önkoşulları](virtual-network-tap-overview.md#prerequisites) okuyun. [Azure Cloud Shell](https://shell.azure.com/bash), veya bilgisayarınızdan Azure komut satırı arabirimi 'NI (CLI) çalıştırarak bu komutları çalıştırabilirsiniz. Azure Cloud Shell, bilgisayarınızda Azure CLı yüklemesi gerektirmeyen ücretsiz bir etkileşimli kabuktur. Azure 'da uygun [izinlere](virtual-network-tap-overview.md#permissions)sahip bir hesapla oturum açmalısınız. Bu makale, Azure CLı sürüm 2.0.46 veya üstünü gerektirir. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI 2.0 yükleme](/cli/azure/install-azure-cli). Sanal ağ dokunma Şu anda bir uzantı olarak kullanılabilir. Çalıştırmanız gereken uzantıyı yüklemek için `az extension add -n virtual-network-tap` . Azure CLı 'yi yerel olarak çalıştırıyorsanız, `az login` Azure ile bir bağlantı oluşturmak için çalıştırmanız da gerekir.

1. Aboneliğinizin KIMLIĞINI sonraki bir adımda kullanılan bir değişkene alın:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Sanal ağ dokunma kaynağı oluşturmak için kullanacağınız abonelik kimliğini ayarlayın.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Sanal ağ dokunma kaynağı oluşturmak için kullanacağınız abonelik KIMLIĞINI yeniden kaydedin. Bir dokunma kaynağı oluştururken bir kayıt hatası alırsanız, aşağıdaki komutu çalıştırın:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Sanal ağ ' a yönelik hedef, toplayıcı veya Analiz Aracı için ağ sanal gereci 'nda bulunan ağ arabirimidir.

   - Ağ sanal gerecinin ağ arabiriminin IP yapılandırmasını sonraki bir adımda kullanılan bir değişkene alın. KIMLIK, dokunma trafiğini toplayacak olan bitiş noktasıdır. Aşağıdaki örnek, *Myresourcegroup* adlı bir kaynak grubunda *mynetworkınterface* adlı bir ağ arabirimi IÇIN *ipconfig1* IP yapılandırmasının kimliğini alır:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Hedef olarak IP yapılandırmasının KIMLIĞINI ve isteğe bağlı bir bağlantı noktası özelliğini kullanarak westcentralus Azure bölgesinde sanal ağ ' a dokunun. Bağlantı noktası, dokunma trafiğinin alındığı ağ arabirimi IP yapılandırmasındaki hedef bağlantı noktasını belirtir:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Sanal ağın hedefi bir Azure iç yük dengeleyiciye ise:
  
   - Azure iç yük dengeleyicinin ön uç IP yapılandırmasını sonraki bir adımda kullanılan bir değişkene alın. KIMLIK, dokunma trafiğini toplayacak olan bitiş noktasıdır. Aşağıdaki örnek, *Myresourcegroup* adlı bir kaynak grubunda *myınternalloadbalancer* adlı bir yük dengeleyici IÇIN *FRONTENDIPCONFIG1* ön uç IP yapılandırmasının kimliğini alır:

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```

   - Hedef ve isteğe bağlı bir bağlantı noktası özelliği olan ön uç IP yapılandırmasının KIMLIĞINI kullanarak sanal ağ ' a dokunun. Bağlantı noktası, dokunma trafiğinin alındığı ön uç IP yapılandırmasındaki hedef bağlantı noktasını belirtir:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Sanal ağ ' ın oluşturulmasını onaylayın:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Bir ağ arabirimine bir dokunma yapılandırması ekleme

1. Mevcut bir sanal ağın KIMLIĞINI alın kaynak ' a dokunun. Aşağıdaki örnek, *Myresourcegroup* adlı kaynak grubunda *mytap* ADLı bir sanal ağ tap 'ı alır:

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. İzlenen sanal makinenin ağ arabiriminde bir dokunma yapılandırması oluşturun. Aşağıdaki örnek, *Mynetworkınterface* adlı bir ağ arabirimi IÇIN bir dokunma yapılandırması oluşturur:

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. DOKUNMA yapılandırması oluşturmayı onaylayın:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>Ağ arabirimindeki dokunma yapılandırmasını silme

   ```azurecli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Bir abonelikte sanal ağ dokunmalar listeleme

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Bir kaynak grubunda bir sanal ağ DOKUNMAYı silme

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
