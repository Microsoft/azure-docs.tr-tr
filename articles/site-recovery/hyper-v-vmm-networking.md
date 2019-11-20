---
title: Azure Site Recovery ile ikincil bir siteye yük devretmeden sonra IP adresleme ayarlama
description: Azure Site Recovery ile olağanüstü durum kurtarma ve yük devretme işleminden sonra ikincil bir şirket içi sitede VM 'lere bağlanmak için IP adreslemesini nasıl ayarlayabileceğinizi açıklar.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: a61f7ff69e648262eb721eb61a98b09dbbee924c
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961436"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Yük devretmeden sonra ikincil şirket içi bir siteye bağlanmak için IP adresi ayarlama

System Center Virtual Machine Manager (VMM) bulutlarındaki Hyper-V VM 'lerinin ikincil bir siteye yük devretme yaptıktan sonra çoğaltma VM 'lerine bağlanmanız gerekir. Bu makale, bunu yapmanıza yardımcı olur. 

## <a name="connection-options"></a>Bağlantı seçenekleri

Yük devretmeden sonra çoğaltma VM 'Leri için IP adreslemesini işlemek için birkaç yol vardır: 

- **Yük devretmeden sonra aynı IP adresini sakla**: Bu senaryoda, çoğaltılan VM Birincil VM Ile aynı IP adresine sahiptir. Bu, yük devretmeden sonra ağla ilgili sorunları basitleştirir, ancak bazı altyapı çalışmaları gerektirir.
- **Yük devretmeden sonra farklı bır IP adresi kullan**: Bu senaryoda VM yük devretmeden sonra yenı bir IP adresi alır. 
 

## <a name="retain-the-ip-address"></a>IP adresini sakla

Birincil siteden IP adreslerini sürdürmek istiyorsanız, ikincil siteye yük devretmeden sonra şunları yapabilirsiniz:

- Birincil ve ikincil siteler arasında uzatılmış bir alt ağ dağıtın.
- Birincil siteden ikincil siteye tam alt ağ yük devretmesi gerçekleştirin. IP adreslerinin yeni konumunu belirtmek için yolları güncelleştirmeniz gerekir.


### <a name="deploy-a-stretched-subnet"></a>Uzatılmış bir alt ağ dağıtma

Uzatılmış bir yapılandırmada alt ağ, hem birincil hem de ikincil sitelerde aynı anda kullanılabilir. Uzatılmış bir alt ağda, bir makineyi ve IP (katman 3) adres yapılandırmasını ikincil siteye taşıdığınızda, ağ trafiği otomatik olarak yeni konuma yönlendirir. 

- Bir katman 2 (veri bağlantısı katmanı) perspektifinden, uzatılmış bir VLAN 'ı yönetebilen ağ donanımlarının olması gerekir.
- VLAN 'ı genişleterek olası hata etki alanı her iki siteye de genişletilir. Bu tek hata noktası haline gelir. Böyle bir senaryoda, büyük olasılıkla bir yayın fırtınası gibi bir olayı yalıtameyebilirsiniz. 


### <a name="fail-over-a-subnet"></a>Bir alt ağ üzerinden yük devretme

Uzatılmış alt ağın avantajlarını gerçekten uzatmadan elde etmek için tüm alt ağın yükünü devrederde olursunuz. Bu çözümde, bir alt ağ, kaynak veya hedef sitede mevcuttur, ancak her ikisi de aynı anda kullanılamaz.

- Yük devretme durumunda IP adresi alanını korumak için, alt ağları bir siteden diğerine taşımak üzere yönlendirici altyapısı için program aracılığıyla düzenleme yapabilirsiniz.
- Yük devretme gerçekleştiğinde, alt ağlar ilişkili VM 'lerle birlikte taşınır.
- Bu yaklaşımın başlıca dezavantajı, bir başarısızlık durumunda tüm alt ağı taşımanız gerekir.

#### <a name="example"></a>Örnek

Aşağıda alt ağ yük devretmesinin bir örneği verilmiştir. 

