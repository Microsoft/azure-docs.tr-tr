---
title: CloudSimple ile Azure VMware çözümü iş yükü VM 'lerini özel buluta geçirme
description: Şirket içi vCenter 'dan CloudSimple özel bulut vCenter 'a sanal makinelerin nasıl geçirileceği açıklanmaktadır
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87b8a112a319519dbde977ee30136a884137212d
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972671"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>Şirket içi vCenter 'dan özel bulut vCenter ortamına iş yükü VM 'lerini geçirme

Şirket içi veri merkezindeki VM 'Leri CloudSimple özel buluta geçirmek için birkaç seçenek mevcuttur.  Özel bulut VMware vCenter 'a yerel erişim sağlar ve VMware tarafından desteklenen araçlar iş yükü geçişi için kullanılabilir. Bu makalede vCenter geçiş seçeneklerinden bazıları açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Şirket içi veri merkezinizden VM 'lerin ve verilerin geçirilmesi, veri merkezinizden özel bulut ortamınıza ağ bağlantısı gerektirir.  Ağ bağlantısı kurmak için aşağıdaki yöntemlerden birini kullanın:

* Şirket içi ortamınız ile özel bulutunuz arasında [siteden sıteye VPN bağlantısı](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) .
* ExpressRoute, şirket içi ExpressRoute bağlantı hattı ile CloudSimple ExpressRoute devresi arasındaki bağlantıyı Global Reach.

Şirket içi vCenter ortamınızdaki ağ yolu, vMotion kullanılarak VM 'lerin geçirilmesi için kullanılabilir olmalıdır.  Şirket içi vCenter 'daki vMotion ağı yönlendirme yeteneklerine sahip olmalıdır.  Güvenlik duvarınızın, şirket içi vCenter ve özel bulut vCenter arasındaki tüm vMotion trafiğine izin verdiğini doğrulayın. (Özel bulutta, vMotion ağı üzerinde yönlendirme varsayılan olarak yapılandırılır.)

## <a name="migrate-isos-and-templates"></a>IOS ve şablonları geçirme

Özel bulutunuzda yeni sanal makineler oluşturmak için ISOs ve VM şablonlarını kullanın.  Özel bulut vCenter 'larınıza ISOs ve şablonları yüklemek ve bunları kullanılabilir hale getirmek için aşağıdaki yöntemi kullanın.

1. ISO kullanıcı arabiriminden ISO 'yu özel bulut vCenter 'a yükleyin.
2. Özel bulut vCenter 'unuzda [bir içerik kitaplığı yayımlayın](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) :

    1. Şirket içi içerik kitaplığınızı yayımlayın.
    2. Özel bulut vCenter üzerinde yeni bir içerik kitaplığı oluşturun.
    3. Yayımlanan şirket içi içerik kitaplığına abone olun.
    4. Abone olunan içeriğe erişmek için içerik kitaplığını eşitler.

## <a name="migrate-vms-using-powercli"></a>PowerCLI kullanarak VM 'Leri geçirme

Şirket içi vCenter 'dan özel bulut vCenter 'a sanal makineler geçirmek için VMware PowerCLI veya VMware Labs 'den kullanılabilen çapraz vCenter Iş yükü geçiş yardımcı programını kullanın.  Aşağıdaki örnek betik PowerCLI geçiş komutlarını göstermektedir.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Hedef vCenter Server ve ESXi konaklarının adlarını kullanmak için DNS iletmeyi Şirket içinden özel buluta yapılandırın.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>NSX katman 2 VPN kullanarak VM 'Leri geçirme

Bu seçenek, şirket içi VMware ortamınızdan Azure 'daki özel buluta iş yüklerini dinamik olarak geçirmeyi sağlar.  Bu uzatılmış katman 2 ağıyla, şirket içi içindeki alt ağ özel bulutta kullanılabilir olacaktır.  Geçişten sonra VM 'Ler için yeni IP adresi ataması gerekli değildir.

[Katman 2 uzatılmış ağları kullanarak iş yüklerini geçirme](migration-layer-2-vpn.md) bir katman 2 ağını şirket içi ortamınızdan özel buluta uzatmak için bir katman 2 VPN kullanmayı açıklar.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Yedekleme ve olağanüstü durum kurtarma araçlarını kullanarak VM 'Leri geçirme

VM 'lerin özel buluta geçirilmesi, yedekleme/geri yükleme araçları ve olağanüstü durum kurtarma araçları kullanılarak yapılabilir.  Bir üçüncü taraf aracı kullanılarak oluşturulan yedeklerden geri yükleme için bir hedef olarak özel bulutu kullanın.  Özel bulut, VMware SRM veya üçüncü taraf bir aracı kullanılarak olağanüstü durum kurtarma için hedef olarak da kullanılabilir.

Bu araçları kullanma hakkında daha fazla bilgi için aşağıdaki konulara bakın:

* [Veeam B & R kullanarak CloudSimple özel bulutundaki iş yükünü sanal makinelerini yedekleme](backup-workloads-veeam.md)
* [Şirket içi VMware iş yükleri için bir olağanüstü durum kurtarma sitesi olarak CloudSimple özel bulutu ayarlama](disaster-recovery-zerto.md)
