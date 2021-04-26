---
title: CloudSimple tarafından Azure VMware çözümü-şirket içi iş yükleri için özel bulut olağanüstü durum sitesi olarak kullanın
description: CloudSimple özel bulutunuzu şirket içi VMware iş yükleri için olağanüstü durum kurtarma sitesi olarak ayarlamayı açıklar
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5dd1f157984cdb300cc3b375d71654e5da54d1ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898139"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Şirket içi VMware iş yükleri için bir olağanüstü durum kurtarma sitesi olarak CloudSimple özel bulutu ayarlama

CloudSimple özel bulutunuz, bir olağanüstü durum durumunda iş sürekliliği sağlamak üzere şirket içi uygulamalar için bir kurtarma sitesi olarak ayarlanabilir. Kurtarma çözümü, çoğaltma ve düzenleme platformu olarak sanal çoğaltmayı temel alır. Kritik altyapı ve uygulama sanal makineleri, şirket içi vCenter 'ınızdan özel buluta sürekli olarak çoğaltılabilir. Yük devretme testi için özel bulutunuzu kullanabilir ve bir olağanüstü durum sırasında uygulamanızın kullanılabilirliğini sağlayabilirsiniz. Benzer bir yaklaşım, bir kurtarma sitesi tarafından farklı bir konumda korunan bir birincil site olarak özel bulutu ayarlamak için de izlenebilir.

> [!NOTE]
> Olağanüstü durum kurtarma ortamınızı boyutlandırmayla ilgili yönergeler için, [sanal çoğaltmaya yönelik boyutlandırma konularını](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) belgelemek için Zerto bölümüne bakın.

CloudSimple çözümü:

* Özellikle olağanüstü durum kurtarma (DR) için bir veri merkezini ayarlama gereksinimini ortadan kaldırır.
* , Dünya çapındaki coğrafi esnekliği için CloudSimple 'ın dağıtıldığı Azure konumlarından yararlanmanızı sağlar.
* , DR için dağıtım maliyetlerini ve toplam sahiplik maliyetini azaltmaya yönelik bir seçenek sunar.

Çözüm şunları yapmanızı gerektirir:

* Özel bulutunuzda zere uygulamasını yüklemek, yapılandırmak ve yönetmek.
* Özel bulut korumalı site olduğunda, zera için kendi lisanslarınızı belirtin. Şirket içi sitenizin lisanslama için CloudSimple sitesinde çalışır şekilde eşleşmesini sağlayabilirsiniz.

Aşağıdaki şekilde, Zerto çözümünün mimarisi gösterilmektedir.