- Yük devretmeden önce birincil sitede 192.168.1.0/24 alt ağı üzerinde çalışan uygulamalar vardır.
- Yük devretme sırasında, bu alt ağdaki tüm VM 'Ler ikincil siteye devredilmez ve IP adreslerini korurlar. 
- Tüm siteler arasındaki yolların, 192.168.1.0/24 alt ağındaki tüm VM 'Lerin artık ikincil siteye taşındığı gerçeğini yansıtacak şekilde değiştirilmesi gerekir.

Aşağıdaki grafiklerde, yük devretmeden önce ve sonra alt ağlar gösterilmektedir.


**Yük devretmeden önce**

![Yük devretmeden önce](./media/hyper-v-vmm-networking/network-design2.png)

**Yük devretmeden sonra**

![Yük devretmeden sonra](./media/hyper-v-vmm-networking/network-design3.png)

Yük devretmeden sonra, Site Recovery VM 'deki her bir ağ arabirimi için bir IP adresi ayırır. Adres, her bir sanal makine örneği için ilgili ağdaki statik IP adresi havuzundan ayrılır.

- İkincil sitedeki IP adresi havuzu kaynak siteyle aynı ise, Site Recovery çoğaltma sanal makinesine aynı IP adresini (kaynak VM 'nin) ayırır. IP adresi VMM 'de ayrılmıştır, ancak Hyper-V konağında yük devretme IP adresi olarak ayarlanmadı. Hyper-v konağındaki yük devretme IP adresi, yük devretmeden hemen önce ayarlanır.
- Aynı IP adresi yoksa Site Recovery havuzdan başka bir kullanılabilir IP adresi ayırır.
- VM 'Ler DHCP kullanıyorsa Site Recovery IP adreslerini yönetmez. İkincil sitedeki DHCP sunucusunun, kaynak siteyle aynı aralıktan adresler ayırabilmesi gerektiğini denetlemeniz gerekir.

### <a name="validate-the-ip-address"></a>IP adresini doğrulama

Bir VM için korumayı etkinleştirdikten sonra, VM 'ye atanan adresi doğrulamak için aşağıdaki örnek betiği kullanabilirsiniz. Bu IP adresi, yük devretme IP adresi olarak ayarlanır ve yük devretme sırasında VM 'ye atanır:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Farklı bir IP adresi kullanın

Bu senaryoda yük devretme yapan VM 'lerin IP adresleri değişir. Bu çözümün dezavantajı gereken bakımda.  DNS ve önbellek girişlerinin güncellenmesi gerekebilir. Bu, şu şekilde azaltılan kapalı kalma süresine yol açabilir:

- İntranet uygulamaları için düşük TTL değerlerini kullanın.
- DNS sunucusunun zamanında güncelleştirilmesi için Site Recovery kurtarma planında aşağıdaki betiği kullanın. Dinamik DNS kaydı kullanırsanız betiğe gerek yoktur.

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Örnek 

Bu örnekte, birincil ve ikincil sitelerde farklı IP adresliyoruz ve birincil veya kurtarma sitesinde barındırılan uygulamalara erişilebileceği üçüncü bir site vardır.

- Yük devretmeden önce, uygulamalar birincil sitede 192.168.1.0/24 alt ağ olarak barındırılır.
- Yük devretmeden sonra, uygulamalar ikincil sitede 172.16.1.0/24 alt ağında yapılandırılır.
- Üç site da birbirine erişebilir.
- Yük devretmeden sonra, uygulamalar kurtarma alt ağına geri yüklenir.
- Bu senaryoda, tüm alt ağın yükünü devretmek ve VPN veya ağ yollarını yeniden yapılandırmak için herhangi bir değişiklik yapılması gerekmez. Yük devretme ve bazı DNS güncelleştirmeleri, uygulamaların erişilebilir kalacağından emin olun.
- DNS dinamik güncelleştirmelere izin verecek şekilde yapılandırıldıysa VM 'Ler, yük devretmeden sonra başlatıldığında yeni IP adresini kullanarak kendilerini kaydeder.

**Yük devretmeden önce**

![Farklı IP adresi-yük devretmeden önce](./media/hyper-v-vmm-networking/network-design10.png)

**Yük devretmeden sonra**

![Farklı IP adresi-yük devretme sonrası](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Sonraki adımlar

[Yük devretme çalıştırma](hyper-v-vmm-failover-failback.md)

