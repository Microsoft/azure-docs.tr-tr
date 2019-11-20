---
title: Sanal ağ yapılandırma dosyasında DNS ayarlarını belirtme | Microsoft Docs
description: Klasik dağıtım modelinde bir sanal ağ yapılandırma dosyası kullanarak bir sanal ağdaki DNS sunucusu ayarlarını değiştirme
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
tags: azure-service-management
ms.assetid: a8905927-92ac-42b5-8c33-8e42c000692c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: genli
ms.openlocfilehash: c15d73673c19383deabe15ef30026990dfd138b9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059087"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Bir sanal ağ yapılandırma dosyasında DNS ayarlarını belirtme
Bir ağ yapılandırma dosyası, etki alanı adı sistemi (DNS) ayarlarını belirtmek için kullanabileceğiniz iki öğeye sahiptir: **Dnsservers** ve **dnsserverref**. **Dnservers** öğesine IP adreslerini ve başvuru ADLARıNı belirterek DNS sunucularının bir listesini ekleyebilirsiniz. Daha sonra, DnsServers öğesinden hangi DNS sunucusu girişlerinin sanal ağınızdaki farklı ağ siteleri için kullanıldığını belirtmek için bir **Dnsserverref** öğesi kullanabilirsiniz.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Bu makale, klasik dağıtım modelini kapsamaktadır.

Ağ yapılandırma dosyası aşağıdaki öğeleri içerebilir. Her bir öğenin başlığı, öğe değeri ayarları hakkında ek bilgi sağlayan bir sayfayla bağlantılıdır.

> [!IMPORTANT]
> Ağ yapılandırma dosyasının nasıl yapılandırılacağı hakkında bilgi için bkz. [ağ yapılandırma dosyası kullanarak sanal ağ](virtual-networks-using-network-configuration-file.md)yapılandırma. Ağ yapılandırma dosyasında bulunan her öğe hakkında daha fazla bilgi için bkz. [Azure sanal ağ yapılandırma şeması](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[DNS öğesi](https://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> **DNSServer** öğesindeki **Name** özniteliği yalnızca **dnsserverref** öğesi için bir başvuru olarak kullanılır. DNS sunucusunun ana bilgisayar adını temsil etmez. Her **DNSServer** öznitelik değeri tüm Microsoft Azure aboneliği genelinde benzersiz olmalıdır
> 
> 

[Sanal ağ siteleri öğesi](https://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Bu ayarı, sanal ağ siteleri öğesi için belirtmek üzere, daha önce DNS öğesinde tanımlanmış olmalıdır. Sanal ağ siteleri öğesindeki DnsServerRef *adı* , DNSServer *adı*için DNS öğesinde belirtilen bir ad değerine başvurmalıdır.
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
* [Azure sanal ağ yapılandırma şemasını](https://go.microsoft.com/fwlink/?LinkId=248093)anlayın.
* [Azure hizmet yapılandırma şemasını](https://msdn.microsoft.com/library/windowsazure/ee758710)anlayın.
* [Ağ yapılandırma dosyalarını kullanarak bir sanal ağ yapılandırın](virtual-networks-using-network-configuration-file.md).

