---
title: Azure ağ Izleyicisi bağlantısına giriş sorun giderme | Microsoft Docs
description: Bu sayfada ağ Izleyicisi bağlantısı sorun giderme özelliğine bir genel bakış sunulmaktadır
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: damendo
ms.openlocfilehash: 9c855fff9e5791b9c0cf870acfc6de53e7a700b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654005"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Azure ağ Izleyicisi 'nde bağlantıya sorun giderme konusuna giriş

Ağ Izleyicisi 'nin bağlantı sorunlarını giderme özelliği, bir sanal makineden bir sanal makineye (VM), tam etki alanı adına (FQDN), URI 'ye veya IPv4 adresine doğrudan TCP bağlantısını denetleme yeteneği sağlar. Ağ senaryoları karmaşıktır, bunlar ağ güvenlik grupları, güvenlik duvarları, Kullanıcı tanımlı yollar ve Azure tarafından sağlanmış kaynaklar kullanılarak uygulanır. Karmaşık yapılandırmalarda sorun giderme bağlantı sorunları zorlayıcı hale getirir. Ağ Izleyicisi, bağlantı sorunlarını bulma ve algılamaya yönelik süreyi azaltmaya yardımcı olur. Döndürülen sonuçlar, bir bağlantı sorununun bir platform veya Kullanıcı yapılandırma sorunundan kaynaklanıyor olup olmadığı hakkında öngörüler sağlayabilir. Bağlantı, [PowerShell](network-watcher-connectivity-powershell.md), [Azure CLI](network-watcher-connectivity-cli.md)ve [REST API](network-watcher-connectivity-rest.md)ile denetlenebilir.

> [!IMPORTANT]
> Bağlantı sorunlarını gidermek için, üzerinde çalıştığınız VM 'nin `AzureNetworkWatcherExtension` VM uzantısının yüklü olması gerekir. Windows VM 'ye uzantı yüklemek için bkz. [Windows Için Azure ağ Izleyicisi Aracısı sanal makine uzantısı](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) ve Linux VM Için [Azure Ağ İzleyicisi Aracısı sanal makine uzantısı](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)' nı ziyaret edin. Uzantı hedef uç noktada gerekli değil.

## <a name="response"></a>Yanıt

Aşağıdaki tabloda, bağlantı sorun gidermesi çalışırken döndürülen özellikler gösterilmektedir.

|Özellik  |Açıklama  |
|---------|---------|
|ConnectionStatus     | Bağlantı denetiminin durumu. Olası sonuçlar **erişilebilir** ve **ulaşılamaz** durumda olur.        |
|Avglatencyms     | Milisaniye cinsinden bağlantı denetimi sırasında ortalama gecikme süresi. (Yalnızca Check durumu erişilebilir ise gösterilir)        |
|Minlatencyms     | Bağlantı denetimi sırasında milisaniye cinsinden en düşük gecikme süresi. (Yalnızca Check durumu erişilebilir ise gösterilir)        |
|Maxlatencyms     | Milisaniye cinsinden bağlantı denetimi sırasında en fazla gecikme süresi. (Yalnızca Check durumu erişilebilir ise gösterilir)        |
|ProbesSent     | Denetim sırasında gönderilen yoklamaların sayısı. En büyük değer 100 ' dir.        |
|ProbesFailed     | Denetim sırasında başarısız olan yoklamaların sayısı. En büyük değer 100 ' dir.        |
|Atlamalar     | Kaynaktan hedefe atlama yoluyla atlama.        |
|Atlamaları []. Türüyle     | Kaynak türü. Olası değerler şunlardır. **kaynak**, **virtualappliance**, **vnetlocal** ve **Internet**.        |
|Atlamaları []. Numarasını | Atlamanın benzersiz tanımlayıcısı.|
|Atlamaları []. Adrestir | Atlamanın IP adresi.|
|Atlamaları []. RESOURCEID | Atlama bir Azure kaynağı ise, atlamanın RESOURCEID. Bir internet kaynağı ise RESOURCEID **İnternet** olur. |
|Atlamaları []. Nexthopıds | Sonraki atlamanın benzersiz tanımlayıcısı.|
|Atlamaları []. Çıkışları | Bu atlamada Denetim sırasında karşılaşılan sorunların toplanması. Sorun yoksa değer boştur.|
|Atlamaları []. Sorunlar []. Tıdır | Geçerli atlamada, sorun oluştuğu yerdir. Olası değerler şunlardır:<br/> **Gelen** -sorun önceki atlamanın geçerli atlamaya olan bağlantıdır<br/>**Giden** sorun, geçerli atlamanın bir sonraki atlamada olan bağlantıdır<br/>**Yerel** -sorun geçerli atlamada.|
|Atlamaları []. Sorunlar []. İnin | Sorunun önem derecesi algılandı. Olası değerler **hata** ve **uyarıdır**. |
|Atlamaları []. Sorunlar []. Türüyle |Sorun türü bulundu. Olası değerler şunlardır: <br/>**CPU**<br/>**Bellek**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Atlamaları []. Sorunlar []. Bağlam |Bulunan sorunla ilgili ayrıntılar.|
|Atlamaları []. Sorunlar []. Context []. Key |Anahtar değer çiftinin anahtarı döndürüldü.|
|Atlamaları []. Sorunlar []. Context []. Value |Döndürülen anahtar değer çiftinin değeri.|

Aşağıda, bir atlama üzerinde bulunan bir sorun örneği verilmiştir.

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>Hata türleri

Bağlantı sorunlarını giderme bağlantıyla ilgili hata türlerini döndürür. Aşağıdaki tabloda döndürülen geçerli hata türlerinin bir listesi verilmiştir.

|Tür  |Açıklama  |
|---------|---------|
|CPU     | Yüksek CPU kullanımı.       |
|Bellek     | Yüksek bellek kullanımı.       |
|GuestFirewall     | Bir sanal makine güvenlik duvarı yapılandırması nedeniyle trafik engellendi.        |
|DNSResolution     | Hedef adres için DNS çözümlemesi başarısız oldu.        |
|NetworkSecurityRule    | Trafik bir NSG kuralı tarafından engelleniyor (kural döndürülür)        |
|UserDefinedRoute|Kullanıcı tanımlı veya sistem yolu nedeniyle trafik bırakıldı. |

### <a name="next-steps"></a>Sonraki adımlar

[Azure Portal](network-watcher-connectivity-portal.md), [POWERSHELL](network-watcher-connectivity-powershell.md), [Azure CLI](network-watcher-connectivity-cli.md)veya [REST API](network-watcher-connectivity-rest.md)kullanarak bağlantı sorunlarını giderme hakkında bilgi edinin.