---
title: HPC ve GPU VM 'Leri ile ilgili bilinen sorunları giderme-Azure sanal makineleri | Microsoft Docs
description: Azure 'da HPC ve GPU VM boyutlarıyla ilgili bilinen sorunları giderme hakkında bilgi edinin.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f5bdae17126048da153f70bf27609bcc4b92fe21
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599596"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>H serisi ve N serisi VM’lerdeki bilinen sorunlar

Bu makale, [H serisi](../../sizes-hpc.md) ve [N SERISI](../../sizes-gpu.md) HPC ve GPU VM 'lerini kullanırken en son yaygın sorunları ve çözümlerini listemaya çalışır.

## <a name="qp0-access-restriction"></a>qp0 erişim kısıtlaması

Güvenlik açıklarına neden olan alt düzey donanım erişimini engellemek için, 0 kuyruk çifti Konuk VM 'Ler tarafından erişilemez. Bu yalnızca, yalnızca ConnectX InfiniBand NIC yönetimi ile ilişkili eylemleri ve ibdiagnet gibi bazı InfiniBand tanılamayı (son kullanıcı uygulamaları değil) etkilemelidir.

## <a name="mofed-installation-on-ubuntu"></a>Ubuntu 'da MOFED yüklemesi
Çekirdekler sürümüne ve daha yeni bir sürüme sahip Ubuntu-18,04 tabanlı Market VM görüntülerinde `5.4.0-1039-azure #42` , bazı durumlarda, bazı durumlarda VM önyüklemesi süresi en fazla 30 dakika kadar bir artış olduğundan, bazı eski Mellanox 'ler uyumsuz olur. Bu, hem Mellanox OFED Versions 5.2-1.0.4.0 ve 5.2-2.2.0.0 sürümleri için bildirilmiştir. Bu sorun, Mellanox OFED 5.3-1.0.0.1 ile çözümlenir.
Uyumsuz olarak kullanılması gerekiyorsa, bir çözüm **kurallı: UbuntuServer: 18_04-LTS-Gen2:18.04.202101290** Market VM görüntüsünü veya daha eski bir sürümü kullanarak çekirdeği güncelleştirmemelidir.

## <a name="mpi-qp-creation-errors"></a>MPı QP oluşturma hataları
Herhangi bir MPI iş yüklerini çalıştırmak için, aşağıda gösterildiği gibi InfiniBand QP oluşturma hataları oluşturulur, sanal makineyi yeniden başlatmayı ve iş yükünü yeniden denemeyi öneririz. Bu sorun gelecekte düzeltilecektir.

```bash
ib_mlx5_dv.c:150  UCX  ERROR mlx5dv_devx_obj_create(QP) failed, syndrome 0: Invalid argument
```

Sorun aşağıdaki gibi gözlemleniyorsa en fazla sıra çifti sayısı değerlerini doğrulayabilirsiniz.
```bash
[user@azurehpc-vm ~]$ ibv_devinfo -vv | grep qp
max_qp: 4096
```

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>HB, HC, HBv2 ve NDv2 üzerinde hızlandırılmış ağ

[Azure hızlandırılmış ağ](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) , artık RDMA ve InfiniBand özellıklı ve SR-ıOV etkinleştirilmiş VM boyutları [HB](../../hb-series.md), [HC](../../hc-series.md), [HBv2](../../hbv2-series.md)ve [NDv2](../../ndv2-series.md)' de kullanılabilir. Bu özellik artık, Azure Ethernet ağı üzerinden Gelişmiş (en fazla 30 Gbps) ve gecikme süreleriyle geliştirilmiştir. Bu, InfiniBand ağı üzerinden RDMA özelliğinden ayrı olsa da, bu özellik için bazı platform değişiklikleri InfiniBand üzerinde iş çalıştırırken belirli MPı uygulamalarının davranışlarını etkileyebilir. Özellikle, bazı sanal makinelerdeki InfiniBand arabiriminin adı biraz farklı olabilir (mlx5_1 önceki mlx5_0 aksine) ve bu, özellikle UCX arabirimi (genellikle OpenMPI ve HPC-X ile) kullanılırken MPı komut satırları için kullanılabilir olmasını gerektirebilir. Şu anda en basit çözüm, CentOS-HPC VM görüntülerinde en son HPC-X ' i kullanmak veya gerekmiyorsa hızlandırılmış ağı devre dışı bırakmak olabilir.
Bu konuda daha fazla bilgi, bu [Techcommunity makalesinde](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965) , gözlemlenen sorunların nasıl ele alınacağını açıklayan yönergeler sunulmaktadır.

## <a name="infiniband-driver-installation-on-non-sr-iov-vms"></a>SR-ıOV olmayan VM 'lerde InfiniBand sürücü yüklemesi

