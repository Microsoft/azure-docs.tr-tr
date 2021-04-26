---
title: Azure Service Fabric ağ en iyi uygulamaları
description: Azure Service Fabric kullanarak ağ bağlantısını yönetmeye yönelik kurallar ve tasarım konuları.
author: chrpap
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: chrpap
ms.openlocfilehash: de6a80f73a97a5732f00e10107fff2330038210a
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122025"
---
# <a name="networking"></a>Ağ

Azure Service Fabric kümelerini oluşturup yönetirken, düğümleriniz ve uygulamalarınız için ağ bağlantısı sağlayabilirsiniz. Ağ kaynakları IP adresi aralıklarını, sanal ağları, yük dengeleyicileri ve ağ güvenlik gruplarını içerir. Bu makalede, bu kaynaklar için en iyi yöntemleri öğreneceksiniz.

Şu özellikleri kullanan kümeler oluşturmayı öğrenmek için Azure [Service Fabric ağ düzenlerini](./service-fabric-patterns-networking.md) gözden geçirin: var olan sanal ağ veya alt ağ, STATIK genel IP adresi, yalnızca iç yük dengeleyici veya iç ve dış yük dengeleyici.

## <a name="infrastructure-networking"></a>Altyapı ağı
Kaynak Yöneticisi şablonunuzda *Enableivme ağ* özelliğini bildirerek sanal makinenizin performansını hızlandırılmış ağ ile en üst düzeye çıkarın. Aşağıdaki kod parçacığı, hızlandırılmış ağ sağlayan bir sanal makine ölçek kümesi Networkınterfaceconfigurations ' dir:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
Service Fabric küme, [hızlandırılmış ağ Ile Linux](../virtual-network/create-vm-accelerated-networking-cli.md)üzerinde sağlanabilir ve [hızlandırılmış ağ ile Windows](../virtual-network/create-vm-accelerated-networking-powershell.md)'u kullanabilir.

Hızlandırılmış ağ, Azure sanal makine serisi SKU 'Ları için desteklenir: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 ve MS/MMS. Hızlandırılmış ağ, Service Fabric bir Windows kümesi için 01/23/2019 Standard_DS8_v3 SKU 'SU kullanılarak başarıyla test edildi ve Service Fabric Linux kümesi için 01/29/2019 üzerinde Standard_DS12_v2 kullanılıyor. Hızlandırılmış ağın en az 4 vCPU gerektirdiğini lütfen unutmayın. 

Mevcut bir Service Fabric kümesinde hızlandırılmış ağı etkinleştirmek için, önce aşağıdakileri gerçekleştirmek üzere bir [sanal makine ölçek kümesi ekleyerek bir Service Fabric kümesini ölçeklendirmelisiniz](./virtual-machine-scale-set-scale-node-type-scale-out.md):
1. Hızlandırılmış ağ etkinken bir NodeType sağlama
2. Hızlandırılmış ağ etkinken hizmetlerinizi ve durumlarını sağlanan NodeType öğesine geçirin

Mevcut bir kümede hızlandırılmış ağı etkinleştirmek için genişleme altyapısının ölçeğini ayarlamak gerekir, çünkü yerinde hızlandırılmış ağın etkinleştirilmesi, [mevcut BIR NIC üzerinde hızlandırılmış ağı etkinleştirmeden önce](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms), bir kullanılabilirlik kümesindeki tüm sanal makinelerin durdurulması ve serbest kalması gerektiğinden kapalı kalma süresine neden olur.

## <a name="cluster-networking"></a>Küme Ağı

* Service Fabric kümeler, [Service Fabric ağ desenlerinde](./service-fabric-patterns-networking.md)özetlenen adımları izleyerek mevcut bir sanal ağa dağıtılabilir.

