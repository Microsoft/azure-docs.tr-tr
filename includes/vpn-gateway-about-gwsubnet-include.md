---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5a734963ced9daefda2b7b6f4a52fd9ef437eddc
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269273"
---
Sanal ağ geçidi, ağ geçidi alt ağı adlı belirli bir alt ağ kullanır. Ağ geçidi alt ağı, sanal ağınızı yapılandırırken belirttiğiniz sanal ağ IP adresi aralığının bir parçasıdır. Sanal ağ geçidi kaynaklarının ve hizmetlerinin kullandığı IP adreslerini içerir. Azure’ın ağ geçidi kaynaklarını dağıtması için alt ağ, 'GatewaySubnet' olarak adlandırılmalıdır. Ağ geçidi kaynaklarının dağıtılacağı farklı bir alt ağ belirtemezsiniz. 'GatewaySubnet' adlı bir alt ağınız yoksa, kendi VPN ağ geçidinizi oluştururken bu başarısız olur.

Ağ geçidi alt ağı oluştururken, alt ağın içerdiği IP adresi sayısını belirtirsiniz. Gerekli IP adresi sayısı, oluşturmak istediğiniz VPN ağ geçidi yapılandırmasına bağlıdır. Bazı yapılandırmalar için diğerlerinden daha fazla IP adresi gerekir. /27 veya /28 kullanan bir ağ geçidi alt ağı oluşturmanızı öneririz.

Adres alanının bir alt ağ ile çakıştığını veya alt ağın sanal ağınız için adres alanında bulunmadığını belirten bir hata görürseniz VNet adres aralığınızı denetleyin. Sanal ağınız için oluşturduğunuz adres aralığında yeterli IP adresi kullanılabilir olmayabilir. Örneğin, varsayılan alt ağınız tüm adres aralığını kapsıyorsa, ek alt ağlar oluşturmak için bir IP adresi kalmamıştır. IP adreslerini serbest bırakmak için mevcut adres alanında alt ağlarınızı ayarlayabilir veya ek bir adres aralığı belirtip orada ağ geçidi alt ağını oluşturabilirsiniz.