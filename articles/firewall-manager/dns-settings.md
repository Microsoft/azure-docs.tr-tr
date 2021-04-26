---
title: Azure Güvenlik duvarı ilkesi DNS ayarları
description: Azure Güvenlik Duvarı ilkelerini, DNS sunucusu ve DNS proxy ayarları ile yapılandırabilirsiniz.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 02/17/2021
ms.author: victorh
ms.openlocfilehash: a02879c5322add16f89f77e2da39daec7d2b5f31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633657"
---
# <a name="azure-firewall-policy-dns-settings"></a>Azure Güvenlik duvarı ilkesi DNS ayarları

Özel bir DNS sunucusu yapılandırabilir ve Azure Güvenlik Duvarı ilkeleri için DNS proxy 'yi etkinleştirebilirsiniz. Bu ayarları, **DNS ayarları** sayfasından güvenlik duvarını veya daha yenisini dağıtırken yapılandırabilirsiniz.

## <a name="dns-servers"></a>DNS sunucuları

DNS sunucusu, etki alanı adlarını IP adreslerine tutar ve çözümler. Azure Güvenlik Duvarı, varsayılan olarak ad çözümlemesi için Azure DNS kullanır. **DNS sunucusu** ayarı, kendi DNS sunucularınızı Azure Güvenlik Duvarı ad çözümlemesi için yapılandırmanızı sağlar. Tek veya birden çok sunucu yapılandırabilirsiniz.

### <a name="configure-custom-dns-servers"></a>Özel DNS sunucularını yapılandırma

1. Güvenlik Duvarı ilkenizi seçin.
2. **Ayarlar** altında **DNS ayarları**' nı seçin.
3. **DNS sunucuları** altında, sanal ağınızda daha önce BELIRTILEN mevcut DNS sunucularını yazabilir veya ekleyebilirsiniz.
4. **Kaydet**’i seçin.
5. Güvenlik duvarı artık, DNS trafiğini ad çözümlemesi için belirtilen DNS sunucusuna yönlendirir.

## <a name="dns-proxy"></a>DNS proxy 'si

Azure Güvenlik duvarını, DNS proxy 'si olarak davranacak şekilde yapılandırabilirsiniz. DNS proxy 'si, istemci sanal makinelerinden bir DNS sunucusuna DNS istekleri için bir ara sunucu görevi görür. Özel bir DNS sunucusu yapılandırırsanız DNS çözümleme uyuşmazlığını önlemek için DNS proxy 'yi etkinleştirmeniz ve ağ kurallarında FQDN filtrelemeyi etkinleştirmeniz gerekir.

DNS proxy 'yi etkinleştirmezseniz, istemciden gelen DNS istekleri farklı bir zamanda bir DNS sunucusuna seyahat edebilir veya güvenlik duvarından göre farklı bir yanıt döndürebilir. DNS proxy, tutarsızlığın önüne geçmek için Azure Güvenlik duvarını istemci isteklerinin yoluna koyar.

DNS proxy yapılandırması üç adım gerektirir:

1. Azure Güvenlik duvarı DNS ayarları 'nda DNS proxy 'yi etkinleştirin.
2. İsteğe bağlı olarak özel DNS sunucunuzu yapılandırın veya belirtilen varsayılanı kullanın.
3. Son olarak, Azure Güvenlik duvarının özel IP adresini, sanal ağ DNS sunucusu ayarlarınızda özel bir DNS adresi olarak yapılandırmanız gerekir. Bu, DNS trafiğinin Azure Güvenlik Duvarı 'na yönlendirilmesini sağlar.

### <a name="configure-dns-proxy"></a>DNS proxy 'yi yapılandırma

DNS proxy 'yi yapılandırmak için, sanal ağ DNS sunucularınız ayarınızı güvenlik duvarı özel IP adresini kullanacak şekilde yapılandırmanız gerekir. Ardından, Azure Güvenlik Duvarı İlkesi **DNS ayarları**' nda DNS proxy 'yi etkinleştirin.

#### <a name="configure-virtual-network-dns-servers"></a>Sanal ağ DNS sunucularını yapılandırma

1. DNS trafiğinin Azure Güvenlik Duvarı üzerinden yönlendirileceği sanal ağı seçin.
2. **Ayarlar** altında, **DNS sunucuları**' nı seçin.
3. **DNS sunucuları** altında **özel** ' i seçin.
4. Güvenlik duvarının özel IP adresini girin.
5. **Kaydet**’i seçin.

#### <a name="enable-dns-proxy"></a>DNS proxy 'yi etkinleştir

1. Azure Güvenlik Duvarı ilkenizi seçin.
2. **Ayarlar** altında **DNS ayarları**' nı seçin.
3. **DNS proxy** varsayılan olarak devre dışıdır. Etkinleştirildiğinde güvenlik duvarı, 53 numaralı bağlantı noktasını dinler ve DNS isteklerini yapılandırılmış DNS sunucularına iletir.
4. Ayarların ortamınıza uygun olduğundan emin olmak için **DNS sunucularının** yapılandırmasını gözden geçirin.
5. **Kaydet**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

[Ağ kurallarında FQDN filtrelemesi](fqdn-filtering-network-rules.md)