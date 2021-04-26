---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4e958026b1167d65f47bddbe5e89ec4d6fed7ee3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92217987"
---
[az network vpn-connection show](/cli/azure/network/vpn-connection) komutunu kullanarak bağlantınızın başarılı olup olmadığını doğrulayabilirsiniz. Örnekte bulunan "-name", test etmek istediğiniz bağlantının adıdır. Bağlantı henüz kurulma aşamasındayken, bağlantı durumu "Bağlanıyor" olarak gösterilir. Bağlantı kurulduktan sonra durum "Bağlandı" olarak değişir.

```azurecli-interactive
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
