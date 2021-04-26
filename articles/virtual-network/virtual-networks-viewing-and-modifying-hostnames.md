---
title: Konak adlarını görüntüleme ve değiştirme | Microsoft Docs
description: Ad çözümlemesi için Azure sanal makineleri, Web ve çalışan rolleri için ana bilgisayar adlarını görüntüleme ve değiştirme
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: ed250e3f32965fc450102fb14b93b93d6753ab3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222794"
---
# <a name="viewing-and-modifying-hostnames"></a>Konak adlarını görüntüleme ve değiştirme
Rol örneklerinizin konak adı tarafından başvurulduğundan izin vermek için, her rol için hizmet yapılandırma dosyasında ana bilgisayar adı için değeri ayarlamanız gerekir. Bunu, istenen ana bilgisayar adını **rol** öğesinin **VMName** özniteliğine ekleyerek yapabilirsiniz. **VMName** özniteliğinin değeri, her rol örneğinin ana bilgisayar adı için temel olarak kullanılır. Örneğin, **VMName** *WebRole* ise ve bu rolün üç örneği varsa, örneklerin ana bilgisayar adları *webrole0*, *webrole1* ve *webrole2* olacaktır. Bir sanal makinenin ana bilgisayar adı sanal makine adına göre doldurulduğundan, yapılandırma dosyasındaki sanal makineler için bir konak adı belirtmeniz gerekmez. Microsoft Azure hizmeti yapılandırma hakkında daha fazla bilgi için bkz [. Azure hizmet yapılandırma şeması (. cscfg dosyası)](/previous-versions/azure/reference/ee758710(v=azure.100))

## <a name="viewing-hostnames"></a>Ana bilgisayar adlarını görüntüleme
Aşağıdaki araçlardan herhangi birini kullanarak, sanal makinelerin ve rol örneklerinin ana bilgisayar adlarını bir bulut hizmetinde görüntüleyebilirsiniz.

### <a name="service-configuration-file"></a>Hizmet yapılandırma dosyası
Dağıtılmış bir hizmetin hizmet yapılandırma dosyasını, Azure portal hizmetin **Yapılandır** dikey penceresinden indirebilirsiniz. Daha sonra, konak adını görmek için **rol adı** öğesinin **VMName** özniteliğini bulabilirsiniz. Bu ana bilgisayar adının, her rol örneğinin ana bilgisayar adı için temel olarak kullanıldığını aklınızda bulundurun. Örneğin, **VMName** *WebRole* ise ve bu rolün üç örneği varsa, örneklerin ana bilgisayar adları *webrole0*, *webrole1* ve *webrole2* olacaktır.

### <a name="remote-desktop"></a>Uzak Masaüstü
Sanal makinelerinize veya rol örneklerine yönelik uzak masaüstü (Windows), Windows PowerShell uzaktan iletişim (Windows) veya SSH (Linux ve Windows) bağlantılarını etkinleştirdikten sonra, etkin bir uzak masaüstü bağlantısından ana bilgisayar adını çeşitli yollarla görüntüleyebilirsiniz:

* Komut isteminde veya SSH terminalinde hostname yazın.
* Komut istemine ipconfig/all yazın (yalnızca Windows).
* Bilgisayar adını sistem ayarlarında görüntüleyin (yalnızca Windows).

### <a name="azure-service-management-rest-api"></a>Azure hizmet yönetimi REST API
REST istemcisinde şu yönergeleri izleyin:

1. Azure portal bağlanmak için bir istemci sertifikasına sahip olduğunuzdan emin olun. İstemci sertifikası almak için [nasıl yapılır: yayımlama ayarları ve abonelik bilgilerini indirme ve Içeri aktarma](/previous-versions/dynamicsnav-2013/dn385850(v=nav.70))bölümünde sunulan adımları izleyin. 
2. X-MS-Version adlı bir başlık girişini 2013-11-01 değeriyle ayarlayın.
3. Şu biçimde bir istek gönder: https: \/ /Management.Core.Windows.net/ \<subscrition-id\> /Services/hostedservices/ \<service-name\> ? embed-Detail = true
4. Her **Roleınstance** öğesi için **hostname** öğesini arayın.

> [!WARNING]
> Ayrıca, **ınternaldnssuffix** öğesini denetleyerek veya Uzak Masaüstü oturumunda (Windows) bir komut isteminde ipconfig/all komutunu çalıştırarak veya bir SSH terminalinde (Linux) cat/etc/resolv.exe ' i çalıştırarak bulut hizmetinizin iç etki alanı sonekini da görüntüleyebilirsiniz.
> 
> 

## <a name="modifying-a-hostname"></a>Ana bilgisayar adını değiştirme
Değiştirilen bir hizmet yapılandırma dosyasını karşıya yükleyerek veya bilgisayarı uzak Masaüstü oturumundan yeniden adlandırarak herhangi bir sanal makine veya rol örneği için ana bilgisayar adını değiştirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
[Ad çözümlemesi (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Azure hizmet yapılandırma şeması (. cscfg)](/previous-versions/azure/reference/ee758710(v=azure.100))

[Azure sanal ağ yapılandırma şeması](/previous-versions/azure/reference/jj157100(v=azure.100))

[Ağ yapılandırma dosyalarını kullanarak DNS ayarlarını belirtme](/previous-versions/azure/virtual-network/virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file)