* Ağ güvenlik grupları (NSG 'ler), gelen ve giden trafiği kümeyle kısıtlamak için düğüm türleri için önerilir. NSG 'de gerekli bağlantı noktalarının açık olduğundan emin olun. 

* Service Fabric sistem hizmetlerinin bulunduğu birincil düğüm türü, dış yük dengeleyici aracılığıyla gösterilmemelidir ve bir [iç yük dengeleyici](./service-fabric-patterns-networking.md#internal-only-load-balancer) tarafından gösterilebilir.

* Kümeniz için [statik bir genel IP adresi](./service-fabric-patterns-networking.md#static-public-ip-address-1) kullanın.

## <a name="network-security-rules"></a>Ağ güvenlik kuralları

Aşağıdaki temel kurallar, Azure yönetilen Service Fabric kümesinin güvenlik kilidi için en düşük gerekliliktir. Aşağıdaki bağlantı noktalarını açma veya IP/URL 'YI onaylama işlemi, kümenin düzgün çalışmasını engeller ve desteklenmiyor olabilir. Bu kural sayesinde, [Otomatik işletim sistemi görüntüsü yükseltmelerini](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)kullanmak için kesinlikle gerekli değildir, aksi takdirde ek bağlantı noktalarının açılması gerekir.

### <a name="inbound"></a>Gelen 
|Öncelik   |Name               |Bağlantı noktası        |Protokol  |Kaynak             |Hedef       |Eylem   
|---        |---                |---         |---       |---                |---               |---
|3900       |Azure              |19080       |TCP       |İnternet           |VirtualNetwork    |İzin Ver
|3910       |İstemci             |19000       |TCP       |İnternet           |VirtualNetwork    |İzin Ver
|3920       |Küme            |1025-1027   |TCP       |VirtualNetwork     |VirtualNetwork    |İzin Ver
|3930       |Kısa ömürlü          |49152-65534 |TCP       |VirtualNetwork     |VirtualNetwork    |İzin Ver
|3940       |Uygulama        |20000-30000 |TCP       |VirtualNetwork     |VirtualNetwork    |İzin Ver
|3950       |SMB                |445         |TCP       |VirtualNetwork     |VirtualNetwork    |İzin Ver
|3960       |RDP                |3389-3488   |TCP       |İnternet           |VirtualNetwork    |Reddet
|3970       |SSH                |22          |TCP       |İnternet           |VirtualNetwork    |Reddet
|3980       |Özel uç nokta    |80          |TCP       |İnternet           |VirtualNetwork    |İzin Ver
|4100       |Gelen engelle      |443         |Herhangi biri       |Herhangi biri                |Herhangi biri               |İzin Ver

Gelen güvenlik kuralları hakkında daha fazla bilgi:

* **Azure**. Bu bağlantı noktası, kümenize gözatıp yönetmek için Service Fabric Explorer tarafından kullanılır ve Azure Yönetim Portalı görüntülenmek üzere kümeniz hakkındaki bilgileri sorgulamak için Service Fabric kaynak sağlayıcısı tarafından da kullanılır. Bu bağlantı noktasına Service Fabric kaynak sağlayıcısından erişilemiyorsa, Azure portal ' de ' düğümler bulunamadı ' veya ' Upgradeservicenoteriþ' gibi bir ileti görürsünüz ve düğüm ve uygulama listeniz boş görünür. Bu, Azure Yönetim Portalı kümenizin görünürlüğünü kullanmak isterseniz, yük dengeleyicinizin genel bir IP adresi kullanıma sunması ve NSG 'nizin gelen 19080 trafiğine izin vermelidir.  

* **İstemci**. REST/PowerShell/CLı gibi API 'Ler için istemci bağlantı uç noktası. 

* **Küme**. Düğümler arası iletişim için kullanılır; hiçbir şekilde engellenmemelidir.

* **Kısa ömürlü**. Service Fabric, bu bağlantı noktalarının bir parçasını uygulama bağlantı noktaları olarak kullanır ve kalan işletim sistemi için kullanılabilir. Ayrıca, bu aralığı işletim sisteminde mevcut olan aralığa eşler, bu nedenle tüm amaçlar için buradaki örnekte verilen aralıkları kullanabilirsiniz. Başlangıç ve bitiş bağlantı noktaları arasındaki farkın en az 255 olduğundan emin olun. Bu fark çok düşükse çakışmalar ile karşılaşabilirsiniz. bu Aralık işletim sistemi ile paylaşılmıştır. Yapılandırılmış dinamik bağlantı noktası aralığını görmek için, *netsh int IPv4 öğesini çalıştırın dinamik bağlantı noktası TCP 'yi göster*. Bu bağlantı noktaları, Linux kümeleri için gerekli değildir.

* **Uygulama**. Uygulama bağlantı noktası aralığı uygulamalarınızın uç nokta gereksinimini kapsayacak kadar büyük olmalıdır. Bu Aralık, makinedeki dinamik bağlantı noktası aralığından, yani ephemeralPorts, yapılandırmada ayarlandığı şekilde özel olmalıdır. Service Fabric, yeni bağlantı noktaları gerektiğinde bu bağlantı noktalarını kullanır ve bu bağlantı noktalarının düğümlerde güvenlik duvarını açma işlemini gerçekleştirir.

* **SMB**. İsteğe bağlı, çalışma zamanı sürüm 7.1 +, varsayılan olarak SMB kullanmaz. SMB protokolü, ımakoya hizmeti tarafından iki senaryo için kullanılıyor. Bu bağlantı noktası, paketleri düğümler tarafından ımagesden yüklemek ve bunları çoğaltmalar arasında çoğaltmak için gereklidir. 

* **RDP**. Internet veya VirtualNetwork for sıçrama kutusu senaryolarında RDP gerekliyse isteğe bağlı. 

* **SSH**. Internet veya VirtualNetwork for sıçrama kutusu senaryolarında SSH gerekliyse isteğe bağlı.

* **Özel uç nokta**. Uygulamanıza internet erişimi olan bir uç noktayı etkinleştirme örneği.

### <a name="outbound"></a>Giden

|Öncelik   |Name               |Bağlantı noktası        |Protokol  |Kaynak             |Hedef       |Eylem   
|---        |---                |---         |---       |---                |---               |---
|3900       |Ağ            |Herhangi biri         |TCP       |VirtualNetwork     |VirtualNetwork    |İzin Ver
|3910       |Kaynak sağlayıcısı  |443         |TCP       |VirtualNetwork     |ServiceFabric     |İzin Ver
|3920       |Yükseltme            |443         |TCP       |VirtualNetwork     |İnternet          |İzin Ver
|3950       |Giden trafiği engelle     |Herhangi biri         |Herhangi biri       |Herhangi biri                |Herhangi biri               |Reddet

Giden güvenlik kuralları hakkında daha fazla bilgi:

* **Ağ**. Alt ağlar ve başka bir sanal ağa yönelik iletişim kanalı.

* **Kaynak sağlayıcısı**. Service Fabric kaynak sağlayıcısı tarafından tüm ARM dağıtımlarını yürütmek için UpgradeService tarafından bağlantı.

* **Yükseltme**. Download.microsoft.com adresini kullanan yükseltme hizmeti bitleri almak için bu, kurulum, yeniden görüntü oluşturma ve çalışma zamanı yükseltmeleri için gereklidir. Hizmet dinamik IP adresleriyle çalışır. "İç yalnızca bir" yük dengeleyicinin senaryosunda, bağlantı noktası 443 ' e giden trafiğe izin veren bir kuralla ek bir dış yük dengeleyici eklenmelidir. İsteğe bağlı olarak, bu bağlantı noktası başarılı bir kurulumdan sonra engellenebilir, ancak bu durumda yükseltme paketinin düğümlere dağıtılması veya bağlantı noktasının kısa bir süre için açılması gerekir, daha sonra el ile yükseltme gerekir.

Güvenlik kilitiyle ilgili sorunları izlemek için [NSG akış günlüğü](../network-watcher/network-watcher-nsg-flow-logging-overview.md) ve [Trafik Analizi](../network-watcher/traffic-analytics.md) ile Azure Güvenlik Duvarı 'nı kullanın. [NSG ile SERVICE fabrıc](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) ARM şablonunun başlaması iyi bir örnektir. 


## <a name="application-networking"></a>Uygulama Ağı

* Windows kapsayıcı iş yüklerini çalıştırmak için, hizmetten hizmete iletişim kurmak üzere [açık ağ modu](./service-fabric-networking-modes.md#set-up-open-networking-mode) ' nu kullanın.

* 80 veya 443 gibi yaygın uygulama bağlantı noktalarını kullanıma sunmak için [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) gibi bir ters proxy veya [Service Fabric ters proxy](./service-fabric-reverseproxy.md) kullanın.

* Azure bulut depolama alanından temel katmanları çekmeden, AIR özellikli makinelerde barındırılan Windows kapsayıcıları için, Docker Daemon 'daki [--Allow-nondağıtılabilir-yapay](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) bayrağını kullanarak yabancı katman davranışını geçersiz kılın.

## <a name="next-steps"></a>Sonraki adımlar

* VM 'lerde veya Windows Server çalıştıran bilgisayarlarda küme oluşturma: [Windows Server için Service Fabric kümesi oluşturma](service-fabric-cluster-creation-for-windows-server.md)
* VM 'lerde veya Linux çalıştıran bilgisayarlarda küme oluşturma: [Linux kümesi oluşturma](service-fabric-cluster-creation-via-portal.md)
* [Service Fabric destek seçenekleri](service-fabric-support.md) hakkında bilgi edinin

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
