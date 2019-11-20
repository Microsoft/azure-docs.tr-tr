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
ms.openlocfilehash: 92a6fef3305891b47c4dc2e0f0432e1079df5a69
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71266534"
---
Sanal ağ geçidi, ağ geçidi alt ağı adlı belirli bir alt ağ kullanır. Ağ geçidi alt ağı, sanal ağınızı yapılandırırken belirttiğiniz sanal ağ IP adresi aralığının bir parçasıdır. Sanal ağ geçidi kaynaklarının ve hizmetlerinin kullandığı IP adreslerini içerir. 


Ağ geçidi alt ağı oluştururken, alt ağın içerdiği IP adresi sayısını belirtirsiniz. Gerekli IP adresi sayısı, oluşturmak istediğiniz VPN ağ geçidi yapılandırmasına bağlıdır. Bazı yapılandırmalar için diğerlerinden daha fazla IP adresi gerekir. /27 veya /28 kullanan bir ağ geçidi alt ağı oluşturmanızı öneririz.


Adres alanının bir alt ağ ile çakıştığını veya alt ağın sanal ağınız için adres alanında bulunmadığını belirten bir hata görürseniz VNet adres aralığınızı denetleyin. Sanal ağınız için oluşturduğunuz adres aralığında yeterli IP adresi kullanılabilir olmayabilir. Örneğin, varsayılan alt ağınız tüm adres aralığını kapsıyorsa, ek alt ağlar oluşturmak için bir IP adresi kalmamıştır. IP adreslerini serbest bırakmak için mevcut adres alanında alt ağlarınızı ayarlayabilir veya ek bir adres aralığı belirtip orada ağ geçidi alt ağını oluşturabilirsiniz.