---
title: 'Hızlı başlangıç: Azure CLı ile Azure ayrılmış HSM oluşturma'
description: Azure CLı kullanarak Azure adanmış HSM 'ler oluşturun, görüntüleyin, listeleyin, güncelleştirin ve silin.
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurecli
ms.date: 01/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 80d5bbb54715c5a1a5102f8991f366e273145edc
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868960"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-by-using-the-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak Azure ayrılmış HSM oluşturma

Bu makalede, [az adanmış HSM](/cli/azure/dedicated-hsm) Azure CLI uzantısı kullanılarak Azure ADANMıŞ bir HSM 'nin nasıl oluşturulacağı ve yönetileceği açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bir [hesabınız yoksa ücretsiz bir hesap oluşturabilirsiniz](https://azure.microsoft.com/free/) .
  
  Birden fazla Azure aboneliğiniz varsa, Azure CLı [az Account set](/cli/azure/account#az_account_set) komutuyla faturalandırma için kullanılacak aboneliği ayarlayın.
  
  ```azurecli-interactive
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]  
  
- Kayıt, onay ve bir sanal ağ ve sağlama için kullanılacak sanal makine dahil olmak üzere, adanmış bir HSM 'nin tüm gereksinimleri karşılandı. Adanmış HSM gereksinimleri ve önkoşulları hakkında daha fazla bilgi için bkz. [öğretici: Azure CLI kullanarak mevcut bir sanal ağa HSM dağıtma](tutorial-deploy-hsm-cli.md).
  

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[Azure Kaynak grubu](../azure-resource-manager/management/overview.md) , Azure kaynaklarını bir grup olarak dağıtmak ve yönetmek için bir mantıksal kapsayıcıdır. Ayrılmış HSM için henüz bir kaynak grubunuz yoksa, az [Group Create](/cli/azure/group#az_group_create) komutunu kullanarak bir tane oluşturun. Aşağıdaki örnek, Azure bölgesinde adlı bir kaynak grubu oluşturur `myRG` `westus` :

```azurecli-interactive
az group create --name myRG --location westus
```

## <a name="create-a-dedicated-hsm"></a>Adanmış bir HSM oluşturma

Adanmış bir HSM oluşturmak için [az adanmış-HSM Create](/cli/azure/dedicated-hsm#az_dedicated_hsm_create) komutunu kullanın. Aşağıdaki örnek, `hsm1` `westus` bölge, `myRG` kaynak grubu ve belirtilen abonelik, sanal ağ ve alt ağ içinde ADLı özel bir HSM sağlar. Gerekli parametreler `name` , ve ' dir `location` `resource group` .

```azurecli-interactive
az dedicated-hsm create \
   --resource-group myRG \
   --name "hsm1" \
   --location "westus" \
   --network-profile-network-interfaces private-ip-address="1.0.0.1" \
   --subnet id="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/hsm-group/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet" \
   --stamp-id "stamp1" \
   --sku name="SafeNet Luna Network HSM A790" \
   --tags resourceType="hsm" Environment="test" \
   --zones "AZ1"
```

Dağıtımın tamamlanabilmesi yaklaşık 25 ila 30 dakika sürer.

## <a name="get-a-dedicated-hsm"></a>Adanmış bir HSM al

Geçerli bir ayrılmış HSM 'yi almak için [az adanmış-HSM Show](/cli/azure/dedicated-hsm#az_dedicated_hsm_show) komutunu çalıştırın. Aşağıdaki örnek, `hsm1` kaynak grubundaki özel HSM 'yi alır `myRG` .

```azurecli-interactive
az dedicated-hsm show --resource-group myRG --name hsm1
```

## <a name="update-a-dedicated-hsm"></a>Adanmış bir HSM 'yi güncelleştirme

Ayrılmış bir HSM 'yi güncelleştirmek için [az adanmış-HSM Update](/cli/azure/dedicated-hsm#az_dedicated_hsm_update) komutunu kullanın. Aşağıdaki örnek, `hsm1` `myRG` kaynak grubunda ayrılmış HSM 'yi ve onun etiketlerini güncelleştirir:

```azurecli-interactive
az dedicated-hsm update --resource-group myRG –-name hsm1 --tags resourceType="hsm" Environment="prod" Slice="A"
```

## <a name="list-dedicated-hsms"></a>Ayrılmış HSM 'leri listeleme

Geçerli adanmış HSM 'ler hakkında bilgi almak için [az adanmış-HSM List](/cli/azure/dedicated-hsm#az_dedicated_hsm_list) komutunu çalıştırın. Aşağıdaki örnek, kaynak grubunda adanmış HSM 'leri listeler `myRG` :

```azurecli-interactive
az dedicated-hsm list --resource-group myRG
```

## <a name="remove-a-dedicated-hsm"></a>Adanmış bir HSM 'yi kaldırma

Adanmış bir HSM 'yi kaldırmak için [az adanmış-HSM Delete](/cli/azure/dedicated-hsm#az_dedicated_hsm_delete) komutunu kullanın. Aşağıdaki örnek, `hsm1` kaynak grubundan ADANMıŞ HSM 'yi siler `myRG` :

```azurecli-interactive
az dedicated-hsm delete --resource-group myRG –-name hsm1
```

## <a name="delete-the-resource-group"></a>Kaynak grubunu silme

Ayrılmış HSM için oluşturduğunuz kaynak grubuna artık ihtiyacınız yoksa, [az Group Delete](/cli/azure/group#az_group_delete) komutunu çalıştırarak bunu silebilirsiniz. Bu komut, özel HSM ile ilgisiz olan her türlü grubu ve içindeki tüm kaynakları siler. Aşağıdaki örnek, `myRG` kaynak grubunu ve içindeki her şeyi siler:

```azurecli-interactive
az group delete --name myRG
```

## <a name="next-steps"></a>Sonraki adımlar

Azure ayrılmış HSM hakkında daha fazla bilgi için bkz. [Azure ayrılmış HSM](overview.md).