![Mimari](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>Çözümü dağıtma

Aşağıdaki bölümlerde özel bulutunuzda Zerto sanal çoğaltmasını kullanarak bir DR çözümünün nasıl dağıtılacağı açıklanır.

1. [Önkoşullar](#prerequisites)
2. [CloudSimple özel bulutu 'nda isteğe bağlı yapılandırma](#optional-configuration-on-your-private-cloud)
3. [CloudSimple özel bulutu üzerinde ZVM ve VRA 'yi ayarlama](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Sanal koruma grubunu ayarla](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Önkoşullar

Şirket içi ortamınızdan özel bulutunuzun sanal çoğaltmasını etkinleştirmek için aşağıdaki önkoşulları doldurun.

1. Şirket [içi ağınız ve CloudSimple özel bulutunuz arasında siteden sıteye VPN bağlantısı kurun](set-up-vpn.md).
2. [Özel bulut yönetimi bileşenlerinizin özel bulut DNS sunucularına iletilmesi IÇIN DNS aramasını ayarlayın](on-premises-dns-setup.md).  DNS aramasının iletilmesini etkinleştirmek için, şirket içi DNS sunucunuzda CloudSimple DNS sunucularına yönelik bir iletme bölgesi girişi oluşturun `*.cloudsimple.io` .
3. Şirket içi vCenter bileşenlerinin şirket içi DNS sunucularına iletilmesi için DNS aramasını ayarlayın.  Siteden siteye VPN üzerinden CloudSimple özel bulutunuzda DNS sunucularına ulaşılamamalıdır. Yardım almak için aşağıdaki bilgileri sağlayarak bir [destek isteği](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)gönderebilirsiniz.  

    * Şirket içi DNS etki alanı adı
    * Şirket içi DNS sunucusu IP adresleri

4. Özel bulutunuzda bir Windows Server yükler. Sunucu, Virtual Manager 'ı yüklemek için kullanılır.
5. [CloudSimple ayrıcalıklarınızı ilerletin](escalate-private-cloud-privileges.md).
6. Özel bulut vCenter üzerinde, sanal yönetici için hizmet hesabı olarak kullanılacak yönetim rolüyle yeni bir kullanıcı oluşturun.

### <a name="optional-configuration-on-your-private-cloud"></a>Özel bulutunuzda isteğe bağlı yapılandırma

1. Şirket içi ortamınızdan VM 'Ler için hedef kaynak havuzları olarak kullanılacak özel bulut vCenter 'unuzda bir veya daha fazla kaynak havuzu oluşturun.
2. Şirket içi ortamınızdan VM 'Ler için hedef klasör olarak kullanmak üzere özel bulut vCenter 'unuzda bir veya daha fazla klasör oluşturun.
3. Yük devretme ağı için VLAN oluşturun ve güvenlik duvarı kuralları ayarlayın. Yardım için bir [destek isteği](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) açın.
4. VM 'lerin yük devretmesini test etmek için yük devretme ağı ve test ağı için dağıtılmış bağlantı noktası grupları oluşturun.
5. [DHCP ve DNS sunucularını](dns-dhcp-setup.md) yükler veya özel bulut ortamınızda bir Active Directory etki alanı denetleyicisi kullanın.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>Özel bulutunuzda ZVM ve VRA 'yi ayarlama

1. Özel bulutunuzda Windows Server 'daki sanal yönetici (ZVM) uygulamasını yükler.
2. Önceki adımlarda oluşturulan hizmet hesabını kullanarak ZVM 'de oturum açın.
3. Zerto Virtual Manager için lisanslama ayarlayın.
4. Özel bulutunuzun ESXi konaklarına sanal çoğaltma gereci (VRA) uygulamasını yükler.
5. Özel bulut ZVM 'nizi şirket içi ZVM 'niz ile eşleştirin.

### <a name="set-up-zerto-virtual-protection-group"></a>Sanal koruma grubunu ayarla

1. Yeni bir sanal koruma grubu (VPG) oluşturun ve VPG için önceliği belirtin.
2. İş sürekliliği için koruma gerektiren sanal makineleri seçin ve gerekirse önyükleme sırasını özelleştirin.
3. Özel bulutunuz olarak kurtarma sitesini ve özel bulut kümesi veya oluşturduğunuz kaynak grubu olarak varsayılan kurtarma sunucusunu seçin. Özel bulutunuzdaki kurtarma veri deposu için **Vsandatastore** ' i seçin.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > VM ayarları seçeneğinde tek tek VM 'Ler için konak seçeneğini özelleştirebilirsiniz.

4. Depolama seçeneklerini gereken şekilde özelleştirin.
5. Daha önce oluşturulan dağıtılmış bağlantı noktası grupları olarak yük devretme ağı ve yük devretme testi ağı için kullanılacak kurtarma ağlarını belirtin ve kurtarma betiklerini gerektiği gibi özelleştirin.
6. Gerekirse ayrı VM 'Ler için ağ ayarlarını özelleştirin ve VPG 'yi oluşturun.
7. Çoğaltma tamamlandıktan sonra yük devretmeyi test edin.

## <a name="reference"></a>Başvuru

[Zerto belgeleri](https://www.zerto.com/myzerto/technical-documentation/)
