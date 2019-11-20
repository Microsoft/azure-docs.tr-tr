---
title: Azure CLı kullanarak Azure Traffic Manager alt ağı geçersiz kılma | Microsoft Docs
description: Bu makale, uç nokta eşlemelerine önceden tanımlanmış IP aralığı aracılığıyla Son Kullanıcı IP adresini temel alarak trafiği bir uç noktaya yönlendirmek üzere bir Traffic Manager profilinin yönlendirme yöntemini geçersiz kılmak için Traffic Manager alt ağ geçersiz kılmanın nasıl kullanılabileceğini anlamanıza yardımcı olur.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: d285db22ef92128e025a677ea7f86d623dfe130c
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351170"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Azure CLı kullanarak alt ağ geçersiz kılma Traffic Manager

Traffic Manager alt ağ geçersiz kılma, bir profilin yönlendirme yöntemini değiştirmenize izin verir.  Bir geçersiz kılma eklenmesi, son kullanıcının IP adresine göre trafiği uç nokta eşleme için önceden tanımlanmış bir IP aralığına göre yönlendirdirecektir. 

## <a name="how-subnet-override-works"></a>Alt ağ geçersiz kılma çalışması

Bir Traffic Manager profiline alt ağ geçersiz kılmaları eklendiğinde, Traffic Manager önce son kullanıcının IP adresi için bir alt ağ geçersiz kılma olup olmadığını kontrol eder. Bir tane bulunursa, kullanıcının DNS sorgusu ilgili uç noktaya yönlendirilir.  Bir eşleme bulunamazsa, Traffic Manager profilin özgün yönlendirme yöntemine geri dönecektir. 

IP adresi aralıkları CıDR aralıkları (örneğin, 1.2.3.0/24) veya adres aralıkları (örneğin, 1.2.3.4-5.6.7.8) olarak belirtilebilir. Her bitiş noktasıyla ilişkili IP aralıkları bu uç nokta için benzersiz olmalıdır. Farklı uç noktalar arasındaki IP aralıklarının çakışması, profilin Traffic Manager tarafından reddedilmesine neden olur.

Alt ağ geçersiz kılmalarını destekleyen iki tür yönlendirme profili vardır:

* **Coğrafi** -TRAFFIC Manager, DNS sorgusunun IP adresi için bir alt ağ geçersiz kılma işlemi bulursa, uç noktanın sistem durumunun ne olduğunu her ne olursa sorguyu uç noktaya yönlendirir.
* **Performans** -Traffic Manager DNS sorgusunun IP adresi için bir alt ağ geçersiz kılma bulduğunda, trafiği yalnızca sağlıklı olması durumunda uç noktaya yönlendirir.  Alt ağ geçersiz kılma uç noktasının sağlıklı olmaması durumunda Traffic Manager, performans yönlendirmesi buluşsal noktasına geri dönecektir.

## <a name="create-a-traffic-manager-subnet-override"></a>Traffic Manager alt ağ geçersiz kılma oluşturma

Bir Traffic Manager alt ağ geçersiz kılma oluşturmak için Azure CLı kullanarak, geçersiz kılma için alt ağları Traffic Manager uç noktasına ekleyebilirsiniz.

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.28 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Traffic Manager uç noktasını alt ağ geçersiz kılma ile güncelleştirin.
[Az Network Traffic-Manager Endpoint Update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update)ile uç noktanızı güncelleştirmek IÇIN Azure CLI 'yi kullanın.

```azurecli

### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints

```

IP adresi aralıklarını, [az Network Traffic-Manager uç noktası güncelleştirmesini](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) **--Remove** seçeneğiyle çalıştırarak kaldırabilirsiniz.

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>Sonraki Adımlar
Traffic Manager [trafik yönlendirme yöntemleri](traffic-manager-routing-methods.md)hakkında daha fazla bilgi edinin.

[Alt ağ trafiği-yönlendirme yöntemi](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method) hakkında bilgi edinin