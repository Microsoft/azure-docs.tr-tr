---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: f222d4a7f4724506112a47eff61ccc48354dd622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028264"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>Yerel ağ geçidinin IP adresi ön eklerini değiştirmek için - ağ geçidi bağlantısı yok

Ağ geçidi bağlantınız yoksa ve IP adresi önekleri eklemek veya kaldırmak istiyorsanız, yerel ağ geçidini oluşturmak için kullandığınız [az network local-gateway create](/cli/azure/network/local-gateway) komutunu kullanın. VPN cihazının ağ geçidi IP adresini güncelleştirmek için de bu komutu kullanabilirsiniz. Geçerli ayarların üzerine yazmak için yerel ağ geçidinizin mevcut adını kullanın. Farklı bir ad kullanırsanız mevcut olanın üzerine yazmak yerine yeni bir yerel ağ geçidi oluşturursunuz.

Her değişiklik yaptığınızda, yalnızca değiştirmek istediğiniz ön ekler değil ön ek listesinin tamamı belirtilmelidir. Yalnızca kalmasını istediğiniz ön ekleri belirtin. Bu durumda, söz konusu ön ekler 10.0.0.0/24 ve 20.0.0.0/24’tür.

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>Yerel ağ geçidinin IP adresi ön eklerini değiştirmek için - ağ geçidi bağlantısı var

Bir ağ geçidi bağlantınız varsa ve IP adresi önekleri eklemek veya kaldırmak istiyorsanız önekleri [az network local-gateway update](/cli/azure/network/local-gateway) komutunu kullanarak güncelleştirebilirsiniz. Bunun sonucunda, VPN bağlantınızda kesinti oluşur. IP adresi öneklerini değiştirirken, VPN ağ geçidini silmeniz gerekmez.

Her değişiklik yaptığınızda, yalnızca değiştirmek istediğiniz ön ekler değil ön ek listesinin tamamı belirtilmelidir. Bu örnekte, 10.0.0.0/24 ve 20.0.0.0/24 zaten mevcuttur. 30.0.0.0/24 ve 40.0.0.0/24 öneklerini ekliyor ve güncelleştirirken 4 öneki de belirtiyoruz.

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 -g TestRG1
```