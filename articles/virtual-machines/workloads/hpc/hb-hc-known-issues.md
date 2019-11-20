---
title: HB serisi ve HC serisi sanal makineler - Azure sanal makineleri ile ilgili bilinen sorunlar | Microsoft Docs
description: Azure'da HB serisi VM boyutları ile ilgili bilinen sorunlar hakkında bilgi edinin.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 8d4b57fb2fee3849e102868c86fe3cab465fc70d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707791"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>HB serisi ve HC serisi sanal makineler ile ilgili bilinen sorunlar

Bu makalede, HB serisi ve HC serisi VM'ler kullanırken en sık karşılaşılan sorunlar ve çözümleri sağlar.

## <a name="dram-on-hb-series"></a>DRAM HB serisi hakkında

HB serisi VM'ler, şu anda yalnızca 228 GB RAM Konuk vm'lerinin getirebilir. Azure hiper Yöneticisi, bilinen bir sınırlama nedeniyle yerel DRAM, AMD Konuk VM için ayrılmış CCX's (NUMA etki alanları) atanan sayfaları önlemek için budur.

## <a name="accelerated-networking"></a>Hızlandırılmış Ağ

Şu anda Azure hızlandırılmış ağ etkin değil, ancak Önizleme dönemi ilerledikçe olur. Bu özellik desteklendiğinde, müşterilerin bilgilendireceğiz.

## <a name="qp0-access-restriction"></a>qp0 erişimi kısıtlama

Kuyruk çifti, güvenlik açıklarına neden olabilir, alt düzey donanım erişimi engellemek için 0 Konuk VM'ler sağlayan erişilebilir değil. Bu, yalnızca genellikle yönetim ConnectX-5 NIC ile ilişkili ve ibdiagnet, ancak son kullanıcı uygulamaları kendilerini gibi bazı InfiniBand tanılama çalıştırma eylemleri etkiler.

## <a name="ud-transport"></a>UD taşıma

Başlatma sırasında dinamik olarak bağlı Aktarım (DCT) HB ve HC serisi desteklemez. Zaman içinde DCT desteği uygulanacaktır. Güvenilir bir bağlantı (RC) ve güvenilir veri birimi (UD) aktarımları desteklenir.

## <a name="gss-proxy"></a>GSS Proxy

Bilinen hata GSS Proxy önemli performans ve yanıt hızını cezası NFS ile kullanıldığında olarak bildirilebilir CentOS/RHEL 7.5 sahiptir. İle azaltılabilir:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Önbellek Temizleme

HPC sistemlerinde sonraki kullanıcı aynı düğümde atanmadan önce bir işi tamamlandıktan sonra belleği temizlemek kullanışlıdır. Linux uygulamaları çalıştırdıktan sonra kullanılabilir bellek sırasında herhangi bir uygulama çalışmıyor rağmen arabellek bellek artış azaltır bulabilirsiniz.

![Komut isteminin ekran görüntüsü](./media/known-issues/cache-cleaning-1.png)

Kullanarak `numactl -H` hangi NUMAnode(s) bellek (büyük olasılıkla tüm ile) arabelleğe gösterir. Linux'ta, kullanıcıların üç önbelleklerinde temizleyebilirsiniz döndürmek için yollar ara belleğe veya önbelleğe alınan 'boşaltmak için '. Kök olması veya sudo izinleri olması gerekir.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Komut isteminin ekran görüntüsü](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Çekirdek uyarıları

Linux altındaki HB serisi VM önyükleme yaparken aşağıdaki çekirdek uyarı iletileri görebilirsiniz.

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

Bu uyarıyı yoksayabilirsiniz. Zaman içinde ele alınacaktır Azure hiper Yöneticisi ile ilgili bilinen bir sınırlama nedeniyle budur.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi edinin [yüksek performanslı bilgi işlem](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) azure'da.
