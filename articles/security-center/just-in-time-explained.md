---
title: Azure Güvenlik Merkezi 'nde tam zamanında sanal makine erişimini anlama
description: Bu belgede, Azure Güvenlik Merkezi 'ndeki tam zamanında VM erişiminin Azure sanal makinelerinize erişimi denetlemenize nasıl yardımcı olduğu açıklanmaktadır
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 9a52596aa0dd5fa7b9a7226d2ae57259dab08d37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93285726"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>Tam zamanında (JIT) VM erişimini anlama

Bu sayfada, Azure Güvenlik Merkezi 'nin tam zamanında (JıT) VM erişimi özelliğinin arkasındaki ilkeler ve önerinin arkasındaki mantık açıklanmaktadır.

Azure portal (Güvenlik Merkezi veya Azure sanal makineleri) veya program aracılığıyla kullanarak sanal makinelerinize JıT uygulama hakkında bilgi edinmek için bkz. [Yönetim bağlantı NOKTALARıNıZı JIT ile koruma](security-center-just-in-time.md).


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>Bir sanal makinede açık yönetim bağlantı noktalarının riski

Tehdit aktörleri, RDP veya SSH gibi açık yönetim bağlantı noktalarıyla erişilebilir makineleri etkin bir şekilde araytırabilirler. Tüm sanal makineleriniz bir saldırı için olası hedeflerdir. Bir VM başarıyla tehlikede olduğunda, ortamınızda daha fazla kaynağa saldırmak için giriş noktası olarak kullanılır.



## <a name="why-jit-vm-access-is-the-solution"></a>JıT VM erişiminin neden çözüm olduğu 

Tüm siber güvenlik önleme teknikleriyle olduğu gibi, amacınız saldırı yüzeyini azaltmanız gerekir. Bu durumda, daha az açık bağlantı noktası, özellikle de yönetim bağlantı noktaları olması anlamına gelir.

Meşru kullanıcılarınız bu bağlantı noktalarını da kullandığından, bunları kapalı tutmak pratik değildir.

Bu dikarma 'yı çözümlemek için Azure Güvenlik Merkezi JıT sunar. JıT ile, sanal makinelerinize gelen trafiği kilitleyerek, gerektiğinde sanal makinelere bağlanmak için kolay erişim sağlarken saldırılara maruz kalma olasılığını azaltabilirsiniz.



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>Ağ güvenlik grupları ve Azure Güvenlik Duvarı ile JıT nasıl çalışır?

Tam zamanında VM erişimini etkinleştirdiğinizde, gelen trafiğin engelleneceğini sanal makine üzerindeki bağlantı noktalarını seçebilirsiniz. Güvenlik Merkezi, [ağ güvenlik grubu](../virtual-network/network-security-groups-overview.md#security-rules) (NSG) ve [Azure Güvenlik duvarı kurallarında](../firewall/rule-processing.md)seçtiğiniz bağlantı noktalarınız için "tüm gelen trafiği reddetme" kurallarının mevcut olmasını sağlar. Bu kurallar, Azure sanal makinelerinizdeki yönetim bağlantı noktalarıyla erişimi kısıtlar ve saldırılardan savunmalarını sağlar. 

Seçilen bağlantı noktaları için başka kurallar zaten varsa, bu mevcut kurallar yeni "tüm gelen trafiği reddetme" kuralları üzerinden önceliklidir. Seçilen bağlantı noktalarında mevcut bir kural yoksa, yeni kurallar NSG ve Azure Güvenlik Duvarı 'nda en üst önceliğe sahip olacaktır.

Bir Kullanıcı bir VM 'ye erişim istediğinde, güvenlik merkezi kullanıcının bu VM için [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) izinlerine sahip olduğunu denetler. İstek onaylanırsa, Güvenlik Merkezi, belirtilen IP adresinden (veya aralığından) seçilen bağlantı noktalarına gelen trafiğe izin vermek için NSG 'ler ve Azure Güvenlik duvarını yapılandırır. Sürenin süresi dolduktan sonra, güvenlik merkezi NSG 'leri önceki durumlarına geri yükler. Zaten kurulan bağlantılar kesintiye uğramaz.

> [!NOTE]
> JıT, Azure [güvenlik duvarı Yöneticisi](../firewall-manager/overview.md)tarafından denetlenen Azure Güvenlik duvarları tarafından korunan VM 'leri desteklemez.




## <a name="how-security-center-identifies-which-vms-should-have-jit-applied"></a>Güvenlik Merkezi 'nin JıT uygulamış olması gereken sanal makineleri nasıl belirlediği

Aşağıdaki diyagramda, desteklenen VM 'lerinizi sınıflandırmaya karar verirken güvenlik merkezi 'nin uyguladığı mantık gösterilmektedir: 

[![Tam zamanında (JıT) sanal makine (VM) Logic Flow](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

Güvenlik Merkezi JıT 'ten faydalanabilir bir makine bulduğunda, bu makineyi önerinin **sağlıksız kaynak** sekmesine ekler. 

![Tam zamanında (JıT) sanal makine (VM) erişim önerisi](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---questions-about-just-in-time-virtual-machine-access"></a>SSS-tam zamanında sanal makine erişimi ile ilgili sorular

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>JıT 'i yapılandırmak ve kullanmak için hangi izinler gereklidir?

JıT, abonelik üzerinde [sunucular Için Azure Defender 'ın](defender-for-servers-introduction.md) etkinleştirilmesini gerektirir. 

**Reader** ve **securityreader** ROLLERININ her ikisi de JIT durumunu ve parametrelerini görüntüleyebilir.

JıT ile çalışabilebilmeniz gereken özel Roller oluşturmak istiyorsanız aşağıdaki tablodaki ayrıntılara ihtiyacınız olacaktır.

> [!TIP]
> Bir VM 'ye JıT erişimi istemesi ve başka bir JıT işlemi gerçekleştirihtiyacı olmayan kullanıcılar için en az ayrıcalıklı bir rol oluşturmak üzere, güvenlik merkezi GitHub topluluk sayfalarından [set-Jleastprivilegedrole betiğini](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) kullanın.

| Bir kullanıcıyı şu şekilde etkinleştirmek için: | Ayarlanacak izinler|
| --- | --- |
|VM için bir JıT ilkesi yapılandırma veya düzenleme | *Bu eylemleri role atayın:*  <ul><li>VM ile ilişkili bir abonelik veya kaynak grubu kapsamında:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> Bir abonelik veya VM kaynak grubu kapsamında: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Bir VM 'ye JıT erişimi isteme | *Bu eylemleri kullanıcıya ata:*  <ul><li>VM ile ilişkili bir abonelik veya kaynak grubu kapsamında:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>VM ile ilişkili bir abonelik veya kaynak grubu kapsamında:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  Bir aboneliğin veya kaynak grubunun veya VM 'nin kapsamında:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  Bir aboneliğin veya kaynak grubunun veya VM 'nin kapsamında:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|JıT ilkelerini okuma| *Bu eylemleri kullanıcıya ata:*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Security/pricings/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>Sonraki adımlar

Bu sayfa, tam zamanında (JıT) sanal makine (VM) erişiminin kullanılması _gerektiğini anlatılmıştır._ 

JIT özellikli sanal makinelerinize erişim isteğinde bulunma ve erişim isteme hakkında bilgi edinmek için nasıl yapılır makalesine ilerleyin:

> [!div class="nextstepaction"]
> [Yönetim bağlantı noktalarınızı JıT ile güvenli hale getirme](security-center-just-in-time.md)