---
title: Azure Güvenlik Duvarı kural işleme mantığı
description: Azure Güvenlik Duvarı kural işleme mantığı hakkında bilgi edinin
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 74e58c316651a1604984ac14c70a3a65d46d6d9f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518209"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure Güvenlik Duvarı kural işleme mantığı

Azure Güvenlik duvarında NAT kuralları, ağ kuralları ve uygulamalar kuralları vardır. Kurallar kural türüne göre işlenir.

## <a name="network-rules-and-applications-rules"></a>Ağ kuralları ve uygulama kuralları

Ağ kuralları önce uygulanır, sonra uygulama kuralları. Kurallar sonlandırılıyor. Bu nedenle, ağ kurallarında bir eşleşme bulunursa uygulama kuralları işlenmez.  Ağ kuralı eşleşmesi yoksa ve paket protokolü HTTP/HTTPS ise bu durumda paket uygulama kuralları tarafından değerlendirilir. Hala eşleşme bulunamazsa paket, altyapı kuralı koleksiyonuna göre değerlendirilir. Ardından hala eşleşme yoksa paket varsayılan olarak reddedilir.

## <a name="nat-rules"></a>NAT kuralları

Gelen bağlantı [, öğretici: Azure Güvenlik Duvarı ile gelen trafiği Azure Portal kullanarak](../firewall/tutorial-firewall-dnat.md)hedef ağ adresi çevirisi (DNAT) yapılandırılarak etkinleştirilebilir. DNAT kuralları önce uygulanır. Bir eşleşme bulunursa, çevrilmiş trafiğe izin veren örtülü olarak karşılık gelen bir ağ kuralı eklenir. Bu davranışı, çevrilen trafikle eşleşen reddetme kuralları olan bir ağ kural koleksiyonunu açıkça ekleyerek geçersiz kılabilirsiniz. Bu bağlantılar için uygulama kuralı uygulanmaz.

## <a name="inherited-rules"></a>Devralınan kurallar

Bir üst ilkeden devralınan ağ kuralı koleksiyonları, yeni ilkenizin kapsamında tanımlanan ağ kuralı koleksiyonlarının üzerine her zaman öncelik atanır. Aynı mantık uygulama kuralı koleksiyonları için de geçerlidir. Bununla birlikte, devralma ne olursa olsun, ağ kuralı koleksiyonları her zaman uygulama kuralı koleksiyonlarından önce işlenir.

Varsayılan olarak, ilkeniz, ana ilke tehdit zekası modunu devralır. Tehdit bilgileri modinizi ilke ayarları sayfasında farklı bir değere ayarlayarak bunu geçersiz kılabilirsiniz. Yalnızca daha sıkı bir değerle geçersiz kılınabilir. Örneğin, üst ilke *yalnızca uyarı*olarak ayarlandıysa, bu yerel ilkeyi *uyarı ve reddetme*için yapılandırabilirsiniz, ancak kapatamaz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Güvenlik Duvarı Yöneticisi önizlemesi hakkında daha fazla bilgi edinin](overview.md)