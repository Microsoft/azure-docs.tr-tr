---
title: Azure Application Gateway ön uç IP adresi yapılandırması
description: Bu makalede Azure Application Gateway ön uç IP adresinin nasıl yapılandırılacağı açıklanır.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: eff63510f70dd7b4cdd522cc5a2a68096cda7166
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548725"
---
# <a name="application-gateway-front-end-ip-address-configuration"></a>Ön uç IP adresi yapılandırması Application Gateway

Uygulama ağ geçidini genel IP adresi, özel bir IP adresi veya her ikisine de sahip olacak şekilde yapılandırabilirsiniz. İstemcilerin Internet 'e yönelik bir sanal IP (VIP) ile Internet üzerinden erişmesi gereken bir arka ucu barındırdığınızda genel IP adresi gereklidir.

## <a name="public-and-private-ip-address-support"></a>Ortak ve özel IP adresi desteği

Application Gateway v2 Şu anda yalnızca özel IP modunu desteklemiyor. Aşağıdaki birleşimleri destekler:

* Özel IP adresi ve genel IP adresi
* Yalnızca genel IP adresi

Daha fazla bilgi için bkz. [Application Gateway hakkında sık sorulan sorular](application-gateway-faq.yml#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address).


Internet 'e açık olmayan bir iç uç nokta için genel bir IP adresi gerekli değildir. Bu, *iç yük dengeleyici* (ILB) uç noktası veya özel ön uç IP 'si olarak bilinir. Bir Application Gateway ıLB, Internet 'e açık olmayan iç iş kolu uygulamaları için yararlıdır. Ayrıca, Internet 'e açık olmayan ancak hepsini bir kez deneme yük dağıtımı, oturum sürekliliği veya TLS sonlandırma gerektiren bir güvenlik sınırı içinde çok katmanlı bir uygulamadaki hizmetler ve katmanlar için de kullanışlıdır.

Yalnızca bir genel IP adresi ve bir özel IP adresi desteklenir. Uygulama ağ geçidini oluştururken ön uç IP 'sini seçersiniz.

- Genel IP adresi için yeni bir genel IP adresi oluşturabilir veya uygulama ağ geçidi ile aynı konumda bulunan bir genel IP 'yi kullanabilirsiniz. Daha fazla bilgi için bkz. [statik ve dinamik genel IP adresi](./application-gateway-components.md#static-versus-dynamic-public-ip-address).

- Özel bir IP adresi için, uygulama ağ geçidinin oluşturulduğu alt ağdan özel bir IP adresi belirtebilirsiniz. Bir tane belirtmezseniz, alt ağdan rastgele bir IP adresi otomatik olarak seçilir. Seçtiğiniz IP adresi türü (statik veya dinamik) daha sonra değiştirilemez. Daha fazla bilgi için bkz. [iç yük dengeleyiciye sahip bir uygulama ağ geçidi oluşturma](./application-gateway-ilb-arm.md).

Ön uç IP adresi, ön uç IP üzerindeki gelen istekleri denetleyen bir *dinleyiciyle* ilişkilendirilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Dinleyici yapılandırması hakkında bilgi edinin](configuration-listeners.md)
