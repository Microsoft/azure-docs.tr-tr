---
title: Azure Load Balancer kavramlar
description: Azure Load Balancer kavramlara genel bakış
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 3f8c288f950f34e1764c50e8eb74a8a73b39b3d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94698538"
---
# <a name="azure-load-balancer-algorithm"></a>Azure Load Balancer algoritması

Yük dengeleyici hem UDP hem de TCP uygulamaları için çeşitli yetenekler sağlar.

## <a name="load-balancing-algorithm"></a>Yük dengeleme algoritması

Ön uca trafiği bir arka uç havuzuna dağıtmak için bir yük dengeleme kuralı oluşturabilirsiniz. Azure Load Balancer gelen akışların dağıtılması için bir karma algoritması kullanır (bayt değil). Yük dengeleyici, akışların üst bilgilerini arka uç havuz örneklerine yeniden yazar. Bir sistem durumu araştırması sağlıklı bir arka uç bitiş noktasını gösterdiğinde yeni akışlar almak için bir sunucu kullanılabilir.

Varsayılan olarak, yük dengeleyici beş demet karma kullanır.

Karma değeri şunları içerir:

- **Kaynak IP adresi**
- **Kaynak bağlantı noktası**
- **Hedef IP adresi**
- **Hedef bağlantı noktası**
- **Akışların kullanılabilir sunucularla eşlenecek IP protokol numarası**

Bir kaynak IP adresine benzeşim, iki veya üç demet olmayan bir karma kullanılarak oluşturulur. Aynı akışın paketleri, yük dengeli ön ucun arkasındaki aynı örneğe ulaşır.

İstemci aynı kaynak IP 'den yeni bir akış başlattığında kaynak bağlantı noktası değişir. Sonuç olarak, beş demet karma, trafiğin farklı bir arka uç uç noktasına geçmesine neden olabilir.
Daha fazla bilgi için bkz. [Azure Load Balancer Dağıtım modunu yapılandırma](./load-balancer-distribution-mode.md).

Aşağıdaki görüntüde karma tabanlı dağıtım gösterilmiştir:

![Karma tabanlı dağıtım](./media/load-balancer-overview/load-balancer-distribution.png)

*Şekil: Karma tabanlı dağıtım*

## <a name="application-independence-and-transparency"></a>Uygulama bağımsızlık ve saydamlığı

Yük dengeleyici, TCP veya UDP veya uygulama katmanıyla doğrudan etkileşime girmez. Herhangi bir TCP veya UDP uygulama senaryosu desteklenebilir. Yük dengeleyici akışın yükünü kapatmaz veya kaynaklanmaz veya akışın yüküne göre etkileşime geçmez. Yük Dengeleyici uygulama katmanı ağ geçidi işlevselliği sağlamaz. Protokol el sıkışmaları her zaman doğrudan istemci ile arka uç havuzu örneği arasında gerçekleşir. Gelen akışa verilen yanıt her zaman sanal makineden gelen yanıttır. Akış sanal makineye ulaştığında özgün kaynak IP adresi de korunur.

- Her uç nokta bir VM tarafından yanıtlanmıştır. Örneğin, istemcisiyle seçilen arka uç VM arasında bir TCP el sıkışması meydana gelir. Ön uca bir istek yanıtı, arka uç VM tarafından oluşturulan bir yanıttır. Bir ön uca bağlantıyı başarıyla doğruladığınızda, en az bir arka uç sanal makinesi için bağlantıyı doğrularsınız.
- Uygulama yükleri yük dengeleyicisine saydamdır. Herhangi bir UDP veya TCP uygulaması desteklenebilir.
- Yük dengeleyici TCP yükü ile etkileşimde olmadığından ve TLS yük boşaltma sağladığından, kapsamlı şifrelenmiş senaryolar oluşturabilirsiniz. Yük dengeleyici kullanmak, TLS bağlantısını VM üzerinde sonlandırarak TLS uygulamaları için büyük ölçekli genişleme sağlar. Örneğin, TLS oturum anahtar kapasiteniz yalnızca, arka uç havuzuna eklediğiniz VM 'lerin türü ve sayısıyla sınırlıdır.

## <a name="next-steps"></a>Sonraki adımlar

- Azure Load Balancer oluşturan [Bileşenler](components.md) hakkında bilgi edinin.
- Load Balancer kullanmaya başlamak için bkz. [genel standart Load Balancer oluşturma](quickstart-load-balancer-standard-public-portal.md) : oluşturma, özel bir IIS uzantısı olan VM oluşturma ve VM 'ler arasında Web uygulamasının yükünü dengeleme.
- [Giden Azure Load Balancer bağlantıları](load-balancer-outbound-connections.md)hakkında bilgi edinin.
- [Azure Load Balancer](load-balancer-overview.md)hakkında daha fazla bilgi edinin.
- [Sistem durumu araştırmaları](load-balancer-custom-probe-overview.md)hakkında bilgi edinin.
- [Standart Load Balancer tanılama](load-balancer-standard-diagnostics.md)hakkında bilgi edinin.
- [Ağ güvenlik grupları](../virtual-network/network-security-groups-overview.md)hakkında daha fazla bilgi edinin.