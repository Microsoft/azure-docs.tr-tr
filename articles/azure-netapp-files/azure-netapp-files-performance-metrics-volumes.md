---
title: Azure NetApp Files için performans kıyaslama test önerileri | Microsoft Docs
description: Azure NetApp Files kullanarak birim performansı ve ölçümleri için kıyaslama testi önerileri sağlar.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 1969b3c237a4133df6f53bd6426ca4d50581cbcb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881724"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Azure NetApp Files için performans kıyaslama test önerileri

Bu makale, Azure NetApp Files kullanarak birim performansı ve ölçümleri için kıyaslama testi önerileri sağlar.

## <a name="overview"></a>Genel Bakış

Azure NetApp Files bir birimin performans özelliklerini anlamak için, açık kaynak aracı [Fio](https://github.com/axboe/fio) 'ı kullanarak çeşitli iş yüklerinin benzetimini yaparak bir dizi kıyaslama uygulayabilirsiniz. FIO, hem Linux hem de Windows tabanlı işletim sistemlerine yüklenebilir.  Bir birim için hem ıOPS hem de üretilen iş için hızlı bir anlık görüntü almak için harika bir araçtır.

### <a name="vm-instance-sizing"></a>VM örneği boyutlandırma

En iyi sonuçlar için, testleri gerçekleştirmek üzere uygun şekilde boyutlandırılabilen bir sanal makine (VM) örneği kullandığınızdan emin olun. Aşağıdaki örneklerde bir Standard_D32s_v3 örneği kullanılmaktadır. VM örnek boyutları hakkında daha fazla bilgi için bkz. Windows tabanlı VM 'Ler için [Azure 'Da Windows sanal makineleri Için boyutlar](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) ve Linux tabanlı VM 'Ler için [Azure 'da Linux sanal makineleri için Boyutlar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

### <a name="azure-netapp-files-volume-sizing"></a>Azure NetApp Files birim boyutlandırma

Beklenen performans düzeyi için doğru hizmet düzeyini ve birim kotası boyutunu seçtiğinizden emin olun. Daha fazla bilgi için bkz. [Azure NetApp Files Için hizmet düzeyleri](azure-netapp-files-service-levels.md) .

### <a name="virtual-network-vnet-recommendations"></a>Sanal ağ (VNet) önerileri

Kıyaslama testini Azure NetApp Files aynı VNet 'te gerçekleştirmeniz gerekir. Aşağıdaki örnekte öneri gösterilmektedir:

![VNet önerileri](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>FIO yüklemesi

FIO, hem Linux hem de Windows için ikili biçimde kullanılabilir. Seçtiğiniz platforma yönelik yüklemek için [Fio](https://github.com/axboe/fio) 'Daki ikili paketler bölümünü izleyin.

## <a name="fio-examples-for-iops"></a>IOPS için FIO örnekleri 

Bu bölümdeki FIO örnekleri aşağıdaki kurulumu kullanır:
* VM örnek boyutu: D32s_v3
* Kapasite havuzu hizmet düzeyi ve boyutu: Premium/50 TiB
* Birim kota boyutu: 48 TiB

Aşağıdaki örneklerde FIO rastgele okuma ve yazma işlemleri gösterilmektedir.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8k blok boyutu% 100 rastgele okuma

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Çıktı: 68k okuma ıOPS 'si görüntülendi

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8k blok boyutu% 100 rastgele yazma

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Çıktı: 73K yazma ıOPS görüntülendi

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Bant genişliği için FIO örnekleri

Bu bölümdeki örneklerde FIO sıralı okuma ve yazma işlemleri gösterilmektedir.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64K blok boyutu% 100 sıralı okuma

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Çıktı: 11,8 Gbit/sn aktarım hızı görüntülendi

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64K blok boyutu% 100 sıralı yazma

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Çıktı: 12,2 Gbit/sn aktarım hızı görüntülendi

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Birim ölçümleri

Azure NetApp Files performans verileri Azure Izleyici sayaçları aracılığıyla kullanılabilir. Sayaçlar Azure portal ve REST API GET istekleri aracılığıyla kullanılabilir. 

Geçmiş verileri aşağıdaki bilgiler için görüntüleyebilirsiniz:
* Ortalama okuma gecikmesi 
* Ortalama yazma gecikmesi 
* IOPS okuma (Ortalama)
* IOPS yaz (Ortalama)
* Birim mantıksal boyutu (Ortalama)
* Birim anlık görüntü boyutu (Ortalama)

### <a name="using-azure-monitor"></a>Azure İzleyici’yi kullanma 

Ölçüm sayfasından, aşağıda gösterildiği gibi, her birim için Azure NetApp Files sayaçlarına erişebilirsiniz:

![Azure Izleyici ölçümleri](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Ayrıca, ölçüm sayfasına giderek, NetApp için filtreleyerek ve ilgilendiğiniz birim sayaçlarını belirterek Azure NetApp Files için Azure Izleyici 'de bir pano oluşturabilirsiniz: 

![Azure İzleyici panosu](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure Izleyici API erişimi

REST API çağrılarını kullanarak Azure NetApp Files sayaçlarına erişebilirsiniz. Bkz [. Azure izleyici ile desteklenen ölçümler: Kapasite havuzları ve birimlere yönelik sayaçlar için Microsoft. NetApp/netappaccounts/capacityhavuzlar/birimler](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) .

Aşağıdaki örnekte, mantıksal birim boyutunu görüntülemek için bir GET URL 'SI gösterilmektedir:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Sonraki adımlar

- [Azure NetApp Files için hizmet düzeyleri](azure-netapp-files-service-levels.md)
- [Azure NetApp Files için performans değerlendirmeleri](azure-netapp-files-performance-benchmarks.md)