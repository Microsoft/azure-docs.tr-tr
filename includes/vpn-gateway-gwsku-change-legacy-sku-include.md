---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c232e1ce183c6935d625b5bc9987a4981865ae4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010844"
---
Kaynak Yöneticisi dağıtım modeliyle çalışıyorsanız, yeni ağ geçidi SKU 'Larına geçiş yapabilirsiniz. Eski bir ağ geçidi SKU 'sunda yeni bir SKU 'ya değişiklik yaptığınızda, mevcut VPN ağ geçidini silip yeni bir VPN ağ geçidi oluşturursunuz.

Akışıyla

1. Sanal ağ geçidine ilişkin tüm bağlantıları kesin.
2. Eski VPN ağ geçidini silin.
3. Yeni VPN ağ geçidini oluşturun.
4. Şirket içi VPN cihazlarınızdaki VPN ağ geçidi IP adresini (Siteden Siteye bağlantılar için) yenisi ile güncelleştirin.
5. Bu ağ geçidine bağlanacak tüm VNet-VNet yerel ağ geçitleri için ağ geçidi IP adresi değerini güncelleştirin.
6. Bu VPN ağ geçidi yoluyla sanal ağa bağlanan P2S istemcileri için yeni istemci VPN yapılandırma paketlerini indirin.
7. Sanal ağ geçidine ilişkin tüm bağlantıları yeniden oluşturun.

Dikkat edilmesi gerekenler:

* Yeni SKU 'Lara geçiş yapmak için VPN ağ geçidinizin Kaynak Yöneticisi dağıtım modelinde olmalıdır.
* Klasik bir VPN ağ geçidiniz varsa, bu ağ geçidi için eski eski SKU 'Ları kullanmaya devam etmeniz gerekir, ancak eski SKU 'Lar arasında yeniden boyutlandırma yapabilirsiniz. Yeni SKU 'Lara geçiş yapılamaz.
* Eski bir SKU 'dan yeni bir SKU 'ya değişiklik yaptığınızda bağlantı kapalı kalma süresine sahip olursunuz.
* Yeni bir ağ geçidi SKU 'suna geçiş yaparken, VPN ağ geçidiniz için genel IP adresi değişir. Bu, daha önce kullandığınız genel IP adresi nesnesini belirtseniz bile olur.