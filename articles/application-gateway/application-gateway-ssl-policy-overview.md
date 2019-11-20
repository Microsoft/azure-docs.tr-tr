---
title: Azure Application Gateway için SSL ilkesine genel bakış
description: Azure Application Gateway SSL ilkesini yapılandırmayı ve arka uç sunucu grubundan şifreleme ve şifre çözme ek yükünü azaltmayı öğrenin.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 6b3d52f1f6bd0390ab6ccafa80b2979cb0e498fd
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130417"
---
# <a name="application-gateway-ssl-policy-overview"></a>Application Gateway SSL ilkesine genel bakış

SSL sertifika yönetimini merkezileştirmek ve arka uç sunucu grubundan şifreleme ve şifre çözme yükünü azaltmak için Azure Application Gateway kullanabilirsiniz. Bu merkezi SSL işleme, kurumsal güvenlik gereksinimlerinize uygun bir merkezi SSL ilkesi belirtmenize de olanak tanır. Bu, uyumluluk gereksinimlerini ve güvenlik yönergelerini ve önerilen uygulamaları karşılamanıza yardımcı olur.

SSL ilkesi, SSL el sıkışması sırasında SSL protokolü sürümünün ve şifre paketlerinin ve şifrelemelerin kullanıldığı sıranın denetimini içerir. Application Gateway, SSL ilkesini denetlemek için iki mekanizma sunar. Önceden tanımlanmış bir ilke ya da özel ilke kullanabilirsiniz.

## <a name="predefined-ssl-policy"></a>Önceden tanımlanmış SSL ilkesi

Application Gateway önceden tanımlanmış üç güvenlik ilkesi vardır. Uygun güvenlik düzeyini almak için ağ geçidinizi bu ilkelerden herhangi biriyle yapılandırabilirsiniz. İlke adlarına, yapılandırıldığı yıl ve aya göre açıklama eklenir. Her ilke farklı SSL protokol sürümleri ve şifre paketleri sunar. En iyi SSL güvenliğini sağlamak için en yeni SSL ilkelerini kullanmanızı öneririz.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Özellik  |Değer  |
|---|---|
|Ad     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Varsayılan| True (önceden tanımlanmış bir ilke belirtilmemişse) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Özellik  |Değer  |
|   ---      |  ---       |
|Ad     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Varsayılan| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Özellik  |Değer  |
|---|---|
|Ad     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Varsayılan| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Özel SSL ilkesi

Gereksinimlerinize göre önceden tanımlanmış bir SSL ilkesinin yapılandırılması gerekiyorsa, kendi özel SSL ilkenizi tanımlamanız gerekir. Özel bir SSL ilkesiyle, desteklenecek en düşük SSL protokolü sürümü ve desteklenen şifre paketleri ve bunların öncelik sıralaması üzerinde tüm denetiminiz vardır.
 
### <a name="ssl-protocol-versions"></a>SSL protokolü sürümleri

* SSL 2,0 ve 3,0 tüm uygulama ağ geçitleri için varsayılan olarak devre dışıdır. Bu protokol sürümleri yapılandırılamaz.
* Özel bir SSL ilkesi, aşağıdaki üç protokolden birini ağ geçidinizin en düşük SSL protokolü sürümü olarak seçme seçeneği sunar: TLSv1_0, TLSv1_1 ve TLSv1_2.
* Hiçbir SSL ilkesi tanımlanmamışsa, üç protokol (TLSv1_0, TLSv1_1 ve TLSv1_2) etkinleştirilir.

### <a name="cipher-suites"></a>Şifre paketleri

Application Gateway, özel ilkenizi seçebileceğiniz aşağıdaki şifre paketlerini destekler. Şifre paketlerinin sıralaması, SSL anlaşması sırasında öncelik sırasını belirler.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

> [!NOTE]
> Bağlantı için kullanılan SSL şifre paketleri de kullanılan sertifikanın türüne bağlıdır. İstemciden uygulama ağ geçidi bağlantılarında, kullanılan şifre paketleri uygulama ağ geçidi dinleyicisinde sunucu sertifikalarının türünü temel alır. Uygulama ağ geçidinde arka uç havuzu bağlantılarına, kullanılan şifre paketleri, arka uç havuzu sunucularındaki sunucu sertifikalarının türünü temel alır.

## <a name="next-steps"></a>Sonraki adımlar

Bir SSL ilkesi yapılandırmayı öğrenmek istiyorsanız, bkz. [bir uygulama ağ GEÇIDINDE SSL Ilkesini yapılandırma](application-gateway-configure-ssl-policy-powershell.md).
