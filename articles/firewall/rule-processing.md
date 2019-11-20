---
title: Azure Güvenlik Duvarı kural işleme mantığı
description: Azure Güvenlik duvarında NAT kuralları, ağ kuralları ve uygulamalar kuralları vardır. Kurallar kural türüne göre işlenir.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2018
ms.author: victorh
ms.openlocfilehash: 0fc11221e0ff79d6e17b93282403792fc135c2a4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166782"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure Güvenlik Duvarı kural işleme mantığı
Azure Güvenlik duvarında NAT kuralları, ağ kuralları ve uygulamalar kuralları vardır. Kurallar kural türüne göre işlenir.


## <a name="network-rules-and-applications-rules"></a>Ağ kuralları ve uygulama kuralları 
Ağ kuralları önce uygulanır, sonra uygulama kuralları. Kurallar sonlandırılıyor. Bu nedenle, ağ kurallarında bir eşleşme bulunursa uygulama kuralları işlenmez.  Ağ kuralı eşleşmesi yoksa ve paket protokolü HTTP/HTTPS ise bu durumda paket uygulama kuralları tarafından değerlendirilir. Hala eşleşme bulunamazsa paket, altyapı kuralı koleksiyonuna göre değerlendirilir. Ardından hala eşleşme yoksa paket varsayılan olarak reddedilir.

## <a name="nat-rules"></a>NAT kuralları
Gelen bağlantı [, öğretici: Azure Güvenlik Duvarı ile gelen trafiği Azure Portal kullanarak](tutorial-firewall-dnat.md)hedef ağ adresi çevirisi (DNAT) yapılandırılarak etkinleştirilebilir. DNAT kuralları önce uygulanır. Bir eşleşme bulunursa, çevrilmiş trafiğe izin veren örtülü olarak karşılık gelen bir ağ kuralı eklenir. Bu davranışı, çevrilen trafikle eşleşen reddetme kuralları olan bir ağ kural koleksiyonunu açıkça ekleyerek geçersiz kılabilirsiniz. Bu bağlantılar için uygulama kuralı uygulanmaz.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Güvenlik duvarını dağıtmayı ve yapılandırmayı](tutorial-firewall-deploy-portal.md)öğrenin.