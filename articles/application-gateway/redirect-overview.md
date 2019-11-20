---
title: Azure Application Gateway için yeniden yönlendirmeye genel bakış
description: Bir dinleyicide alınan trafiği başka bir dinleyiciye veya bir dış siteye yeniden yönlendirmek için Azure Application Gateway yeniden yönlendirme özelliği hakkında bilgi edinin.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 5943d8aad4d5dd0d981fae9b2325dd3fc75b31e8
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129876"
---
# <a name="application-gateway-redirect-overview"></a>Application Gateway yeniden yönlendirmeye genel bakış

Trafiği yeniden yönlendirmek için uygulama ağ geçidi kullanabilirsiniz.  Bir dinleyiciden alınan trafiği başka bir dinleyiciye veya bir dış siteye yeniden yönlendirmeyi sağlayan genel bir yeniden yönlendirme mekanizmasına sahiptir. Bu, uygulama yapılandırmasını basitleştirir, kaynak kullanımını iyileştirir ve genel ve yol tabanlı yeniden yönlendirme dahil yeni yeniden yönlendirme senaryolarını destekler.

Birçok Web uygulaması için ortak bir yeniden yönlendirme senaryosu, uygulama ve kullanıcıları arasındaki iletişimin şifreli bir yol üzerinde gerçekleşmesini sağlamak için otomatik HTTP 'den HTTPS 'ye yeniden yönlendirmeyi destekliyoruz. Geçmişte, müşteriler, tek amacı HTTP üzerinde aldığı istekleri HTTPS 'ye yeniden yönlendirmek olan özel bir arka uç havuzu oluşturma gibi teknikler kullanmıştır. Application Gateway ' de yeniden yönlendirme desteği sayesinde, bir yönlendirme kuralına yeni bir yeniden yönlendirme yapılandırması ekleyerek ve hedef dinleyici olarak HTTPS protokolüne sahip başka bir dinleyici belirterek bunu yapabilirsiniz.

Aşağıdaki yeniden yönlendirme türleri desteklenir:

- 301 kalıcı yeniden yönlendirme
- 302 bulundu
- 303 diğer bkz.
- 307 geçici yeniden yönlendirme

Application Gateway yeniden yönlendirme desteği aşağıdaki özellikleri sunar:

-  **Küresel yeniden yönlendirme**

   Bir dinleyicisinden ağ geçidinde başka bir dinleyiciye yeniden yönlendirir. Bu özellik, bir sitede HTTP’den HTTPS’ye yeniden yönlendirmeyi sağlar.
- **Yol tabanlı yeniden yönlendirme**

   Bu tür bir yeniden yönlendirme, yalnızca belirli bir site alanında HTTP-HTTPS yeniden yönlendirmesine, örneğin/cart/* ile belirtilen bir alışveriş sepeti alanı sağlar.
- **Dış siteye yönlendir**

![Meniz](./media/redirect-overview/redirect.png)

Bu değişiklik ile müşterilerin, yeniden yönlendirmenin istendiği hedef dinleyiciyi veya dış siteyi belirten yeni bir yeniden yönlendirme yapılandırma nesnesi oluşturması gerekir. Yapılandırma öğesi, URI yolunu ve sorgu dizesini yeniden yönlendirilen URL 'ye eklemeyi etkinleştirmek için seçenekleri de destekler. Yeniden yönlendirme türünü de seçebilirsiniz. Bu yeniden yönlendirme yapılandırması oluşturulduktan sonra, kaynak dinleyicisine yeni bir kural aracılığıyla eklenir. Temel bir kural kullanırken, yeniden yönlendirme yapılandırması bir kaynak dinleyicisi ile ilişkilendirilir ve küresel bir yeniden yönlendirmelidir. Yol tabanlı bir kural kullanıldığında, yeniden yönlendirme yapılandırması URL yol eşlemesinde tanımlanır. Bu nedenle, yalnızca bir sitenin belirli yol alanı için geçerlidir.

### <a name="next-steps"></a>Sonraki adımlar

[Uygulama ağ geçidinde URL yeniden yönlendirmeyi yapılandırma](tutorial-url-redirect-powershell.md)
