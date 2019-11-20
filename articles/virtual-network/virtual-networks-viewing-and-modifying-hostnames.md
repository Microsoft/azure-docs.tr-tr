---
title: Konak adlarını görüntüleme ve değiştirme | Microsoft Docs
description: Ad çözümlemesi için Azure sanal makineleri, Web ve çalışan rolleri için ana bilgisayar adlarını görüntüleme ve değiştirme
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: cce248e2906f4a36737388e8cc7124b1bb19fbae
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058669"
---
# <a name="viewing-and-modifying-hostnames"></a>Konak adlarını görüntüleme ve değiştirme
Rol örneklerinizin konak adı tarafından başvurulduğundan izin vermek için, her rol için hizmet yapılandırma dosyasında ana bilgisayar adı için değeri ayarlamanız gerekir. Bunu, istenen ana bilgisayar adını **rol** öğesinin **VMName** özniteliğine ekleyerek yapabilirsiniz. **VMName** özniteliğinin değeri, her rol örneğinin ana bilgisayar adı için temel olarak kullanılır. Örneğin, **VMName** *WebRole* ise ve bu rolün üç örneği varsa, örneklerin ana bilgisayar adları *webrole0*, *webrole1*ve *webrole2*olacaktır. Bir sanal makinenin ana bilgisayar adı sanal makine adına göre doldurulduğundan, yapılandırma dosyasındaki sanal makineler için bir konak adı belirtmeniz gerekmez. Microsoft Azure hizmeti yapılandırma hakkında daha fazla bilgi için bkz [. Azure hizmet yapılandırma şeması (. cscfg dosyası)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Ana bilgisayar adlarını görüntüleme
Aşağıdaki araçlardan herhangi birini kullanarak, sanal makinelerin ve rol örneklerinin ana bilgisayar adlarını bir bulut hizmetinde görüntüleyebilirsiniz.

### <a name="service-configuration-file"></a>Hizmet yapılandırma dosyası
Dağıtılmış bir hizmetin hizmet yapılandırma dosyasını, Azure portal hizmetin **Yapılandır** dikey penceresinden indirebilirsiniz. Daha sonra, konak adını görmek için **rol adı** öğesinin **VMName** özniteliğini bulabilirsiniz. Bu ana bilgisayar adının, her rol örneğinin ana bilgisayar adı için temel olarak kullanıldığını aklınızda bulundurun. Örneğin, **VMName** *WebRole* ise ve bu rolün üç örneği varsa, örneklerin ana bilgisayar adları *webrole0*, *webrole1*ve *webrole2*olacaktır.

### <a name="remote-desktop"></a>Uzak Masaüstü
Sanal makinelerinize veya rol örneklerine yönelik uzak masaüstü (Windows), Windows PowerShell uzaktan iletişim (Windows) veya SSH (Linux ve Windows) bağlantılarını etkinleştirdikten sonra, etkin bir uzak masaüstü bağlantısından ana bilgisayar adını çeşitli yollarla görüntüleyebilirsiniz:

* Komut isteminde veya SSH terminalinde hostname yazın.
* Komut istemine ipconfig/all yazın (yalnızca Windows).
* Bilgisayar adını sistem ayarlarında görüntüleyin (yalnızca Windows).

### <a name="azure-service-management-rest-api"></a>Azure hizmet yönetimi REST API
REST istemcisinde şu yönergeleri izleyin:

1. Azure portal bağlanmak için bir istemci sertifikasına sahip olduğunuzdan emin olun. İstemci sertifikası almak için, şu adımları [izleyin: Yayımlama ayarlarını ve abonelik bilgilerini](https://msdn.microsoft.com/library/dn385850.aspx)Indirin ve içeri aktarın. 
2. X-MS-Version adlı bir başlık girişini 2013-11-01 değeriyle ayarlayın.
3. Şu biçimde bir istek\/gönder: https:/Management.Core.Windows.net/\<subscrition-id\>/Services/hostedservices/\<Service-Name\>? Merge-Detail = true
4. Her **Roleınstance** öğesi için **hostname** öğesini arayın.

> [!WARNING]
> Ayrıca, **ınternaldnssuffix** öğesini denetleyerek veya Uzak Masaüstü oturumunda (Windows) veya cat/etc/çalıştırarak bir komut isteminden ipconfig/all komutunu çalıştırarak bulut hizmetinizin iç etki alanı sonekini görüntüleyebilirsiniz. bir SSH terminalinde (Linux) resolv. conf.
> 
> 

## <a name="modifying-a-hostname"></a>Ana bilgisayar adını değiştirme
Değiştirilen bir hizmet yapılandırma dosyasını karşıya yükleyerek veya bilgisayarı uzak Masaüstü oturumundan yeniden adlandırarak herhangi bir sanal makine veya rol örneği için ana bilgisayar adını değiştirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
[Ad çözümlemesi (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Azure hizmet yapılandırma şeması (. cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Azure sanal ağ yapılandırma şeması](https://go.microsoft.com/fwlink/?LinkId=248093)

[Ağ yapılandırma dosyalarını kullanarak DNS ayarlarını belirtme](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

