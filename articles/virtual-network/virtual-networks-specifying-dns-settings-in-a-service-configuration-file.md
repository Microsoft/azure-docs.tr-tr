---
title: Bir hizmet yapılandırma dosyasında DNS ayarlarını belirtme | Microsoft Docs
description: sanal ağ için hizmet yapılandırma dosyasını kullanarak özel DNS ayarlarını belirtme
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: f27802d76a8b94a0d5f1eb0c35fd55c93712e557
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059075"
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Bir hizmet yapılandırma dosyasında DNS ayarlarını belirtme
## <a name="dns-elements"></a>DNS öğeleri
Hizmet yapılandırma dosyası, hizmetin kullanacağı etki alanı adı sistemi (DNS) sunucuları için IPv4 adresleri listesi içeren bir DnsServers öğesi içerebilir. Hizmet yapılandırma dosyasındaki ayarlar, ağ yapılandırma dosyasındaki ayarları dikkate alır. Daha fazla bilgi için bkz. [Azure hizmet yapılandırma şeması (. cscfg dosyası)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**NetworkConfiguration öğesi**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> **DNSServer** öğesindeki **Name** özniteliği yalnızca bir başvuru adı olarak kullanılır. DNS sunucusunun ana bilgisayar adını temsil etmez. Her **DNSServer** öznitelik değeri tüm Microsoft Azure aboneliği genelinde benzersiz olmalıdır.
> 
> 

## <a name="see-also"></a>Ayrıca Bkz.
[Azure hizmet yapılandırma şeması (. cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Azure sanal ağ yapılandırma şeması](https://go.microsoft.com/fwlink/?LinkId=248093)

[Ağ yapılandırma dosyalarını kullanarak bir sanal ağ yapılandırma](https://go.microsoft.com/fwlink/?LinkId=248094)

[Yönetim Portalı sanal ağ ayarları hakkında](https://go.microsoft.com/fwlink/?LinkId=248092)