Şu anda H16r, H16mr ve NC24r, SR-ıOV etkin değil. InfiniBand Stack Bifurcation hakkındaki bazı ayrıntılar [burada](../../sizes-hpc.md#rdma-capable-instances)yer alır.
InfiniBand, SR-ıOV etkin VM boyutları üzerinde, SR-ıOV olmayan VM boyutları ND sürücüleri gerektirdiğinden, ALıNAN sürücülerle yapılandırılabilir. Bu ıB desteği [, CentOS, RHEL ve Ubuntu](configure.md)için uygun şekilde sunulmaktadır.

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>H serisi ve N serisi VM 'lerde Ubuntu ile Cloud-init ile yinelenen MAC

IB arabirimini getirmeye çalıştığı için Ubuntu VM görüntülerinde Cloud-init ile ilgili bilinen bir sorun vardır. Bu durum VM önyüklemesi üzerinde veya genelleştirdikten sonra bir VM görüntüsü oluşturmaya çalışırken gerçekleşebilir. VM önyükleme günlükleri şöyle bir hata gösterebilir:
```console
“Starting Network Service...RuntimeError: duplicate mac found! both 'eth1' and 'ib0' have mac”.
```

Bu ' Cloud-init on Ubuntu ile yinelenen MAC, bilinen bir sorundur. Bu, yeni çekirdekler ile çözümlenir. Sorun ile karşılaşılırsa geçici çözüm:
1) (Ubuntu 18,04) Market VM görüntüsünü dağıtma
2) IB 'yi etkinleştirmek için gerekli yazılım paketlerini yükler ([burada yönerge](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351))
3) EnableRDMA = y öğesini değiştirmek için waagent. conf dosyasını Düzenle
4) Bulutta ağı devre dışı bırakma-init
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) MAC 'i kaldırmak için, Cloud-init tarafından oluşturulan Netplan ağ yapılandırma dosyasını Düzenle
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="dram-on-hb-series-vms"></a>DRAM on HB Serisi VM 'Ler

HB Serisi VM 'Ler Şu anda Konuk VM 'lere yalnızca 228 GB RAM kullanıma sunabilir. Benzer şekilde, HBv2 üzerinde 458 GB ve HBv3 VM 'lerinde 448 GB. Bunun nedeni, sayfaların Konuk sanal makine için ayrılmış olan (NUMA etki alanları) yerel DRAM 'ye atanmasını engellemek için Azure hiper yöneticisinin bilinen bir sınırlamasıdır.

## <a name="gss-proxy"></a>GSS proxy

GSS proxy 'Si, SP1 ile kullanıldığında önemli bir performans ve yanıt verme cezası olarak bildirimde bulunan CentOS/RHEL 7,5 ' de bilinen bir hataya sahiptir. Bu, ile azaltılabilir:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Önbellek Temizleme

HPC sistemlerinde, bir sonraki kullanıcıya aynı düğüm atanmadan önce, bir iş bittikten sonra belleği temizlemek genellikle yararlı olur. Linux 'ta uygulama çalıştırdıktan sonra, herhangi bir uygulama çalıştırmamasına rağmen arabellek belleğinizin arttığı sırada kullanılabilir belleğinizin azaldığı fark edebilirsiniz.

![Temizlemeden önce komut isteminin ekran görüntüsü](./media/known-issues/cache-cleaning-1.png)

Kullanmak, `numactl -H` belleğin hangi numlik ile arabelleğe alındığına (muhtemelen büyük olasılıkla) sahip olduğunu gösterir. Linux 'ta kullanıcılar, arabelleğe alınmış veya önbelleğe alınmış belleği ' ücretsiz ' olarak döndürmek için önbellekleri üç yolla temizleyebilir. Kök olmanız veya sudo izinleriniz olması gerekir.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Temizlemeden sonra komut isteminin ekran görüntüsü](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Çekirdek uyarıları

Linux altında bir HB Serisi VM 'yi önyüklerken aşağıdaki çekirdek uyarısı iletilerini yoksayabilirsiniz. Bunun nedeni, Azure hiper yöneticinin zaman içinde ele alacak bilinen bir sınırlamasıdır.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```


## <a name="next-steps"></a>Sonraki adımlar

- Performans ve ölçeklenebilirlik için iş yüklerini en iyi şekilde yapılandırma hakkında bilgi edinmek için [HB Serisi genel bakış](hb-series-overview.md) ve [HC Serisi genel bakışı](hc-series-overview.md) gözden geçirin.
- [Azure Işlem Tech Community bloglarında](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)en son Duyurular, HPC iş yükü örnekleri ve performans sonuçları hakkında bilgi edinin.
- HPC iş yüklerini çalıştırmanın daha üst düzey mimari görünümü için bkz. [Azure 'Da yüksek performanslı bilgi işlem (HPC)](/azure/architecture/topics/high-performance-computing/).
