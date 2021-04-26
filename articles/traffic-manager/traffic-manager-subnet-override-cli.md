---
title: Azure CLı kullanarak Azure Traffic Manager alt ağı geçersiz kılma | Microsoft Docs
description: Bu makale, uç nokta eşlemelerine önceden tanımlanmış IP aralığı aracılığıyla Son Kullanıcı IP adresini temel alarak trafiği bir uç noktaya yönlendirmek üzere bir Traffic Manager profilinin yönlendirme yöntemini geçersiz kılmak için Traffic Manager alt ağ geçersiz kılmanın nasıl kullanılabileceğini anlamanıza yardımcı olur.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: fab167884d9060edc4f626d3ee05fa0b23389d92
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767808"
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

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.28 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Traffic Manager uç noktasını alt ağ geçersiz kılma ile güncelleştirin.
[Az Network Traffic-Manager Endpoint Update](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_update)ile uç noktanızı güncelleştirmek IÇIN Azure CLI 'yi kullanın.

```azurecli-interactive
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

IP adresi aralıklarını, [az Network Traffic-Manager uç noktası güncelleştirmesini](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_update) **--Remove** seçeneğiyle çalıştırarak kaldırabilirsiniz.

```azurecli-interactive
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints
```

## <a name="next-steps"></a>Sonraki Adımlar

Traffic Manager [trafik yönlendirme yöntemleri](traffic-manager-routing-methods.md)hakkında daha fazla bilgi edinin.

[Alt ağ trafiği-yönlendirme yöntemi](./traffic-manager-routing-methods.md#subnet-traffic-routing-method) hakkında bilgi edinin