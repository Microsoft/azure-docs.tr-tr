---
title: Azure CLı kullanarak Azure DDoS koruma planı oluşturma ve yapılandırma
description: Azure CLı kullanarak DDoS koruma planı oluşturma hakkında bilgi edinin
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 8a8da50dc703d59dc16b5cb6253d39aeb33fd76d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777662"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak Azure DDoS koruma standardı oluşturma ve yapılandırma

Azure CLı 'yı kullanarak Azure DDoS koruma standardı ile çalışmaya başlayın. 

DDoS koruma planı, aboneliklerde DDoS koruma standardı etkin olan bir sanal ağ kümesini tanımlar. Kuruluşunuz için bir DDoS koruma planı yapılandırabilir ve sanal ağları birden çok abonelikten aynı plana bağlayabilirsiniz. 

Bu hızlı başlangıçta bir DDoS koruma planı oluşturacak ve bunu bir sanal ağa bağlayacaksınız. 

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure CLı yerel olarak veya Azure Cloud Shell yüklendi

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu hızlı başlangıç, Azure CLı sürüm 2.0.28 veya üstünü gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli).

## <a name="create-a-ddos-protection-plan"></a>DDoS koruma planı oluşturma

Azure 'da, ilgili kaynakları bir kaynak grubuna ayırabilirsiniz. Var olan bir kaynak grubunu kullanabilir ya da yeni bir tane oluşturabilirsiniz.

Bir kaynak grubu oluşturmak için [az Group Create](/cli/azure/group#az_group_create)kullanın. Bu örnekte kaynak grubumuzu _Myresourcegroup_ olarak adlandırın ve _Doğu ABD_ konumunu kullanın:

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus
```

Şimdi _Myddosprotectionplan_ adlı bir DDoS koruma planı oluşturun:

```azurecli-interactive
az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Sanal ağ için DDoS korumasını etkinleştirme

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Yeni bir sanal ağ için DDoS korumasını etkinleştir

Bir sanal ağ oluştururken DDoS korumasını etkinleştirebilirsiniz. Bu örnekte sanal ağı _Myvnet_ olarak adlandırın: 

```azurecli-interactive
az network vnet create \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --location eastus \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

Sanal ağ için DDoS standardı etkinleştirildiğinde bir sanal ağı başka bir kaynak grubuna veya aboneliğe taşıyamazsınız. DDoS standart özellikli bir sanal ağı taşımanız gerekiyorsa, önce DDoS standardını devre dışı bırakın, sanal ağı taşıyın ve ardından DDoS standardını etkinleştirin. Taşıma işleminden sonra, sanal ağ içindeki tüm korumalı genel IP adresleri için otomatik olarak ayarlanan ilke eşikleri sıfırlanır.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Mevcut bir sanal ağ için DDoS korumasını etkinleştir

[DDoS koruma planı oluştururken](#create-a-ddos-protection-plan), bir veya daha fazla sanal ağı plana ilişkilendirebilirsiniz. Birden fazla sanal ağ eklemek için, adları veya kimlikleri, boşlukla ayrılmış olarak listeleriz. Bu örnekte, _Myvnet_ ekleyeceğiz:

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
    --vnets MyVnet
```

Alternatif olarak, belirli bir sanal ağ için DDoS korumasını etkinleştirebilirsiniz:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

## <a name="validate-and-test"></a>Doğrula ve test et

İlk olarak, DDoS koruma planınızın ayrıntılarını kontrol edin:

```azurecli-interactive
az network ddos-protection show \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

Komutun DDoS koruma planınızın doğru ayrıntılarını döndürdüğünden emin olun.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynaklarınızın bir sonraki öğreticide kalmasını sağlayabilirsiniz. Artık gerekmiyorsa, _Myresourcegroup_ kaynak grubunu silin. Kaynak grubunu sildiğinizde, DDoS koruma planını ve ilgili tüm kaynakları da silersiniz. 

Kaynak grubunu silmek için [az Group Delete](/cli/azure/group#az_group_delete)kullanın:

```azurecli-interactive
az group delete \
--name MyResourceGroup 
```

DDoS korumasını devre dışı bırakmak için verilen bir sanal ağı güncelleştirin:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection false
    --ddos-protection-plan ""
```

DDoS koruma planını silmek istiyorsanız, önce tüm sanal ağların ilişkilendirmesini kaldırmanız gerekir. 

## <a name="next-steps"></a>Sonraki adımlar

DDoS koruma planınız için telemetri görüntüleme ve yapılandırma hakkında bilgi edinmek için öğreticilere devam edin.

> [!div class="nextstepaction"]
> [DDoS konuma telemetrisini görüntüleme ve yapılandırma](telemetry.md)
